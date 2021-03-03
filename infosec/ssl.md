# SSL

## Check server certificate with OpenSSL

### With SNI

```bash
openssl s_client -showcerts -servername www.example.com -connect www.example.com:443 </dev/null
```

### Without SNI

```bash
openssl s_client -showcerts -connect www.example.com:443 </dev/null

```

### Full details

```bash
echo | \
    openssl s_client -servername www.example.com -connect www.example.com:443 2>/dev/null | \
    openssl x509 -text
```
