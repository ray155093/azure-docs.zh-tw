---
title: "串流記錄和主控台"
description: "串流記錄和主控台概觀"
author: btardif
manager: wpickett
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3e50a287-781b-4c6a-8c53-eec261889d7a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7704270d7bba54577f61f3f349dc69dc6ddf08c6


---
# <a name="streaming-logs-and-the-console"></a>串流記錄和主控台
## <a name="streaming-logs"></a>串流記錄
**Azure 入口網站**提供整合式串流記錄檢視器，可讓您從 **App Service** 應用程式即時檢視追蹤事件。  

設定此功能只需要一些簡單的步驟：

* 在程式碼中撰寫追蹤
* 啟用您應用程式的應用程式**診斷記錄**
* 在 **Azure 入口網站**中檢視來自內建**串流記錄** UI 的串流。

### <a name="how-to-write-traces-in-your-code"></a>如何在程式碼中撰寫追蹤
在程式碼中撰寫追蹤很簡單。  在 C# 中可輕易撰寫下列程式碼：

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

Trace 類別位於 System.Diagnostics 命名空間。

您可以在 node.js 應用程式中，撰寫此程式碼以取得相同的結果：

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>如何啟用和檢視串流記錄
![][BrowseSitesScreenshot] 診斷會根據應用程式啟用。 開始瀏覽至您想要啟用這項功能的網站。  

![][DiagnosticsLogs] 從 [設定] 功能表中，向下捲動至 [監視] 區段，然後按一下 [(1) 診斷記錄]。 然後 [(2) 啟用] **應用程式記錄 (檔案系統)** 或**應用程式記錄 (Blob)**。[層級] 選項可讓您變更要擷取之追蹤的嚴重性層級。 如果您才剛嘗試熟悉此功能，將層級設定為 [詳細資訊]，以確保收集所有追蹤陳述式。

按一下刀鋒頂端的 [儲存]  ，隨後可準備檢視記錄。

> [!NOTE]
> **嚴重性層級**愈高，系統會耗用更多的資源進行記錄，且會產生更多追蹤。 請確定**嚴重性層級**設定為正確的生產詳細資訊或高流量網站。 
> 
> 

![][StreamingLogsScreenshot] 若要檢視 Azure 入口網站內的**串流記錄**，按一下 [(1) 記錄串流] 以及 [設定] 功能表的 [監視] 區段。 如果您的應用程式主動撰寫追蹤陳述式，則您應在 **(2) 資料流記錄 UI** 中近乎即時地看到這些追蹤陳述式。

## <a name="console"></a>主控台
**Azure 入口網站**提供能夠存取您 Web 應用程式環境的主控台。 您可以探索應用程式的檔案系統並執行 powershell/cmd 指令碼。 執行主控台命令時，您會受到與執行應用程式程式碼之相同權限集的限制。 已封鎖存取受保護的目錄或執行需要提高權限的指令碼。  

![][ConsoleScreenshot] 從 [設定] 功能表中，向下捲動至 [開發工具] 區段，然後按一下 [(1) 主控台] 則 [(2) 主控台] UI 會在右方開啟。

若要熟悉**主控台**，請嘗試類似的基本命令：

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png



<!--HONumber=Nov16_HO3-->


