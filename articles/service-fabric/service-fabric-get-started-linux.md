---
title: "在 Linux 上設定開發環境 | Microsoft Docs"
description: "在 Linux 上安裝執行階段和 SDK，並建立本機開發叢集。 完成此設定之後，您就可以開始建置應用程式。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 5840932d79ef9fd99a94eb6ae4e587b0e616065e
ms.contentlocale: zh-tw
ms.lasthandoff: 07/01/2017


---
# 在 Linux 上準備您的開發環境
<a id="prepare-your-development-environment-on-linux" class="xliff"></a>
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

 若要在 Linux 開發機器上部署和執行 [Azure Service Fabric 應用程式](service-fabric-application-model.md) ，請安裝執行階段和通用 SDK。 您也可以安裝 Java 和 .NET Core 的選擇性 SDK。

## 必要條件
<a id="prerequisites" class="xliff"></a>

### 支援的作業系統版本
<a id="supported-operating-system-versions" class="xliff"></a>
下列為支援開發的作業系統版本：

* Ubuntu 16.04 (`Xenial Xerus`)

## 更新 apt 來源
<a id="update-your-apt-sources" class="xliff"></a>
若要透過 apt-get 安裝 SDK 和相關聯的執行階段套件，您必須先更新 apt 來源。

1. 開啟終端機。
2. 將 Service Fabric 存放庫新增至來源清單。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. 將 `dotnet` 存放庫新增至來源清單。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. 將新的 GPG 金鑰新增至 apt keyring。

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. 將 Docker 的官方 GPG 金鑰新增至 apt keyring。

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. 設定 Docker 存放庫。

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. 根據新增的存放庫重新整理套件清單。

    ```bash
    sudo apt-get update
    ```

## 安裝並設定容器和來賓可執行檔的 SDK
<a id="install-and-set-up-the-sdk-for-containers-and-guest-executables" class="xliff"></a>

更新來源後，您可以安裝 SDK。

1. 安裝 Service Fabric SDK 套件。 系統會要求您確認安裝並同意授權合約。

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

    >   [!TIP]
    >   下列命令會自動接受 Service Fabric 套件的授權︰
    >   ```bash
    >   echo "servicefabric servicefabric/accepted-eula-v1 select true" | debconf-set-selections
    >   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | debconf-set-selections
    >   ```
    
2. 執行 SDK 安裝指令碼。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

一旦您已執行安裝一般 SDK 套件的步驟後，就能執行 `yo azuresfguest` 或 `yo azuresfcontainer`，以使用來賓可執行檔或容器服務建立應用程式。 您可能需要將您的 **$NODE_PATH** 環境變數設定為節點模組的所在位置。 


```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
```

如果您以 root 的身分使用環境，可能需要使用下列命令設定變數︰

```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
```


> [!TIP]
> 您可以將這些命令新增至 ~/.bashrc 檔案，讓您不必在每一次登入時設定環境變數。
>

## 設定 XPlat Service Fabric CLI
<a id="setup-the-xplat-service-fabric-cli" class="xliff"></a>
[XPlat CLI][azure-xplat-cli-github] 包含可供與 Service Fabric 實體 (包括叢集和應用程式) 進行互動的命令。 它是以 Node.js 為基礎，所以先[確認您已安裝 Node][install-node]，再繼續執行下列指示：

1. 將 github 存放庫複製到您的開發電腦。

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. 切換至所複製的存放庫，並使用 Node Package Manager (npm) 安裝 CLI 的相依項目。

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. 建立從所複製存放庫的 `bin/azure` 資料夾到 `/usr/bin/azure` 的符號連結。

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. 最後，啟用自動完成 Service Fabric 命令。

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

### 設定 Azure CLI 2.0
<a id="setup-azure-cli-20" class="xliff"></a>

作為 XPlat CLI 的替代項目，Azure CLI 中目前已包含一個 Service Fabric 命令模組。

如需安裝 Azure CLI 2.0 和使用 Service Fabric 命令的詳細資訊，請參閱[使用者入門文件](service-fabric-azure-cli-2-0.md)。

## 設定本機叢集
<a id="set-up-a-local-cluster" class="xliff"></a>
如果一切都已順利安裝，您應該能夠啟動本機叢集。

1. 執行叢集安裝指令碼。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. 開啟 網頁瀏覽器並瀏覽至 http://localhost:19080/Explorer。 如果叢集已經啟動，您應會看見 Service Fabric Explorer 儀表板。

    ![Linux 上的 Service Fabric Explorer][sfx-linux]

此時，您可以根據客體容器或來賓可執行檔，部署預先建置的 Service Fabric 應用程式套件或新的套件。 若要使用 Java 或 .NET Core SDK 建置新的服務，請遵循後面幾節所提供的選擇性設定步驟。


> [!NOTE]
> Linux 不支援獨立叢集 - 預覽中僅支援單機和 Azure Linux 多電腦叢集。
>

## 安裝 Java SDK (選擇性，如果您想要使用 Java 程式設計模型)
<a id="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models" class="xliff"></a>
Java SDK 提供了使用 Java 建置 Service Fabric 服務所需的程式庫和範本。

1. 安裝 Java SDK 套件。

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. 執行 SDK 安裝指令碼。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

## 安裝 Eclipse Neon 外掛程式 (選擇性)
<a id="install-the-eclipse-neon-plugin-optional" class="xliff"></a>

您可以從**適用於 Java 開發人員的 Eclipse 整合式開發環境 (IDE)** 安裝適用於 Service Fabric 的 Eclipse 外掛程式。 除了 Service Fabric Java 應用程式之外，您可以使用 Eclipse 來建立 Service Fabric 來賓可執行檔應用程式和容器應用程式。

> [!NOTE]
> Java SDK 是使用 Eclipse 外掛程式的必要條件，即使您只將它用於來賓可執行檔和容器應用程式。
>

1. 在 Eclipse 中，確定已安裝最新版 Eclipse **Neon** 和最新的 Buildship 版本 (1.0.17 或更新版本)。 您可以選擇 [說明] > [安裝詳細資料]，檢查已安裝的元件版本。 您可以使用[這裡][buildship-update]的指示更新 Buildship。
2. 若要安裝 Service Fabric 外掛程式，請選擇 [說明] > [安裝新軟體...]
3. 在 [使用] 文字方塊中，輸入：http://dl.microsoft.com/eclipse
4. 按一下 [新增]。

    ![Eclipse 外掛程式][sf-eclipse-plugin]

5. 選擇 Service Fabric 外掛程式，然後按 [下一步]。
6. 繼續進行安裝並接受使用者授權合約。

如果您已安裝 Service Fabric Eclipse 外掛程式，請確定您使用的是最新版本。 您可以藉由選取 ``Help => Installation Details`` 及在已安裝外掛程式清單中搜尋 Service Fabric 來檢查。 如果有可用的較新版本，請選取 [更新]。

如需詳細資訊，請參閱 [Service Fabric 開始使用 Eclipse](service-fabric-get-started-eclipse.md)。


## 安裝 .NET Core SDK (選擇性，如果您想要使用 .NET Core 程式設計模型)
<a id="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models" class="xliff"></a>
.NET Core SDK 提供了使用 .NET Core 建置 Service Fabric 服務所需的程式庫和範本。

1. 安裝 .NET Core SDK 套件。

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. 執行 SDK 安裝指令碼。

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## 更新 SDK 和執行階段
<a id="updating-the-sdk-and-runtime" class="xliff"></a>

若要更新為最新版的 SDK 和執行階段，請執行下列命令 (取消選取您不想要的 SDK)︰

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
```


> [!NOTE]
> 更新套件可能會導致本機開發叢集停止。 請依照本頁上的指示，在升級之後重新啟動本機叢集。

## 後續步驟
<a id="next-steps" class="xliff"></a>
* [使用 Yeoman 在 Linux 上建立和部署第一個 Service Fabric Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)
* [在 Linux 上使用適用於 Eclipse 的 Service Fabric 外掛程式建立和部署第一個 Service Fabric Java 應用程式](service-fabric-get-started-eclipse.md)
* [在 Linux 上建立第一個 CSharp 應用程式](service-fabric-create-your-first-linux-application-with-csharp.md)
* [在 OSX 上準備您的開發環境](service-fabric-get-started-mac.md)
* [使用 XPlat CLI 管理 Service Fabric 應用程式](service-fabric-azure-cli.md)
* [Service Fabric Windows/Linux 的差異](service-fabric-linux-windows-differences.md)

## 相關文章
<a id="related-articles" class="xliff"></a>

* [開始使用 Service Fabric 和 Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [開始使用 Service Fabric XPlat CLI](service-fabric-azure-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

