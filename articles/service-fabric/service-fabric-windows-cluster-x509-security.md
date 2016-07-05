<properties
   pageTitle="連接到安全的私人叢集 | Microsoft Azure"
   description="本文說明如何保護獨立或私人叢集內以及用戶端與叢集之間的通訊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/10/2016"
   ms.author="dkshir"/>

# 使用憑證保護獨立 Windows 叢集

本文說明如何保護獨立 Windows 叢集的各個節點之間的通訊，以及如何使用 X.509 憑證來驗證連線到此叢集的用戶端。這可確保只有已獲授權的使用者可以存取叢集和已部署的應用程式，以及執行管理工作。憑證安全性應在叢集建立之時先在叢集上啟用。如需有關節點對節點安全性、用戶端對節點安全性和角色型存取控制的詳細資訊，請參閱[叢集安全性案例](service-fabric-cluster-security.md)。

## 您需要哪些憑證？

若要開始，請[下載獨立叢集封裝](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)至叢集中的其中一個節點。在下載的封裝中，您會找到 **ClusterConfig.X509.json** 檔案。以 [編輯] 模式開啟此檔案，並檢閱 [屬性] 索引標籤下的 **security** 區段：

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
			"ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
        }
    },

此區段描述保護獨立 Windows 叢集所需的所有憑證。將 **ClusterCredentialType** 和 **ServerCredentialType** 的值設定為 X509，可啟用以憑證為基礎的安全性。

>[AZURE.NOTE] [指紋](https://en.wikipedia.org/wiki/Public_key_fingerprint)是憑證的主要身分識別。閱讀[如何擷取憑證的指紋](https://msdn.microsoft.com/library/ms734695.aspx)，以找出您所建立的憑證指紋。

下表列出您在設定叢集時所需的實際憑證：

|**CertificateInformation 設定**|**說明**|
|-----------------------|--------------------------|
|ClusterCertificate|需有此憑證，才能保護叢集上節點之間的通訊。您可以使用兩個不同的憑證 (主要和次要) 進行容錯移轉。在 **Thumbprint** 區段中設定主要憑證的指紋，以及在 **ThumbprintSecondary** 變數中設定次要憑證的指紋。|
|ServerCertificate|用戶端嘗試連線到此叢集時，會向用戶端此憑證顯示此憑證。為了方便起見，您可以選擇對 *ClusterCertificate* 和 *ServerCertificate* 使用相同的憑證。您可以使用兩個不同的伺服器憑證 (主要和次要) 進行容錯移轉。在 **Thumbprint** 區段中設定主要憑證的指紋，以及在 **ThumbprintSecondary** 變數中設定次要憑證的指紋。 |
|ClientCertificateThumbprints|這是您想在經過驗證的用戶端上安裝的一組憑證。在您要允許存取叢集和應用程式執行所在的電腦上，您可以安裝數個不同的用戶端憑證。在 **CertificateThumbprint** 變數中設定每個憑證的指紋。如果您將 **IsAdmin** 設為 *true*，則已安裝此憑證的用戶端可以對叢集執行各種管理活動。如果 **IsAdmin** 為 *false*，它只能存取在叢集上執行的應用程式。|
|ClientCertificateCommonNames|針對 **CertificateCommonName** 設定第一個用戶端憑證的一般名稱。**CertificateIssuerThumbprint** 是此憑證的簽發者指紋。閱讀[使用憑證](https://msdn.microsoft.com/library/ms731899.aspx)，以深入了解一般名稱和簽發者。|


## 安裝憑證

若要保護叢集之間的通訊，您必須先取得叢集節點的 X.509 憑證。此外，若要將此叢集的連線限制於經過授權的電腦/使用者，您必須取得並安裝這些潛在用戶端電腦的憑證。如需取得 X.509 憑證的步驟，請參閱[如何取得憑證](https://msdn.microsoft.com/library/aa702761.aspx)。您必須建立 **.pfx** 憑證，才能夠儲存您的私密金鑰。或者，如果您有 Azure 訂用帳戶，您可以依照[取得 X.509 憑證](service-fabric-secure-azure-cluster-with-certs.md#acquirecerts)一節來建立您的憑證。擁有此憑證後，您可以使用下列步驟在叢集節點上進行安裝。請注意，這些步驟假設您的節點上已經安裝最新的 Windows PowerShell 3.x。您必須針對叢集和伺服器憑證及其各自的次要憑證，在每個節點上重複這些步驟。

- 將 .pfx 檔案複製到該節點。

- 以系統管理員身分開啟 PowerShell 視窗並輸入下列命令：
	
		Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My `
		-FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $password -AsPlainText -Force)

以您用來建立此憑證的密碼取代 *$password*。以複製到這個節點之 .pfx 的完整路徑取代 $PfxFilePath。

- 在此憑證上設定存取控制，以便 Service Fabric 程序藉由執行下列指令碼來使用它：

		$cert = get-item Cert:\LocalMachine\My<CertificateThumbprint>
		$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

		#Note down the string output by this command, this is the container name for your private key. 

		$privateKeyFilePath = 'C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys<PrivateKeyContainerName>'
	
		#Observe the access rights currently assigned to this certificate.
		get-acl $privateKeyFilePath | fl

		#Set the ACL so that the Service Fabric process can access it.
		$acl = get-acl $privateKeyFilePath
		$acl.SetAccessRule((New-Object System.Security.Accesscontrol.FileSystemAccessRule("NT AUTHORITY\NetworkService","FullControl","Allow")))
		Set-Acl $privateKeyFilePath $acl -ErrorAction Stop 
	

使用上述步驟，也可在您要允許存取叢集的電腦上安裝用戶端憑證。


## 後續步驟

設定 ClusterConfig.X509.json 檔案的 **security** 區段後，您可以繼續進行[建立叢集](service-fabric-cluster-creation-for-windows-server.md#createcluster)一節，以設定節點和建立獨立叢集。請記得在建立叢集時使用 **ClusterConfig.X509.json** 檔案。例如，您的命令可能如下所示：

	cd $ServiceFabricDeployAnywhereFolder
	.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true -Verbose


順利執行安全的獨立 Windows 叢集，並已設定經過驗證的用戶端以進行連接之後，請依照[使用 PowerShell 來連線到安全的叢集](service-fabric-connect-to-secure-cluster.md#connectsecurecluster)一節來連接它。

<!---HONumber=AcomDC_0622_2016-->