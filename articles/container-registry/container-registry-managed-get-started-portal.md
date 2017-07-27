---
title: "建立私人 Docker 登錄 - Azure 入口網站 | Microsoft Docs"
description: "使用 Azure 入口網站開始建立及管理私人 Docker 容器登錄"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: zh-tw
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>使用 Azure 入口網站建立受管理的容器登錄

Azure Container Registry 是用於儲存私用 Docker 容器映像的受管理 Docker 容器登錄服務。 本指南詳述如何使用 Azure 入口網站建立受管理的 Azure Container Registry 執行個體。

受管理 Azure 容器登錄為預覽版本，並不適用於所有區域。

## <a name="log-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-a-container-registry"></a>建立容器登錄庫

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

2. 在 Marketplace 中搜尋 **Azure 容器登錄**，然後選取它。

3. 按一下 [建立]，這樣會開啟 ACR 建立刀鋒視窗。

    ![容器登錄庫設定](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. 在 [Azure 容器登錄] 刀鋒視窗中，輸入下列資訊。 完成後按一下 [建立]。

    a. **登錄名稱**：指定登錄的全域唯一最上層網域名稱。 在此範例中，登錄庫名稱是 *myAzureContainerRegistry1*，但請替換成您自己的唯一名稱。 此名稱只能包含字母和數字。

    b. **資源群組**：選取現有的[資源群組](../azure-resource-manager/resource-group-overview.md#resource-groups)，或輸入新群組的名稱。

    c. **位置**：選取[可使用](https://azure.microsoft.com/regions/services/)此服務的 Azure 資料中心位置，例如 [美國中南部]。

    d. **管理員使用者**：如果您想，可啟用管理員使用者存取登錄。 您可以在建立登錄庫後變更此設定。

    e. **使用受管理登錄**：選取 [是]，讓 ACR 自動管理登錄儲存體、使用 Webhook，以及使用 AAD 驗證。

    f. **定價層**：選取定價層，以在這裡查看 ACR 定價的詳細資訊。

## <a name="log-in-to-acr-instance"></a>登入 ACR 執行個體

發送和提取容器映像之前，您必須登入 ACR 執行個體。 

若要這麼做，請使用 Azure CLI 2.0。 首先，如有需要，請使用 [az login](/cli/azure/#login) 命令登入 Azure。 

```azurecli
az login
```

接下來，使用 [az acr login](/cli/azure/acr#login) 命令登入 Azure Container Registry。

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

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

在本快速入門中，您已使用 Azure 入口網站建立受管理的 Azure Container Registry 執行個體。

> [!div class="nextstepaction"]
> [使用 Docker CLI 推送您的第一個映像](container-registry-get-started-docker-cli.md)
