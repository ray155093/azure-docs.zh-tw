<properties 
	pageTitle="連接至具有 MongoDB 的通訊協定支援的 DocumentDB 帳戶 | Microsoft Azure" 
	description="了解如何連接至具有 MongoDB 的通訊協定支援的 DocumentDB 帳戶，現在可供預覽。使用 MongoDB 連接字串來連接。" 
	keywords="mongodb 連接字串"
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
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

# 如何連接至具有 MongoDB 的通訊協定支援的 DocumentDB 帳戶

了解如何使用標準 MongoDB 連接字串 URI 格式，連接至具有 MongoDB 的通訊協定支援的 DocumentDB 帳戶。

## 取得帳戶的連接字串資訊

1. 在新的視窗中，登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [所有設定] 刀鋒視窗中，按一下 [連接字串]。若要瀏覽至 [所有設定]，在 Jumpbar 上按一下 [瀏覽]、按一下 [DocumentDB] 帳戶，然後選取具有 MongoDB 通訊協定支援的 DocumentDB 帳戶。

	![[所有設定] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. [連接字串資訊] 刀鋒視窗隨即開啟，並且顯示使用 MongoDB 的驅動程式連接至帳戶所需的所有資訊，包括預先建構的連接字串。

	![[連接字串] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## 連接字串需求

請務必注意，DocumentDB 支援標準 MongoDB 連接字串 URI 格式，有幾個特定需求︰DocumentDB 帳戶需要驗證和透過 SSL 的安全通訊。因此，連接字串格式為：

	mongodb://username:password@host:port/[database]?ssl=true

[連接字串] 刀鋒視窗中此字串的值可用的位置如上所述。

- 使用者名稱 (必要)
	- DocumentDB 帳戶名稱
- 密碼 (必要)
	- DocumentDB 帳戶密碼
- 主機 (必要)
	- DocumentDB 帳戶的 FQDN
- 連接埠 (必要)
	- 10250
- 資料庫 (選擇性)
	- 連接所使用的預設資料庫
- ssl=true (必要)

例如，請考慮上面的 [連接字串資訊] 中顯示的帳戶。有效的連接字串為：
	
	mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## 連接 MongoDB 的 C# 驅動程式
如先前所述，所有 DocumentDB 帳戶都需要驗證和透過 SSL 的安全通訊。雖然 MongoDB 連接字串的 URI 格式支援 ssl=true 查詢字串參數，使用 MongoDB C# 驅動程式，在建立 MongoClient 時需要使用 MongoClientSettings 物件。有了上述的帳戶資訊，下列程式碼片段會示範如何連接到帳戶，並使用「工作」資料庫。

	        MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
	

## 後續步驟


- 了解如何[使用 MongoChef](documentdb-mongodb-mongochef.md) 和具有 MongoDB 通訊協定支援的 DocumentDB 帳戶。
- 探索具有 MongoDB 通訊協定支援的 DocumentDB 的[範例](documentdb-mongodb-samples.md)。

 

<!---HONumber=AcomDC_0601_2016-->