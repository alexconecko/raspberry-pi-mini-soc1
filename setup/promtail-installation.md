## ALTERNATIVE ARRANGEMENTS
As mentioned in Loki Setup: Promtail will be EOL as of March 2026, docs reference installing Promtail which will not work.

For our purposes, and ease of install I will simply install Promtail manually.

### PREP
Open the Promtail dashboard port:
```
sudo ufw allow 9080/tcp
sudo ufw allow 9081/tcp
```

### Install Procedure
Since our systems is Ubuntu/Debian based:

Move to a temp folder:
```
cd /tmp
```

Download Promtail .deb for ARM64:
```
wget https://github.com/grafana/loki/releases/download/v3.6.3/promtail_3.6.3_arm64.deb
```

```
sudo dpkg -i promtail_3.6.3_arm64.deb
```
If there are missing dependencies, fix with:
```
sudo apt-get install -f
```

## Enable & Start Promtail
```
sudo systemctl enable promtail
sudo systemctl start promtail
sudo systemctl status promtail
```
You should now see the service as active.

On installation, generally it automatically sets itself to start at boot.

### Configure Promtail to scrape Cowrie logs
Edit Promtail config and add new scrape to scrape section:
```
sudo nano /etc/promtail/config.yml
```

Add under default job:
```
- job_name: cowrie
    static_configs:
    - targets:
        - localhost
      labels:
        job: cowrie
        __path__: /home/cowrie/cowrie/var/log/cowrie/cowrie.json
```
- `job: cowrie` → label you can use in Grafana queries
- `__path__` → points to your actual Cowrie JSON log

## Restart and verify:
```
sudo systemctl restart promtail
sudo systemctl status promtail
```

## Make Promtail use different ports permanently (avoid dashboard issues later)
Edit your Promtail systemd service so it always uses unused ports.

1. Open the service override:
```
sudo systemctl edit promtail
```

2. Add the override so it runs Promtail with explicit ports
```
[Service]
ExecStart=
ExecStart=/usr/bin/promtail -config.file /etc/promtail/config.yml -log.level=info -server.http-listen-port=9081 -server.grpc-listen-port=36294
```
The first `ExecStart=` line clears the original one, the second sets your custom command.

3. Reload systemd and restart Promtail:
```
sudo systemctl daemon-reload
sudo systemctl restart promtail
sudo systemctl status promtail
```
From now on, it will always start on boot using these ports, no conflicts.

# If you have issues such as job not being ready in dashboard

Make the file owned by the Promtail user (without specifying group):
```
sudo chown promtail /home/cowrie/cowrie/var/log/cowrie/cowrie.json
```
Add directory execute permissions so Promtail can traverse:
```
sudo chmod o+x /home/cowrie
sudo chmod o+x /home/cowrie/cowrie
sudo chmod o+x /home/cowrie/cowrie/var
sudo chmod o+x /home/cowrie/cowrie/var/log
sudo chmod o+x /home/cowrie/cowrie/var/log/cowrie
```
Then restart and verify.
```
curl -G http://localhost:3100/loki/api/v1/query_range \
  --data-urlencode 'query={job="cowrie"}' \
  --data-urlencode "start=$(($(date +%s%N)-3600000000000))" \
  --data-urlencode "end=$(date +%s%N)"
```
- You should see JSON with `streams` containig Cowrie logs.
- If it's empty, check Promtail logs again and make sure the tailer seeked from
  the beginning on first run.

# If after this you have issues with dashboards not populating
Check the file and directory permissions:
```
ls -l /home/cowrie/cowrie/var/log/cowrie/
```
You'll likely see that `cowrie.json` is not writable by the user running Cowrie (`cowrie` user)

1. Fix ownership:
Assuming you run Cowrie as cowrie:cowrie (the usual setup):

```
sudo chown -R cowrie:cowrie /home/cowrie/cowrie/var/log/cowrie
```

This makes the directory and all files inside writable by the Cowrie user.

2. Fix permissions:
Change ownership of the JSON log so Cowrie can write:
```
sudo chown cowrie:cowrie /home/cowrie/cowrie/var/log/cowrie/cowrie.json
```
Double-check permissions (owner must have write access):
```
sudo chmod 640 /home/cowrie/cowrie/var/log/cowrie/cowrie.json
```
- 640 → owner can read/write, group can read, others no access
- That’s enough for Cowrie (cowrie) to write and Promtail (promtail) to read if promtail is in the cowrie group (optional).

3. Add Promtail to `cowrie` group so it can read:
   ```
   sudo usermod -aG cowrie promtail
   ```

4. Restart service:
   ```
   sudo systemctl restart promtail
   ```
   
5. Test reading as `socadmin`:
   ```
   sudo tail -f /home/cowrie/cowrie/var/log/cowrie/cowrie.json
   ```

