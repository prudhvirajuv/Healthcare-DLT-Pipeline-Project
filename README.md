# Healthcare-DLT-Pipeline-Project

<!-- TABLE OF CONTENTS -->
## Table of Contents

* [Overview](#Overview)
* [Architecture diagram](#architecture-diagram)
* [Technologies Used](#Technologies-Used)
* [Datasets and Source Code](#datasets-and-source-code)
* [Usage](#Usage)
* [Monitoring and Logging](#Monitoring-and-Logging)
* [Troubleshooting](#Troubleshooting)
* [License](#license)
* [Contact](#contact)


## Overview
This project implements an end-to-end Data Engineering solution using **Databricks** and **Delta Live Tables (DLT)** for processing healthcare data. The main objective is to build a robust ETL pipeline that reads data from Databricks File System (DBFS), performs incremental processing using Delta Lake, and stores the output across three layers: **Bronze** (raw data), **Silver** (validated data), and **Gold** (aggregated data).

The ETL process involves:
1. Reading the full dataset (map data) from one delta table and incremental data from another.
2. Joining and validating the data.
3. Aggregating outputs into a series of Delta Live Tables across the Bronze, Silver, and Gold layers.

## Architecture
The project follows a three-layer architecture:
- **Bronze Layer**: Stores raw, ingested data as-is from source systems.
- **Silver Layer**: Contains cleaned, validated, and transformed data.
- **Gold Layer**: Holds aggregated and processed data ready for analytics.

### Data Flow
1. **Data Ingestion**: Spark application reads raw data from the DBFS path and appends it to a Delta table in the Databricks catalog.
2. **DLT Pipeline**:
   - Reads full map data from the Bronze Delta table.
   - Reads incremental data from another Delta table.
   - Joins and validates the data.
   - Aggregates outputs to generate **Output 1** and **Output 2**.
3. **Output Storage**:
   - Writes validated data to the Silver table.
   - Performs further transformations and aggregations before writing the final output to the Gold table.

## Technologies Used
- **Apache Spark**: For reading data, transformations, and processing.
- **Databricks Delta Live Tables (DLT)**: For managing and processing data pipelines.
- **Delta Lake**: For ACID-compliant data storage and handling incremental data.
- **Databricks File System (DBFS)**: For storing input files.

<!-- Datasets and Source Code -->
## Datasets and Source Code
 - The dataset files are located in the /datasets/ folder.
 - The code files are located in the /code/ folder.

## Monitoring and Logging

Monitoring and logging are essential components of the ETL pipeline to ensure data quality, track performance, and quickly detect issues.

### Monitoring
1. **Databricks Jobs Dashboard**:
   - Monitor the execution status of your DLT pipeline using the Databricks Jobs UI. It provides insights into performance, run times, and task statuses.
   - Navigate to the **Jobs** page in Databricks and search for your DLT pipeline job to view its history and current status.

2. **DLT Pipeline Monitoring**:
   - Use the **Delta Live Tables UI** to track the pipeline's health, view data quality metrics, and monitor throughput. This UI displays metrics such as the number of records processed, error counts, and data quality rule violations.

3. **Event Logs**:
   - Enable **DLT Event Logs** to capture detailed metrics and logs for each pipeline run. Access these logs through the `system.dlt_events` table in the Databricks catalog for analysis and auditing.

4. **Databricks SQL Analytics**:
   - Use **Databricks SQL** to create real-time monitoring dashboards. Query metrics from Delta tables, including record counts and processing times, for efficient analysis.

### Logging
1. **Delta Lake Logs**:
   - Delta Lake automatically logs changes, including transactions and schema modifications. Access these logs using the Delta Table history:
     ```sql
     DESCRIBE HISTORY <delta_table_name>;
     ```

2. **Application Logs**:
   - Configure Spark applications to log processing details to Databricks' logging service or external logging systems such as **AWS CloudWatch**, **Azure Monitor**, or **Google Cloud Logging**.

3. **Error Handling and Alerts**:
   - Implement custom error handling in your DLT pipeline using **EXPECT** clauses for data quality checks. Set up alerts for failed expectations using Slack or email integrations.

## Troubleshooting

Below are common issues and solutions for the Healthcare DLT Pipeline project:

1. **Pipeline Fails to Start**:
   - **Issue**: Missing configuration or incorrect parameters in the DLT pipeline settings.
   - **Solution**: Verify that all required parameters (e.g., input paths, table names) are correctly configured. Check pipeline logs for specific error messages.

2. **Data Quality Issues**:
   - **Issue**: Inconsistent or incorrect data in the Silver or Gold tables.
   - **Solution**: 
     - Review data validation steps in the Silver layer.
     - Use the Delta Live Tables UI to inspect **Expectations** and identify data quality rule violations.
     - Implement stricter data quality checks or adjust transformation logic.

3. **Incremental Load Errors**:
   - **Issue**: Errors occur during incremental data processing from the Delta table.
   - **Solution**: 
     - Verify **watermark** and **checkpoint** configurations for correct incremental reads.
     - Check for schema changes in source data causing compatibility issues.

4. **Performance Issues**:
   - **Issue**: Slow processing times or high resource usage.
   - **Solution**:
     - Optimize Spark configurations (e.g., increase shuffle partitions).
     - Use Delta Lake's **OPTIMIZE** and **VACUUM** commands to improve performance:
       ```sql
       OPTIMIZE <delta_table_name>;
       VACUUM <delta_table_name> RETAIN 0 HOURS;
       ```

5. **Data Write Failures**:
   - **Issue**: Errors while writing to Delta tables due to schema mismatches or data conflicts.
   - **Solution**:
     - Enable schema evolution with `mergeSchema`:
       ```python
       df.write.format("delta").option("mergeSchema", "true").mode("append").save("/mnt/path/to/table")
       ```
     - Use `MERGE` statements for upserts:
       ```sql
       MERGE INTO target_table
       USING source_table
       ON target_table.id = source_table.id
       WHEN MATCHED THEN UPDATE SET *
       WHEN NOT MATCHED THEN INSERT *
       ```

6. **Missing Data in the Gold Layer**:
   - **Issue**: Aggregated data in the Gold table does not match expectations.
   - **Solution**: 
     - Validate transformation logic in the Silver layer before aggregation.
     - Review join conditions during aggregation to ensure all necessary keys are included.

By implementing robust monitoring, logging, and troubleshooting practices, the Healthcare DLT Pipeline can maintain data quality and quickly resolve any issues that arise.


<!-- LICENSE -->
## License
Distributed under the MIT License. See [LICENSE](LICENSE) for more information.

<!-- CONTACT -->
## Contact
Please feel free to contact me if you have any questions.
[Prudhvi](https://www.linkedin.com/in/prudhvi-raju-vegeshna-a45606195/) 
