---
title: 管理 Linux VM 的可用性 | Microsoft Docs
description: 了解如何使用多部虛擬機器，確保 Azure 中 Linux 應用程式的高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/25/2016
ms.author: cynthn

---
# 管理虛擬機器的可用性
了解如何設定及管理多部虛擬機器，以確保 Azure 中 Linux 應用程式的高可用性。您也可以[管理 Windows 虛擬機器的可用性](virtual-machines-windows-manage-availability.md)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

如需有關使用 CLI 在 Resource Manger 部署模型中建立可用性設定組的指示，請參閱 [azure availset：用來管理可用性設定組的命令](azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets)。

[!INCLUDE [virtual-machines-common-manage-availability](../../includes/virtual-machines-common-manage-availability.md)]

## 後續步驟
若要深入了解如何對虛擬機器進行負載平衡，請參閱[對虛擬機器進行負載平衡](virtual-machines-linux-load-balance.md)。

<!---HONumber=AcomDC_0601_2016-->