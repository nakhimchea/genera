# [Data] Web App - Attach on HBase & Hadoop (API Endpoints)

1. Merge Data APIs (Hadoop and HBase) to Image of HBase and Hadoop by combining Docker compose file and Docker file.

## API Health Check

![Health Check Response](image1.png)

**Endpoint:** `GET http://localhost:8000/health`

Response shows:
- API status: healthy
- HDFS status: healthy
- Metadata system: enabled
- Settings configuration (HDFS host, port, URL, data directory)
- Available endpoints for HDFS and HBase operations

## HDFS File Listing

![HDFS Files Response](image2.png)

**Endpoint:** `GET {{base_url}}/files`

Response shows list of files in HDFS:
- `.metadata`
- `hbase`
- `n8n`
- `s3`
- Total count: 4 files

## HBase Table Listing

![HBase Tables Response](image3.png)

**Endpoint:** `GET {{base_url}}/hbase/tables`

Response shows:
- Tables: `employee`
- Total count: 1 table
