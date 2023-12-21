# SSH

## Create keypair and write private key to STDOUT

```bash
mkfifo key && ((cat key ; rm key)&) && (echo y | ssh-keygen -N '' -q -f key -t ed25519 -b 4096 > /dev/null
```

Source: https://gist.github.com/kraftb/9918106?permalink_comment_id=2733298#gistcomment-2733298




