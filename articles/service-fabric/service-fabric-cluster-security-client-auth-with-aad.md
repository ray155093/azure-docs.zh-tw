
<properties
   pageTitle="Service Fabric 叢集安全性：使用 Azure Active Directory 的用戶端驗證 | Microsoft Azure"
   description="本文說明如何建立使用 Azure Active Directory (AAD) 進行用戶端驗證的 Service Fabric 叢集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/13/2016"
   ms.author="seanmck"/>

# 建立使用 Azure Active Directory 進行用戶端驗證的 Service Fabric 叢集

您可以為使用 Azure Active Directory (AAD) 的 Service Fabric 叢集，保護其管理端點的存取。本文涵蓋如何建立必要 AAD 構件、如何在叢集建立期間填入它們，以及之後如何連線到這些叢集。

## 在 AAD 中建立 Service Fabric 叢集的模型

AAD 可讓組織 (稱為租用戶) 管理使用者對應用程式的存取，這分成具有 Web 型登入 UI 的應用程式以及具有原生用戶端體驗的應用程式。在本文中，假設您已經建立租用戶。否則，請從閱讀[如何取得 Azure Active Directory 租用戶](../active-directory/active-directory-howto-tenant.md)開始進行。

Service Fabric 叢集提供其管理功能的各種進入點 (包括 Web 型 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 和 [Visual Studio](service-fabric-manage-application-in-visual-studio.md))。因此，您將建立兩個 AAD 應用程式來控制對叢集的存取：一個 Web 應用程式和一個原生應用程式。

為了簡化與設定 AAD 與 Service Fabric 叢集相關的一些步驟，我們建立了一組 Windows PowerShell 指令碼。

>[AZURE.NOTE] 您必須在建立叢集「之前」執行這些步驟；因此，在指令碼預期叢集名稱和端點的情況下，這些應該是計劃的值，而不是您所建立的值。

1. [下載指令碼][sf-aad-ps-script-download]到您的電腦。

2. 以滑鼠右鍵按一下 ZIP 檔案，選擇 [屬性]，然後核取 [解除封鎖] 核取方塊並套用。

3. 解壓縮 zip 檔案。

4. 提供 TenantId、ClusterName 和 WebApplicationReplyUrl 作為參數，來執行 `SetupApplications.ps1`。例如：

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    在 Azure 傳統入口網站中查看租用戶的 URL，就可以找到 **TenantId**。內嵌在 URL 中的 GUID 就是 TenantId。例如：

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    在指令碼所建立的 AAD 應用程式前面會加上**叢集名稱**。它不需要完全符合實際的叢集名稱，因為它只是用來讓您更輕鬆地將 AAD 構件對應到與之搭配使用的 Service Fabric 叢集。

    **WebApplicationReplyUrl** 是 AAD 在完成登入程序之後讓您的使用者返回的預設端點。您應該將此設定為您叢集的 Service Fabric Explorer 端點，其預設值為︰

    https://&lt;cluster_domain&gt;:19080/Explorer

    系統會提示您登入具有 AAD 租用戶系統管理權限的帳戶。這樣做之後，指令碼將繼續建立 Web 和原生應用程式來代表 Service Fabric 叢集。如果您在 [Azure 傳統入口網站][azure-classic-portal]中查看租用戶的應用程式，則應該會看到兩個新項目︰

    - ClusterName\_Cluster
    - ClusterName\_Client

    此指令碼將會列印下節建立叢集時 Azure Resource Manager (ARM) 範本所需的 Json，因此請讓 PowerShell 視窗持續開啟。

    ![SetupApplication 指令碼的輸出包括 ARM 範本所需的 Json][setupapp-script-output]

## 建立叢集

現在，您已經建立 AAD 應用程式，可以建立 Service Fabric 叢集。目前，Azure 入口網站不支援設定 Service Fabric 叢集的 AAD 驗證，因此，您需要在 PowerShell 或 Visual Studio 中使用 ARM 範本來進行這項作業。

請注意，AAD 僅用於叢集的用戶端驗證。若要建立安全的叢集，您也必須提供憑證，以用來保護叢集中節點之間的通訊，並提供叢集管理端點的伺服器驗證。您可以找到 [Azure 快速入門庫中安全叢集的 ARM 範本][secure-cluster-arm-template]，也可以遵循 [Visual Studio 中 Service Fabric 資源群組專案](service-fabric-cluster-creation-via-visual-studio.md)讀我檔案中所提供的指示。

以對等形式，將 `SetupApplication` 指令碼的 ARM 範本程式碼片段輸出加入 fabricSettings、managementEndpoint 等。如果您已關閉這個視窗，則程式碼片段也會顯示如下︰

```json
  "azureActiveDirectory": {
    "tenantId": "<your_tenant_id>",
    "clusterApplication": "<your_cluster_application_client_id>",
    "clientApplication": "<your_native_application_client_id>"
  }
```

clusterApplication 指的是上一節所建立的 Web 應用程式。您可以在 SetupApplication 指令碼輸出中找到其識別碼 (稱為 `WebAppId`)。clientApplication 指的是原生應用程式，而在 SetupApplication 輸出中其用戶端識別碼是提供為 NativeClientAppId。

## 將使用者指派給角色

建立應用程式來代表您的叢集之後，需要將使用者指派給 Service Fabric 所支援的角色︰唯讀和系統管理員。您可以使用 [Azure 傳統入口網站][azure-classic-portal]來執行這項作業。

1. 瀏覽至您的租用戶，然後選擇 [應用程式]。
2. 選擇 Web 應用程式，而其名稱類似 `myTestCluster_Cluster`。
3. 按一下 [使用者] 索引標籤。
4. 選擇要指派的使用者，然後按一下畫面底部的 [指派] 按鈕。

    ![將使用者指派給角色按鈕][assign-users-to-roles-button]

5. 選取要指派給使用者的角色。

    ![將使用者指派給角色][assign-users-to-roles-dialog]

>[AZURE.NOTE] 如需 Service Fabric 中角色的詳細資訊，請參閱[角色型存取控制 (適用於 Service Fabric 用戶端)](service-fabric-cluster-security-roles.md)。

## 連線到叢集

在啟用 AAD 的叢集上瀏覽至 Service Fabric Explorer 時，會將您自動重新導向至安全的登入頁面。

若要從 Windows PowerShell 或 Visual Studio 這類原生用戶端進行連線，您需要指出 AAD 作為登入機制，然後提供伺服器憑證指紋，用來驗證端點的身分識別。這兩個進入點的詳細資料顯示如下。

### 從 Visual Studio 連線

在 Visual Studio 中，您可以修改發佈設定檔來加入必要屬性，如下所示︰

```xml
<ClusterConnectionParameters     
    ConnectionEndpoint="<your_cluster_endpoint>:19000"  
    AzureActiveDirectory="true"
    ServerCertThumbprint="<your_cert_thumbprint>"
    />
```

當您發佈至叢集時，Visual Studio 將會顯示您可以向叢集驗證的登入視窗。

![Visual Studio 發佈期間的 AAD 登入視窗][vs-publish-aad-login]

### 從 Windows PowerShell 連線

在 PowerShell 中，您可以提供 Connect-ServiceFabricCluster Cmdlet 的必要參數，如下所示︰

```PowerShell
Connect-ServiceFabricCluster -AzureActiveDirectory -ConnectionEndpoint <cluster_endpoint>:19000 -ServerCertThumbprint <server_cert_thumbprint>
```

與在 Visual Studio 中相同，PowerShell 將會顯示安全登入視窗來進行驗證。

>[AZURE.NOTE] PowerShell 和 Visual Studio 所使用的 Service Fabric TCP 閘道預設會接聽連接埠 19000。如果您已設定不同的連接埠，則應該在指定連接端點時改用該連接埠。

## 已知問題

### 因回覆位址不符而發生原生用戶端驗證錯誤

從原生用戶端 (例如 Visual Studio 或 PowerShell) 驗證時，可能會看到如下的錯誤訊息︰

回覆位址 http://localhost/ 不符合針對應用程式 &lt;叢集用戶端應用程式 GUID&gt; 所設定的回覆位址

若要解決這個問題，除了已經存在的位址 'urn:ietf:wg:oauth:2.0:oob' 之外，也請將 **http://<i></i>localhost** 當成重新導向 URI 來加入 AAD 的叢集用戶端應用程式定義中。

## 後續步驟

- 深入閱讀 [Service Fabric 叢集安全性](service-fabric-cluster-security.md)
- 了解如何[使用 Visual Studio 發佈至遠端叢集](service-fabric-publish-app-remote-cluster.md)

<!-- Links -->
[sf-aad-ps-script-download]: http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[secure-cluster-arm-template]: https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad
[aad-graph-api-docs]: https://msdn.microsoft.com/zh-TW/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com

<!-- Images -->
[assign-users-to-roles-button]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles.png
[setupapp-script-output]: ./media/service-fabric-cluster-security-client-auth-with-aad/setupapp-script-arm-json-output.png
[vs-publish-aad-login]: ./media/service-fabric-cluster-security-client-auth-with-aad/vs-login-prompt.png

<!---HONumber=AcomDC_0615_2016-->