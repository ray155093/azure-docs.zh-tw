---
title: "建立 Azure Service Fabric 容器應用程式 | Microsoft Docs"
description: "在 Azure Service Fabric 上建立第一個 Windows 容器應用程式。  使用 Python 應用程式建置 Docker 映像、將映像推送到容器登錄，建置和部署 Service Fabric 容器應用程式。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 8c9d6c65666b5ffedf058e0a83d4fc41fff80235
ms.contentlocale: zh-tw
ms.lasthandoff: 07/15/2017

---

# <a name="create-your-first-service-fabric-container-application-on-windows"></a>在 Windows 建立第一個 Service Fabric 容器應用程式
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

在 Service Fabric 叢集上的 Windows 容器中執行現有的應用程式，不需要變更您的應用程式。 本文會逐步引導您建立包含 Python [Flask](http://flask.pocoo.org/) Web 應用程式的 Docker 映像，並將它部署到 Service Fabric 叢集。  您也將透過 [Azure Container Registry](/azure/container-registry/) 共用容器化應用程式。  本文假設您對 Docker 有基本認識。 您可藉由閱讀 [Docker 概觀](https://docs.docker.com/engine/understanding-docker/)來了解 Docker。

## <a name="prerequisites"></a>必要條件
執行下列項目的開發電腦︰
* Visual Studio 2015 或 Visual Studio 2017。
* [Service Fabric SDK 和工具](service-fabric-get-started.md)。
*  Docker for Windows。  [取得 Docker CE for Windows (穩定)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)。 安裝並啟動 Docker 之後，以滑鼠右鍵按一下系統匣圖示，然後選取 [切換至 Windows 容器]。 這是執行以 Windows 為基礎的 Docker 映像時的必要動作。

有三個或更多節點在具有容器的 Windows Server 2016 上執行的 Windows 叢集 - [建立叢集](service-fabric-cluster-creation-via-portal.md)或[免費試用 Service Fabric](https://aka.ms/tryservicefabric)。 

Azure Container Registry 中的登錄 - 在 Azure 訂用帳戶中[建立容器登錄](../container-registry/container-registry-get-started-portal.md)。 

## <a name="define-the-docker-container"></a>定義 Docker 容器
根據位於 Docker 中樞的 [Python 映像](https://hub.docker.com/_/python/)建立映像。 

在 Dockerfile 中定義您的 Docker 容器。 Dockerfile 包含下列相關指示：設定您容器內的環境、載入您要執行的應用程式，以及對應連接埠。 Dockerfile 是 `docker build` 命令的輸入，該命令可建立映像。 

建立空的目錄並建立 Dockerfile 檔案 (沒有副檔名)。 將下列內容新增至 Dockerfile 並儲存變更：

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

如需詳細資訊，請參閱 [Dockerfile 參考](https://docs.docker.com/engine/reference/builder/)。

## <a name="create-a-simple-web-application"></a>建立簡單 Web 應用程式
建立 Flask Web 應用程式，其會在連接埠 80 上接聽並傳回 "Hello World!"。  在相同的目錄中，建立 requirements.txt 檔案。  新增下列內容並儲存變更：
```
Flask
```

此外，建立 app.py 檔案並新增下列內容：

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>建立映像
執行 `docker build` 命令來建立可執行 Web 應用程式的映像。 開啟 PowerShell 視窗，然後瀏覽至包含 Dockerfile 的目錄。 執行以下命令：

```
docker build -t helloworldapp .
```

此命令會使用 Dockerfile 中的指示建立新映像，將此映像命名 (-t 標記) 為 "helloworldapp"。 建置映像時，會從 Docker 中樞向下提取基底映像，並根據基底映像建立可新增應用程式的新映像。  

建置命令完成後，執行 `docker images` 命令以查看新映像的資訊︰

```
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>在本機執行應用程式
先確認您的映像在本機，再將它推送至容器登錄。  

執行應用程式：

```
docker run -d --name my-web-site helloworldapp
```

name - 提供執行中容器的名稱 (而不是容器識別碼)。

容器啟動後，尋找其 IP 位址，以便您從瀏覽器連線到執行中的容器︰
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

連線到執行中的容器。  開啟 Web 瀏覽器並指向傳回的 IP 位址，例如 "http://172.31.194.61"。 您應該會看到 "Hello World!" 標題 顯示在瀏覽器中。

若要停止您的容器，請執行︰

```
docker stop my-web-site
```

從您的開發電腦刪除容器：

```
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>將映像推送至容器登錄
確認容器在開發電腦上執行後，將映像發送到 Azure Container Registry 中您的登錄。

使用您的[登錄認證](../container-registry/container-registry-authentication.md)執行 ``docker login`` 登入容器登錄庫。

下列範例會傳遞 Azure Active Directory [service principal](../active-directory/active-directory-application-objects.md) 的識別碼和密碼。 例如，您可能基於自動化案例已指派服務主體到您的登錄庫。 或者，您可以使用登錄使用者名稱和密碼進行登入。

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

下列命令會建立映像的標籤或別名，包含登錄的完整路徑。 這個範例會指定 ```samples``` 命名空間中的映像，以避免登錄根目錄雜亂。

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

將映像推送至容器登錄：

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
6. 如果您的容器化服務需要一個端點來進行通訊，現在可以將通訊協定、連接埠及類型新增至 ServiceManifest.xml 檔案中的 ```Endpoint```。 在本文中，容器化服務會接聽連接埠 80： 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    ```
    提供 ```UriScheme```，就會自動向「Service Fabric 命名」服務註冊容器端點以供搜尋。 本文結尾會提供完整的 ServiceManifest.xml 範例檔案。 
7. 在 ApplicationManifest.xml 檔案的 ```ContainerHostPolicies``` 中指定 ```PortBinding```，以設定容器連接埠與主機連接埠的對應。  在本文中，```ContainerPort``` 為 8081 (如 Dockerfile 所指定，容器會公開連接埠 80)，而 ```EndpointRef``` 為 "Guest1TypeEndpoint" (服務資訊清單中定義的端點)。  通訊埠 8081 上服務的連入要求會對應到容器上的連接埠 80。  如果您的容器需要向私人存放庫進行驗證，則新增 ```RepositoryCredentials```。  在本文中，新增 myregistry.azurecr.io 容器登錄的帳戶名稱和密碼。 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
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

## <a name="deploy-the-container-application"></a>部署容器應用程式
若要發佈您的應用程式，以滑鼠右鍵按一下 [方案總管] 中的 **MyFirstContainer**，然後選取 [發佈]。

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 是一個 Web 型工具，可檢查和管理 Service Fabric 叢集中的應用程式與節點。 開啟瀏覽器並瀏覽至 http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/，然後遵循應用程式部署。  此應用程式會進行部署，但在叢集節點中下載映像之前 (視映像大小而定，這可能需要一些時間) 會處於錯誤狀態︰![錯誤][1]

應用程式處於 ```Ready``` 狀態時便已準備就緒︰![就緒][2]

開啟瀏覽器並瀏覽至 http://containercluster.westus2.cloudapp.azure.com:8081。 您應該會看到 "Hello World!" 標題 顯示在瀏覽器中。

## <a name="clean-up"></a>清除
當叢集在執行時，您需要繼續支付費用，請考慮[刪除您的叢集](service-fabric-get-started-azure-cluster.md#remove-the-cluster)。  [合作對象叢集](http://tryazureservicefabric.westus.cloudapp.azure.com/)會在幾個小時後自動刪除。

將映像推送至容器登錄之後，您可以從開發電腦刪除本機映像︰

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>完整範例 Service Fabric 應用程式和服務資訊清單
以下是本文中使用的完整服務和應用程式資訊清單。

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
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
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
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
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
* 閱讀[在容器中部署 .NET 應用程式](service-fabric-host-app-in-a-container.md)教學課程。
* 深入了解 Service Fabric [應用程式生命週期](service-fabric-application-lifecycle.md)。
* 請查看 GitHub 上的[ Service Fabric 容器程式碼範例](https://github.com/Azure-Samples/service-fabric-dotnet-containers)。

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

