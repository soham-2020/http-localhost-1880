Real-Time Industrial Motor Anomaly Detection System with Edge AI and IoT Dashboard
A real-time industrial motor anomaly detection system built on the ESP32-CAM using TinyML (MobileNetV2 int8 quantized), secured MQTT, and a live Node-RED IoT dashboard — all running at the edge with zero cloud dependency.

🔍 Problem Statement
Industrial motors and rotating equipment are critical assets in manufacturing plants, power stations, and processing facilities. Undetected anomalies in these machines — such as surface defects, misalignment, foreign object intrusion, or abnormal wear — lead to unplanned downtime, costly repairs, and in severe cases, serious safety hazards.
Current anomaly detection solutions rely on either manual visual inspection (error-prone and labor-intensive) or expensive cloud-connected vision systems (high latency, internet dependency, data privacy concerns). Neither approach is suitable for resource-constrained or remote industrial environments.
This project addresses the gap by deploying a quantized MobileNetV2 model directly on an ESP32-CAM microcontroller — a device with only 520KB of RAM and costing under ₹500. The system performs real-time visual anomaly detection entirely at the edge, with zero cloud dependency, and publishes results over a secured MQTT channel to a live IoT monitoring dashboard. This approach achieves 94.8% anomaly recall at a fraction of the cost and complexity of traditional industrial vision systems.

🔥 Project Overview
This project visually monitors a motor in real time, detects anomalies using an on-device AI model, and publishes results to a secured MQTT broker which feeds a live dashboard.
The entire inference pipeline runs on the ESP32-CAM — no cloud, no internet required for inference.

🏗️ System Architecture
ESP32-CAM (TinyML Inference)
        ↓ MQTT publish (secured)
Mosquitto Broker (Linux)
        ↓
Node-RED Dashboard (Live IoT Monitoring)

📊 Model Performance
MetricValueOverall Accuracy87.8%Anomaly Detection Recall94.8%Normal Detection Recall78.4%F1 Score (Anomaly)0.90Model TypeMobileNetV2 96x96 (int8 quantized)Inference Time~643ms per frameRAM Usage~280KBModel Size<1MB

🛠️ Tech Stack
Hardware

ESP32-CAM (AI Thinker) with OV2640 camera
CP2102 USB-to-TTL programmer
DC Motor (5V)

Firmware

Arduino IDE 2.x
Edge Impulse TensorFlow Lite Micro
PubSubClient (MQTT)
ArduinoJson

Backend

Mosquitto MQTT Broker
Node-RED Dashboard
Ubuntu (WSL2)

ML Pipeline

Edge Impulse (training + deployment)
MobileNetV2 Transfer Learning
int8 Quantization for edge deployment
Dataset: Casting Product Quality Inspection (Kaggle)


🔒 Security Features

MQTT broker authentication (username + password)
Anonymous connections disabled
Mosquitto listening on secured interface only
Node-RED editor password protected (bcrypt hashed)
Credentials stored in separate config files


📁 Project Structure
Motor-Anomaly-Detection-TinyML/
├── firmware/
│   └── esp32cam_firmware.ino      # Main ESP32-CAM code
├── dashboard/
│   └── node-red-flow.json         # Node-RED flow export
├── mosquitto/
│   └── security.conf              # Mosquitto security config
├── model/
│   └── (Edge Impulse project link)
└── README.md

🚀 Setup Instructions
1. Clone the repo
bashgit clone https://github.com/YOUR_USERNAME/Motor-Anomaly-Detection-TinyML
2. Setup Mosquitto
bashsudo apt install mosquitto mosquitto-clients -y
sudo mosquitto_passwd -c /etc/mosquitto/passwd mqttuser
sudo nano /etc/mosquitto/conf.d/security.conf
# Add: listener 1883 0.0.0.0
# Add: allow_anonymous false
# Add: password_file /etc/mosquitto/passwd
sudo systemctl restart mosquitto
3. Setup Node-RED
bashsudo npm install -g --unsafe-perm node-red
npm install -g node-red-dashboard
node-red
# Import dashboard/node-red-flow.json
4. Flash ESP32-CAM

Open firmware/esp32cam_firmware.ino in Arduino IDE
Update WiFi credentials and MQTT server IP
Select Board: AI Thinker ESP32-CAM
Connect IO0 to GND for flash mode
Upload and reset


📈 Why This Project?
Industrial anomaly detection is a multi-million dollar problem. Traditional vision systems require powerful servers and cloud connectivity. This project demonstrates that:

TinyML can run production-grade models on $5 hardware
Edge inference eliminates latency and cloud costs
MQTT is industry standard for IoT monitoring
Security is not optional even in embedded systems


🎯 Industry Applications

PCB defect detection on assembly lines
Motor health monitoring in industrial plants
Quality inspection in manufacturing
Predictive maintenance systems


🔮 Future Scope

Motor Control via Relay Module — A relay module connected to ESP32 GPIO can be integrated to enable remote start/stop control of the motor directly from the dashboard. When an anomaly is detected with high confidence, the system can automatically cut motor power as a safety measure — converting this from a pure monitoring system to a closed-loop control system
TLS Encryption on MQTT — Add certificate based mTLS for encrypted data transmission
Grafana Integration — Time series anomaly logging with InfluxDB + Grafana for historical analysis
OTA Updates — Over the air firmware updates for ESP32 without physical access
Multi-camera support — Monitor multiple motors simultaneously from a single dashboard
Edge TPU deployment — Port to Google Coral for faster inference at higher frame rates


📌 Key Learnings

int8 quantization reduces model size by 4x with minimal accuracy loss
Auto class weighting is critical for imbalanced industrial datasets
WSL2 port forwarding is required for ESP32 ↔ Linux MQTT communication
MobileNetV2 0.1 is the optimal architecture for ESP32's 520KB RAM constraint


🔗 References

Edge Impulse Documentation
Casting Product Dataset - Kaggle
Mosquitto MQTT Broker
Node-RED
