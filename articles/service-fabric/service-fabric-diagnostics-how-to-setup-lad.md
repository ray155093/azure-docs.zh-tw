<properties
   pageTitle="使用 Linux Azure 診斷收集記錄檔 | Microsoft Azure"
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



# <a name="collect-logs-by-using-azure-diagnostics"></a>使用 Azure 診斷收集記錄檔

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

當您執行 Azure Service Fabric 叢集時，最好從中央位置的所有節點收集記錄檔。 只要將記錄檔集中在一個位置，不論問題發生在服務、應用程式或叢集本身，都能輕鬆分析問題並進行疑難排解。 上傳和收集記錄檔的方式之一是使用可將記錄檔上傳至 Azure 儲存體的 Azure 診斷擴充功能。 您可以從儲存體讀取事件，並將它們放在 [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) 之類的產品或其他記錄檔剖析解決方案中。

## <a name="log-sources-that-you-might-want-to-collect"></a>您可能想要收集的記錄來源
- **Service Fabric 記錄檔**︰由平台透過 [LTTng](http://lttng.org) 所發出並上傳至儲存體帳戶。 記錄檔可能是平台所發出的操作事件或執行階段事件。 這些記錄檔會儲存在叢集資訊清單所指定的位置。 (若要取得儲存體帳戶的詳細資訊，請搜尋 **AzureTableWinFabETWQueryable** 標籤並尋找 **StoreConnectionString**)。
- **應用程式事件**：從服務程式碼發出。 您可以使用任何撰寫文字型記錄檔的記錄解決方案，例如 LTTng。 如需詳細資訊，請參閱 LTTng 文件中關於追蹤您應用程式的內容。  


## <a name="deploy-the-diagnostics-extension"></a>部署診斷擴充功能
收集記錄檔的第一個步驟是將診斷擴充功能部署在 Service Fabric 叢集的每個 WM 上。 診斷擴充功能會收集每個 VM 上的記錄檔，並將它們上傳至您指定的儲存體帳戶。 步驟視您使用 Azure 入口網站或 Azure Resource Manager 而有所不同。

若要在建立叢集過程中將診斷擴充功能部署至叢集中的 VM，請將 [診斷] 設為 [開啟]。 建立叢集之後，您就無法使用入口網站變更這項設定。

接著，設定 Linux Azure 診斷 (LAD) 來收集檔案，並將它們放入您的儲存體帳戶中。 [使用 LAD 監視 Linux VM 的效能和診斷資料](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md)一文中的案例 3 (＜上傳您自己的記錄檔＞) 將會說明這個程序。 遵循這個程序就可以存取追蹤。 您可以將追蹤上傳到所選擇的視覺化檢視。

您也可以使用 Azure Resource Manager 來部署診斷擴充功能。 此程序在 Windows 及 Linux 上都很類似，而[如何利用 Azure 診斷收集記錄檔](service-fabric-diagnostics-how-to-setup-wad.md)中將針對 Windows 叢集提供相關說明。

您也可以使用 Operations Management Suite，如 [Operations Management Suite Log Analytics with Linux (搭配 Linux 使用 Operations Management Suite Log Analytics)](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/) 中所述。

完成此設定之後，LAD 代理程式就會監視指定的記錄檔。 每當新的一行附加至檔案時，系統就會建立 syslog 項目並傳送至您所指定的儲存體。


## <a name="next-steps"></a>後續步驟
若要更仔細了解您在進行問題的疑難排解時應該調查哪些事件，請參閱 [LTTng 文件](http://lttng.org/docs)和[使用 LAD](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md)。



<!--HONumber=Oct16_HO2-->


