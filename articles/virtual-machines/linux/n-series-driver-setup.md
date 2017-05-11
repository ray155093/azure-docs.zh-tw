---
title: "適用於 Linux 的 N 系列驅動程式設定 | Microsoft Docs"
description: "如何針對 Azure 中執行 Linux 的 N 系列 VM 設定 NVIDIA GPU 驅動程式"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: 69f1363c26d8b5a18ffd5629c6a49c34306dd7c0
ms.contentlocale: zh-tw
ms.lasthandoff: 05/04/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>為執行 Linux 的 N 系列 VM 設定 GPU 驅動程式

若要利用 Azure N 系列 VM (執行 Linux) 的 GPU 功能，您必須在每個 VM 上安裝 NVIDIA 圖形驅動程式。 本文提供您在部署 N 系列 VM 後的驅動程式安裝步驟。 驅動程式設定資訊也適用於 [Windows VM](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


如需 N 系列 VM 規格、儲存體容量與磁碟的詳細資料，請參閱 [GPU Linux VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 



## <a name="supported-distributions-and-drivers"></a>支援的散發套件和驅動程式

> [!IMPORTANT]
> 目前，Linux GPU 驅動程式支援僅適用於 Azure NC VM。 

支援下列來自 Azure Marketplace 的散發套件在 N 系列 Linux VM 上執行 NVIDIA 圖形驅動程式。

### <a name="nc-vms-tesla-k80-card"></a>NC VM (Tesla K80 卡)
* Ubuntu 16.04 LTS 
* Red Hat Enterprise Linux 7.3 
* CentOS 型 7.3 

**支援的驅動程式**：NVIDIA CUDA 8.0，驅動程式分支 R375。 [安裝步驟](#install-cuda-drivers-for-nc-vms)




> [!WARNING] 
> 在 Red Hat 產品上安裝第三方軟體可能會影響 Red Hat 支援條款。 請參閱 [Red Hat 知識庫文件 (英文)](https://access.redhat.com/articles/1067)。
>




## <a name="install-cuda-drivers-for-nc-vms"></a>安裝適用於 NC VM 的 CUDA 驅動程式

以下是從 NVIDIA CUDA Toolkit 8.0 在 Linux NC VM 上安裝 NVIDIA 驅動程式的步驟。 

C 和 C++ 開發人員可以選擇性地安裝完整 Toolkit，以建置 GPU 加速的應用程式。 如需詳細資訊，請參閱 [CUDA 安裝指南](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)。


> [!NOTE]
> 這裡所提供的 CUDA 驅動程式下載連結，為發行當時的最新驅動程式。 如需最新的驅動程式，請瀏覽 [NVIDIA](http://www.nvidia.com/) 網站。
>

若要安裝 CUDA Toolkit，請透過 SSH 連線至每部 VM。 若要確認系統有 CUDA 功能的 GPU，請執行下列命令︰

```bash
lspci | grep -i NVIDIA
```
您會看到類似下列範例 (顯示 NVIDIA Tesla K80 卡) 的輸出︰

![lspci 命令輸出](./media/n-series-driver-setup/lspci.png)

然後執行您的配送映像特有的命令。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
安裝可能需要數分鐘的時間。

若要選擇性地安裝完整的 CUDA Toolkit，請輸入︰

```bash
sudo apt-get install cuda
```

重新啟動 VM 並繼續確認安裝。

### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 或 Red Hat Enterprise Linux 7.3

> [!IMPORTANT] 
> 由於已知的問題，在執行 CentOS 7.3 或 Red Hat Enterprise Linux 7.3 的 NC24r VM 上安裝 NVIDIA CUDA 驅動程式將會失敗。
>

首先，請取得更新。 

```bash
sudo yum update

sudo reboot
```

重新連線至 VM，並使用下列命令繼續安裝：

```bash
sudo yum install kernel-devel

sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

sudo yum install dkms

CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo yum install cuda-drivers
```

安裝可能需要數分鐘的時間。 若要選擇性地安裝完整的 CUDA Toolkit，請輸入︰

```bash
sudo yum install cuda
```

重新啟動 VM 並繼續確認安裝。


### <a name="verify-driver-installation"></a>確認驅動程式安裝


若要查詢 GPU 裝置狀態，請透過 SSH 連線至 VM 並執行與驅動程式一起安裝的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令列公用程式。 

您會看到類似以下的輸出：

![NVIDIA 裝置狀態](./media/n-series-driver-setup/smi.png)


### <a name="cuda-driver-updates"></a>CUDA 驅動程式更新

我們建議您在部署後定期更新 CUDA 驅動程式。

#### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

更新完成後，重新啟動 VM。

#### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 或 Red Hat Enterprise Linux 7.3

```bash
sudo yum update
```

更新完成後，重新啟動 VM。



## <a name="troubleshooting"></a>疑難排解

* 執行具有 4.4.0-75 Linux 核心之 Ubuntu 16.04 LTS 的 Azure N 系列 VM 具有和 CUDA 驅動程式相關的已知問題。 若要在升級核心時維持驅動程式功能，請至少升級至核心版本 4.4.0-77。 



## <a name="next-steps"></a>後續步驟

* 如需 N 系列 VM 上 NVIDIA GPU 的詳細資訊，請參閱︰
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (適用於 Azure NC VM)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (適用於 Azure NV VM)

* 若要擷取已安裝 NVIDIA 驅動程式的 Linux VM 映像，請參閱[如何一般化和擷取 Linux 虛擬機器](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
