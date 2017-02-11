---
title: "訂用帳戶和帳戶指導方針 | Microsoft Docs"
description: "了解 Azure 上訂用帳戶和帳戶的關鍵設計和實作指導方針。"
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 761fa847-78b0-4078-a33a-d95d198d1029
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b410436de11550aab2160421abfb27107f7874b0


---
# <a name="subscription-and-accounts-guidelines"></a>訂用帳戶和帳戶指導方針
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

本文著重於了解在環境和使用者群增加的情況下，管理訂用帳戶和帳戶的方法。

## <a name="implementation-guidelines-for-subscriptions-and-accounts"></a>訂用帳戶和帳戶的實作指導方針
决策：

* 您需要裝載 IT 工作負載或基礎結構的是哪種訂用帳戶與帳戶的組合？
* 如何分解階層以使其符合您的組織？

工作：

* 將您的邏輯組織階層定義為您想從訂用帳戶層級管理它的方式。
* 若要符合此邏輯階層，請定義所需的帳戶及每個帳戶下的訂用帳戶。
* 使用您的命名慣例來建立訂用帳戶與帳戶的組合。

## <a name="subscriptions-and-accounts"></a>訂用帳戶與帳戶
若要使用 Azure，您需要一個以上的 Azure 訂用帳戶。 虛擬機器 (VM) 或虛擬網路等資源存在於這些訂用帳戶中。

* 企業客戶通常會有 Enterprise 註冊，這是階層中最上層的資源，而且會與一或多個帳戶相關聯。
* 對於消費者與不具 Enterprise 註冊的客戶來說，最上層的資源是帳戶。
* 訂用帳戶會關聯至帳戶，而且每個帳戶可以有一或多個訂用帳戶。 Azure 會在訂用帳戶層級記錄計費資訊。

由於帳戶/訂用帳戶關係中有兩個階層層級的限制，因此，請務必將帳戶和訂用帳戶的命名慣例與計費需求保持一致。 舉例來說，如果有一家全球性公司使用 Azure，他們可能選擇針對每個區域擁有一個帳戶，並在區域層級管理訂用帳戶：

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

例如，您可以使用下列結構：

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

如果某個區域決定擁有一個以上與特定群組相關聯的訂用帳戶，則命名慣例必須包括在帳戶或訂用帳戶名稱上為額外資料進行編碼的方式。 這個組織允許訊息傳遞計費資料在計費報告期間產生新的階層層級：

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

組織應看起來如下：

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

我們會透過可下載的檔案，針對單一帳戶或企業合約中的所有帳戶提供詳細的計費資訊。

## <a name="next-steps"></a>後續步驟
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Nov16_HO3-->


