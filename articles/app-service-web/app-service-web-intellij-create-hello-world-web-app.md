<properties 
	pageTitle="在 IntelliJ 中建立 Azure 的 Hello World Web 應用程式 | Microsoft Azure" 
	description="本教學課程將示範如何使用適用於 IntelliJ 的 Azure 工具組來建立 Azure 的 Hello World Web 應用程式。" 
	services="app-service\web" 
	documentationCenter="java" 
	authors="selvasingh" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/07/2016" 
	ms.author="asirveda;robmcm"/>

# 在 IntelliJ 中建立 Azure 的 Hello World Web 應用程式

本教學課程示範如何使用 [Azure Toolkit for IntelliJ (適用於 IntelliJ 的 Azure 工具組)]，建立基本的 Hello World 應用程式，並做為 Web 應用程式部署到 Azure。下文所示的基本 JSP 範例乃力求簡潔，不過只要與 Azure 部署相關，幾乎類似的步驟皆適用於 Java servlet。

當您完成本教學課程，在網頁瀏覽器中檢視您的應用程式時，看起來會如下圖所示：

![][01]
 
## 必要條件

* Java Developer Kit (JDK) 1.7 版或更新版本。
* IntelliJ 概念旗艦版。這可透過 <https://www.jetbrains.com/idea/download/index.html> 下載。
* Java 型 Web 伺服器或應用程式伺服器的散發套件，例如 Apache Tomcat 或 Jetty。
* Azure 訂用帳戶，可從 <https://azure.microsoft.com/free/> 或 <http://azure.microsoft.com/pricing/purchase-options/> 取得。
* 適用於 IntelliJ 的 Azure 工具組。如需詳細資訊，請參閱 [Installing the Azure Toolkit for IntelliJ (安裝適用於 IntelliJ 的 Azure 工具組)]。

## 建立 Hello World 應用程式

首先，我們將從建立 Java 專案開始。

1. 啟動 IntelliJ，在功能表上依序按一下 [檔案]、[新增] 及 [專案]。

   ![][02]

1. 在 [新增專案] 對話方塊中，依序選取 [Java] 和 [Web 應用程式]，然後按 [下一步]。

   ![][03a]

   如果系統提示不指派任何 SDK 並繼續，請按一下 [是]。

   ![][03b]

1. 基於本教學課程的目的，將專案命名為 **Java-Web-App-On-Azure**，然後按一下 [完成]。

   ![][04]

1. 在 IntelliJ 的專案總管檢視中，依序展開 [Java-Web-App-On-Azure] 和 [web]，然後按兩下 [index.jsp]。

   ![][05]

1. 當 index.jsp 檔案在 IntelliJ 中開啟時，於現有的 `<body>` 元素中加入文字以動態顯示 **Hello World!**。您已更新的 `<body>` 內容看起來應該與下列範例類似：

   `<body><b><% out.println("Hello World!"); %></b></body>`

1. 儲存 index.jsp。

## 將應用程式部署至 Azure Web 應用程式容器

您有數種方式可以將 Java Web 應用程式部署至 Azure。本教學課程說明其中一個最簡單的方式：將您的應用程式部署至 Azure Web 應用程式容器，無需特殊的專案類型或額外的工具。Azure 會為您提供 JDK 及 Web 容器軟體，因此您不需要自己上傳；只需要您的 Java Web 應用程式。如此一來，您的應用程式發行程序只需數秒，連一分鐘都不用。

1. 在 IntelliJ 的專案總管中，以滑鼠右鍵按一下 [Java-Web-App-On-Azure] 專案。操作功能表顯示時，選取 [Azure]，然後按一下 [發佈為 Azure Web 應用程式...]

   ![][06]

1. 如果尚未從 IntelliJ 登入 Azure，系統會提示您登入 Azure 帳戶︰

   ![][07]

   注意︰如果您有多個 Azure 帳戶，登入程序期間的某些提示即使內容相同也可能會出現多次。發生此情況時，請遵循登入指示繼續。

1. 在您成功登入 Azure 帳戶後，[管理訂用帳戶] 對話方塊將會顯示與您的認證相關聯的訂用帳戶清單。如果列出多個訂用帳戶，而您只想使用其中幾個帳戶，您可以選擇取消選取要使用的訂用帳戶。當您選取訂用帳戶之後，按一下 [關閉]。

   ![][08]

1. 當 [部署至 Azure Web 應用程式容器] 對話方塊出現時，它會顯示您先前建立的所有 Web 應用程式容器；如果您尚未建立任何容器，清單將會是空白的。   

   ![][09]

1. 如果您之前尚未建立 Azure Web 應用程式容器，或您想要將應用程式發佈到新的容器中，請使用下列步驟。否則，請選取現有的 Web 應用程式容器，並跳至以下的步驟 6。

  1. 按一下 **+**。

        ![][10]

  1. [新增 Web 應用程式容器] 對話方塊會隨即顯示，此對話方塊將用來進行接下來的幾個步驟。

        ![][11]

  1. 為您的 Web 應用程式容器輸入 **DNS 標籤**，這會為您在 Azure 中的 Web 應用程式構成主機 URL 的分葉 DNS 標籤。注意：名稱必須可用，且符合 Azure Web 應用程式命名需求。

  1. 在 [Web Container] (Web 容器) 下拉式功能表中，為您的應用程式選取適當的軟體。

        Currently, you can choose from Tomcat 8, Tomcat 7 or Jetty 9. A recent distribution of the selected software will be provided by Azure, and it will run on a recent distribution of JDK 8 created by Oracle and provided by Azure.

  1. 在 [Subscription] (訂用帳戶) 下拉式選單中，選取您希望此部署使用的訂用帳戶。

  1. 在 [Resource Group] (資源群組) 下拉式功能表中，選取您要與 Web 應用程式相關聯的資源群組。

        Note: Azure Resource Groups allow you to group related resources together so that, for example, they can be deleted together.

        You can select an existing Resource Group (if you have any) and skip to step g below, or use the following these steps to create a new Resource Group:

      * 按一下 [New...] (新增...)

      * [New Resource Group] (新增資源群組) 對話方塊會隨即顯示：

            ![][12]

      * 在 [Name] (名稱) 文字方塊中，為新的資源群組指定名稱。

      * 在 [Region] (區域) 下拉式功能表中，為資源群組選取適當的 Azure 資料中心位置。

      * 按一下 [確定]。

  1. [App Service Plan] (App Service 方案) 下拉式功能表會列出與您選取之資源群組相關聯的應用程式服務方案。

        Note: An App Service Plan specifies information such as the location of your Web App, the pricing tier and the compute instance size. A single App Service Plan can be used for multiple Web Apps, which is why it is maintained separately from a specific Web App deployment.

        You can select an existing App Service Plan (if you have any) and skip to step h below, or use the following these steps to create a new App Service Plan:

      * 按一下 [New...] (新增...)

      * [New App Service Plan] (新增 App Service 方案) 對話方塊會隨即顯示：

            ![][13]

      * 在 [Name] (名稱) 文字方塊中，為新的 App Service 方案指定名稱。

      * 在 [Location] (位置) 下拉式功能表中，為該方案選取適當的 Azure 資料中心位置。

      * 在 [Pricing Tier] (定價層) 下拉式功能表中，為方案選取適當的價格。若為測試用途，您可以選擇 [Free] (免費)。

      * 在 [Instance Size] (執行個體大小) 下拉式功能表中，為方案選取適當的執行個體大小。若為測試用途，您可以選擇 [Small] (小型)。

  1. 一旦您完成所有上述步驟之後，[New Web App Container] (新增 Web 應用程式容器) 對話方塊看起來應該如下圖所示：

        ![][14]

  1. 按一下 [確定] 來完成建立新的 Web 應用程式容器。

        Wait a few seconds for the list of the Web App containers to be refreshed, and your newly-created web app container should now be selected in the list.

1. 現在您已準備好將 Web 應用程式初始部署至 Azure；按一下 [確定]，將您的 Java 應用程式部署至選取的 Web 應用程式容器。

    ![][15]

    注意：根據預設，您的應用程式將會部署為應用程式伺服器的子目錄。如果您想要部署為根應用程式，請選取 [部署到根目錄] 核取方塊，然後按一下 [確定]。

1. 接下來，您應該會看到 [Azure 活動記錄檔] 檢視，它會指出 Web 應用程式的部署狀態。

    ![][16]

    將您的 Web 應用程式部署至 Azure 的程序，應該只需幾秒鐘即可完成。當您的應用程式就緒時，您會在 [狀態] 欄中看到名為**已發佈**的連結。當您按一下連結時，它會帶您到已部署的 Web 應用程式首頁，或者您也可以使用下一節中的步驟，以瀏覽至您的 Web 應用程式。

## 瀏覽至您在 Azure 上的 Web 應用程式

若要瀏覽至您在 Azure 上的 Web 應用程式，您可以使用 [Azure 總管] 檢視。

如果 [Azure 總管] 檢視尚未開啟，您可以按一下 IntelliJ 中的 [檢視] 功能表，按一下 [工具視窗]，然後按一下 [服務總管] 來開啟。如果您之前尚未登入，系統將會提示您登入。

顯示 [Azure 總管] 之後，使用下列這些步驟來停止您的 Web 應用程式：

1. 展開 [Azure] 節點。

1. 展開 [Web Apps] 節點。

1. 以滑鼠右鍵按一下所需的 Web 應用程式。

1. 操作功能表出現時，按一下 [在瀏覽器中開啟]。

    ![][17]

## 更新 Web 應用程式

更新現有執行中的 Azure Web 應用程式是一項快速又簡單的程序，而且您有兩個更新選項：

* 您可以更新現有 Java Web 應用程式的部署。
* 您可以將其他的 Java 應用程式發佈到相同的 Web 應用程式容器。

在任一種情況中，程序都是相同的，而且只需幾秒鐘：

1. 在 IntelliJ 專案總管中，以滑鼠右鍵按一下您要更新或新增到現有 Web 應用程式容器的 Java 應用程式。

1. 操作功能表顯示時，選取 [Azure]，然後選取 [發佈為 Azure Web 應用程式...]

1. 由於您之前已經登入，因此會看到您現有 Web 應用程式容器的清單。選取您要發佈或重新發佈 Java 應用程式的 Web 應用程式容器，然後按一下 [確定]。

幾秒鐘之後，[Azure 活動記錄檔] 檢視將會將您已更新的部署顯示為 [已發佈]，而您將可以在網頁瀏覽器中確認已更新的應用程式。

## 啟動或停止現有的 Web 應用程式

若要啟動或停止現有的 Azure Web 應用程式容器 (包括其中所有已部署的 Java 應用程式)，您可以使用 [Azure 總管] 檢視。

如果 [Azure 總管] 檢視尚未開啟，您可以按一下 IntelliJ 中的 [檢視] 功能表，按一下 [工具視窗]，然後按一下 [服務總管] 來開啟。如果您之前尚未登入，系統將會提示您登入。

顯示 [Azure 總管] 之後，使用下列這些步驟來啟動或停止您的 Web 應用程式：

1. 展開 [Azure] 節點。

1. 展開 [Web Apps] 節點。

1. 以滑鼠右鍵按一下所需的 Web 應用程式。

1. 操作功能表出現時，按一下 [啟動] 或 [停止]。請注意，功能表選項是內容感知的，因此您只能停止執行中的 Web 應用程式，或是啟動目前尚未執行的 Web 應用程式。

    ![][18]

## 後續步驟

如需適用於 Java IDE 的 Azure 套件組的詳細資訊，請參閱下列連結：

- [適用於 Eclipse 的 Azure 工具組]
  - [安裝 Azure Toolkit for Eclipse]
  - [Create a Hello World Web App for Azure in Eclipse (在 Eclipse 中建立 Azure Hello World Web 應用程式)]
  - [適用於 Eclipse 的 Azure 工具組的新功能]
- [Azure Toolkit for IntelliJ]
  - [安裝 Azure Toolkit for IntelliJ]
  - 在 IntelliJ 中建立 Azure 的 Hello World Web 應用程式 (本文)

如需如何搭配使用 Azure 與 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心]。

如需建立 Azure Web Apps 的詳細資訊，請參閱 [Web 應用程式概觀]。

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[適用於 Eclipse 的 Azure 工具組]: ../azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij.md
[Azure Toolkit for IntelliJ (適用於 IntelliJ 的 Azure 工具組)]: ../azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse (在 Eclipse 中建立 Azure Hello World Web 應用程式)]: ./app-service-web-eclipse-create-hello-world-web-app.md
[安裝 Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ (安裝適用於 IntelliJ 的 Azure 工具組)]: ../azure-toolkit-for-intellij-installation.md
[安裝 Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[適用於 Eclipse 的 Azure 工具組的新功能]: ../azure-toolkit-for-eclipse-whats-new.md

[Azure Java 開發人員中心]: https://azure.microsoft.com/develop/java/
[Web 應用程式概觀]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-No-SDK-Specified.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png

<!---HONumber=AcomDC_0608_2016-->