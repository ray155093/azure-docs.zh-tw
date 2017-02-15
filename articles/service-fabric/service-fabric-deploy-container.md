---
title: "Service Fabric 和部署容器 | Microsoft Docs"
description: "Service Fabric 及使用容器來部署微服務應用程式。 本文說明 Service Fabric 為容器提供的功能，以及如何將 Windows 容器映像部署至叢集。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 799cc9ad-32fd-486e-a6b6-efff6b13622d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2016
ms.author: mfussell
translationtype: Human Translation
ms.sourcegitcommit: af9f761179896a1acdde8e8b20476b7db33ca772
ms.openlocfilehash: 1c5f3bc66c902c3b7186cad44728fa5237dd298a


---
# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>預覽︰將 Windows 容器部署至 Service Fabric
> [!div class="op_single_selector"]
> * [部署 Windows 容器](service-fabric-deploy-container.md)
> * [部署 Docker 容器](service-fabric-deploy-container-linux.md)
> 
> 

本文將引導您在 Windows 容器中建置容器化服務的程序。

> [!NOTE]
> 這項功能在 Linux 上處於預覽階段，在 Windows Server 2016 上尚無法使用。 在即將發行的 Azure Service Fabric 中，此功能會是的 Windows Server 2016 的預覽階段功能。 
> 
> 

Service Fabric 有數個容器功能可協助您建置由容器化微服務組成的應用程式。 

功能包括：

* 容器映像部署和啟用
* 資源管理
* 儲存機制驗證
* 容器連接埠至主機連接埠的對應
* 容器至容器的探索及通訊
* 能夠設定環境變數

讓我們看看將容器化服務封裝納入應用程式時，每項功能如何運作。

## <a name="package-a-windows-container"></a>封裝 Windows 容器
在封裝容器時，您可以選擇使用 Visual Studio 專案範本，或是[手動建立應用程式套件](#manually)。 當您使用 Visual Studio 時，就可讓 [新增專案範本] 為您建立應用程式套件的結構和資訊清單檔案。 未來的版本將會發行 VS 範本。

## <a name="use-visual-studio-to-package-an-existing-container-image"></a>使用 Visual Studio 封裝現有容器映像
> [!NOTE]
> 在即將發行的 Service Fabric 適用的Visual Studio 中，您能夠將容器新增至應用程式，就像您現在新增來賓執行檔一樣。 如需詳細資訊，請參閱[將來賓執行檔部署至 Service Fabric](service-fabric-deploy-existing-app.md) 主題。 目前，您必須如下一節所述手動封裝容器。
> 
> 

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container"></a>手動封裝和部署容器
手動封裝容器化服務的程序是基於下列步驟：

1. 將容器發佈至儲存機制。
2. 建立封裝目錄結構。
3. 編輯服務資訊清單檔。
4. 編輯應用程式資訊清單檔。

## <a name="deploy-and-activate-a-container-image"></a>部署和啟用容器映像
在 Service Fabric [應用程式模型](service-fabric-application-model.md)中，容器代表多個服務複本所在的應用程式主機。 若要部署和啟用容器，請在服務資訊清單的 `ContainerHost` 元素中輸入容器映像的名稱。

在服務資訊清單中，新增 `ContainerHost` 作為進入點。 然後將 `ImageName` 設為容器儲存機制和映像的名稱。 下列的局部資訊清單示範如何從名為 `myrepo` 的儲存機制，部署名為 `myimage:v1` 的容器：

```xml
    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimage:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>
```

您可以指定選擇性 `Commands` 元素，以及一組要在容器內執行的命令 (以逗號分隔)，以提供輸入命令。

## <a name="understand-resource-governance"></a>了解資源管理
資源管理是容器的功能，可限制容器在主機上可使用的資源。 應用程式資訊清單中指定的 `ResourceGovernancePolicy` 是用於宣告服務程式碼套件的資源限制。 可為以下資源設定限制：

* 記憶體
* MemorySwap
* CpuShares (CPU 相對權數)
* MemoryReservationInMB  
* BlkioWeight (BlockIO 相對權數)。

> [!NOTE]
> 在未來版本中，會加入支援指定特定的區塊 IO 限制，例如 IOP、讀取/寫入 BPS 及其他限制。
> 
> 

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500"
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="authenticate-a-repository"></a>驗證儲存機制
若要下載容器，您可能必須提供容器儲存機制的登入認證。 於應用程式資訊清單中指定的登入認證，是用於指定從映像儲存機制下載容器映像所需的登入資訊 (或 SSH 金鑰)。 下列範例顯示名為 TestUser  的帳戶及純文字密碼 (「不」建議使用)：

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

我們建議使用部署至電腦的憑證將密碼加密。

下列範例顯示名為 TestUser 的帳戶，其密碼以名為 MyCert 的憑證加密。 您可以使用 `Invoke-ServiceFabricEncryptText` Powershell 命令來建立密碼的加密文字。 如需詳細資訊，請參閱[管理 Service Fabric 應用程式中的密碼](service-fabric-application-secret-management.md)。

用於解密密碼的憑證私密金鑰必須以頻外方法部署到本機電腦。 (在 Azure 中，這個方法就是 Azure Resource Manager。)然後，當 Service Fabric 將服務套件部署到電腦時，它可以解密密碼。 搭配使用帳戶名稱和密碼，之後可以用它向容器儲存機制提供驗證。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping"></a>設定容器連接埠至主機連接埠的對應
您可以在應用程式資訊清單中指定 `PortBinding`，以設定用來與容器通訊的主機連接埠。 連接埠繫結會將服務在容器內接聽的連接埠，對應至主機上的連接埠。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-to-container-discovery-and-communication"></a>設定容器至容器的探索及通訊
使用 `PortBinding` 原則，可以將容器連接埠對應至服務資訊清單中的 `Endpoint`，如下列範例所示。 端點 `Endpoint1` 可以指定固定通訊埠 (例如，連接埠 80)。 也可以完全不指定連接埠，在此情況下，將從叢集的應用程式連接埠範圍內為您選擇隨機連接埠。

如果您在來賓容器的服務資訊清單中使用 `Endpoint` 標記指定端點，Service Fabric 可以自動將此端點發佈至命名服務。 因此，在叢集中執行的其他服務都可以使用用於解析的 REST 查詢找到這個容器。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

向命名服務註冊，可讓您輕易地在容器內的程式碼中，利用[反向 Proxy](service-fabric-reverseproxy.md) 執行容器對容器的通訊。 將 http 接聽連接埠和您想要與之通訊的服務名稱提供給反向 Proxy，並將這些都設為環境變數，便可進行通訊。 如需詳細資訊，請參閱下一節。 

## <a name="configure-and-set-environment-variables"></a>設定環境變數
針對部署在容器中的服務，或部署為程序/來賓執行檔的服務，您可以在服務資訊清單中為每個程式碼套件指定環境變數。 這些環境變數的值可以在應用程式資訊清單中明確覆寫，或在部署期間指定為應用程式參數。

下列服務資訊清單 XML 程式碼片段示範如何指定程式碼封裝的環境變數：

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>
```

在應用程式資訊清單層級上可覆寫這些環境變數︰

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>
```

在以上範例中，我們為 `HttpGateway` 環境變數指定明確的值 (19000)，而 `BackendServiceName` 參數的值是透過 `[BackendSvc]` 應用程式參數來設定。 這些設定可讓您在部署應用程式時指定 `BackendServiceName` 的值，而在資訊清單中沒有固定的值。

## <a name="complete-examples-for-application-and-service-manifest"></a>應用程式和服務資訊清單的完整範例

以下是應用程式資訊清單：

```xml
    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>
```

以下是服務資訊清單 (在之前的應用程式資訊清單中指定) 的範例：

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless front end in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Endpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>後續步驟
現在您已部署容器化的服務，可以開始了解如何讀取 [Service Fabric 應用程式生命週期](service-fabric-application-lifecycle.md)並管理其生命週期。




<!--HONumber=Nov16_HO3-->


