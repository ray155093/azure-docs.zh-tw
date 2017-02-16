---
title: "Azure AD Connect：傳遞驗證 | Microsoft Docs"
description: "本主題提供您所需資訊，以讓您了解 Azure AD 傳遞驗證如何搭配內部部署 Active Directory (AD) 運作，來提供對 Azure Active Directory (Azure AD) 和已連線服務的存取。"
services: active-directory
keywords: "什麼是 Azure AD Connect, 安裝 Active Directory, Azure AD 的必要元件, SSO, 單一登入"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: c692d552a649ce194591e23b30d39f434ef853eb
ms.openlocfilehash: f87be3753f1eb5a01945cd154b0ee9132e9bb43c


---

# <a name="what-is-azure-ad-pass-through-authentication"></a>什麼是 Azure AD 傳遞驗證
使用相同的認證 (使用者名稱與密碼) 存取您的企業資源和雲端服務，可確保使用者不需記住不同的認證，並減少他們忘記登入方式的機會。  這還有另一個優點，就是能夠降低支援人員參與密碼重設事件的機率。  

雖然許多組織都不排斥使用 Azure AD [密碼雜湊同步處理](active-directory-aadconnectsync-implement-password-synchronization.md)來為使用者提供單一認證以存取內部部署和雲端服務，但其他組織則是要求密碼 (即使是雜湊形式) 不得離開其內部組織界限範圍。  

Azure AD 傳遞驗證可為這些客戶提供簡單的解決方案，在不需要複雜的網路基礎結構，或不需要在雲端有任何形式內部部署密碼存在的情況下，確保會針對他們的內部部署 Active Directory 執行 Azure AD 服務的密碼驗證。  

與[單一登入](active-directory-aadconnect-sso.md)結合時，使用者同樣不需要輸入他們的密碼就能登入 Azure AD 或其他雲端服務，可在這些客戶的公司電腦上為他們提供真正的整合式體驗。

![傳遞驗證](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

傳遞驗證可以透過 AAD Connect 設定，並且使用會監聽密碼驗證要求的簡單內部部署代理程式。  代理程式可以輕鬆地部署到多部電腦，以提供高可用性和負載平衡。  因為所有通訊都是輸出通訊，所以不需要 DMZ 或在 DMZ 中安裝連接器。  連接器的電腦需求如下：

- Windows Server 2012 R2 或更新版本
- 加入樹系中將在其中驗證使用者的網域

>[!NOTE]
>如果樹系之間彼此信任且名稱尾碼路由已正確設定，就能支援多樹系環境。

## <a name="supported-clients-in-the-preview"></a>預覽版本中支援的用戶端
對傳遞驗證的支援，是透過支援[新式驗證](https://aka.ms/modernauthga)的網頁瀏覽器型用戶端和 Office 用戶端來提供。  對於像是舊版 Office 用戶端、Exchange Active Sync (也就是行動裝置上的原生電子郵件用戶端) 這種不支援的用戶端，建議客戶使用和新式驗證對等的功能。  這不只能夠允許使用傳遞驗證，也允許套用條件式存取，例如 Multi-Factor Authentication。

對於使用 Windows 10 並已加入 Azure AD 的客戶，目前不支援傳遞驗證。  但是除了上面提到的舊版用戶端之外，客戶也可以利用密碼雜湊同步處理做為 Windows 10 的自動遞補。

>[!NOTE]
>當已經在 Azure AD Connect 中選取傳遞驗證做為登入選項時，預覽期間會預設啟用密碼雜湊同步處理。 您可以在 Azure AD Connect 的 [選項] 頁面上停用此功能。

## <a name="how-azure-ad-pass-through-authentication-works"></a>Azure AD 傳遞驗證的運作方式
當使用者在 Azure AD 登入頁面中輸入它們的使用者名稱和密碼時，Azure AD 會將使用者名稱和密碼放在適當的內部部署連接器佇列上以供驗證。  然後其中一個可用的內部部署連接器就會抓取使用者名稱和密碼，並針對 Active Directory 加以驗證。  驗證會透過標準 Windows API 發生，類似於 Active Directory Federation Services 驗證密碼的方式。

然後內部部署網域控制站會評估要求並向連接器傳回回應，接著會將此回應傳回給 Azure AD。 然後 Azure AD 會評估回應並適當地回應使用者，例如，簽發權杖或要求多重要素驗證。  下圖顯示各種步驟。


![傳遞驗證](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="azure-ad-pass-through-prerequisites"></a>Azure AD 傳遞驗證必要條件
在您可以啟用及使用 Azure AD 傳遞驗證之前，您需要有：

- Azure AD Connect 軟體
- 您必須是全域管理員的 Azure AD 目錄。  

>[!NOTE]
>建議使用僅限雲端使用的系統管理員帳戶，這樣一來您就能夠在您的內部部署服務失敗或無法使用時，管理您租用戶的組態。

- 執行 Windows Server 2012 R2 或更新版本，以在其上執行 AAD Connect 工具的伺服器。  這部電腦必須是和將受驗證之使用者所在相同樹系的成員。
- 請注意，如果您有超過一個樹系包含要使用 Azure AD 同步處理的使用者，那些樹系之間必須彼此信任。
- 內部部署的 UserPrincipalName 必須用來作為 Azure AD 使用者名稱。
- 執行 Windows Server 2012 R2 或更新版本，以在其上執行第二個連接器來獲得高可用性和負載平衡的第二部伺服器。  下面包含如何部署此連接器的指示。
- 如果連接器和 Azure AD 之間有防火牆，請確定：
    - 如果已經啟用 URL 篩選，請確定連接器可以和下列 URL 通訊：
        -  *.msappproxy.net
        -  *.servicebus.windows.net.  
        -  連接器也會在直接 IP 連線上建立對 [Azure 資料中心 IP 範圍](https://www.microsoft.com/en-us/download/details.aspx?id=41653)的連線。
    - 請確定防火牆不會執行 SSL 檢查，因為連接器會使用用戶端憑證來與 Azure AD 通訊。
    - 請確定連接器可以在下面的連接埠上對 Azure AD 發出 HTTPS (TCP) 要求。

|連接埠號碼|說明
| --- | ---
|80|允許輸出 HTTP 流量來進行安全性驗證 (例如 SSL 憑證撤銷清單)。
|443|   針對 Azure AD 啟用使用者驗證。
|8080/443|  啟用連接器開機順序以及連接器自動更新。
|9090|  啟用連接器註冊 (只有在連接器註冊程序才需要)。
|9091|  啟用連接器信任憑證自動更新。
|9352、5671|    可讓連接器與 Azure AD 服務之間針對連入要求進行通訊。
|9350|  [選擇性] 針對連入要求啟用較佳的效能。
|10100–10120|   可讓連接器的回應回到 Azure AD。

如果您的防火牆根據原始使用者強制執行流量，請針對來自當做網路服務執行的 Windows 服務的流量，開放這些連接埠。 此外，請務必為 NT Authority\System 啟用連接埠 8080。

## <a name="enabling-pass-through-authentication"></a>啟用傳遞驗證
Azure AD 傳遞驗證是透過 Azure AD Connect 啟用。  啟用傳遞驗證將會在和 Azure AD Connect 相同的伺服器上部署第一個連接器。  在安裝 Azure AD Connect 時，請選取一個自訂安裝並在登入選項頁面上選取傳遞驗證。 如需詳細資訊，請參閱[自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)。

![單一登入](./media/active-directory-aadconnect-sso/sso3.png)

依照預設，第一個傳遞驗證連接器將會安裝在 Azure AD Connect 伺服器上。  您應該在另一部電腦上部署第二個連接器，以確保您擁有驗證要求的高可用性和負載平衡。

若要部署第二個連接器，請遵循下列指示：

### <a name="step-1-install-the-connector-without-registration"></a>步驟 1：安裝連接器，但不註冊

1.  下載最新的[連接器](https://go.microsoft.com/fwlink/?linkid=837580)。
2.  以系統管理員身分開啟命令提示字元。
3.  執行下列命令，其中的 /q 表示無訊息安裝，即安裝不會提示您接受「使用者授權合約」。

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>步驟 2：在 Azure AD 註冊連接器以用於傳遞認證

1.  以系統管理員身分開啟 PowerShell 視窗
2.  瀏覽至 “C:\Program Files\Microsoft AAD App Proxy Connector”，然後執行指令碼。
.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication
3.  出現提示時，輸入您 Azure AD 租用戶「系統管理員」帳戶的認證。

## <a name="troubleshooting-pass-through-authentication"></a>針對傳遞驗證進行疑難排解
針對傳遞驗證進行疑難排解時，可能會發生幾個不同類別的問題。  視問題類型而定，您可能需要查詢不同的地方。

針對與連接器有關的錯誤，您可以查看位於 [應用程式及服務記錄檔\Microsoft\AadApplicationProxy\Connector\Admin] 底下的連接器電腦「事件記錄檔」。  如有需要，只要檢視分析和偵錯記錄檔，並啟用連接器工作階段記錄檔，即可取得更詳細的記錄檔。  請注意，不建議在預設啟用此記錄檔的情況下執行，而且只有在停用此記錄檔之後，才能看見內容。

您也可以在 C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace 中的連接器追蹤記錄檔中找到其他資訊。 這些記錄也包括個別使用者傳遞驗證失敗的原因，例如下面包含錯誤碼 1328 的項目：

    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ

您可以透過啟動命令提示字元並執行以下命令，來取得錯誤的詳細資料。 (在要求中使用錯誤號碼取代 '1328'。)

Net helpmsg 1328

結果將如下所示。

![傳遞驗證](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

如果已經啟用稽核記錄，您也可以在網域控制站的安全性記錄檔中找到其他資訊。  依連接器針對驗證要求進行的簡單查詢會像下面這樣：

    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>

Azure AD 登入畫面上報告的其他錯誤會在下面和適當的解決步驟一起詳細說明。

|錯誤|說明|解決方案
| --- | --- | ---
|AADSTS80001|無法連線至 Active Directory|請確定連接器電腦已加入網域，且能夠連線至 Active Directory。  
|AADSTS8002|連線至 Active Directory 時發生逾時|請檢查以確定 Active Directory 可用並且正在回應來自連接器的要求。
|AADSTS80004|傳遞給連接器的使用者名稱無效|請確定使用者正嘗試用來登入的使用者名稱正確無誤。
|AADSTS80005|驗證發生無法預期的 WebException|這是暫時性問題。 請重試要求。  如果持續發生失敗，請連絡 Microsoft 支援服務。
|AADSTS80007|和 Active Directory 通訊時發生錯誤|請檢查連接器記錄檔以了解詳細資訊，並確認 Active Directory 如預期般運作。



<!--HONumber=Jan17_HO1-->


