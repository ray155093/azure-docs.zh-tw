---
title: "在 Azure 中建立 Windows VM 的不同方式 | Microsoft Docs"
description: "列出使用資源管理員建立 Windows 虛擬機器的不同方式。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 809ba8f4-b54e-43c5-bbe3-8e710c49971f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5e51c49aac01a22d86c7c1a12b2f2ca7ddc056bc
ms.lasthandoff: 03/31/2017


---
# <a name="different-ways-to-create-a-windows-virtual-machine"></a>建立 Windows 虛擬機器的不同方式

Azure 提供建立虛擬機器的不同方式，因為虛擬機器適用於不同的使用者和用途。 這表示您需要針對虛擬機器以及建立方式進行一些選擇。 本文提供這些選項及指示連結的摘要說明。

## <a name="azure-portal"></a>Azure 入口網站
使用 Azure 入口網站是嘗試設定虛擬機器的簡單方法，特別是在您剛開始使用 Azure 時。 

[使用入口網站建立執行 Windows 的虛擬機器](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="template"></a>範本
虛擬機器需要各種資源 (例如可用性設定組和儲存體帳戶)。 您不是分開部署與管理每個資源，而是建立一個 Azure Resource Manager 範本，藉此經由協調的單一作業來部署與佈建所有資源。

* [利用 Resource Manager 範本建立 Windows 虛擬機器](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="azure-powershell"></a>Azure PowerShell
如果您偏好使用命令殼層，可以使用 Azure PowerShell。

* [使用 PowerShell 建立 Windows VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="visual-studio"></a>Visual Studio
使用 Visual Studio 搭配 Azure Tools for Visual Studio 和 Azure SDK 來建置、管理與部署 VM。

[Azure Tools for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)


