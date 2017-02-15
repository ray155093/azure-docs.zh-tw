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
ms.date: 12/07/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4bf470737f961219250c37d890ed81eb62409626
ms.openlocfilehash: f03026212dfcbe2faa22188ebfb2e953114a87fd


---
# <a name="set-up-gpu-drivers-for-n-series-vms"></a>為 N 系列 VM 設定 GPU 驅動程式
若要利用 Azure N 系列 VM (執行支援的 Linux 散發套件) 的 GPU 功能，您必須在部署之後於每個 VM 上安裝 NVIDIA 圖形驅動程式。 本文也適用於 [Windows VM](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

如需 N 系列 VM 規格、儲存體容量與磁碟的詳細資料，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。



## <a name="supported-gpu-drivers"></a>支援的 GPU 驅動程式


> [!NOTE]
> 目前，Linux GPU 支援僅適用於執行 Ubuntu Server 16.04 LTS 的 Azure NC VM。

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>NC VM 的 NVIDIA Tesla 驅動程式

* [Ubuntu 16.04 LTS](https://go.microsoft.com/fwlink/?linkid=836899) (.run 自我解壓縮安裝程式)

## <a name="tesla-driver-installation-on-ubuntu-1604-lts"></a>Ubuntu 16.04 LTS 上的 Tesla 驅動程式安裝

1. 對 Azure N 系列 VM 進行 SSH 連接。

2. 若要確認系統有 CUDA 功能的 GPU，請執行下列命令︰

    ```bash
    lspci | grep -i NVIDIA
    ```
    您會看到類似下列範例 (顯示 NVIDIA Tesla K80 卡) 的輸出︰

    ![lspci 命令輸出](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

3. 為您的散發驅動程式下載 .run 檔案。 下列範例命令會將 Ubuntu 16.04 LTS Tesla 驅動程式下載至 /tmp 目錄：

    ```bash
    wget -O /tmp/NVIDIA-Linux-x86_64-367.48.run https://go.microsoft.com/fwlink/?linkid=836899
    ```

4. 如果您需要在系統上安裝 `gcc` 和 `make` (Tesla 驅動程式所需)，請輸入下列命令︰

    ```bash
    sudo apt-get update
    
    sudo apt install gcc

    sudo apt install make
    ```

4. 變更至包含驅動程式安裝程式的目錄，然後執行命令，如下所示︰

    ```bash
    chmod +x NVIDIA-Linux-x86_64-367.48.run
    
    sudo sh ./NVIDIA-Linux-x86_64-367.48.run
    ```

## <a name="verify-driver-installation"></a>確認驅動程式安裝


若要查詢 GPU 裝置狀態，請執行與驅動程式一起安裝的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface)命令列公用程式。 

![NVIDIA 裝置狀態](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="optional-installation-of-nvidia-cuda-toolkit-on-ubuntu-1604-lts"></a>選擇性地在 Ubuntu 16.04 LTS 上安裝 NVIDIA CUDA Toolkit

您可以選擇性地在執行 Ubuntu 16.04 LTS 的 NC VM 上安裝 NVIDIA CUDA Toolkit 8.0。 除了 GPU 驅動程式，此工具組會提供完整開發環境，讓 C 和 C++ 開發人員建置 GPU 加速應用程式。

若要安裝 CUDA Toolkit，請執行命令，如下所示：

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.44-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers
```

安裝可能需要數分鐘的時間。

## <a name="next-steps"></a>後續步驟

* 如需 N 系列 VM 上 NVIDIA GPU 的詳細資訊，請參閱︰
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (適用於 Azure NC VM)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (適用於 Azure NV VM)




<!--HONumber=Dec16_HO2-->


