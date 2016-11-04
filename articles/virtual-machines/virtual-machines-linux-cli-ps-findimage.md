---
title: 使用 Azure CLI 選取 Linux VM 映像 | Microsoft Docs
description: 了解如何在使用資源管理員部署模型建立 Linux 虛擬機器時，判斷映像的發行者、訂閱詳情及 SKU。
services: virtual-machines-linux
documentationcenter: ''
author: squillace
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill

---
# 使用 Azure CLI 選取 Linux VM 映像
本主題說明如何針對您可能在其中進行部署的每個位置，尋找發行者、提供項目、SKU 及版本。例如，一些常用的 Linux VM 映像包括︰

**常用 Linux 映像表**

| PublisherName | 提供項目 | SKU |
|:--- |:--- |:--- |:--- |
| RedHat |RHEL |7\.2 |
| credativ |Debian |8 |
| SUSE |openSUSE |13\.2 |
| SUSE |SLES |12-SP1 |
| OpenLogic |CentOS |7\.1 |
| Canonical |UbuntuServer |14\.04.4-LTS |
| CoreOS |CoreOS |Stable |

[!INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0824_2016-->