System Architecture
The system is composed of the following components:
C++ Ingestion Engine
Ingests live or simulated telemetry data
Serializes events into JSON payloads
Publishes messages to Kafka topics
Designed for low latency and high throughput
Kafka Message Broker
Serves as the central event backbone
Provides durable, ordered, and fault-tolerant streams
Decouples ingestion, processing, and storage layers
Topics
telemetry.raw
telemetry.clean
telemetry.anomalies

Apache Flink Stream Processor
Consumes telemetry from Kafka
Performs parsing, normalization, and enrichment
Computes sliding-window metrics
Detects anomalous patterns based on configurable thresholds
Writes outputs to both Kafka and S3


AWS S3 Lakehouse
Stores raw, cleaned, and derived telemetry datasets
Acts as scalable, low-cost long-term storage
Supports downstream analytical workloads
Trino SQL Engine
Executes interactive SQL queries directly over S3-resident data
Enables ad-hoc analysis and exploratory analytics
Supports ANSI SQL for integration with BI and reporting tools
Streamlit Dashboard
Visualizes telemetry metrics and system health
Displays near real-time data summaries
Supports validation and inspection of streaming pipelines

AWS S3 Lakehouse
Stores raw, cleaned, and derived telemetry datasets
Acts as scalable, low-cost long-term storage
Supports downstream analytical workloads
Trino SQL Engine
Executes interactive SQL queries directly over S3-resident data
Enables ad-hoc analysis and exploratory analytics
Supports ANSI SQL for integration with BI and reporting tools

Streamlit Dashboard
Visualizes telemetry metrics and system health
Displays near real-time data summaries
Supports validation and inspection of streaming pipelines


telemetry_lakehouse/
│── README.md
│── ingestion/
│   └── cpp/
│       └── main.cpp                # C++ ingestion engine
│── streaming/
│   └── flink_job.py                # Flink stream processing job
│── dashboard/
│   └── streamlit_app.py            # Streamlit dashboard
│── lakehouse/
│   └── trino/
│       └── schema.sql              # Trino table definitions
└── infra/
    └── docker/
        └── Dockerfile              # Dashboard container


4. Component Details
4.1 C++ Ingestion Engine

The ingestion service is implemented in C++20 and optimized for low-latency data ingestion.
Key technologies:

nlohmann/json for serialization

librdkafka / cppkafka for Kafka publishing

libcurl / cpr for external data sources (if applicable)

4. Component Details
4.1 C++ Ingestion Engine

The ingestion service is implemented in C++20 and optimized for low-latency data ingestion.
Key technologies:

nlohmann/json for serialization

librdkafka / cppkafka for Kafka publishing

libcurl / cpr for external data sources (if applicable)

4.2 Kafka Streaming Layer

Kafka provides reliable event delivery between ingestion and processing layers, enabling horizontal scalability and fault isolation.

4.3 Apache Flink Processing Pipeline

The Flink job performs:

JSON parsing and schema normalization

Rolling window aggregations

Metric computation

Anomaly detection

Output routing to Kafka and S3

The pipeline is designed to operate with sub-second pipeline latency under sustained data rates.

4.4 Lakehouse Storage (AWS S3)

Data is organized as:

s3://telemetry-lake/raw/
s3://telemetry-lake/clean/
s3://telemetry-lake/anomalies/


Data formats include JSON, CSV, or Parquet depending on the processing stage.

4.5 Trino Query Engine

Trino enables interactive analytics such as:

Metric trends over time

Anomaly frequency analysis

Distribution and aggregation queries

Historical performance analysis

4.6 Streamlit Dashboard

The dashboard provides:

Telemetry metric visualizations

System health indicators

Streaming pipeline validation views

5. Running Locally
5.1 Streamlit Dashboard
pip install streamlit
streamlit run dashboard/streamlit_app.py

5.2 C++ Ingestion Engine
cd ingestion/cpp
g++ main.cpp -o ingestion -std=c++20
./ingestion

5.3 Flink Job
python3 streaming/flink_job.py

6. Docker Deployment
Build the dashboard image
docker build -t telemetry-dashboard infra/docker

Run the container
docker run -p 8501:8501 telemetry-dashboard

7. Scalable Cloud Deployment

A production-scale deployment may include:

Kafka cluster (Amazon MSK or Kubernetes-based)

Apache Flink cluster running on Kubernetes

Trino cluster for distributed SQL analytics

AWS S3 for lakehouse storage

Streamlit deployed via EC2, EKS, or Fargate

Optional extensions include Infrastructure as Code using Terraform or AWS CDK.

