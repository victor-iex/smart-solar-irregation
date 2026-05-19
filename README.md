# Smart-Solar-Irrigation

# Smart Automated Irrigation System with Web Dashboard

An automated, non-blocking soil moisture monitoring and irrigation system built using an **ESP8266** microcontroller. The system features an intelligent 5-second dry verification guard to eliminate sensor noise, automated pump control via a transistor-driven relay, and a local real-time Wi-Fi web dashboard to track pump status and raw moisture levels.

---

## 🚀 Features

* **Intelligent Verification Guard:** Prevents false triggers. The pump will only activate if the soil remains continuously dry for more than 5 seconds.
* **Safety Runtime Limit:** Automatically shuts off the pump after 10 seconds of continuous operation to protect against water overflow or running the pump dry.
* **Local Web Dashboard:** Hosts a lightweight, mobile-responsive local HTTP server that refreshes every 2 seconds to showcase live system states.
* **Non-Blocking Architecture:** Uses `millis()` for internal timing tasks instead of restrictive `delay()` functions, ensuring the web server remains responsive at all times.
* **Hardware Interface Optimization:** Integrates a BC547 NPN transistor to flawlessly bridge the 3.3V logic level threshold mismatch common with active-low 5V relay modules.

---

## 🛠️ Hardware Requirements

* **Microcontroller:** ESP8266 NodeMCU or D1 Mini
* **Sensor:** Soil Moisture Sensor (Capacitive or Resistive)
* **Actuator:** 5V DC Submersible Water Pump / Solenoid Valve
* **Switching Element:** 5V Relay Module (Active-Low or Active-High)
* **Level Shifter / Driver:** BC547 NPN Transistor & $1\text{ k}\Omega$ Resistor
* **Power Source:** 5V External Power Supply (Shared GND with ESP8266)

---

## 🔌 Circuit Wiring Schematic

The system uses a BC547 transistor setup to handle the Active-Low trigger on standard 5V relay modules while preserving intuitive Active-High logic rules inside the firmware codebase.

```text
                  +5V (From Power Supply)
                   |
                   | [Relay VCC]
                   +-------------------+
                                       |
  ESP8266 Pin D2      1k Resistor   [Relay IN]
   (GPIO 4) -----------[ R ]-----------| Base (Middle Pin)
                                       | [BC547 NPN Transistor]
                                       | Emitter -> GND



Components / Module,Pin Component,Pin Microcontroller (ESP8266),Notes
Soil Moisture Sensor,VCC,3V3 / 3.3V,Power input
,GND,GND,Ground reference
,A0 / Signal,A0,Analog read interface


BC547 Transistor,Base (Pin 2),D2 (GPIO 4),Must connect through a 1 kΩ resistor
,Collector (Pin 1),Relay IN Pin,Sinks voltage to ground when activated
,Emitter (Pin 3),GND,Ground loop link


Relay Module,VCC,VIN / 5V,Local or external power rail
,GND,GND,Shared ground framework



💻 Software & Firmware Installation

Prerequisites
Download and install the Arduino IDE.
Install the ESP8266 board definitions package inside the Arduino IDE:Go to File > Preferences.Add the following link to the Additional Boards Manager URLs:http://arduino.esp8266.com/stable/package_esp8266com_index.jsonGo to Tools > Board > Boards Manager, search for esp8266, and install the package.

ConfigurationsOpen the .ino sketch file in your Arduino IDE.Update the network credentials lines near the header to connect with your home router network environment:
C++const char* ssid = "YOUR_WIFI_SSID";
const char* password = "YOUR_WIFI_PASSWORD";
Set your board target (Tools > Board > ESP8266 Boards > NodeMCU 1.0) and select the correct COM Port.Hit Upload.


📊 System Logic FrameworkThe internal automation loops operate on the following predefined numerical logic constraints:Dry Soil Threshold: Reads values $> 500$ (typically outputs a max value of 1024 when resting completely out of water).Wet Soil Threshold: Reads values $< 500$ (typically reads 400 to 600 when fully submerged or damp).Dry Validation Rule: If the sensor output fluctuates into wet territory for a brief instant during the 5-second window, the startup sequence timer instantly clears itself to prevent unnecessary relay wear.

🖥️ Local Monitoring UI DashboardOnce the device successfully links up with your local Wi-Fi router network, open your serial monitor tool configured at 115200 baud rate to locate the active device address:PlaintextWiFi connected!
Local IP Address to access dashboard: 192.168.1.15
HTTP Web Server Started.
Launch a standard web browser application from a laptop or mobile phone terminal residing on that exact same network domain and direct the navigation bar directly to that IP address string (http://192.168.1.15). It will display a live interface showcasing raw statistics alongside the active machine status indicator.
