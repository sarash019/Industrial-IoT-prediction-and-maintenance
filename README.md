# 🔧 IoT Predictive Maintenance System

> Real-time sensor monitoring, ML-powered failure prediction, and proactive device alerting — before breakdowns occur.

![Python](https://img.shields.io/badge/Python-3.9+-3776AB?style=flat-square&logo=python&logoColor=white)
![Apache Kafka](https://img.shields.io/badge/Apache%20Kafka-Streaming-231F20?style=flat-square&logo=apachekafka&logoColor=white)
![Apache Spark](https://img.shields.io/badge/Apache%20Spark-Real--time-E25A1C?style=flat-square&logo=apachespark&logoColor=white)
![AWS IoT](https://img.shields.io/badge/AWS%20IoT%20Core-Connected-FF9900?style=flat-square&logo=amazonaws&logoColor=white)
![MLflow](https://img.shields.io/badge/MLflow-Experiment%20Tracking-0194E2?style=flat-square&logo=mlflow&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-22c55e?style=flat-square)

---

## Overview

The **IoT Predictive Maintenance System** is an end-to-end pipeline that monitors sensor data from connected industrial devices, applies machine learning to detect anomalies, and alerts operators before a failure occurs — minimizing downtime and maintenance costs.

```
Sensor Devices → Kafka Stream → Spark Processing → ML Prediction → Alert / Dashboard
                                      ↑
                               AWS IoT Core
```

---

## ✨ Key Features

- **Real-time streaming** of simulated sensor data (temperature, vibration, pressure) via Apache Kafka
- **Stream processing** with Apache Spark for low-latency analytics
- **Cloud device management** through AWS IoT Core with secure MQTT connectivity
- **ML-powered predictions** with full experiment tracking and model versioning via MLflow
- **Automated alerting** before device failures occur
- **Reproducible pipelines** driven by YAML configuration

---

## 🏗️ Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌──────────────────┐
│  Sensor Devices │────▶│  Kafka Producer  │────▶│   Kafka Topic    │
│  (Simulated)    │     │ kafka_producer.py│     │  (sensor-data)   │
└─────────────────┘     └─────────────────┘     └────────┬─────────┘
                                                          │
                         ┌────────────────────────────────▼─────────┐
                         │           Apache Spark (Real-time)        │
                         │         preprocess_data.py                │
                         └────────────────────────────────┬──────────┘
                                                          │
                    ┌─────────────────┐      ┌────────────▼──────────┐
                    │  AWS IoT Core   │      │    ML Prediction       │
                    │ aws_iot_config  │      │  mlflow_experiment.py  │
                    │     .json       │      │  model_config.yaml     │
                    └─────────────────┘      └────────────┬──────────┘
                                                          │
                                             ┌────────────▼──────────┐
                                             │   Alerts & MLflow UI   │
                                             │  Model Registry        │
                                             └───────────────────────┘
```

---

## 📁 Project Structure

```
iot-predictive-maintenance/
├── kafka_producer.py        # Simulates and streams sensor data to Kafka
├── preprocess_data.py       # Cleans, scales, and engineers features
├── mlflow_experiment.py     # Model training, logging, and registry
├── aws_iot_config.json      # AWS IoT Core device & connection config
├── model_config.yaml        # Hyperparameters and model configuration
└── README.md
```

---

## 🧩 Components

### 1. Kafka Streaming — `kafka_producer.py`

Generates and publishes simulated sensor telemetry to a Kafka topic at configurable intervals.

**Sensor signals produced:**

| Signal      | Unit  | Description                        |
|-------------|-------|------------------------------------|
| Temperature | °C    | Operating temperature of the device |
| Vibration   | m/s²  | Mechanical vibration amplitude      |
| Pressure    | bar   | Internal system pressure            |

```python
# Example: start the Kafka producer
python kafka_producer.py --topic sensor-data --interval 1.0
```

---

### 2. Data Preprocessing — `preprocess_data.py`

Prepares raw sensor streams for model consumption via a three-stage pipeline:

- **Missing value handling** — drops or imputes incomplete records
- **Scaling** — standardizes features for consistent model performance
- **Feature engineering** — derives computed features (rolling averages, delta values, etc.)

```python
# Example: run preprocessing on a raw data batch
python preprocess_data.py --input raw_data.csv --output processed_data.csv
```

---

### 3. ML Experiment Tracking — `mlflow_experiment.py`

Trains predictive failure models and logs everything to MLflow for full reproducibility.

- Tracks parameters, metrics, and artifacts per experiment run
- Registers trained models in the MLflow Model Registry
- Reads hyperparameters from `model_config.yaml`

```python
# Example: launch an experiment run
python mlflow_experiment.py --config model_config.yaml
```

```yaml
# model_config.yaml (example)
model:
  type: RandomForestClassifier
  n_estimators: 100
  max_depth: 10
  random_state: 42
training:
  test_size: 0.2
  cv_folds: 5
```

---

### 4. AWS IoT Core — `aws_iot_config.json`

Configures secure cloud connectivity for device registration, policy management, and MQTT message routing.

```json
{
  "endpoint": "your-endpoint.iot.region.amazonaws.com",
  "clientId": "iot-device-001",
  "topic": "sensor/data",
  "certPath": "./certs/device.cert.pem",
  "keyPath": "./certs/device.private.key",
  "caPath": "./certs/AmazonRootCA1.pem"
}
```

> **Note:** Never commit certificate files or keys to version control. Use environment variables or AWS Secrets Manager.

---

## 🚀 Getting Started

### Prerequisites

- Python 3.9+
- Apache Kafka 3.x
- Apache Spark 3.x
- AWS account with IoT Core access
- MLflow (local or remote tracking server)

### Installation

```bash
# Clone the repository
git clone https://github.com/your-org/iot-predictive-maintenance.git
cd iot-predictive-maintenance

# Install dependencies
pip install -r requirements.txt
```

### Running the Pipeline

```bash
# 1. Start Kafka (if running locally)
bin/zookeeper-server-start.sh config/zookeeper.properties &
bin/kafka-server-start.sh config/server.properties &

# 2. Start the MLflow tracking server
mlflow server --host 0.0.0.0 --port 5000

# 3. Stream sensor data
python kafka_producer.py

# 4. Run preprocessing + Spark job
spark-submit preprocess_data.py

# 5. Train and log the ML model
python mlflow_experiment.py --config model_config.yaml
```

---

## 📊 MLflow Experiment UI

Once running, open the MLflow UI to inspect runs, compare metrics, and promote models to production:

```
http://localhost:5000
```

Each run logs:
- Model parameters (from `model_config.yaml`)
- Training and validation metrics (accuracy, F1, AUC-ROC)
- Confusion matrix and feature importance artifacts
- Registered model version with stage (`Staging` / `Production`)

---

## ⚙️ Configuration

| File                  | Purpose                                      |
|-----------------------|----------------------------------------------|
| `aws_iot_config.json` | AWS IoT Core endpoint, certs, and topic      |
| `model_config.yaml`   | ML hyperparameters and training settings     |

---

## 🔒 Security Notes

- All AWS IoT communication is secured via mutual TLS (X.509 certificates)
- Store certificates and private keys outside of version control (use `.gitignore`)
- Use IAM roles with least-privilege policies for AWS IoT Core access
- Rotate device certificates regularly

---

## 📈 Roadmap

- [ ] Add support for real hardware sensors (MQTT over TLS)
- [ ] Dashboard UI for live anomaly visualization
- [ ] Multi-device fleet management
- [ ] Automated model retraining on drift detection
- [ ] Docker Compose setup for local development

---

## 🤝 Contributing

Contributions are welcome! Please open an issue to discuss proposed changes before submitting a pull request.

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes (`git commit -m 'Add your feature'`)
4. Push to the branch (`git push origin feature/your-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

<p align="center">Built with Apache Kafka · Apache Spark · AWS IoT Core · MLflow</p>
