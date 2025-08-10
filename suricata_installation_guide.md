# Suricata IDS Installation & Configuration on Ubuntu (Integration-ready for Wazuh)

## Step 1: Prepare Ubuntu Server OS Installation
- Download Ubuntu Server ISO: [https://ubuntu.com/download/server](https://ubuntu.com/download/server)
- Create a bootable USB or mount the ISO in your virtual machine.
- Complete the OS installation and log in.
- Update the system:
```bash
sudo apt update && sudo apt upgrade -y
```

---

## Step 2: Install Required Package
```bash
sudo apt-get install software-properties-common
```

---

## Step 3: Add Suricata Stable PPA Repository
```bash
sudo add-apt-repository ppa:oisf/suricata-stable
```
**Explanation:** This adds the official Suricata stable repository so you can install and update Suricata directly from its maintainers.

---

## Step 4: Update Package List
```bash
sudo apt update
```

---

## Step 5: Install Suricata and jq
```bash
sudo apt install suricata jq
```
- `suricata`: The intrusion detection system.
- `jq`: A command-line JSON processor (useful for Suricata log processing).

---

## Step 6: Verify Suricata Installation and Service Status
```bash
sudo suricata --build-info
sudo systemctl status suricata
```

---

## Step 7: Enable and Start Suricata Service, and Identify Network Interface
```bash
sudo systemctl enable suricata.service
sudo systemctl start suricata
sudo systemctl status suricata
ip a
```
> Common interfaces: `ens33` or `eth0`.

---

## Step 8: Edit Suricata Configuration File
```bash
sudo nano /etc/suricata/suricata.yaml
```

---

## Step 9: Update Suricata Configuration
```yaml
HOME_NET: "<UBUNTU_IP>"
EXTERNAL_NET: "any"

stats:
  enabled: yes

af-packet:
  - interface: eth0
```
> Replace `<UBUNTU_IP>` with your Ubuntu server IP and `eth0` with your interface name.

---

## Step 10: Download and Extract Emerging Threats Suricata Ruleset
```bash
sudo mkdir -p /etc/suricata/rules
cd /tmp/ && curl -LO https://rules.emergingthreats.net/open/suricata-6.0.8/emerging.rules.tar.gz
sudo tar -xvzf emerging.rules.tar.gz
sudo mv rules/*.rules /etc/suricata/rules/
sudo chmod 640 /etc/suricata/rules/*.rules
```

---

## Step 11: Add Rules Path to Suricata Configuration
In `/etc/suricata/suricata.yaml`:
```yaml
default-rule-path: /etc/suricata/rules

rule-files:
  - test.rules
```

---

## Step 12: Create a Test Rule for Suricata
```bash
echo "alert icmp any any -> any any (msg:\"ICMP test detected\"; sid:1000001; rev:1;)" | sudo tee /etc/suricata/rules/test.rules
```
> This triggers an alert for any ICMP (ping) traffic.

---

✅ **Suricata is now installed, configured, and ready to integrate with Wazuh.**
