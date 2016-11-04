---
title: 建立 Linux VM | Microsoft Docs
description: 了解如何以執行 Linux 作業系統的傳統部署模型建立自訂虛擬機器。
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: iainfou

---
# 如何建立自訂 Linux VM
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

本主題說明如何使用傳統部署模型，以 Azure CLI 建立自訂虛擬機器 (VM)。我們將使用 Azure 上可用「映像」中的 Linux 映像。Azure CLI 命令提供下列組態選項：

* 將 VM 連線到虛擬網路
* 將 VM 加入現有的雲端服務
* 將 VM 加入現有的儲存體帳戶
* 將 VM 加入可用性集合或位置

> [!IMPORTANT]
> 如果要讓虛擬機器使用虛擬網路，以便依主機名稱來連接虛擬機器，或設定跨單位連線，則必須在建立虛擬機器時指定虛擬網路。只有在建立虛擬機器時，才能將虛擬機器設定為加入虛擬網路。如需虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](http://go.microsoft.com/fwlink/p/?LinkID=294063)。
> 
> 

## 如何使用傳統的部署模型建立 Linux 虛擬機器
[!INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]

<!---HONumber=AcomDC_0824_2016-->