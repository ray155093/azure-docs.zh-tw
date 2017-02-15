---
title: "在 Azure 入口網站中建立 VM 的 FQDN | Microsoft Docs"
description: "了解如何在 Azure 入口網站中為基於 Resource Manager 的虛擬機器建立完整格式的網域名稱或 FQDN。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/14/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: f556fd0318accc19f0fa56fa7f2a8716ee6f1c02
ms.openlocfilehash: 72d176803151af5b3c5da50f1f2d72c0dd27f44a


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>在 Azure 入口網站中建立完整格式的網域名稱
在 [Azure 入口網站](https://portal.azure.com) 中使用 Resource Manager 部署模型來建立虛擬機器 (VM) 時，會自動建立虛擬機器的公用 IP 資源。 您可以使用此 IP 位址從遠端存取 VM。 雖然入口網站預設不會建立 [fully qualified domain name (完整網域名稱)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)(或稱 FQDN)，但是您可以在 VM 建立之後建立一個。 這篇文章示範建立 DNS 名稱或 FQDN 的步驟。

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

您現在可以使用這個 DNS 名稱 (例如用於「遠端桌面通訊協定」(RDP)) 從遠端連接到 VM。

## <a name="next-steps"></a>後續步驟
既然您的 VM 已經有公用 IP 和 DNS 名稱，您便可以部署通用應用程式架構或服務，例如 IIS、SQL 或 SharePoint。

您也可以進一步閱讀[使用 Resource Manager](../azure-resource-manager/resource-group-overview.md)，以取得建置 Azure 部署的相關秘訣。




<!--HONumber=Nov16_HO3-->


