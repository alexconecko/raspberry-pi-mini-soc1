## Raspberry Pi SOC Honeypot — Real-Time Attack Monitoring & Alerting

## PROJECT GOAL
Build a single device solution that functions as a mini SOC sensor and analysis node, providing:
- Real attacker interaction (SSH/Telnet honeypot)
- Centralised structured logging
- Dashboards showing attack activity
- Alerts for high-risk events
- Hardened, headless Linux operation
- Clear documentation and security rationale

This is hands on learning about defensive monitoring.

## Tech Stack
Core Components
- OS: Ubuntu Server 22.04 LTS (ARM64)
- Honeypot: Cowrie (SSH & Telnet)
- Log Store: Loki (JSON logs, label-based)
- Visualisation & Alerts: Grafana

This stack was chosen as its fully ARM64 compatible allowing the solution to function from a single device.

  
