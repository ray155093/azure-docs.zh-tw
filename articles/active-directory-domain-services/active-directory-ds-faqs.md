---
title: "常見問題集 - Azure Active Directory Domain Services | Microsoft Docs"
description: "關於「Azure Active Directory 網域服務」的常見問題集"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 5e6bab265b2b6eabd1a878492588c4eb39d1b332
ms.openlocfilehash: 89dfabb8feafffee2ed8143c372b53d02033d582
ms.lasthandoff: 01/14/2017


---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services：常見問題集 (FAQ)
此頁面會回答有關 Azure Active Directory Domain Services 的常見問題。 請隨時回來查看最新消息。

### <a name="troubleshooting-guide"></a>疑難排解指南
如需設定或管理「Azure AD 網域服務」時會發生之常見問題的解決方式，請參閱 [疑難排解指南](active-directory-ds-troubleshooting.md) 。

### <a name="configuration"></a>組態
#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>我可以針對單一 Azure AD 目錄建立多個網域嗎？
否。 您只能針對單一 Azure AD 目錄，建立由 Azure AD 網域服務所服務的單一網域服務 。  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>是否可以啟用 Azure Resource Manager 虛擬網路中的 Azure AD 網域服務？
否。 Azure AD 網域服務只可以在傳統 Azure 虛擬網路中啟用。 您可以使用虛擬網路對等互連，將傳統虛擬網路連接到 Resource Manager 虛擬網路，以在 Resource Manager 虛擬網路中使用受管理的網域。

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>我可以在訂用帳戶內的多個虛擬網路中使用 Azure AD 網域服務嗎？
服務本身並不直接支援這種情況。 Azure AD 網域服務一次只能在一個虛擬網路上使用。 不過，您可以在多個虛擬網路之間設定連線，以將 Azure AD 網域服務公開至其他虛擬網路。 此文章說明如何 [在 Azure 中連接虛擬網路](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>我是否可以使用 PowerShell 來啟用「Azure AD 網域服務」？
目前不提供「Azure AD 網域服務」的 PowerShell/自動化部署。

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>「Azure AD 網域服務」是否可以在新的 Azure 入口網站中使用？
否。 「Azure AD 網域服務」只能在 [Azure 傳統入口網站](https://manage.windowsazure.com)設定。 我們希望未來擴充支援 [Azure 入口網站](https://portal.azure.com) 。

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>可以將網域控制站新增至 Azure AD 網域服務管理的網域嗎？
否。 Azure AD 網域服務所提供的網域是受管理的網域。 您不需要佈建、設定或管理此網域的網域控制站 - Microsoft 會以服務形式提供這些管理活動。 因此，您無法為受管理的網域新增其他網域控制站 (讀寫或唯讀)。

### <a name="administration-and-operations"></a>管理和作業
#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>我可以使用遠端桌面連線到我的受管理網域的網域控制站嗎？
否。 您沒有權限透過遠端桌面連接至受管理網域上的網域控制站。 「AAD DC 系統管理員」群組的成員可以使用 AD 系統管理工具，例如 Active Directory 管理中心 (ADAC) 或 AD PowerShell 來管理受管理網域。 這些工具會在加入受管理網域的 Windows 伺服器上，使用「遠端伺服器管理工具」功能安裝。

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>我已經啟用 Azure AD 網域服務。 我應該使用哪一個使用者帳戶來將電腦加入此網域？
您已新增至系統管理群組 (例如「AAD DC 系統管理員」) 的使用者都能夠將電腦新增網域。 此外，此群組中的使用者會被授與已加入網域之電腦的遠端桌面存取權限。

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>我可以針對 Azure AD 網域服務所提供的網域取得網域系統管理員權限嗎？
否。 您不會取得受管理網域的系統管理權限。 您無法在網域內使用「網域系統管理員」和「企業系統管理員」權限。 Azure AD 目錄內現有的網域系統管理員或企業系統管理員群組也不會被授與該網域的網域/企業系統管理員權限。

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>我可以在 Azure AD 網域服務所提供的網域上，使用 LDAP 或其他 AD 系統管理工具來修改群組成員資格嗎？
否。 您無法在 Azure AD 網域服務所服務的網域上修改群組成員資格。 這同樣適用於使用者屬性。 但是，您可能會在 Azure AD 中或內部部署網域上變更群組成員資格或使用者屬性。 這類變更會自動同步處理到 Azure AD 網域服務。

#### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>我對 Azure AD 目錄所做的變更要多久才會反映在我的受管理網域中？
使用 Azure AD UI 或 PowerShell 在您 Azure AD 目錄中進行的變更會同步至您的受管理網域。 這個同步處理程序會在背景執行。 在您目錄的單次初始同步處理完成之後，通常需要 20 分鐘的時間，在 Azure AD 中所做的變更才會反映在您的受管理網域中。

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>可以擴充 Azure AD 網域服務所提供之網域的結構描述嗎？
否。 結構描述是由 Microsoft 針對受管理網域進行管理。 Azure AD 網域服務不支援結構描述延伸模組。

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>是否可以在受管理的網域中修改或新增 DNS 記錄？
是。 屬於「AAD DC 系統管理員」群組的使用者會被授與「DNS 系統管理」權限，以在受管理網域中修改 DNS 記錄。 這些使用者可以在加入受管理網域且執行 Windows Server 的電腦上，使用 DNS 管理員主控台以管理 DNS。 若要使用 DNS 管理員主控台，請安裝 DNS 伺服器工具，這是伺服器上的「遠端伺服器管理工具」選擇性功能的一部分。 如需 [管理、監視及疑難排解 DNS 的公用程式](https://technet.microsoft.com/library/cc753579.aspx) 詳細資訊，可在 TechNet 上取得。

### <a name="billing-and-availability"></a>計費與可用性
#### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD 網域服務是付費服務嗎？
是。 如需詳細資訊，請參閱 [價格頁面](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

#### <a name="is-there-a-free-trial-for-the-service"></a>是否可以免費試用服務？
此服務隨附於 Azure 的免費試用版。 您可以註冊以 [免費試用 Azure 一個月](https://azure.microsoft.com/pricing/free-trial/)。

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>我可以從 Enterprise Mobility Suite (EMS) 中取得 Azure AD 網域服務嗎？ 我是否需要 Azure AD Premium 才能使用 Azure AD 網域服務？
否。 Azure AD 網域服務是隨用隨付的 Azure 服務，並不是 EMS 的一部分。 Azure AD Domain Services 可以與所有 Azure AD 版本 (免費、基本及進階) 搭配使用。 計費方式是每小時依據使用量計費。

#### <a name="what-azure-regions-is-the-service-available-in"></a>哪些 Azure 區域提供此服務？
請參閱[依區域提供的 Azure 服務](https://azure.microsoft.com/regions/#services/)頁面，以查看可使用 Azure AD 網域服務的 Azure 區域清單。

