---
title: "在 AAD 中針對 Windows 建立工作或學校身分識別 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 中建立與 Windows 虛擬機器搭配使用的工作或學校身分識別。"
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: d07dca34-618a-48aa-9971-03d9c1210f4a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 7694b959a384aaed213adc31e02debca31b7c131
ms.lasthandoff: 04/03/2017


---
# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-windows-vms"></a>在 Azure Active Directory 中建立與 Windows VM 搭配使用的工作或學校身分識別
如果您已建立個人 Azure 帳戶，或有個人 MSDN 訂用帳戶並已建立 Azure 帳戶來運用 MSDN Azure 點數 -- 即表示您已使用「Microsoft 帳戶」  身分識別建立它。 Azure 有許多很棒的功能 -- 例如 [資源群組範本](../../azure-resource-manager/resource-group-overview.md) -- 需要工作或學校帳戶 (由 Azure Active Directory 管理的身分識別) 才能運作。 幸運的是，預設的 Azure Active Directory 網域會提供您個人的 Azure 帳戶，讓您可以用來建立新的工作或學校帳戶，以搭配需要這類帳戶使用的 Azure 功能，因此您可以遵循下列指示來建立新的工作或學校帳戶。

不過，最近的變更讓您可以使用[這裡](../../xplat-cli-connect.md)所述的 `azure login` 互動式登入方法，以任何類型的 Azure 帳戶來管理您的訂用帳戶。 您可以使用同樣的機制，或者可以遵循接下來的指示。 也可以 [在 Azure Active Directory 中建立與 Linux VM 搭配使用的工作或學校身分識別](../linux/create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

[!INCLUDE [virtual-machines-common-create-aad-work-id](../../../includes/virtual-machines-common-create-aad-work-id.md)]


