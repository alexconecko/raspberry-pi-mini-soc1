## Installing basic protections
Ensures the OS is up-to-date and installs essential tools for monitoring, firewall management, and intrusion prevention.
```
sudo apt update && sudo apt full-upgrade -y
sudo apt install ufw fail2ban htop curl git -y
```
Reduces vulnerabilities from outdated packages and provides baseline protection against common attacks.

## Firewall
Opens only necessary ports and enables the firewall to enforce network restrictions.
```
sudo ufw allow ssh
sudo ufw allow 5601/tcp   # Wazuh dashboard
sudo ufw enable
```
Limits exposure to external attackers, ensuring only required services are accessible.

## Create SOC user
Creates a dedicated administrative account for SOC operations and grants sudo privileges.
```
sudo adduser socadmin
sudo usermod -aG sudo socadmin
```
Minimizes risk by separating administrative access from the default account and applying the principle of least privilege.

## SSH Key Setup
Generate Key
```
ssh-keygen -t ed25519
```
- File location: Enter (default)
- Passphrase: Enter (empty for now)

Verify:
```
dir %USERPROFILE%\.ssh
```
You must see:
- id_ed25519
- id_ed25519.pub

Install Public Key on Pi
On Windows:
```
type %USERPROFILE%\.ssh\id_ed25519.pub
```
Copy the entire line.
On the Pi (as `socadmin`)
```
mkdir -p ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys
```
Paste the key on one line, save, then:
```
chmod 600 ~/.ssh/authorized_keys
chown -R socadmin:socadmin ~/.ssh
```

## Harden SSH
Edit SSH config:
```
sudo nano /etc/ssh/sshd_config
```
Set:
```
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AllowUsers socadmin
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 2
```
Restart SSH (do NOT close current session):
```
sudo systemctl restart ssh
```
Open a new terminal and confirm no password required:
```
ssh socadmin@mini-soc     # Replace mini-soc with your host name
```


## Lock Default Account
Disables login for the original Pi user, leaving only the SOC user active.
```
sudo passwd -l USERNAME      # The name you initially set your Pi should replace USERNAME
```
Prevents unauthorized access via default credentials and ensures all SSH access is controlled through a hardened, monitored account.
