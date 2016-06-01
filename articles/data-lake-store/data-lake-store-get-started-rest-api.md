<properties 
   pageTitle="使用 REST API 開始使用Data Lake Store | Microsoft Azure" 
   description="使用 WebHDFS REST API 執行 Data Lake Store 上的作業" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/29/2016"
   ms.author="nitinme"/>

# 使用 REST API 開始使用 Azure Data Lake Store

> [AZURE.SELECTOR]
- [入口網站](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

在本文中，您將了解如何使用 WebHDFS REST API 和 Data Lake Store REST API 執行帳戶管理，以及 Azure Data Lake Store 上的檔案系統作業。Azure Data Lake Store 會公開自己的 REST API 以進行帳戶管理作業。不過，因為 Data Lake Store 與 HDFS 和 Hadoop 生態系統相容，所以它支援使用 WebHDFS REST API 進行檔案系統作業。

>[AZURE.NOTE] 如需 Data Lake Store 之 REST API 支援的詳細資訊，請參閱 [Data Lake Store REST API 參考](https://msdn.microsoft.com/library/mt693424.aspx)。

## 必要條件

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **啟用您的 Azure 訂用帳戶**以使用 Data Lake Store 公開預覽版。請參閱[指示](data-lake-store-get-started-portal.md#signup)。
- **建立 Azure Active Directory 應用程式**。有兩種方式可使用 Azure Active Direcotry 來進行驗證 - **互動式**和**非互動式**。根據您要進行驗證的方式，會有不同的必要條件。
	* **針對互動式驗證** (本文所使用的方式) - 在 Azure Active Directory 中，您必須建立**原生用戶端應用程式**。一旦您建立應用程式，請擷取與下列應用程式相關的值。
		- 取得應用程式的**用戶端識別碼**和**重新導向 URI**
		- 設定委派權限

	* **針對非互動式驗證** - 在 Azure Active Directory 中，您必須建立 **Web 應用程式**。一旦您建立應用程式，請擷取與下列應用程式相關的值。
		- 取得應用程式的**用戶端識別碼**、**用戶端密碼**和**重新導向 URI**
		- 設定委派權限
		- 將 Azure Active Directory 應用程式指派給角色。角色可以在您要授與權限給 Azure Active Directory 應用程式的範圍層級。例如，您可以在訂用帳戶層級或資源群組的層級指派應用程式。如需指示，請參閱[指派應用程式給角色](../resource-group-create-service-principal-portal.md#assign-application-to-role)。 

	如需有關如何擷取這些值、設定權限和指派角色的指示，請參閱[使用入口網站建立 Active Directory 應用程式和服務主體](../resource-group-create-service-principal-portal.md)。

- [cURL](http://curl.haxx.se/)。本文使用 cURL 示範如何對 Data Lake Store 帳戶進行 REST API 呼叫。

## 如何使用 Azure Active Directory 驗證？

您可以使用兩種方法來使用 Azure Active Directory 進行驗證。

### 互動式 (使用者驗證)

在此案例中，應用程式會提示使用者登入，且會在使用者的內容中執行所有作業。執行下列步驟以進行互動式驗證。

1. 透過您的應用程式，將使用者重新導向至下列 URL：

		https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

	>[AZURE.NOTE] \<REDIRECT-URI> 需要編碼才能在 URL 中使用。因此，https://localhost，使用 `https%3A%2F%2Flocalhost`)

	本教學課程的目的是讓您取代以上 URL 中的預留位置值，並將此值貼在網路瀏覽器網址列中。系統會將您重新導向，以使用 Azure 登入資料來進行驗證。一旦成功登入，回應會顯示在瀏覽器網址列中。回應格式如下：
		
		http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. 擷取回應中的授權碼。在本教學課程中，您可以從網路瀏覽器的網址列中複製授權碼，並在 POST 要求中傳遞至權杖端點，如下所示：

		curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

	>[AZURE.NOTE] 在此情況下，不需要編碼 \<REDIRECT-URI>。

3. 回應為 JSON 物件，包含存取權杖 (例如 `"access_token": "<ACCESS_TOKEN>"`) 重新整理權杖 (例如：`"refresh_token": "<REFRESH_TOKEN>"`)。您的應用程式會在存取 Azure Data Lake Store 時使用存取權杖，並會在存取權杖過期時重新整理以取得另一個存取權杖。

		{"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":	"1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  存取權杖過期時，您可以使用重新整理權杖要求新的存取權杖，如下所示：

		 curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      		-F grant_type=refresh_token \
      		-F resource=https://management.core.windows.net/ \
      		-F client_id=<CLIENT-ID> \
      		-F refresh_token=<REFRESH-TOKEN>
 
如需互動使用者驗證的詳細資料，請參閱[授權碼授與流程](https://msdn.microsoft.com/library/azure/dn645542.aspx)。

### 非互動式

在此案例中，應用程式提供自己的認證來執行作業。為此，您必須發出 POST 要求，如下列所示要求。

	curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

這項要求的輸出將包含授權權杖 (在以下的輸出中以 `access-token` 表示)，您接下來將會利用 REST API 呼叫將其傳遞。將此驗證權杖儲存在文字檔中；您將在本文稍後需要此權杖。

	{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

本文使用**非互動式**方法。如需有關非互動式 (服務對服務呼叫) 的詳細資訊，請參閱[使用認證進行服務對服務呼叫](https://msdn.microsoft.com/library/azure/dn645543.aspx)。

## 建立 Data Lake Store 帳戶

這項作業以在[這裡](https://msdn.microsoft.com/library/mt694078.aspx)定義的 REST API 呼叫為基礎。

使用下列 cURL 命令。以 Data Lake Store 名稱取代 **<yourstorename>**。

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

在上述命令中，以您稍早擷取的授權權杖取代 <`REDACTED`>。此命令的要求承載包含在提供給上方 `-d` 參數的 **input.json** 檔案中。Input.json 檔案的內容如下所示︰

	{
	"location": "eastus2",
	"tags": {
		"department": "finance"
		},
	"properties": {}
	}	

## 在 Data Lake Store 帳戶中建立資料夾

這項作業以[這裡](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory)定義的 WebHDFS REST API 呼叫為基礎。

使用下列 cURL 命令。以 Data Lake Store 名稱取代 **<yourstorename>**。

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

在上述命令中，以您稍早擷取的授權權杖取代 <`REDACTED`>。此命令會在 Data Lake Store 帳戶的根資料夾下建立名為 **mytempdir** 的目錄。

如果作業順利完成，您應該會看到像這樣的回應︰

	{"boolean":true}

## 在 Data Lake Store 帳戶中列入資料夾

這項作業以[這裡](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory)定義的 WebHDFS REST API 呼叫為基礎。

使用下列 cURL 命令。以 Data Lake Store 名稱取代 **<yourstorename>**。

	curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

在上述命令中，以您稍早擷取的授權權杖取代 <`REDACTED`>。

如果作業順利完成，您應該會看到像這樣的回應︰

	{
	"FileStatuses": {
		"FileStatus": [{
			"length": 0,
			"pathSuffix": "mytempdir",
			"type": "DIRECTORY",
			"blockSize": 268435456,
			"accessTime": 1458324719512,
			"modificationTime": 1458324719512,
			"replication": 0,
			"permission": "777",
			"owner": "NotSupportYet",
			"group": "NotSupportYet"
		}]
	}
	}

## 將資料上傳至 Data Lake Store 帳戶

這項作業以[這裡](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File)定義的 WebHDFS REST API 呼叫為基礎。

使用 WebHDFS REST API 上傳資料是兩個步驟的程序，如下所述。

1. 提交 HTTP PUT 要求，而不傳送要上傳的檔案資料。在下列命令中，以 Data Lake Store 名稱取代 **<yourstorename>**。

		curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

	此命令的輸出將會包含暫時重新導向 URL，如下所示。

		HTTP/1.1 100 Continue

		HTTP/1.1 307 Temporary Redirect
		...
		...
		Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
		...
		...

2. 您現在必須針對為回應中**位置**屬性所列出的 URL 提交另一個 HTTP PUT 要求。以 Data Lake Store 名稱取代 **<yourstorename>**。

		curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

	輸出將類似於：

		HTTP/1.1 100 Continue

		HTTP/1.1 201 Created
		...
		...

## 從 Data Lake Store 帳戶讀取資料

這項作業以[這裡](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File)定義的 WebHDFS REST API 呼叫為基礎。

從 Data Lake Store 帳戶讀取資料是兩個步驟的程序。

* 您要先針對端點 `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN` 提交 GET 要求。這樣會傳回要提交下一個 GET 要求的目標位置。
* 接下來您要針對端點 `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true` 提交 GET 要求。這樣會顯示檔案的內容。

不過，由於第一和第二個步驟之間的輸入參數沒有任何差異，您可以使用 `-L` 參數來提交第一個要求。`-L` 選項基本上會將兩個要求結合為一個，並且讓 cURL 在新的位置重做要求。最後會顯示所有要求呼叫的輸出，如下所示。以 Data Lake Store 名稱取代 **<yourstorename>**。

	curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

您應該會看到如下所示的輸出：

	HTTP/1.1 307 Temporary Redirect
	...
	Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
	...
	
	HTTP/1.1 200 OK
	...
	
	Hello, Data Lake Store user!

## 在 Data Lake Store 帳戶中重新命名檔案

這項作業以[這裡](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory)定義的 WebHDFS REST API 呼叫為基礎。

請使用下列 cURL 命令重新命名檔案。以 Data Lake Store 名稱取代 **<yourstorename>**。

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

您應該會看到如下所示的輸出：

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## 從 Data Lake Store 帳戶刪除檔案

這項作業以[這裡](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory)定義的 WebHDFS REST API 呼叫為基礎。

請使用下列 cURL 命令刪除檔案。以 Data Lake Store 名稱取代 **<yourstorename>**。

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

您應該會看到如下的輸出：

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## 刪除 Data Lake Store 帳戶

這項作業以在[這裡](https://msdn.microsoft.com/library/mt694075.aspx)定義的 REST API 呼叫為基礎。

使用下列 cURL 命令刪除 Data Lake Store 帳戶。以 Data Lake Store 名稱取代 **<yourstorename>**。

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

您應該會看到如下的輸出：

	HTTP/1.1 200 OK
	...
	...

## 另請參閱

- [與 Azure Data Lake Store 相容的開放原始碼巨量資料應用程式](data-lake-store-compatible-oss-other-applications.md)
 

<!---HONumber=AcomDC_0518_2016-->