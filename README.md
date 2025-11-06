# 🛰 Receiving Unknown-Sized Continuous Data with STM32

[![Platform](https://img.shields.io/badge/platform-STM32G071RB-blue.svg)]()
[![Language](https://img.shields.io/badge/language-C-green.svg)]()
[![License](https://img.shields.io/badge/license-Educational-lightgrey.svg)]()
[![Framework](https://img.shields.io/badge/framework-STM32CubeIDE-orange.svg)]()
[![Protocol](https://img.shields.io/badge/protocol-UART%20+%20DMA-yellow.svg)]()

---

## 📘 Project Overview

**Receiving Unknown-Sized Continuous Data with STM32** is an embedded communication system developed on the **Nucleo-G071RB** board.  
It is designed to **receive continuous and unknown-sized data via UART** without CPU intervention, using **DMA, Interrupts, and Receiver Timeout (RTO)** mechanisms for efficient and lossless data acquisition.
The goal was to **create a demo system** that mimics a **conveyor-based weighing system**, where weight data from an industrial transmitter is processed in real time without missing packets.

---

## ⚙️ Key Features

✅ **Continuous UART reception** with unknown data size  
✅ **DMA + Interrupt architecture** for efficient data handling  
✅ **Receiver Timeout (RTO)** to detect end of transmission  
✅ **Non-blocking, event-driven design (no polling)**  
✅ **Buffer management** with overrun and timeout protection  
---

## 🧠 System Architecture
### 🧩 Data Flow

```
     ┌────────────────────────────┐
     │  BAYKON TX20 Transmitter   │
     │  (Weight Data Output)      │
     └──────────┬─────────────────┘
                │  RS-232 → UART
                ▼
     ┌────────────────────────────┐
     │   STM32G071RB MCU (NUCLEO) │
     │                            │
     │ ┌────────────────────────┐ │
     │ │ UART + DMA Controller  │ │  → Handles async data reception
     │ └────────────────────────┘ │
     │            │
     │            ▼
     │ ┌────────────────────────┐ │
     │ │ Receiver Timeout (RTO) │ │  → Detects end of frame
     │ └────────────────────────┘ │
     │            │
     │            ▼
     │ ┌────────────────────────┐ │
     │ │ Interrupt Handler (ISR)│ │  → Flags and callbacks
     │ └────────────────────────┘ │
     │            │
     │            ▼
     │ ┌────────────────────────┐ │
     │ │ Data Buffer Management │ │  → RxRawData, RxBuffer, ProcessedData
     │ └────────────────────────┘ │
     │            │
     │            ▼
     │ ┌────────────────────────┐ │
     │ │ LED + User Button      │ │  → Debugging and control
     │ └────────────────────────┘ │
     └────────────────────────────┘
```

---

## 🧩 Hardware Connections

| STM32 Pin   | Peripheral | Function        |
|--------------|-------------|----------------|
| PA9          | UART1_TX   | Data Transmission |
| PA10         | UART1_RX   | Data Reception |
| PC13         | User Button| Start Reception |
| LED_GREEN    | Debug LED  | Status Indicator |
| GND          | Common     | Ground Reference |

- **Communication:** RS-232 → UART (via level converter)  
- **Baud rate:** 19200 bps  
- **Timeout threshold:** 960 USART clock cycles (≈500 ms)  

---

## 💾 Data Structure

### RX Buffers
| Buffer | Size | Purpose |
|:--------|:------:|:----------|
| `RxRawData` | 300 bytes | Raw incoming data from DMA |
| `RxBuffer` | 18 bytes | Parsed payload |
| `ProcessedData` | Variable | Converted readable format |

### Weight Frame Format (TX20)
```
>m00  1337     0
```

→ Processed as `1.337 kg` after parsing.

---

## ⚙️ Software Architecture

| Module        | Functionality                                  |
|----------------|------------------------------------------------|
| **UART**       | Handles serial data via USART peripheral       |
| **DMA**        | Transfers data without CPU load                |
| **EXTI**       | Detects button presses                         |
| **NVIC**       | Prioritizes and manages interrupts             |
| **RTO**        | Detects end of data frame                      |
| **LED Control**| Debug feedback for valid ranges                |

---

## 🧰 Development Environment

| Component | Tool |
|:-----------|:----:|
| **MCU** | STM32G071RB (NUCLEO-G071RB) |
| **IDE** | STM32CubeIDE |
| **Configurator** | STM32CubeMX |
| **Debugger** | ST-Link |
| **Language** | C (LL & HAL Libraries) |
| **Terminal** | Python Serial App / Hercules |
| **OS** | Windows / Ubuntu |

---

## 🧪 Verification & Results

### ✅ Objective 1: Receive Data from PC  
- Sent test strings via Python Serial App.  
- Verified correct parsing and structure alignment.  
- Observed continuous data without loss or overflow.

### ✅ Objective 2: Receive Data from Indicator  
- BX21S indicator sent real-time weight values.  
- STM32 successfully processed and displayed them as decimal weights.  
- LED turned **ON** when weight between **1.330 – 1.340 kg**.

---

## ⚡ Algorithms & Interrupts

- **Receiver Timeout (RTO):** detects silence and ends frame  
- **DMA Callback:** signals data reception completion  
- **NVIC Interrupt:** handles overrun, parity, and timeout events  
- **Bitmasking:** extracts sign and decimal position from registers  
- **LED Logic:** compares processed weights for threshold indication  

---

## 🚀 Future Improvements

- 💾 Integrate **Flash or SD logging** for long-term data storage  
- 🧮 Add **CRC or checksum** for data validation  
- ⚙️ Implement **DMA double-buffering** for seamless streaming  
- 🔄 Support **bidirectional UART** communication  
- 📊 Develop a **Python visualization dashboard**

---

## 🧑‍💻 Author

- 👤 **Oğuz Mert Coşkun**  
- 🎓 Electrical & Electronics Engineering — Özyeğin University  
- 📧 [oguzmertcoskun2004@gmail.com](mailto:oguzmertcoskun2004@gmail.com)  
- 🔗 [GitHub: Omert2004](https://github.com/Omert2004)

---

## 📄 License

This repository is for **educational and research purposes only.**  
You are free to use or extend it with proper credit to the author.

---

## 📂 Repository Structure

```
STM32-Receiving-Unkown-Sized-Continuous-Data-with-UART/
│
├── UART1_BX21S/           # Real indicator communication
├── UART2_PC_Telemetry/    # PC → STM32 communication test
│   ├── Core/
│   ├── Drivers/
│   ├── STM32CubeMX files
│   └── main.c
│
├── Documentation/
│   └── EE300_Summer_Practice_Report.pdf
│
├── README.md
└── LICENSE
```

### 🧩 Keywords
`STM32` `UART` `DMA` `Interrupts` `ReceiverTimeout` `EmbeddedSystems`  
`RealTimeData` `LLLibrary` `HAL` `Baykon` `IndustrialAutomation`
