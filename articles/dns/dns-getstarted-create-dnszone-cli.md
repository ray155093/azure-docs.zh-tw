---
title: "使用 CLI 建立 DNS 區域 | Microsoft Docs"
description: "了解如何建立 Azure DNS 中的 DNS 區域。 這份逐步指南，將引導您使用 Azure CLI 來建立與管理第一個 DNS 區域。"
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
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 5bbd490925e5e25f10044af55af49daa494ee026

---

# <a name="create-an-azure-dns-zone-using-cli"></a>使用 CLI 建立 Azure DNS 區域

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

本文將逐步引導您使用跨平台 Azure CLI 此適用 Windows、Mac 和 Linux) 來建立 DNS 區域，。 您也可以使用 PowerShell 或 Azure 入口網站來建立 DNS 區域。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您具備下列項目。

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
* 您必須安裝最新版的 Azure CLI，該 CLI 適用於 Windows、Linux 或 MAC。 您可以在 [安裝 Azure CLI](../xplat-cli-install.md)中取得詳細資訊。

## <a name="step-1---sign-in-and-create-a-resource-group"></a>步驟 1 - 登入並建立資源群組

### <a name="switch-cli-mode"></a>切換 CLI 模式

Azure DNS 使用 Azure Resource Manager。 請確定您已將 CLI 模式切換為使用 ARM 命令。

```azurecli
azure config mode arm
```

### <a name="sign-in-to-your-azure-account"></a>登入您的 Azure 帳戶

系統會提示使用您的認證進行驗證。 請注意，您只能使用 OrgID 帳戶。

```azurecli
azure login
```

### <a name="select-the-subscription"></a>選取訂用帳戶

檢查帳戶的訂用帳戶。

```azurecli
azure account list
```

選擇要使用哪一個 Azure 訂用帳戶。

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>建立資源群組

Azure Resource Manager 需要所有的資源群組指定一個位置。 這用來作為該資源群組中資源的預設位置。 然而，因為所有 DNS 資源是全球性，而非區域性，資源群組位置的選擇不會對 Azure DNS 造成影響。

如果您使用現有的資源群組，則可略過此步驟。

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>註冊資源提供者

Azure DNS 服務由 Microsoft.Network 資源提供者管理。 您的 Azure 訂用帳戶必須註冊為使用此資源提供者，您才能使用 Azure DNS。 每個訂用帳戶只需執行一次此作業。

```azurecli
azure provider register --namespace Microsoft.Network
```

## <a name="step-2---create-a-dns-zone"></a>步驟 2 - 建立 DNS 區域

使用 `azure network dns zone create` 命令建立 DNS 區域。 若要查看此命令的說明，請輸入 `azure network dns zone create -h`。

下列範例會在稱為「MyResourceGroup」的資源群組中建立稱為「contoso.com」的 DNS 區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="step-3---verify"></a>步驟 3 - 驗證

### <a name="view-records"></a>檢視記錄

建立 DNS 區域也會建立下列 DNS 記錄：

* 「起始點授權」(SOA) 記錄。 這出現在每個 DNS 區域的根。
* 授權名稱伺服器 (NS) 記錄。 這些顯示哪些名稱伺服器裝載該區域。 Azure DNS 使用名稱伺服器集區，因此，不同的名稱伺服器可以指派至 Azure DNS 中的不同區域。 如需詳細資訊，請參閱 [將網域委派給 Azure DNS](dns-domain-delegation.md) 。

若要檢視這些記錄，請使用 `azure network dns-record-set list`：

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> 位於 DNS 區域的根 (或「頂點」 ) 的記錄集使用 **@** 做為記錄集名稱。

### <a name="test-name-servers"></a>測試名稱伺服器

您可以使用 nslookup、dig 或 `Resolve-DnsName` PowerShell Cmdlet 等 DNS 工具測試您的 DNS 區域在 Azure DNS 名稱伺服器上是否存在。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將 DNS 查詢直接導向您的區域的其中一個名稱伺服器。 如上面的 "azure network dns record-set show" 所列，NS 記錄提供您區域的名稱伺服器。 請務必在下列命令中用正確的值取代您的區域。

下列範例使用 'dig'，使用為 DNS 區域指派的名稱伺服器來查詢網域 contoso.com。 使用 'dig' 的查詢必須指向我們將 *@\<區域的名稱伺服器\>* 和區域名稱用於的名稱伺服器。

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

## <a name="next-steps"></a>後續步驟

建立 DNS 區域後，請建立 [記錄集和記錄](dns-getstarted-create-recordset-cli.md)，以建立適用於您網際網路網域的 DNS 記錄。




<!--HONumber=Dec16_HO2-->


