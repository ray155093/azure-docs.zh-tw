---
title: 使用 Azure 入口網站建立 DNS 區域的記錄集和記錄 | Microsoft Docs
description: 如何使用 Azure 入口網站建立 Azure DNS 的主機記錄並建立記錄集和記錄
services: dns
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: cherylmc

---
# 使用 Azure 入口網站建立 DNS 記錄集和記錄
> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)
> 
> 

本文將逐步引導您完成使用 Azure 入口網站建立記錄和記錄集的程序。建立 DNS 區域之後，您將會新增網域的 DNS 記錄。若要這樣做，您必須先了解 DNS 記錄和記錄集。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## 建立記錄集和記錄
下列範例會逐步解說使用 Azure 入口網站建立記錄集和記錄的程序。我們將使用 DNS "A" 記錄類型。

1. 登入入口網站。
2. 移至您要在其中建立記錄集的 [DNS 區域] 刀鋒視窗。
3. 在 [DNS 區域] 刀鋒視窗頂端，選取 [記錄集] 以開啟 [新增記錄集] 刀鋒視窗。
   
    ![新的記錄集](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)
4. 在 [新增記錄集] 刀鋒視窗中，為您的記錄集命名。例如，您可以將記錄集命名為 "**www**"。
   
    ![新增記錄集](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)
5. 選取您想要建立的記錄類型。例如，選取 [A]。
6. 設定 **TTL**。入口網站中預設的存留時間是一小時。
7. 新增 IP 位址，每行一個 IP 位址。當您使用先前所述的建議記錄集名稱和記錄類型時，您會將 IPv4 IP 位址新增到 www 記錄集的 **A** 記錄。
8. 完成新增 IP 位址之後，選取刀鋒視窗底部的 [確定]。隨即會建立 DNS 記錄集。

## 後續步驟
若要管理您的記錄集和記錄，請參閱[使用 Azure 入口網站管理 DNS 記錄和記錄集](dns-operations-recordsets-portal.md)。

如需 Azure DNS 的詳細資訊，請參閱 [Azure DNS 概觀](dns-overview.md)。

<!---HONumber=AcomDC_0817_2016-->