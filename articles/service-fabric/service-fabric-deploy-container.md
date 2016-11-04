---
title: Service Fabric 和部署容器 | Microsoft Docs
description: Service Fabric 及使用容器來部署微服務應用程式。 本文說明 Service Fabric 為容器提供的功能，以及如何將容器映像部署至叢集
services: service-fabric
documentationcenter: .net
author: msfussell
manager: ''
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: msfussell

---
# <a name="preview:-deploy-a-container-to-service-fabric"></a>預覽︰將容器部署至 Service Fabric
> [!NOTE]
> 這項功能在 Linux 上處於預覽階段，在 Windows Server 上目前無法使用。 在 Windows Server 2016 GA 之後的下一版 Service Fabric 上，這將於 Windows Server 中進入預覽階段，之後在後續版本中就會開始支援。
> 
> 

Service Fabric 有數個容器功能可協助您建置由容器化微服務組成的應用程式。 這些稱為容器化服務。 功能包括：

* 容器映像部署和啟用
* 資源管理
* 儲存機制驗證
* 容器連接埠至主機連接埠的對應
* 容器至容器的探索及通訊
* 能夠設定環境變數

讓我們逐一檢視將容器化服務封裝納入到應用程式時的每一項功能。

## <a name="packaging-a-container"></a>封裝容器
在封裝容器時，您可以選擇使用 Visual Studio 專案範本，或是 [手動建立應用程式封裝](#manually)。 使用 Visual Studio，就可讓新增專案精靈為您建立應用程式封裝的結構和資訊清單檔案。

## <a name="using-visual-studio-to-package-an-existing-executable"></a>使用 Visual Studio 封裝現有的可執行檔
> [!NOTE]
> 在 Visual Studio 工具 SDK 的未來版本中，您將能夠將容器新增至應用程式，就好像現在新增來賓可執行檔一樣。 請參閱 [將來賓可執行檔部署至 Service Fabric](service-fabric-deploy-existing-app.md) 主題。 目前，您必須執行手動封裝，如下所述。
> 
> 

<a id="manually"></a>

## <a name="manually-packaging-and-deploying-container"></a>手動封裝和部署容器
手動封裝容器化服務的程序是基於下列步驟：

1. 將容器發佈至儲存機制。
2. 建立封裝目錄結構。
3. 編輯服務資訊清單檔。
4. 編輯應用程式資訊清單檔。

## <a name="container-image-deployment-and-activation."></a>容器映像部署和啟用。
在 Service Fabric [應用程式模型](service-fabric-application-model.md)中，容器代表多個服務複本所在的應用程式主機。 若要部署和啟用容器，請在服務資訊清單的 `ContainerHost` 元素中輸入容器映像的名稱。

在服務資訊清單中，新增 `ContainerHost` 作為進入點，並將 `ImageName` 設為容器儲存機制和映像的名稱。 下列局部的資訊清單示範如何從稱為 *myrepo* 的儲存機制，部署稱為 *myimage:v1* 的容器

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

您可以指定選擇性 `Commands` 元素，以及一組要在容器內執行的命令 (以逗號分隔)，以提供輸入命令給容器映像。 

## <a name="resource-governance"></a>資源管理
資源控管是容器的功能，可限制容器在主機上可使用的資源。 應用程式資訊清單中指定的 `ResourceGovernancePolicy`可讓您宣告服務程式碼封裝的資源限制。 可設定的資源限制如下：

* 記憶體
* MemorySwap
* CpuShares (CPU 相對權數)
* MemoryReservationInMB  
* BlkioWeight (BlockIO 相對權數)。 

> [!NOTE]
> 在未來版本中，可能會支援指定特定的區塊 IO 限制，例如 IOP、讀取/寫入 BPS 及其他限制。
> 
> 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>儲存機制驗證
若要下載容器，您可能必須向容器儲存機制提供登入認證。 application  資訊清單中指定的登入認證，用於指定從映像儲存機制下載容器映像所需的登入資訊或 SSH 金鑰。  下列範例顯示名為 TestUser  的帳戶及純文字密碼。 **不** 建議使用。

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

密碼可以且應該使用部署至電腦的憑證來加密。

下列範例顯示名為 *TestUser* 的帳戶，其密碼以稱為 *MyCert* 的憑證加密。 您可以使用 `Invoke-ServiceFabricEncryptText` Powershell 命令來建立密碼的加密文字。 相關作法的詳細資訊，請參閱 [管理 Service Fabric 應用程式中的密碼](service-fabric-application-secret-management.md) 一文。 將密碼解密的憑證私密金鑰必須以頻外方法部署到本機電腦 (在 Azure 中，這是透過 Resource Manager)。 然後，當 Service Fabric 將服務封裝部署到電腦時，它就能夠將密碼解密，並連同使用者名稱，利用這些認證向容器儲存機制進行驗證。

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
            <SecurityAccessPolicies>
                <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
            </SecurityAccessPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>容器連接埠至主機連接埠的對應
您可以在應用程式資訊清單中指定 `PortBinding` ，以設定用來與容器通訊的主機連接埠。 連接埠繫結會將服務在容器內接聽的連接埠，對應至主機上的連接埠

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>容器至容器的探索及通訊
您可以使用 `PortBinding` 原則，將容器連接埠對應至服務資訊清單中的 `Endpoint`，如下列範例所示。 端點 `Endpoint1` 可以指定固定通訊埠，例如連接埠 80，或完全不指定連接埠，在此情況下，將從叢集的應用程式連接埠範圍內為您選擇隨機連接埠。

對於來賓容器而言，像這樣在服務資訊清單中指定 `Endpoint` ，可讓 Service Fabric 自動將此端點發佈到命名服務，以便叢集中執行的其他服務可以利用解析服務 REST 查詢來探索這個容器。 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

向命名服務註冊，可讓您輕易地在容器內的程式碼中，利用 [反向 Proxy](service-fabric-reverseproxy.md)來執行容器對容器的通訊。 您只需要將 http 接聽連接埠和您想要與之通訊的服務名稱提供給反向 Proxy，並將這些都設為環境變數即可。 相關作法請參閱下一節。  

## <a name="configure-and-set-environment-variables"></a>設定環境變數
針對部署在容器中的服務，或部署為處理序/來賓可執行檔的服務，您可以在服務資訊清單中為每個程式碼封裝指定環境變數。 這些環境變數的值可以在應用程式資訊清單中明確覆寫，或在部署期間指定為應用程式參數。

下列服務資訊清單 XML 程式碼片段示範如何指定程式碼封裝的環境變數。 

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

在應用程式資訊清單層級上可覆寫這些環境變數︰

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

在上述範例中，我們為 `HttpGateway` 環境變數指定明確的值 (19000)，而 `BackendServiceName` 參數的值是透過 `[BackendSvc]` 應用程式參數來設定。 這可讓您在部署應用程式時指定 `BackendServiceName`的值，而在資訊清單中沒有固定的值。 

## <a name="complete-examples-for-application-and-service-manifest"></a>應用程式和服務資訊清單的完整範例
以下是顯示容器功能的應用程式資訊清單範例。

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


以下是顯示容器功能的服務資訊清單範例 (在先前的應用程式資訊清單中指定)

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
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
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>



<!--HONumber=Oct16_HO2-->


