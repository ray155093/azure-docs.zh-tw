---
title: "使用 Azure CLI 2.0 建立 DNS 區域 | Microsoft Docs"
description: "了解如何建立 Azure DNS 中的 DNS 區域。 這份逐步指南，將引導您使用 Azure CLI 2.0 來建立與管理第一個 DNS 區域。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 03/01/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>使用 Azure CLI 2.0 建立 Azure DNS 區域

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

本文將逐步引導您使用跨平台 Azure CLI 此適用 Windows、Mac 和 Linux) 來建立 DNS 區域，。 您也可以使用 [Azure PowerShell](dns-getstarted-create-dnszone.md) 或 [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)來建立 DNS 區域。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本

您可以使用下列其中一個 CLI 版本來完成工作︰

* [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md) - 適用於傳統和資源管理部署模型的 CLI。
* [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md) - 適用於資源管理部署模型的新一代 CLI。

## <a name="introduction"></a>簡介

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>設定適用於 Azure DNS 的 Azure CLI 2.0

### <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您具備下列項目。

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

* 安裝最新版的 Azure CLI，該 CLI 適用於 Windows、Linux 或 MAC。 您可以在[安裝 Azure CLI 2.0 (英文)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2) 中取得詳細資訊。

### <a name="sign-in-to-your-azure-account"></a>登入您的 Azure 帳戶

開啟主控台視窗，並驗證您的認證。 如需詳細資訊，請參閱＜從 Azure CLI 登入 Azure＞

```azurecli
az login
```

### <a name="select-the-subscription"></a>選取訂用帳戶

檢查帳戶的訂用帳戶。

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>選擇要使用哪一個 Azure 訂用帳戶。

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>建立資源群組

Azure Resource Manager 需要所有的資源群組指定一個位置。 這用來作為該資源群組中資源的預設位置。 然而，因為所有 DNS 資源是全球性，而非區域性，資源群組位置的選擇不會對 Azure DNS 造成影響。

如果您使用現有的資源群組，則可略過此步驟。

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>建立 DNS 區域

使用 `az network dns zone create` 命令建立 DNS 區域。 若要查看此命令的說明，請輸入 `az network dns zone create --help`。

下列範例會在稱為 MyResourceGroup 的資源群組中建立稱為 contoso.com 的 DNS 區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>確認您的 DNS 區域

### <a name="view-records"></a>檢視記錄

建立 DNS 區域也會建立下列 DNS 記錄：

* 「起始點授權」  (SOA) 記錄。 此記錄出現在每個 DNS 區域的根。
* 授權名稱伺服器 (NS) 記錄。 這些記錄顯示哪些名稱伺服器裝載該區域。 Azure DNS 使用名稱伺服器集區，因此，不同的名稱伺服器可能會指派至 Azure DNS 中的不同區域。 如需詳細資訊，請參閱[將網域委派給 Azure DNS](dns-domain-delegation.md)。

若要檢視這些記錄，請使用 `az network dns record-set list`：

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

`az network dns record-set list` 的回應如下所示︰

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> 位於 DNS 區域的根 (或「頂點」 ) 的記錄集使用 **@** 做為記錄集名稱。

### <a name="test-name-servers"></a>測試名稱伺服器

您可以使用 nslookup、dig 或 `Resolve-DnsName` PowerShell Cmdlet 等 DNS 工具測試您的 DNS 區域在 Azure DNS 名稱伺服器上是否存在。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將 DNS 查詢直接導向您的區域的其中一個名稱伺服器。 如 `az network dns record-set list` 所提供，NS 記錄提供您區域的名稱伺服器。

下列範例使用 'dig'，使用指派給 DNS 區域的名稱伺服器來查詢網域 contoso.com。 請務必用您區域的正確值替代。

```
  > dig @ns1-01.azure-dns.com contoso.com
  
  <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
(1 server found)
global options: +cmd
  Got answer:
->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
  flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
  WARNING: recursion requested but not available

  OPT PSEUDOSECTION:
  EDNS: version: 0, flags:; udp: 4000
  QUESTION SECTION:
contoso.com.                        IN      A

  AUTHORITY SECTION:
contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

Query time: 93 msec
SERVER: 208.76.47.5#53(208.76.47.5)
WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
MSG SIZE  rcvd: 120
```

## <a name="next-steps"></a>後續步驟

建立 DNS 區域後，請在您的區域中[建立 DNS 記錄集和記錄](dns-getstarted-create-recordset-cli.md)。


