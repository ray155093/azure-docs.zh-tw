---
title: "Azure Multi-Factor Authentication 常見問題集 | Microsoft Docs"
description: "提供與 Azure Multi-Factor Authentication 相關的常見問題與答案清單。 Multi-Factor Authentication 是一種驗證使用者身分識別的方法。它除了需要使用者名稱與密碼之外，還需要其他驗證方式。 它可以為使用者登入和交易提供一層額外的安全性。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9617cd00ed4a5f8f867542238e5008a9a17663c9
ms.openlocfilehash: 1e01bdc099af865e01eb2784cf367e482300ade8


---
# <a name="azure-multi-factor-authentication-faq"></a>Azure Multi-Factor Authentication 常見問題集
此常見問題集回答關於 Azure Multi-Factor Authentication 和使用 Multi-Factor Authentication 服務的常見問題，包括計費模式和可用性相關問題。

## <a name="general"></a>一般
**問：Azure Multi-Factor Authentication Server 如何處理使用者資料？**

使用 Multi-Factor Authentication Server，使用者資料只會儲存在內部部署伺服器。 雲端中不會儲存任何持續性的使用者資料。 使用者執行雙步驟驗證時，Multi-Factor Authentication Server 會將資料傳送到 Azure Multi-Factor Authentication 雲端服務以供驗證。 Multi-Factor Authentication Server 與 Multi-Factor Authentication 雲端服務之間的通訊會透過傳出連接埠 443，使用安全通訊端層 (SSL) 或傳輸層安全性 (TLS) 通訊。

驗證要求傳送至雲端服務時，會收集資料以用於驗證和使用方式報告。 雙步驟驗證記錄檔中包含的資料欄位如下：

* **唯一識別碼** (使用者名稱或內部部署 Multi-Factor Authentication Server 識別碼兩者之一)
* **名字和姓氏** (選擇性)
* **電子郵件地址** (選擇性)
* **電話號碼** (進行語音通話或簡訊驗證時)
* **裝置權杖** (執行行動應用程式驗證時)
* **驗證模式**
* **驗證結果**
* **Multi-Factor Authentication Server 名稱**
* **Multi-Factor Authentication Server IP**
* **用戶端 IP** (如果有的話)

您可以在 Multi-Factor Authentication Server 中設定選擇性欄位。

驗證結果 (成功或拒絕) 和拒絕原因會與驗證資料一起儲存，可在驗證和使用方式報告中取得。

## <a name="billing"></a>計費
回答大部分計費問題的方式是參照 [Multi-Factor Authentication 定價頁面](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

**問：透過通話或簡訊驗證我的使用者時，我的組織是否需要付費？**

組織不需為透過 Azure Multi-Factor Authentication 對使用者撥打個別通話或傳送簡訊支付費用。 根據個人電話服務，電話擁有者可能需要支付所接到電話或簡訊的費用。

**問：「每位使用者」計費模式的收費是根據設定使用 Multi-Factor Authentication 的使用者數目或執行驗證的使用者數目？**

計費是根據設定使用 Multi-Factor Authentication 的使用者數目來計費。

**問：Multi-Factor Authentication 是如何計費？**

當您使用「每位使用者」或「每次驗證」模型時，Azure MFA 就是消費型資源。 所有費用都是透過組織的 Azure 訂用帳戶支付，例如虛擬機器、網站等。

當您使用授權模型時，需要購買 Azure Multi-Factor Authentication 授權，然後指派給使用者，就像 Office 365 和其他訂用帳戶產品一樣。

若要深入了解您的選項，請參閱[Azure Multi-Factor Authentication 的作用](multi-factor-authentication-how-it-works.md#how-to-get-azure-multi-factor-authentication)

**問：是否有免費版本的 Azure Multi-Factor Authentication 供系統管理員使用？**

在某些情況下，是的。 適用於 Azure 系統管理員的 Multi-Factor Authentication 免費提供一小部分的 Azure MFA 功能。 這項優惠適用於未連結到消費型 Azure Multi-Factor Authentication 提供者的 Azure Active Directory 執行個體中的 Azure 全域管理員群組成員。 使用 Multi-Factor Authentication 提供者時，會將目錄中已設定使用 Multi-Factor Authentication 的所有系統管理員和使用者升級為完整版本的 Azure Multi-Factor Authentication。

**問：是否有免費版本的 Azure Multi-Factor Authentication 供 Office 365 使用者使用？**

在某些情況下，是的。 適用於 Office 365 的 Multi-Factor Authentication 免費提供一小部分的 Azure MFA 功能。 如果消費型 Azure Multi-Factor Authentication 提供者尚未連結至對應的 Azure Active Directory 執行個體，則這項優惠適用於已獲指派 Office 365 授權的使用者。 使用 Multi-Factor Authentication 提供者時，會將目錄中已設定使用 Multi-Factor Authentication 的所有系統管理員和使用者升級為完整版本的 Azure Multi-Factor Authentication。

**問：我的組織是否能隨時在「每位使用者」與「每次驗證」耗用量計費模式之間切換？**

您的組織會在建立資源時選擇計費模式。 資源佈建之後就無法變更計費模式。 不過，您可以建立另一個 Multi-Factor Authentication 資源來取代原始資源。 使用者設定和組態選項無法轉移到新的資源。

**問：我的組織是否能隨時在「耗用量計費」和「授權」模式之間切換？**

對已有每位使用者 Azure Multi-Factor Authentication 提供者的目錄新增授權時，消費型計費也會減少所擁有的授權數量。 如果所有設定使用 Multi-Factor Authentication 的使用者都已獲指派授權，系統管理員就可以刪除 Azure Multi-Factor Authentication 提供者。

您不能混用「每次驗證耗用量計費」與「授權」模型。 當每次驗證 Multi-Factor Authentication 提供者連結至目錄時，不論組織是否擁有任何授權，都會對其收取所有 Multi-Factor Authentication 驗證要求的費用。

**問：我的組織是否必須使用和同步處理身分識別才能使用 Azure Multi-Factor Authentication？**

當組織使用消費型計費模式時，不需要 Azure Active Directory。 您可以自行選擇是否要將 Multi-Factor Authentication Provider 連結至目錄。 如果您的組織未連結至目錄，則可以將 Multi-Factor Authentication Server 或 Multi-Factor Authentication SDK 部署在內部。

授權模式需要 Azure Active Directory，因為當您購買授權並指派給目錄中的使用者時，會將授權加入目錄。

## <a name="usability"></a>可用性
**問：如果使用者沒有接到電話，或使用者無法取得該電話怎麼辦？**

如果使用者已設定備用電話，則應該再試一次，並在登入頁面出現提示時選取該電話。 如果使用者未設定其他方法，則組織的系統管理員可以更新指派給使用者主要電話的號碼。

**問：如果使用者因無法再存取某個帳戶而連絡系統管理員，系統管理員要如何處理？**

系統管理員可以要求使用者再次進行註冊程序，以重設使用者的帳戶。 深入了解 [在雲端使用 Azure Multi-Factor Authentication 管理使用者和裝置設定](multi-factor-authentication-manage-users-and-devices.md)。

**問：如果使用者使用應用程式密碼的手機遺失或遭竊，系統管理員要如何處理？**

系統管理員可以刪除該使用者的所有應用程式密碼，以防止未經授權的存取。 當使用者有替代的裝置之後，即可建立密碼。 深入了解 [在雲端使用 Azure Multi-Factor Authentication 管理使用者和裝置設定](multi-factor-authentication-manage-users-and-devices.md)。

**問：如果使用者無法登入非瀏覽器的應用程式該怎麼辦？**

已設定使用 Multi-Factor Authentication 的使用者，需要應用程式密碼才能登入一些非瀏覽器的應用程式。 使用者需要清除 (刪除) 登入資訊，重新啟動該應用程式，然後使用他們的使用者名稱和應用程式密碼來登入。

取得有關建立應用程式密碼的詳細資訊，及其他 [應用程式密碼說明](multi-factor-authentication-end-user-app-passwords.md)。

> [!NOTE]
> 適用於 Office 2013 用戶端的新式驗證
> 
> Office 2013 用戶端 (包括 Outlook) 支援新式驗證通訊協定。 您可以將 Office 2013 設定成支援 Multi-Factor Authentication。 在您設定好 Office 2013 之後，Office 2013 用戶端就不需要應用程式密碼了。 如需詳細資訊，請參閱 [發表 Office 2013 新式驗證公開預覽](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。
> 
> 

**問：如果使用者未收到簡訊，或使用者回覆雙向簡訊但驗證逾時時，使用者該怎麼辦？**

因為有些無法控制的因素可能會影響服務可靠性，所以不保證會傳遞簡訊，以及在雙向簡訊中收到回覆。 這些因素包含目的地國家/地區、行動電信業者和訊號強度。

遭遇簡訊接收困難的使用者，應該改為選取行動應用程式或通話方法。 行動應用程式可以透過行動數據和 Wi-Fi 連接接收通知。 此外，即使裝置收不到訊號，行動應用程式可以產生驗證碼。 Microsoft 驗證器應用程式適用於 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。

如果您必須使用簡訊，則建議盡可能使用單向簡訊，而不要使用雙向簡訊。 單向簡訊比較可靠，並可防止使用者因為回覆從其他國家/地區傳送的簡訊而產生全域簡訊費用。

**問：我是否可以搭配 Azure Multi-Factor Authentication Server 使用硬體權杖？**

如果您正在使用 Azure Multi-Factor Authentication Server，則可以匯入協力廠商的 Open Authentication (OATH) 一次性限時密碼 (TOTP) 權杖，然後將它們用於雙步驟驗證。

如果您將秘密金鑰檔案放在 CSV 檔案中，並匯入至 Azure Multi-Factor Authentication Server，則可以使用屬於 OATH TOTP 權杖的 ActiveIdentity 權杖。 您可以在下列情況下使用 OATH 權杖：搭配使用 Active Directory Federation Services (ADFS)、在用戶端系統可處理存取挑戰回應時搭配使用遠端驗證撥號使用者服務 (RADIUS)，以及搭配使用 Internet Information Server (IIS) 表單型驗證。

您可以匯入下列格式的協力廠商 OATH TOTP Token︰  

* 可攜式對稱金鑰容器 (PSKC)  
* 如果檔案包含序號、Base 32 格式的秘密金鑰和時間間隔，則為 CSV  

**問：我是否可以使用 Azure Multi-Factor Authentication Server 保護「終端機服務」的安全？**

可以，但如果您使用的是 Windows Server 2012 R2 或更新版本，則只能使用遠端桌面閘道 (RD 閘道)。

Windows Server 2012 R2 中的安全性變更已改變 Azure Multi-Factor Authentication Server 在 Windows Server 2012 及舊版本中，對本機安全性授權 (LSA) 安全性封裝的連接方式。 對於 Windows Server 2012 或較舊版本中的終端機服務版本，您可以[使用 Windows 驗證來保護應用程式](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)。 如果您是使用 Windows Server 2012 R2，則需要 RD 閘道。

**問：為什麼設定來電顯示之後，使用者會收到來自匿名來電者的 Multi-Factor Authentication 來電？**

當透過公共電話網路發出 Multi-Factor Authentication 通話時，有時會透過不支援來電顯示的電信業者路由傳送。 因此，即使 Multi-Factor Authentication 系統一律會傳送來電顯示號碼，但並不保證一定會顯示來電號碼。

## <a name="errors"></a>錯誤數
**問：如果使用者在使用行動應用程式通知時看到「驗證要求不適用於已啟用的帳戶」錯誤訊息，應該怎麼辦？**

請告訴他們遵循這個從行動應用程式移除其帳戶的程序，然後重新新增帳戶︰

1. 移至 [您的 Azure 入口網站設定檔](https://account.activedirectory.windowsazure.com/profile/) ，並使用您的組織帳戶登入。
2. 選取 [其他安全性驗證]。
3. 將現有帳戶從行動應用程式移除。
4. 按一下 [設定] ，然後依照指示來重新設定行動應用程式。

**問：如果使用者在登入非瀏覽器應用程式時看到 0x800434D4L 錯誤訊息，應該怎麼辦？**

目前使用者只能在他可以透過瀏覽器存取的應用程式和服務上，才能使用其他安全性驗證。 本機電腦上安裝的非瀏覽器應用程式 (也稱為「豐富型用戶端應用程式」，例如 Windows PowerShell) 不適用於需要其他安全性驗證的帳戶。 在此情況下，使用者可能會看到應用程式產生 0x800434D4L 錯誤。

此種情況的因應措施是使用不同的使用者帳戶，進行系統管理和非系統管理作業。 您稍後可以連結系統管理帳戶與非系統管理帳戶之間的信箱，以便使用非系統管理帳戶登入 Outlook。 如需詳細資訊，請了解如何 [讓系統管理員能夠開啟及檢視使用者信箱的內容](http://help.outlook.com/141/gg709759.aspx?sl=1)。

## <a name="next-steps"></a>後續步驟
如果這裡沒有您問題的解答，請將它留在頁面底部的註解中。 或者，以下是取得協助的一些其他選項︰

* 在 [Microsoft 支援知識庫](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport)搜尋常見技術問題的解決方案。
* 在 [Azure Active Directory 論壇](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)中，搜尋及瀏覽來自社群的技術問題與解答，或是提出您的問題。
* 如果您是舊版 PhoneFactor 的客戶且有問題或需要協助重設密碼，請使用 [重設密碼](mailto:phonefactorsupport@microsoft.com) 連結來建立支援案例。
* 請透過 [Azure Multi-Factor Authentication Server (PhoneFactor) 支援](https://support.microsoft.com/oas/default.aspx?prid=14947)連絡支援專業人員。 連絡我們時，請盡量包含有關問題的最多資訊，這樣會十分有幫助。 您可以提供的資訊包含您看到錯誤的頁面、特定錯誤碼、特定工作階段識別碼，以及看到錯誤的使用者的識別碼。




<!--HONumber=Jan17_HO1-->


