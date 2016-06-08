<properties
    pageTitle="在您的 Logic Apps 中新增 SMTP 連接器 | Microsoft Azure"
    description="搭配 REST API 參數來使用 SMTP 連接器的概觀"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/19/2016"
   ms.author="mandia"/>

# 開始使用 SMTP 連接器
連線到 SMTP 伺服器來傳送電子郵件。您可以從下列應用程式使用 SMTP 連接器︰

- 邏輯應用程式 (如本主題所述)
- PowerApps (如需完整清單，請參閱 [PowerApps 連線清單](https://powerapps.microsoft.com/tutorials/connections-list/))

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。

您可以利用 SMTP 來：

- 建置您的商務流程，包括使用 SMTP 傳送電子郵件。 
- 使用動作來傳送電子郵件。這個動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當您的 FTP 伺服器上有新檔案時，您可以取得該檔案，並利用 SMTP 來傳送已附加該檔案的電子郵件。 

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
SMTP 連接器提供下列動作，但不包含觸發程序。

|觸發程序 | 動作|
|--- | ---|
|None | 傳送電子郵件|

所有連接器都支援 JSON 和 XML 格式的資料。

## 建立至 SMTP 的連線

>[AZURE.INCLUDE [建立 SMTP 連線的步驟](../../includes/connectors-create-api-smtp.md)]

當您建立連線之後，請輸入 SMTP 的屬性，例如收件者或副本值。本主題的 **REST API 參考**一節會說明這些屬性。

>[AZURE.TIP] 您可以在其他的邏輯應用程式中，使用這個相同的 SMTP 連線。

## Swagger REST API 參考
適用的版本：1.0。

### 傳送電子郵件
將電子郵件傳送給一或多位收件者。```POST: /SendEmail```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|emailMessage| 多種|yes|body|無 |Email message|

## 物件定義

#### Email：SMTP 電子郵件

| 名稱 | 資料類型 | 必要|
|---|---|---|
|收件人|字串|no|
|CC|字串|no|
|主旨|字串|no|
|內文|字串|no|
|從|字串|no|
|IsHtml|布林值|no|
|密件副本|字串|no|
|重要性|字串|no|
|附件|array|no|


#### Attachment：電子郵件的附件

| 名稱 | 資料類型 |必要|
|---|---|---|
|FileName|字串|no|
|ContentId|字串|no|
|ContentData|字串|yes|
|ContentType|字串|yes|
|ContentTransferEncoding|字串|yes|


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0525_2016-->