# Data Engineering for Predictive Analytics

This project implements a full data-driven architecture for predictive analytics, covering the complete lifecycle from raw data ingestion to machine learning model deployment using urban data from the Open Data BCN portal. It utilizes a Data Lake achitecture to transition data from raw ingestion to analysis-ready features for predictive models, with all pipelines orchestrated via Apache Airflow.

For full implementation details, architcture diagrams, and design justifications, check `DataEngineering_PredictiveAnalytics.pdf`. 

## Data Engineering Backbone
The project follows a desing and implementation of a Data Lake with clearly separated zones to support scalable processing and data quality: 
- **Landing Zone**: Stores immutable, raw data ingested from source systems (JSON/CSV) in the local file system.
- **Formatted Zone**: Data is standardized and lightly cleaned using PySpark, then stored in MongoDB for flexible schema management.
- **Exploitation Zone**: Contains refined, analysis-ready datasets (features, KPIs), stored in Delta Lake format and optimized for machine learning and fast analytical accessw.

All data pipelines are implemented using Apache Spark (PySpark) and designed to support periodic execution.

## Predictive Analysis Backbone
Using data from the Exploitation Zone, the project implements an end-to-end machine learning pipeline for predictive analytics on urban data:
- **Model Training**: Automated pipelines using Spark MLib to train multiple classification models.
- **Model management**: Integration with MLflow to track hyperparameteres, performance metrics, and model versions.
- **Auto-Deployment**: Models are ranked based on the peformance, and the best-performing one is automatically tagged and promoted in the MLflow Model Registry for future use.

## Pipeline Orchestration
The entire lifecycle is managed by Apache Airflow via Directed Acyclic Graph (DAG), providing:
- **Dependency Management**: Ensures the formatting only runs after ingestion, and model training only starts once Delta tables are updated.
- **Retries and Alerts**: Automatic retry logic for failures and notification triggers for pipeline status.
- **Scheduling**: Periodic execution to simulate real-world data updates and support data drift scenarios.

This orchestration layer ensures reproducibility, reliability, and correct execution order across the full pipeline.

## Technologies Used
- `Apache Airflow`: Orchestration (DAGs, task dependencies, scheduling)
- `PySpark`: Distributed data processing (Spark SQL, DataFrames)
- `Local File System`: Landing Zone
- `MongoDB`: Formatted Zone
- `Delta Lake`: Exploitation Zone
- `Spark Mlib`: Machine learning pipelines
- `MLflow`: Model management (tracking, versioning, registry)

## Authors
The project was developed as part of an Advanced Databases course assignment at the Universitat Politècnica de Catalunya (UPC) by:
- Ona Siscart Noguer
- Aina Vila Arbusà
