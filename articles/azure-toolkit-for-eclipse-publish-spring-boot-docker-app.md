---
title: "使用適用於 Eclipse 的 Azure 工具組，將 Spring Boot 應用程式發佈為 Docker 容器 | Microsoft Docs"
description: "了解如何使用適用於 Eclipse 的 Azure 工具組，將 Web 應用程式發佈至 Microsoft Azure 作為 Docker 容器。"
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
ms.openlocfilehash: 36d8233b64144d87501cd4d4a9cac539ab143d38
ms.contentlocale: zh-tw
ms.lasthandoff: 06/23/2017


---

# <a name="publish-a-spring-boot-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>使用適用於 Eclipse 的 Azure 工具組，將 Spring Boot 應用程式發佈為 Docker 容器

**[Spring Framework]** 是開放原始碼解決方案，可協助 Java 開發人員建立企業級應用程式。 建立在該平台之基礎上的其中一個更熱門的專案是 [Spring Boot]，其中會提供用來建立獨立 Java 應用程式的簡化方法。

**[Docker]** 是開放原始碼解決方案，可協助開發人員自動化部署、調整及管理容器中執行的應用程式。

本教學課程會逐步引導您使用適用於 Eclipse 的 Azure 工具組，將 Spring Boot 應用程式作為 Docker 容器發佈至 Microsoft Azure。

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="cloning-the-default-spring-boot-docker-app-repository"></a>複製預設 Spring Boot Docker 應用程式存放庫

### <a name="importing-the-public-repository"></a>匯入公用存放庫

下列步驟會逐步引導您使用 IntelliJ 將 Spring Boot Docker 存放庫複製至本機電腦。 如果您想要使用命令列，請參閱[將 Spring Boot 應用程式部署到 Azure Container Service 中的 Linux][Deploy Spring Boot on Linux in ACS]。

1. 開啟 Eclipse。

1. 按一下 [檔案]，然後按一下 [匯入]。

   ![檔案匯入功能表][CL01]

1. 開啟 [匯入] 對話方塊時：

   a. 展開 [Git]。

   b.這是另一個 C# 主控台應用程式。 反白顯示 [Projects from Git] (Git 中的專案)。
   
   c. 按一下 [下一步] 。

   ![匯入對話方塊][CL02]

1. 在 [Select Repository Source] (選取存放庫來源) 頁面上：

   a. 反白顯示 [Clone URI] (複製 URI)。
   
   b.這是另一個 C# 主控台應用程式。 按一下 [下一步] 。

   ![Select Repository Source (選取存放庫來源)][CL03]

1. 在 [Source Git Repository] (來源 Git 存放庫) 頁面上：

   a. 輸入 `https://github.com/spring-guides/gs-spring-boot-docker.git` 作為存放庫 **URI**；這應該會自動填入 [主機] 和 [存放庫路徑] 欄位的正確值。
   
   b.這是另一個 C# 主控台應用程式。 Spring Boot 是公用存放庫，因此您不應該輸入 Git 使用者名稱和密碼。
   
   c. 按一下 [下一步] 。

   ![Source Git Repository (來源 Git 存放庫)][CL04]

1. 在 [分支] 頁面上，按一下 [下一步]。

   ![分支][CL05]

1. 在 [Local Destination] (本機目的地) 頁面上：

   a. 指定您想要放置本機存放庫的本機資料夾。
   
   b.這是另一個 C# 主控台應用程式。 按一下 [下一步] 。

   ![Local Destination (本機目的地)][CL06]

1. 在 [Select a wizard to use for importing projects] (選取要用於匯入專案的精靈) 頁面上：

   a. 選取 [Import as a general project] (匯入為一般專案)。
   
   b.這是另一個 C# 主控台應用程式。 按一下 [下一步] 。

   ![Local Destination (本機目的地)][CL07]

1. 在 [Import Projects] (匯入專案) 頁面上：

   a. 指定 [專案名稱]。
   
   b.這是另一個 C# 主控台應用程式。 按一下 [完成] 。

   ![Import Projects (匯入專案)][CL08]

1. 成功複製存放庫時，您會看到 Eclipse 中列出所有檔案。

   ![Local repository (本機存放庫)][CL09]

### <a name="creating-a-maven-project-from-your-local-repository"></a>從本機存放庫建立 Maven 專案

Spring Boot Docker 存放庫包含將用於此教學課程的已完成 Maven 專案。 

1. 按一下 [檔案]，然後按一下 [匯入]。

   ![檔案匯入功能表][CL01]

1. 開啟 [匯入] 對話方塊時：

   a. 展開 [Maven]。
   
   b.這是另一個 C# 主控台應用程式。 反白顯示 [Existing Maven Projects] (現有 Maven 專案)。
   
   c. 按一下 [下一步] 。

   ![匯入對話方塊][MV01]

1. 在 [Maven Projects] (Maven 專案) 頁面上：

   a. 指定 [根目錄] 之本機存放庫內的 `complete` 資料夾。
   
   b.這是另一個 C# 主控台應用程式。 展開 [進階] 區段，然後在 [Name template] (名稱範本) 中輸入自訂名稱。
   
   c. 核取專案內 `pom.xml` 檔案的方塊。
   
   d. 按一下 [完成] 。

   ![Maven Projects (Maven 專案)][MV02]

1. 成功開啟 Maven 專案時，您會看到 Eclipse 中列出第二個專案。

   ![Local Maven project (本機 Maven 專案)][MV03]

## <a name="building-your-spring-boot-app-with-maven"></a>使用 Maven 建置 Spring Boot 應用程式

1. 在 Eclipse Project Explorer 中，反白顯示 Maven 專案。

1. 按一下 [執行]，並按一下 [執行身分]，然後按一下 [Maven build] (Maven 組建).

   ![Run as Maven build (以 Maven 組建方式執行)][BU01]

1. 成功建置應用程式時，主控台視窗將會列出狀態。

   ![Successful Maven build (成功的 Maven 組建)][BU02]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>使用 Docker 容器將您的 web 應用程式發佈至 Azure

1. 在 Eclipse Project Explorer 中，反白顯示 Maven 專案。

1. 按一下 Azure [發佈] 功能表，然後按一下 [發佈為 Docker 容器]。

   ![發佈為 Docker 容器][PU01]

1. 顯示 [在 Azure 上部署 Docker 容器] 對話方塊時：

   a. 輸入自訂 [Docker image name] (Docker 映像名稱)。
   
   b.這是另一個 C# 主控台應用程式。 在 [Artifact to deploy] (要部署的構件) 中，指定您剛剛建置之 `gs-spring-boot-docker-0.1.0.jar` 檔案的路徑。

   ![Specify Docker options (指定 Docker 選項)][PU02]

   將會顯示任何現有 Docker 主機。 如果您選擇部署到現有的主機，您可以跳至步驟 4。 否則，您必須使用下列步驟建立新的主機：

   a. 按一下 [新增] 。

      ![新增 Docker 主機][PU03]

   b.這是另一個 C# 主控台應用程式。 顯示 [建立 Docker 主機] 對話方塊時，您可以選擇接受預設值，或是為新的 Docker 主機指定任何自訂設定 (各種設定的詳細描述可以在[使用適用於 IntelliJ 的 Azure 工具組，將 Web 應用程式發佈為 Docker 容器][Publish Container with Azure Toolkit]文章中取得)。當您已指定要使用的設定時，按一下 [下一步]。

      ![指定 Docker 主機選項][PU04]

   c. 您可以選擇使用 Azure Key Vault 中的現有登入認證，或者可以選擇輸入新的 Docker 登入認證。 當您已指定選項時，按一下 [完成]。

      ![指定 Docker 主機認證][PU05]

1. 反白顯示您的 Docker 主機，然後按一下 [下一步]。

   ![選取要使用的 Docker 主機][PU06]

1. 在 [在 Azure 上部署 Docker 容器] 對話方塊的最後一頁，您必須指定下列選項：

   a. 您可以選擇對將會主控 Docker 容器的容器指定自訂名稱，或者您可以接受預設值。

   b.這是另一個 C# 主控台應用程式。 您必須使用下列語法輸入 Docker 主機的 TCP 連接埠："[外部連接埠]:[內部連接埠]。 例如，"80:8080" 會指定外部連接埠 "80" 和預設內部 Spring Boot 連接埠 "8080"。
   
      如果您已自訂您的內部連接埠 (例如藉由編輯 application.yml 檔案)，您必須指定連接埠號碼才能在 Azure 中正確路由。

   c. 一旦您已設定這些選項，請按一下 [完成]。

   ![在 Azure 上部署 Docker 容器][PU07]

1. 當 Azure 工具組完成發佈時，Azure 活動記錄會顯示狀態為「已發佈」。

   ![已成功部署 Docker 主機][PU08]

## <a name="next-steps"></a>後續步驟

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Deploy Spring Boot on Linux in ACS]: ./container-service/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL01.png
[CL02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL02.png
[CL03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL09.png

[MV01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV01.png
[MV02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV02.png
[MV03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV03.png

[BU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU02.png

[PU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU02.png
[PU03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU03.png
[PU04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU06.png
[PU07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU07.png
[PU08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU08.png

