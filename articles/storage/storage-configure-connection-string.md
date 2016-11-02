<properties 
    pageTitle="設定 Azure 儲存體的連接字串 | Microsoft Azure"
    description="設定 Azure 儲存體帳戶的連接字串。 連接字串包含在執行階段從應用程式驗證儲存體帳戶存取所需的資訊。"
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="configure-azure-storage-connection-strings"></a>設定 Azure 儲存體連接字串

## <a name="overview"></a>Overview

連接字串包含在執行階段從您的應用程式存取 Azure 儲存體帳戶中資料所需的驗證資訊。 您可以設定連接字串，以進行下列動作：

- 連線至 Azure 儲存體模擬器
- 在 Azure 中存取儲存體帳戶。
- 透過共用存取簽章 (SAS) 存取 Azure 中的指定資源。

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>儲存您的連接字串

您的應用程式需要在執行階段存取連接字串，才能驗證對 Azure 儲存體進行的要求。 您有幾種不同的選項，用來儲存連接字串：

- 對於在桌面上或裝置上執行的應用程式，您可以在 `app.config ` 檔案或 `web.config` 檔案中儲存此連接字串。 將連接字串加入 **AppSettings** 區段中。
- 對於在 Azure 雲端服務中執行的應用程式，您可以將連接字串儲存在 [Azure 服務組態結構描述 (.cscfg) 檔](https://msdn.microsoft.com/library/ee758710.aspx)中。 將此連接字串加入服務組態檔的 [ConfigurationSettings]  區段。
- 您也可以直接在您的程式碼中使用連接字串。 不過，在大部分情況下，建議您在組態檔中儲存組態字串。

在組態檔內儲存連接字串，可讓您更容易更新連接字串，藉此在儲存體模擬器和雲端中的 Azure 儲存體帳戶之間切換。 您只需要編輯連接字串以指向您的目標環境。

您可以使用 [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) 類別，在執行階段存取連接字串，而不論應用程式執行所在的地方為何。

## <a name="create-a-connection-string-to-the-storage-emulator"></a>建立儲存體模擬器的連接字串

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

如需儲存體模擬器的詳細資訊，請參閱 [使用 Azure 儲存體模擬器進行開發和測試](storage-use-emulator.md) 。

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>建立 Azure 儲存體帳戶的連接字串

若要建立 Azure 儲存體帳戶的連接字串，請使用以下的連接字串格式。 指出您是否要透過 HTTPS (建議選項) 或 HTTP 連線至儲存體帳戶、使用您的儲存體帳戶名稱來取代 `myAccountName`，以及使用您的帳戶存取金鑰來取代 `myAccountKey`：

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

例如，您的連接字串看起來會類似下列連接字串範例：

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Azure 儲存體服務可同時支援連接字串中的 HTTP 和 HTTPS；不過，強烈建議您使用 HTTPS。

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>使用共用存取簽章建立連接字串

[AZURE.INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>建立明確儲存體端點的連接字串

您可以在連接字串中明確指定服務端點，而不使用預設端點。 若要建立指定明確端點的連接字串，請使用下列格式來指定每個服務的完整服務端點，包括通訊協定規格 (HTTPS (建議選項) 或 HTTP)：

    DefaultEndpointsProtocol=[http|https];
    BlobEndpoint=myBlobEndpoint;
    QueueEndpoint=myQueueEndpoint;
    TableEndpoint=myTableEndpoint;
    FileEndpoint=myFileEndpoint;
    AccountName=myAccountName;
    AccountKey=myAccountKey

您可能想要指定明確端點的一個案例為是否您已將 Blob 儲存體端點對應至自訂網域。 在此情況下，您可以在連接字串中指定 Blob 儲存體的自訂端點，並選擇性地指定其他服務的預設端點 (如果您的應用程式使用它們)。

以下範例是指定 Blob 服務的明確端點的有效連接字串︰

    # Blob endpoint only
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    AccountName=storagesample;
    AccountKey=account-key

    # All service endpoints
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    FileEndpoint=myaccount.file.core.windows.net;
    QueueEndpoint=myaccount.queue.core.windows.net;
    TableEndpoint=myaccount;
    AccountName=storagesample;
    AccountKey=account-key

連接字串中列出的端點值可用來建構連接到 Blob 服務的要求 URI，而它會要求任何傳回到您程式碼的 URI 形式。

請注意，如果您選擇在連接字串中省略服務端點，則無法使用該連接字串，從程式碼中存取該服務中的資料。

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>建立包含端點尾碼的連接字串

若要建立區域或包含不同端點尾碼的執行個體 (例如 Azure China 或 Azure Governance) 中儲存體服務的連接字串，請使用下列連接字串格式。 指出您是否要透過 HTTP 或 HTTPS 連接到儲存體帳戶、使用您的儲存體帳戶名稱來取代 `myAccountName`、使用您的帳戶存取金鑰來取代 `myAccountKey`，以及使用 URI 尾碼來取代 `mySuffix`：


    DefaultEndpointsProtocol=[http|https];
    AccountName=myAccountName;
    AccountKey=myAccountKey;
    EndpointSuffix=mySuffix;


例如，您的連接字串應該看起來類似下列連接字串：

    DefaultEndpointsProtocol=https;
    AccountName=storagesample;
    AccountKey=<account-key>;
    EndpointSuffix=core.chinacloudapi.cn;

## <a name="parsing-a-connection-string"></a>剖析連接字串

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## <a name="next-steps"></a>後續步驟

- [使用 Azure 儲存體模擬器進行開發和測試](storage-use-emulator.md)
- [Azure 儲存體總管](storage-explorers.md)
- [使用共用存取簽章 (SAS)](storage-dotnet-shared-access-signature-part-1.md)



<!--HONumber=Oct16_HO2-->


