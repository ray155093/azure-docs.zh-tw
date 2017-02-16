---
title: "開始使用 Azure DNS | Microsoft Docs"
description: "了解如何建立 Azure DNS 的 DNS 區域。此逐步解說可幫助您建立第一個 DNS 區域，開始使用 PowerShell 來裝載 DNS 網域。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: d78583b7-e669-435c-819b-7605cf791b0e
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 23b7eef53e6fb7bc17c33e54f20d7369cfce52e8

---

# <a name="create-a-dns-zone-using-powershell"></a>使用 PowerShell 建立 DNS 區域

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

本文將逐步引導您完成使用 PowerShell 建立 DNS 區域的步驟。 您也可以使用 CLI 或 Azure 入口網站來建立 DNS 區域。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="a-nametagetagaabout-etags-and-tags"></a><a name="tagetag"></a>關於 Etag 和標籤

### <a name="a-nameetagsaetags"></a><a name="etags"></a>Etag

假設有兩個人或兩個處理序同時嘗試修改 DNS 記錄。 何者獲勝？ 獲勝者知道他剛才已覆寫另一人所做的變更嗎？

Azure DNS 使用 Etag 以安全地處理相同資源的並行變更。 每個 DNS 資源 (區域或記錄集) 都有一個相關聯的 Etag。 每當擷取資源時，也會擷取其 Etag。 更新資源時，您可以選擇傳回 Etag，讓 Azure DNS 可以確認伺服器上的 Etag 相符。 因為每次更新資源都會重新產生 Etag，Etag 不符就表示發生並行變更。 建立新的資源時也會使用 Etag，以確保該資源尚不存在。

根據預設，Azure DNS PowerShell 會使用 Etag 來禁止對區域和記錄集進行並行變更。 選擇性的 *-Overwrite* 參數可以用來停用 Etag 檢查，在此情況下，將覆寫任何已發生的並行變更。

在 Azure DNS REST API 層級上是使用 HTTP 標頭指定 Etag。  下表提供它們的行為：

| 頁首 | 行為 |
| --- | --- |
| None |PUT 一定成功 (沒有 Etag 檢查) |
| If-match <etag> |唯有當資源存在且 Etag 符合時，PUT 才會成功 |
| If-match * |唯有當資源存在時，PUT 才會成功 |
| If-none-match * |唯有當資源不存在時，PUT 才會成功 |

### <a name="a-nametagsatags"></a><a name="tags"></a>標記

標記與 Etag 不同。 標記是名稱-值組的清單，由 Azure Resource Manager 在計費或分群用途上用來標示資源。 如需標記的詳細資訊，請參閱 [使用標記來組織您的 Azure 資源](../resource-group-using-tags.md)。

Azure DNS PowerShell 在區域與記錄集上支援使用選項 `-Tag` 參數來指定「標籤」。

## <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您具備下列項目。

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
* 您必須安裝最新版的 Azure Resource Manager PowerShell Cmdlet  (1.0 或更新版本)。 如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。

## <a name="step-1---sign-in"></a>步驟 1 - 登入

開啟 PowerShell 主控台並連接到您的帳戶。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與 Resource Manager](../powershell-azure-resource-manager.md)。

使用下列範例來協助您連接：

```powershell
Login-AzureRmAccount
```

檢查帳戶的訂用帳戶。

```powershell
Get-AzureRmSubscription
```

指定您要使用的訂用帳戶。

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="step-2---create-a-resource-group"></a>步驟 2 - 建立資源群組

Azure Resource Manager 需要所有的資源群組指定一個位置。 這用來作為該資源群組中資源的預設位置。 然而，因為所有 DNS 資源是全球性，而非區域性，資源群組位置的選擇不會對 Azure DNS 造成影響。

如果您使用現有的資源群組，則可略過此步驟。

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

## <a name="step-3---register"></a>步驟 3 - 註冊

Azure DNS 服務由 Microsoft.Network 資源提供者管理。 您的 Azure 訂用帳戶必須註冊為使用此資源提供者，您才能使用 Azure DNS。 每個訂用帳戶只需執行一次此作業。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="step-4----create-a-dns-zone"></a>步驟 4 - 建立 DNS 區域

使用 `New-AzureRmDnsZone` Cmdlet 建立 DNS 區域。 下列範例使用或不使用標籤建立 DNS 區域。 如需標籤的詳細資訊，請參閱本文中關於 [標籤](#tags) 的一節。

> [!NOTE]
> 在 Azure DNS 中，指定的區域名稱結尾不能有 **'.'**。 例如，指定為 '**contoso.com**'，而非 '**contoso.com**'。

### <a name="to-create-a-dns-zone"></a>建立 DNS 區域

下列範例會在稱為「MyResourceGroup」的資源群組中建立稱為「contoso.com」的 DNS 區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="to-create-a-dns-zone-with-tags"></a>使用標籤建立 DNS 區域

下列範例示範如何使用兩個標記 *project = demo* 和 *env = test* 建立 DNS 區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )
```

## <a name="view-records"></a>檢視記錄

建立 DNS 區域也會建立下列 DNS 記錄：

* 「起始點授權」  (SOA) 記錄。 這出現在每個 DNS 區域的根。
* 授權名稱伺服器 (NS) 記錄。 這些顯示哪些名稱伺服器裝載該區域。 Azure DNS 使用名稱伺服器集區，因此，不同的名稱伺服器可能會指派至 Azure DNS 中的不同區域。 如需詳細資訊，請參閱 [將網域委派給 Azure DNS](dns-domain-delegation.md) 。

若要檢視這些記錄，請使用 `Get-AzureRmDnsRecordSet`：

    Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
    RecordType        : SOA
    Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
    Tags              : {}

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
    Tags              : {}


位於 DNS 區域的根 (或「頂點」 ) 的記錄集使用 **@** 做為記錄集名稱。

## <a name="test"></a>測試

您可以使用 nslookup、dig 或 [Resolve-DnsName PowerShell Cmdlet](https://technet.microsoft.com/library/jj590781.aspx)等 DNS 工具測試您的 DNS 區域。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將 DNS 查詢直接導向您的區域的其中一個名稱伺服器。 如上面的 `Get-AzureRmDnsRecordSet` 所列，NS 記錄提供您區域的名稱伺服器。 請務必在下列命令中用正確的值取代您的區域。

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)

## <a name="next-steps"></a>後續步驟

建立 DNS 區域之後，請建立 [記錄集和記錄](dns-getstarted-create-recordset.md) ，以開始解析您的網際網路網域名稱。



<!--HONumber=Dec16_HO2-->


