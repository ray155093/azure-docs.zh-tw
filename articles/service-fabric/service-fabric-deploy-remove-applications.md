---
title: "Service Fabric 應用程式部署 | Microsoft Docs"
description: "如何部署和移除 Service Fabric 中的應用程式"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: dbd4dd3cadf162ea18d58639d31589f7b9b8efc3
ms.openlocfilehash: 2dfdcd08501a63d62ec6ba565d1abc7d42c8c680
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-and-remove-applications-using-powershell"></a>使用 PowerShell 部署與移除應用程式
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

[封裝應用程式類型][10]後，即可將它部署至 Azure Service Fabric 叢集中。 部署涉及下列三個步驟：

1. 將應用程式封裝上傳至映像存放區
2. 註冊應用程式類型
3. 建立應用程式執行個體

在叢集中部署應用程式且執行個體開始執行之後，您就可以刪除應用程式執行個體和其應用程式類型。 從叢集完全移除應用程式需要執行下列步驟︰

1. 移除 (或刪除) 執行中的應用程式執行個體
2. 取消註冊不再需要的應用程式類型
3. 移除映像存放區中的應用程式封裝

如果您在本機開發叢集上使用 [Visual Studio 部署和偵錯應用程式](service-fabric-publish-app-remote-cluster.md)，則先前所有步驟都會透過 PowerShell 指令碼自動處理。  在應用程式專案的 [指令碼] 資料夾中可找到這個指令碼。 本文提供該指令碼的背景資料，讓您可以在 Visual Studio 之外執行相同的作業。 
 
## <a name="connect-to-the-cluster"></a>連接到叢集
執行本文中的任何 PowerShell 命令之前，請一律透過使用 [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster) 連接至 Service Fabric 叢集的方式啟動。 若要連線至本機開發叢集，請執行下列命令︰

```powershell
PS C:\>Connect-ServiceFabricCluster
```

針對連線至遠端叢集，或連線至使用 Azure Active Directory、X509 憑證或 Windows Active Directory 保護的叢集，如需相關的範例，請參閱[連線至安全的叢集](service-fabric-connect-to-secure-cluster.md)。

## <a name="upload-the-application-package"></a>上傳應用程式封裝
上傳應用程式封裝會將它放在一個可由內部 Service Fabric 元件存取的位置。 如果您想要在本機確認應用程式套件，使用 [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) cmdlet。  [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) 命令會將應用程式封裝上傳至叢集映像存放區。 **Get ImageStoreConnectionStringFromClusterManifest** Cmdlet 是 Service Fabric SDK PowerShell 模組的一部分，可用來取得映像存放區連接字串。  若要匯入 SDK 模組，請執行︰

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

假設您在 Visual Studio 中建置並封裝名為 *MyApplication* 的應用程式。 根據預設，ApplicationManifest.xml 中列出的應用程式類型名稱會是 "MyApplicationType"。  應用程式封裝 (其中包含必要的應用程式資訊清單、服務資訊清單和程式碼/組態/資料封裝) 位於 *C:\Users\username\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*。 

下列命令會列出應用程式封裝的內容︰

```powershell
PS C:\> tree /f 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

下列範例會將套件上傳至映像存放區中的 "MyApplicationV1" 資料夾︰

```powershell
PS C:\> $path = 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

如果您未指定 *-ApplicationPackagePathInImageStore* 參數，應用程式封裝會複製到映像存放區中的 "Debug" 資料夾。

請參閱[了解映像存放區連接字串](service-fabric-image-store-connection-string.md)，以取得有關映像存放區和映像存放區連接字串的補充資訊。

## <a name="register-the-application-package"></a>註冊應用程式封裝
註冊應用程式封裝時，應用程式類型和應用程式資訊清單中宣告的版本可供使用。 系統會讀取在上一個步驟上傳的套件，請確認套件、處理套件內容，然後將處理過的套件複製至內部系統位置。  

執行 [Register-servicefabricapplicationtype](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) Cmdlet，將應用程式類型註冊在叢集，使它可供部署使用︰

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" 是應用程式封裝所在映像存放區中的資料夾。 名稱為 "MyApplicationType" 和版本為 "1.0.0" (兩者都在應用程式資訊清單中) 的應用程式類型，現在已註冊在叢集中。

[Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) 命令只有在系統成功註冊應用程式封裝之後才會返回。 註冊所需時間取決於應用程式封裝的大小和內容。 **-TimeoutSec** 參數可在必要時用來提供較長的逾時 (預設逾時為 60 秒)。  如果這是大型應用程式封裝且您遇到逾時，使用 **-Async** 參數。

[Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) 命令會列出所有成功註冊的應用程式類型版本和其註冊狀態。

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="create-the-application"></a>建立應用程式
您可以使用 [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication) Cmdlet，從任何已成功註冊的應用程式類型版本，將應用程式具現化。 每個應用程式名稱的開頭必須為 *fabric:* 配置，並且是每個應用程式執行個體的唯一名稱。 如果已在目標應用程式類型的應用程式資訊清單中定義預設服務，也會一併建立這些服務。

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
多個應用程式執行個體可以針對任何指定的已註冊應用程式類型版本來建立。 每個應用程式執行個體將在隔離狀態下執行，包含本身的工作目錄和程序。

若要查看哪些具名的應用程式和服務正在叢集中執行，請執行 [Get-servicefabricapplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) 和 [Get-servicefabricservice](/powershell/servicefabric/vlatest/get-servicefabricservice) Cmdlet︰

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>移除應用程式
當不再需要應用程式執行個體時，您可以使用 [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) Cmdlet，依名稱永久移除它。 [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) 也會自動移除屬於應用程式的所有服務，永久地移除所有服務狀態。 此作業無法回復，且應用程式狀態無法復原。

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>取消註冊應用程式類型
當不再需要應用程式類型的特定版本時，您應該使用 [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) Cmdlet 取消註冊該應用程式類型。 取消註冊未使用的應用程式類型會釋放映像存放區已使用的儲存空間。 只要沒有對應的應用程式針對應用程式類型進行具現化，且沒有擱置中的應用程式升級進行參考該類性，便可取消註冊該應用程式類型。

執行 [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) Cmdlet，查看目前註冊在叢集中的應用程式類型：

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

執行 [Unregister-servicefabricapplicationtype](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype)，取消註冊特定應用程式類型︰

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```
## <a name="remove-an-application-package-from-the-image-store"></a>從映像存放區移除應用程式封裝
當不再需要應用程式封裝時，您可以從映像存放區刪除它，以釋放系統資源。

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

## <a name="troubleshooting"></a>疑難排解
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage 要求 ImageStoreConnectionString
Service Fabric SDK 環境應已正確設定預設值。 但若有需要，所有命令的 ImageStoreConnectionString都應符合 Service Fabric 叢集正在使用的值。 您可以在使用 [Get-servicefabricclustermanifest](/powershell/servicefabric/vlatest/get-servicefabricclustermanifest) 命令所擷取的叢集資訊清單中，找到 ImageStoreConnectionString：

```powershell
PS C:\> Get-ServiceFabricClusterManifest
```

會在叢集資訊清單中找到 ImageStoreConnectionString：

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

## <a name="next-steps"></a>後續步驟
[Service Fabric 應用程式升級](service-fabric-application-upgrade.md)

[Service Fabric 健康狀態簡介](service-fabric-health-introduction.md)

[診斷和疑難排解 Service Fabric 服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[在 Service Fabric 中模型化應用程式](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

