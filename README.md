Readme · MDCopy<div align="center">
Motor Anomaly Detection
Real-Time Edge AI + IoT Monitoring System
Show Image
Show Image
Show Image
Show Image
Show Image
Show Image
<br/>

Deploying production-grade AI on a Rs.500 microcontroller.
Real-time visual anomaly detection at the edge — no cloud, no compromise.
DEMO: https://youtube.com/shorts/fE8pEs_nPZ4

<br/>
Show Image
Show Image
Show Image
</div>

Problem Statement
Industrial motors and rotating equipment are critical assets in manufacturing plants, power stations, and processing facilities. Undetected anomalies in these machines — such as surface defects, misalignment, foreign object intrusion, or abnormal wear — lead to unplanned downtime, costly repairs, and in severe cases, serious safety hazards.
Current anomaly detection solutions rely on either manual visual inspection (error-prone and labor-intensive) or expensive cloud-connected vision systems (high latency, internet dependency, data privacy concerns). Neither approach is suitable for resource-constrained or remote industrial environments.
This project addresses the gap by deploying a quantized MobileNetV2 model directly on an ESP32-CAM microcontroller — a device with only 520KB of RAM and costing under Rs.500. The system performs real-time visual anomaly detection entirely at the edge, with zero cloud dependency, and publishes results over a secured MQTT channel to a live IoT monitoring dashboard. This approach achieves 94.8% anomaly recall at a fraction of the cost and complexity of traditional industrial vision systems.

Project Overview
This project visually monitors a motor in real time, detects anomalies using an on-device AI model, and publishes results to a secured MQTT broker which feeds a live dashboard.
The entire inference pipeline runs on the ESP32-CAM — no cloud, no internet required for inference.

System Architecture
ESP32-CAM (TinyML Inference)
        | MQTT publish (secured)
Mosquitto Broker (Linux)
        |
Node-RED Dashboard (Live IoT Monitoring)

Model Performance
<div align="center">
MetricValueOverall Accuracy87.8%Anomaly Recall94.8%Normal Recall78.4%F1 Score (Anomaly)0.90Model ArchitectureMobileNetV2 96x96 (int8)Inference Time~643ms/frameRAM Usage~280KB / 520KBModel Size< 1MB
</div>

Why anomaly recall matters more than accuracy:
In industrial safety, missing a real defect is far more dangerous than a false alarm. Our model prioritizes catching every anomaly — 94.8% recall means almost nothing slips through.


Tech Stack
<table>
<tr>
<td valign="top" width="33%">
Hardware

ESP32-CAM (AI Thinker)
OV2640 Camera Module
CP2102 USB-TTL Programmer
DC Motor (5V)

</td>
<td valign="top" width="33%">
Software

Arduino IDE 2.x
Edge Impulse Studio
TensorFlow Lite Micro
PubSubClient (MQTT)
ArduinoJson

</td>
<td valign="top" width="33%">
Backend

Mosquitto MQTT Broker
Node-RED Dashboard
Ubuntu 24 (WSL2)
bcrypt Authentication

</td>
</tr>
</table>

Security Architecture
+------------------------------------------+
|             Security Layers              |
+------------------------------------------+
|  MQTT Username + Password Auth           |
|  Anonymous Connections Blocked           |
|  Broker Bound to Specific Interface      |
|  Node-RED bcrypt Password Auth           |
|  Credentials in Separate Config Files    |
+------------------------------------------+
|  Future: mTLS Certificate Auth           |
+------------------------------------------+

Setup Instructions
1. Clone
bashgit clone https://github.com/SohamVashistha/Motor-Anomaly-Detection-TinyML
cd Motor-Anomaly-Detection-TinyML
2. Setup MQTT Broker
bashsudo apt install mosquitto mosquitto-clients -y
sudo mosquitto_passwd -c /etc/mosquitto/passwd mqttuser
sudo nano /etc/mosquitto/conf.d/security.conf
listener 1883 0.0.0.0
allow_anonymous false
password_file /etc/mosquitto/passwd
bashsudo systemctl restart mosquitto
3. Setup Node-RED
bashsudo npm install -g --unsafe-perm node-red
node-red &
# Open http://localhost:1880
# Import dashboard/node-red-flow.json
4. Flash ESP32-CAM
1. Open firmware/esp32cam_firmware.ino in Arduino IDE
2. Update WiFi credentials and MQTT server IP
3. Connect IO0 to GND (flash mode)
4. Board: AI Thinker ESP32-CAM | Port: COMX
5. Upload, remove IO0 wire, press Reset
6. Open Serial Monitor at 115200 baud

Future Scope
FeatureDescriptionPriorityRelay Motor ControlConnect relay module to ESP32 GPIO for remote start/stop from dashboard. On high-confidence anomaly detection, system auto-cuts motor power — converting from pure monitoring to closed-loop controlHighmTLS EncryptionCertificate-based mutual authentication on MQTT channelMediumGrafana + InfluxDBTime-series anomaly logging and historical trend analysisMediumOTA Firmware UpdatesPush model updates wirelessly without physical accessMediumMulti-Camera SupportMonitor multiple motors from a single dashboardLowEdge TPU DeploymentPort to Google Coral for faster inference at higher FPSLow

Industry Applications
Manufacturing    ->  PCB defect detection on assembly lines
Power Sector     ->  Motor and transformer health monitoring
Automotive       ->  Weld quality inspection on chassis lines
Pharma           ->  Pill packaging defect detection
Mining           ->  Conveyor belt spillage monitoring

Key Technical Decisions
Why MobileNetV2 over custom CNN?
Pre-trained on ImageNet, fine-tuned for our domain. Transfer learning gives stronger feature extraction with far less training data than building from scratch.
Why int8 quantization?
4x smaller model, faster inference, negligible accuracy drop. Essential for fitting inside ESP32's 520KB RAM without floating point hardware.
Why MQTT over HTTP?
Persistent connections, minimal overhead, pub/sub pattern perfectly matches sensor-to-dashboard IoT flow. Industry standard for embedded IoT communication.
Why auto class weighting?
Dataset had 3:1 anomaly-to-normal imbalance. Without weighting, normal recall was 34%. With auto-weighting it jumped to 78.4%. Single toggle, massive impact.

Repository Structure
Motor-Anomaly-Detection-TinyML/
|
|-- firmware/
|   |-- esp32cam_firmware.ino     <- ESP32 inference + MQTT publish
|
|-- dashboard/
|   |-- node-red-flow.json        <- Importable Node-RED flow
|
|-- mosquitto/
|   |-- security.conf             <- Broker auth configuration
|
|-- model/
|   |-- README.md                 <- Edge Impulse project + metrics
|
|-- README.md

References

Edge Impulse Documentation
Casting Product Dataset - Kaggle
TensorFlow Lite Micro
Mosquitto MQTT Broker
Node-RED


<div align="center">
Built by Soham Vashistha
