# Arch Linux installation

My installation notes on installing Arch Linux to Thinkpad T480s. Heavily inspired and adapted from Mischa van den Burg's excellent [install Arch Linux THE RIGHT way](https://www.youtube.com/playlist?list=PL_JVnPgp2IRcFnHqZdmQwWdv8n49vGHqp) video series.

## Preparations

Boot Arch Linux ISO from USB or some other way

* Load Finnish keymap
  * `loadkeys fi`
* Connect WiFi interface
  * `iwctl`
    * `station wlan0 scan`
    * `station wlan0 connect $SSID`

## Disk

### Partitioning

* Delete old partitions with fdisk `fdisk /dev/nvme0n1`
  * write changes to disk
* Delete old partition table
  * `sgdisk --zap-all /dev/sdX`
  * `wipefs --all /dev/sdX`
* Create new partitions with fdisk
  * 1Gb boot partition with type EFI
  * Rest of the disk space with Linux LVM type

### Encryption

* Create LUKS encryption container to bigger partition
  * `cryptsetup luksFormat /dev/nvme0n1p2`

### LVM

* Create physical volume on top of the opened LUKS container
  * `pvcreate /dev/mapper/cryptlvm`
* Create volume group
  * `vgcreate thinkpad /dev/mapper/cryptlvm`
* Create logical volumes
  * `lvcreate -L 4G thinkpad -n swap`
  * `lvcreate -L 32G thinkpad -n root`
  * `lvcreate -l 100%FREE thinkpad -n home`
* Create filesystems
  * `mkfs.ext4 /dev/thinkpad/root`
  * `mkfs.ext4 /dev/thinkpad/home`
  * `mkswap /dev/thinkpad/swap`

### Mount rest of the partitions

* `mount /dev/thinkpad/root /mnt`

### Prepare boot partition

* `mkfs.fat -F32 /dev/nvme0n1p1`
* `mount --mkdir /dev/nvme0n1p1 /mnt/boot`

### Enable swap

`swapon /dev/thinkpad/swap`

## Install base system

* Install basic packages
  * `pacstrap -K /mnt base linux linux-firmware`
* Generate fstab
  * `genfstab -U /mnt >> /mnt/etc/fstab`
* Chroot to the installed Linux filesystem
  * `arch-chroot /mnt`
* Install CPU microcode updates
  * `pacman -Syu intel-ucode`
* Set timezone
  * `ln -sf /usr/share/zoneinfo/Europe/Helsinki /etc/localtime`
* Sync clock
  * `hwclock --systohc`
* Configure locales
  * `locale-gen en_US.UTF-8`
  * `echo "LANG=en_US.UTF-8" > /etc/locale.conf`

## Setup networking with systemd-networkd

* Enable systemd-networkd and systemd-resolved
  * `systemctl enable systemd-networkd`
  * `systemctl enable systemd-resolved`
* Use systemd-resolved stub resolver
  * `sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf`
* Configure wireless interface
```
cat << 'EOF' > /etc/systemd/network/25-wireless.network 
[Match]
Name=wlan0

[Network]
DHCP=yes
IgnoreCarrierLoss=3s
EOF
```

* Install `ìwd`
  * `pacman -Syu iwd`
* Enable `iwd`
  * `systemctl enable iwd`

## Configure initrmfs hooks

Following is needed to decrypt root filesystem

In `/etc/mkinitcpio.conf`, set:

```
HOOKS=(base systemd autodetect microcode modconf kms keyboard block sd-encrypt lvm2 filesystems fsck)
```

* Install `lvm2` package
  * ``pacman -Syu lvm2
* Regenerate initrmfs
  * `mkinitcpio -P`

## Install bootloader (systemd-boot)

* `bootctl install`
* Configure bootloader entry with correct option for the LUKS partition
```
cat << 'EOF' > /boot/loader/entries/arch.conf
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img

options rd.luks.name=61c24ef5-ba64-493a-8fbf-9f6050a9026a=thinkpad root=/dev/thinkpad/root rw
EOF
```
* Build initrmfs once more, just in case
  * `mkinitcpio -P`

## Mount home and create user

* `mount /dev/thinkpad/home /home/`
* Fstab needs to be configured. `genfstab` is part of `arch-install-scripts` package
  * `pacman -Syu arch-install-scripts`
  * Remove old entrie from `/etc/fstab`
  * Generate new fstab
    * genfstab / >> /etc/fstab
* Install `sudo`
  * `pacman -Syu sudo`
* Create user
  * `useradd -m tatu`
* Set password
  * `passwd tatu`
* Add user to wheel group
  * `usermod -aG wheel tatu`
* With `visudo` remove comment from line
  * `# %wheel ALL=(ALL:ALL) ALL`

## Set hostname

`echo "thinkpad" > /etc/hostname`

## Booting to the new system

That's all, base system is now installed and it should be bootable.
