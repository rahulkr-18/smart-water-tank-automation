# Smart Water Tank Level Control System
### Industrial Automation Portfolio Project

![Status](https://img.shields.io/badge/Status-Stage%205%20Complete-brightgreen)
![PLC](https://img.shields.io/badge/PLC-Siemens%20S7--1500-blue)
![Language](https://img.shields.io/badge/Language-SCL%20IEC%2061131--3-blue)
![HMI](https://img.shields.io/badge/HMI-WinCC%20RT%20Advanced%20V20-darkblue)
![Simulation](https://img.shields.io/badge/Simulation-PLCSIM%20Advanced%20V20-blue)
![License](https://img.shields.io/badge/License-MIT-green)

---

## Overview

A complete industrial automation project demonstrating end-to-end PLC + SCADA-class HMI development for water tank level control — built entirely without hardware using industry-standard tools.

**Stage 5 Complete:** Full PLC programming + WinCC RT Advanced HMI with operator screens, alarm management, and historical trending — all integrated and tested in simulation.

**Demonstrates competency in:**
- Siemens TIA Portal V20 — Hardware configuration, networking, project management
- Structured Control Language (SCL) — IEC 61131-3 compliant PLC programming
- WinCC RT Advanced — SCADA-class operator station development
- Industrial alarm management — Discrete alarms with AlarmWord packing
- Historical trending — Live data visualization for operational analysis
- PROFINET networking — PLC ↔ HMI tag binding and acquisition cycles
- PLCSIM Advanced — Hardware-free simulation and integration testing

---

## System Architecture

```
┌──────────────────────────────────────────────────────────┐
│         WinCC RT Advanced HMI (Operator Station)          │
│   ┌────────────┬────────────┬────────────┐                │
│   │  Overview  │   Alarms   │   Trends   │                │
│   │  - Tank    │  - Alarm   │  - Live    │                │
│   │  - Lamps   │    log     │    curves  │                │
│   │  - I/O     │  - Ack     │  - Multi-  │                │
│   │  - Buttons │    history │    tag     │                │
│   └────────────┴────────────┴────────────┘                │
└──────────────────────────────────────────────────────────┘
                           ↕ PROFINET / S7 Communication
                           ↕ HMI Connection_1
┌──────────────────────────────────────────────────────────┐
│         S7-1500 PLC (CPU 1511-1 PN)                       │
│   ┌──────────────────────────────────────┐                │
│   │ OB1 Main — Cyclic program entry      │                │
│   │   ├─ FC1 ScaleLevel (analog→%)       │                │
│   │   ├─ FB1 TankControl (logic, alarms) │                │
│   │   └─ FB2 RateCalc (fill/drain rates) │                │
│   │                                       │                │
│   │ DB1 TankData — 16 process variables  │                │
│   └──────────────────────────────────────┘                │
└──────────────────────────────────────────────────────────┘
                           ↕ Simulation
┌──────────────────────────────────────────────────────────┐
│         PLCSIM Advanced V20 (Virtual PLC)                 │
└──────────────────────────────────────────────────────────┘
```

---

## Technology Stack

| Component | Tool | Version |
|---|---|---|
| PLC Programming Environment | Siemens TIA Portal | V20 |
| PLC Language | SCL (IEC 61131-3) | — |
| PLC Simulation | PLCSIM Advanced | V20 |
| Operator HMI | WinCC RT Advanced | V20 |
| Communication | S7 over PROFINET / TCP-IP | — |
| Networking | PN/IE_1 subnet, IP 192.168.0.x | — |
| Version Control | Git / GitHub | — |

---

## Project Structure

```
smart-water-tank-automation/
├── plc/
│   ├── src/                          # SCL source code (human-readable)
│   │   ├── FC1_ScaleLevel.scl
│   │   ├── FB1_TankControl.scl
│   │   ├── FB2_RateCalc.scl
│   │   └── OB1_Main.scl
│   └── WaterTankControl1.zap20       # Complete TIA project archive
├── hmi/
│   └── screenshots/                   # 12 portfolio screenshots
│       ├── 01_system_idle.png
│       ├── 02_system_running.png
│       ├── 03_alarm_overflow_flashing.png
│       ├── 04_alarm_dryrun_flashing.png
│       ├── 05_screen_alarms.png
│       ├── 06_screen_trends.png
│       ├── 07_db1_tankdata.png
│       ├── 08_fb1_tankcontrol_code.png
│       ├── 09_ob1_main.png
│       ├── 10_hmi_tags_table.png
│       ├── 11_network_view.png
│       └── 12_integration_proof.png
├── plc_testing/
│   └── screenshots/                   # Stage 4 PLC verification
├── README.md
├── LICENSE
└── .gitignore
```

---

## PLC Program Structure

| Block | Type | Purpose |
|---|---|---|
| OB1 Main | Organization Block | Cyclic program — calls FC1, FB1, FB2 in sequence; ORs HMI command bits with hardware inputs |
| FC1 ScaleLevel | Function | Scales analog input 0-27648 → 0-100% real value |
| FB1 TankControl | Function Block | Core control logic — start latch, valve/pump control, overflow & dry-run protection, emergency stop |
| FB2 RateCalc | Function Block | Computes fill rate and drain rate from level changes over time |
| DB1 TankData | Global Data Block | 16 process variables shared between PLC code and HMI |
| DB2 TankControl_DB | Instance DB | Persistent state for FB1 (RunLatch, ValveLatch, edge detection) |
| DB3 RateCalc_DB | Instance DB | Persistent state for FB2 (rate history) |

---

## Control Logic Summary

The PLC implements layered safety logic:

1. **Emergency Stop (highest priority)** — When E-Stop is active, all outputs forced off, all latches cleared, RETURN immediately.
2. **Start/Stop latching** — HMI start command sets RunLatch TRUE; stop command or alarm clears it. Existing hardware Start/Stop inputs are also supported via OR logic.
3. **Pump control** — OutletPump runs continuously while RunLatch is TRUE.
4. **Inlet valve control** — Latched ON when LevelLow input rises, latched OFF when LevelHigh rises.
5. **Overflow protection** — Level above 95% AND inlet open → fires AlarmOverflow, force closes valve.
6. **Dry-run protection** — Level below 5% AND pump running → fires AlarmDryRun, force stops pump.

---

## DB1.TankData — Variable List

| # | Variable | Type | Purpose |
|---|---|---|---|
| 1 | LevelRaw | Int | Raw analog input value |
| 2 | LevelPercent | Real | Scaled level 0-100% |
| 3 | InletValve | Bool | Inlet valve state |
| 4 | OutletPump | Bool | Outlet pump state |
| 5 | SystemRunning | Bool | System latched on/off |
| 6 | AlarmOverflow | Bool | Overflow alarm bit |
| 7 | AlarmDryRun | Bool | Dry run alarm bit |
| 8 | FillRate | Real | Calculated fill rate (L/min) |
| 9 | DrainRate | Real | Calculated drain rate (L/min) |
| 10 | AITimeToEmpty | Real | Predicted time to empty (placeholder for AI layer) |
| 11 | CycleCount | Int | Number of completed fill cycles |
| 12 | HMI_StartCmd | Bool | Momentary start command from HMI |
| 13 | HMI_StopCmd | Bool | Momentary stop command from HMI |
| 14 | HMI_EStop | Bool | Emergency stop from HMI (latched until reset) |
| 15 | AlarmWord | Word | Packed alarm bits for HMI Discrete alarms (bit 0=Overflow, 1=DryRun, 2=EStop) |

---

## HMI Architecture

### Three operator screens

**ScreenOverview** — Primary operator station
- Tank graphic with Bar element bound to LevelPercent (0-100% with scale)
- I/O field showing live percentage
- 5 status lamps: PUMP, INLET VALVE, SYSTEM RUN, DRY RUN, OVERFLOW
  - Green = normal/running, Gray = off, Red flashing = alarm
- 4 numeric readouts: Fill Rate, Drain Rate, Time to Empty (AI), Cycle Count
- 3 control buttons: START (momentary, SetBitWhileKeyPressed), STOP (momentary), EMERGENCY STOP (latched, InvertBit)

**ScreenAlarms** — Industrial alarm log
- Alarm View widget showing chronological active and historical alarms
- Columns: No., Time, Date, Status (I/IO/IA/IOA), Alarm text, Acknowledge group
- 3 alarms defined: Tank Overflow, Dry Run, Emergency Stop
- Acknowledge button for operator response

**ScreenTrends** — Historical visualization
- Trend View widget with multi-curve display
- Live curves: Level % (blue), Fill Rate (green)
- Time-based X-axis with operator pan/zoom
- Tag connection table showing real-time values with timestamps

### MainTemplate (shared layout)
- Header bar with project title and live date/time
- Bottom navigation strip: Overview / Alarms / Trends buttons
- Applied to all three screens for consistency

### HMI ↔ PLC Tag Binding
- 17 HMI tags mapped to PLC tags via PROFINET S7 connection
- Acquisition cycles tuned for purpose:
  - 100 ms for status lamps and operator commands (instant response)
  - 500 ms for alarm bits (sufficient for safety)
  - 1 s for slowly-changing values (rates, predictions, counters)

### Industrial alarm packing (AlarmWord pattern)
A single Word variable in DB1 packs multiple alarm bits. WinCC's Discrete alarms use bit positions to fire individual alarm messages. This is the standard industrial pattern — efficient communication and scales to 16 alarms per Word.

---

## Screenshots

### System States

| State | Screenshot |
|-------|-----------|
| System Idle | `hmi/screenshots/01_system_idle.png` |
| System Running | `hmi/screenshots/02_system_running.png` |
| Overflow Alarm | `hmi/screenshots/03_alarm_overflow_flashing.png` |
| Dry Run Alarm | `hmi/screenshots/04_alarm_dryrun_flashing.png` |
| Alarms Screen | `hmi/screenshots/05_screen_alarms.png` |
| Trends Screen | `hmi/screenshots/06_screen_trends.png` |

### Engineering & Architecture

| View | Screenshot |
|------|-----------|
| DB1 Variable Structure | `hmi/screenshots/07_db1_tankdata.png` |
| FB1 SCL Code | `hmi/screenshots/08_fb1_tankcontrol_code.png` |
| OB1 Main Block | `hmi/screenshots/09_ob1_main.png` |
| HMI Tags Table | `hmi/screenshots/10_hmi_tags_table.png` |
| Network View (PLC + HMI) | `hmi/screenshots/11_network_view.png` |
| Live Integration Test | `hmi/screenshots/12_integration_proof.png` |

---

## Industry Standards Applied

| Standard | Application |
|---|---|
| IEC 61131-3 | SCL programming language and block structure |
| ISA-18.2 | Alarm management — priority classes, acknowledgement, history |
| ISO 13849 | Emergency stop interlock design — fail-safe layered logic |
| IEC 62541 | OPC-UA communication (planned for AI layer) |
| ISA-95 | Multi-layer automation hierarchy (Field → Control → Operator) |

---

## Testing & Verification

The project was tested in two phases:

**Stage 4 — PLC standalone testing**
- Watch Tables used to force inputs and verify FB1 logic
- Start latch, valve open/close, overflow alarm, dry-run alarm, emergency stop all verified
- Screenshots in `plc_testing/screenshots/`

**Stage 5 — Live HMI integration testing**
- Both PLCSIM Advanced and WinCC Runtime active simultaneously
- Operator commands triggered from HMI verified in PLC
- Alarm conditions triggered in PLC verified on HMI (lamps + alarm log)
- Trend curves verified by stepping LevelRaw through full range
- All 12 portfolio screenshots captured during live runtime

---

## How to Open the Project

### Prerequisites
- Windows 10/11
- Siemens TIA Portal V20 (Trial or licensed)
- PLCSIM Advanced V20

### Steps
1. Install TIA Portal V20 from Siemens.
2. Install PLCSIM Advanced V20 (separate installer).
3. Open TIA Portal → **Project → Retrieve from archive...** → select `plc/WaterTankControl1.zap20`.
4. Choose a target folder for extraction.
5. Once project opens:
   - Compile PLC: right-click PLC_1 → Compile → Software (rebuild all)
   - Start PLCSIM Advanced and create a virtual S7-1500 instance
   - Right-click PLC_1 → Download to device
6. Compile HMI: right-click HMI_TankStation → Compile → Software (rebuild all)
7. Start runtime: right-click HMI_TankStation → Start runtime simulation

---

## Roadmap

### Stage 5 — Complete
- WinCC RT Advanced HMI with 3 operator screens
- Discrete alarm management with AlarmWord packing
- Historical trending with Trend View widget
- Full PLC ↔ HMI integration tested

### Stage 6 — Python AI Layer (planned)
- OPC-UA client connection to S7-1500 (PLC has built-in OPC-UA server)
- ML model for tank-empty prediction (scikit-learn linear regression / LSTM)
- Write prediction back to DB1.AITimeToEmpty
- Demonstrates OT/IT integration and Industrial Edge AI patterns

### Stage 7 — Documentation & Demo (planned)
- 60-second runtime video walkthrough
- Architecture diagram (Mermaid)
- LinkedIn project showcase post

---

## Author

**Rahul Kuravanparambil Rajan**
Automation & Embedded Systems Engineer | M.Sc. Electrical Engineering and Information Technology, TH Deggendorf

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://linkedin.com/in/rahulkuravanparambilrajan)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black)](https://github.com/rahulkr-18)

---

## License

MIT License — see LICENSE file for details.
