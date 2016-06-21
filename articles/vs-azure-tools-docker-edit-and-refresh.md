<properties
   pageTitle="偵錯本機 Docker 容器中的應用程式 | Microsoft Azure"
   description="了解如何透過「編輯和重新整理」來修改在本機 Docker 容器中執行的應用程式、重新整理容器，以及設定偵錯中斷點"
   services="visual-studio-online"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# 偵錯本機 Docker 容器中的應用程式

## 概觀
Visual Studio Tools for Docker 提供一致的方式，在 Linux Docker 容器中本機開發和驗證您的應用程式。您不需要每次變更程式碼之後都重新啟動容器。本文章將說明如何使用「編輯及重新整理」功能在本機 Docker 容器中啟動 ASP.NET Core Web 應用程式、進行任何必要的變更，然後重新整理瀏覽器來查看這些變更。它也會示範如何設定中斷點來進行偵錯。

> [AZURE.NOTE] 未來版本將支援 Windows 容器

## 必要條件
您的電腦必須安裝下列工具。

- [Visual Studio 2015 Update 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- [Microsoft ASP .NET Core RC 2](http://go.microsoft.com/fwlink/?LinkId=798481)
- [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)

若要在本機執行 Docker 容器，您需要本機 Docker 用戶端。您可以使用需要停用 Hyper-V 的已發行 [Docker 工具箱](https://www.docker.com/products/overview#/docker_toolbox)，或是使用利用 Hyper-V 並需要 Windows 10 的 [Docker for Windows Beta](https://beta.docker.com)。

如果使用 Docker 工具箱，您將需要[設定 Docker 用戶端](./vs-azure-tools-docker-setup.md)

## 編輯在本機 Docker 容器中執行的應用程式
Visual Studio 2015 Tools for Docker 可讓 ASP .NET Core RC2 Web 應用程式開發人員在 Docker 容器中測試及執行自己的應用程式、在 Visual Studio 應用程式中變更應用程式，以及重新整理瀏覽器來查看已套用至容器中執行之應用程式的變更。運用 .NET Core 和 Visual Studio Tools for Docker 0.20 版，您也可以設定與 Docker 容器一起執行的程式碼的中斷點。

1. 從 Visual Studio 功能表中，選取 [檔案] > [新增] > [專案]。

1. 在 [新增專案] 對話方塊的 [範本] 區段下，選取 [Visual C#] > [Web]。

1. 選取 [ASP.NET Core Web 應用程式 (.NET Core)]。

1. 指定新應用程式的名稱 (或使用預設值)，然後點選 [確定]。

1. 在 [ASP.NET Core 範本] 下，選取 [Web 應用程式]，然後點選 [確定]。

1. 使用 Docker 做為您的部署解決方案時，請取消選取 [雲端中的主機]。

1. 在 [Visual Studio 方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [加入] > [Docker 支援]。

	![][0]

1. 專案節點下方隨即會建立下列檔案：

	![][1]

> [AZURE.NOTE] 如果使用 [Docker for Windows Beta](https://beta.docker.com)，請開啟 [屬性\\Docker.props] 並移除預設值，然後重新啟動 Visaul Studio，這樣值才會生效。
>
> ![][2]

##編輯和重新整理
若要快速反覆查看變更，您可以啟動容器內的應用程式，並繼續進行變更，而檢視變更的方式與使用 IIS Express 相同。

1. 將 [方案組態] 設定為 `Debug`，然後按 **&lt;CTRL + F5>** 來建置 Docker 映像，然後在本機執行此映像。

    建置好容器映像並正在 Docker 容器中執行之後，Visual Studio 會在您的預設瀏覽器中啟動 Web 應用程式。如果您是使用 Microsoft Edge 瀏覽器或者發生錯誤，請參閱[疑難排解](vs-azure-tools-docker-troubleshooting-docker-errors.md)一節。

1. 前往「關於」頁面，這是我們要在其中進行變更的頁面。

1. 返回 Visual Studio，然後開啟 `Views\Home\About.cshtml`。

1. 將下列 HTML 內容新增到檔案結尾，然後儲存變更。

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	檢視輸出視窗，當 .NET 組建完成且您看到這些行時，切換回您的瀏覽器，然後重新整理「關於」頁面。

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.	您的變更已經套用！

##中斷點偵錯
通常，需要運用 Visual Studio 的偵錯功能進一步檢查變更。

1.	返回 Visual Studio，然後開啟 `Controllers\HomeController.cs`

1.  將 About() 方法的內容取代為下列內容：

	```
	string message = "Your application description page from wthin a Container";
	ViewData["Message"] = message;
    ````

1.  在 `string message`... 行的左側設定中斷點。

1.  按 **&lt;F5>** 來開始偵錯。

1.  瀏覽至 [關於] 頁面，來叫用中斷點。

1.  切換至 Visual Studio 來檢視中斷點，然後檢查訊息的值。

	![][3]

##摘要
運用 [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)，您將可獲得在本機運作的生產力，以及在 Docker 容器內開發的生產環境真實性。

## 疑難排解
[疑難排解 Visual Studio Docker 開發](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## 進一步了解 Docker 與 Visual Studio、Windows 和 Azure

- [Docker Tools for Visual Studio](http://aka.ms/dockertoolsforvs) - 在容器中開發 .NET Core 程式碼
- [Docker Tools for Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) - 建置和部署 Docker 容器
- [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode) - 用於編輯 Docker 檔案的語言服務，將推出更多其他 e2e 案例
- [Windows 容器資訊](http://aka.ms/containers) - Windows Server 和 Nano Server 資訊
- [Azure 容器服務](https://azure.microsoft.com/services/container-service/) - [Azure 容器服務內容](http://aka.ms/AzureContainerService)

## 各種 Docker 工具

[一些不錯的 Docker 工具 (Steve Lasker 的部落格)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## 不錯的文章

[NGINX 的微服務簡介](https://www.nginx.com/blog/introduction-to-microservices/)

## 簡報

- [Steve Lasker：VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [ASP.NET Core @ build 2016 簡介 - 示範](https://channel9.msdn.com/Events/Build/2016/B810)
- [開發容器中的 .NET 應用程式 (Channel 9)](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png
[2]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-props.png
[3]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0608_2016-->