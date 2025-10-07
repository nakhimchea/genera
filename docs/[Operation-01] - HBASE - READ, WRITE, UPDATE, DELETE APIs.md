# HBASE - READ, WRITE, UPDATE, DELETE APIs

## HBase Operations

* Complete table management (create, delete, list)
* Row operations (put, get, delete, scan)
* Multi-column family support with advanced configurations
* Batch scanning with configurable limits

## HBase API Endpoints

### Table Management

* **GET** `/hbase/tables` - List all tables
* **POST** `/hbase/tables` - Create new table
* **DELETE** `/hbase/tables/{name}` - Delete table

### Row Operations

* **PUT** `/hbase/tables/{table}/rows/{key}` - Insert/update row
* **GET** `/hbase/tables/{table}/rows/{key}` - Get specific row
* **GET** `/hbase/tables/{table}/rows` - Scan table
* **DELETE** `/hbase/tables/{table}/rows/{key}` - Delete row
