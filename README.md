# 🧩 Messaging-Based Big Data Pipeline (Kafka–PySpark–Hive)

### Technologies
**Apache Kafka, PySpark, Apache NiFi, HDFS, Hive, Airflow, Docker**

---

## 📘 Overview
This project demonstrates an **end-to-end distributed messaging-based data pipeline** for real-time data ingestion, processing, storage, and analytics.  
It was implemented as part of the *Big Data Systems (Spring 2025)* course at **New York University**.

The pipeline follows a **Kappa-style architecture**, using:
- **NiFi** for ingestion and enrichment  
- **Kafka** for distributed messaging  
- **PySpark Structured Streaming** for transformation and aggregation  
- **HDFS & Hive** for persistent, queryable storage  
- **Airflow** for orchestration and automation  

A real-world use case—**COVID-19 analytics**—was used to validate the system’s scalability, reliability, and low-latency performance.

---

## ⚙️ Architecture
**End-to-End Data Flow:**
1. **NiFi** ingests CSV data (e.g., country-wise COVID metrics) from HTTP sources.  
2. **Kafka** stores validated messages in raw and processed topics.  
3. **PySpark Structured Streaming** consumes from Kafka, cleans and aggregates data hourly, then outputs to both:
   - Kafka (`dezyre_out`) for real-time dashboards  
   - HDFS (Parquet format) for long-term storage  
4. **Hive External Tables** provide SQL access to HDFS data for analytics.  
5. **Airflow** DAGs manage partition refresh, job deployment, and monitoring.  

<p align="center">
  <img src="https://raw.githubusercontent.com/SamKh5/Messaging-Based-Big-Data-Pipeline/main/assets/architecture.jpeg" alt="Pipeline Architecture" width="600"/>
</p>

---

## 📂 Project Structure
```
RootDirectory/
│
├── docker-compose.yml           # Multi-container setup (Kafka, Hive, NiFi, Airflow, Spark)
├── hadoop.env / hadoop-hive.env # Environment configs
├── airflow_init.sh              # Airflow initialization script
├── hive_connection.py           # Hive connector logic
├── hive_script.py               # Hive DDL + query automation
├── test.py                      # Utility script for testing data flow
├── Nifi1..4.png                 # NiFi workflow snapshots
├── processed_data.hql           # Hive queries on processed data
├── corana_data.hql              # Example input HQL file
├── docker_containers.png        # Live container view (Airflow, Kafka, NiFi, Hive)
└── BIG_DATA_REPORT.pdf          # Full academic project report
```

---

## 🚀 Features
- **End-to-end streaming pipeline** (NiFi → Kafka → PySpark → HDFS → Hive)
- **Real-time aggregation** of incoming data (hourly country-level stats)
- **Exactly-once processing** via Spark checkpoints and Kafka offset tracking
- **Fault tolerance** with 3x Kafka replication & HDFS redundancy
- **Containerized deployment** using Docker Compose
- **Automated orchestration** with Airflow DAGs for partition refresh and job scheduling
- **Scalable and extensible** architecture—tested up to 12,000 events/sec and 400M daily events

---

## 🧪 Example Use Case — COVID-19 Analytics
A streaming dataset of confirmed cases and deaths by country is processed as:
1. **Input:** Raw CSV via NiFi HTTP listener  
2. **Kafka Topic:** `dezyre_data_csv`  
3. **Transformation:** PySpark aggregates hourly totals and outputs structured data  
4. **Output:**  
   - Kafka topic `dezyre_out` for real-time visualization  
   - `/data/processed/ingest_date=YYYY-MM-DD/` on HDFS for Hive queries  
5. **Hive Query Example:**
   ```sql
   SELECT country_code_final, SUM(hourly_confirmed) AS daily_total_cases
   FROM covid_processed
   WHERE ingest_date = '2025-05-13'
   GROUP BY country_code_final;
   ```

---

## 📊 Performance Summary
| Metric | Value |
|:--|:--|
| Sustained Throughput | 5,000 events/sec |
| Peak Throughput | 12,000 events/sec |
| End-to-End Latency | ~65 seconds |
| Data Volume | ~400M events/day (≈43GB raw) |
| Reliability | 99.9% uptime, zero data loss |

---

## 🛠️ Setup Instructions
### Prerequisites
- Docker & Docker Compose  
- Java 8+  
- Python 3.9+  
- Hadoop and Spark environment variables configured

### Run the Stack
```bash
cd docker_exp
docker-compose up -d
```

### Verify Running Containers
```bash
docker ps
```

Expected containers: **nifi**, **kafka**, **spark**, **hive**, **airflow**, **namenode**, **datanode**

---

## 🧩 Team Members
- **Sampurna Khuntia** – PySpark Stream Processing, HDFS/Hive Integration  
- **Dhruv Gupta** – Kafka Setup & Airflow Orchestration  
- **Harshit Ojha** – NiFi Dataflow and Ingestion Logic  
- **Dhairyasheel Patil** – Docker Deployment and Monitoring  

---

## 📚 References
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)
- [PySpark Structured Streaming Guide](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)
- [Apache NiFi Docs](https://nifi.apache.org/docs.html)
- [Apache Hive Manual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)
- [Apache Airflow Docs](https://airflow.apache.org/docs/)
- [Apache Parquet Format](https://parquet.apache.org/documentation/latest/)
