---
title: "在 OpenSUSE VM 上安裝 MySQL | Microsoft Docs"
description: "了解如何在 Azure 中的 OpenSUSE Linux 虛擬機器上安裝 MySQL。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: ff9e23e9324cc47ae1a5d7cb52f13920000b8557


---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>在 Azure 中執行 OpenSUSE Linux 的虛擬機器上安裝 MySQL
[MySQL][MySQL] 是一種很受歡迎的開放原始碼 SQL 資料庫。 本教學課程會示範如何建立執行 OpenSUSE Linux 的虛擬機器，然後安裝 MySQL。

> [!IMPORTANT] 
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>建立執行 OpenSUSE Linux 的虛擬機器
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>在虛擬機器上安裝和執行 MySQL
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>後續步驟
如需 MySQL 的詳細資訊，請參閱 [MySQL 文件][MySQLDocs]。

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com




<!--HONumber=Dec16_HO1-->


