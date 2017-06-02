---
title: "將作用中的自訂網域移轉至 Azure App Service | Microsoft Docs"
description: "了解如何在完全不停機的情況下，將已指派給即時網站的自訂網域移轉至 Azure App Service 中的應用程式。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 6adc34f208c6c4363b73f6e42b4a4d6ae2e4483f
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017


---
# <a name="migrate-an-active-custom-domain-to-azure-app-service"></a>將作用中的自訂網域移轉至 Azure App Service

本文將說明如何在完全不停機的情況下，將作用中的自訂網域移轉至 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。

當您將即時網站及其網域名稱移轉至 App Service 時，該網域名稱已對即時流量提供服務，而您不想要在移轉期間的 DNS 解析時發生停機。 在此情況下，您必須先將網域名稱繫結至 Azure 應用程式以進行網域驗證。 

## <a name="prerequisites"></a>必要條件

此文章假設您已經知道如何[手動將自訂網域對應至 App Service](app-service-web-tutorial-custom-domain.md)。

## <a name="bind-the-domain-name-preemptively"></a>事先繫結網域名稱

當您事先繫結自訂網域時，要完成下列兩項之後才能對 DNS 記錄進行任何變更︰

- 確認網域擁有權
- 為您的應用程式啟用網域名稱

當您最後將 DNS 記錄變更為指向您的 App Service 應用程式，DNS 解析時可在完全不停機的情況下，將用戶端從舊網站重新導向您的 App Service 應用程式。

請依照下列步驟執行：

1. 首先，依照下列步驟，使用 DNS 登錄建立驗證 TXT 記錄：[建立 DNS 記錄](app-service-web-tutorial-custom-domain.md#create-a)。
額外的 TXT 記錄會採用從 &lt;*subdomain*>.&lt;*rootdomain*> 對應至 &lt;*appname*>.azurewebsites.net 的慣例。
請參閱下表中的範例：  
 
    <table cellspacing="0" border="1">
    <tr>
    <th>FQDN 範例</th>
    <th>TXT 主機</th>
    <th>TXT 值</th>
    </tr>
    <tr>
    <td>contoso.com (根網域)</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>www.contoso.com (子網域)</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>\*.contoso.com (萬用字元)</td>
    <td>awverify\*.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    </table>

2. 然後，依照下列步驟，將自訂網域名稱新增至 Azure 應用程式：[為您的應用程式啟用自訂網域名稱](app-service-web-tutorial-custom-domain.md#enable-a)。

    您的自訂網域現已在您的 Azure 應用程式中啟用。 您只需向網域註冊機構更新 DNS 記錄即可。

3. 最後，更新網域的 DNS 記錄以指向您的 Azure 應用程式，如下所示：[建立 DNS 記錄](app-service-web-tutorial-custom-domain.md#create-a)。 

    在 DNS 傳播發生之後，使用者流量應重新導向至 Azure 應用程式。

## <a name="next-steps"></a>後續步驟
了解如何[在 Azure 中購買 SSL 憑證](web-sites-purchase-ssl-web-site.md)或[使用來自其他位置的 SSL 憑證](app-service-web-tutorial-custom-ssl.md)來使用 HTTPS 保護自訂網域名稱。

> [!NOTE]
> 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](https://azure.microsoft.com/try/app-service/)，即可在 App Service 中立即建立短期入門 Web 應用程式。 不需要信用卡；沒有承諾。
> 
> 

[開始使用 Azure DNS](../dns/dns-getstarted-create-dnszone.md)  
[在自訂網域中建立 Web 應用程式的 DNS 記錄](../dns/dns-web-sites-custom-domain.md)  
[將網域委派給 Azure DNS](../dns/dns-domain-delegation.md)


