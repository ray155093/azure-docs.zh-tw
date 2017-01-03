---
title: "使用 Azure AD 應用程式 Proxy 連接器 | Microsoft Docs"
description: "涵蓋如何建立和管理「Azure AD 應用程式 Proxy」中的連接器群組。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 5404372d-3092-4054-aeee-26afb1399f33
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ba87b73be45cb0893f418453bc0efb3293772c95


---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups---public-preview"></a>使用連接器群組在個別的網路和位置上發佈應用程式 - 公開預覽版
> [!div class="op_single_selector"]
> * [Azure 入口網站](active-directory-application-proxy-connectors-azure-portal.md)
> * [Azure 傳統入口網站](active-directory-application-proxy-connectors.md)
> 
> 

連接器群組可用於多種不同狀況，包括：

* 具有多個互連資料中心的網站。 在此情況下，您會希望儘量將流量保留在資料中心內，因為跨資料中心的連結既昂貴、速度又慢。 您可以在每個資料中心都部署連接器，來只為資料中心內的應用程式提供服務。 這種方法可將跨資料中心的連結減到最少，讓使用者體驗完全的流暢性。
* 管理安裝在隔離網路 (不是主要的公司網路的一部分) 上的應用程式。 您可以使用連接器群組將專用連接器安裝在隔離網路上，以同時將應用程式與網路隔離。
* 對於安裝在 IaaS 上以供雲端存取的應用程式，連接器群組提供通用的服務來保護對所有應用程式的存取。 連接器群組不會對公司網路產生額外的相依性，或是造成不完整的應用程式體驗。 連接器可安裝在每個雲端資料中心，而且只為此網路中的應用程式提供服務。 您可以安裝數個連接器以獲得高可用性。
* 支援多樹系環境，其中可以依樹系部署特定連接器，並設定為針對特定應用程式提供服務。
* 連接器群組可用於災害復原網站，以偵測容錯移轉或做為主要網站的備份。
* 連接器群組也可用來為單一租用戶的多家公司提供服務。

## <a name="prerequisite-create-your-connectors"></a>必要條件︰建立連接器
若要將連接器組成群組，您必須確定 [已安裝多個連接器](active-directory-application-proxy-enable.md)。 當您安裝新的連接器時，它會自動加入「預設」  連接器群組。

## <a name="step-1-create-connector-groups"></a>步驟 1：建立連接器群組
您可以建立任意數量的連接器群組。 連接器群組的建立是在 [Azure 入口網站](https://portal.azure.com)中完成。

1. 選取 **Azure Active Directory** 以移至您目錄的管理儀表板。 從該處選取 [企業應用程式] > [應用程式 Proxy]。
2. 選取 [連接器群組]  按鈕。 [新增連接器群組] 刀鋒視窗隨即出現。
3. 指定新連接器群組的名稱，然後使用下拉式功能表來選取哪些連接器屬於此群組。
4. 完成連接器群組時，選取 [儲存]  。

## <a name="step-2-assign-applications-to-your-connector-groups"></a>步驟 2：將應用程式指派給您的連接器群組
最後一個步驟是將每個應用程式設定至將為其提供服務的連接器群組。

1. 從您目錄的管理儀表板中，選取 [企業應用程式] > [所有應用程式] > 您想要指派給連接器群組的應用程式 > [應用程式 Proxy]。
2. 在 [連接器群組] 底下，使用下拉式功能表來選取應用程式所要使用的群組。
3. 按一下 [儲存]  以套用變更。

## <a name="see-also"></a>另請參閱
* [啟用應用程式 Proxy](active-directory-application-proxy-enable.md)
* [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
* [啟用條件式存取](active-directory-application-proxy-conditional-access.md)
* [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)

如需最新消息，請查閱 [應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Dec16_HO5-->


