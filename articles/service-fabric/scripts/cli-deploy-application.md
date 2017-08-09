---
title: "Azure CLI 指令碼範例 - 將應用程式部署至叢集"
description: "Azure CLI 指令碼範例 - 將應用程式部署到 Service Fabric 叢集。"
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: de6ec4378a05656ecefefa7d5994a4f5dad404ba
ms.contentlocale: zh-tw
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>將應用程式部署到 Service Fabric 叢集

這個範例指令碼會將應用程式封裝複製到叢集映像存放區、在叢集中註冊應用程式類型，並從應用程式類型建立應用程式執行個體。  如果已在目標應用程式類型的應用程式資訊清單中定義任何預設服務，則這些服務也會一併建立。

視需要安裝 [Azure CLI](../service-fabric-azure-cli-2-0.md)。

## <a name="sample-script"></a>範例指令碼

[!code-sh[主要](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "將應用程式部署到叢集")]

## <a name="clean-up-deployment"></a>清除部署 

指令碼範例執行之後，[移除應用程式](cli-remove-application.md)中的指令碼可用來移除應用程式執行個體、取消註冊應用程式類型，並且從映像存放區刪除應用程式封裝。

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。


| 命令 | 注意事項 |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | 選取要使用的叢集。 |
| [sf application upload](/cli/azure/sf/application#upload) | 上傳應用程式檔案和資訊清單。 |
| [sf application provision](/cli/azure/sf/application#provision) | 在叢集上註冊應用程式。|
| [sf application create](/cli/azure/sf/application#create) | 建立應用程式的執行個體，並將任何已定義的服務部署到節點。 |

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Azure CLI 文件](../service-fabric-azure-cli-2-0.md)。

您可以在 [Azure CLI 範例](../samples-cli.md)中找到適用於 Azure Service Fabric 的其他 Azure CLI 範例。

