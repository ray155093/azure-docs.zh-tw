<properties
   pageTitle="如何利用 Linux Azure 診斷收集記錄檔 | Microsoft Azure"
   description="本文將說明如何設定 Azure 診斷，以便從 Azure 中執行的 Service Fabric Linux 叢集收集記錄檔。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# 如何利用 Azure 診斷收集記錄檔

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

當您執行 Azure Service Fabric 叢集時，最好從中央位置的所有節點收集記錄檔。只要將記錄檔集中在一個位置，不論問題發生在服務、應用程式或叢集本身，都能輕鬆分析問題並進行疑難排解。上傳和收集記錄檔的方式之一是使用可將記錄檔上傳至 Azure 儲存體的 Azure 診斷延伸模組。您可以讀取儲存體的事件，並將它們放在 [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) 之類的產品或其他記錄檔剖析方案中。

## 您可能想要收集的不同記錄來源
1. **Service Fabric 記錄檔︰**由平台使用 LTTng 所發出並上傳至儲存體帳戶。記錄檔可能是操作事件或平台所發出的執行階段事件。這些記錄檔儲存在叢集資訊清單中所指向的位置 (搜尋標記 “AzureTableWinFabETWQueryable”，然後尋找 “StoreConnectionString”，以取得儲存體帳戶詳細資料。
2. **應用程式事件︰**服務程式碼所發出的事件。您可以使用任何會寫入文字型記錄檔的記錄解決方案，例如 [LTTng](http://lttng.org)。請參閱 LTTng 文件來追蹤您的應用程式。


## 部署診斷延伸模組
收集記錄檔的第一個步驟是將診斷延伸模組部署在 Service Fabric 叢集的每個 WM 上。診斷延伸模組會收集每個 VM 上的記錄檔，並將它們上傳至您指定的儲存體帳戶。步驟視您使用 Azure 入口網站或 Azure Resource Manager 而有所不同。

### 在建立叢集過程中部署診斷延伸模組 
若要在建立叢集過程中將診斷延伸模組部署至叢集中的 VM，請將 [診斷] 設為 [開啟]。在建立叢集之後，無法使用入口網站來變更此設定。

設定 Linux Azure 診斷 (LAD) 來收集檔案，並將它們放入客戶儲存體帳戶中。[使用 LAD 來監視和診斷 Linux VM](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md)一文中的案例 3 (「上傳您自己的記錄檔」) 說明這個程序。遵循此程序可讓您存取追蹤，而這些追蹤可上傳至您所選擇的視覺化檢視。


您也可以使用 Azure Resource Manager 來部署診斷延伸模組。此程序在 Windows 及 Linux 上很類似，而[如何利用 Azure 診斷收集記錄檔](service-fabric-diagnostics-how-to-setup-wad.md)一文也以 Windows 叢集為主提供相關說明。

您也可以使用 OMS，如 [OMS 記錄分析與 linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/)所述。

完成此設定之後，LAD 代理程式就會監視指定的記錄檔。每當新的一行附加至檔案時，就會建立 syslog 項目來傳送至客戶所指定的儲存體。


## 後續步驟
請參閱 [LTTng 文件](http://lttng.org/docs)和[使用 LAD](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md)，以更仔細了解您在進行問題的疑難排解時應該調查哪些事件。

<!---HONumber=AcomDC_0928_2016-->