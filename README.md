# S3 Parquet dbt Pipeline

A multi-platform data pipeline that processes parquet files from S3 and transforms them using dbt with support for three data warehouses: Databricks, Redshift, and Starburst.

## Overview

This project implements a proof-of-concept data pipeline for processing parquet files stored in S3 with a date-partitioned structure (yy/mm/dd) through dbt transformations, orchestrated by Airflow, and delivered to multiple data warehouse platforms.

### Key Features

- **Multi-Platform Support**: Single codebase targeting Databricks, Redshift, and Starburst
- **Orchestrated Workflow**: Airflow DAGs with Cosmos package for dbt integration
- **Date-Partitioned Processing**: Dynamic partition handling with year/month/day structure
- **Modularized Transformations**: Consistent transformation logic with platform-specific optimizations
- **Infrastructure as Code**: Docker for local development, Terraform for cloud deployment

## Architecture

![Architecture Diagram](docs/images/architecture.png)

The architecture consists of four main layers:

1. **Data Source Layer**: S3 bucket with parquet files organized in a date-partitioned structure
2. **Orchestration Layer**: Airflow with Cosmos for workflow management
3. **Transformation Layer**: dbt Core with multi-target profiles
4. **Warehouse Layer**: Databricks, Redshift, and Starburst endpoints

## Prerequisites

- Python 3.8+
- Docker and Docker Compose
- AWS account with S3 access
- Access to at least one of: Databricks, Redshift, or Starburst
- dbt CLI (for local development)

## Repository Structure
s3-parquet-dbt-pipeline/
├── .github/                           # GitHub Actions workflows
│   └── workflows/
│       ├── dbt-test.yml               # dbt tests workflow
│       └── deploy.yml                 # Deployment workflow
├── airflow/                           # Contains all Airflow-related configurations
│   ├── dags/                          # Directory for Airflow DAG definitions
│   │   └── multi_platform_dag.py      # Main DAG for orchestrating the pipeline
│   ├── plugins/                       # Directory for custom Airflow plugins
│   │   └── operators/                 # Any custom operators
│   ├── Dockerfile                     # Builds the Airflow container with required dependencies
│   └── requirements.txt               # Python dependencies for Airflow
├── dbt/                               # Contains all dbt project files
│   ├── models/                        # SQL models for data transformation
│   │   ├── sources/                   # YAML files defining external data sources
│   │   │   └── s3_sources.yml         # Defines S3 parquet data sources
│   │   ├── staging/                   # Initial transformation models
│   │   │   ├── stg_customer_data.sql  # Customer data staging model
│   │   │   ├── stg_order_data.sql     # Order data staging model
│   │   │   └── stg_product_data.sql   # Product data staging model 
│   │   └── marts/                     # Business-level models
│   │       ├── dim_customers.sql      # Customer dimension model
│   │       ├── dim_products.sql       # Product dimension model
│   │       └── fct_orders.sql         # Order fact model
│   ├── macros/                        # Reusable SQL functions
│   │   └── platform_utils.sql         # Platform-specific SQL adaptations
│   ├── tests/                         
│   │   └── data_tests.sql             # Custom data tests
│   ├── dbt_project.yml                # Main dbt project configuration
│   ├── packages.yml                   # External dbt packages
│   └── profiles/                      # Connection profiles
│       └── profiles.yml               # Multi-target profiles
├── infra/                             # Infrastructure as Code
│   ├── terraform/                     # Terraform modules for cloud deployment
│   │   ├── aws/                       # AWS resources definitions
│   │   ├── databricks/                # Databricks resources configuration
│   │   └── main.tf                    # Main Terraform configuration
├── scripts/                           # Utility scripts 
│   └── generate_sample_data.py        # Script to generate test data
├── docker-compose.yml                 # Docker configuration for local development
├── .env.example                       # Environment variables template
├── .gitignore                         # Git ignore file
├── requirements.txt                   # Python dependencies
├── setup.py                           # Package setup
└── README.md                          # Project documentation


## Quick Start
1. **Clone this repository**

2. **Set up environment variables**

3. **Start the local development environment**

4. **Access Airflow UI**

Navigate to http://localhost:8080
- Username: airflow
- Password: airflow

5. **Run a dbt model locally**


## Configuration Guide

### 1. AWS Configuration

1. Create an S3 bucket to store parquet files
2. Organize data in the pattern: `s3://bucket/table/yy/mm/dd/*.parquet`
3. Set up AWS Glue Catalog for metadata management
4. Configure AWS credentials with appropriate permissions

### 2. Data Warehouse Setup

#### Redshift
1. Create a Redshift cluster
2. Configure Redshift Spectrum for S3 access
3. Create schemas for transformation outputs

#### Databricks
1. Create a Databricks workspace
2. Configure Unity Catalog or Hive Metastore
3. Set up S3 mount points for data access

#### Starburst
1. Deploy Starburst Enterprise or Trino
2. Configure Hive connector for S3 access
3. Set up catalogs and schemas

### 3. Airflow Setup

1. Configure connections to AWS, Redshift, Databricks, and Starburst
2. Set Airflow variables for S3 bucket and paths
3. Enable the main DAG from the UI

## Usage Guide

### Running the Pipeline

1. **via Airflow UI**:
   - Navigate to the Airflow UI
   - Enable the `multi_platform_s3_pipeline` DAG
   - Trigger a manual run or wait for scheduled execution

2. **via Command Line**:
docker-compose exec airflow-scheduler airflow dags trigger multi_platform_s3_pipeline


### Adding New Data Sources

1. Add table definition in `dbt/models/sources/s3_sources.yml`
2. Create staging model in `dbt/models/staging/`
3. Create mart models in `dbt/models/marts/`
4. Update Airflow DAG if necessary

### Customizing Transformations

1. Modify SQL in the appropriate model files
2. Use macros for platform-specific SQL differences
3. Test changes with `dbt compile` before running

## Troubleshooting

### Common Issues

- **S3 Access Denied**: Check AWS credentials and bucket permissions
- **dbt Connection Errors**: Verify profiles.yml configuration and environment variables
- **Airflow Task Failures**: Check task logs in Airflow UI
- **Platform-Specific SQL Errors**: Review the logs for the specific platform

### Logs

- Airflow logs: Available in the Airflow UI or `/opt/airflow/logs`
- dbt logs: Available in `dbt/logs` or Airflow task logs

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run tests
5. Submit a pull request


