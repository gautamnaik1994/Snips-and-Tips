# Glue and Athena

### How Athena Works with Nested JSON

Athena is a serverless, schema-on-read engine powered by Trino/Presto. It reads raw files directly from Amazon S3 using a Deserializer (SerDe) like `org.openx.data.jsonserde.JsonSerDe`.

* Nested JSON handling: Objects within objects are mapped to the `STRUCT` data type, and lists are mapped to `ARRAY`. You query nested fields using dot notation (`user.address.city`) or flatten arrays using the `UNNEST` SQL operator.
* Missing Keys handling: If a record is missing a field present in the schema, Athena simply evaluates it as `NULL`. It does not throw an error or crash.

### How AWS Glue Crawler Builds the Data Catalog

When a Glue Crawler scans your JSON files on S3, it processes records sequentially using Classifiers (specifically, built-in or custom JSON Classifiers):

1. Infers Nested Types: If it finds a nested object like `{"user": {"id": 101, "role": "admin"}}`, it creates a `STRUCT<id: bigint, role: string>` column in the AWS Glue Data Catalog.
2. Handles Missing Keys (Schema Union): Glue builds a superset (union) of all keys across all records in your dataset. If Record 1 has `{a, b}` and Record 2 has `{b, c}`, Glue creates a table schema containing all three columns (`a`, `b`, `c`). When querying, missing keys evaluate to `NULL`.
3. Resolves Data Type Conflicts: If Record 1 has `"id": 101` (integer) and Record 2 has `"id": "A-101"` (string), Glue resolves conflicting types by merging them into `string` or marking the column with a dynamic schema state.

Key  Takeaways

* Format Requirement: Standard JSON SerDes require ndjson (newline-delimited JSON), where each JSON record sits on a single line. Multi-line or pretty-printed JSON requires the Amazon Ion SerDe.
* Glue DynamicFrames: In Glue ETL jobs, nested JSON is loaded into a `DynamicFrame` without requiring a fixed schema up front. You can use transforms like `.relationalize()` or `.unnest()` to convert nested structs/arrays into flat relational columns.
* ML Pipeline Optimization: For ML workflows, leaving JSON as-is can be slow and expensive. Converting nested JSON into columnar formats like Apache Parquet via Glue ETL drastically improves Athena query performance and lowers costs for ML feature extraction.

#### Quick  Reference Summary

| **Inconsistent Case**                | **Glue Crawler Result**                     | **Athena Behavior**                                                    | **Fix/Best Practice**                                                           |
| ------------------------------------ | ------------------------------------------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Missing key in nested JSON           | Generates unified `STRUCT`                  | Missing key evaluates to `NULL`                                        | None needed; handled natively.                                                  |
| Missing key in Array of objects      | Generates `ARRAY<STRUCT>`                   | Missing key evaluates to `NULL`                                        | None needed; handled natively.                                                  |
| Type Conflict (`String` vs `STRUCT`) | Logs a `ChoiceType` or defaults to `STRING` | Query fails with SerDe Error (unless `use.null.for.invalid.data=true`) | Use Glue ETL `ResolveChoice` or map as `STRING` and parse using `json_extract`. |

### Spark vs Python Shell

While Glue is best known for running Apache Spark, it is not restricted to Spark only.

You do not have to use heavy multi-node Spark clusters for small tasks. AWS Glue natively supports Python Shell Jobs, which run plain Python scripts without spinning up Spark or Java virtual machines.

#### How Python Shell Jobs Work

Instead of spinning up a multi-node cluster, a Python Shell job provisions a single, standard Linux environment running a native Python interpreter.

* Execution Engine: Standard Python 3.x (no PySpark, no JVM overhead).
* Pre-installed Libraries: It comes pre-loaded with common data engineering and data science libraries, including `pandas`, `numpy`, `scikit-learn`, `boto3`, `scipy`, and `s3fs`.
* Billing & Hardware: You choose between two lightweight compute profiles:
  * 1/16 DPU (0.0625 DPU): Gives you \~1 vCPU and 1 GB RAM.
  * 1 DPU: Gives you \~4 vCPUs and 16 GB RAM.

#### Why Use Python Shell Instead of Spark?

| **Feature**   | **Glue Spark Job**                   | **Glue Python Shell Job**                               |
| ------------- | ------------------------------------ | ------------------------------------------------------- |
| Startup Time  | \~1 to 3 minutes (cluster spin-up)   | \~5 to 10 seconds                                       |
| Minimum DPUs  | 2 DPUs (Worker G.1X/G.2X)            | 0.0625 DPU (1/16th)                                     |
| Billing Floor | 1 minute (2 DPUs min = \~$0.015/min) | 1 minute (0.0625 DPU min = \~$0.00046/min)              |
| Max Data Size | Multi-GB to Terabytes                | Small to medium (fits in instance RAM, e.g., < 10 GB)   |
| Best For      | Distributed Big Data ETL             | Small CSVs, API polling, Pandas cleanups, orchestration |

### AWS Glue

The AWS Glue Data Catalog serves as a centralized metadata repository (a managed Hive Metastore) that decouples underlying data storage (like Amazon S3) from query execution engines.<br>

#### Key Features & Technical Characteristics

* Schema-on-Read Definition: Stores metadata—including database/table names, column data types, field structures (`STRUCT`, `ARRAY`), and exact physical S3 URIs—without modifying the raw files on disk.<br>
* Automated Data Discovery (Crawlers): Glue Crawlers scan files sequentially (CSVs, JSON, Parquet) to infer schemas automatically using classifiers.<br>
* Schema Unioning (Superset Strategy): Handles inconsistent semi-structured data by creating a unified superset schema. Missing keys across records evaluate natively to `NULL`.<br>
* Handling Structural Conflicts: When primitive types clash with nested objects (e.g., `STRING` vs. `STRUCT`), Glue falls back to dynamic types (`ChoiceType`/`STRING`), avoiding total schema generation failure.<br>
* Partition Metadata & Pruning: Maps directory structures (e.g., `/year=2026/month=08/`) to accelerate query performance by allowing engines to skip non-matching S3 file paths without scanning full directories.<br>
* Multi-Engine Interoperability: Functions as a single source of truth across AWS services (Athena, SageMaker, Glue ETL, Redshift Spectrum, EMR).<br>
* Centralized Governance: Integrates with AWS Lake Formation to enforce fine-grained, column-level, and row-level access permissions.<br>

#### What the Glue Data Catalog Is _Not_

* Not a File Reader or Execution Engine: Athena and Spark engines read the raw S3 files directly using the schema pointers inside the Catalog; query engines do not route file payloads through the Catalog itself.<br>
* Not an Inverted/B-Tree Index: It does not index individual row values for point lookups (`WHERE id = 123`). It indexes metadata at the table, column, file, and partition levels.<br>
* Not Aware of Column Renames: It cannot infer if a field was renamed (e.g., `salary` $$ $\rightarrow$ $$ `ctc`). It treats renames statelessly as one deleted column + one added column unless handled via Glue ETL (`rename_field`) or manual schema updates.
