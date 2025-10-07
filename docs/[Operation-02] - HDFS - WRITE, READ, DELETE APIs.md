# HDFS - WRITE, READ, DELETE APIs

## API Endpoints

### Core Operations

* **POST** `/hdfs/files/upload?{filename}&{folder}&overwrite=false` - Upload any file type with metadata
* **GET** `/hdfs/files?{path}&include_metadata=true` - List files with optional metadata
* **GET** `/hdfs/metadata/{filename}&{folder}` - Read file metadata
* **DELETE** `/hdfs/files/{filename}` - Delete file and its metadata

## Features

* **Universal File Support**: Upload and manage any file type to HDFS
* **Metadata Tracking**: Automatic generation and storage of file metadata, i.e:
```json
{
  "filename": "report.pdf",
  "file_path": "/uploads/report.pdf",
  "file_type": "application/pdf",
  "created_at": "2025-08-22T13:13:40Z",
  "updated_at": "2025-08-22T13:13:40Z",
  "hash": "a3f5c9d8e2b4f1a7c6d9e8f0b1c2d3e4"
}
```
* **Overwrite Protection**: Configurable file overwrite protection
* **Folder Organization**: Support for hierarchical folder structures
* **Health Monitoring**: Built-in health checks for API and HDFS connectivity
