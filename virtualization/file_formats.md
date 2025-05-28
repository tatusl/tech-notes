# File formats

## Running .ova packaged virtual appliances with KVM/QEMU

OVA is Open Virtual Appliance package, which is a tar archive containing .ovf descriptor file and typically one or more disk images, for example in .vmdk format. OVF file describes specifications for a virtual machine in XML format.

.ova file can be extracted with the following command:

```
tar xf $name_of_ova_file.ova
```

Then, .vmdk disk images can be converted to KVM/QEMU supported QCOW2 format with the following command:

```
qemu-img convert -O qcow2 $path_to_vmdk_file.vmdk $name_of_qcow2_file.qcow2
```

New VM can be then created from QCOW2 disk image using for example libvirt.

If there are multiple disk images in .ova, VM might need multiple disks to be attached.


## Resources

* Open Virtualization Format - https://en.wikipedia.org/wiki/Open_Virtualization_Format
* Virtual appliance - https://en.wikipedia.org/wiki/Virtual_appliance
