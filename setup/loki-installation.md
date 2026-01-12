## Read the docs, simple.

https://grafana.com/docs/loki/latest/setup/install/

Note: Promtail will be EOL as of March 2026, docs reference installing `Promtail` which will
not work. `Alloy` has effectively replaced Promtail as Alloy serves as a unified agent for logs, 
metrics, and traces, offering more flexibility and future-proofing.

## Back up the config file
```
sudo cp /etc/loki/config.yml /etc/loki/config.yml.bak
```
This way you can recover instantly if something goes wrong.

## CONFIG FILE
- Set logging to `info` (`debug` can be overly verbose)
- Storage is in `/tmp/loki`, meaning everything is lost on reboot.
  - TODO: look into persistence

## Note

Loki should run as a system service (```systemd```), it starts automatically at boot,
otherwise you start it manually using:
```
systemctl start loki
```
Verify:
```
systemctl status loki
```
You should see the service as ```active```
