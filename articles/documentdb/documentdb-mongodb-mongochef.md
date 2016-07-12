<properties 
	pageTitle="使用 MongoChef 和具有 MongoDB 通訊協定支援的 DocumentDB 帳戶 | Microsoft Azure" 
	description="了解如何搭配使用 MongoChef 與 MongoDB 的通訊協定支援的 DocumentDB 帳戶，現在可供預覽。" 
	keywords="mongochef"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="stbaro"/>

# 使用 MongoChef 和具有 MongoDB 通訊協定支援的 DocumentDB 帳戶

若要使用 MongoChef，連接至具有 MongoDB 的通訊協定支援的 DocumentDB 帳戶，您必須：

- 下載並安裝 [MongoChef](http://3t.io/mongochef)
- 擁有具有 MongoDB 通訊協定支援的 DocumentDB 帳戶的[連接字串](documentdb-connect-mongodb-account.md)資訊。

## 在 MongoChef 中建立連接  

若要將具有 MongoDB 通訊協定支援的 DocumentDB 帳戶新增至 MongoChef 連接管理員，請執行下列步驟。

1. 使用[這裡](documentdb-connect-mongodb-account.md)的資訊，擷取具有 MongoDB 連接資訊通訊協定支援的 DocumentDB。

	![[連接字串] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

2. 按一下 [連接] 以開啟 [連接管理員]，然後按一下 [新增連線]

	![[MongoChef 連接管理員] 的螢幕擷取畫面](./media/documentdb-mongodb-mongochef/ConnectionManager.png)
	
2. 在 [新增連線] 視窗中，請在 [伺服器] 索引標籤上輸入具有 MongoDB 通訊協定支援的 DocumentDB 帳戶的主機 (FQDN) 和連接埠。
	
	![[MongoChef 連接管理員伺服器] 索引標籤的螢幕擷取畫面](./media/documentdb-mongodb-mongochef/ConnectionManagerServerTab.png)

3. 在 [新增連線] 視窗中，請在 [驗證] 索引標籤上選擇驗證模式 [標準 (MONGODB-CR 或 SCARM-SHA-1)]，並輸入使用者名稱和密碼。接受預設的驗證資料庫 (管理員)，或提供您自己的值。

	![[MongoChef 連接管理員驗證] 索引標籤的螢幕擷取畫面](./media/documentdb-mongodb-mongochef/ConnectionManagerAuthenticationTab.png)

4. 在 [新增連線] 視窗中，請在 [SSL] 索引標籤上勾選 [使用 SSL 通訊協定連接] 核取方塊和 [接受自我簽署的 SSL 憑證] 選項按鈕。

	![[MongoChef 連接管理員 SSL] 索引標籤的螢幕擷取畫面](./media/documentdb-mongodb-mongochef/ConnectionManagerSSLTab.png)

5. 按一下 [測試連接] 按鈕以驗證連接資訊，按一下 [確定] 以返回 [新增連線] 視窗，然後按一下 [儲存]。

	![[MongoChef 測試連接] 視窗的螢幕擷取畫面](./media/documentdb-mongodb-mongochef/TestConnectionResults.png)

## 使用 MongoChef 以建立資料庫、集合和文件  

若要使用 MongoChef 建立資料庫、集合和文件，請執行下列步驟。

1. 在 [連接管理員] 中，反白顯示連接，然後按一下 [連接]。

	![[MongoChef 連接管理員] 的螢幕擷取畫面](./media/documentdb-mongodb-mongochef/ConnectToAccount.png)

2. 以滑鼠右鍵按一下主機，然後選擇 [新增資料庫]。提供資料庫名稱，然後按一下 [確定]。
	
	![[MongoChef 新增資料庫] 選項的螢幕擷取畫面](./media/documentdb-mongodb-mongochef/AddDatabase1.png)

3. 以滑鼠右鍵按一下資料庫，然後選擇 [新增集合]。提供集合名稱，然後按一下 [建立]。

	![[MongoChef 新增集合] 選項的螢幕擷取畫面](./media/documentdb-mongodb-mongochef/AddCollection.png)

4. 按一下 [集合] 功能表項目，然後按一下 [新增文件]。

	![[MongoChef 新增文件] 功能表項目的螢幕擷取畫面](./media/documentdb-mongodb-mongochef/AddDocument1.png)

5. 在 [新增文件] 對話方塊中，貼上下列項目，然後按一下 [新增文件]。

		{
    	"_id": "AndersenFamily",
    	"lastName": "Andersen",
    	"parents": [
       		{ "firstName": "Thomas" },
       		{ "firstName": "Mary Kay"}
    	],
    	"children": [
       	{
           	"firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
           	"pets": [{ "givenName": "Fluffy" }]
       	}
    	],
    	"address": { "state": "WA", "county": "King", "city": "seattle" },
    	"isRegistered": true
		}

	
6. 新增其他文件，這次使用下列內容。

		{
	    "_id": "WakefieldFamily",
	    "parents": [
    	    { "familyName": "Wakefield", "givenName": "Robin" },
        	{ "familyName": "Miller", "givenName": "Ben" }
    	],
    	"children": [
	        {
            	"familyName": "Merriam", 
             	"givenName": "Jesse", 
            	"gender": "female", "grade": 1,
            	"pets": [
	                { "givenName": "Goofy" },
                	{ "givenName": "Shadow" }
            	]
        	},
        	{ 
	            "familyName": "Miller", 
             	"givenName": "Lisa", 
             	"gender": "female", 
             	"grade": 8 }
    	],
    	"address": { "state": "NY", "county": "Manhattan", "city": "NY" },
    	"isRegistered": false
		}

7. 執行範例查詢。例如，搜尋姓氏 'Andersen' 的家族，並傳回父母和州欄位。

	![Mongo Chef 查詢結果的螢幕擷取畫面](./media/documentdb-mongodb-mongochef/QueryDocument1.png)
	

## 後續步驟

- 探索具有 MongoDB 通訊協定支援的 DocumentDB 的[範例](documentdb-mongodb-samples.md)。
- 了解具有 MongoDB 通訊協定支援的 DocumentDB 的[預覽開發指導方針](documentdb-mongodb-guidelines.md)。

 

<!---HONumber=AcomDC_0629_2016-->