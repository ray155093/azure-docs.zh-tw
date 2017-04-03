---
title: "最佳化 VM 網路輸送量 | Microsoft Docs"
description: "了解如何最佳化 Azure 虛擬機器網路輸送量。"
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2017
ms.author: steveesp
translationtype: Human Translation
ms.sourcegitcommit: 50be31e179bf52e009596fbc68339dfb5a1aa1e4
ms.openlocfilehash: d53b1cae9845be32bd053ef196203ea83df06b10
ms.lasthandoff: 02/15/2017


---

# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>最佳化 Azure 虛擬機器的網路輸送量

Azure 虛擬機器 (VM) 有預設網路設定，可進一步針對網路輸送量進行最佳化。 此文章說明如何最佳化 Microsoft Azure Windows 和 Linux VM (包括如 Ubuntu、CentOS 和 Red Hat 等主要發行版本) 的網路輸送量。

## <a name="windows-vm"></a>Windows VM

相較於不使用接收端調整 (RSS) 的 VM，使用 RSS 的 VM 可達到更高的最大輸送量。 根據預設，Windows VM 中可能會停用 RSS。 完成下列步驟來判斷是否已啟用 RSS，以及在它已停用的情況下將它啟用。

1. 輸入 `Get-NetAdapterRss` PowerShell 命令來查看是否已針對網路介面卡啟用 RSS。 在從 `Get-NetAdapterRss` 傳回的下列範例輸出中，RSS 並未啟用。

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. 輸入下列命令以啟用 RSS：

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    上述命令沒有輸出。 此命令已變更 NIC 設定，導致約一分鐘的暫時性連線中斷。 連線中斷時隨即出現 [正在重新連線] 對話方塊。 第三次嘗試後，連線通常就會恢復。
3. 再次輸入 `Get-NetAdapterRss` 命令以確認 VM 中已啟用 RSS。 如果成功，則會傳回下列範例輸出：

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : True
    ```

## <a name="linux-vm"></a>Linux VM

根據預設，Azure Linux VM 中一律會啟用 RSS。 2017 年 1 月之後發行的 Linux 核心包含新的網路最佳化選項，它們可讓 Linux VM 達到更高的網路輸送量。

### <a name="ubuntu"></a>Ubuntu

若要獲得最佳化，請先更新至截至 2017 年 1 月推出的最新支援版本，也就是：
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
更新完成之後，請輸入下列命令以取得最新的核心︰

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

選擇性命令︰

`apt-get -y dist-upgrade`

### <a name="centos"></a>CentOS

若要獲得最佳化，請先更新至截至 2017 年 1 月推出的最新支援版本，也就是：
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
更新完成之後，請安裝最新的 Linux Integration Services (LIS)。
輸送量最佳化選項從 LIS 4.1.3 版開始提供。 輸入下列命令以安裝 LIS：

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

若要獲得最佳化，請先更新至截至 2017 年 1 月推出的最新支援版本，也就是：

"Publisher": "RedHat" "Offer": "RHEL" "Sku": "7.3" "Version": "7.3.20161104"

更新完成之後，請安裝最新的 Linux Integration Services (LIS)。
輸送量最佳化選項從 LIS 4.1.3 版開始提供。 輸入下列命令以下載並安裝 LIS：

```bash
mkdir lis4.1.3
cd lis4.1.3
wget https://download.microsoft.com/download/7/6/B/76BE7A6E-E39F-436C-9353-F4B44EF966E9/lis-rpms-4.1.3.tar.gz
tar xvzf lis-rpms-4.1.3.tar.gz
cd LISISO
install.sh #or upgrade.sh if previous LIS was previously installed
```
 
若要深入了解 Linux Integration Services Version 4.1 for Hyper-V，請檢視[下載頁面](https://www.microsoft.com/download/details.aspx?id=51612)。

