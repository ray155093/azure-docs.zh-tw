---
title: "如何使用 CLI 在傳統模式中設定靜態私人 IP| Microsoft Docs"
description: "了解靜態私人 IP (DIP) 以及如何在傳統模式中使用 CLI 進行管理"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 17386acf-c708-4103-9b22-ff9bf04b778d
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 70da85679c6e594278803fb073665a6eeab7df34


---
# <a name="how-to-set-a-static-private-ip-address-classic-in-azure-cli"></a>如何在 Azure CLI 設定靜態的私人 IP 位址 (傳統)
[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文涵蓋之內容包括傳統部署模型。 您也可以 [管理資源管理員部署模型中的靜態私人 IP 位址](virtual-networks-static-private-ip-arm-cli.md)。

下列範例 Azure CLI 命令會預期已經建立簡單的環境。 如果您想要執行如本文件中所顯示的命令，請先建置 [建立 vnet](virtual-networks-create-vnet-classic-cli.md)中所說明的測試環境。

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>建立 VM 時如何指定靜態私人 IP 位址
若要根據上述案例，在名為 TestService 的雲端服務中建立名為 DNS01 的新 VM，請遵循下列步驟：

1. 如果您從未使用過 Azure CLI，請參閱 [安裝和設定 Azure CLI](../xplat-cli-install.md) ，並依照指示進行，直到選取您的 Azure 帳戶和訂用帳戶為止。
2. 執行 **azure service create** 命令來建立雲端服務。
   
        azure service create TestService --location uscentral
   
    預期的輸出：
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. 執行 **azure create vm** 命令來建立 VM。 請注意靜態私人 IP 位址的值。 輸出後顯示的清單可說明所使用的參數。
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    預期的輸出：
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (或 --location)**。 將要建立 VM 的 Azure 區域。 在本文案例中為 *centralus*。
   * **-n (或 --vm-name)**。 要建立之 VM 的名稱。
   * **-w (或 --virtual-network-name)**。 將建立 VM 之 VNet 的名稱。 
   * **-S (或 --static-ip)**。 VM 的靜態私人 IP 位址
   * **TestService**。 將建立 VM 之雲端服務的名稱。
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**。 用來建立 VM 的映像。
   * **adminuser**。 Windows VM 的本機系統管理員。
   * **AdminP@ssw0rd**。 Windows VM 的本機系統管理員密碼。

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>如何擷取 VM 的靜態私人 IP 位址資訊
若要檢視使用上述指令碼建立之 VM 的靜態私人 IP 位址資訊，請執行下列 Azure CLI 命令並觀察 *Network StaticIP*的值：

    azure vm static-ip show DNS01

預期的輸出：

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>如何移除 VM 的靜態私人 IP 位址
若要移除上述指令碼中新增至 VM 的靜態私人 IP 位址，請執行下列 Azure CLI 命令：

    azure vm static-ip remove DNS01

預期的輸出：

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>如何將靜態私人 IP 位址新增至現有的 VM
若要將靜態私人 IP 位址新增至使用上述指令碼建立之 VM，請執行下列命令：

    azure vm static-ip set DNS01 192.168.1.101

預期的輸出：

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>後續步驟
* 深入了解 [保留的公用 IP](virtual-networks-reserved-public-ip.md) 位址。
* 深入了解 [執行個體層級公用 IP (ILPIP)](virtual-networks-instance-level-public-ip.md) 位址。
* 請參閱 [保留 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)。




<!--HONumber=Nov16_HO3-->


