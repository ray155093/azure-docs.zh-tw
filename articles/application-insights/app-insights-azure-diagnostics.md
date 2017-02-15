---
title: "將 Azure 診斷記錄傳送至 Application Insights | Microsoft Docs"
description: "設定 Azure 雲端服務診斷記錄的詳細資料，該記錄會傳送至 Application Insights 入口網站。"
services: application-insights
documentationcenter: .net
author: sbtron
manager: douge
ms.assetid: a67dd20a-fc5d-4391-ba63-bfe164fb62f7
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2843ff25c118afe5d5421b322b529e9a9f2d5036


---
# <a name="configure-azure-diagnostic-logging-to-application-insights"></a>設定 Azure 診斷以記錄至 Application Insights
當您在 Microsoft Azure 中設定雲端服務專案或虛擬機器時， [Azure 可以產生診斷記錄](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)。 您可以將此記錄傳送至 Application Insights，以便分析它與 Application Insights SDK 從應用程式內傳送的診斷和使用狀況遙測。 Azure 記錄檔會包含應用程式管理的事件，例如啟動、停止、當機，以及效能計數器。 記錄檔也會包含應用程式中對 System.Diagnostics.Trace 的呼叫。

本文詳細說明診斷擷取的組態。

您必須在 Visual Studio 中安裝 Azure SDK 2.8 或更新版本。

## <a name="get-an-application-insights-resource"></a>取得 Application Insights 資源
為了獲得最佳體驗，請[將 Application Insights SDK 新增至雲端服務應用程式的每個角色](app-insights-cloudservices.md)，或[新增至您將在 VM 中執行的任何應用程式](app-insights-overview.md)。 接著，您就可以傳送要在相同 Application Insights 資源中分析和顯示的診斷資料。

或者，如果不想使用 SDK - 例如，如果應用程式已經處於線上狀態 - 您只能在 Azure 入口網站中 [建立新的 Application Insights 資源](app-insights-create-new-resource.md) 。 選擇 **Azure 診斷** 做為應用程式類型。

## <a name="send-azure-diagnostics-to-application-insights"></a>將 Azure 診斷傳送至 Application Insights
如果您可以更新應用程式專案，則在 Visual Studio 中選取每個角色、選擇其屬性，並且在 [設定] 索引標籤中，選取 [將診斷傳送至 Application Insights] 。

或者，當您使用 [發行] 命令來上傳應用程式時，您也可以在 [診斷] 頁面上選擇 Application Insights 選項。

如果您的應用程式已經處於線上狀態，則使用 Visual Studio 的伺服器總管或雲端服務總管以開啟應用程式的內容。 選取 [傳送診斷至 Application Insights] 。

在每個案例中，您都會被詢問您建立的 Application Insights 資源的詳細資料。

建議您將個別角色的資料傳送到個別資源。 您可以透過建立[儀表板](app-insights-dashboards.md)，讓它們的度量圖表在入口網站中並排顯示。

[深入了解設定雲端服務應用程式的 Application Insights](app-insights-cloudservices.md)。

## <a name="configuring-the-azure-diagnostics-adapter"></a>設定 Azure 診斷配接器
只在您想要選取傳送至 Application Insights 的部分記錄檔時參閱。 根據預設，會傳送所有項目，包括：Microsoft Azure 事件、效能計數器、追蹤從應用程式對 System.Diagnostics.Trace 的呼叫。

當您在角色屬性編輯器或發行精靈中變更診斷選項時，實際上就是在變更兩組檔案的內容：

* 角色[診斷組態檔](https://msdn.microsoft.com/library/azure/dn782207.aspx)。 您可以在 [方案總管] 中的 `<Your Service>/Roles/*/diagnostics.wadcfgx` 底下找到它們
* 服務組態檔 `ServiceConfiguration.*.cscfg`。

為了影響比精靈所允許的還要更多的特定選項，請直接編輯這些檔案。 請繼續閱讀來取得更多詳細資料。 

## <a name="separate-development-and-production-resources"></a>區分開發和生產環境資源
您可以將來自應用程式之開發和生產環境戳記的遙測資料傳送給不同的 Application Insights 資源。 這可避免您的開發遙測被即時遙測淹沒。 

1. 為每個戳記[建立新的 Application Insights 資源](app-insights-create-new-resource.md)。 請從每個資源的 [程式集] 索引標籤中取得檢測金鑰。
2. 編輯兩個 .cscfg 檔案並插入不同的檢測金鑰。

## <a name="choose-the-priority-levels-to-send"></a>選擇傳送的優先順序層級
在每個角色的診斷組態檔 `diagnostics.wadcfgx` 中，您可以依層級篩選記錄訊息。

### <a name="define-a-sink"></a>定義接收器
`<SinksConfig>` 會定義可以傳送 Azure 診斷資料的其他接收器位置。  範例 `SinksConfig` 如下所示：

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights/>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig>

```

`Channels` 可指名將被傳送至接收器的資料。 通道的行為類似篩選器。 `loglevel` 屬性可讓您指定通道將會傳送的記錄層級。 可用值為： `{Verbose, Information, Warning, Error, Critical}`。

### <a name="send-data-to-the-sink"></a>將資料傳送到接收器
在 DiagnosticMonitorConfiguration 節點底下或其底下的任何節點新增接收器屬性，即可將資料傳送到 Application Insights 接收器。 將接收器元素新增至每個節點會指定您想要從該節點及其下的任何節點收集資料，並傳送到指定的接收器。

例如，由 Azure SDK 所建立的預設值是傳送所有的 Azure 診斷資料：

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

但是，如果您只想要傳送錯誤記錄檔，將接收器名稱限定為通道名稱：

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

請注意，我們使用我們所定義的接收器名稱，以及上面定義的通道名稱。

如果只想要傳送「詳細資訊」應用程式記錄檔至 Application Insights，則會將接收器屬性新增至 `Logs` 節點。

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

您也可以在階層的不同層級的組態中包含多個接收器。 在此情況下，在階層最上層指定的接收器會做為全域設定，而在個別元素指定的接收器則做為該全域設定的覆寫。

以下的公用組態檔完整範例會將所有錯誤傳送至 Application Insights (在 `DiagnosticMonitorConfiguration` 節點指定)，此外還有應用程式記錄檔的「詳細資訊」層級記錄 (在 `Logs` 節點指定)。

```xml

    <WadCfg>
     <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData">
       <!-- All info below sent to this channel -->
      <DiagnosticInfrastructureLogs />
      <PerformanceCounters>
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
        <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
      </PerformanceCounters>
      <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Application!*" />
      </WindowsEventLog>
      <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/>
       <!-- This specific info sent to this channel -->
     </DiagnosticMonitorConfiguration>

     <SinksConfig>
      <Sink name="ApplicationInsights">
        <ApplicationInsights/>
        <Channels>
          <Channel logLevel="Error" name="MyTopDiagData"  />
          <Channel logLevel="Verbose" name="MyLogData"  />
        </Channels>
      </Sink>
     </SinksConfig>
    </WadCfg>
```


對於這項功能有一些要注意的限制：

* 通道只是為了配合記錄類型 (而不是效能計數器) 使用。 如果您對效能計數器元素指定通道，將會忽略它。
* 通道的記錄層級不能超過 Azure 診斷所要收集的記錄層級。 例如：您不能在「記錄」元素中收集「應用程式記錄」錯誤，並且嘗試傳送「詳細資訊」記錄至 Application Insight 同步處理。 scheduledTransferLogLevelFilter 屬性一律必須收集與您正嘗試傳送到接收器的記錄相等或更多個記錄。
* 您無法將 Azure 診斷擴充收集的任何 blob 資料傳送至 Application Insights。 例如，目錄節點下指定的任何項目。 針對損毀傾印，實際損毀傾印將仍可傳送至 blob 儲存體，並只會將損毀傾印所產生的通知傳送至 Application Insights。

## <a name="next-steps"></a>後續步驟
* [使用 Application Insights 監視 Azure 雲端服務](app-insights-cloudservices.md)
* [使用 PowerShell 將 Azure 診斷傳送至 Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Azure 診斷組態檔](https://msdn.microsoft.com/library/azure/dn782207.aspx)




<!--HONumber=Nov16_HO3-->


