Data Factory 中的複製活動會將資料從來源資料存放區複製到接收資料存放區。 Data Factory 支援下列資料存放區。 可將來自任何來源的資料寫入任何接收器。 按一下資料存放區，即可了解如何將資料複製到該存放區，以及從該存放區複製資料。

| 類別 | 資料存放區 | 支援作為來源 | 支援作為接收器 |
|:--- |:--- |:--- |:--- |
| **Azure** |[Azure Blob 儲存體](../articles/data-factory/data-factory-azure-blob-connector.md) |✓  |✓  |
| &nbsp; |[Azure Data Lake Store](../articles/data-factory/data-factory-azure-datalake-connector.md) |✓  |✓  |
| &nbsp; |[Azure DocumentDB](../articles/data-factory/data-factory-azure-documentdb-connector.md) |✓ |✓  |
| &nbsp; |[Azure SQL Database](../articles/data-factory/data-factory-azure-sql-connector.md) |✓  |✓  |
| &nbsp; |[Azure SQL 資料倉儲](../articles/data-factory/data-factory-azure-sql-data-warehouse-connector.md) |✓  |✓  |
| &nbsp; |[Azure 搜尋服務索引](../articles/data-factory/data-factory-azure-search-connector.md) | |✓  |
| &nbsp; |[Azure 表格儲存體](../articles/data-factory/data-factory-azure-table-connector.md) |✓  |✓  |
| **資料庫** |[Amazon Redshift](../articles/data-factory/data-factory-amazon-redshift-connector.md) |✓  | |
| &nbsp; |[DB2](../articles/data-factory/data-factory-onprem-db2-connector.md)* |✓  | |
| &nbsp; |[MySQL](../articles/data-factory/data-factory-onprem-mysql-connector.md)* |✓  | |
| &nbsp; |[Oracle](../articles/data-factory/data-factory-onprem-oracle-connector.md)* |✓  |✓  |
| &nbsp; |[PostgreSQL](../articles/data-factory/data-factory-onprem-postgresql-connector.md)* |✓  | |
| &nbsp; |[SAP Business Warehouse](../articles/data-factory/data-factory-sap-business-warehouse-connector.md)* |✓  | |
| &nbsp; |[SAP HANA](../articles/data-factory/data-factory-sap-hana-connector.md)* |✓  | |
| &nbsp; |[SQL Server](../articles/data-factory/data-factory-sqlserver-connector.md)* |✓  |✓  |
| &nbsp; |[Sybase](../articles/data-factory/data-factory-onprem-sybase-connector.md)* |✓  | |
| &nbsp; |[Teradata](../articles/data-factory/data-factory-onprem-teradata-connector.md)* |✓  | |
| **NoSQL** |[Cassandra](../articles/data-factory/data-factory-onprem-cassandra-connector.md)* |✓  | |
| &nbsp; |[MongoDB](../articles/data-factory/data-factory-on-premises-mongodb-connector.md)* |✓  | |
| **檔案** |[Amazon S3](../articles/data-factory/data-factory-amazon-simple-storage-service-connector.md) |✓  | |
| &nbsp; |[檔案系統](../articles/data-factory/data-factory-onprem-file-system-connector.md)* |✓  |✓  |
| &nbsp; |[FTP](../articles/data-factory/data-factory-ftp-connector.md) |✓  | |
| &nbsp; |[HDFS](../articles/data-factory/data-factory-hdfs-connector.md)* |✓  | |
| &nbsp; |[SFTP](../articles/data-factory/data-factory-sftp-connector.md) |✓  | |
| **其他** |[一般 HTTP](../articles/data-factory/data-factory-http-connector.md) |✓  | |
| &nbsp; |[泛型 OData](../articles/data-factory/data-factory-odata-connector.md) |✓  | |
| &nbsp; |[一般 ODBC](../articles/data-factory/data-factory-odbc-connector.md)* |✓  | |
| &nbsp; |[Salesforce](../articles/data-factory/data-factory-salesforce-connector.md) |✓  | |
| &nbsp; |[Web 資料表 (來自 HTML 的資料表)](../articles/data-factory/data-factory-web-table-connector.md) |✓  | |
| &nbsp; |[GE Historian](../articles/data-factory/data-factory-odbc-connector.md#ge-historian-store)* |✓  | | |

> [!NOTE]
> 具有 * 的資料存放區可以在內部部署環境或 Azure IaaS 上，並且需要您在內部部署/Azure IaaS 機器上安裝 [資料管理閘道](../articles/data-factory/data-factory-data-management-gateway.md) 。
>
>
