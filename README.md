# 🤖 Autonomous Vehicle — Main Control Board (OW_Main_PCB)

> **Designed by:** Janardhan BV  
> **Tool:** KiCad EDA 9.0.7  
> **MCU:** STM32 Nucleo F446RE (ARM Cortex-M4, 180 MHz)  
> **File:** `Ow_main_pcb.kicad_sch`  
> **Type:** Personal Project — Autonomous Vehicle Main Controller

---

## 📌 Project Overview

This is the **main control board (MCB)** for an autonomous wheeled vehicle, designed to act as the central hub that interfaces the **STM32 Nucleo F446RE** with all vehicle subsystems — motor PWM outputs, relay-controlled actuators, RS-485 temperature sensing, W5500 Ethernet connectivity, and an RGB error-status LED. The board is designed in **KiCad EDA 9.0.7** on a rectangular PCB with four corner mounting holes for secure chassis mounting.

The MCB features:
- **STM32 Nucleo F446RE** piggyback mount on dual female header rows
- **W5500 Ethernet controller** for remote telemetry and control over TCP/IP
- **MAX485 RS-485 transceiver** for robust long-cable sensor/motor communication
- **2× OMRON G5LE-1 relays** for 12V actuator switching
- **4× PWM output ports** (Molex 4-pin) for motor speed control
- **2× Digital I/O ports** for external peripherals
- **RGB error indicator LED** with PWM dimming
- **Dual XT30 power inputs** (12V) with AMS1117-3.3 onboard LDO

---

## 🖼️ Project Visuals

### 3D Top View
![3D Top View](images/TopView.jpg)

### Schematic
![Schematic](images/SCH.pdf)

---

## 🏗️ System Architecture

```
12V Power (XT30 ×2)
      │
      ├──► Relay K1, K2 (12V switched outputs — G5LE-1)
      │
      └──► AMS1117-3.3 ──► 3.3V rail
              │
              ▼
      STM Nucleo F446RE (U3)  ◄──── USB/ST-LINK (onboard)
              │
   ┌──────────┼──────────────────────────────────┐
   ▼          ▼            ▼                    ▼
W5500       MAX485      4× PWM Ports      2× Relay Drivers
Ethernet    RS-485      (Motor Control)   (BSS138 / AO3400A)
(SPI 3.3V)  UART Bridge  PWM_P1–P4        K1, K2
   │                         │
   ▼                    Molex 4-pin
TCP/IP                   Connectors
Remote Ctrl              (U7–U11)
                              │
                    Temperature Sensor (RS-485 Bus)
                              │
                         Digital_P1, Digital_P2
                              │
                    RGB Error LED (R, G, B + PWM)
```

---

## 📊 Component BOM

### Core ICs & Modules

| Ref | Component | Part | Function |
|:---:|:---|:---|:---|
| U3 | MCU Board | STM Nucleo F446RE | Main controller (STM32F446RET6, 180 MHz) |
| U1 | Ethernet Controller | W5500 | SPI hardwired TCP/IP Ethernet |
| U2 | RS-485 Transceiver | MAX485 (DIP-8) | UART ↔ RS-485 bridge |
| K1, K2 | Relay | OMRON G5LE-1 | 10A SPDT 12V relay (×2) |
| U5 | LDO Regulator | AMS1117-3.3 | 5V → 3.3V supply |
| U6, U4 | Power Connector | XT30 | 12V input (×2) |

### MOSFETs

| Ref | Part | Type | Function |
|:---:|:---|:---|:---|
| Q1, Q2, Q3 | BSS138 | N-ch logic-level FET | Relay coil driving / signal switching |
| Q4, Q6, Q7 | AO3400A | N-ch 5.7A / 30V FET | High-current relay / actuator drive |
| Q5, Q8 | 2N7002 | N-ch logic-level FET | Level shifting / enable control |

### Passives & Indicators

| Ref | Value | Function |
|:---:|:---:|:---|
| R1–R6, R8, R11, R13, R16, R18 | 10 kΩ | Pull-up / pull-down resistors |
| R7, R12, R14 | 10 Ω | Current limiting / damping |
| R9, R15 | 1 kΩ | Gate / base resistors |
| R10, R17 | 680 Ω | LED current limiting |
| C1 | 1000 µF | Bulk supply decoupling |
| D2 | LED (Red) | Error RGB — Red channel |
| D4 | LED (Green) | Error RGB — Green channel |
| D3 | LED (Blue) | Error RGB — Blue channel |
| H1–H4 | MountingHole_Pad | PCB corner mounting holes |

### Peripheral Connectors

| Ref | Type | Signals | Function |
|:---:|:---:|:---|:---|
| U7 | Molex 4-pin | PWM_P1 | PWM Motor Port 1 |
| U8 | Molex 4-pin | PWM_P2 | PWM Motor Port 2 |
| U9 | Molex 4-pin | PWM_P3 | PWM Motor Port 3 |
| U10 | Molex 4-pin | PWM_P4 | PWM Motor Port 4 |
| U11 | Molex 4-pin | Digital_P1/P2 | Digital I/O Ports |

---

## 🔌 Signal Net Map — STM32 Nucleo F446RE

| Net Name | Nucleo Pin | Function |
|:---|:---:|:---|
| ETH_SPI_MOSI_3v3 | PA7 | W5500 SPI MOSI |
| ETH_SPI_MISO_3v3 | PA6 | W5500 SPI MISO |
| ETH_SPI_SCK_3v3 | PA5 | W5500 SPI Clock |
| ETH_SPI_CS_3v3 | PA4 | W5500 SPI Chip Select |
| temp_uart_rs485_5V_tx | UART TX | MAX485 DI (RS-485 TX) |
| temp_uart_rs485_5V_rx | UART RX | MAX485 RO (RS-485 RX) |
| temp_max485_en_3v3 | GPIO | MAX485 RE/DE enable |
| PWM_P1 – PWM_P4 | TIM PWM pins | 4-channel motor speed control |
| Relay_Signal_1 | GPIO | Relay K1 coil trigger |
| Relay_Signal_2 | GPIO | Relay K2 coil trigger |
| error_rgb_led_pwm_3v3_R | TIM PWM | RGB LED Red (PWM) |
| error_rgb_led_pwm_3v3_G | TIM PWM | RGB LED Green (PWM) |
| error_rgb_led_pwm_3v3_B | TIM PWM | RGB LED Blue (PWM) |
| Digital_P1, Digital_P2 | GPIO | External digital peripherals |

---

## ⚙️ Subsystem Details

### 🌐 Ethernet — W5500
The **W5500** is a hardwired TCP/IP Ethernet controller that offloads the full network stack (TCP, UDP, IPv4, ARP, ICMP) from the STM32. [web:79] It communicates via **SPI at up to 80 MHz** and supports **8 simultaneous sockets** with 32KB internal buffer. [web:81] This enables:
- Remote **telemetry streaming** (speed, sensor data, state)
- Remote **command injection** over TCP/UDP from a control PC or server
- **OTA firmware update** capability via Ethernet

### 📡 RS-485 — MAX485
The **MAX485** is a half-duplex RS-485/RS-422 transceiver driven by the STM32 UART. [file:71] It provides:
- **Up to 2.5 Mbps** data rate
- **±7V common-mode range** — robust against ground shifts in vehicle environments
- Communication with **temperature sensors** or **motor controllers** on a daisy-chain RS-485 bus
- RE/DE pin controlled by MCU GPIO (`temp_max485_en`) for TX/RX direction switching

### ⚡ Relay Control — G5LE-1 (K1, K2)
Two **OMRON G5LE-1** relays switch 12V loads (actuators, lights, emergency stop) with a rated switching capacity of 10A at 250VAC / 30VDC. [file:71] Each relay is driven by an **AO3400A N-channel MOSFET** (Q4/Q6) with:
- 10kΩ gate pull-down for default OFF state
- BSS138 logic-level gate driver
- Flyback diode protection on coil

### 🎛️ PWM Motor Control
Four **Molex 4-pin ports (U7–U11)** expose PWM signals generated by the STM32's hardware timers. [file:71] Each port carries:
- PWM signal (motor speed reference)
- 5V / 12V power
- GND
- Optional digital I/O

### 🔴 RGB Error LED
An **RGB LED (D2/D3/D4)** provides visual system status: [file:71]

| Color | State |
|:---:|:---|
| 🟢 Green | System OK / Running |
| 🟡 Yellow (R+G) | Standby / Initializing |
| 🔴 Red | Error / Fault detected |
| 🔵 Blue | Communication active |
| ⚪ White (all) | Bootup / Reset |

PWM versions (`error_rgb_led_pwm_3v3_R/G/B`) allow brightness control from 3.3V MCU GPIO.

---

## ⚡ Power Architecture

```
12V Input (XT30 ×2)
   ├──► Relay K1, K2 (Relay_COM / Relay_Out — 12V switched)
   └──► 5V (from Nucleo E5V pin)
             └──► AMS1117-3.3 ──► 3.3V (W5500, MAX485, MOSFETs, LEDs)
```

| Rail | Source | Consumers |
|:---:|:---|:---|
| 12V | XT30 connector | Relays (K1, K2), Actuator outputs |
| 5V | Nucleo E5V / USB | MCU, Molex port power, MAX485 VCC |
| 3.3V | AMS1117-3.3 | W5500, MCU I/O, MAX485 logic, RGB LED |

---

## 📐 PCB Design Highlights

- **EDA Tool:** KiCad EDA 9.0.7
- **File:** `Ow_main_pcb.kicad_sch`
- **Board Shape:** Rectangular with 4× corner mounting holes (H1–H4) for chassis mount
- **MCU Mount:** STM Nucleo F446RE piggybacks via dual female 2.54mm pin header rows
- **W5500:** Module connector on right edge (SPI 3.3V + RST + INT)
- **Relays K1, K2:** Through-hole, positioned on left half for easy wiring to actuators
- **XT30 Power Inputs:** Dual XT30 connectors for redundant 12V supply or load balancing
- **Bulk Cap C1 = 1000µF:** Power supply stiffening for relay coil switching transients
- **MAX485 (DIP-8):** Socketed, top-left area, close to RS-485 screw terminals
- **RGB LED:** Bottom-right corner for clear visibility during operation

---

## 🧪 Bring-Up & Testing Procedure

### Step 1 — Power Rail Verification
- Apply 12V via XT30 connector
- Measure: 3.3V at AMS1117 output, 5V at Nucleo 5V pin
- Idle current expected: ~120–200 mA (Nucleo + W5500 + logic)

### Step 2 — Nucleo Programming
- Connect USB to Nucleo ST-LINK port
- Flash STM32CubeIDE / PlatformIO project
- Verify UART output on `temp_uart_3v3_rx/tx`

### Step 3 — Ethernet Connectivity
- Connect RJ45 to W5500 module
- Ping the board IP from host PC
- Verify 8 socket handles, send/receive TCP packet

### Step 4 — RS-485 Communication
- Connect RS-485 sensor/device on MAX485 A/B terminals
- Send AT command / protocol frame from STM32 UART
- Verify loopback response and error-free receive

### Step 5 — Relay Test
- Set `Relay_Signal_1` GPIO HIGH
- Verify K1 coil energizes (click sound, ~12V on COM→OUT1)
- Test K2 same way; verify flyback diode absorbs coil spike

### Step 6 — PWM Motor Output
- Configure STM32 TIM for 20 kHz PWM on PWM_P1–P4
- Connect motor driver to Molex 4-pin connectors
- Sweep duty cycle 10%→90% — verify motor speed response

### Step 7 — Full System Integration
- Load autonomous vehicle firmware
- Verify telemetry stream over Ethernet to control station
- Test emergency stop via relay Relay_Signal_2
- Validate RGB LED state machine (Green = OK, Red = fault)

---

## 🖥️ Firmware Stack

```
STM32CubeIDE / PlatformIO (STM32F446RE target)
├── HAL_SPI         → W5500 Ethernet (ioLibrary_Driver / WIZnet HAL)
├── HAL_UART        → MAX485 RS-485 temperature bus
├── HAL_TIM_PWM     → 4× PWM motor ports (TIM1/TIM2/TIM3/TIM4)
├── HAL_GPIO        → Relay control, RGB LED, Digital ports
└── FreeRTOS        → Task scheduler (Sensing, Control, Telemetry)
```

---

## 📁 Repository Structure

```
AV-Main-Control-Board/
├── images/
│   ├── TopView.jpg                ← 3D board render
│   └── SCH.pdf                   ← KiCad schematic export
├── KiCad/
│   ├── Ow_main_pcb.kicad_sch     ← Schematic source
│   ├── Ow_main_pcb.kicad_pcb     ← PCB layout source
│   └── Ow_main_pcb.kicad_pro     ← Project file
├── Gerber/
│   └── (Gerber + drill files)
├── BOM/
│   └── BOM_AV_Main_PCB.csv
├── Firmware/
│   └── av_main_fw/
└── README.md
```

---

## 🏷️ GitHub Topics to Add

```
autonomous-vehicle  stm32  nucleo-f446re  w5500  ethernet
max485  rs485  kicad  pcb-design  motor-control  relay
embedded-systems  robotics  pwm  tcp-ip
```

---

## ⚠️ Design Notes & Cautions

- **XT30 polarity** — always verify before powering; reverse polarity will damage AMS1117 and MCU
- **AO3400A gate drive** — logic-level FET, fully enhanced at 3.3V (Vgs(th) = 0.45–1.0V typ.)
- **W5500 is 3.3V only** — do not connect 5V to any W5500 pin; level-shift if feeding from 5V MCU GPIO
- **MAX485 RE/DE** — ensure MCU drives `temp_max485_en` LOW before entering receive mode to avoid bus contention
- **G5LE-1 coil voltage = 12V** — ensure relay drive MOSFETs (AO3400A) pull coil to GND; 12V is supplied via XT30
- **C1 = 1000µF** must be low-ESR type (electrolytic or tantalum) to handle relay switching transients cleanly

---

## 📄 License

This project is open for educational and personal use.  
© 2025 Janardhan BV — All rights reserved.

---

## 🙋 Author

**Janardhan BV**  
Embedded Hardware Engineer | PCB Design | Power Electronics  
📍 Bengaluru, India

---
*Designed in KiCad EDA 9.0.7 | STM32 Nucleo F446RE — Autonomous Vehicle Main Control Board*
