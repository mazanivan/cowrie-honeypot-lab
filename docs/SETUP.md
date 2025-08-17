# Cowrie Honeypot – Setup Guide (VM)

Tested on **Ubuntu 22.04** in a **VirtualBox VM** (network: Bridged).  
Goal: run the Cowrie honeypot in an isolated Python environment with a custom `userdb`.

---

## 1) System preparation
```bash
sudo apt update
sudo apt install -y git python3 python3-venv python3-dev build-essential libssl-dev libffi-dev jq
```

---

## 2) Clone the repository
```bash
cd ~
git clone https://github.com/cowrie/cowrie.git
cd cowrie
```

---

## 3) Virtual environment (venv)
```bash
python3 -m venv cowrie-env
source cowrie-env/bin/activate
```
Prompt will show `(cowrie-env)`. Exit with `deactivate`.

---

## 4) Install dependencies
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

---

## 5) Configuration

### 5.1) cowrie.cfg
```bash
cp etc/cowrie.cfg.dist etc/cowrie.cfg
```

### 5.2) userdb
```bash
cp etc/userdb.example etc/userdb
nano etc/userdb
```

**Line format**
```
username::password
```
- first field = username  
- second field = empty (leave as ::)  
- third field = password  

**Special symbols**
- `*` → wildcard (matches any username)  
- `x` → disabled account (invalid login)  

**Example entries**
```
test::1234
admin::password
root::toor
*::qwerty
```

---

## 6) Start / Stop / Restart
```bash
./bin/cowrie start
./bin/cowrie status
./bin/cowrie stop
./bin/cowrie restart
```
```
**After every change in any cowrie file , you need to restart program **
```
---

## 7) Logs
Main log files:
- var/log/cowrie/cowrie.log
- var/log/cowrie/cowrie.json
- var/log/cowrie/tty/

Useful commands:
```bash
tail -f var/log/cowrie/cowrie.log
tail -f var/log/cowrie/cowrie.json | jq .
```

---

## 8) Attacker machine test
```bash
nmap -sV -p 2222 HONEYPOT_IP
ssh -p 2222 test@HONEYPOT_IP
# password: 1234
```

---

## 9) Common issues
- Port 2222 already in use → change in etc/cowrie.cfg or stop conflicting service  
- userdb not working → check that etc/userdb exists with correct syntax  
- Missing Python packages → install system dev packages and rerun pip install -r requirements.txt  
- Invalid passwords accepted → review * (wildcard) and x (disabled) rules in userdb
