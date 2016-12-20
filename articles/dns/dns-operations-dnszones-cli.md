---
title: "使用 CLI 管理 DNS 區域 | Microsoft Docs"
description: "您可以使用 Azure CLI 管理 DNS 區域。 如何在 Azure DNS 上更新、刪除及建立 DNS 區域"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: 6fe10238adea0cbe1a47c05767930a363645028b

---

# <a name="how-to-manage-dns-zones-using-cli"></a>如何使用 CLI 管理 DNS 區域

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

本指南將示範如何使用跨平台 Azure CLI 管理 DNS 區域資源。

這些指示使用 Microsoft Azure CLI。 請務必更新至最新的 Azure CLI (0.9.8 或更新版本)，才能使用 Azure DNS 命令。 請輸入 `azure -v` ，以檢查電腦中安裝的 Azure CLI 版本。 您可以安裝適用於 Windows、Linux 或 MAC 的 Azure CLI。 您可以在 [安裝 Azure CLI](../xplat-cli-install.md)中取得詳細資訊。

Azure DNS 是僅能以 Azure 資源管理員運作的服務。 它沒有 ASM API。 您需要確定 Azure CLI 已設定為使用 Resource Manager 模式。 您可以使用 `azure config mode arm` 命令執行此工作。

如果您看到錯誤：「*'dns' 不是 azure 命令*」訊息，可能是因為您正在 ASM 模式中使用 Azure CLI，而非 Resource Manager 模式中。

## <a name="create-a-new-dns-zone"></a>建立新的 DNS 區域

若要建立新的 DNS 區域來裝載您的網域，請參閱 [使用 CLI 建立 Azure DNS 區域](dns-getstarted-create-dnszone-cli.md)。

## <a name="get-a-dns-zone"></a>取得 DNS 區域

若要擷取 DNS 區域，請使用 `azure network dns zone show`：

```azurecli
azure network dns zone show myresourcegroup contoso.com
```

此作業會傳回 DNS 區域的識別碼、資料錄集和標記的數量。

## <a name="list-dns-zones"></a>列出 DNS 區域

若要擷取資源群組中的 DNS 區域，請使用 `azure network dns zone list`。

```azurecli
azure network dns zone list myresourcegroup
```

## <a name="update-a-dns-zone"></a>更新 DNS 區域

您可以使用 `azure network dns zone set`變更 DNS 區域資源。 這不會更新區域內的任何 DNS 記錄集 (請參閱 [如何管理 DNS 記錄](dns-operations-recordsets.md))。 它只用來更新區域資源本身的屬性。 這在目前限於區域資源的 Azure Resource Manager「標籤」。 如需詳細資訊，請參閱 [Etag 和標記](dns-getstarted-create-dnszone.md#tagetag) 。

```azurecli
azure network dns zone set myresourcegroup contoso.com -t prod=value2
```

## <a name="delete-a-dns-zone"></a>刪除 DNS 區域

可以使用 `azure network dns zone delete`刪除 DNS 區域。 此作業具有選擇性的 *-q* 參數，可隱藏要求您確認是否想要移除 DNS 區域的提示。

在 Azure DNS 中刪除 DNS 區域之前，您必須刪除所有記錄集，但建立區域時在區域的根自動建立的 NS 和 SOA 記錄除外。

```azurecli
azure network dns zone delete myresourcegroup contoso.com
```

## <a name="next-steps"></a>後續步驟

建立 DNS 區域之後，請建立 [記錄集和記錄](dns-getstarted-create-recordset-cli.md) ，以開始解析您的網際網路網域名稱。




<!--HONumber=Nov16_HO3-->


