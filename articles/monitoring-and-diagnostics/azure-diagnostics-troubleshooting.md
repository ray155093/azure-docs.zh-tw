---
title: "針對 Azure 診斷進行疑難排解 | Microsoft Docs"
description: "針對在 Azure 虛擬機器、Service Fabric 或 雲端服務中使用 Azure 診斷時出現的問題進行疑難排解。"
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 81f9a5a8e5ce8389c12a40eb02dd554fc140e009
ms.contentlocale: zh-tw
ms.lasthandoff: 03/22/2017


---
# <a name="azure-diagnostics-troubleshooting"></a>Azure 診斷疑難排解
有關使用 Azure 診斷的疑難排解資訊。 如需有關 Azure 診斷的詳細資訊，請參閱 [Azure 診斷概觀](azure-diagnostics.md)。

## <a name="azure-diagnostics-is-not-starting"></a>Azure 診斷未啟動
診斷是由兩個元件所組成：客體代理程式外掛程式和監視代理程式。 您可以檢查記錄檔 **DiagnosticsPluginLauncher.log** 和 **DiagnosticsPlugin.log**，以取得診斷為何無法啟動的相關資訊。  

在雲端服務角色中，客體代理程式外掛程式的記錄檔位於下列：

```
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.7.4.0\
```

在 Azure 虛擬機器中，客體代理程式外掛程式的記錄檔位於下列：

```
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.7.4.0\Logs\
```

記錄檔的最後一行包含結束代碼。  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```

外掛程式會傳回下列結束代碼：

| 結束代碼 | 說明 |
| --- | --- |
| 0 |成功。 |
| -1 |一般錯誤。 |
| -2 |無法載入 rcf 檔。<p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。 |
| -3 |無法載入診斷組態檔。<p><p>解決方法：因組態檔未通過結構描述驗證而造成。 解決方法是提供以結構描述編譯的組態檔。 |
| -4 |診斷之監視代理程式的另一個執行個體已在使用本機資源目錄。<p><p>解決方法：為 **LocalResourceDirectory** 指定其他值。 |
| -6 |客體代理程式外掛程式啟動器嘗試使用無效的命令列來啟動診斷。<p><p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。 |
| -10 |診斷外掛程式結束並發生未處理的例外狀況。 |
| -11 |客體代理程式無法建立負責啟動及監視監視代理程式的處理序。<p><p>解決方法：確認有足夠的系統資源，可啟動新的處理序。<p> |
| -101 |呼叫診斷外掛程式時的引數無效。<p><p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。 |
| -102 |外掛程式處理序無法對本身進行初始化。<p><p>解決方法：確認有足夠的系統資源，可啟動新的處理序。 |
| -103 |外掛程式處理序無法對本身進行初始化。 具體而言，其無法建立記錄器物件。<p><p>解決方法：確認有足夠的系統資源，可啟動新的處理序。 |
| -104 |無法載入客體代理程式提供的 rcf 檔。<p><p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。 |
| -105 |診斷外掛程式無法開啟診斷組態檔。<p><p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用診斷外掛程式時才會發生。 |
| -106 |無法讀取診斷組態檔。<p><p>解決方法：因組態檔未通過結構描述驗證而造成。 因此，解決方法是提供以結構描述編譯的組態檔。 對於 Azure 虛擬機器，您可以在 VM 上的 *%SystemDrive%\WindowsAzure\Config* 資料夾中，找到傳送給診斷擴充功能的組態。 開啟適當的 XML 檔並搜尋 **Microsoft.Azure.Diagnostics**，然後搜尋 **xmlCfg** 或 **WadCfg** 欄位。 如果出現 WadCfg 欄位，表示組態在 JSON 中。 如果出現 xmlCfg 欄位，表示組態在 XML 中且以 base64 編碼。 您必須將它解碼，才能查看診斷載入的 XML。 對於雲端服務角色，您可以在 VM 上的 C:\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.7.4.0\config.txt，找到傳遞給診斷擴充功能的組態。 該資料是以 base64 編碼，因此您將需要進行[解碼](http://www.bing.com/search?q=base64+decoder)，才能查看診斷載入的 XML。<p> |
| -107 |傳遞給監視代理程式的資源目錄無效。<p><p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用監視代理程式時才會發生。</p> |
| -108 |無法將診斷組態檔轉換成監視代理程式組態檔。<p><p>此內部錯誤應只有當使用無效的組態檔以手動方式叫用診斷外掛程式時才會發生。 |
| -110 |一般診斷組態錯誤。<p><p>此內部錯誤應只有當使用無效的組態檔以手動方式叫用診斷外掛程式時才會發生。 |
| -111 |無法啟動監視代理程式。<p><p>解決方法：確認有足夠的系統資源可用。 |
| -112 |一般錯誤 |

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>診斷資料未記錄至 Azure 儲存體
Azure 診斷會將所有資料儲存至 Azure 儲存體。

遺漏事件資料的最常見原因是不正確地定義儲存體帳戶資訊。

解決方法：更正診斷組態檔，並重新安裝診斷。
如果在重新安裝診斷擴充功能後仍有問題，則您可能要進一步偵錯，做法為：仔細檢查監視代理程式的任何錯誤。 將事件資料上傳至您的儲存體帳戶之前，資料是儲存在 LocalResourceDirectory 中。
這個目錄路徑會視情況而異。

對於雲端服務角色：

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

對於虛擬機器：

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

如果 LocalResourceDirectory 資料夾中沒有檔案，則監視代理程式將無法啟動。 這種情況通常是由於無效的組態檔所造成，即應在 CommandExecution.log 中回報的事件。

如果監視代理程式成功收集事件資料，您會看到組態檔中所定義之每個事件的 .tsf 檔案。 監視代理程式會在 MaEventTable.tsf 檔案中記錄錯誤。 若要檢查此檔案的內容，您可以使用 tabel2csv 應用程式，將 .tsf 檔案轉換為逗號分隔 (.csv) 值檔案：

在雲端服務角色上：

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*%SystemDrive%* 通常為是 D：

在虛擬機器上：

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

先前的命令會產生記錄檔 *maeventtable.csv*，您可以開啟檔案並檢查失敗的訊息。    

## <a name="diagnostics-data-tables-not-found"></a>找不到診斷資料資料表
在內含 Azure 診斷資料的 Azure 儲存體中，資料表是使用下列程式碼命名：

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

下列是一個範例：

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```

產生的 4 個資料表如下︰

| Event | 資料表名稱 |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

