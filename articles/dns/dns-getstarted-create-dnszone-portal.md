---
title: "如何在 Azure 入口網站中建立和管理 DNS 區域 | Microsoft Docs"
description: "了解如何建立 Azure DNS 中的 DNS 區域。 這份逐步指南，將引導您使用 Azure 入口網站來建立與管理第一個 DNS 區域。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f44c5ea1-4c85-469e-888e-5f5b34451664
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: c9bebc8f54d3f732b3014f6885ee65a067e9d1d8

---

# <a name="create-a-dns-zone-in-the-azure-portal"></a>在 Azure 入口網站中建立 DNS 區域

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

本文將逐步引導您完成使用 Azure 入口網站建立 DNS 區域的步驟。 您也可以使用 PowerShell 或 CLI 建立 DNS 區域。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="create-a-dns-zone"></a>建立 DNS 區域

1. 登入 Azure 入口網站
2. 在 [中樞] 功能表上，按一下 [新增] > [網路] > [DNS 區域] 以開啟 [建立 DNS 區域] 刀鋒視窗。

    ![DNS 區域](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

4. 在 [建立 DNS 區域]  刀鋒視窗中，提供 DNS 區域的 [名稱]。 例如 *contoso.com*。
 
    ![建立區域](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

5. 接下來，指定您想要使用的資源群組。 您可以建立新的資源群組，或選取已經存在的資源群組。 如果您選擇建立新的資源群組，請使用 [位置] 下拉式清單來指定資源群組的位置。 請注意，這項設定是指資源群組的位置，不會對 DNS 區域有任何影響。 DNS 區域一定是「全域」位置，並不會顯示出來。

6. 如果您想要輕鬆地在儀表板上找到您的新區域，您可以讓 [釘選到儀表板] 核取方塊保持選取狀態。 然後按一下 [ **建立**]。

    ![釘選到儀表板](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

7. 按一下 [建立] 之後，您會看到您的新區域已設定到儀表板上。

    ![建立中](./media/dns-getstarted-create-dnszone-portal/creating150.png)

8. 新區域建立好之後，新區域的刀鋒視窗就會在儀表板上開啟。

## <a name="view-records"></a>檢視記錄

建立 DNS 區域也會建立下列記錄：

* 「起始點授權」(SOA) 記錄。 SOA 出現在每個 DNS 區域的根。
* 授權名稱伺服器 (NS) 記錄。 這些顯示哪些名稱伺服器裝載該區域。 Azure DNS 使用名稱伺服器集區，因此，不同的名稱伺服器可能會指派至 Azure DNS 中的不同區域。 如需詳細資訊，請參閱 [將網域委派給 Azure DNS](dns-domain-delegation.md) 。

在 [DNS 區域] 刀鋒視窗的下半部，您可以看到 DNS 區域的記錄集。

![區域](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## <a name="test-name-servers"></a>測試名稱伺服器

您可以使用 nslookup、dig 或 [Resolve-DnsName PowerShell Cmdlet](https://technet.microsoft.com/library/jj590781.aspx) 等 DNS 工具測試您的 DNS 區域在 Azure DNS 名稱伺服器上是否存在。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將 DNS 查詢直接導向您的區域的其中一個名稱伺服器。 Azure 入口網站中會提供您區域的名稱伺服器：
    
![區域](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)

請務必在下列命令中用正確的名稱伺服器取代您的區域。

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = azuredns-hostmaster.microsoft.com
            serial  = 1
            refresh = 3600 (1 hour)
            retry   = 300 (5 mins)
            expire  = 2419200 (28 days)
            default TTL = 300 (5 mins)

## <a name="next-steps"></a>後續步驟

建立 DNS 區域後，請建立 [記錄集和記錄](dns-getstarted-create-recordset-portal.md)，以建立適用於您網際網路網域的 DNS 記錄。




<!--HONumber=Dec16_HO2-->


