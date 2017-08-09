---
title: "在 Linux 上的 Web 應用程式中使用 .NET Core | Microsoft Docs"
description: "在 Linux 上的 Web 應用程式中使用 .NET Core。"
keywords: "azure app service, Web 應用程式, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
authors: michimune, rachelappel
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: aelnably;wesmc;mikono;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: cbcc43e04e58894b6505fdf6c48c7792343049b9
ms.contentlocale: zh-tw
ms.lasthandoff: 07/26/2017

---

# <a name="use-net-core-in-an-azure-web-app-on-linux"></a>在 Linux 上的 Azure Web 應用程式中使用 .NET Core #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

Linux 上的 [Web 應用程式](https://docs.microsoft.com/azure/app-service-web/app-service-linux-intro)使用 Linux 作業系統提供可高度擴充、自我修復的 Web 主機服務。 本教學課程包含逐步指示，說明如何在 Linux 上的 Azure web 應用程式上建立 [.NET Core](https://docs.microsoft.com/aspnet/core/) 應用程式。 

![Linux 上的 Web 應用程式][10]

您可以使用 Mac、Windows 或 Linux 電腦，依照下面步驟操作。

## <a name="prerequisites"></a>必要條件 ##

若要完成本教學課程： 

* 安裝 [.NET Core SDK](https://www.microsoft.com/net/download/core)。
* 安裝 [Git](https://git-scm.com/downloads)。

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-local-net-core-application"></a>建立本機 .NET Core 應用程式 ##

啟動新的終端機工作階段。 建立名為 `hellodotnetcore` 的目錄，並將目前的目錄變更為該目錄。 接著輸入下列內容： 

```
dotnet new web
``` 

  此命令會建立三個檔案 (hellodotnetcore.csproj、Program.cs 和 Startup.cs)，以及在目前的目錄下建立一個空資料夾 (wwwroot/)。 `.csproj` 檔案的內容應如下所示： 

```xml
  <!-- Empty lines are omitted. -->

  <Project Sdk="Microsoft.NET.Sdk.Web">
        <PropertyGroup>
        <TargetFramework>netcoreapp1.1</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
        <Folder Include="wwwroot\" />
        </ItemGroup>
        <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore" Version="1.1.2" />
        </ItemGroup>
  </Project>
```

由於此應用程式是 Web 應用程式，所以 ASP.NET Core 套件的參考會自動新增至 hellodotnetcore.csproj 檔案。 系統會根據所選的架構設定套件的版本號碼。 因為使用 .NET Core 1.1，所以此範例會參考 ASP.NET Core 1.1.2 版。

## <a name="build-and-test-the-application-locally"></a>在本機建置和測試應用程式 ##

您可以使用 `dotnet restore` 命令後面接著 `dotnet run` 命令，建置及執行 .NET Core 應用程式，如下所示：

```
dotnet restore
dotnet run
```


當應用程式啟動時，它會顯示一則訊息，指出應用程式正在連接埠接聽連入要求。 

```bash
Hosting environment: Production
Content root path: C:\hellodotnetcore
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

測試瀏覽至`http://localhost:5000/`與瀏覽器。 如果一切運作正常，您會看到 "Hello World!" 作為結果文字。

![使用瀏覽器測試][7]

## <a name="create-a-net-core-app-in-the-azure-portal"></a>在 Azure 入口網站中建立 .NET Core 應用程式 ##

首先，您必須建立空的 Web 應用程式。 登入 [Azure 入口網站](https://portal.azure.com/)，並[在 Linux 上建立新的 Web 應用程式](https://portal.azure.com/#create/Microsoft.AppSvcLinux)。

![建立 Web 應用程式][1]

當 [建立] 頁面開啟時，提供您的 Web 應用程式詳細資料：

![選擇 .NET Core 執行階段堆疊][2]

使用下表作為指南來填寫 [建立] 頁面，然後選取 [確定] 和 [建立] 來建立應用程式。

| 設定      | 建議的值  | 說明                                        |
| ------------ | ---------------- | -------------------------------------------------- |
| 應用程式名稱 | hellodotnetcore  | 您的應用程式名稱。 此名稱必須是唯一的。 |
| 訂用帳戶 | 選擇現有的訂用帳戶 | Azure 訂用帳戶。 |
| 資源群組 | myResourceGroup |  要包含 Web 應用程式的 Azue 資源群組。 |
| App Service 方案 | 現有的 App Service 方案名稱 |  App Service 方案。  |
| 設定容器 | .NET Core 1.1 | 此 Web 應用程式的容器類型：內建、Docker 或私人登錄。 |
| 映像來源  | 內建  |  映像的來源。 |
| 執行階段堆疊  | .NET Core 1.1  | 執行階段堆疊和版本。  |

## <a name="deploy-your-application-via-git"></a>透過 Get 部署應用程式 ##

使用 Git，在 Linux 上將 .NET Core 應用程式部署至 Azure App Service Web App。

新的 Azure Web 應用程式已設定 Git 部署。 將您的 Web 應用程式名稱插入後，瀏覽至下列 URL 就可以找到 Git 部署 URL︰

```https://{your web app name}.scm.azurewebsites.net/api/scm/info```

根據您的 web 應用程式名稱，Git URL 具有下列格式︰

```https://{your web app name}.scm.azurewebsites.net/{your web app name}.git```

執行下列命令，將本機應用程式部署至您的 Azure Web 應用程式： 
 
```bash
git init
git remote add azure <Git deployment URL from above>
git add *.csproj *.cs
git commit -m "Initial deployment commit"
git push azure master
```

您不需要推送 *bin /* 或 *obj/*目錄之下的任何檔案，因為當應用程式的原始程式檔推送至 Azure 時，會在雲端建置應用程式。 建置程序完成之後，二進位檔案會複製到應用程式的目錄：/home/site/wwwroot/。

確認遠端部署作業報告成功。 推送作業可能需要一些時間，因為套件解決方案和建置程序會在雲端中執行。 您會看到數則狀態訊息，包括陳述已複製檔案的訊息。 輸出應如下所示：

```bash
/* some output has been removed for brevity */
remote: Copying file: 'System.Net.Websockets.dll' 
remote: Copying file: 'System.Runtime.CompilerServices.Unsafe.dll' 
remote: Copying file: 'System.Runtime.Serialization.Primitives.dll' 
remote: Copying file: 'System.Text.Encodings.Web.dll' 
remote: Copying file: 'hellodotnetcore.deps.json' 
remote: Copying file: 'hellodotnetcore.dll' 
remote: Omitting next output lines...
remote: Finished successfully.
remote: Running post deployment commands...
remote: Deployment successful.
To https://hellodotnetcore.scm.azurewebsites.net/
 * [new branch]           master -> master

```

一旦部署完成後，需重新啟動 Web 應用程式進行部署，才會生效。 若要這麼做，請移至 Azure 入口網站並巡覽至您 Web 應用程式的 [概觀] 頁面。 選取頁面中的 [重新啟動] 按鈕。 快顯視窗出現時，選取 [是] 進行確認。 您可以瀏覽您的 Web 應用程式，如下所示：

![在 Linux 上瀏覽已部署至 Azure App Service 的 .NET Core 應用程式][10]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟
* [Linux 上的 Azure App Service Web 應用程式常見問題集](./app-service-linux-faq.md)

[1]: ./media/app-service-linux-using-dotnetcore/top-level-create.png
[2]: ./media/app-service-linux-using-dotnetcore/dotnet-new-webapp.png
[7]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-local.png
[10]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-azure.png

