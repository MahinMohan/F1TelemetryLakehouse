Distributed F1 Telemetry Lakehouse

This repository contains a distributed, real-time telemetry lakehouse architecture for Formula 1 data analytics.
The system integrates a high-performance C++ ingestion engine, Kafka for distributed messaging, Flink for real-time stream processing, AWS S3 as a data lake, Trino for interactive SQL analytics, and Streamlit for real-time visualization.

The project demonstrates skills in distributed systems, low-latency ingestion, stream processing, cloud data engineering, and containerized deployment.

1. Project Overview

The goal of this project is to build a real-time, end-to-end telemetry pipeline capable of:

Ingesting raw live or simulated F1 telemetry using a C++ engine.

Streaming telemetry events through Kafka.

Processing and enriching data in real time using Apache Flink.

Persisting both raw and processed data into an AWS S3-based lakehouse.

Querying data through Trino using ANSI SQL.

Monitoring and visualizing car telemetry and anomalies through a Streamlit dashboard.

This architecture models real-world production systems used for motorsport analytics, IoT telemetry, and distributed monitoring pipelines.

2. System Architecture

The system is composed of the following components:

C++ ingestion engine

Fetches FastF1 or simulated live telemetry.

Serializes data into JSON.

Produces messages to Kafka topics.

Kafka message broker

Serves as the central event backbone.

Provides ordered, durable, fault-tolerant streams.

Topics used:

f1.telemetry.raw

f1.telemetry.clean

f1.telemetry.anomalies

Flink stream processor

Cleans, normalizes, and enriches telemetry.

Computes sliding-window metrics (speed, RPM, throttle, brake).

Detects anomalies such as RPM drops, overheating, or gear inconsistencies.

Sends results to S3 and Kafka.

AWS S3 Lakehouse

Stores raw, clean, and anomaly-level telemetry.

Acts as a scalable, low-cost long-term storage layer.

Trino SQL Engine

Executes analytical and ad-hoc SQL queries on S3-resident data.

Allows interactive exploration and downstream reporting.

Streamlit dashboard

Displays near real-time telemetry.

Shows driver performance, car component health, and detected anomalies.

Supports analytical views for race engineers and analysts.

3. Repository Structure
f1_full/
│── README.md
│── ingestion/
│   └── cpp/
│       └── main.cpp                # C++ ingestion engine source
│── streaming/
│   └── flink_job.py                # Flink streaming job
│── dashboard/
│   └── streamlit_app.py            # Streamlit real-time dashboard
│── lakehouse/
│   └── trino/
│       └── schema.sql              # Trino table definitions
└── infra/
    └── docker/
        └── Dockerfile              # Container for dashboard deployment

4. Component Details
4.1 C++ Ingestion Engine

The ingestion engine is responsible for collecting live or simulated F1 telemetry data and sending it to Kafka. It is built for performance and low latency using:

C++20

nlohmann/json for serialization

librdkafka or cppkafka for Kafka producing

libcurl/cpr for API requests (if consuming live FastF1-like endpoints)

4.2 Kafka Messaging

Kafka is the backbone of the telemetry system, ensuring decoupled and fault-tolerant communication between ingestion and processing layers.

Suggested topics:

f1.telemetry.raw
Unprocessed raw telemetry.

f1.telemetry.clean
Cleaned and normalized frames.

f1.telemetry.anomalies
Anomaly-level telemetry events.

4.3 Apache Flink Stream Processing

The Flink job processes raw telemetry from Kafka and performs:

JSON parsing

Type normalization

Rolling window calculations

Calculation of driver performance metrics

Anomaly detection based on thresholds

Output writing to S3 and back to Kafka

The pipeline is designed for sub-second latency and continuous processing.

4.4 AWS S3 Lakehouse

The data stored in the lakehouse is structured as:

s3://f1-lake/raw/
s3://f1-lake/clean/
s3://f1-lake/anomalies/


Data is stored in JSON, CSV, or Parquet formats depending on the processing step.
This storage layer allows both low-latency query workloads and long-term historical analysis.

4.5 Trino Query Engine

Trino provides interactive SQL querying over the S3 datasets.
Typical queries include:

Driver performance over time

Anomaly rate per lap

Speed distribution by sector

Engine and component degradation patterns

4.6 Streamlit Dashboard

The dashboard provides:

Live telemetry graphs

Real-time anomaly feed

Session-level summaries

Driver comparison views

Health metrics for engine, gearbox, ERS

5. Running Locally
5.1 Running the Streamlit Dashboard

Install dependencies:

pip install streamlit


Start the UI:

streamlit run dashboard/streamlit_app.py

5.2 Running the Ingestion Engine

Navigate to the ingestion folder:

cd ingestion/cpp
g++ main.cpp -o ingestion -std=c++20
./ingestion

5.3 Running the Flink Job

Execute the job:

python3 streaming/flink_job.py

6. Docker Deployment
6.1 Build the dashboard container
docker build -t f1-dashboard infra/docker

6.2 Run the container
docker run -p 8501:8501 f1-dashboard

7. Cloud Deployment (Option A Architecture)

A full-scale deployment consists of:

Kafka cluster (Amazon MSK or Strimzi on Kubernetes)

Flink job cluster running in Kubernetes

Trino cluster for SQL analytics

S3 buckets for lakehouse storage

Streamlit service deployed via EC2, EKS, or Fargate

Optional: Infrastructure as Code using Terraform or CDK

This architecture supports horizontal scaling, high throughput, and real-time analytics.
