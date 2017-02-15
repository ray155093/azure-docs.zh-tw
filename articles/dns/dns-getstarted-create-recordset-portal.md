---
title: "使用 Azure 入口網站建立 DNS 區域的記錄集和記錄 | Microsoft Docs"
description: "如何使用 Azure 入口網站建立 Azure DNS 的主機記錄並建立記錄集和記錄"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f93905f4-e82e-45db-b490-878d318e6aba
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: b3951106fe2e8607e65bd0ae47fa2ea3346b8ca5

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>使用 Azure 入口網站建立 DNS 記錄集和記錄

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

本文將逐步引導您完成使用 Azure 入口網站建立記錄和記錄集的程序。 若要這樣做，您必須先了解 DNS 記錄和記錄集。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

在此頁面上所有範例都使用 'A' DNS 記錄類型。 其他記錄類型的程序很類似。

如果新記錄的名稱和類型與現有記錄相同，您需要將它新增至現有的記錄集&mdash;請參閱[使用 Azure 入口網站管理 DNS 記錄和記錄集](dns-operations-recordsets-portal.md)。 如果新記錄的名稱和類型與現有記錄不同，則必須建立新的記錄集，如下所述。

## <a name="create-records-in-a-new-record-set"></a>在新記錄集中建立記錄

下列範例會逐步解說使用 Azure 入口網站建立記錄集和記錄的程序。

1. 登入入口網站。
2. 移至您要在其中建立記錄集的 [DNS 區域]  刀鋒視窗。
3. 在 [DNS 區域] 刀鋒視窗頂端，選取 [+ 記錄集] 以開啟 [新增記錄集] 刀鋒視窗。

    ![新的記錄集](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. 在 [新增記錄集] 刀鋒視窗中，為您的記錄集命名。 例如，您可以將記錄集命名為 "**www**"。

    ![新增記錄集](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. 選取您想要建立的記錄類型。 例如，選取 [A] 。
6. 設定 **TTL**。 入口網站中預設的存留時間是一小時。
7. 在記錄集新增每個記錄的詳細資料。 在此案例中，記錄類型為 'A'，因此您必須新增 A 記錄 IP 位址，每行一個 IP 位址。
8. 完成新增 IP 位址之後，選取刀鋒視窗底部的 [確定]  。 隨即會建立 DNS 記錄集。

### <a name="verify-name-resolution"></a>驗證名稱解析

您可以使用 nslookup、dig 或 [Resolve-DnsName PowerShell Cmdlet](https://technet.microsoft.com/library/jj590781.aspx) 等 DNS 工具測試您的 DNS 記錄在 Azure DNS 名稱伺服器上是否存在。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將[ DNS 查詢直接導向您區域的其中一個名稱伺服器](dns-getstarted-create-dnszone.md#test-name-servers)。 請務必在下列命令中用正確的值取代您的記錄。

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>後續步驟

了解如何[將網域名稱委派給 Azure DNS 名稱伺服器](dns-domain-delegation.md)

若要管理您的記錄集和記錄，請參閱 [使用 Azure 入口網站管理 DNS 記錄和記錄集](dns-operations-recordsets-portal.md)。



<!--HONumber=Dec16_HO2-->


