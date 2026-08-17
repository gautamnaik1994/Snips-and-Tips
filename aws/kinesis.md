# Kinesis

Amazon Kinesis Data Streams stores the data.<br>

* Amazon Kinesis Data Streams (KDS): Actively stores data records in shards. By default, it retains data for 24 hours, but you can extend retention up to 365 days (1 year). Multiple consumer applications can independently replay and re-read stored data within this retention window.<br>
* Amazon Data Firehose (formerly Kinesis Data Firehose): Does not store data. It is an ingestion and delivery conduit designed to transform, batch, and load streaming data directly into destinations like Amazon S3, Redshift, OpenSearch, or Splunk. Data buffers temporarily in memory/disk for up to 15 minutes only until it is flushed to its final destination.

#### Quick Comparison

| **Feature**      | **Kinesis Data Streams (KDS)**               | **Amazon Data Firehose**                             |
| ---------------- | -------------------------------------------- | ---------------------------------------------------- |
| Primary Purpose  | Real-time ingestion & temporary data storage | Automated ETL & direct delivery to storage/analytics |
| Data Retention   | 24 hours (default) to 365 days               | None (transient buffer: 60s–900s)                    |
| Data Replay      | Yes (consumers can read at any offset)       | No (data stream flows strictly one-way to target)    |
| Common ML Target | Custom model inference (SageMaker / Lambda)  | Storing raw or Parquet data in S3 for training       |

### AWS Flink vs AWS Firehose

The key distinction lies in compute vs. transport and complex analytics vs. basic ETL:

* AWS Managed Service for Apache Flink _(formerly Kinesis Data Analytics)_ is a stateful compute engine designed for complex, sub-second stream processing and real-time analytics.
* Amazon Data Firehose _(formerly Kinesis Data Firehose)_ is a managed transport/delivery service designed to ingest, perform simple transformations (like converting JSON to Parquet), and load data directly into storage sinks (like S3, Redshift, or OpenSearch).

#### Core Technical Comparison

| **Feature**       | **AWS Managed Service for Apache Flink**                              | **Amazon Data Firehose**                                                            |
| ----------------- | --------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| Primary Role      | Stateful, real-time stream processing & analytics                     | Ingestion, simple transformation, and sink loading (ETL)                            |
| Processing Power  | Complex event processing (sliding windows, joins, state tracking)     | Basic batch transformations (JSON to Parquet/ORC, Lambda enrichment)                |
| Latency           | Sub-second real-time streaming                                        | Micro-batch delivery (60 to 900 seconds buffer)                                     |
| Code / Logic      | Programmatic (Java, Scala, Python/PyFlink, or SQL)                    | Configuration-driven (No-code / low-code)                                           |
| State Management  | Keeps stateful memory (e.g., tracking a 1-hour moving average)        | Stateless (processes records in isolated micro-batches)                             |
| Data Destinations | Custom streams, S3, OpenSearch, databases, or downstream applications | Managed destinations only (Amazon S3, Redshift, OpenSearch, Splunk, HTTP endpoints) |

#### How They Work Together in ML Pipelines

In enterprise machine learning architectures, Flink and Firehose are often used sequentially in the same streaming pipeline:

```
[ Data Producers ] 
       │
       ▼
[ Kinesis Data Streams / MSK ] ──► [ AWS Flink ] (Real-time Model Inference / Feature Engineering)
       │
       ▼
[ Amazon Data Firehose ] ─────────► [ Amazon S3 ] (Data Lake for Batch Model Retraining)
```

1. AWS Flink reads from Kinesis Data Streams to evaluate feature drift, compute real-time sliding averages, or trigger immediate SageMaker inference alerts.
2. Amazon Data Firehose takes the raw or enriched stream, converts it into columnar Parquet format, and automatically buffers it into Amazon S3 for long-term storage and model training.

#### Why Flink Is Not a Feature Store

Not directly—AWS Flink is a stream processor, not a feature store. However, it plays a vital role as the streaming feature engineering engine that feeds real-time features _into_ an online feature store (such as Amazon SageMaker Feature Store or Amazon DynamoDB).

<a class="button secondary">GitHub</a>

For the AWS Machine Learning exam, it helps to understand why Flink isn't a feature store on its own, and how it fits into feature engineering pipelines:

####

| Feature Store Capabilities      | AWS Managed Service for Apache Flink                                                          |
| ------------------------------- | --------------------------------------------------------------------------------------------- |
| Centralized Catalog & Discovery | ❌ No built-in metadata, tagging, or discovery UI across teams.                                |
| Low-Latency Point Lookups       | ❌ Cannot be directly queried key-by-key by low-latency inference APIs (e.g., REST endpoints). |
| Online/Offline Synchronization  | ❌ Does not natively maintain historical time-travel data alongside real-time values.          |
| Stateful Stream Computation     | ✅ Excellent at aggregating rolling time-windows (e.g., "count of clicks in past 10 minutes"). |

While Flink maintains internal "application state" in memory/RocksDB to compute sliding window aggregations, that state is private to the Flink job and not exposed as a queryable database for external ML endpoints.

#### The Standard AWS Architecture for Real-Time Features

In streaming ML architectures, Flink acts as the feature processor, while SageMaker Feature Store acts as the storage and serving layer:

```
[ Ingestion ]          [ Feature Computation ]          [ Feature Storage & Serving ]
 
Raw Events              AWS Managed Service               SageMaker Feature Store
(Kinesis Data Streams)  for Apache Flink                   ├── Online Store (DynamoDB-backed)
       │                        │                              └── < 10ms lookup for inference
       ▼                        ▼                              
[ Real-Time Data ] ──► [ Sliding Aggregations ] ──────►   └── Offline Store (S3-backed)
                       (e.g., 5-min fraud score)               └── Batch training & time-travel
```

1. Kinesis Data Streams ingests live clickstream, fraud, or telemetry events.
2. AWS Flink runs continuous computations (e.g., rolling averages, click counts, velocity checks) over sliding time windows.
3. Flink writes the output directly to Amazon SageMaker Feature Store (or DynamoDB).
4. SageMaker Endpoint / Lambda queries the Online Feature Store at inference time with sub-10ms latency.

#### AWS Exam Takeaway

*   If a question asks for a service to store, manage, catalogue, and serve low-latency features for real-time model inference: Amazon SageMaker Feature Store.

    <a class="button secondary">GitHub</a>
* If a question asks for a service to compute complex aggregations over streaming data in real time before storing: AWS Managed Service for Apache Flink.
