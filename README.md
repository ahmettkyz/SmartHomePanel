# SmartHomePanel
Functional room thermostat with a simple design
# ESP32-S3 Industrial HMI Touch Interface

A robust, high-performance Human-Machine Interface (HMI) designed for the ESP32-S3 7-inch RGB Touch LCD. Built with **Arduino IDE** and **LVGL 8.3.11**, this project demonstrates real-time data monitoring, stable UI rendering, and persistent storage management using NVS, while maintaining an active WiFi connection without DMA/PSRAM bottlenecks.

## 📸 Screenshots
<img width="1536" height="2048" alt="IMG_2739" src="https://github.com/user-attachments/assets/151cb4b6-5e9e-4dc4-aaf4-735f9646c6ab" />
<img width="1536" height="2048" alt="IMG_2740" src="https://github.com/user-attachments/assets/7f30be09-8965-49e3-a5d6-9dd889e1eb5b" />
<img width="1536" height="2048" alt="IMG_2741" src="https://github.com/user-attachments/assets/0f997a1a-097d-4d2d-8ba3-50eb6af25ea9" />
<img width="1536" height="2048" alt="IMG_2742" src="https://github.com/user-attachments/assets/67fbff5e-88de-454f-8f27-c0ddfd5ff44e" />
<img width="1536" height="2048" alt="IMG_2743" src="https://github.com/user-attachments/assets/5975568c-2e2e-41d8-a5fe-d4f481c0cc2a" />

## 🚀 Features
* **High-Performance UI:** Smooth, anti-aliased graphics rendering using LVGL 8.3.11.
* **Dual-Core Processing:** UI rendering and WiFi/Network operations are isolated on separate cores to prevent interrupt conflicts.
* **Non-Volatile Storage (NVS):** Persistent saving of user configurations and operational states.
* **PSRAM Optimized:** Custom display driver configurations to handle 800x480 RGB panel frame buffers via OPI PSRAM without screen tearing.

## 🛠️ Hardware Requirements
* **Board:** Waveshare ESP32-S3-Touch-LCD-7
* **MCU:** ESP32-S3 dual-core Xtensa LX7
* **Memory:** 16 MB Flash, 8 MB OPI PSRAM (Crucial for LVGL frame buffers)
* **Display:** 7-inch 800x480 RGB Parallel LCD with Capacitive Touch

## ⚙️ Technical Optimizations (Solving PSRAM & WiFi Conflicts)
Driving a large RGB display via DMA while maintaining a WiFi connection often leads to screen tearing or shifting due to PSRAM bandwidth limits and core interrupts. This project implements several critical optimizations:
1. **PCLK Adjustment:** Reduced the pixel clock frequency (e.g., to 14-16 MHz) to allow the DMA controller sufficient time to fetch the frame buffer from PSRAM.
2. **Core Pinning (FreeRTOS):** Network tasks (WiFi) are constrained to Core 0, while the LVGL `lv_timer_handler` and display flushing routines are pinned to Core 1 using `xTaskCreatePinnedToCore`.
3. **Memory Allocation:** Enforced OPI mode for both Flash and PSRAM in the IDE settings to maximize bus throughput.

## 💻 Installation & Setup

1. **IDE Setup:** Open Arduino IDE and install the ESP32 board package (v2.0.x or higher recommended).
2. **Dependencies:** Install the following libraries via the Library Manager:
   * `lvgl` (Version 8.3.11)
   * `esp32` (Version 3.0.2)
   * `NVS`
3. **Board Configuration (Tools Menu):**
   * Board: ESP32S3 Dev Module
   * Flash Size: 16MB (128Mb)
   * Partition Scheme: 16M Flash (3MB APP/9.9MB FATFS)
   * PSRAM: **OPI PSRAM** *(Do not use QSPI)*
   * Flash Mode: OPI (or QIO depending on board rev)
4. **LVGL Configuration:** Replace the default `lv_conf.h` with the one provided in this repository's `include/` folder.
5. Compile and upload.
6. Important Note on lv_conf.h:
This project requires specific LVGL configuration parameters tailored for the ESP32-S3 7-inch RGB display to ensure optimal PSRAM usage and avoid frame buffer tearing. We highly recommend using the manufacturer's verified configuration.

Please download the official lv_conf.h provided by Waveshare here:
👉 [Waveshare ESP32-S3-Touch-LCD-7 Resources](https://docs.waveshare.com/ESP32-S3-Touch-LCD-7/Resources-And-Documents)

Once downloaded, place the lv_conf.h file inside the include/ directory of this project or directly inside your Arduino libraries/lvgl/ folder before compiling.

## 📂 Project Structure
```
├── src/
│   ├── SmartHomePanel.ino      
│   └── ui/                     
│       ├── ui.c / ui.h         
│       ├── ui_events.c         
│       ├── screens/
│       ├── images/           
│       └── fonts/           
├── include/
│   └── lv_conf.h               
│                              
│                               # https://docs.waveshare.com/ESP32-S3-Touch-LCD-7/Resources-And-Documents
├── SquareLine_Project/         
│   ├── SquareLine_Project.spj  
│   └── assets/                 
├── README.md                   
└── LICENSE                    
