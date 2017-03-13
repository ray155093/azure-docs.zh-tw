---
title: "使用 Jenkins 為您的 Linux java 應用程式 Linux 連續建置和部署 | Microsoft Docs"
description: "使用 Jenkins 為您的 Linux java 應用程式 Linux 連續建置和部署"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: raunakp
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: sayantancs
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: d3c64e3ed78a5379592b5e4276c83d26f04d2e1c
ms.lasthandoff: 03/08/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>使用 Jenkins 建置和部署您的 Linux Java 應用程式
Jenkins 是連續整合和部署的熱門工具。 在本文件中，我們將引導您使用 Jenkins 建置和部署您的 Service Fabric 應用程式。

## <a name="general-prerequisites"></a>一般先決條件
1. 您需要在本機安裝 git。 您可以根據您的作業系統，從[這裡](https://git-scm.com/downloads)安裝適當的 git 版本。 如果您是 git 的新手，您可以依照[文件](https://git-scm.com/docs)中所述的步驟來熟悉 git。
2. 您需要有好用的 Service Fabric Jenkins 外掛程式。 從[這裡](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi)下載 Service Fabric Jenkins 外掛程式。

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>在 Service Fabric 叢集內設定 Jenkins

### <a name="prerequisites"></a>必要條件
1. 讓 Service Fabric Linux 叢集準備就緒。 從 Azure 入口網站建立的 Service Fabric 叢集已安裝 Docker。 如果您執行本機叢集，請使用命令 ``docker info`` 檢查是否已安裝 Docker，如果未安裝，則據以安裝，使用 -
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
2. 將 Service Fabric 容器應用程式部署在叢集 ``http://PublicIPorFQDN:19080``。 您可以依照下列步驟 -
```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker && cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080 # Azure CLI cluster connect command
bash Scripts/install.sh
```
  這會將 Jenkins 容器安裝到您連線的叢集。 您可以使用 Service Fabric 總管監視容器應用程式。

### <a name="steps"></a>步驟
1. 從瀏覽器移至 URL ``http://PublicIPorFQDN:8081``。 它會提供您登入所需之初始管理密碼的路徑。 您可以繼續使用 Jenkins 做為系統管理員使用者，或者您可以在使用初始管理帳戶登入之後，建立和變更使用者。
> [!NOTE]
> 建立叢集時，您必須確定 8081 連接埠指定為應用程式端點連接埠
>

2. 使用 ``docker ps -a`` 取得容器執行個體識別碼。
3. 使用對容器的 SSH 登入，並且貼上在 Jenkins 入口網站顯示的路徑。 例如，如果在入口網站中它顯示路徑 `PATH_TO_INITIAL_ADMIN_PASSWORD`，您可以 -  
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash # This takes you inside docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. 安裝 GitHub 以搭配 Jenkins 使用[這個項目](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)。
    * 使用 GitHub 提供的指示，產生 SSH 金鑰，並且將 ssh 金鑰新增至 GitHub 帳戶，該帳戶是主控的儲存機制。
    * 執行 Jenkins-docker shell (而不是在您的主機上) 中上述連結所述的命令
    * 若要從您的主機登入 Jenkins shell，您需要 -
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>在 Service Fabric 叢集外設定 Jenkins

### <a name="prerequisites"></a>必要條件
1. 您必須安裝 docker。 如果尚未安裝，您可以在終端機輸入下列命令來安裝 docker。
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
現在，當您在終端機執行 ``docker info``，您會在輸出中看到 docker 服務正在執行。

### <a name="steps"></a>步驟
  1. 提取 Service Fabric Jenkins 容器︰``docker pull IMAGE_NAME ``
  2. 執行容器映像︰``docker run -itd -p 8080:8080 IMAGE_NAME``
  3. 取得 docker 容器的識別碼，該容器具有 jenkins 映像 (您剛剛安裝)。 您可以使用命令 ``docker ps –a`` 列出所有 docker 容器
  4. 取得 Jenkins 帳戶的系統管理員密碼。 針對這個目的，您可以執行下列工作 -
    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    如果容器識別碼是 2d24a73b5964，您只需要插入 2d24
    * 從 ``http://<HOST-IP>:8080`` 的入口網站登入 Jenkins 儀表板需要此密碼
    * 第一次登入之後，您可以建立您自己的使用者帳戶，並針對未來目的使用該帳戶，或者您可以繼續使用系統管理員帳戶。 一旦您建立新的使用者，您需要繼續使用。
  5. 安裝 GitHub 以搭配 Jenkins 使用[這個項目](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)。
      * 使用 GitHub 提供的指示，產生 SSH 金鑰，並且將 ssh 金鑰新增至 GitHub 帳戶，該帳戶是主控的儲存機制。
      * 執行 Jenkins-docker shell (而不是在您的主機上) 中上述連結所述的命令
      * 若要從您的主機登入 Jenkins shell，您需要 -
    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

> [!NOTE]
> 請確定裝載 Jenkins 容器映像的叢集/機器具有公開 IP，讓 Jenkins 執行個體可以收到來自 GitHub 的通知。
>

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>從入口網站安裝 Service Fabric Jenkins 外掛程式

1. 移至 ``http://PublicIPorFQDN:8081``。
2. 從 Jenkins 儀表板，選取 ``Manage Jenkins`` -> ``Manage Plugins`` -> ``Advanced``。
在這裡，您可以上傳外掛程式。 選取 ``Choose file`` 選項，然後選取 serviceFabric.hpi 檔案，您已在「必要條件」下載該檔案。 一旦您選取上傳，Jenkins 將會自動為您安裝外掛程式。 它可能也會要求重新啟動，請允許它。

## <a name="creating-and-configuring-a-jenkins-job"></a>建立和設定 Jenkins 作業

1. 從儀表板建立 ``new item``
2. 輸入新的項目名稱 - 假設是 ``MyJob``、選取自由樣式專案，然後按一下 [確定]
3. 然後前往 [作業] 頁面，並按一下 ``Configure`` -
  * 在 [一般] 區段的 ``Github project`` 底下，指定您的 GitHub 專案 URL，它會裝載您想要與 Jenkins CI/CD 流程整合的 Service Fabric Java 應用程式 (例如 - ``https://github.com/sayantancs/SFJenkins``)。
  * 在 ``Source Code Management`` 區段底下，選取 ``Git``。 指定儲存機制 URL，它會裝載您想要與 Jenkins CI/CD 流程整合的 Service Fabric Java 應用程式 (例如 - ``https://github.com/sayantancs/SFJenkins.git``)。 您也可以在這裡指定要建置哪些分支，例如 - ``*/master``。
4. 使用下列步驟，設定您的 *Github* (也就裝載儲存機制)，讓它能夠與 Jenkins 溝通，-
  - 移至您的 GitHub 儲存機制頁面。 移至 ``Settings`` -> ``Integrations and Services``。
  - 選取 ``Add Service``、輸入 Jenkins，選取 ``Jenkins-Github plugin``。
  - 輸入您的 Jenkins webhook URL (根據預設，它應該是 ``http://<PublicIPorFQDN>:8081/github-webhook/``)。 按一下新增/更新服務。
  - 測試事件將會傳送至您的 Jenkins 執行個體。 您應該會在 GitHub 中看到 webhook 所做的綠色勾號，而且您的專案將會建置！
  - 在 ``Build Triggers`` 區段底下，選取您想要哪個建置選項 - 針對這個使用案例，我們計劃在每當有推送到儲存機制發生時觸發建置 - 所以對應的選項會是 - ``GitHub hook trigger for GITScm polling`` (先前是「當變更推送至 GitHub 時變更」)
  - 在 ``Build section`` 底下 - 從下拉式清單 ``Add build step``，選取選項 ``Invoke Gradle Script``。 在隨附的 Widget 中，針對您的應用程式指定 ``Root build script`` 的路徑。 它會從指定的路徑挑選 build.gradle，並據以運作。 請注意 - 如果您建立專案，名為 ``MyActor`` (使用 Eclipse 外掛程式或 Yeoman 產生器)，則根建置指令碼應該包含 - ``${WORKSPACE}/MyActor``。 做為範例，本節大部分看起來像 -

    ![Service Fabric Jenkins 建置動作][build-step]
  - 在 ``Post-Build Actions`` 下拉式清單底下，選取 ``Deploy Service Fabric Project``。 這裡您必須提供叢集詳細資料，Jenkins 編譯 Service Fabric 應用程式會部署，且額外應用程式詳細資料會用來部署應用程式。 下列螢幕擷取畫面可以做為參考︰

    ![Service Fabric Jenkins 建置動作][post-build-step]

 >[!Note]
 > 如果您使用 Service Fabric 來部署 Jenkins 容器映像，這裡的叢集可能與裝載 Jenkins 容器應用程式的叢集相同。
 >

### <a name="end-to-end-scenario"></a>端對端案例
現在，您的 GitHub 和 Jenkins 已設定，而且您現在可以在儲存機制的 ``MyActor`` 專案中進行一些範例變更，例如 https://github.com/sayantancs/SFJenkins，並且將變更推送至遠端 ``master`` 分支 (或您已設定來搭配使用的任何分支)。 現在會觸發您設定的 Jenkins 作業 ``MyJob``。 它所做的基本上 - 就是從 GitHub 擷取變更、建置它們並且將應用程式部署至您在建置後動作中指定的叢集端點。  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

