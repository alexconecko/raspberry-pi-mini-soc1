## Read the docs, simple.

https://grafana.com/docs/alloy/latest/set-up/install/linux/

## Start Command
```
sudo systemctl start alloy
```
Verify:
```
sudo systemctl status alloy
```

## Config File
Alloy needs a .yaml file to successfully ingest the cowrie logs.

### CREATE CONFIG FILE:
1. Pick a location Alloy’s config (systemd typically expects `/etc/alloy/config.alloy`).
2. Edit it as root:
   ```
   sudo nano /etc/alloy/config.alloy
   ```
3. Paste this minimal config:
   ```
   // Sample config for Alloy.
    //
    // For a full configuration reference, see https://grafana.com/docs/alloy
    logging {
      level = "warn"
    }
    
    prometheus.exporter.unix "default" {
      include_exporter_metrics = true
      disable_collectors       = ["mdadm"]
    }
    
    prometheus.scrape "default" {
      targets = array.concat(
        prometheus.exporter.unix.default.targets,
        [{
          // Self-collect metrics
          job         = "alloy",
          __address__ = "127.0.0.1:12345",
        }],
      )
    
      forward_to = [
      // TODO: components to forward metrics to (like prometheus.remote_write or
      // prometheus.relabel).
      ]
    }

   //NON DEFAULT CONFIG
   filelog.receiver "cowrie" {
     include = ["/home/cowrie/cowrie/var/log/cowrie/cowrie.json"]
     start_at = "end_of_file"
     include_file_path = false
   }
   
   loki.exporter "cowrie_loki" {
     endpoint = "http://localhost:3100/loki/api/v1/push"
     labels = ["job=cowrie"]
   }
   
   pipeline "cowrie_pipeline" {
     receivers = ["cowrie"]
     exporters = ["cowrie_loki"]
   }
   ```
### ENSURE LOG FILE EXISTS
Even an empty file must exist before Alloy starts. Alloy does not create it.
```
sudo touch /home/cowrie/cowrie/var/log/cowrie/cowrie.json
sudo chown alloy:alloy /home/cowrie/cowrie/var/log/cowrie/cowrie.json
sudo chmod 644 /home/cowrie/cowrie/var/log/cowrie/cowrie.json
```
   
### PERMISSIONS
Ensure Alloy can read the log file:
```
sudo chown -R socadmin:socadmin /home/cowrie/cowrie/var/log/cowrie/
sudo chmod 644 /home/cowrie/cowrie/var/log/cowrie/cowrie.json
```
- Alloy runs as your system user (check `systemctl status alloy`).
- File must be readable by that user.

   
