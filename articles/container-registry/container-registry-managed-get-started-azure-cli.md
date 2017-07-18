---
title: "建立私人 Docker 容器登錄 - Azure CLI | Microsoft Docs"
description: "使用 Azure CLI 2.0 開始建立及管理私人 Docker 容器登錄"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: zh-tw
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-cli"></a>使用 Azure CLI 建立受管理的容器登錄

Azure Container Registry 是用於儲存私用 Docker 容器映像的受管理 Docker 容器登錄服務。 本指南詳述如何使用 Azure CLI 建立受管理的 Azure Container Registry 執行個體。

受管理 Azure 容器登錄為預覽版本，並不適用於所有區域。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 *westcentralus* 位置建立名為 *myResourceGroup* 的資源群組。

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-container-registry"></a>建立容器登錄庫

使用 [az acr create](/cli/azure/acr#create) 命令，以建立 ACR 執行個體。

> [!NOTE]
> 當您建立登錄庫時，請指定含有字母和數字的全域唯一最上層網域名稱。

 範例中的登錄名稱是 *myContainerRegistry1*，請替換成您自己的唯一名稱。

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

建立登錄時，輸出大致如下：

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="log-in-to-acr-instance"></a>登入 ACR 執行個體

發送和提取容器映像之前，您必須登入 ACR 執行個體。 若要這樣做，請使用 [az acr login](/cli/azure/acr#login) 命令。

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

此命令在完成之後會傳回「登入成功」訊息。

## <a name="use-azure-container-registry"></a>使用 Azure Container Registry

### <a name="list-container-images"></a>列出容器映像

使用 `az acr` CLI 命令來查詢儲存機制中的映像和標籤。

> [!NOTE]
> 目前，容器登錄庫不支援用 `docker search` 命令查詢映像和標籤。

### <a name="list-repositories"></a>列出儲存機制

下列範例會以 JSON (JavaScript 物件標記法) 格式列出登錄庫中的儲存機制︰

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>列出標籤

下列範例會以 JSON 格式列出 **samples/nginx** 儲存機制上的標籤：

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure CLI 建立受管理的 Azure Container Registry 執行個體。

> [!div class="nextstepaction"]
> [使用 Docker CLI 推送您的第一個映像](container-registry-get-started-docker-cli.md)

