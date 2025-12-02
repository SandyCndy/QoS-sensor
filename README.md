# 📶 TRAI Network QoS Sensor using ESP32

This project presents a **real-time Quality of Service (QoS) monitoring sensor** built using the **ESP32 DevKit V1**. It is designed to analyze mobile network quality indicators like **RSSI (Received Signal Strength Indicator)** and **ping latency** to a public server such as Google.com. The sensor broadcasts this data over a **local Wi-Fi network**, allowing users to access a live dashboard through a web browser without needing internet connectivity.

This project was built for academic research under the **Telecom Regulatory Authority of India (TRAI)** to demonstrate a **low-cost, simulation-based network QoS monitoring system** suitable for drive tests and performance evaluations.

---

## 🎯 Objective

To build a **portable, low-cost, real-time network QoS sensor** using ESP32 that:
- Measures and displays **Wi-Fi signal strength** and **internet latency**
- Hosts a **local HTML dashboard**
- Requires minimal hardware
- Can be extended with live MQTT streaming, data logging, or even cloud sync

---

## 🛠️ Features

- 📡 Measures **RSSI** in dBm
- ⏱️ Calculates **ping latency** to external servers
- 🌐 Hosts a **local web dashboard** with auto-refresh
- 🧠 Extremely **lightweight**, runs on bare ESP32
- 🔌 Works offline after local Wi-Fi connection
- 🎓 Ideal for **academic demo, research papers**, or **drive test field deployments**

---

## 🧱 Hardware Requirements

| Component        | Details                       |
|------------------|-------------------------------|
| ESP32 Board      | DOIT ESP32 DevKit V1          |
| USB Cable        | Micro USB for power & upload  |
| Laptop           | With Arduino IDE (v2.3+ recommended) |
| Wi-Fi            | ESP32 hosts Wi-Fi network     |

---


### ✅ Dashboard Output on Browser
![WhatsApp Image 2025-07-24 at 12 57 11_20bdccd7](https://github.com/user-attachments/assets/9facba8f-6b85-46d2-b509-c604a0d4d0db)

### 🔌 Device Setup
![WhatsApp Image 2025-07-24 at 12 57 10_e1059da6](https://github.com/user-attachments/assets/9ff7827b-719a-494e-a216-af86c003d2c5)


---

## 📊 System Architecture

ESP32 ↔ Wi-Fi ↔ Internet
      ↓
   Local Web Server
      ↓
User Browser → Dashboard (HTML)

## 🖥 How It Works

- 🟢 **ESP32 boots up and hosts its own Wi-Fi network** (SSID: `antivirus`)
- 📱 **User connects to this Wi-Fi** from any nearby phone or laptop
- 📶 ESP32 measures:
  - **RSSI** using `WiFi.RSSI()` to evaluate signal strength
  - **Ping latency** using `WiFiClient.connect("google.com", 80)`
- 🌐 ESP32 runs a **local web server** that serves a real-time **HTML dashboard**
- 🌍 **Browser connects to ESP32's IP address** (automatically shown in Serial Monitor)
- 🔁 **Data auto-refreshes every 5 seconds**, allowing continuous monitoring

---
## 🖥 Code

#include <WiFi.h>
#include <WiFiClient.h>
#include <WebServer.h>

// Your existing WiFi credentials
const char* ssid = "Nothing";
const char* password = "supersandy";  // replace if needed

WebServer server(80);

void setup() {
  Serial.begin(115200);
  Serial.println("🔌 Connecting to WiFi...");

  WiFi.begin(ssid, password);

  // Wait until connected
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  // Once connected
  Serial.println("\n✅ Connected to WiFi!");
  Serial.print("🌐 ESP32 IP Address: http://");
  Serial.println(WiFi.localIP());  // THIS is the IP you open in browser

  server.on("/", handleRoot);
  server.begin();
  Serial.println("✅ Web Server started");
}

void loop() {
  server.handleClient();
}

void handleRoot() {
  long rssi = WiFi.RSSI();
  int latency = measureLatency("google.com");

  String html = "<!DOCTYPE html><html><head><meta http-equiv='refresh' content='5'>";
  html += "<title>ESP32 QoS Monitor</title></head><body>";
  html += "<h2>📶 ESP32 QoS Sensor</h2>";
  html += "<p><b>Signal Strength (RSSI):</b> " + String(rssi) + " dBm</p>";
  html += "<p><b>Ping Latency:</b> " + String(latency) + " ms</p>";
  html += "<p><i>Auto-refreshes every 5 seconds</i></p>";
  html += "</body></html>";

  server.send(200, "text/html", html);
}

int measureLatency(const char* host) {
  WiFiClient client;
  unsigned long start = millis();
  if (client.connect(host, 80)) {
    unsigned long end = millis();
    client.stop();
    return end - start;
  } else {
    return -1;
  }
}



---

## 🧪 Future Improvements

- 📤 **Push live data to MQTT brokers or cloud dashboards** for centralized monitoring
- 📉 Add **real-time graphing** using tools like **Plotly.js** or **Python Dash**
- 🗂 Store data locally using **microSD card** or **SPIFFS**
- 🌐 Expand for **mobile-tethered networks** to support 4G/5G drive testing
- 📱 Develop a **responsive mobile dashboard** for smartphones
- 🛰 Integrate **GPS module** for location-tagged QoS measurements

---

## 👨‍💻 Author

**Sandeep Kumar**  
🎓 Final Year B.Tech – Electronics and Communication Engineering  
🛠️ Specializing in IoT, VLSI, and FPGA-based systems  
🔗 [https://www.linkedin.com/in/sandeepkumar2612/](https://www.linkedin.com/in/sandeepkumar2612/)

---

## 🪪 License

This project is licensed under the **MIT License**. See the [LICENSE](./LICENSE) file for full details.

---
> ⚠️ **Disclaimer**: This is a simulated prototype created for academic and demonstration purposes. It does not replace certified telecom drive test or QoS tools used by official auditing teams.


