---
title: "使用 PowerShell 管理執行個體層級公用 IP (傳統) | Microsoft Docs"
description: "了解 ILPIP (PIP) 及如何使用 PowerShell 管理它們。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: c934f78e514230958fad8b2aa9be4d2e56a3a835
ms.openlocfilehash: f1919d84cf912e184d87a5eeb462355e8ee3da07


---
# <a name="instance-level-public-ip-classic-overview"></a>執行個體層級公用 IP (Classic) 概觀
執行個體層級公用 IP (ILPIP) 是您可以直接指派至 VM 或角色執行個體的 IP 位址，而不是指派至 VM 或角色執行個體所在的雲端服務。 這不會取代指派給雲端服務的 VIP (虛擬 IP)。 應該說是您可以用來直接連接到 VM 或角色執行個體的其他 IP 位址。

> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議大部分的新部署使用 Resource Manager。 請確定您了解 [IP 位址](virtual-network-ip-addresses-overview-classic.md) 在 Azure 中的運作方式。

![ILPIP 和 VIP 之間的差異](./media/virtual-networks-instance-level-public-ip/Figure1.png)

如圖 1 所示，儘管通常是使用 VIP:&lt;連接埠號碼&gt; 來存取個別 VM，但還是會使用 VIP 來存取雲端服務。 將 ILPIP 指派給特定的 VM，就能直接使用該 IP 位址來存取 VM。

當您在 Azure 中建立雲端服務時，對應的 DNS A 記錄即會自動建立，以允許透過完整格式的網域名稱 (FQDN) 存取服務，而不需使用實際的 VIP。 相同程序也適用於 ILPIP，但可改為透過 FQDN 而不是 ILPIP 來允許存取 VM 或角色執行個體。 例如，若您建立名為 contosoadservice 的雲端服務，並設定名為 contosoweb 且具有兩個執行個體的 Web 角色，Azure 將會為那些執行個體登錄下列 A 記錄：

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> 您只能針對每個 VM 或角色執行個體指派一個 ILPIP。 您可以針對每個訂用帳戶最多使用 5 個 ILPIP。 ILPIP 目前不支援多個 NIC 的 VM。
> 
> 

## <a name="why-should-i-request-an-ilpip"></a>為什麼我應該要求 ILPIP？
如果想要透過直接指派 IP 位址的方式連接到 VM 或角色執行個體，而不是使用雲端服務 VIP:&lt;連接埠號碼&gt;，請為 VM 或角色執行個體要求 ILPIP。

* **被動式 FTP** - 若 VM 上有 ILPIP，您可以在近乎所有的連接埠上接收流量，不必開啟端點接收流量。 這會啟用動態選擇連接埠的案例，如被動式 FTP 等。
* **輸出 IP** - 源自 VM 的輸出流量是使用 ILPIP 作為來源送出，而這可將 VM 唯一識別為外部實體。

> [!NOTE]
> ILPIP 過去稱為 PIP，表示公用 IP。
> 

## <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>如何在建立 VM 期間使用 PowerShell 要求 ILPIP
下列 PowerShell 指令碼會建立名為 FTPService 的新雲端服務，然後從 Azure 擷取映像，並使用擷取的映像建立名為 FTPInstance 的 VM、設定要使用 ILPIP 的 VM，然後將 VM 加入新服務：

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>如何擷取 VM 的 ILPIP 資訊
若要檢視使用上述指令碼建立之 VM 的 ILPIP 資訊，請執行下列 PowerShell 命令，並觀察 PublicIPAddress 和 PublicIPName 的值：

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

預期的輸出：
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>如何從 VM 移除 ILPIP
若要移除在上述指令碼中加入 VM 的 ILPI，請執行下列 PowerShell 命令：

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>如何將 ILPIP 加入現有的 VM
若要將 ILPIP 新增至使用上述指令碼建立的 VM，請執行下列命令：

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>如何使用服務組態檔來將 ILPIP 關聯至 VM
您也可以使用服務組態檔 (CSCFG) 來將 ILPIP 關聯至 VM。 下列範例 XML 示範如何設定雲端服務，以使用名為 *MyPublicIP* 的角色執行個體 ILPIP： 

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>後續步驟
* 了解 [IP 位址](virtual-network-ip-addresses-overview-classic.md) 在傳統部署模型中的運作方式。
* 深入了解 [保留的 IP](virtual-networks-reserved-public-ip.md)。




<!--HONumber=Jan17_HO1-->


