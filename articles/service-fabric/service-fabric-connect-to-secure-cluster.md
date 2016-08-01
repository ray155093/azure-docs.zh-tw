<properties
   pageTitle="驗證用戶端對叢集的存取 |Microsoft Azure"
   description="說明如何使用憑證來驗證用戶端對 Service Fabric 叢集的存取，以及如何保護用戶端與叢集之間的通訊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="ryanwi"/>

# 連線到安全的叢集
當用戶端連線到 Service Fabric 叢集節點時，用戶端可以使用憑證安全性來接受驗證及保護已建立的通訊。這可確保只有已獲授權的使用者可以存取叢集和已部署的應用程式，以及執行管理工作。憑證安全性必須在叢集建立之時即事先在叢集上啟用。至少應使用兩個憑證保護叢集，一個是叢集和伺服器憑證，另一個用於用戶端存取。建議您也使用額外的次要憑證和用戶端存取憑證。如需有關叢集安全性案例的詳細資訊，請參閱[叢集安全性](service-fabric-cluster-security.md)。

若要使用憑證安全性來保護用戶端與與叢集節點之間的通訊，您必須先取得用戶端憑證，並安裝到本機電腦上的個人 (My) 存放區或目前使用者的「個人」存放區。您也會需要伺服器憑證的指紋，讓用戶端可以驗證叢集。

請執行下列 PowerShell Cmdlet 以在您將用來存取叢集的電腦上設定用戶端憑證。

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

如果是自我簽署憑證，您將必須先把它匯入您電腦的「受信任的人」存放區，才能使用此憑證來連線到安全的叢集。

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

<a id="connectsecurecluster"></a>
## 使用 PowerShell 來連線到安全的叢集

執行下列 PowerShell 命令來連線到安全的叢集。憑證詳細資料必須與叢集節點上的憑證相符。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

舉例來說，上述 PowerShell 命令應該會類似下列內容：*ServerCertThumbprint* 是安裝在叢集節點上的伺服器憑證指紋，*FindValue* 是管理員用戶端憑證指紋。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```

## 使用 FabricClient API 來連線到安全的叢集
請參閱以下的 [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx)。叢集中的節點必須具備有效的憑證，這些憑證在 SAN 中的通用名稱或 DNS 名稱會出現在於 [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) 上設定的 [RemoteCommonNames 屬性](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx) 中。這可讓用戶端與叢集節點之間進行相互驗證。

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


## 後續步驟

- [Service Fabric 叢集升級程序與您的期望](service-fabric-cluster-upgrade.md)
- [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric 健康情況模型簡介](service-fabric-health-introduction.md)
- [應用程式安全性及 RunAs](service-fabric-application-runas-security.md)

<!---HONumber=AcomDC_0720_2016-->