<properties
	pageTitle="設定和資料漫遊常見問題集 | Microsoft Azure"
	description="回答 IT 系統管理員可能會遇到的設定和應用程式資料同步處理的一些問題。"
	services="active-directory"
    keywords="企業狀態漫遊設定, windows 雲端, 企業狀態漫遊常見問題集"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="femila"/>

# 設定和資料漫遊常見問題集

本主題將回答 IT 系統管理員可能會遇到的設定和應用程式資料同步處理的一些問題。

## 哪些資料會漫遊？
**Windows 設定**：內建至 Windows 作業系統的電腦設定。一般而言，這些是個人化使用者電腦的設定，且包含下列類別：

- **佈景主題**：桌面主題、工作列設定等。
- **Internet Explorer 設定**：最近開啟的索引標籤、我的最愛等
- **Edge 瀏覽器設定**：我的最愛、閱讀清單
- **密碼**：網際網路密碼、Wi-Fi 設定檔等
- **語言喜好設定**：鍵盤配置、系統語言、日期和時間等
- **輕鬆存取**：高對比佈景主題、朗讀程式、放大鏡等
- **其他 Windows 設定**：命令提示字元設定、應用程式清單等

**應用程式資料**：通用 Windows 應用程式可以將設定資料寫入「漫遊」資料夾，然後寫入這個資料夾中的任何資料會自動同步處理。由個別應用程式開發人員設計應用程式以充分利用這項功能。如需有關如何開發使用漫遊的通用 Windows 應用程式的詳細資訊，請參閱[應用程式資料儲存體 API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) 和 [Windows 8 appdata 漫遊開發人員部落格](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx)。

## 哪些帳戶用於設定同步處理？
在 Windows 8 和 Windows 8.1 中，設定同步處理一律使用取用者 Microsoft 帳戶。企業使用者有能力可以將 Microsoft 帳戶連接至 Active Directory 網域帳戶，以存取設定同步處理。在 Windows 10 中，此「連接的 Microsoft 帳戶」的功能是由主要/次要帳戶架構取代。

主要帳戶是定義為用來登入 Windows 的帳戶 – 可以是 Microsoft 帳戶、Azure Active Directory (Azure AD) 帳戶、內部部署 Active Directory 帳戶或本機帳戶。除了主要帳戶，Windows 10 使用者可以將一或多個次要雲端帳戶新增至他們的裝置。這些次要帳戶通常是 Microsoft 帳戶、Azure Active Directory 帳戶或其他例如 Gmail 或 Facebook 的帳戶。這些次要帳戶提供其他服務的存取權，例如單一登入和 Windows 市集，但是它們無法進行設定同步處理。

在 Windows 10 中，只有裝置的主要帳戶可用來進行設定同步處理 (請參閱[我該如何從 Windows 8 中的 Microsoft 帳戶設定同步處理升級至 Windows 10 中的 Azure AD 設定同步處理？](active-directory-windows-enterprise-state-roaming-faqs.md#How-do-I-upgrade-from-Microsoft-account-settings-sync-in-Windows-8-to-Azure-AD-settings-sync-in Windows-10?))。

裝置上不同使用者帳戶之間的資料永遠不會混合。有兩個設定可用來同步處理設定：
- Windows 設定一律會使用主要帳戶進行漫遊。
- 應用程式資料將會利用用來取得應用程式的帳戶來標記。只會同步處理以主要帳戶標記的應用程式。透過 Windows 市集側載應用程式或透過行動裝置管理 (MDM) 側載應用程式時，會決定應用程式擁有權標記。

如果無法識別應用程式的擁有者，它會以主要帳戶漫遊。如果裝置從 Windows 8 或 Windows 8.1 升級至 Windows 10，所有應用程式會由 Microsoft 帳戶標示為取得，因為一般而言，大部分應用程式已透過 Windows 市集取得，在 Windows 10 之前的 Azure AD 帳戶沒有 Windows 市集支援。如果應用程式是透過離線授權安裝，則會使用裝置上的主要帳戶標記應用程式。

>[AZURE.NOTE]  
企業擁有且連接到 Azure AD 的 Windows 10 裝置無法再將它們的 Microsoft 帳戶連接到網域帳戶。將 Microsoft 帳戶連接至網域帳戶並且讓所有使用者的資料同步處理至 Microsoft 帳戶的能力 (例如，透過「連接的 Microsoft 帳戶和 Active Directory」功能的 Microsoft 帳戶漫遊)，會從已加入連接的 Active Directory/Azure AD 環境中的 Windows 10 裝置移除。

## 我該如何從 Windows 8 中的 Microsoft 帳戶設定同步處理升級至 Windows 10 中的 Azure AD 設定同步處理？
加入執行 Windows 8 或 Windows 8.1 且具有連接的 Microsoft 帳戶的 Active Directory 網域的使用者，將會透過使用者的 Microsoft 帳戶同步處理設定。升級至 Windows 10 之後，只要 Active Directory 網域未與 Azure AD 連接，已加入網域的使用者會繼續透過 Microsoft 帳戶同步處理使用者設定。如果內部部署 Active Directory 網域與 Azure AD 連接，則使用者的裝置會開始嘗試使用連接的 Azure AD 帳戶同步處理設定。如果 Azure AD 系統管理員未啟用企業狀態漫遊，則具有連接的 Azure AD 帳戶的使用者將會停止同步處理設定。啟用透過 Azure AD 進行設定同步處理之後，如果使用者使用 Azure AD 身分識別登入，Windows 10 使用者將會立即開始使用 Azure AD 同步處理 Windows 設定。

在其公司的裝置儲存任何個人資料的使用者應該注意，Windows OS 和應用程式資料會開始同步處理至 Azure AD。這有下列含意：

- 個人的 Microsoft 帳戶使用者將緩慢地使其設定「逐漸遠離」工作或學校 Azure AD 帳戶的設定，因為 Microsoft 帳戶與 Azure AD 設定同步處理現在使用的是個別的帳戶。
- 個人資料 (例如 Wi-Fi 密碼、Web 認證，以及先前透過已連線的 Microsoft 帳戶同步處理的 Internet Explorer 我的最愛和索引標籤)，將透過 Azure AD 同步處理。


## Microsoft 帳戶和 Azure AD 企業狀態漫遊互通性如何運作？
在 Windows 10 的 2015 年 11 月或更新版本中，企業狀態漫遊一次僅支援一個帳戶。如果使用者使用工作或學校的 Azure AD 帳戶登入 Windows，則所有資料都將透過 Azure AD 同步處理。如果使用者使用個人 Microsoft 帳戶登入 Windows，則所有資料會透過 Microsoft 帳戶同步處理。通用的 AppData 只有在應用程式的授權是由主要帳戶所擁有時，才能使用裝置上的主要登入帳戶漫遊。任何次要帳戶所擁有的應用程式的通用 AppData 都不會進行同步處理。

## 對來自多個租用戶的 Azure AD 帳戶進行設定同步處理？
當不同 Azure AD 租用戶的多個 Azure AD 帳戶位於相同的裝置時，您必須更新裝置的登錄，以與每個 Azure AD 租用戶的 Azure Rights Management 服務 (RMS) 服務進行通訊。

1. 首先，針對每個 Azure AD 租用戶您需要 GUID。開啟 Azure 傳統入口網站，然後選取 Azure AD 租用戶。租用戶的 GUID 位於瀏覽器網址列的 URL 中，如下所示：`https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. 具有 GUID 之後，您必須新增下列登錄機碼：**HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\SettingSync\\WinMSIPC<租用戶識別碼 GUID>**。從 <**租用戶識別碼 GUID**> 機碼，建立名為 **AllowedRMSServerUrl** 的新多字串值 (REG-MULTI-SZ)，並針對其資料，指定裝置存取之其他 Azure 租用戶的授權發佈點 URL。
3. 您可以藉由執行 **Get-AadrmConfiguration** Cmdlet 來尋找授權發佈點 URL。如果 **LicensingIntranetDistributionPointUrl** 與 **LicenseingExtranetDistributionPointUrl** 的值不同，則同時指定這兩個值。如果值相同，則只要指定值一次。


## 適用於現有 Windows 傳統型應用程式的漫遊設定選項有哪些？
漫遊僅適用於通用 Windows 應用程式。有兩個選項可用來在現有 Windows 傳統型應用程式上啟用漫遊：

- 使用[桌面橋接器](http://aka.ms/desktopbridge)，您可以輕鬆地將現有的 Windows 傳統型應用程式帶入通用 Windows 平台。從這裡開始，僅需最少的程式碼變更就能夠充分利用 Azure AD appdata 漫遊。桌面橋接器會為應用程式提供應用程式身分識別，需要有此身分識別才能為現有的傳統型應用程式啟用應用程式資料漫遊。
- 使用 [User Experience Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx)，您可以針對現有的 Windows 傳統型應用程式建立自訂設定範本，並只針對 Win32 應用程式啟用漫遊。這個選項不需要應用程式開發人員變更應用程式的程式碼。UE-V 僅限於已購買 Microsoft 桌面最佳化套件之客戶的內部部署 Active Directory 漫遊。

管理員可以將 UEV 設定為只能漫遊 Windows 傳統型應用程式資料，方法是透過 [UE-V 群組原則](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)停用 Windows 作業系統設定和通用應用程式資料的漫遊。

- 停用「漫遊 Windows 設定」群組原則
- 啟用「不要同步處理 Windows 應用程式」群組原則
- 在應用程式區段停用「IE」漫遊

在未來，Microsoft 可能會調查讓 UE-V 深度整合到 Windows 及透過 Azure AD 雲端擴充 UE-V 漫遊設定的方式。


## 我可以在內部部署儲存同步處理的設定和資料嗎？
企業狀態漫遊會將所有同步處理的資料儲存於 Azure 雲端，而 UE-V 可為需要內部部署漫遊解決方案的企業提供內部部署漫遊解決方案。

## 誰擁有正在進行漫遊的資料？
企業擁有透過企業狀態漫遊來漫遊的資料。資料會儲存在 Azure 資料中心。在傳輸過程中和在雲端中待用的所有使用者資料，都會使用 Azure Rights Management (Azure RMS) 進行加密。相較於以 Microsoft 帳戶為基礎的設定同步處理 (其中只有某些機密資料，例如使用者認證，會在離開裝置之前加密)，這是一大改進。

Microsoft 致力於保護客戶資料。企業使用者的設定資料會在離開 Windows 10 裝置之前自動由 Azure RMS 加密，因此，另一位使用者無法讀取此資料。如果貴組織擁有 Azure RMS 的付費訂用帳戶，您可以使用其他 Azure RMS 功能，例如追蹤及撤銷文件、自動保護包含機密資訊的電子郵件，以及管理您自己的金鑰 (「自備金鑰」解決方案，也稱為 BYOK)。如需這些功能及 Azure RMS 運作方式的詳細資訊，請參閱[什麼是 Azure Rights Management](https://technet.microsoft.com/jj585026.aspx)。

## 我可以管理特定應用程式或設定的同步處理嗎？
在 Windows 10 中，沒有 MDM 或群組原則設定可以停用個別應用程式的漫遊。租用戶系統管理員可以停用受管理的裝置上所有應用程式的 appdata 同步處理，但是在每個應用程式或應用程式內的層級不會有更細微的控制。

## 個別使用者如何啟用/停用漫遊？
在 [設定] 應用程式中，移至 [帳戶] -> [同步處理您的設定]。您可以從這個頁面看見正在使用哪一個帳戶漫遊設定，您可以啟用或停用要漫遊得個別群組設定。

##Microsoft 對於在 Windows 10 中立即啟用漫遊的建議是什麼？
Microsoft 提供數個不同的設定漫遊解決方案可供使用，包括漫遊使用者設定檔、UE-V 和企業狀態漫遊。Microsoft 承諾將在未來的 Windows 版本中，致力於發展企業狀態漫遊。如果您的組織尚未就緒或不想要將資料移到雲端，Microsoft 建議使用 UE-V 做為主要的漫遊技術。如果您的組織需要針對現有的 Windows 傳統型應用程式支援漫遊，但是想要移往雲端，Microsoft 建議您同時使用企業狀態漫遊和 UE-V。UE-V 和企業狀態漫遊是非常類似的技術，不會互斥，現在它們彼此互補，確保您的組織提供使用者所需的漫遊服務。

使用企業狀態漫遊和 UE-V 時，適用下列規則：

- 企業狀態漫遊是裝置上主要的漫遊代理程式。UE-V 是用來補充「Win32 間距」。
- 您應該使用 UE-V 群組原則來停用 Windows 設定和現代 UWP 應用程式資料的 UE-V 漫遊，因為這些功能已經透過企業狀態漫遊涵蓋在內。

##企業狀態漫遊如何支援虛擬桌面基礎結構 (VDI)？
只有 Windows 10 用戶端 SKU 支援企業狀態漫遊，伺服器 SKU 不支援。如果用戶端 VM 裝載於 Hypervisor 電腦上且使用者從遠端連接到虛擬機器，則使用者的資料將會漫遊。如果有多位使用者共用相同的作業系統且使用者從遠端連接到伺服器以取得完整的桌面體驗，漫遊就不一定會運作。未正式支援以後者工作階段為基礎的案例。


## 我的組織在使用漫遊一段時間之後購買 Azure RMS，會發生什麼事？
如果您的組織已經使用有使用限制的 Azure RMS 免費訂用帳戶在 Windows 10 中使用漫遊，購買付費 Azure RMS 訂用帳戶不會對漫遊功能造成任何影響，且您的 IT 系統管理員不需要任何組態變更。

## 已知問題

- 智慧卡或虛擬智慧卡登入 Windows 會導致設定同步處理停止運作。如果您嘗試使用智慧卡或虛擬智慧卡登入您的裝置，同步處理將會停止運作。Windows 10 未來的更新可能會解決此問題。
- 同步處理 IE 我的最愛不適用於較舊版本的 Windows 10 組建。您將需要適用於 Windows 10 的 7 月份累積更新 (組建 10586.494 或更高版本)，才能使 IE 我的最愛進行同步處理。
- 在某些情況下，如果設定了 Multi-Factor Authentication (MFA)，企業狀態漫遊就無法同步處理資料。
    - 如果使用者已設定為在 Azure AD 入口網站中需要 [Azure MFA](multi-factor-authentication.md)，使用者可能無法在使用密碼登入 Windows 10 裝置時同步處理設定。這種類型的 MFA 設定是用來保護 Azure 管理員帳戶。管理員使用者仍然能夠藉由使用 [Microsoft Passport for Work](active-directory-azureadjoin-passport.md) PIN 來登入他們的 Windows 10 裝置，或者在存取其他 Azure 服務 (例如 Office 365) 時完成 Multi-Factor Authentication 來同步處理。
    - 如果管理員設定 AD FS MFA 條件式存取原則，而且裝置上的存取權杖到期，則同步處理可能會失敗。請確定您會先登出，然後再使用 [Microsoft Passport for Work](active-directory-azureadjoin-passport.md) PIN 登入，或者在存取其他 Azure 服務 (例如 Office 365) 時完成 Multi-Factor Authentication。
   
- 如果電腦透過自動註冊到 Azure AD 裝置來加入網域，而電腦已離站很久，它可能會遇到同步處理失敗，而且網域驗證無法完成。若要解決此問題，請將電腦連線到公司網路，以讓同步處理繼續執行。


## 相關主題
- [企業狀態漫遊概觀](active-directory-windows-enterprise-state-roaming-overview.md)
- [在 Azure Active Directory 中啟用企業狀態漫遊](active-directory-windows-enterprise-state-roaming-enable.md)
- [設定同步處理的群組原則和 MDM 設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Windows 10 漫遊設定參考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0803_2016-->