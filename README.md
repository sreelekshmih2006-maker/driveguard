# DriveGuard 
### Intelligent Driver Safety Monitoring & Warning System for Hazardous Road Conditions

> Sense the environment. Understand the road. Warn before it's too late.

DriveGuard is a low-cost, multi-sensor driver-assistance system designed to improve road safety in hazardous driving environments such as fog-prone roads, mountainous terrain, hairpin bends, sharp curves, and low-visibility conditions.

The system combines environmental sensing, vehicle-dynamics monitoring, GPS-based hazard awareness, terrain estimation, and obstacle detection to generate a real-time driving-risk assessment and provide timely visual, audible, and haptic warnings.

---

## 📌 Overview

Driving through mountainous and fog-prone regions presents several safety challenges:

* Reduced visibility
* Sharp and unexpected turns
* Sudden obstacles
* Steep terrain
* Vehicle instability during cornering
* Delayed driver reaction

Conventional Advanced Driver Assistance Systems (ADAS) can address many of these challenges, but their cost and integration requirements make them inaccessible for many conventional vehicles.

DriveGuard addresses this gap through an affordable embedded architecture that combines multiple low-cost sensors with intelligent sensor fusion.

---

## 🎯 Objectives

DriveGuard is designed to:

* Monitor environmental conditions affecting visibility.
* Identify fog-favorable conditions.
* Detect hazardous terrain and predefined accident-prone zones.
* Monitor vehicle movement and orientation.
* Detect nearby obstacles.
* Provide early warnings for upcoming hazards.
* Evaluate overall driving risk using sensor fusion.
* Improve driver awareness without requiring expensive automotive hardware.

---

# 🧠 System Architecture

```text
                         DRIVEGUARD
                             │
                             ▼
                    ┌─────────────────┐
                    │   ESP32 / MCU   │
                    │  Control Layer  │
                    └────────┬────────┘
                             │
          ┌──────────────────┼──────────────────┐
          │                  │                  │
          ▼                  ▼                  ▼
   ENVIRONMENTAL       VEHICLE DYNAMICS    LOCATION / TERRAIN
      SENSING              SENSING             AWARENESS
          │                  │                  │
    ┌─────┴─────┐       ┌────┴─────┐      ┌────┴─────┐
    │Temp/Humid.│       │Acceler.  │      │   GPS    │
    │Air Quality│       │Gyroscope │      │ Barometer│
    └─────┬─────┘       └────┬─────┘      └────┬─────┘
          │                  │                  │
          └──────────────────┼──────────────────┘
                             │
                             ▼
                   ┌─────────────────────┐
                   │    SENSOR FUSION    │
                   │   & RISK ANALYSIS   │
                   └──────────┬──────────┘
                              │
                 ┌────────────┼────────────┐
                 │            │            │
                 ▼            ▼            ▼
              LOW          MEDIUM        HIGH/
                                         CRITICAL
                 │            │            │
                 └────────────┼────────────┘
                              ▼
                   ┌─────────────────────┐
                   │  DRIVER ALERTING    │
                   │                     │
                   │ OLED │ Buzzer │ Vibe│
                   └─────────────────────┘
```

---

# ✨ Key Features

| Feature                        | Description                                                                          |
| ------------------------------ | ------------------------------------------------------------------------------------ |
| 🌫️ **Fog Risk Assessment**    | Uses temperature, humidity and air-quality data to identify fog-favorable conditions |
| ⛰️ **Terrain Awareness**       | Uses barometric pressure and altitude estimation to identify elevated terrain        |
| 📍 **Hazard-Zone Detection**   | GPS identifies predefined dangerous road sections                                    |
| 🚗 **Vehicle Dynamics**        | Accelerometer and gyroscope detect abnormal vehicle movement                         |
| 🚧 **Obstacle Detection**      | Ultrasonic sensor detects nearby objects                                             |
| 🧠 **Sensor Fusion**           | Combines multiple sensor inputs into a unified risk score                            |
| 📺 **Real-Time Display**       | OLED displays environmental and safety information                                   |
| 🔊 **Audible Alert**           | Warns the driver during hazardous conditions                                         |
| 📳 **Haptic Alert**            | Provides vibration-based warnings                                                    |
| 🖐️ **Touch-Free Interaction** | Gesture/proximity sensor enables driver interaction                                  |

---

# 🔧 Hardware

## MYOSA Platform

DriveGuard is designed around the **MYOSA sensor ecosystem**, including:

* MYOSA Motherboard
* Accelerometer & Gyroscope
* Gesture / Proximity / RGB Sensor
* Barometric Pressure Sensor
* Temperature & Humidity Sensor
* Air Quality Sensor
* OLED Display
* Actuator

## Additional Hardware

* ESP32 / compatible microcontroller
* GPS module
* Ultrasonic sensor
* Power supply / battery

---

# 💻 Software Stack

| Layer                   | Technology                          |
| ----------------------- | ----------------------------------- |
| Microcontroller         | ESP32                               |
| Firmware                | C/C++                               |
| Development Environment | Arduino IDE                         |
| Communication           | I²C / UART / GPIO / Analog          |
| Display                 | SSD1306 OLED                        |
| Positioning             | GPS                                 |
| Environmental Sensing   | Temperature, Humidity & Air Quality |
| Risk Analysis           | Rule-based Sensor Fusion            |

---

# 🔌 Sensor Interfaces

The system uses multiple communication interfaces:

```text
                 ESP32
                   │
       ┌───────────┼────────────┐
       │           │            │
      I²C         UART         GPIO
       │           │            │
       ▼           ▼            ▼
   ┌────────┐   ┌─────┐   ┌────────────┐
   │ IMU    │   │ GPS │   │ Ultrasonic │
   │Barometer│   └─────┘   │  Actuator  │
   │ OLED   │              └────────────┘
   │Gesture │
   └────────┘

       Analog
          │
          ▼
   ┌────────────┐
   │Air Quality │
   └────────────┘
```

> **Note:** GPIO assignments and I²C addresses depend on the specific MYOSA hardware revision and should be verified before deployment.

---

# ⚙️ How It Works

DriveGuard follows a continuous sensing → analysis → decision → alert pipeline.

### 1. Environmental Sensing

The temperature and humidity sensor continuously monitors atmospheric conditions.

High humidity combined with suitable temperature conditions increases the probability of **fog-favorable conditions**.

The air-quality sensor provides additional information about airborne particles that may contribute to reduced visibility.

```text
Temperature
     +
Humidity
     +
Air Quality
     ↓
Environmental Analysis
     ↓
Fog Risk
```

---

### 2. Terrain & Location Analysis

The barometric sensor provides pressure measurements that can be used to estimate altitude.

GPS provides the vehicle's geographical position.

The current position can then be compared against a database of predefined hazardous locations.

```text
GPS Position
     +
Altitude
     +
Hazard Database
     ↓
Terrain / Location Risk
```

Potential hazard categories include:

* Hairpin bends
* Sharp curves
* Accident-prone zones
* Fog-prone zones
* Steep sections

---

### 3. Vehicle Dynamics Analysis

The accelerometer and gyroscope monitor vehicle motion.

The system analyzes:

* Lateral acceleration
* Longitudinal acceleration
* Angular velocity
* Sudden swerving
* Harsh braking
* Excessive cornering
* Vehicle tilt

```text
Acceleration + Angular Velocity
              ↓
       Motion Analysis
              ↓
     Unsafe Manoeuvre?
```

---

### 4. Obstacle Detection

The ultrasonic sensor continuously measures the distance to nearby objects.

Example thresholds:

```text
> 200 cm       → SAFE
100–200 cm     → MEDIUM
50–100 cm      → HIGH
< 50 cm        → CRITICAL
```

The thresholds can be calibrated according to the vehicle and sensor configuration.

---

### 5. Sensor Fusion

Individual risk factors are combined to calculate an overall road-risk score.

The current prototype uses:

| Risk Factor         | Weight |
| ------------------- | -----: |
| Environmental / Fog |    30% |
| Terrain             |    15% |
| Vehicle Motion      |    25% |
| Obstacle            |    20% |
| GPS Hazard          |    10% |

### Risk Equation

```text
Risk Score =
    0.30 × Fog Risk
  + 0.15 × Terrain Risk
  + 0.25 × Motion Risk
  + 0.20 × Obstacle Risk
  + 0.10 × GPS Hazard Risk
```

The resulting score is normalized to **0–100**.

---

# 🚦 Risk Classification

```text
┌───────────────┬──────────┬──────────────────────────┐
│ Score         │ Level    │ Response                 │
├───────────────┼──────────┼──────────────────────────┤
│ 0 – 24        │ LOW      │ Normal monitoring        │
│ 25 – 49       │ MEDIUM   │ Advisory warning         │
│ 50 – 74       │ HIGH     │ Audible + haptic alert   │
│ 75 – 100      │ CRITICAL │ Immediate warning        │
└───────────────┴──────────┴──────────────────────────┘
```

---

# 📺 Driver Interface

The OLED provides a compact real-time safety dashboard.

Example:

```text
┌────────────────────────┐
│      DRIVEGUARD        │
│                        │
│ Temp: 18.5°C  Hum: 92% │
│ Fog: 78%      Obj: 82cm│
│                        │
│ RISK: HIGH       67%   │
│                        │
│ HIGH FOG               │
└────────────────────────┘
```

Possible recommendations include:

* **REDUCE SPEED**
* **OBSTACLE AHEAD**
* **HIGH FOG**
* **SHARP TURN AHEAD**
* **HAZARD ZONE**
* **DRIVE SAFE**

---

# 🚨 Alert System

DriveGuard provides multimodal alerts:

### 🟢 Low Risk

No warning.

### 🟡 Medium Risk

Advisory notification on OLED and short alert.

### 🟠 High Risk

Audible and haptic warning.

### 🔴 Critical Risk

Continuous high-priority warning through the actuator.

---

# 📂 Repository Structure

```text
DriveGuard/
│
├── README.md
├── LICENSE
│
├── firmware/
│   └── DriveGuard.ino
│
├── hardware/
│   ├── schematic/
│   ├── pcb/
│   └── pinout/
│
├── documentation/
│   ├── architecture.md
│   ├── methodology.md
│   └── testing.md
│
├── images/
│   ├── system-architecture.png
│   ├── circuit-diagram.png
│   └── prototype.jpg
│
└── data/
    └── hazard-zones.csv
```

---

# 🚀 Getting Started

## Prerequisites

* ESP32 development board
* Arduino IDE
* MYOSA sensor modules
* GPS module
* Ultrasonic sensor
* USB cable
* Suitable power supply

---

## Required Libraries

Install the following libraries through the Arduino IDE Library Manager:

```text
DHT sensor library
Adafruit Unified Sensor
Adafruit GFX Library
Adafruit SSD1306
TinyGPS++
```

Additional libraries may be required depending on the exact MYOSA sensor models.

---

## Installation

Clone the repository:

```bash
git clone https://github.com/<username>/DriveGuard.git
```

Navigate to the project:

```bash
cd DriveGuard
```

Open:

```text
firmware/DriveGuard.ino
```

Select the appropriate ESP32 board and COM port in Arduino IDE.

Compile and upload the firmware.

---

# 📍 Hazard Database

DriveGuard supports predefined GPS hazard zones.

Each zone can contain:

```text
Latitude
Longitude
Detection Radius
Hazard Type
```

Example:

```cpp
{
    9.500000,
    76.330000,
    150,
    "HAIRPIN AHEAD"
}
```

This allows the system to provide **location-aware warnings before the vehicle reaches a known hazard**.

---

# 🧪 Testing & Validation

The prototype can be evaluated through controlled test scenarios.

### Environmental Test

Simulate high humidity and observe the change in fog-risk estimation.

### Obstacle Test

Place an object at different distances from the ultrasonic sensor and verify the corresponding risk levels.

### GPS Test

Configure a test hazard zone and verify that the system detects the vehicle entering the defined radius.

### Motion Test

Apply controlled acceleration and rotation to the IMU and verify unsafe-motion detection.

### Integrated Test

Combine environmental, location, motion, and obstacle conditions to validate the sensor-fusion response.

---

# 📊 Example Scenario

### Scenario: Mountain Road + Dense Fog

```text
Temperature       → 18°C
Humidity          → 92%
Air Quality       → Elevated
Altitude          → High
GPS               → Hazard Zone
Obstacle Distance → 80 cm
Vehicle Motion    → Normal
```

The system combines these measurements and generates:

```text
Environmental Risk → HIGH
Terrain Risk       → HIGH
Obstacle Risk      → HIGH
Location Risk      → HIGH

Overall Risk       → HIGH
```

Driver notification:

```text
⚠ HIGH RISK

HIGH FOG
HAZARD ZONE
OBSTACLE AHEAD

RECOMMENDED:
REDUCE SPEED
```

---


# 🌍 Applications

DriveGuard can be adapted for:

* Mountain roads
* Ghat sections
* Fog-prone highways
* Hill stations
* Passenger vehicles
* Public transportation
* Commercial transport
* Emergency vehicles

---

# ⚠️ Safety Notice

DriveGuard is a research and prototype project intended to assist drivers.

It should not be considered a certified automotive safety system. All hardware, thresholds, algorithms, and warning mechanisms must undergo appropriate validation before any real-world deployment.

---

# 👥 Project



https://github.com/user-attachments/assets/e8352895-28c3-4eb3-92e0-553f241ed4dc<img width="1600" height="900" alt="WhatsApp Image 2026-08-25 at 9 50 50 PM" src="https://github.com/user-attachments/assets/d5219e91-e747-4165-a447-387d9f40a9c6" />










**DriveGuard**

An intelligent, affordable approach to driver safety in hazardous road environments.

> **Sense the environment. Understand the road. Warn before it's too late.**

