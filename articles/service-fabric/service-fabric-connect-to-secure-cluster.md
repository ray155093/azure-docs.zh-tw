---
title: "驗證用戶端對叢集的存取 | Microsoft Docs"
description: "說明如何驗證用戶端對 Service Fabric 叢集的存取，以及如何保護用戶端與叢集之間的通訊。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 65775053918e12ef8881f417dacc0a63f080d093
ms.openlocfilehash: 6de98012e768abc7f8450e97648444a74474b5e9


---
# <a name="connect-to-a-secure-cluster"></a>連線到安全的叢集
當用戶端連線到 Service Fabric 叢集節點時，用戶端可以使用憑證安全性或 Azure Active Directory (AAD) 來接受驗證及保護已建立的通訊。 此驗證可確保只有已獲授權的使用者可以存取叢集和已部署的應用程式，以及執行管理工作。  憑證或 AAD 安全性必須在叢集建立之時即事先在叢集上啟用。  如需有關叢集安全性案例的詳細資訊，請參閱 [叢集安全性](service-fabric-cluster-security.md)。 如果您要連接到使用憑證保護的叢集，請在要連接到叢集的電腦上[設定用戶端憑證](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert)。 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-cli"></a>使用 Azure CLI 連線到安全的叢集
下列 Azure CLI 命令說明如何連線到安全的叢集。 

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>使用用戶端憑證連線到安全的叢集
憑證詳細資料必須與叢集節點上的憑證相符。 

如果您的憑證有憑證授權單位 (CA)，則您需要新增 `--ca-cert-path` 參數，如下列範例所示︰ 

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
如果您有多個 CA，請使用逗號做為分隔符號。 

如果憑證中的一般名稱不符合連接端點，您可以使用 `--strict-ssl-false` 參數略過驗證。 

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```

如果您想要跳過 CA 驗證，您可以新增 ``--reject-unauthorized-false`` 參數，如下列命令所示︰

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

若要連線至使用自我簽署憑證保護的叢集，請使用下列命令以移除 CA 驗證和一般名稱驗證。

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

連線之後，您應該能夠[執行其他 CLI 命令](service-fabric-azure-cli.md)來與叢集互動。 

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-secure-cluster-using-powershell"></a>使用 PowerShell 來連線到安全的叢集
在您透過 PowerShell 執行叢集上的作業之前，先建立叢集的連接。 叢集連接適用於指定 PowerShell 工作階段中的所有後續命令。

### <a name="connect-to-an-unsecure-cluster"></a>連線到不安全的叢集

若要連接至不安全的叢集，請提供叢集端點位址至 **Connect-ServiceFabricCluster** 命令︰

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>使用 Azure Active Directory 連線到安全的叢集

若要連接至使用 Azure Active Directory 來授權叢集系統管理員存取權的安全叢集，請提供叢集憑證指紋，並使用 AzureActiveDirectory 旗標。  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>使用用戶端憑證連線到安全的叢集
執行下列 PowerShell 命令來連線至使用用戶端憑證授權系統管理員存取權的安全叢集。 提供叢集憑證指紋，以及已授與權限來管理叢集的用戶端憑證指紋。 憑證詳細資料必須與叢集節點上的憑證相符。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* 是安裝在叢集節點上的伺服器憑證指紋。 *FindValue* 是系統管理員用戶端憑證的指紋。
填入參數後，命令看起來如下列範例所示︰ 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```


<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-secure-cluster-using-the-fabricclient-apis"></a>使用 FabricClient API 來連線到安全的叢集
Service Fabric SDK 會提供叢集管理的 [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) 類別。 

### <a name="connect-to-an-unsecure-cluster"></a>連線到不安全的叢集

若要連接至遠端不安全的叢集，建立 FabricClient 執行個體，並提供叢集位址︰

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

針對在叢集 (例如，Reliable Service) 內執行的程式碼，建立 FabricClient 且不指定叢集位址。 FabricClient 連接到目前正在執行程式碼之節點上的本機管理閘道，避免額外的網路躍點。

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>使用用戶端憑證連線到安全的叢集

叢集中的節點必須具備有效的憑證，這些憑證在 SAN 中的通用名稱或 DNS 名稱會出現在於 [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) 上設定的 [RemoteCommonNames](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx) 屬性中。 遵循此程序，就可讓用戶端與叢集節點之間進行相互驗證。

```csharp
string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

X509Credentials xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
FabricClient fc = new FabricClient(xc, connection);
Task<bool> t = fc.PropertyManager.NameExistsAsync(new Uri("fabric:/any"));
try
{
    bool result = t.Result;
    Console.WriteLine("Cluster is connected");
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();

    // Client certificate
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "MY";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = thumb;

    // Server certificate
    xc.RemoteCertThumbprints.Add(thumb);
    xc.RemoteCommonNames.Add(name);

    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>使用 Azure Active Directory 連線到安全的叢集

遵循此程序，針對用戶端身分識別啟用 Azure Active Directory，以及針對伺服器身分識別啟用伺服器憑證。

若要使用會顯示 AAD 互動式登入對話方塊的互動模式︰

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

FabricClient fc = new FabricClient(
    claimsCredentials,
    connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

若要使用無訊息模式，而不需任何人為互動︰

(此範例依存於 Microsoft.IdentityModel.Clients.ActiveDirectory，版本：2.19.208020213

請參閱 [Microsoft.IdentityModel.Clients.ActiveDirectory Namespace](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx) 了解如何取得權杖和詳細資訊)

```csharp
string tenantId = "c15cfcea-02c1-40dc-8466-fbd0ee0b05d2";
string clientApplicationId = "118473c2-7619-46e3-a8e4-6da8d5f56e12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob"
    );

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";
ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

FabricClient fc = new FabricClient(
   claimsCredentials,
   connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    string token = "";
    try
    {
        var authResult = authContext.AcquireToken(
            resource,
            clientId,
            new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
        token = authResult.AccessToken;
    }
    catch (AdalException ex)
    {
        Console.WriteLine("Get AccessToken failed: {0}", ex.Message);
    }

    return token;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>使用 Service Fabric Explorer 連線到安全的叢集
若要連線到指定之叢集的 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)，請將您的瀏覽器指向：

`http://<your-cluster-endpoint>:19080/Explorer`

Azure 入口網站的叢集基本資訊窗格中也會提供完整 URL。

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>使用 Azure Active Directory 連線到安全的叢集

若要連接至使用 AAD 保護的叢集，請將您的瀏覽器指向：

`https://<your-cluster-endpoint>:19080/Explorer`

系統將會自動提示您使用 AAD 登入。

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>使用用戶端憑證連線到安全的叢集

若要連接至使用憑證保護的叢集，請將您的瀏覽器指向：

`https://<your-cluster-endpoint>:19080/Explorer`

系統會自動提示您選取用戶端憑證。

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>設定遠端電腦上的用戶端憑證
至少應使用兩個憑證保護叢集，一個是叢集和伺服器憑證，另一個用於用戶端存取。  建議您也使用額外的次要憑證和用戶端存取憑證。  若要使用憑證安全性來保護用戶端與叢集節點之間的通訊，您必須先取得並安裝用戶端憑證。 此憑證可以安裝到本機電腦或目前使用者的個人 (My) 存放區。  您也需要伺服器憑證的指紋，讓用戶端可以驗證叢集。

請執行下列 PowerShell Cmdlet 以在您存取叢集的電腦上設定用戶端憑證。

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

如果是自我簽署憑證，您必須先把它匯入您電腦的「受信任的人」存放區，才能使用此憑證來連線到安全的叢集。

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>後續步驟
* [Service Fabric 叢集升級程序與您的期望](service-fabric-cluster-upgrade.md)
* [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。
* [Service Fabric 健康情況模型簡介](service-fabric-health-introduction.md)
* [應用程式安全性及 RunAs](service-fabric-application-runas-security.md)




<!--HONumber=Dec16_HO2-->


