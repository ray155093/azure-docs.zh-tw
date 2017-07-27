---
title: "使用適用於 IntelliJ 的 Azure 工具組將 Spring Boot 應用程式發佈為 Docker 容器 | Microsoft Docs"
description: "了解如何使用適用於 IntelliJ 的 Azure 工具組，將 Web 應用程式發佈至 Microsoft Azure 作為 Docker 容器。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/21/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 8d1f0f0f95647c85b2b5f3550e306e3ce35e81c3
ms.contentlocale: zh-tw
ms.lasthandoff: 06/23/2017


---

# <a name="publish-a-spring-boot-app-as-a-docker-container-using-the-azure-toolkit-for-intellij"></a>使用適用於 IntelliJ 的 Azure 工具組，將 Spring Boot 應用程式發佈為 Docker 容器

**[Spring Framework]** 是開放原始碼解決方案，可協助 Java 開發人員建立企業級應用程式。 建立在該平台之基礎上的其中一個更熱門的專案是 [Spring Boot]，其中會提供用來建立獨立 Java 應用程式的簡化方法。

**[Docker]** 是開放原始碼解決方案，可協助開發人員自動化部署、調整及管理容器中執行的應用程式。

本教學課程會逐步引導您使用適用於 IntelliJ 的 Azure 工具組，將 Spring Boot 應用程式作為 Docker 容器發佈至 Microsoft Azure。

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="cloning-the-default-spring-boot-docker-app-repo"></a>複製預設 Spring Boot Docker 應用程式存放庫

下列步驟會逐步引導您完成使用 IntelliJ 複製 Spring Boot Docker 存放庫。 如果您想要使用命令列，請參閱[將 Spring Boot 應用程式部署到 Azure Container Service 中的 Linux][Deploy Spring Boot on Linux in ACS]。

1. 開啟 IntelliJ。

1. 在 [歡迎使用] 畫面上，選擇 [從版本控制簽出] 下拉式功能表中的 [GitHub] 選項。

   ![從版本控制簽出][CL01]

1. 如果系統提示您登入，請輸入您的認證。

   * 如果您使用使用者名稱/密碼來登入 GitHub：

      ![輸入 GitHub 認證對話方塊][CL02a]

   * 如果您使用權杖來登入 GitHub：

      ![輸入 GitHub 認證對話方塊][CL02b]

1. 針對存放庫 URL 輸入 `https://github.com/spring-guides/gs-spring-boot-docker.git`，指定本機路徑和資料夾資訊，然後按一下 [複製]。

   ![複製存放庫對話方塊][CL03]

1. 當系統提示您建立 IntelliJ 專案時，選擇 [否]。

   ![建立 IntelliJ 專案][CL04]

1. 在 [歡迎使用] 畫面中，按一下 [匯入專案]。

   ![匯入專案][CL05]

1. 找出您複製 Spring Boot 存放庫所在的路徑，反白顯示根目錄底下的 **complete** 資料夾，然後按一下 [確定]。

   ![匯入專案][CL06]

1. 出現提示時，選擇 [從現有來源建立專案]。

   ![從現有來源建立專案][CL07]

1. 指定您的專案名稱或接受預設值，確認 **complete**的正確路徑，然後按 [下一步]。

   ![指定專案名稱][CL08]

1. 自訂任何要匯入的目錄，然後按 [下一步]。

   ![選擇目錄][CL09]

1. 檢閱要匯入的程式庫，然後按 [下一步]。

   ![檢閱專案程式庫][CL10]

1. 檢閱模組結構，然後按 [下一步]。

   ![檢閱模組結構][CL11]

1. 指定您的 JDK，然後按 [下一步]。

   ![指定 JDK][CL12]

1. 按一下 [完成] 。

   ![完成][CL13]

1. IntelliJ 會匯入 Spring Boot 應用程式作為專案，並且在匯入完成時顯示結構。

   ![IntelliJ 中的 Spring Boot 應用程式][CL14]

## <a name="building-your-spring-boot-app"></a>建置 Spring Boot 應用程式

### <a name="build-the-app-using-the-maven-pom"></a>使用 Maven POM 建置應用程式

1. 如果尚未開啟 Maven 工具視窗，請加以開啟，若要完成這項操作，請依序按一下 [檢視]、[工具視窗]、[Maven 專案]。

   ![檢視 Maven 工具視窗][BU01]

1. 在 Maven 工具視窗中，以滑鼠右鍵按一下 [套件]，然後選擇 [執行 Maven 建置]。 (如果 Maven 專案未自動顯示，您可能需要按一下 Maven 工具列上的 [重新匯入] 圖示。)

   ![執行 Maven 建置][BU02]

1. Spring Boot 應用程式成功建立時，IntelliJ 應該會顯示「建置成功」訊息。

   ![建置成功][BU03]

### <a name="create-a-deployment-ready-artifact"></a>建立已可供部署的構件

若要發佈 Spring Boot 應用程式，您必須建立已可供部署的構件。 若要這樣做，請使用下列步驟：

1. 在 IntelliJ 中開啟 web 應用程式專案。

1. 依序按一下 [檔案] 及 [專案結構]。

   ![專案結構功能表][ART01]

1. 按一下綠色的加號 (「**+**」) 來新增構件，按一下 [JAR]，然後按一下 [空白]。

   ![新增構件][ART02]

1. 為構件命名同時確定未新增 ".jar" 副檔名，然後指定 Maven 輸出的目標資料夾。

   ![指定構件屬性][ART03]

1. 選擇性：建立構件的資訊清單：

   a. 按一下 [建立資訊清單]。

      ![指定構件路徑][ART04a]

   b.這是另一個 C# 主控台應用程式。 選擇構件的預設路徑，然後按一下 [確定]。

      ![指定構件路徑][ART04b]

   c. 按一下省略符號 **...** 以指定主要類別。

      ![找出主要類別][ART04c]

   d. 選擇您的主要類別，然後按一下 [確定]。

      ![指定主要類別][ART04d]

1. 按一下 [確定] 。

   ![關閉構件屬性對話方塊][ART05]

> [!NOTE]
>
> 如需在 IntelliJ 中建立成品的詳細資訊，請參閱 JetBrains 網站上的[設定成品]。
>

### <a name="build-the-artifact-for-deployment"></a>建置要部署的構件

1. 按一下 [建置]，然後按一下 [構件]。

   ![建置構件功能表][BU04]

1. 當 [建置構件] 內容功能表顯示時，按一下 [建置]。

   ![建置構件內容功能表][BU05]

1. IntelliJ 應該會在專案工具視窗中為 Spring Boot 應用程式顯示已完成的構件。

   ![建立的構件][BU06]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>使用 Docker 容器將您的 web 應用程式發佈至 Azure

1. 如果您尚未登入您的 Azure 帳戶，請遵循[適用於 IntelliJ 的 Azure 工具組登入指示][Azure Sign In for IntelliJ]文章中的步驟。

1. 在 [專案總管] 工具視窗中，以滑鼠右鍵按一下專案，然後選取 [Azure] > [發佈為 Docker 容器]。

   ![發佈為 Docker 容器][PU01]

1. 當 [在 Azure 上部署 Docker 容器] 對話方塊顯示時，將會顯示任何現有的 Docker 主機。 如果您選擇部署到現有的主機，您可以跳至步驟 4。 否則，您必須使用下列步驟建立新的主機：

   a. 按一下綠色的加號 (「**+**」)。

      ![新增 Docker 主機][PU02]

   b.這是另一個 C# 主控台應用程式。 當 [建立 Docker 主機] 對話方塊顯示時，您可以選擇接受預設值，或是為新的 Docker 主機指定任何自訂設定。 (各種設定的詳細描述可以在[使用適用於 IntelliJ 的 Azure 工具組，將 Web 應用程式發佈作為 Docker 容器][Publish Container with Azure Toolkit]文章中取得。)當您已指定要使用的設定時，按 [下一步]。

      ![指定 Docker 主機選項][PU03a]

   c. 您可以選擇使用 Azure Key Vault 中的現有登入認證，或者可以選擇輸入新的 Docker 登入認證。 當您已指定選項時，按一下 [完成]。

      ![指定 Docker 主機認證][PU03b]

1. 反白顯示您的 Docker 主機，然後按 [下一步]。

   ![選取要使用的 Docker 主機][PU04]

1. 在 [在 Azure 上部署 Docker 容器] 對話方塊的最後一頁，您必須指定下列選項：

   a. 您可以選擇對將會主控 Docker 容器的容器指定自訂名稱，或者您可以接受預設值。

   b.這是另一個 C# 主控台應用程式。 您必須使用下列語法輸入 Docker 主機的 TCP 連接埠："*[external port]*:*[internal port]*。 例如，"80:8080" 會指定外部連接埠 "80" 和預設內部 Spring Boot 連接埠 "8080"。
   
      如果您已自訂您的內部連接埠 (例如藉由編輯 application.yml 檔案)，您必須指定連接埠號碼才能在 Azure 中正確路由。

   c. 一旦您已設定這些選項，請按一下 [完成]。

   ![在 Azure 上部署 Docker 容器][PU05]

1. 當 Azure 工具組完成發佈時，Azure 活動記錄會顯示狀態為「已發佈」。

   ![已成功部署 Docker 主機][PU06]

## <a name="next-steps"></a>後續步驟

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

請參閱 JetBrains 網站上的[建立 Spring Boot 專案](https://www.jetbrains.com/help/idea/creating-spring-boot-projects.html)，以深入了解使用 IntelliJ 來建立 Spring Boot 應用程式的其他方法。

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Sign In for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[設定成品]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html
[Deploy Spring Boot on Linux in ACS]: ./container-service/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL01.png
[CL02a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02a.png
[CL02b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02b.png
[CL03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL09.png
[CL10]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL10.png
[CL11]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL11.png
[CL12]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL12.png
[CL13]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL13.png
[CL14]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL14.png

[ART01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART01.png
[ART02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART02.png
[ART03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART03.png
[ART04a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04a.png
[ART04b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04b.png
[ART04c]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04c.png
[ART04d]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04d.png
[ART05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART05.png

[BU01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU02.png
[BU03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU03.png
[BU04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU04.png
[BU05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU05.png
[BU06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU06.png

[PU01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU02.png
[PU03a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03a.png
[PU03b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03b.png
[PU04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU06.png

