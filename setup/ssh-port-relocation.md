## Reducing Our Attack Surface
Move the administrative SSH service off the default port to reduce automated scanning and allow safe honeypot deployment.
It reduces attack surface while maintaining key based authentication and monitoring set up earlier.

---

# IMPLEMENTATION
Do this after initial system hardening.

Edit SSH config
```
sudo nano /etc/ssh/sshd_config
```
Change Port 22 to a new port number, must be above 1024 or else root needed.

Update firewall:
```
sudo ufw allow 52222/tcp
sudo ufw reload
```

Restart SSH:
```
sudo systemctl restart ssh
sudo ss -tlnp | grep ssh
```

Test access:
```
ssh user@ip -p 52222
```




