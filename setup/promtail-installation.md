## ALTERNATIVE ARRANGEMENTS
As mentioned in Loki Setup: Promtail will be EOL as of March 2026, docs reference installing Promtail which will not work.

For our purposes, and ease of install I will simply install Promtail manually.

### PREP
Open the Promtail dashboard port:
```
sudo ufw allow 9080/tcp
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
Check Promtail logs:
```
sudo journalctl -u promtail -f
```

