---
title: "在 Linux 上的 Web Apps 中使用適用於 Node.js 的 PM2 組態 | Microsoft Docs"
description: "在 Linux 上的 Web Apps 中使用適用於 Node.js 的 PM2 組態"
keywords: "azure app service, web 應用程式, nodejs, pm2, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: fb420f32-6d74-49c7-992f-0ed5616e66e7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a46f52198e956406c2384c45ed4323aff27259a0


---
# <a name="use-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>在 Linux 上的 Web Apps 中使用適用於 Node.js 的 PM2 組態
針對 Linux 上的 Web Apps ，如果您將應用程式堆疊設定為 Node.js，則可以選擇設定 Node.js 啟動檔案，如下圖所示。

![設定 Node.js 啟動檔案][1]

您可以使用此項目來執行下列工作︰

* 指定 Node.js 應用程式的啟動指令碼 (例如︰/bin/server.js)。
* 指定要用於 Node.js 應用程式的 PM2 組態檔 (例如︰/foo/process.json)。
  
  > [!NOTE]
  > 如果您想要在修改某些檔案時自動重新啟動您的 Node.js 程序，請使用 PM2 組態。 否則，應用程式收到變更通知時不會重新啟動 (例如，當應用程式的程式碼變更時)。
  > 
  > 

您可以在 Node.js [處理檔案文件](http://pm2.keymetrics.io/docs/usage/application-declaration/)中查看所有選項，以下只是可作為 process.json 檔案的範例︰

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

此組態中的重要注意事項如下︰

* "script" 屬性指定應用程式的啟動指令碼。
* "instances" 屬性指定節點程序要啟動的執行個體數目。 如果您在有多個核心的較大 VM 上執行應用程式，最好設定較高的值，以充分運用資源。
* "watch" 陣列指定造成您想要重新啟動節點程序的所有檔案 (當它們變更時)。
* 針對 "watch_options"，由於應用程式內容的裝載方式，您目前需要將 "usePolling" 指定為 true。

## <a name="next-steps"></a>後續步驟
* [何謂 Linux 上的 App Service？](app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png



<!--HONumber=Nov16_HO3-->


