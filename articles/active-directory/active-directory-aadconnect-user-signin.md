---
title: Azure AD Connect：使用者登入 | Microsoft Docs
description: Azure AD Connect 使用者登入 (用於自訂設定)。
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: billmath

---
# <a name="azure-ad-connect-user-sign-on-options"></a>Azure AD Connect 使用者登入選項
Azure AD Connect 可讓使用者使用同一組密碼登入雲端和內部部署資源。  您可以從數個不同方式中選擇啟用此設定的方式。

## <a name="choosing-a-user-sign-in-method"></a>選擇使用者的登入方法
由於大部分組織只想要讓使用者登入 Office 365、SaaS 應用程式和其他 Azure AD 資源，因此建議使用預設的密碼同步處理選項。
然而，部分組織有必須使用同盟登入選項 (例如 AD FS) 的特定原因。  其中包含：

* 組織已部署 AD FS 或協力廠商同盟提供者
* 安全性原則禁止將密碼雜湊同步至雲端
* 要求使用者從公司網路存取加入網域電腦的雲端資源時，使用無縫式 SSO (沒有其他密碼提示)
* 您需要一些特定的 AD FS 功能
  * 使用協力廠商提供者或智慧卡的內部部署多因素驗證 (了解 Windows Server 2012 R2 中適用於 AD FS 的協力廠商 MFA 提供者)
  * Active Directory 整合功能，例如軟帳戶鎖定或 AD 密碼及工作時數原則
  * 使用裝置註冊、Azure AD 聯結或 Intune MDM 原則有條件存取內部部署和雲端資源

### <a name="password-synchronization"></a>密碼同步處理
使用密碼同步處理，就可將使用者密碼的雜湊從內部部署 Active Directory 同步至 Azure AD。  當密碼在內部部署中變更或重設時，新密碼會立即同步至 Azure AD，讓使用者用來存取雲端資源的密碼永遠與內部部署相同。  密碼絕不會傳送至 Azure AD，也不會以未加密文字儲存在 Azure AD 中。
密碼同步處理可以搭配密碼回寫功能一起使用，以啟用 Azure AD 中的自助式密碼重設功能。

<center>![雲端](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[密碼同步處理的詳細資訊](active-directory-aadconnectsync-implement-password-synchronization.md)

### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>使用 Windows Server 2012 R2 伺服器陣列中最新或現有 AD FS 的同盟
使用同盟登入，使用者就可以使用內部部署密碼登入 Azure AD 服務；使用公司網路時，無須再次輸入密碼就可登入服務。  使用 AD FS 的同盟選項可讓您在 Windows Server 2012 R2 伺服器陣列中部署新的或指定現有的 AD FS。  如果您選擇指定現有的伺服器陣列，則 Azure AD Connect 將會在伺服器陣列與 Azure AD 間設定信任，讓使用者能夠登入。

<center>![雲端](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2,-you-will-need-the-following"></a>若要在 Windows Server 2012 R2 中部署使用 AD FS 的同盟，您需要下列項目
如果您要部署新的伺服器陣列：

* 同盟伺服器的 Windows Server 2012 R2 伺服器。
* Web 應用程式 Proxy 的 Windows Server 2012 R2 伺服器。
* 含所想要同盟服務名稱的一個 SSL 憑證的 .pfx 檔案，例如 fs.contoso.com。

如果您要部署新的伺服器陣列，或使用現有的伺服器陣列：

* 同盟伺服器上的本機系統管理員認證。
* 在想要部署 Web 應用程式 Proxy 角色之任何工作群組 (非加入網域) 伺服器上的本機系統管理員認證。
* 執行精靈的電腦必須能夠透過 Windows 遠端管理連線到您要安裝 AD FS 或 Web 應用程式 Proxy 的任何其他電腦。

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>使用較早版本的 AD FS 或協力廠商解決方案登入
如果您已使用較早版本的 AD FS (例如 AD FS 2.0) 或協力廠商同盟提供者設定雲端登入，就能夠透過 Azure AD Connect 選擇略過使用者登入設定。  這可讓您取得最新的同步處理和 Azure AD Connect 的其他功能，同時仍使用現有的解決方案用於登入。

## <a name="user-sign-in-and-user-principal-name-(upn)"></a>使用者登入和使用者主體名稱 (UPN)
### <a name="understanding-user-principal-name"></a>了解使用者主體名稱
在 Active Directory 中，預設的 UPN 尾碼是在其中建立使用者帳戶之網域的 DNS 名稱。 大部分情況下，這是註冊為網際網路上的企業網域的網域名稱。 不過，您可以使用 Active Directory 網域及信任新增其他 UPN 尾碼。

使用者的 UPN 格式是 username@domai. 例如，Active Directory contoso.com 使用者 John 可能有 UPN john@contoso.com. 使用者的 UPN 是以 RFC 822 為基礎。 雖然 UPN 和電子郵件共用相同的格式，但使用者的 UPN 值不一定等於使用者的電子郵件地址。

### <a name="user-principal-name-in-azure-ad"></a>Azure AD 中的使用者主體名稱
Azure AD Connect 精靈將會使用 userPrincipalName 屬性，或讓您指定內部部署中要用來做為 Azure AD 中的使用者主體名稱的屬性 (在自訂安裝中)。 這是要用於登入 Azure AD 的值。 如果使用者主體名稱屬性的值未對應到 Azure AD 中已驗證的網域，則 Azure AD 會將該值替換為預設的 .onmicrosoft.com 值。

Azure Active Directory 中的每個目錄隨附內建網域名稱，格式為 contoso.onmicrosoft.com，可讓您開始使用 Azure 或其他 Microsoft 服務。 您可以使用自訂網域改善及簡化登入體驗。 如需關於 Azure AD 中的自訂網域名稱以及如何驗證網域的資訊，請閱讀 [將您的自訂網域名稱新增至 Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Azure AD 登入組態
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>使用 Azure AD Connect 設定 Azure AD 登入組態
Azure AD 登入體驗取決於 Azure AD是否能符合將同步處理至 Azure AD 目錄中已驗證的其中一個自訂網域之使用者的使用者主體名稱尾碼。 Azure AD Connect 會在設定 Azure AD 登入設定時提供協助，讓使用者在雲端的登入體驗類似於內部部署的體驗。 Azure AD Connect 會列出為網域定義的 UPN 尾碼，嘗試將它們與 Azure AD 中的自訂網域做比對，並協助您進行需要採取的適當動作。
Azure AD 的登入頁面會列出為內部部署 Active directory 定義的 UPN 尾碼，並顯示每個尾碼相對應的狀態。 狀態值可以是下列其中一個︰

* 已驗證︰Azure AD Connect 可在 Azure AD 中找到匹配的已驗證網域，不需要採取任何動作
* 未驗證︰Azure AD Connect 可在 Azure AD 中找到匹配、但未驗證的自訂網域。 使用者應該驗證自訂網域，以確保使用者的 UPN 尾碼不會在同步處理之後，變更為預設的 .onmicrosoft.com 尾碼。
* 未新增︰Azure AD Connect 找不到對應至 UPN 尾碼的自訂網域。 使用者必須新增並驗證對應至 UPN 尾碼的自訂網域，以確保使用者的 UPN 尾碼不會在同步處理之後，變更為預設的 .onmicrosoft.com 尾碼。

Azure AD 的登入頁面會列出為內部部署 Active Directory 定義的 UPN 尾碼，以及 Azure AD 中對應的自訂網域與目前的驗證狀態。 在自訂安裝中，您現在可以在 [Azure AD 登入]  頁面上選取使用者主體名稱的屬性。

![Azure AD 登入頁面](.\\media\\active-directory-aadconnect-user-signin\\custom_azure_sign_in.png)

您可以按一下 [重新整理] 按鈕，從 Azure AD 中重新擷取最新的自訂網域狀態。

### <a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>選取 Azure AD 中使用者主體名稱的屬性
UserPrincipalName - 屬性 userPrincipalName 是使用者登入 Azure AD 和 Office 365 時會使用的屬性。 使用的網域 (也稱為 UPN 尾碼)，應該會在同步處理使用者前於 Azure AD 中進行驗證。 強烈建議保留預設屬性 userPrincipalName。 如果此屬性不可路由傳送且無法驗證，可以選取另一個屬性，例如選取 email 做為保存登入 ID 的屬性。 這就是所謂的替代 ID。 替代 ID 屬性值必須遵循 RFC822 標準。 替代 ID 可以搭配密碼單一登入 (SSO) 和同盟 SSO 做為登入解決方案使用。

> [!NOTE]
> 使用替代 ID 會與所有 Office 365 工作負載不相容。 如需詳細資訊，請參閱 [設定替代的登入 ID](https://technet.microsoft.com/library/dn659436.aspx)。
> 
> 

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>不同的自訂網域狀態和對於 Azure 登入體驗的影響
請務必要了解 Azure AD 目錄中的自訂網域狀態與內部部署中所定義的 UPN 尾碼之間的關聯性。 讓我們來逐個了解，當您使用 Azure AD Connnect 設定同步處理時，所可能遇到的不同 Azure 登入體驗。

針對以下資訊，讓我們假設我們所關注的 UPN 尾碼 contoso.com，會在內部部署目錄中做為 UPN 的一部分，例如 user@contoso.com。

###### <a name="express-settings-/-password-synchronization"></a>快速設定/密碼同步處理
| State | 對使用者的 Azure 登入體驗的影響 |
|:---:|:--- |
| 未新增 |在此案例中，Azure AD 目錄內並未針對 contoso.com 新增任何自訂網域。 在內部部署中具有尾碼 @contoso.com, 的 UPN 的使用者，將不能使用其內部部署 UPN 來登入 Azure。 他們必須為預設的 Azure AD 目錄新增尾碼，以改為使用 Azure AD 提供給他們的新 UPN。 例如，如果您要將使用者同步至 Azure AD 目錄 azurecontoso.onmicrosoft.com，則內部部署使用者 user@contoso.com 將得到的 UPN 會是 user@azurecontoso.onmicrosoft.com |
| 未驗證 |在此案例中，我們已在 Azure AD 目錄中新增自訂網域 contoso.com，但其尚未驗證。 如果您不先驗證網域就繼續同步處理使用者，則 Azure AD 會對使用者指派新的 UPN，正如它在「未新增」案例中所做的動作。 |
| Verified |在此案例中，我們已在 Azure AD 中為 UPN 尾碼新增並驗證自訂網域 contoso.com。 使用者可以使用其內部部署使用者主體名稱 (例如 user@contoso.com,)，在其同步處理至 Azure AD 之後登入至 Azure |

###### <a name="ad-fs-federation"></a>AD FS 同盟
您無法與 Azure AD 中的預設 .onmicrosoft.com 網域或 Azure AD 中未驗證的自訂網域建立同盟。 當您執行 Azure AD Connect 精靈時，如果您選取要與未驗證的網域建立同盟，則 Azure AD Connect 會提示您必須建立的記錄，其中要有該網域的 DNS 裝載位置資訊。 如需詳細資訊，請參閱 [這裡](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)。

如果您選取的使用者登入選項為 [與 AD FS 同盟]，您就必須擁有自訂網域，才能繼續在 Azure AD 中建立同盟。 在我們的討論中，這表示我們應該在 Azure AD 目錄中新增自訂網域 contoso.com。

| State | 對使用者的 Azure 登入體驗的影響 |
|:---:|:--- |
| 未新增 |在此案例中，Azure AD Connect 無法在 Azure AD 目錄中找到 UPN 尾碼 contoso.com 的匹配自訂網域。 如果您需要讓使用者使用 AD FS 搭配其內部部署 UPN (例如 user@contoso.com) 來進行登入，您就必須新增自訂網域 contoso.com |
| 未驗證 |在此案例中，Azure AD Connect 會提示您適當的詳細資料，指導您如何在稍後的階段驗證網域 |
| Verified |在此案例中，您可以繼續進行設定而不需要採取任何進一步的動作 |

## <a name="changing-user-sign-in-method"></a>變更使用者的登入方法
您可以在使用精靈完成 Azure AD Connect 的初始設定之後，使用 Azure AD Connect 中的可用工作，將使用者的登入方法從同盟變更為密碼同步處理。 請再次執行 Azure AD Connect 精靈，然後您將會看到可以執行的工作清單。 在工作清單中選取 [變更使用者登入]  。 

![變更使用者登入」](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

在下一頁，系統會要求您提供 Azure AD 的認證。

![連接至 Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

在 [使用者登入] 頁面上，選取 [密碼同步處理]。 這會讓目錄從同盟目錄變更為受管理目錄。

![連接至 Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

> [!NOTE]
> 如果您只是要暫時切換到密碼同步處理，則請勾選 [不要轉換使用者帳戶]。 不勾選該選項會導致將每個使用者都轉換為同盟使用者，而這可能耗時數小時。
> 
> 

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

深入了解 [Azure AD Connect：設計概念](active-directory-aadconnect-design-concepts.md)

<!--HONumber=Oct16_HO2-->


