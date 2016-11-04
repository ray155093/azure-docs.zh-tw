---
title: 瀏覽並選取 Windows VM 映像 | Microsoft Docs
description: 了解如何在使用資源管理員部署模型建立 Windows 虛擬機器時，判斷映像的發行者、訂閱詳情及 SKU。
services: virtual-machines-windows
documentationcenter: ''
author: squillace
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill

---
# 使用 PowerShell 或 CLI 在 Azure 中瀏覽並選取 Windows 虛擬機器映像
本主題說明如何針對您可能在其中進行部署的每個位置，尋找 VM 映像發行者、提供項目、SKU 及版本。例如，一些常用的 Windows VM 映像包括︰

## 常用 Windows 映像表
| PublisherName | 提供項目 | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-DW |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftWindowsServer |WindowsServer |Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

[!INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0824_2016-->