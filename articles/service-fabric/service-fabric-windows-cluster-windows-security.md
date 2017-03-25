---
title: "使用 Windows 安全性保護在 Windows 上執行的叢集 | Microsoft Docs"
description: "了解如何使用 Windows 安全性在 Windows 上執行的獨立叢集上設定節點對節點和用戶端對節點安全性。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 6294a6fac8748f70c807df76feb425cb627bf4c3
ms.lasthandoff: 03/15/2017


---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>使用 Windows 安全性保護 Windows 上的獨立叢集
為避免有人未經授權存取 Service Fabric 叢集，您必須保護叢集。 當叢集執行生產工作負載時，安全性尤其重要。 本文說明如何在 *ClusterConfig.JSON* 檔案中使用 Windows 安全性，設定節點對節點和用戶端對節點的安全性。  此程序會對應[建立在 Windows 上執行的獨立叢集](service-fabric-cluster-creation-for-windows-server.md)的設定安全性步驟。 如需有關 Service Fabric 如何使用 Windows 安全性的詳細資訊，請參閱[叢集安全性案例](service-fabric-cluster-security.md)。

> [!NOTE]
> 請仔細考慮選擇節點對節點安全性，因為您無法選擇將叢集從某種安全性升級到另一種安全性。 若要變更安全性選擇，您必須重建完整叢集。
>
>

## <a name="configure-windows-security"></a>設定 Windows 安全性  
隨著 [Microsoft.Azure.ServiceFabric.WindowsServer<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) 獨立叢集套件下載的 *ClusterConfig.Windows.JSON* 範例組態檔包含可供設定 Windows 安全性的範本。 Windows 安全性於 **Properties** 區段中設定︰

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

| **組態設定** | **說明** |
| --- | --- |
| ClusterCredentialType |設定為 [Windows] 可啟用 Windows 安全性。 |
| ServerCredentialType |設定為 [Windows] 可為用戶端啟用 Windows 安全性。<br /><br />這表示叢集的用戶端和叢集本身都在 Active Directory 網域內執行。 |  
| WindowsIdentities |包含叢集和用戶端身分識別。 |  
| ClusterIdentity |使用電腦群組名稱 (domain\machinegroup) 來設定節點對節點安全性。 |  
| ClientIdentities |設定用戶端對節點安全性。 用戶端使用者帳戶的陣列。 |  
| 身分識別 |新增網域使用者 (domain\username) 以做為用戶端身分識別。 |  
| IsAdmin |設定為 true 可指定網域使用者具有系統管理員用戶端存取權，設定為 false 則具有使用者用戶端存取權。 |  

[節點對節點安全性](service-fabric-cluster-security.md#node-to-node-security)是使用 **ClusterIdentity** 來設定的。 節點必須彼此知悉才能在兩者間建立信任關係。 若要讓節點彼此知悉，請建立包含叢集中所有節點的網域群組。 此群組名稱應指定於 **ClusterIdentity**。 如需詳細資訊，請參閱[在 Active Directory 中建立群組](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx)。  

[用戶端對節點安全性](service-fabric-cluster-security.md#client-to-node-security)是使用 **ClientIdentities** 來設定的。 若要在用戶端與叢集之間建立信任，您必須設定叢集，讓叢集知道它可以信任的用戶端身分識別。 您可以透過兩種不同方式建立信任︰

- 指定可以連線的網域群組使用者。
- 指定可以連線的網域節點使用者。

Service Fabric 針對連線到 Service Fabric 叢集的用戶端，支援兩種不同的存取控制類型：系統管理員和使用者。 存取控制可讓叢集系統管理員針對不同的使用者群組限制特定類型的叢集作業的存取權，讓叢集更加安全。  系統管理員可以完整存取管理功能 (包括讀取/寫入功能)。 使用者預設只具有管理功能的讀取存取權 (例如查詢功能)，以及解析應用程式和服務的能力。  

下列範例 **security** 區段可設定 Windows 安全性，指定 ServiceFabric/clusterA.contoso.com 中的電腦隸屬於叢集，並指定 CONTOSO\usera 具有系統管理員用戶端存取權︰

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> 不應在網域控制站上部署 Service Fabric。 請確定在使用電腦群組或群組受管理服務帳戶 (gMSA) 時，ClusterConfig.json 不會包含網域控制站的 IP 位址。
>
>

## <a name="next-steps"></a>後續步驟
在 *ClusterConfig.JSON* 檔案中設定 Windows 安全性之後，繼續進行 [建立在 Windows 上執行的獨立叢集](service-fabric-cluster-creation-for-windows-server.md)中的叢集建立程序。

如需有關節點對節點安全性、用戶端對節點安全性和角色型存取控制的詳細資訊，請參閱[叢集安全性案例](service-fabric-cluster-security.md)。

如需使用 PowerShell 或 FabricClient 來連線的範例，請參閱[連線到安全的叢集](service-fabric-connect-to-secure-cluster.md) 。

