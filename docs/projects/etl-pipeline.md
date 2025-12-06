# AWS Serverless ETL Pipeline

## The Problem
We needed to ingest 50GB of daily logs from an external API, sanitize PII data, and load it into Redshift for the analytics team, ensuring a latency of under 15 minutes.

## The Architecture
```mermaid
graph LR
    A[API Source] -->|JSON| B(AWS Lambda);
    B -->|Raw Data| C[(S3 Bucket)];
    C -->|Trigger| D{AWS Glue};
    D -->|Cleaned Parquet| E[(Redshift)];
    D -->|Logs| F[CloudWatch];

1.  **Ingestion:** AWS Lambda fetches data from API every 10 mins.
2.  **Storage:** Raw JSON lands in S3 (Bronze Layer).
3.  **Processing:** Glue Job converts JSON to Parquet and handles PII masking.
4.  **Warehousing:** Data is loaded into Redshift (Silver Layer).

## Key Code Snippet
Here is how I optimized the Lambda memory usage:

```python
def lambda_handler(event, context):
    # Streaming response to keep memory footprint low
    with requests.get(url, stream=True) as r:
        upload_to_s3(r.raw)