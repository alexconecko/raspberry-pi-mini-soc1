## Read the docs, simple.

https://docs.cowrie.org/en/latest/INSTALL.html

I recommend using authbind.

If you do, dont forget to start with:
```
AUTHBIND_ENABLED=yes cowrie start
```

## Verify cowrie honeypot is listening on port 22
```
ss -tulnp | grep 22
```
You should see something to the effect of: 
```
tcp   LISTEN 0   50   0.0.0.0:22   0.0.0.0:*   users:(("twistd",pid=4318,fd=11))
```
