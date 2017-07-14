---
title: "Azure 資訊安全中心中的合作夥伴整合 | Microsoft 文件"
description: "此文件說明 Azure 資訊安全中心如何與合作夥伴整合，以提高您 Azure 資源的整體安全性。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: 85460ea37d251b53074de9d832610d253700f2c1
ms.contentlocale: zh-tw
ms.lasthandoff: 06/24/2017


---
# Azure 資訊安全中心中的合作夥伴整合
<a id="partner-integration-in-azure-security-center" class="xliff"></a>
此文件說明 Azure 資訊安全中心如何與合作夥伴整合，以提高整體安全性並在 Azure 中提供整合體驗，同時充分利用 Azure Marketplace 進行合作夥伴認證和計費。

>[!NOTE] 
>從 2017 年 6 月初開始，資訊安全中心會使用 Microsoft Monitoring Agent 來收集和儲存資料。 若要深入了解，請參閱 [Azure 資訊安全中心平台移轉](security-center-platform-migration.md)。 本文中的資訊說明轉換至 Microsoft Monitoring Agent 後的資訊安全中心功能。
>

## 為什麼要從資訊安全中心部署合作夥伴的解決方案？
<a id="why-deploy-partners-solutions-from-security-center" class="xliff"></a>

運用資訊安全中心中的合作夥伴整合有四個主要原因：

- **部署方式簡單**：依照資訊安全中心的建議來部署合作夥伴解決方案更加容易。 使用預設組態和網路拓撲，部署程序可以完全自動化，或者客戶可以選擇半自動化選項，以允許更大的組態彈性與自訂。
- **整合偵測**：來自合作夥伴解決方案的安全性事件會自動收集、彙總以及顯示為資訊安全中心警示和事件的一部分。 這些事件也會與來自其他來源的偵測整合，以提供進階的威脅偵測功能。
- **統一的健全狀況監視與管理**：整合式的健全狀況事件，可讓客戶一眼就能監視所有合作夥伴解決方案。 提供基本管理功能，而且可以讓您輕鬆使用合作夥伴解決方案存取進階組態。
- **匯出至 SIEM**：客戶現在可以使用 Microsoft Azure Log Integration (預覽)，以 CEF 格式將所有資訊安全中心與合作夥伴的警示匯出至內部部署 SIEM 系統中


## 哪些合作夥伴會與資訊安全中心整合？
<a id="what-partners-are-integrated-with-security-center" class="xliff"></a>
資訊安全中心目前與下列解決方案整合︰

- Endpoint Protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)、Symantec、[Microsoft Antimalware for Azure](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Web 應用程式防火牆 ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall)、[F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html)、[Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF)、[Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets)、[App Gateway WAF](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- 新一代防火牆 ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/)、[Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/)、[Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) 和 [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- 弱點評估 ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

資訊安全中心會隨時間擴展這些現有類別內合作夥伴的數目，並加入新的類別。 

## 如何部署合作夥伴解決方案？
<a id="how-to-deploy-a-partner-solution" class="xliff"></a>

根據您的 Azure 環境組態和您定義的安全性原則，資訊安全中心可能會建議部署協力廠商解決方案。 此建議將引導您完成選取和安裝協力廠商解決方案的程序。 目前的整體部署體驗可能會根據解決方案類型和合作夥伴而有所不同。 如需詳細資訊，請參閱底下的連結：

- [新增 Web 應用程式防火牆](security-center-add-web-application-firewall.md)
- [新增新一代防火牆](security-center-add-next-generation-firewall.md)
- [安裝端點保護](security-center-install-endpoint-protection.md)
- [未安裝弱點評估](security-center-vulnerability-assessment-recommendations.md)

## 如何管理合作夥伴解決方案？
<a id="how-to-manage-partner-solutions" class="xliff"></a>

部署合作夥伴解決方案後，您可以檢視解決方案的健康狀態相關資訊，並從主要資訊安全中心儀表板的 [協力廠商解決方案] 圖格執行基本管理工作。 如需管理資訊安全中心中合作夥伴解決方案的相關資訊，請閱讀[透過 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)。

![合作夥伴整合](./media/security-center-partner-integration/security-center-partner-integration-fig1-1-newUI.png)

> [!NOTE]
> Symantec Endpoint Protection 支援僅限於探索，但無法使用任何健康情況警示。
>

## 另請參閱
<a id="see-also" class="xliff"></a>
在此文件中，您已了解如何在 Azure 資訊安全中心中整合合作夥伴解決方案。 若要深入了解資訊安全中心，請參閱下列主題：

* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)
* [管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)
* [Azure 資訊安全中心不同類型的安全性警示](security-center-alerts-type.md)
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) — 了解如何監視 Azure 資源的健全狀況。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) — 了解如何監視合作夥伴解決方案的健全狀況。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) — 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) — 尋找有關 Azure 安全性與相容性的部落格文章。

