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
ms.date: 12/07/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 927503bb18a63db1da2c92e034dbccb7707afab4

---

# <a name="how-to-manage-dns-zones-using-cli"></a>如何使用 CLI 管理 DNS 區域

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

本指南將示範如何使用跨平台 Azure CLI 管理 DNS 區域資源。

這些指示使用 Microsoft Azure CLI。 請務必更新至最新的 Azure CLI，才能使用這些 Azure DNS 命令。 您可以安裝適用於 Windows、Linux 或 MAC 的 Azure CLI。 您可以在 [安裝 Azure CLI](../xplat-cli-install.md)中取得詳細資訊。

Azure DNS 是僅能以 Azure 資源管理員運作的服務。 它沒有「傳統」部署模型。 您需要確定 Azure CLI 已設定為使用 Resource Manager 模式。 您可以使用 `azure config mode arm` 命令執行此工作。

如果您看到訊息「錯誤：'dns' 不是 azure 命令」，可能是因為您正在 Azure 服務管理模式中使用 Azure CLI，而不是在 Resource Manager 模式中。

所有與 Azure DNS 相關的 CLI 命令都會以 `azure network dns` 開頭。 使用 `--help` 選項 (簡短形式為 `-h`) 即可取得每個命令的說明。  例如：

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>建立 DNS 區域

使用 `azure network dns zone create` 命令建立 DNS 區域。 如需協助，請參閱 `azure network dns zone create -h`。

下列範例示範如何建立具有或不具有 [Azure Resource Manager 標籤](dns-zones-records.md#tags)的 DNS 區域。

### <a name="to-create-a-dns-zone"></a>建立 DNS 區域

下列範例會在稱為「MyResourceGroup」的資源群組中建立稱為「contoso.com」的 DNS 區域。 使用範例來建立您的 DNS 區域，並將值替換為您自己的值。

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>使用標籤建立 DNS 區域。

下列範例示範如何使用 `--tags` 參數 (簡短形式為 `-t`) 建立具有兩個標籤 project = demo 和 env = test 的 DNS 區域。

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>取得 DNS 區域

若要擷取 DNS 區域，請使用 `azure network dns zone show`。 如需協助，請參閱 `azure network dns zone show -h`。

下列範例會從資源群組 MyResourceGroup 傳回 DNS 區域 contoso.com 及其相關聯的資料。 

```azurecli
azure network dns zone show MyResourceGroup contoso.com

info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

請注意，`azure network dns zone show` 不會傳回 DNS 記錄。 若要列出 DNS 記錄，請使用 `azure network dns record-set list`。


## <a name="list-dns-zones"></a>列出 DNS 區域

若要列舉 DNS 區域，請使用 `azure network dns zone list`。 如需協助，請參閱 `azure network dns zone list -h`。

指定資源群組只會列出資源群組內的區域︰

```azurecli
azure network dns zone list MyResourceGroup
```

省略資源群組則會列出訂用帳戶中的所有區域︰

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>更新 DNS 區域

您可以使用 `azure network dns zone set`變更 DNS 區域資源。 如需協助，請參閱 `azure network dns zone set -h`。

此命令不會更新區域內的任何 DNS 記錄集 (請參閱[如何管理 DNS 記錄](dns-operations-recordsets.md))。 它只用來更新區域資源本身的屬性。 這些屬性目前僅限於區域資源的 [Azure Resource Manager「標籤」](dns-zones-records.md#tags)。

下列範例示範如何更新 DNS 區域上的標籤。 現有標籤會由指定值取代。

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>刪除 DNS 區域

可以使用 `azure network dns zone delete`刪除 DNS 區域。 如需協助，請參閱 `azure network dns zone delete -h`。

> [!NOTE]
> 刪除 DNS 區域也會刪除該區域內的所有 DNS 記錄。 此作業無法復原。 如果 DNS 區域正在使用中，當該區域遭到刪除時，使用該區域的服務將會失敗。
>
>若要防止區域意外遭到刪除，請參閱[如何保護 DNS 區域和記錄](dns-protect-zones-recordsets.md)。

此命令會提示您確認。 選擇性的 `--quiet` 參數 (簡短形式為 `-q`) 會隱藏這個提示。

下列範例示範如何從資源群組 MyResourceGroup 刪除區域 contoso.com。

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>後續步驟

了解如何在 DNS 區域中[管理記錄集和記錄](dns-getstarted-create-recordset-cli.md)。
<br>
了解如何[將您的網域委派給 Azure DNS](dns-domain-delegation.md)。




<!--HONumber=Dec16_HO2-->


