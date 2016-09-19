<properties
	pageTitle="如何設定 Azure Active Directory 裝置型條件式存取原則來控制對 Azure Active Directory 連線應用程式的存取"
	description="說明 IT 系統管理員如何為 Azure AD 連線應用程式設定裝置型條件式存取原則。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="markvi"/>


# 如何設定 Azure Active Directory 裝置型條件式存取原則來控制對 Azure Active Directory 連線應用程式的存取 


Azure Active Directory 裝置型條件式存取是可供您保護組織資源免於遭受下列情況的能力︰

- 從不明/未受管理的裝置進行的存取
- 從不符合貴組織所定義安全性原則的裝置進行的存取。

如需有關條件式存取的概觀，請參閱 [Azure Active Directory 條件式存取](active-directory-conditional-access.md)。

您可以設定裝置型條件式存取原則來保護下列各項︰

- Office 365 SharePoint Online - 用以保護組織的網站和文件。

- Office 365 Exchange Online - 用以保護組織的電子郵件。

- 連線到 Azure AD 以進行驗證的 SaaS 應用程式。

- 透過「Azure AD 應用程式 Proxy」發佈的內部部署應用程式。


在 Azure 管理入口網站中，您可以移至目錄中的特定應用程式來設定此原則。

 
  ![應用程式](./media/active-directory-conditional-access-policy-connected-applications/01.png "應用程式")


選取應用程式之後，請按一下 [設定] 索引標籤以設定「條件式存取」原則。


  ![以裝置為準的存取規則](./media/active-directory-conditional-access-policy-connected-applications/02.png "以裝置為準的存取規則")


 

若要啟用裝置型條件式存取原則，請在 [以裝置為準的存取規則] 區段中，針對 [啟用存取規則]，選取 [開啟]。

此原則是由三個元件所組成︰

1. **套用對象** - 存取應用程式時，要套用此原則的使用者範圍。

2. **裝置規則** - 裝置必須符合才能存取應用程式的必要條件。

3. **應用程式強制** - 評估原則時應針對的用戶端應用程式 (原生對上瀏覽器)。

  ![以裝置為準的存取規則](./media/active-directory-conditional-access-policy-connected-applications/03.png "以裝置為準的存取規則")
 

## 選取要套用原則的使用者 

在 [套用對象] 區段中，您可以選取要套用此原則的使用者範圍。

您有兩個範圍選項：

- **所有使用者** - 針對存取應用程式的每個人

- **群組** - 可將範圍限制在身為群組成員的使用者。

![套用對象](./media/active-directory-conditional-access-policy-connected-applications/11.png "套用對象")


您可以藉由選取 [例外]，在存取應用程式時將使用者從此原則中排除。當您需要讓特定使用者能夠暫時存取應用程式時，這會相當有用。舉例來說，如果某些使用者有尚未針對條件式存取準備就緒 (尚未註冊、未符合規範等) 的裝置，請選取此選項。
 

## 選取裝置必須符合的條件 

您可以使用 [裝置規則] 來為裝置設定能夠存取應用程式的條件。

針對「裝置型條件式存取」，以下是支援的裝置：

- 「Windows 10 年度更新版」、Windows 7 及 Windows 8.1。

- Windows Server 2016、Windows Server 2012 R2、Windows Server 2012 及 Windows Server 2008 R2

- iOS 裝置 (iPad、iPhone)

- Android 裝置

即將推出對 Mac 的支援。

  ![裝置](./media/active-directory-conditional-access-policy-connected-applications/04.png "應用程式")



 >[AZURE.NOTE] 如需有關「加入網域」與「加入 Azure AD」之間差異的資訊，請參閱[您工作場所中使用 Windows 10 裝置](active-directory-azureadjoin-windows10-devices.md)。


您有兩個裝置規則選項：

- **所有裝置都必須相容** - 這會要求存取應用程式的所有裝置平台都必須符合規範。針對不支援「裝置型條件式存取」的平台，系統將會拒絕這些裝置的存取。

- **只有選取的裝置必須相容** - 這只會要求選取的裝置平台必須符合規範。非選取的平台或其他存取應用程式的平台則獲允許存取。

  ![應用程式](./media/active-directory-conditional-access-policy-connected-applications/05.png "應用程式")



已加入 Azure AD 的裝置如果在目錄中被 Microsoft Intune 或與 Azure AD 整合的第三方「行動裝置管理」(MDM) 系統標示為「相容」，即為相容裝置。

已加入網域的裝置如果符合下列兩種情況其中之一，即為相容裝置︰

- 如果它們已向 Azure AD 註冊，由於它們已加入網域，因此許多組織會將它們視為信任的裝置。

- 如果它們在 Azure AD 中被 System Center Configuration Manager 2016 標示為「相容」。

 ![裝置規則](./media/active-directory-conditional-access-policy-connected-applications/06.png "裝置規則")
 

Windows 個人裝置如果在目錄中被 Microsoft Intune 或與 Azure AD 整合的第三方「行動裝置管理」(MDM) 系統標示為「相容」，即為相容裝置。

非 Windows 裝置如果在目錄中被 Microsoft Intune 標示為「相容」，即為相容裝置。

 >[AZURE.NOTE] 如需有關如何依據裝置管理系統來設定 Azure AD 以符合裝置相容性的詳細資訊，請參閱 [Azure Active Directory 條件式存取](active-directory-conditional-access.md)。


當您選取特定裝置平台時，您可以選取一或多個選項，包括 Android、iOS、Windows Mobile (Windows 8.1 手機和平板電腦) 及 Windows (所有其他 Windows 裝置，包括所有 Windows 10 裝置)。使用此選項時，只會針對選取的平台進行原則評估。如果存取嘗試是來自不屬於所選範圍的裝置，則不會評估任何裝置原則，而只要使用者獲允許存取，該裝置即獲允許存取。

![裝置規則](./media/active-directory-conditional-access-policy-connected-applications/07.png "裝置規則")
  

## 選取將在其下評估原則的用戶端應用程式類型 

在 [應用程式強制] 區段中，您需設定評估原則時所必須針對的應用程式類型。


您有兩個應用程式選項：

- 適用於瀏覽器與原生應用程式
- 僅限原生應用程式


![應用程式](./media/active-directory-conditional-access-policy-connected-applications/08.png "應用程式")


選取 [適用於瀏覽器與原生應用程式] 會在下列項目存取應用程式時強制執行原則︰

- 瀏覽器 (例如 Windows 10 中的 Edge、iOS 中的 Safari 等)
- 在任何平台中使用 Active Directory 驗證程式庫 (ADAL) 或在 Windows 10 中使用 Web 帳戶管理員 (WAM) API 的應用程式

>[AZURE.NOTE] 如需有關瀏覽器支援及其他針對存取裝置型受 CA 保護應用程式之使用者的考量，請參閱 [Azure Active Directory 條件式存取](active-directory-conditional-access.md)。

 

## 保護來自 Exchange Active Sync 型應用程式的電子郵件存取 

在 Office 365 Exchange Online 應用程式中，您有一個名為 [Exchange ActiveSync] 的額外區段。此區段可讓您封鎖對 Exchange Active Sync 型郵件應用程式的電子郵件存取。

![應用程式](./media/active-directory-conditional-access-policy-connected-applications/09.png "應用程式")
 
![應用程式](./media/active-directory-conditional-access-policy-connected-applications/10.png "應用程式")

 
## 其他主題

- [Azure Active Directory 條件式存取](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0907_2016-->