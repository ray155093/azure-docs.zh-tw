---
title: "使用 DocumentDB 模擬器在本機開發 | Microsoft Docs"
description: "您也可以免費使用 DocumentDB 模擬器在本機開發及測試應用程式，不需建立 Azure 訂用帳戶。"
services: documentdb
documentationcenter: 
keywords: "DocumentDB 模擬器"
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 0d0264857f552b65f5934eea686329e8d36a1204
ms.openlocfilehash: e86b1990ea38f9f8af4e5401fe37eec321075e64


---
# <a name="use-the-azure-documentdb-emulator-for-development-and-testing"></a>使用 Azure DocumentDB 模擬器進行開發和測試

[**下載模擬器**](https://aka.ms/documentdb-emulator)

Azure DocumentDB 模擬器提供一個模擬 Azure DocumentDB 服務的本機環境做為開發之用。 您可以使用 DocumentDB 模擬器在本機開發及測試應用程式，不需建立 Azure 訂用帳戶，也不會產生任何費用。 如果您滿意應用程式在 DocumentDB 模擬器中的運作方式，就可以切換成使用雲端的 Azure DocumentDB 帳戶。

我們建議從觀看下列影片開始，其中 Kirill Gavrylyuk 示範如何開始使用 DocumentDB 模擬器。

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="documentdb-emulator-system-requirements"></a>DocumentDB 模擬器系統需求
DocumentDB 模擬器的硬體和軟體需求如下︰

* 軟體需求
  * Windows Server 2012 R2、Windows Server 2016 或 Windows 10
*   最低硬體需求
  * 2 GB RAM
  * 10 GB 可用硬碟空間

## <a name="installing-the-documentdb-emulator"></a>安裝 DocumentDB 模擬器
您可以從 [Microsoft 下載中心](https://aka.ms/documentdb-emulator)下載並安裝 DocumentDB 模擬器。 

> [!NOTE]
> 若要安裝、設定和執行 DocumentDB 模擬器，您必須具備電腦的系統管理權限。

## <a name="checking-for-documentdb-emulator-updates"></a>檢查 DocumentDB 模擬器的更新
DocumentDB 模擬器包含內建的 Azure DocumentDB 資料總管可瀏覽 DocumentDB 內儲存的資料、建立新的集合，以及讓您知道有新的更新可供下載。 

> [!NOTE]
> 在某個 DocumentDB 模擬器版本中建立的資料不保證可以使用不同的版本存取。 如果您需要長期保存資料，建議您將資料儲存於 Azure DocumentDB 帳戶中，而不是 DocumentDB 模擬器中。 

## <a name="how-the-documentdb-emulator-works"></a>DocumentDB 模擬器的運作方式
DocumentDB 模擬器提供 DocumentDB 服務的高逼真度模擬。 它支援與 Azure DocumentDB 完全相同的功能，包括支援建立和查詢 JSON 文件、佈建和擴充集合，以及執行預存程序和觸發程序。 您可以使用 DocumentDB 模擬器開發及測試應用程式，並且只需對 DocumentDB 的連接端點進行單一組態變更，就能將它們部署至全球規模的 Azure。

雖然我們已建立實際 DocumentDB 服務的高逼真度本機模擬，但是 DocumentDB 模擬器的實作是不同的服務。 例如，DocumentDB 模擬器會使用標準的作業系統元件，例如使用本機檔案系統以獲得持續性，以及使用 HTTPS 通訊協定堆疊進行連線。 這表示，依賴 Azure 基礎結構的某些功能，例如全域複寫、讀取/寫入的單一數字毫秒延遲，以及可調式的一致性層級等，都無法透過 DocumentDB 模擬器使用。


## <a name="authenticating-requests-against-the-documentdb-emulator"></a>對 DocumentDB 模擬器的驗證要求
就像雲端的 Azure 文件一樣，您對 DocumentDB 模擬器的每個要求都必須經過驗證。 DocumentDB 模擬器支援主要金鑰驗證的單一固定帳戶及已知驗證金鑰。 此帳戶和金鑰都是唯一允許搭配 DocumentDB 模擬器使用的認證， 如下：

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> DocumentDB 模擬器所支援的主要金鑰僅能與模擬器搭配使用。 您無法將生產用的 DocumentDB 帳戶和金鑰與 DocumentDB 模擬器搭配使用。 

此外，就像 Azure DocumentDB 服務一樣，DocumentDB 模擬器僅支援透過 SSL 的安全通訊。

## <a name="start-and-initialize-the-documentdb-emulator"></a>啟動及初始化 DocumentDB 模擬器

若要啟動 Azure DocumentDB 模擬器，請選取 [開始] 按鈕或按下 Windows 鍵。 先輸入 **DocumentDB 模擬器**，再從應用程式清單選取模擬器。 

![選取 [開始] 按鈕或按下 Windows 鍵，先輸入 **DocumentDB 模擬器**，再從應用程式清單選取模擬器](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-start.png)

如果模擬器正在執行，您就會在 Windows 工作列通知區域看到圖示。 DocumentDB 模擬器預設會在接聽連接埠 8081 的本機電腦 ("localhost") 上執行。

![DocumentDB 本機模擬器的工作列通知](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-taskbar.png)

DocumentDB 模擬器預設會安裝到 `C:\Program Files\DocumentDB Emulator` 目錄。 您也可以從命令列啟動和停止模擬器。 如需詳細資訊，請參閱[命令列工具參考](#command-line)。

## <a name="start-the-local-emulator-data-explorer"></a>啟動本機模擬器資料總管

當本機模擬器啟動時，會自動在瀏覽器中開啟 DocumentDB 資料總管。 地址會顯示為 [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html)。 如果您關閉資料總管，想要稍後重新開啟，可以在瀏覽器中開啟 URL 或從 Windows 系統匣圖示中的 DocumentDB 模擬器啟動，如下所示。

![DocumentDB 本機模擬器的資料總管啟動程式](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-data-explorer-launcher.png)

## <a name="developing-with-the-documentdb-emulator"></a>使用 DocumentDB 模擬器進行開發
在桌面上執行 DocumentDB 模擬器之後，就可以使用任何支援的 [DocumentDB SDK](documentdb-sdk-dotnet.md) 或 [DocumentDB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 與模擬器互動。 DocumentDB 模擬器也包含內建的資料總管，可讓您建立集合、檢視及編輯文件，而不需要撰寫任何程式碼。 

    // Connect to the DocumentDB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==",
        new ConnectionPolicy { EnableEndpointDiscovery = false });

> [!NOTE]
> 連線到模擬器時，您必須設定 EnableEndpointDiscovery = false 連接組態。

如果您使用 [MongoDB 的 DocumentDB 通訊協定支援](documentdb-protocol-mongodb.md)，請使用下列連接字串︰

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

您可以使用現有的工具，像是 [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) 連線到 DocumentDB 模擬器。 您也可以使用 [DocumentDB 資料移轉工具](https://github.com/azure/azure-documentdb-datamigrationtool)在 DocumentDB 模擬器與 Azure DocumentDB 服務之間移轉資料。

## <a name="export-the-documentdb-emulator-ssl-certificate"></a>匯出 DocumentDB 模擬器 SSL 憑證

.Net 語言和執行階段使用 Windows 憑證存放區來安全地連線到 DocumentDB 本機模擬器。 其他語言則有自己管理和使用憑證的方法。 Java 使用自己的[憑證存放區](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)，而 Python 使用[通訊端包裝函式](https://docs.python.org/2/library/ssl.html)。

若要取得憑證來搭配未整合 Windows 憑證存放區的語言和執行階段使用，您必須使用 Windows 憑證管理員將其匯出。 您可以執行 certlm.msc 或依照[匯出 DocumentDB 模擬器憑證](./documentdb-nosql-local-emulator-export-ssl-certificates.md)文章的逐步指示開始。 憑證管理員執行之後時，開啟 [個人憑證] (如下所示)，並將有 "DocumentDBEmulatorCertificate" 易記名稱的憑證匯出為 BASE-64 編碼的 X.509 (.cer) 檔案。

![DocumentDB 本機模擬器的 SSL 憑證](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-ssl_certificate.png)

遵循[新增憑證至 Java CA 憑證存放區](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store)文章中的指示，X.509 憑證就可以匯入 Java 憑證存放區。  憑證匯入 cacerts 存放區之後，Java 和 MongoDB 應用程式就可以連線到 DocumentDB 本機模擬器。

## <a name="a-idcommand-lineadocumentdb-emulator-command-line-tool-reference"></a><a id="command-line"></a>DocumentDB 模擬器命令列工具參考
您可以從安裝位置使用命令列來啟動和停止模擬器、設定選項，以及執行其他作業。

### <a name="command-line-syntax"></a>命令列語法

    DocumentDB.LocalEmulator.exe [/shutdown] [/datapath] [/port] [/mongoport] [/directports] [/key] [/?]

若要檢視選項清單，請在命令提示字元輸入 `DocumentDB.LocalEmulator.exe /?` 。

<table>
<tr>
  <td><strong>選項</strong></td>
  <td><strong>說明</strong></td>
  <td><strong>命令</strong></td>
  <td><strong>引數</strong></td>
</tr>
<tr>
  <td>[無引數]</td>
  <td>使用預設設定啟動 DocumentDB 模擬器</td>
  <td>DocumentDB.LocalEmulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>關閉 DocumentDB 模擬器</td>
  <td>DocumentDB.LocalEmulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>說明</td>
  <td>顯示命令列引數清單</td>
  <td>DocumentDB.LocalEmulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Datapath</td>
  <td>指定用來儲存資料檔案的路徑</td>
  <td>DocumentDB.LocalEmulator.exe /datapath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;︰可存取的路徑</td>
</tr>
<tr>
  <td>Port</td>
  <td>指定用於模擬器的連接埠號碼。  預設值為 8081</td>
  <td>DocumentDB.LocalEmulator.exe /port=&lt;port&gt;</td>
  <td>&lt;port&gt;︰單一連接埠號碼</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>指定要用於 MongoDB 相容性 API 的連接埠號碼。 預設值為 10250</td>
  <td>DocumentDB.LocalEmulator.exe /mongoport=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;︰單一連接埠號碼</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>指定要用於直接連線的連接埠。 預設值為 10251、10252、10253、10254</td>
  <td>DocumentDB.LocalEmulator.exe /directports:&lt;directports&gt;</td>
  <td>&lt;directports&gt;︰以逗號分隔的 4 個連接埠清單</td>
</tr>
<tr>
  <td>Key</td>
  <td>模擬器的授權金鑰。 金鑰必須是 64 位元組向量的 base-64 編碼方式</td>
  <td>DocumentDB.LocalEmulator.exe /key:&lt;key&gt;</td>
  <td>&lt;key&gt;：金鑰必須是 64 位元組向量的 base-64 編碼方式</td>
</tr>
<tr>
  <td>EnableThrottling</td>
  <td>指定啟用要求節流行為</td>
  <td>DocumentDB.LocalEmulator.exe /enablethrottling</td>
  <td></td>
</tr>
<tr>
  <td>DisableThrottling</td>
  <td>指定停用要求節流行為</td>
  <td>DocumentDB.LocalEmulator.exe /disablethrottling</td>
  <td></td>
</tr>
</table>

## <a name="differences-between-the-documentdb-emulator-and-azure-documentdb"></a>DocumentDB 模擬器和 Azure DocumentDB 之間的差異 
因為 DocumentDB 模擬器是在本機開發人員工作站上提供一個執行的模擬環境，所以模擬器和雲端 Azure DocumentDB 帳戶之間會有一些功能上的差異：

* DocumentDB 模擬器僅支援單一固定帳戶及已知的主要金鑰。  在 DocumentDB 模擬器中無法重新產生金鑰。
* DocumentDB 模擬器服務無法擴充，也不支援大量集合。
* DocumentDB 模擬器不會模擬不同的 [DocumentDB 一致性層級](documentdb-consistency-levels.md)。
* DocumentDB 模擬器不會模擬[多重區域複寫](documentdb-distribute-data-globally.md)。
* DocumentDB 模擬器不支援 Azure DocumentDB 服務中可用的服務配額覆寫 (例如文件大小限制、增加的分割集合儲存體)。
* 因為 Azure DocumentDB 服務最近有變更，DocumentDB 模擬器複本可能不是最新狀態，請使用 [DocumentDB 容量規劃工具](https://www.documentdb.com/capacityplanner) ，準確地評估應用程式的生產輸送量 (RU) 需求。

## <a name="next-steps"></a>後續步驟
* 若要深入了解 DocumentDB，請參閱 [Azure DocumentDB 簡介](documentdb-introduction.md)
* 若要開始針對 DocumentDB 模擬器進行開發，請下載其中一個[支援的 DocumentDB SDK](documentdb-sdk-dotnet.md)。



<!--HONumber=Nov16_HO5-->


