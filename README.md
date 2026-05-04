# Smart Water Tank Level Control System
### Industrial Automation Portfolio Project

![Status](https://img.shields.io/badge/Status-In%20Progress-blue)
![PLC](https://img.shields.io/badge/PLC-Siemens%20S7--1500-blue)
![Language](https://img.shields.io/badge/Language-SCL%20IEC%2061131--3-blue)
![SCADA](https://img.shields.io/badge/SCADA-Ignition-orange)
![AI](https://img.shields.io/badge/AI-Python%20OPC--UA-yellow)
![License](https://img.shields.io/badge/License-MIT-green)

---

## Overview

A complete industrial automation project demonstrating a four-layer automation architecture for water tank level control — built entirely without hardware using industry-standard tools.

**Designed to demonstrate competency in:**
TIA Portal V20 (SCL) · WinCC HMI · Ignition SCADA · OPC-UA · Python AI

---

## System Architecture
SCADA Layer — Ignition
Dashboards · Historian · Alarms · Reports
↕ OPC-UA
AI Layer — Python
Prediction · Anomaly Detection · Write-back
↕ OPC-UA
Control Layer — TIA Portal SCL
S7-1500 · OB1 · FB1 · FB2 · FC1 · DB1
↕ Simulation
Field Layer — PLCSIM
Virtual sensors · Valve · Pump

---

## Technology Stack

| Layer | Tool | Version |
|---|---|---|
| PLC Programming | Siemens TIA Portal | V20 |
| PLC Language | SCL (IEC 61131-3) | — |
| PLC Simulation | PLCSIM V20 | — |
| HMI | WinCC Comfort | V20 |
| SCADA | Ignition | 8.1 |
| Communication | OPC-UA (IEC 62541) | — |
| AI / Analytics | Python 3.x | 3.11 |

---

## Project Structure
smart-water-tank-automation/
├── plc/
│   ├── src/
│   │   ├── FC1_ScaleLevel.scl
│   │   ├── FB1_TankControl.scl
│   │   ├── FB2_RateCalc.scl
│   │   └── OB1_Main.scl
│   └── WaterTankControl1.zap20
├── hmi/
│   └── screenshots/
├── scada/
│   └── screenshots/
├── ai/
│   ├── tank_ai.py
│   └── requirements.txt
└── docs/
└── project_documentation.pdf

---

## PLC Program Structure

| Block | Type | Description |
|---|---|---|
| OB1 Main | Organization Block | Main cyclic program — calls all blocks in order |
| FB1 TankControl | Function Block | Core control logic, interlocks, alarms |
| FB2 RateCalc | Function Block | Fill and drain rate calculator |
| FC1 ScaleLevel | Function | Analog signal scaling 0-27648 to 0-100% |
| DB1 TankData | Global Data Block | Shared process data and AI results |
| DB2 TankControl_DB | Instance DB | Stores FB1 static variables |
| DB3 RateCalc_DB | Instance DB | Stores FB2 static variables |

---

## Control Logic Summary

- Inlet valve opens when level drops below 20% and closes at 90%
- Outlet pump runs continuously while system is active
- Emergency stop highest priority — forces all outputs off immediately
- Overflow protection — level above 95% force closes valve and raises alarm
- Dry run protection — level below 5% force stops pump to prevent damage
- All logic written in SCL following IEC 61131-3 standard

---

## AI Layer

The Python AI layer connects to the PLC via OPC-UA and runs every 10 seconds:

- Reads live level and drain rate from PLC data block
- Predicts time until tank reaches critical level
- Detects anomalous drain rates — possible leak detection
- Writes prediction back to DB1.AITimeToEmpty
- Result displayed automatically on HMI and SCADA

```bash
pip install -r ai/requirements.txt
python ai/tank_ai.py
```

---

## Industry Standards Applied

| Standard | Application |
|---|---|
| IEC 61131-3 | SCL programming language and block structure |
| ISA-18.2 | Alarm management — priority, acknowledgement, history |
| ISO 13849 | Emergency stop interlock design |
| IEC 62541 | OPC-UA communication protocol |
| ISA-95 | Four-layer automation hierarchy |

---

## How to Open the Project

### PLC
1. Install TIA Portal V20 trial from Siemens
2. Open TIA Portal → Retrieve from archive → select `plc/WaterTankControl1.zap20`
3. Start PLCSIM V20 and download to simulated PLC

### SCADA
1. Install Ignition 8.1 from inductiveautomation.com
2. Configure OPC-UA connection to PLCSIM endpoint
3. Import tag configuration

### AI Layer
```bash
pip install -r ai/requirements.txt
python ai/tank_ai.py
```

---

## Project Status

- [x] PLC Code — FC1, FB1, FB2, OB1 — 0 compile errors
- [x] GitHub repository setup
- [ ] PLCSIM simulation and testing
- [ ] HMI screens — WinCC Comfort
- [ ] SCADA — Ignition dashboards and historian
- [ ] Python AI layer — prediction and anomaly detection
- [ ] Full system integration and testing
- [ ] Project documentation PDF

---

## Author

**Rahul Kuravanparambil Rajan**
Automation Engineer | PLC Developer | IIoT Enthusiast

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://linkedin.com/in/rahulkuravanparambilrajan)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black)](https://github.com/rahulkr-18)

---

## License

MIT License — see LICENSE file for details.
