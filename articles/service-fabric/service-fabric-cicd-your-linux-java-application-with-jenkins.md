---
title: "使用 Jenkins 連續建置和整合 Azure Service Fabric Linux Java 應用程式 | Microsoft Docs"
description: "使用 Jenkins 連續建置和整合 Linux Java 應用程式"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 335f41e2e4a40e64e87382ea338673de3ab79c27
ms.lasthandoff: 03/14/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>使用 Jenkins 建置和部署您的 Linux Java 應用程式
Jenkins 是連續整合和部署的熱門工具。 在本文件中，我們將引導您使用 Jenkins 建置和部署您的 Service Fabric 應用程式。

## <a name="general-prerequisites"></a>一般先決條件
1. 在本機安裝 git。 您可以根據您的作業系統，從[這裡](https://git-scm.com/downloads)安裝適當的 git 版本。 如果您是 git 的新手，您可以依照[文件](https://git-scm.com/docs)中所述的步驟來熟悉 git。
2. 備妥 Service Fabric Jenkins 外掛程式。 從[這裡](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi)下載 Service Fabric Jenkins 外掛程式。

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>在 Service Fabric 叢集內設定 Jenkins

### <a name="prerequisites"></a>必要條件
1. 讓 Service Fabric Linux 叢集準備就緒。 從 Azure 入口網站建立的 Service Fabric 叢集已安裝 Docker。 如果您在本機執行叢集，請使用命令 ``docker info`` 檢查是否已安裝 Docker，如果未安裝，則使用下列命令據以安裝：

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. 使用下列步驟將 Service Fabric 容器應用程式部署至叢集：

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
這會在叢集上安裝 Jenkins 容器，您可以使用 Service Fabric Explorer 加以監視。

### <a name="steps"></a>步驟
1. 從瀏覽器移至 URL ``http://PublicIPorFQDN:8081``。 它會提供登入所需之初始管理密碼的路徑。 您可以繼續使用 Jenkins 做為系統管理員使用者，或者您可以在使用初始管理帳戶登入之後，建立和變更使用者。

  > [!NOTE]
  > 建立叢集時，您必須確定 8081 連接埠指定為應用程式端點連接埠
  >

2. 使用 ``docker ps -a`` 取得容器執行個體識別碼。
3. 以 SSH 登入容器，並貼上您在 Jenkins 入口網站中看到的路徑。 例如，如果在入口網站中它顯示路徑 `PATH_TO_INITIAL_ADMIN_PASSWORD`，您可以 -

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. 使用[連結](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)所述的步驟設定 GitHub 來搭配 Jenkins 運作。
    * 使用 GitHub 所提供的指示產生 SSH 金鑰，並將 SSH 金鑰新增至裝載儲存機制的 GitHub 帳戶。
    * 在 Jenkins Docker 殼層 (而非在主機上) 執行上述連結內所提到的命令
    * 若要從主機登入 Jenkins 殼層，請使用下列命令：

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>在 Service Fabric 叢集外設定 Jenkins

### <a name="prerequisites"></a>必要條件
您必須安裝 Docker。 使用下列命令可從終端機安裝 Docker︰

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

現在，當您在終端機執行 ``docker info``，您應該會在輸出中看到 Docker 服務正在執行。

### <a name="steps"></a>步驟
  1. 提取 Service Fabric Jenkins 容器映像︰``docker pull servicefabric-microsoft.azurecr.io/jenkins:v1``
  2. 執行容器映像︰``docker run -itd -p 8080:8080 servicefabric-microsoft.azurecr.io/jenkins:v1``
  3. 取得容器映像執行個體的識別碼。 您可以使用命令 ``docker ps –a`` 列出所有 Docker 容器
  4. 使用下列步驟登入 Jenkins 入口網站︰

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    如果容器識別碼是 2d24a73b5964，請使用 2d24。
    * 從入口網站 (``http://<HOST-IP>:8080``) 登入 Jenkins 儀表板需要此密碼
    * 第一次登入之後，您可以建立您自己的使用者帳戶，並針對未來目的使用該帳戶，或者您可以繼續使用系統管理員帳戶。 一旦您建立使用者，您需要繼續使用。
  5. 使用[連結](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)所述的步驟設定 GitHub 來搭配 Jenkins 運作。
      * 使用 GitHub 提供的指示，產生 SSH 金鑰，並且將 ssh 金鑰新增至 GitHub 帳戶，該帳戶是主控的儲存機制。
      * 在 Jenkins Docker 殼層 (而非在主機上) 執行上述連結內所提到的命令
      * 若要從主機登入 Jenkins 殼層，請使用下列命令：

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

    > [!NOTE]
    > 請確定裝載 Jenkins 容器映像的叢集/機器具有公開 IP，讓 Jenkins 執行個體可以收到來自 GitHub 的通知。
    >

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>從入口網站安裝 Service Fabric Jenkins 外掛程式

1. 移至 ``http://PublicIPorFQDN:8081``。
2. 從 Jenkins 儀表板中，選取 [管理 Jenkins] -> [管理外掛程式] -> [進階]。
在這裡，您可以上傳外掛程式。 選取 [選擇檔案] 選項，然後選取 serviceFabric.hpi 檔案，您已在「必要條件」下載該檔案。 一旦您選取上傳，Jenkins 會自動安裝外掛程式。 在系統要求時允許重新啟動。

## <a name="creating-and-configuring-a-jenkins-job"></a>建立和設定 Jenkins 作業

1. 從儀表板建立**新項目**
2. 輸入項目名稱 - 假設是 **MyJob**、選取自由樣式專案，然後按一下 [確定]
3. 然後前往 [作業] 頁面，並按一下 [設定] -
  * 在 [一般] 區段的 [Github 專案] 底下，指定您的 GitHub 專案 URL，它會裝載您想要與 Jenkins CI/CD 流程整合的 Service Fabric Java 應用程式 (例如 - ``https://github.com/sayantancs/SFJenkins``)。
  * 在 [原始程式碼管理] 區段底下，選取 [Git]。 指定儲存機制 URL，它會裝載您想要與 Jenkins CI/CD 流程整合的 Service Fabric Java 應用程式 (例如 - ``https://github.com/sayantancs/SFJenkins.git``)。 您也可以在這裡指定要建置哪些分支，例如 - ***/master**。
4. 使用下列步驟，設定您的 *GitHub* (裝載儲存機制者)，讓它能夠與 Jenkins 溝通 -
  1. 移至您的 GitHub 儲存機制頁面。 移至 [設定] -> [整合和服務]。
  2. 選取 [新增服務]、輸入 Jenkins，然後選取 [Jenkins-Github 外掛程式]。
  3. 輸入您的 Jenkins webhook URL (根據預設，它應該是 ``http://<PublicIPorFQDN>:8081/github-webhook/``)。 按一下 [新增/更新服務]。
  4. 隨即會將測試事件傳送至您的 Jenkins 執行個體。 您應該會在 GitHub 中看到 webhook 所做的綠色勾號，而且您的專案將會建置！
  5. 在 [建置觸發程序] 區段底下，選取您想要哪個建置選項 - 針對這個使用案例，我們計劃在每當有推送到儲存機制發生時觸發建置 - 所以對應的選項會是 - [用來輪詢 GITScm 的GitHub 攔截觸發程序] (先前是「當變更推送至 GitHub 時變更」)
  6. 在 [建置] 區段底下 - 從下拉式清單 [新增建置步驟]，選取 [叫用 Gradle 指令碼] 選項。 在隨附的 Widget 中，針對您的應用程式指定 [根建置指令碼] 的路徑。 它會從指定的路徑挑選 build.gradle，並據以運作。 如果您建立名為 ``MyActor`` 的專案 (使用 Eclipse 外掛程式或 Yeoman 產生器)，則根建置指令碼應該包含 - ``${WORKSPACE}/MyActor``。 做為範例，本節大部分看起來像 -

    ![Service Fabric Jenkins 建置動作][build-step]
  7. 在 [建置後動作] 下拉式清單底下，選取 [部署 Service Fabric 專案]。 這裡您必須提供叢集詳細資料，Jenkins 編譯 Service Fabric 應用程式會部署，且額外應用程式詳細資料會用來部署應用程式。 下列螢幕擷取畫面可以做為參考︰

    ![Service Fabric Jenkins 建置動作][post-build-step]

 > [!NOTE]
 > 如果您使用 Service Fabric 來部署 Jenkins 容器映像，這裡的叢集可能與裝載 Jenkins 容器應用程式的叢集相同。
 >

### <a name="end-to-end-scenario"></a>端對端案例
現在，您的 GitHub 和 Jenkins 已設定，而且您現在可以在儲存機制的 ``MyActor`` 專案中進行一些範例變更，例如 https://github.com/sayantancs/SFJenkins，並且將變更推送至遠端 ``master`` 分支 (或您已設定來搭配使用的任何分支)。 這會觸發您設定的 Jenkins 作業 ``MyJob``。 它所做的基本上 - 就是從 GitHub 擷取變更、建置它們並且將應用程式部署至您在建置後動作中指定的叢集端點。  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

