---
title: "使用應用程式 Proxy 進行單一登入 | Microsoft Docs"
description: "涵蓋如何使用 Azure AD 應用程式 Proxy 提供單一登入。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: ded0d9c9-45f6-47d7-bd0f-3f7fd99ab621
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 74c932301fdeb0956933dd23a331efa3d36a24a1


---
# <a name="single-sign-on-with-application-proxy"></a>使用應用程式 Proxy 進行單一登入
單一登入是 Azure AD 應用程式 Proxy 的重要元素。 它經由下列步驟提供最佳的使用者體驗︰

1. 使用者登入雲端  
2. 所有安全性驗證都在雲端完成 (預先驗證)  
3. 當要求傳送至內部部署應用程式時，應用程式 Proxy 連接器會模擬使用者。 後端應用程式會將此視為來自已加入網域之裝置的一般使用者。

![存取的圖表，從使用者經過應用程式 Proxy 到公司網路](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Azure AD 應用程式 Proxy 可協助您為使用者提供單一登入 (SSO) 體驗。 使用下列指示來發佈您使用 SSO 的應用程式：

## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>使用 KCD 搭配應用程式 Proxy 的內部部署 IWA 應用程式 SSO
您可以在 Active Directory 中提供應用程式 Proxy 連接器權限來模擬使用者並代表其傳送和接收權杖，以使用「整合式 Windows 驗證」(IWA) 啟用應用程式的單一登入。

### <a name="network-diagram"></a>網路圖表
此圖表說明使用者嘗試存取採用 IWA 之內部部署應用程式時的流程。

![Microsoft AAD 驗證流程圖](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. 使用者輸入 URL，以透過「應用程式 Proxy」存取內部部署應用程式。
2. 「應用程式 Proxy」將要求重新導向至 Azure AD 驗證服務，以進行預先驗證。 此時，Azure AD 會套用任何適用的驗證和授權原則，例如多重要素驗證。 若使用者通過驗證，Azure AD 會建立權杖並將它傳送給使用者。
3. 使用者將權杖傳遞給「應用程式 Proxy」。
4. 「應用程式 Proxy」會驗證權杖並從中擷取「使用者主體名稱」(UPN)，然後再透過雙重驗證安全通道，將要求、UPN 和「服務主體名稱」(SPN) 傳送至「連接器」。
5. 「連接器」會與內部部署 AD 執行「Kerberos 限制委派」(KCD) 交涉，以模擬使用者將 Kerberos 權杖傳送至應用程式。
6. Active Directory 會將應用程式的 Kerberos 權杖傳送至「連接器」。
7. 「連接器」會使用從 AD 接收的 Kerberos 權杖，將原始要求傳送至應用程式伺服器。
8. 應用程式會傳送回應至「連接器」，然後再傳回至「應用程式 Proxy」服務，最後再傳回給使用者。

### <a name="prerequisites"></a>必要條件
搭配應用程式 Proxy 開始使用 SSO 之前，請確定您的環境已完成下列設定和組態︰

* 您的應用程式 (例如 SharePoint Web 應用程式) 已設為使用「整合式 Windows 驗證」。 如需詳細資訊，請參閱[啟用支援 Kerberos 驗證](https://technet.microsoft.com/library/dd759186.aspx)，或者若是使用 SharePoint，請參閱[為 SharePoint 2013 中的 Kerberos 驗證做規劃](https://technet.microsoft.com/library/ee806870.aspx)。
* 您的所有應用程式都有「服務主體名稱」。
* 執行「連接器」的伺服器與執行應用程式的伺服器，皆已加入網域且屬於相同網域或信任網域。 如需有關加入網域的詳細資訊，請參閱 [將電腦加入網域](https://technet.microsoft.com/library/dd807102.aspx)。
* 執行「連接器」的伺服器有權限讀取使用者的 TokenGroupsGlobalAndUniversal。 這是預設設定，可能受環境強化安全性所影響。 請參閱 [KB2009157](https://support.microsoft.com/en-us/kb/2009157) 以取得更多相關說明。

### <a name="active-directory-configuration"></a>Active Directory 組態
根據您的「應用程式 Proxy 連接器」和已發佈的伺服器是否位於相同網域，Active Directory 組態會有所不同。

#### <a name="connector-and-published-server-in-the-same-domain"></a>連接器和已發佈的伺服器位於相同網域
1. 在 Active Directory 中，移至 [工具] > [使用者和電腦]。
2. 選取執行「連接器」的伺服器。
3. 按一下滑鼠右鍵，然後選取 [屬性] > [委派]。
4. 選取 [信任這台電腦，但只委派指定的服務]，在 [這個帳戶可以呈送委派認證的服務] 下方，新增應用程式伺服器的 SPN 身分識別值。
5. 這可讓「應用程式 Proxy 連接器」針對清單中所定義的應用程式，在 AD 中模擬使用者。

![[連接器 SVR 屬性] 視窗螢幕擷取畫面](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>連接器和已發佈的伺服器位於不同網域
1. 如需跨網域使用 KCD 的先決條件清單，請參閱 [跨網域的 Kerberos 限制委派](https://technet.microsoft.com/library/hh831477.aspx)。
2. 在 Windows 2012 R2 中，使用「連接器」伺服器的 `principalsallowedtodelegateto` 屬性讓「應用程式 Proxy」委派「連接器」伺服器，其中已發佈的伺服器為 `sharepointserviceaccount`，委派伺服器為 `connectormachineaccount`。
   
        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com
   
        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector
   
        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

> [!NOTE]
> `sharepointserviceaccount` 可以是 SPS 電腦帳戶，或是用來執行 SPS 應用程式集區的服務帳戶。
> 
> 

### <a name="azure-classic-portal-configuration"></a>Azure 傳統入口網站設定
1. 根據 [使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)中的所述指示來發佈您的應用程式。 請務必選取 [Azure Active Directory] 作為 [預先驗證方法]。
2. 應用程式出現於應用程式清單後，將其選取並按一下 [設定] 。
3. 在 [屬性] 下方，將 [內部驗證方法] 設定為 [整合式 Windows 驗證]。  
   ![進階應用程式組態](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. 輸入應用程式伺服器的 [內部應用程式 SPN]  。 在此範例中，已發佈應用程式的 SPN 為 http/lob.contoso.com。  

> [!IMPORTANT]
> 如果您的內部部署 UPN 和 Azure Active Directory 中的 UPN 不相同，您必須設定 [委派的登入身分識別](#delegated-login-identity) ，才能讓預先驗證運作。
> 
> 

|  |  |
| --- | --- |
| 內部驗證方法 |如果您使用 Azure AD 進行預先驗證，您可以設定內部驗證方法，讓使用者從單一登入 (SSO) 到此應用程式受益。 <br><br> 如果您的應用程式使用 IWA，請選取 [整合式 Windows 驗證] (IWA)，然後您可以設定「Kerberos 限制委派」(KCD) 以針對此應用程式啟用 SSO。 使用 IWA 的應用程式必須設為使用 KCD，否則「應用程式 Proxy」將無法發佈這些應用程式。 <br><br> 如果您的應用程式不是使用 IWA，請選取 [無]。 |
| 內部應用程式 SPN |這是在內部部署 Azure AD 中所設定的內部應用程式「服務主體名稱」(SPN)。 「應用程式 Proxy 連接器」會使用 SPN，以針對使用 KCD 的應用程式擷取 Kerberos 權杖。 |

## <a name="sso-for-non-windows-apps"></a>非 Windows 應用程式的 SSO
Azure AD 應用程式 Proxy 的 Kerberos 委派流程會在 Azure AD 在雲端驗證使用者開始。 一旦要求到達內部部署，Azure AD 應用程式 Proxy 連接器會利用與本機 Active Directory 互動，代表使用者發出 Kerberos 票證。 此程序稱為「Kerberos 限制委派 (KCD)」。 在下一個階段中，要求會傳送至具有此 Kerberos 票證的後端應用程式。 有許多定義如何傳送這類要求的通訊協定。 大部分的非 Windows 伺服器會預期 Azure AD 應用程式 Proxy 現在支援的交涉/SPNego。

![非 Windows SSO 的圖表](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### <a name="delegated-login-identity"></a>委派的登入身分識別
委派的登入身分識別可協助您處理兩種不同的登入案例︰

* 通常會以使用者名稱或 SAM 帳戶名稱形式 (而不是電子郵件地址 (username@domain)) 取得使用者身分識別的非 Windows 應用程式。
* 替代登入設定，其中 Azure AD 中的 UPN 和內部部署 Active Directory 中的 UPN 不同。

使用應用程式 Proxy，您就可以選取要用來取得 Kerberos 票證的身分識別。 這項設定會因應用程式而異。 其中的一些選項適合不接受電子郵件地址格式的系統，另外的選項則設計用於替代登入。

![[委派的登入身分識別] 參數螢幕擷取畫面](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

如果使用委派的登入身分識別，則組織中所有網域或樹系的這個值可能不是唯一。 您可以藉由使用兩個不同的連接器群組發佈這些應用程式兩次來避免此問題。 因為每個應用程式有不同的使用者對象，您可以將其「連接器」加入不同的網域。

## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>在內部部署和雲端身分識別不相同時使用 SSO
除非另行設定，否則應用程式 Proxy 會假設使用者在雲端與內部部署中具有完全相同的身分識別。 您可以針對每個應用程式設定執行單一登入時應該使用的身分識別。  

此功能可讓具有不同內部部署與雲端身分識別的許多組織，可從雲端單一登入到內部部署應用程式，而不需要使用者輸入不同的使用者名稱與密碼。 這包括下列組織：

* 在內部有多個網域 (joe@us.contoso.com,、joe@eu.contoso.com)，而在雲端有單一網域 (joe@contoso.com).
* 在內部有無法路由傳送的網域名稱 (joe@contoso.usa)，而在雲端有合法網域名稱。
* 請勿在內部使用網域名稱 (joe)
* 在內部部署和雲端中使用不同別名。 例如 joe-johns@contoso.com 對上 joej@contoso.com  

它也有助於不接受電子郵件地址形式位址的應用程式，這對於非 Windows 後端伺服器是很常見的案例。

### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>設定不同雲端和內部部署身分識別的 SSO
1. 設定 Azure AD Connect 設定，讓主要的身分識別會是電子郵件地址 (郵件)。 這是在自訂程序中完成 (透過變更同步設定中的 [使用者主體名稱]  欄位)。 請注意，這些設定也決定使用者如何登入 Office 365、Windows 10 裝置與其他使用 Azure AD 作為其身分識別存放區的應用程式。  
   ![識別使用者螢幕擷取畫面 - [使用者主體名稱] 下拉式清單](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. 在您想要修改之應用程式的應用程式組態設定中，選取要使用的 [委派的登入識別]  ：
   
   * 使用者主體名稱： joe@contoso.com  
   * 替代的使用者主體名稱： joed@contoso.local  
   * 使用者主體名稱的使用者名稱部分：joe  
   * 替代的使用者主體名稱的使用者名稱部分：joed  
   * 內部部署 SAM 帳戶名稱：視內部網域控制站設定而定
   
   ![[委派的登入身分識別] 下拉式功能表螢幕擷取畫面](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>疑難排解不同身分識別的 SSO
如果 SSO 程序中發生錯誤，它會顯示在「連接器」電腦事件記錄檔，如 [疑難排解](active-directory-application-proxy-troubleshoot.md)中所述。
但在某些情況下，要求將成功傳送至後端應用程式，同時此應用程式會以各種其他 HTTP 回應來回覆。 疑難排解這些情況應該要從檢查連接器電腦上應用程式 Proxy 工作階段事件記錄檔中的事件編號 24029 開始。 用於委派的使用者身分識別會出現在事件詳細資料的 [使用者] 欄位內。 若要開啟工作階段記錄，請選取事件檢視器檢視功能表中的 [顯示分析與偵錯記錄檔]  。

## <a name="see-also"></a>另請參閱
* [使用應用程式 Proxy 發行應用程式](active-directory-application-proxy-publish.md)
* [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)
* [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
* [啟用條件式存取](active-directory-application-proxy-conditional-access.md)

如需最新消息，請查閱 [應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg



<!--HONumber=Nov16_HO3-->


