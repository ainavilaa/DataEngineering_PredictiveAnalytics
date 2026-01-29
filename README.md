# Data Engineering for Predictive Analytics
This project implements an end-to-end Data Engineering pipeline using **Apache Airflow**, **Apache Spark**, and **MongoDB**. It automates the collection, formatting, and processing of Barcelona's rental and social data to perform predictive analysis on neighborhood prices.

For full implementation details, architcture diagrams, and design justifications, check `DataEngineering_PredictiveAnalytics.pdf`. 

---
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

## Run
### 1. Prerequisites
Ensure the following are installed in your Linux environment:
* **Python 3.10+**
* **Java 8 or 11** (Required for Apache Spark)
* **Docker** (Used by the script to automatically launch a MongoDB container)

### 2. Environment Setup
We recommend using a virtual environment to manage dependencies:
```bash
# Create and activate virtual environment
python3 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```
### 3. Starting Services
The following script automates the setup. It creates the virtual environment, installs Airflow and dependencies, launches a **MongoDB container** (Docker), and initializes the Airflow database and admin user.
```
chmod +x start_env.sh
./start_env.sh
```
### 4. Running the Pipeline

You can trigger and monitor the DAG in two ways:

**Option A: Terminal**
Run our custom orchestrator. It will trigger a new DAG run and provide a real-time dashboard with automated error diagnostics:
```
#@main_project_folder>  python3 run.py
```

**Option B: Airflow Web Interface (GUI)**
* Visit http://localhost:8080
* Credentials:
    * Username: admin
    * Password: admin
* Find bcn_rental_prediction_pipeline in the DAG list.
* Unpause the DAG (toggle the switch to blue).
* Click on the DAG name and use the "Play" button (Trigger DAG) to start.

*Note: If you run the python script, you can also open the browser to see how the run advances using the same URL.* 

### 5. Shutting Down

To safely terminate all background processes, including the Airflow scheduler, the webserver, and the MongoDB Docker container, run:
```
chmod +x stop_env.sh
./stop_env.sh
```
*Note:Remember to type `deactivate` in your terminal to exit the Python virtual environment. *

## Project Structure
This is a brief guide for orientation purposes and in case anything should fail: 

```
proj2_BDA/
├── dags/                    # DAG orchestration
│   └── orchestration.py     # orchestrator (airflow)
├── pipelines/               # Data engineering an Data analysis backbones
|   ├── utils.py
│   ├── collectors/         
│   ├── formatters/          
│   ├── transformers/        
│   └── models/              # ML training and evaluation
├── scripts/                 # Utility scripts
├── data/                    
│   ├── raw_data/            # Source files (price data read rom here)
│   ├── landing_zone/        
│   ├── formatted_zone/      # empty folder (data in MongoDB)
│   └── exploitation_zone/   
├── logs/                    # pipeline execution logs
├── mlresults/               # Data Analysis results csv and visualization notebook
├── mlruns/                  # M;LFlow run logs
├── config.yaml              # Global configuration
├── run.py                   # application trigger script and monitoring
├── requirements.txt         # dependencies 
├── README.md                # this file
├── start_env.sh             # Service initialization script
└── stop_env.sh              # Service termination script
```
## Authors
The project was developed as part of an Advanced Databases course assignment at the Universitat Politècnica de Catalunya (UPC) by:
- Ona Siscart Noguer
- Aina Vila Arbusà
