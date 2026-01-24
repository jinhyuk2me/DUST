![Banner](https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/banner.png?raw=true)

<p align="center">
  <a href="https://docs.google.com/presentation/d/1-bRbadY4XmSBsaMfYFJiN6WQ00letQ_9P2LTwLdzEXg/edit?usp=sharing">
    <img src="https://img.shields.io/badge/PRESENTATION-GoogleSlides-yellow?style=for-the-badge&logo=google-slides&logoColor=white" alt="Presentation Slides">
  </a>
  <a href="https://youtu.be/hftyShwyZxk">
    <img src="https://img.shields.io/badge/DEMO-YouTube-red?style=for-the-badge&logo=youtube&logoColor=white" alt="System Demo Video">
  </a>
</p>

# 📚 Table of Contents

- [1. Team](#1-team)
- [2. Project Overview](#2-project-overview)
- [3. Project Goal](#3-project-goal)
- [4. System Design](#4-system-design)
- [5. Key Features](#5-key-features)
- [6. Technical Challenges](#6-technical-challenges)
- [7. Limitations & Scalability](#7-limitations--scalability)
- [8. Tech Stack](#8-tech-stack)

> 📄 Korean version: [`README.md`](README.md)

# 1. Team

### 🧑‍💼 Jinhyuk Jang [`@jinhyuk2me`](https://github.com/jinhyuk2me)
- Designed and implemented the main server
- Designed and developed the GUI
- Authored the overall system architecture
- Defined the communication interfaces
- Built and managed the database

### 🧑‍💼 Daein Kim [`@Daeinism`](https://github.com/Daeinism)
- Project lead for DUST
- Designed and built the resource distributor hardware/firmware
- Designed and built the gate module hardware/firmware

### 🧑‍💼 Geonwoo Lee [`@DigitalNomad230`](https://github.com/DigitalNomad230)
- Reviewed and maintained technical documentation
- Built the resource storage hardware/firmware
- Implemented logic that links container capacity with facility operation

### 🧑‍💼 Seunghoon Lee [`@leesh0806`](https://github.com/leesh0806)
- Developed the AGV module and mechanical structure
- Designed AGV circuitry
- Implemented the line-tracing control algorithm
- Built FSM-based driving control
- Implemented the AGV TCP communication protocol

---

# 2. Project Overview
> ⏰ Period: 3 May – 15 May 2025

<p align="center">
  <img src="https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/gui/main_monitoring_1.gif?raw=true" width="45%" style="margin-right:10px;">
  <img src="https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/facilities/load_1.gif?raw=true" width="45%">
</p>

`DUST (Dynamic Unified Smart Transport)` is an IoT-based transportation control platform that lets an RFID-guided `AGV` coordinate with logistics facilities—gates, conveyors, loading/stocking stations—in real time through a unified control loop.

---

# 3. Project Goal

<p align="center">
  <img src="https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/agv.jpg?raw=true" width="50%">
</p>

Industrial sites increasingly need `AGVs (Automated Guided Vehicles)` that can autonomously follow fixed routes **and** interact with multiple facilities (gates, belts, storage bays). This project integrates every step of that logistics scenario into a single control surface driven by an AGV-centric workflow.

---

# 4. System Design

## System Architecture

<p align="center">
  <img src="https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/system_architecture/sys_archi.png?raw=true" width="80%">
</p>

## ER Diagram

<p align="center">
  <img src="https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/erd/erd.png?raw=true" width="50%">
</p>

---

# 5. Key Features
 
## 🚚 AGV-related Functions

<table>
  <tr>
    <td colspan="2" align="center">
      <img src="https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/truck/truck_1.gif?raw=true" width="45%" style="margin-right:10px;">
      <img src="https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/truck/truck_2.gif?raw=true" width="45%">
    </td>
  </tr>
  <tr>
    <td>🔁 <b>Autonomous Driving</b></td>
    <td>ESP32-controlled driving along the RFID-tag path</td>
  </tr>
  <tr>
    <td>📡 <b>Position Reporting</b></td>
    <td>Reads RFID tags → determines position → reports to the server</td>
  </tr>
  <tr>
    <td>🔋 <b>Battery Monitoring</b></td>
    <td>Periodically publishes remaining battery and FSM state</td>
  </tr>
  <tr>
    <td>📦 <b>Mission Execution</b></td>
    <td>Receives missions → triggers FSM transitions → performs auto loading/unloading</td>
  </tr>
  <tr>
    <td>🛑 <b>Collision Avoidance</b></td>
    <td>Stops via ultrasonic sensor when an obstacle is detected</td>
  </tr>
</table>

---

## 🏭 Facility Control Summary

| Facility | Sample Image | Description |
|----------|--------------|-------------|
| **🚪 Gate Control** | <img src="https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/facilities/gate_1.gif?raw=true" width="220px"> | - Opens automatically for registered AGVs<br>- Blocks unregistered AGVs |
| **📦 Loader Control** | <img src="https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/facilities/load_1.gif?raw=true" width="220px"> | - Automatic drop-off once the AGV arrives |
| **🌀 Belt / Storage Control** | <img src="https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/facilities/belt_1.gif?raw=true" width="220px"> | - Start/stop belts via server commands or conditional triggers<br>- Detect storage saturation via sensors<br>- Auto-select container A/B based on remaining space<br>- Safety interlock blocks belt motion when full |

---

## 🖥 Central Control Server

- **FSM Orchestration** – Issues commands based on AGV/facility states
- **State Logging** – Collects AGV/facility telemetry at fixed intervals
- **Emergency Control** – Manual override for emergency stop and recovery
- **Auto Socket Registration** – Promotes unregistered AGVs from TEMP IDs to real IDs
- **Auto Charge Switching** – Moves the AGV to charging mode when idle + low battery

---

## 🧑‍💼 User Interface (PyQt6 GUI)

| Tab | Description | Preview |
|-----|-------------|---------|
| **📍 Main Monitoring** | Real-time visualization of the AGV location and FSM state + manual drive controls | ![main1](https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/gui/main_monitoring_1.gif?raw=true) |
| **🧭 Mission Management** | Register/delete missions manually and manage the flow from creation → dispatch → completion | ![mission](https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/gui/mission%20management.gif?raw=true) |
| **📑 Event Log** | Tail the live event stream covering state changes, command execution, and sensor events | ![event](https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/gui/event%20log.gif?raw=true) |
| **⚙️ Settings** | Configure AGV IDs, ports, communication parameters, and other runtime options | ![settings](https://github.com/addinedu-ros-9th/iot-repo-4/blob/main/assets/images/gui/settings.gif?raw=true) |

---

# 6. Technical Challenges

| Issue | Root Cause | Resolution |
|-------|------------|------------|
| Communication latency | JSON parsing overhead | Switched critical commands to a custom byte protocol to minimize parsing time |
| PWM instability | PID loop conflicted with RFID reading cycle | Pause PID updates during RFID reads to stabilize line following |

---

# 7. Limitations & Scalability

| Limitation | Current Behavior | Future Direction |
|------------|------------------|------------------|
| Single-AGV FSM | GUI & FSM handle only one AGV | Extend to multi-AGV FSM + UI |
| Simulated battery level | Battery handled via simulated values | Integrate INA226 sensor for real readings |
| Simple facility ACK | Only checks ACK without retry logic | Add timeout-based retry & error logging |
| No persistent settings | Runtime settings reset each session | Store configs via JSON/MySQL and restore on restart |

---

# 8. Tech Stack

| Category | Technologies | |
|----------|-------------|--|
| **Environment** | Linux (Ubuntu 24.04) | ![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=white) ![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=Ubuntu&logoColor=white) |
| **MCU & Firmware** | ESP32-WROOM, Arduino IDE | ![ESP32](https://img.shields.io/badge/ESP32-WROOM-E7352C?style=for-the-badge&logo=espressif&logoColor=white) ![Arduino](https://img.shields.io/badge/Arduino-00979D?style=for-the-badge&logo=arduino&logoColor=white) |
| **Languages** | Python 3.12, C++ | ![Python](https://img.shields.io/badge/python-3776AB?style=for-the-badge&logo=python&logoColor=white) ![C++](https://img.shields.io/badge/c++-%2300599C.svg?style=for-the-badge&logo=c%2B%2B&logoColor=white) |
| **Control UI** | PyQt6 | ![PyQt6](https://img.shields.io/badge/PyQt6-41CD52?style=for-the-badge&logo=qt&logoColor=white) |
| **Database** | MySQL | ![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white) |
| **Version Control** | Git, GitHub | ![Git](https://img.shields.io/badge/git-F05032?style=for-the-badge&logo=git&logoColor=white) ![GitHub](https://img.shields.io/badge/github-181717?style=for-the-badge&logo=github&logoColor=white) |
| **Collaboration** | Confluence, Slack, Jira | ![Confluence](https://img.shields.io/badge/confluence-172B4D?style=for-the-badge&logo=confluence&logoColor=white) ![Slack](https://img.shields.io/badge/slack-4A154B?style=for-the-badge&logo=slack&logoColor=white) ![Jira](https://img.shields.io/badge/Jira-0052CC?style=for-the-badge&logo=Jira&logoColor=white) |

---
