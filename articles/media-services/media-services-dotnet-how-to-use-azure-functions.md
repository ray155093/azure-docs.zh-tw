---
title: "開發具有媒體服務的 Azure Functions"
description: "本主題說明如何使用 Azure 入口網站開始開發具有媒體服務的 Azure Functions。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b077504e7a289fd4976aa22bab0ad5784ffb491b
ms.lasthandoff: 03/15/2017


---
#<a name="develop-azure-functions-with-media-services"></a>開發具有媒體服務的 Azure Functions
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

本主題討論如何使用 Azure 入口網站開始開發具有媒體服務的 Azure Functions。 

您也可以藉由按一下 [部署至 Azure] 按鈕，從[這裡](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)部署現有的媒體服務 Azure Functions。 此存放庫包含 Azure Functions 範例，這些範例使用 Azure 媒體服務來顯示與直接從 Blob 儲存體擷取內容、進行編碼，再將內容寫回 Blob 儲存體相關的工作流程。 此存放庫也包含如何透過 Webhook 和 Azure 佇列監視作業通知的範例。

您可以根據[這個](https://github.com/Azure-Samples/media-services-dotnet-Functions-integration)存放庫中的範例開發您的 Functions。 本主題說明如何開始建立使用媒體服務的 Azure Functions。 

## <a name="prerequisites"></a>必要條件

您必須先具備有效的 Azure 帳戶，才可以建立第一個函式。 如果您還沒有 Azure 帳戶， [可以使用免費帳戶](https://azure.microsoft.com/free/)。

如果您要建立會對 Azure 媒體服務 (AMS) 帳戶執行動作或是會接聽媒體服務所傳送之事件的 Azure Functions，您應該建立 AMS 帳戶，如[這裡](media-services-portal-create-account.md)所述。

## <a name="create-a-function-app"></a>建立函數應用程式

如[這裡](../azure-functions/functions-create-first-azure-function-azure-portal.md#create-a-function-app)所述建立函式應用程式。

## <a name="create-a-function"></a>建立函式

部署應用程式函式後，您可以在 **App Services** Azure Functions 之中找到它。 

1. 選取您的函式應用程式，然後按一下 [新增函式]。
3. 選擇 [C#] 語言和 [Webhook + API] 案例。
3. 選取 [GenericWebHook-CSharp] (每當它收到 Webhook 要求時便會執行) 或 [HttpTrigger-CSharp] (每當它收到 HTTP 要求時便會執行)，並為您的函式命名。
4. 按一下 [建立] 。 

## <a name="get-function-url"></a>取得函式 URL

若要從 HTTP 測試工具或從另一個瀏覽器視窗觸發函式的執行，您需要函式 URL 值。 

![設定](./media/media-services-azure-functions/media-services-azure-functions002.png)

## <a name="files"></a>檔案

您的 Azure 函式會與本節所述的程式碼檔案和其他檔案相關聯。 根據預設，函式會與 **function.json** 和 **run.csx** 檔案相關聯。 您必須新增 **project.json** 檔案。 本節其餘部分會說明這些檔案的定義。

![檔案](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

function.json 檔案會定義函式繫結和其他組態設定。 執行階段使用此檔案來判斷要監視的事件，以及如何傳入資料並從函式執行傳回資料。 

以下是 **function.json** 檔案的範例。

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [
        "post",
        "get",
        "put",
        "update",
        "patch"
          ]
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }
    
### <a name="projectjson"></a>project.json

project.json 檔案包含相依性。 以下是包含 AMS 程式庫的 **function.json**檔案範例。

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

這是您的函式適用的 C# 程式碼。 如需 Webhook 函式的範例，請參閱[這個](media-services-dotnet-check-job-progress-with-webhooks.md)主題。 

定義好函式之後，按一下 [執行]。
    
    ///////////////////////////////////////////////////
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using System.Globalization;
    using Newtonsoft.Json;
    using Microsoft.Azure;
    using System.Net;
    using System.Security.Cryptography;
    

    static string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    static string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");
    
    static CloudMediaContext _context = null;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
        byte[] requestBody = await taskForRequestBody;
    
        string jsonContent = await req.Content.ReadAsStringAsync();
        log.Info($"Request Body = {jsonContent}");
    
        // some valication code ...

        _context = new CloudMediaContext(new MediaServicesCredentials(
        _mediaServicesAccountName,
        _mediaServicesAccountKey));

        // some AMS operations ...
      
        return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
    }



## <a name="configure-function-app-settings"></a>設定函式應用程式設定

在開發媒體服務函式時，您可以很方便地在 [應用程式設定] 區段中新增會在整個函式中使用的參數。 

例如：

![設定](./media/media-services-azure-functions/media-services-azure-functions001.png)


## <a name="next-step"></a>後續步驟

現在，您可以開始開發媒體服務應用程式。 如需詳細資訊，請參閱[使用 Azure WebHook 監視 .NET 的媒體服務作業通知](media-services-dotnet-check-job-progress-with-webhooks.md)。   

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


