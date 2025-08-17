# Cowrie Honeypot Lab

This repository contains my experiments with the [Cowrie SSH/Telnet honeypot](https://github.com/cowrie/cowrie).

## Overview
- Simulates a typical SSH attack chain (port scanning, brute-force login attempts, and command execution).
- Records attacker behavior in a safe and controlled environment.
- Built for learning and experimentation with honeypots and SIEM tools.

## Documentation
- [Experiment Notes](docs/EXPERIMENT.md) – detailed walkthrough of the honeypot test.
- [Setup Guide](docs/SETUP.md)
- Integration notes for ELK / Wazuh.

## Next Steps
- Integrate Cowrie logs into ELK/Kibana dashboards.  
- Forward logs to Wazuh SIEM.  
- Deploy Cowrie on a VPS to capture real-world attacks.

