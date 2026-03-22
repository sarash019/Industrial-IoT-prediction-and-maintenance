This IoT Predictive Maintenance System is designed to monitor sensor data from connected devices, predict potential failures, and alert users before a breakdown occurs. The pipeline:

Simulates sensor data and streams it to Kafka.
Processes data in real-time with Spark.
Connects to AWS IoT Core for device management.
Uses machine learning models for predictive analytics, tracking experiments and versions with MLflow.
Key Components
AWS IoT Integration
The AWS IoT Core configuration (aws_iot_config.json) enables cloud connectivity for managing IoT devices and sending/receiving data securely. This part supports real-time data management and monitoring through AWS's secure infrastructure.

Kafka Streaming The kafka_producer.py script generates and streams simulated sensor data (e.g., temperature, vibration, pressure) to a Kafka topic. Kafka serves as the data backbone for processing and analytics, enabling scalable and real-time data handling.

Data Preprocessing
The preprocess_data.py script cleans and prepares raw data, making it suitable for model training. Preprocessing steps include:

Missing value handling: Removes or imputes missing data. Scaling: Standardizes data for consistent model performance. Feature engineering: Adds or transforms features based on analysis requirements.

Machine Learning with MLflow
The mlflow_experiment.py script handles model training, tracking, and version control with MLflow. Key features include:

Experiment logging: Tracks model parameters, metrics, and training details. Model registry: Saves models in MLflow for easy retrieval and deployment. Hyperparameter management: Uses configurations from model_config.yaml for model consistency and reproducibility.
