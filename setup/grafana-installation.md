## Read the docs, simple.

https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/

## Start Grafana
```
sudo systemctl start grafana-server
```

### Enable it to start at boot
```
sudo systemctl enable grafana-server
```

### Check status
```
sudo systemctl status grafana-server
```

## Web Interface
In a browser, go to:
`http://<pi_ip>:3000`
- Replace with the Pi's IP.
- Login with Grafana credentials, by default should be `admin:admin`

You should now be able to connect remotely, on the same network.

If this fails, you may need to check firewall:
```
sudo ufw allow 3000/tcp
sudo ufw status
```

## Configure Grafana
1. Log in to Grafana (default http://<your-server>:3000, user admin).
2. Go to Settings → Data Sources → Add data source.
3. Select Loki.
4. URL:
```
http://localhost:3100
```
5. Click Save & Test
   - Should say "Data source successfully connected."


