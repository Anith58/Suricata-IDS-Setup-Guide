# Suricata IDS Setup Guide

## ✨ Overview

This document describes how to install, configure, and run **Suricata** (an open-source network threat detection engine) on a Linux VM. Suricata is used to monitor network traffic and generate alerts for suspicious activity.

---

## 🖥️ System Requirements

- OS: Ubuntu 22.04 LTS
- User: Root or sudo access
- Network Interface: Must be known (e.g., eth0 or ens33)

---

## 🔧 Step 1: Install Suricata

### Update System

```bash
sudo apt update && sudo apt upgrade -y
```

### Install Suricata

```bash
sudo apt install suricata -y
```

> This installs the latest Suricata available from Ubuntu's official repository.

---

## ⚙️ Step 2: Configure Suricata

### Identify Network Interface

```bash
ip a
```

> Note your active network interface (e.g., `ens33` or `eth0`).

### Edit Suricata Config

```bash
sudo nano /etc/suricata/suricata.yaml
```

Update the following lines:

```yaml
af-packet:
  - interface: ens33  # change to your network interface
```

Optional: Enable EVE JSON output for easy log parsing:

```yaml
eve-log:
  enabled: yes
```

---

## ▶️ Step 3: Run Suricata

### Start Suricata in IDS mode

```bash
sudo suricata -c /etc/suricata/suricata.yaml -i ens33
```

> This starts Suricata with your configuration on the selected interface.

### Enable auto-start

```bash
sudo systemctl enable suricata
sudo systemctl start suricata
```

---

## 📁 Step 4: View Suricata Logs

### Logs are stored at:

```bash
/var/log/suricata/
```

Important files:

- `fast.log` – real-time alerts
- `eve.json` – detailed structured logs (used for SIEM integration)

### View live logs

```bash
tail -f /var/log/suricata/fast.log
```

---

## 🔄 Step 5: Update Rules

### Install Suricata-Update Tool

```bash
sudo apt install python3-pip -y
pip3 install suricata-update
```

### Update default rule set

```bash
sudo suricata-update
```

---

## 🧪 Step 6: Test Suricata (Optional)

### Use test rule or simulate traffic

To test detection without real threats:

```bash
echo "alert icmp any any -> any any (msg:\"ICMP test detected\"; sid:1000001; rev:1;)" | sudo tee /etc/suricata/rules/test.rules
```

Then add this to `suricata.yaml`:

```yaml
  - test.rules
```

Reload Suricata:

```bash
sudo systemctl restart suricata
```

---

## 🧰 Tools Used

- Suricata IDS
- Ubuntu 22.04 LTS

---

---

## 👤 Author

**ANITH**\
Cybersecurity Enthusiast | SOC Analyst in Training

