## <a name="specifying-compression"></a>指定壓縮
處理大型資料集可能會導致 I/O 和網路瓶頸。 因此，存放區中的壓縮資料不但可以跨網路加速資料傳輸和節省磁碟空間，也能在處理巨量資料時帶來顯著的效能提升。 目前，Azure Blob 或內部部署檔案系統等以檔案為基礎的資料存放區支援壓縮。  

> [!NOTE]
> 不支援 **AvroFormat**、**OrcFormat** 或 **ParquetFormat** 的資料壓縮設定。 讀取這些格式的檔案時，Data Factory 會偵測及使用中繼資料中的壓縮轉碼器；寫入這些格式的檔案當，Data Factory 會選擇該格式的預設壓縮轉碼器。 例如，ZLIB for OrcFormat 和 SNAPPY for ParquetFormat。
>
>

若要指定資料集的壓縮，請使用資料集 JSON 中的 **壓縮** 屬性，如下列範例所示：   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```
假設上述範例資料集做為複製活動的輸出，複製活動會透過使用最佳比率的 GZIP 轉碼器壓縮初輸資料，然後將壓縮的資料寫入到 Azure Blob 儲存體中名為 pagecounts.csv.gz 的檔案。   

[壓縮]  區段有兩個屬性：  

* **類型：**壓縮轉碼器，它可以是 **GZIP**、**Deflate****BZIP2** 或 **ZipDeflate**。  
* **層級：**壓縮比，它可以是**最佳**或**最快**。

  * **最快：** 即使未以最佳方式壓縮所產生的檔案，壓縮作業也應儘速完成。
  * **最佳**：即使作業耗費較長的時間才能完成，壓縮作業也應以最佳方式壓縮。

    如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。

當您在輸入資料集 JSON 中指定 `compression` 屬性時，管線可以從來源讀取壓縮的資料，當您在輸出資料集 JSON 中指定屬性，複製活動可以將壓縮的資料寫入到目的地。 以下是一些範例案例：

* 從 Azure blob 讀取 GZIP 壓縮資料，將其解壓縮，並將結果資料寫入到 Azure SQL Database。 您可以利用設為 GZIP 的 `compression` `type` JSON 屬性，定義輸入 Azure Blob 資料集。
* 從來自內部部署檔案系統之純文字檔案讀取資料、使用 GZip 格式加以壓縮並將壓縮的資料寫入到 Azure blob。 您可以利用設為 GZip 的 `compression` `type` JSON 屬性，定義輸出 Azure Blob 資料集。
* 從 FTP 伺服器讀取.zip 檔、將它解壓縮以取得其中的檔案，並將這些檔案放入 Azure Data Lake Store。 您可以利用設為 ZipDeflate 的 `compression` `type` JSON 屬性，定義輸入 FTP 資料集。
* 從 Azure blob 讀取 GZIP 壓縮資料，將其解壓縮、使用 BZIP2 將其壓縮，並將結果資料寫入到 Azure blob。 您會在此情況下利用設為 GZIP 的 `compression` `type` 定義輸入 Azure Blob 資料集，並利用設為 BZIP2 的 `compression` `type` 定義輸出資料集。   


<!--HONumber=Jan17_HO3-->


