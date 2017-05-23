---
title: "建立 Azure Service Fabric 容器應用程式 | Microsoft Docs"
description: "在 Azure Service Fabric 上建立第一個容器應用程式。  使用您的應用程式建置 Docker 映像、將映像推送到容器登錄，建置和部署 Service Fabric 容器應用程式。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: acb68b274228aa647dc7be5d36b2b077bd213c1b
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>建立您的第一個 Service Fabric 容器應用程式
在 Service Fabric 叢集上的 Windows 容器中執行現有的應用程式，不需要變更您的應用程式。 本快速入門會逐步引導您建立包含 Web 應用程式的 Docker 映像、將新的映像推送到 Azure Container Registry、建立 Service Fabric 容器應用程式，以及將容器應用程式部署至 Service Fabric 叢集。  本文假設您對 Docker 有基本認識。 您可藉由閱讀 [Docker 概觀](https://docs.docker.com/engine/understanding-docker/)來了解 Docker。

## <a name="prerequisites"></a>必要條件
執行下列項目的開發電腦︰
* Visual Studio 2015 或 Visual Studio 2017。
* [Service Fabric SDK 和工具](service-fabric-get-started.md)。
*  Docker for Windows。  [取得 Docker CE for Windows (穩定)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)。 安裝並啟動 Docker 之後，以滑鼠右鍵按一下系統匣圖示，然後選取 [切換至 Windows 容器]。 這是執行以 Windows 為基礎的 Docker 映像時的必要動作。

有三個或更多節點在具有容器的 Windows Server 2016 上執行的 Windows 叢集 - [建立叢集](service-fabric-get-started-azure-cluster.md)或[免費試用 Service Fabric](http://tryazureservicefabric.westus.cloudapp.azure.com/)。 

Azure Container Registry 中的登錄 - 在 Azure 訂用帳戶中[建立容器登錄](../container-registry/container-registry-get-started-portal.md)。 

## <a name="create-a-simple-web-app"></a>建立簡單 Web 應用程式
收集您需要在一個地方載入 Docker 映像中的所有資產。 針對此快速入門，在您的開發電腦上建立 "Hello World" Web 應用程式。

1. 建立一個目錄，例如 c:\temp\helloworldapp。
2. 建立一個子目錄 c:\temp\helloworldapp\content。
3. 在 c:\temp\helloworldapp\content 中建立 index.html 檔案。
4. 編輯 index.html 檔案並新增以下這一行：
    ```
    <h1>Hello World!</h1>
    ```
5. 儲存您對 index.html 所做的變更。

## <a name="build-the-docker-image"></a>建置 Docker 映像
根據位於 Docker 中樞的 [microsft/iis 映像](https://hub.docker.com/r/microsoft/iis/)建立映像。 microsoft/iis 映像衍生自 Windows Server Core 基礎 OS 映像，並且包含 Internet Information Services (IIS)。  在您的容器中執行此映像會自動啟動 IIS 和已安裝的網站。

在 Dockerfile 中定義您的 Docker 映像。 Dockerfile 包含建立映像及載入您執行之應用程式的指示。 Dockerfile 是 ```docker build``` 命令的輸入，該命令可建立映像。 

1. 在 c:\temp\helloworldapp 中建立 Dockerfile 檔案 (不含副檔名) 並新增下列程式碼︰

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    此 Dockerfile 中沒有 ```ENTRYPOINT``` 命令。 您不需要此命令。 使用 IIS 執行 Windows Server 時，IIS 處理序是進入點，其已設定為在基底映像中啟動。

    如需詳細資訊，請參閱 [Dockerfile 參考](https://docs.docker.com/engine/reference/builder/)。

2. 執行 ```docker build``` 命令來建立可執行 Web 應用程式的映像。 開啟 PowerShell 視窗並瀏覽至 *c:\temp\helloworldapp*。 執行以下命令：

    ```
    docker build -t helloworldapp .
    ```
    此命令會使用 Dockerfile 中的指示建立新映像，將此映像命名 (-t 標記) 為 "helloworldapp"。 建置映像時，會從 Docker 中樞向下提取基底映像，並根據基底映像建立可新增應用程式的新映像。  [microsft/iis 映像](https://hub.docker.com/r/microsoft/iis/)和 OS 基底映像均為 10.5 GB，需要時間下載並解壓縮至您的開發電腦。  就像是選擇要外出吃午餐或只是喝杯咖啡一樣。  如果您先前已將基礎 OS 映像提取至開發電腦，下載所需的時間比較少。

3. 建置命令完成後，執行 `docker images` 命令以查看新映像的資訊︰

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>確認映像在本機執行
先確認您的映像在本機，再將它推送至容器登錄。  

1. 使用 ```docker run``` 啟動容器：

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    name - 提供執行中容器的名稱 (而不是容器識別碼)。

2. 容器啟動後，尋找其 IP 位址，以便您從瀏覽器連線到執行中的容器︰
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. 連線到執行中的容器。  開啟 Web 瀏覽器並指向連接埠 8000 上傳回的 IP 位址，例如 " http://172.31.194.61:8000 "。 您應該會看到 "Hello World!" 標題 顯示在瀏覽器中。

4. 若要停止您的容器，請執行︰

    ```
    docker stop my-web-site
    ```

5. 從您的開發電腦刪除容器：

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>將映像推送至容器登錄
確認容器在開發電腦上執行後，將映像發送到 Azure Container Registry 中您的登錄。

1. 使用您的[登錄認證](../container-registry/container-registry-authentication.md)執行 ``docker login`` 登入容器登錄庫。

    下列範例會傳遞 Azure Active Directory [service principal](../active-directory/active-directory-application-objects.md) 的識別碼和密碼。 例如，您可能基於自動化案例已指派服務主體到您的登錄庫。

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. 下列命令會建立映像的標籤或別名，包含登錄的完整路徑。 這個範例會指定 ```samples``` 命名空間中的映像，以避免登錄根目錄雜亂。

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  將映像推送至容器登錄：

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>在 Visual Studio 中建立並封裝容器化服務
Service Fabric SDK 和工具會提供一個服務範本，協助您將容器部署到 Service Fabric 叢集。

1. 啟動 Visual Studio。  選取 [檔案] > [新增] > [專案]。
2. 選取 [Service Fabric 應用程式]，將它命名為 "MyFirstContainer"，然後按一下 [確定]。
3. 從 [服務範本] 的清單中選取 [來賓容器]。
4. 在 [映像名稱] 中輸入 "myregistry.azurecr.io/samples/helloworldapp"，也就是您推送至容器存放庫的映像。 
5. 指定服務的名稱，然後按一下 [確定]。
6. 如果您的容器化服務需要一個端點來進行通訊，現在可以將通訊協定、連接埠及類型新增至 ServiceManifest.xml 檔案中的 ```Endpoint```。 在此快速入門中，容器化服務會接聽連接埠 80： 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    提供 ```UriScheme```，就會自動向「Service Fabric 命名」服務註冊容器端點以供搜尋。 本文結尾會提供完整的 ServiceManifest.xml 範例檔案。 
7. 在 ApplicationManifest.xml 檔案的 ```ContainerHostPolicies``` 中指定 ```PortBinding```，以設定容器連接埠與主機連接埠的對應。  在此快速入門中，```ContainerPort``` 為 8000 (如 Dockerfile 所指定，容器會公開連接埠 8000)，而 ```EndpointRef``` 為 "Guest1TypeEndpoint" (服務資訊清單中定義的端點)。  通訊埠 80 上服務的連入要求會對應到容器上的連接埠 8000。  如果您的容器需要向私人存放庫進行驗證，則新增 ```RepositoryCredentials```。  在此快速入門中，新增 myregistry.azurecr.io 容器登錄的帳戶名稱和密碼。 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    本文結尾會提供完整的 ApplicationManifest.xml 範例檔案。
8. 設定叢集連線端點，以便您將應用程式發佈至叢集。  在 [Azure 入口網站](https://portal.azure.com)中，您可以在叢集的 [概觀] 刀鋒視窗中找到用戶端連線端點。 在 [方案總管] 中，開啟 [MyFirstContainer]->[PublishProfiles] 之下的 Cloud.xml。  將叢集名稱和連接埠新增至 **ClusterConnectionParameters**。  例如：
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. 儲存所有檔案並建立您的專案。  

10. 若要封裝您的應用程式，以滑鼠右鍵按一下 [方案總管] 中的 **MyFirstContainer**，然後選取 [封裝]。 

## <a name="deploy-the-container-app"></a>部署容器應用程式
1. 若要發佈您的應用程式，以滑鼠右鍵按一下 [方案總管] 中的 **MyFirstContainer**，然後選取 [發佈]。

2. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 是一個 Web 型工具，可檢查和管理 Service Fabric 叢集中的應用程式與節點。 開啟瀏覽器並瀏覽至 http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/，然後遵循應用程式部署。  此應用程式會部署，但在映像下載於叢集節點之前 (視映像大小而定，這可能需要一些時間) 會處於錯誤狀態︰ ![錯誤][1]

3. 應用程式處於 ```Ready``` 狀態時便已準備就緒︰ ![就緒][2]

4. 開啟瀏覽器並瀏覽至 http://containercluster.westus2.cloudapp.azure.com。 您應該會看到 "Hello World!" 標題 顯示在瀏覽器中。

## <a name="clean-up"></a>清除
當叢集在執行時，您需要繼續支付費用，請考慮[刪除您的叢集](service-fabric-get-started-azure-cluster.md#remove-the-cluster)。  [合作對象叢集](http://tryazureservicefabric.westus.cloudapp.azure.com/)會在幾個小時後自動刪除。

將映像推送至容器登錄之後，您可以從開發電腦刪除本機映像︰

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>完整範例 Service Fabric 應用程式和服務資訊清單
以下是本快速入門中使用的完整服務和應用程式資訊清單。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>後續步驟
* 深入了解如何[在 Service Fabric 上執行容器](service-fabric-containers-overview.md)。
* 深入了解 Service Fabric [應用程式生命週期](service-fabric-application-lifecycle.md)。
* 請查看 GitHub 上的[ Service Fabric 容器程式碼範例](https://github.com/Azure-Samples/service-fabric-dotnet-containers)。

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

