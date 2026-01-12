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

## Configure Grafana Data Source
1. Log in to Grafana (default http://<your-server>:3000, user admin).
2. Go to Settings → Data Sources → Add data source.
3. Select Loki.
4. URL:
```
http://localhost:3100
```
As Grafana is running on the same Pi as Loki you can point the data source to **localhost**.

This is because Grafana can talk directly to Loki on the same machine.

5. Click Save & Test
   - Should say "Data source successfully connected."
  
## Add Dashboard Panels
 1. Go to Dashboards
 2. Select Loki Data Source
 3. Add Visualisation
 4. Add/Edit Panel

For the purposes of this project we will switch editor to "Code" (do not use Builder)

**Paste these EXACT queries:**

SSH Login Attempts Over Time

*Panel type: Time series*
```
count_over_time({job="cowrie"} |= "login" [1m])
```
Top Source IPs

*Panel type: Bar chart*
```
topk(5, sum by (src_ip) (count_over_time({job="cowrie"} |= "login" [1h])))
```
Top Usernames

*Panel type: Bar Chart (Horizontal)*
```
topk(5, sum by (username) (count_over_time({job="cowrie"} |= "login" [1h])))
```
Commands Executed

*Panel type: Table*
```
topk(5, sum by (input) (count_over_time({job="cowrie"} |= "input" [1h])))
```
Session Counts

*Panel type: Time series*
```
count_over_time({job="cowrie"} |= "session.closed" [1m])
```


