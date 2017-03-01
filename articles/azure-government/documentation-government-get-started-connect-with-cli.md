---
title: "使用 Azure CLI 連接到 Azure Government | Microsoft Docs"
description: "透過Azure CLI 連接，在 Azure Government 中管理訂用帳戶的相關資訊"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c7cbe993-375e-4aed-9aa5-2f4b2111f71c
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 6e070870a93d2636f076b339dd401358735a9bd0
ms.lasthandoff: 02/21/2017


---


# <a name="connect-to-azure-government-with-azure-cli"></a>使用 Azure CLI 連接到 Azure Government

若要使用 Azure CLI，您需要連接到 Azure Government，而不是 Azure Public。 Azure CLI 可透過指令碼用來管理大量訂用帳戶，或用來存取 Azure 入口網站中目前無法使用的功能。 如果您是在 Azure Public 中使用 Azure CLI，則幾乎相同。  Azure Government 中的差異如下︰

有多種方式可用來[安裝 Azure CLI](https://docs.microsoft.com/en-us/azure/xplat-cli-install)。 如果您已經安裝 Node，最簡單的方式就是安裝 npm 套件：

若要從 npm 套件安裝 CLI，請確定已下載並安裝[最新的 Node.js 和 npm](https://nodejs.org/en/download/package-manager/)。 然後，執行 **npm 安裝**，以安裝 azure-cli 套件：

```bash
npm install -g azure-cli
```

在 Linux 散發套件上，您可能需要使用 **sudo**，才能順利執行 **npm** 命令，如下所示：

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> 如果您需要在 Linux 散發套件或作業系統上安裝或更新 Node.js 和 npm，建議您安裝最新的 Node.js LTS 版本 (4.x)。 如果您使用較舊的版本，可能會發生安裝錯誤。


安裝 Azure CLI 之後，您必須登入 Azure Government：

```
azure login --username your-user-name@your-gov-tenant.onmicrosoft.com  --environment AzureUSGovernment
```

一旦登入之後，就可以像平常一樣執行 Azure CLI 命令：

```
azure webapp list my-resource-group
```
