<properties
	pageTitle="在 Azure AD 網域服務中設定安全的 LDAP (LDAPS) | Microsoft Azure"
	description="針對 Azure AD 網域服務受管理網域設定安全的 LDAP (LDAPS)"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# 針對 Azure AD 網域服務受管理網域設定安全的 LDAP (LDAPS)
本文說明如何為 Azure AD 網域服務受管理網域啟用安全的輕量型目錄存取通訊協定 (LDAPS)。安全的 LDAP 亦稱為「透過安全通訊端層 (SSL)/傳輸層安全性 (TLS) 的輕量型目錄存取通訊協定 (LDAP)」。

## 開始之前
若要執行本文中所列出的工作，您將需要︰

1. 有效的 **Azure 訂用帳戶**。

2. **Azure AD 目錄** - 與內部部署目錄或僅限雲端的目錄同步處理。

3. **Azure AD 網域服務**必須已針對 Azure AD 目錄啟用。如果還沒有啟用，請按照[入門指南](./active-directory-ds-getting-started.md)所述的所有工作來進行。

4. **用來啟用安全 LDAP 的憑證**。
    - **建議** - 從您的企業 CA 或公共憑證授權單位取得憑證。這是更安全的組態選項。
	- 或者，您也可以選擇[建立自我簽署憑證](#task-1---obtain-a-certificate-for-secure-ldap)，如本文稍後所示。

<br>

### 安全 LDAP 憑證的需求
請先根據下列準則取得有效憑證再啟用安全的 LDAP。如果您嘗試使用無效/不正確的憑證來為受管理的網域啟用安全的 LDAP，您將會遭遇失敗。

1. **信任的簽發者** - 憑證必須由需要使用安全的 LDAP 連線到網域的電腦，所信任的授權單位加以發行。這可能是您組織的企業憑證授權單位或這些電腦所信任的公共憑證授權單位。

2. **存留期** - 憑證必須至少在接下來的 3 至 6 個月內都要保持有效。這可確保當憑證過期時，還能對受管理的網域進行安全的 LDAP 存取。

3. **主體名稱** - 在受管理的網域中，憑證的主體名稱必須是萬用字元。比方說，如果您的網域名稱為 'contoso100.com'，則憑證的主體名稱必須是 '*.contoso100.com'。DNS 名稱 (主體替代名稱) 也必須設定為此萬用字元名稱。

3. **金鑰使用方法** - 必須將憑證設定為下列用途 - 數位簽章與金鑰編密。

4. **憑證目的** - 憑證必須有效可進行 SSL 伺服器驗證。

<br>

## 工作 1 - 取得安全 LDAP 的憑證
第一項工作牽涉到取得憑證，以便用於對受管理的網域進行安全的 LDAP 存取。您有兩個選擇：

- 從憑證授權單位 (例如貴組織的企業 CA) 或從公共憑證授權單位取得憑證。

- 建立自我簽署憑證。


### 選項 A (建議選項) - 從憑證授權單位取得安全的 LDAP 憑證
如果您的組織部署企業公開金鑰基礎結構 (PKI)，您必須從組織的企業憑證授權單位 (CA) 取得憑證。如果您的組織從公共憑證授權單位取得其憑證，您必須從該公共憑證授權單位取得安全的 LDAP 憑證。

在要求憑證時，請務必遵循[安全 LDAP 憑證的需求](#requirements-for-the-secure-ldap-certificate)中所述的需求。

請注意，需要使用安全的 LDAP 連線到受管理網域的用戶端電腦必須信任 LDAPS 憑證的簽發者。


### 選項 B - 為安全的 LDAP 建立自我簽署的憑證
您可以選擇為安全的 LDAP 建立自我簽署的憑證，但前提是：

- 您組織中的憑證並非由企業憑證授權單位核發，或是
- 您預計不會使用公共憑證授權單位所核發的憑證。

**使用 PowerShell 建立自我簽署憑證**

在您的 Windows 電腦上以**系統管理員**的身分開啟新的 PowerShell 視窗，並輸入下列命令以建立新的自我簽署憑證。

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

在上述範例中，將 'contoso100.com' 替換為 Azure AD 網域服務受管理網域的 DNS 網域名稱。

![選取 Azure AD 目錄](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

新建立的自我簽署憑證會放在本機電腦的憑證存放區中。


## 工作 2 - 將安全的 LDAP 憑證匯出到 .PFX 檔案
在開始這項工作之前，請先確定您已從您的企業憑證授權單位或公共憑證授權單位取得安全的 LDAP 憑證，或已建立自我簽署憑證。

執行下列步驟，以將 LDAPS 憑證匯出到 .PFX 檔案。

1. 按 [開始] 按鈕並輸入 **R** 以顯示 [執行] 對話方塊。輸入 **mmc**，然後按一下 [確定]。

    ![啟動 MMC 主控台](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)

2. 在 [使用者帳戶控制] 提示字元處按一下 [是]，以系統管理員身分啟動 MMC (Microsoft Management Console)。

3. 在 [檔案] 功能表中，按一下 [新增/移除嵌入式管理單元...]。

    ![新增嵌入式管理單元至 MMC 主控台](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)

4. 在 [新增或移除嵌入式管理單元] 對話方塊中，選取 [憑證] 嵌入式管理單元，然後按一下 [新增 >] 按鈕。

    ![新增憑證嵌入式管理單元至 MMC 主控台](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)

5. 在 [憑證嵌入式管理單元] 精靈中，選取 [電腦帳戶] 並按一下 [下一步]。

    ![新增電腦帳戶的憑證嵌入式管理單元](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)

6. 在 [選取電腦] 頁面上，選取 [本機電腦: (執行這個主控台的電腦)] 並按一下 [完成]。

    ![新增憑證嵌入式管理單元 - 選取電腦](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)

7. 在 [新增或移除嵌入式管理單元] 對話方塊中，按一下 [確定] 以在 MMC 中新增憑證嵌入式管理單元。

    ![新增憑證嵌入式管理單元至 MMC - 完成](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)

8. 在 MMC 視窗中，按一下以展開 [主控台根目錄]。您應該會看到已載入 [憑證] 嵌入式管理單元。按一下 [憑證 (本機電腦)] 加以展開。按一下以依序展開 [個人] 節點和 [憑證] 節點。

    ![開啟個人憑證存放區](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)

9. 如上圖所示，您應該會看到我們剛建立了自我簽署憑證。您可以檢查憑證的屬性以確定指紋符合您在建立憑證時 PowerShell 視窗上所報告的指紋。

10. 選取自我簽署憑證並**按一下滑鼠右鍵**。在快顯功能表中，依序選取 [所有工作] 和 [匯出...]。

    ![匯出憑證](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)

11. 在 [憑證匯出精靈] 中按一下 [下一步]。

    ![匯出憑證精靈](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)

12. 在 [匯出私密金鑰] 頁面上，選取 [是，匯出私密金鑰] 並按一下 [下一步]。

    ![匯出憑證的私密金鑰](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [AZURE.WARNING] 您必須匯出私密金鑰以及憑證。若您提供的 PFX 未包含憑證的私密金鑰，則為受管理的網域啟用安全的 LDAP 將會失敗。

13. 在 [匯出檔案格式] 頁面上，選取 [個人資訊交換 - PKCS #12 (.PFX)] 作為匯出憑證的檔案格式。

    ![匯出憑證檔案格式](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

	> [AZURE.NOTE] 只有 .PFX 檔案格式受到支援。請勿將憑證匯出為 .CER 檔案格式。

14. 在 [安全性] 頁面上選取 [密碼] 選項，然後輸入密碼來保護 .PFX 檔案。請記住此密碼，因為下一個工作會用到它。按一下 [下一步] 繼續進行。

    ![匯出憑證指定密碼](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

	> [AZURE.NOTE] 記下此密碼。在[工作 3 - 為受管理的網域啟用安全 LDAP](#task-3---enable-secure-ldap-for-the-managed-domain) 中，為此受管理網域啟用安全 LDAP 時，會需要此密碼。

15. 在 [要匯出的檔案] 頁面上，指定檔案名稱及接收匯出憑證的位置。

    ![匯出憑證指定路徑](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)

16. 在接下來的頁面上按一下 [完成]，以將憑證匯出至 PFX 檔案。憑證匯出後，您應該會看到確認對話方塊。

    ![憑證匯出完成](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## 工作 3 - 為受管理的網域啟用安全 LDAP
請執行下列設定步驟以啟用安全 LDAP。

1. 巡覽至 **[Azure 傳統入口網站](https://manage.windowsazure.com)**。

2. 在左窗格中，選取 [Active Directory] 節點。

3. 選取已啟用 Azure AD 網域服務的 Azure AD 目錄 (也稱為「租用戶」)。

    ![選取 Azure AD 目錄](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. 按一下 [設定] 索引標籤。

    ![設定目錄的索引標籤](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. 向下捲動到標題為 [網域服務] 的區段。您應該會看到標題為 [安全 LDAP (LDAPS)] 的選項，如以下螢幕擷取畫面中所示。

    ![網域服務組態區段](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)

6. 按一下 [設定憑證...] 按鈕以顯示 [設定安全 LDAP 的憑證] 對話方塊。

    ![設定安全 LDAP 的憑證](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)

7. 按一下 [含有憑證的 PFX 檔案] 下的資料夾圖示，指定要用於對受管理網域進行安全 LDAP 存取之憑證所在的 PFX 檔案。此外，也請輸入將憑證匯出到 PFX 檔案時所指定的密碼。完成時，請按一下底部的完成按鈕。

    ![指定安全 LDAP 的 PFX 檔案和密碼](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)

8. [設定] 索引標籤的 [網域服務] 區段應該會變成灰色，而且會有幾分鐘的時間處在 [擱置...] 狀態。在此期間，LDAPS 憑證會進行驗證以確認是否正確，並為受管理的網域設定安全 LDAP。

    ![安全 LDAP - 擱置中狀態](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

    > [AZURE.NOTE] 為受管理的網域啟用安全 LDAP 需要約 10 到 15 分鐘的時間。如果提供的安全 LDAP 憑證不符合所要求的準則 (例如網域名稱不正確、憑證已到期或即將到期等)，則不會為您的目錄啟用安全 LDAP，而且您會看到失敗。

9. 在為受管理的網域成功啟用安全 LDAP 後，[擱置...] 訊息應該會消失。您應該會看到已顯示憑證的指紋。

    ![安全 LDAP 已啟用](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>


## 工作 4 - 透過網際網路啟用安全 LDAP 存取
**選擇性工作** - 如果您不打算使用 LDAPS 來透過網際網路存取受管理的網域，請略過這項工作。

開始這項工作之前，請先確定您已完成[工作 3](#task-3---enable-secure-ldap-for-the-managed-domain) 中所述的步驟。

1. 您應該會在 [設定] 頁面的 [網域服務] 區段中看到 [透過網際網路啟用安全 LDAP 存取] 的選項。此選項預設會設定為 [否]，因為依預設會停用透過安全 LDAP 對受管理網域的網際網路存取。

    ![安全 LDAP 已啟用](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)

2. 將 [透過網際網路啟用安全 LDAP 存取] 切換為 [是]。按一下底部面板上的 [儲存] 按鈕。![安全 LDAP 已啟用](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)

3. [設定] 索引標籤的 [網域服務] 區段應該會變成灰色，而且會有幾分鐘的時間處在 [擱置...] 狀態。在此期間，將會啟用透過安全 LDAP 對受管理網域的網際網路存取。

    ![安全 LDAP - 擱置中狀態](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

    > [AZURE.NOTE] 為受管理的網域啟用透過安全 LDAP 的網際網路存取需要約 10 分鐘的時間。

4. 在成功啟用透過網際網路對受管理網域進行安全 LDAP 存取後，[擱置...] 訊息應該會消失。您應該會在 [LDAPS 存取的外部 IP 位址] 欄位中，看到可用來透過 LDAPS 存取您目錄的外部 IP 位址。

    ![安全 LDAP 已啟用](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## 工作 5 - 設定 DNS 以從網際網路存取受管理的網域
**選擇性工作** - 如果您不打算使用 LDAPS 來透過網際網路存取受管理的網域，請略過這項工作。

開始這項工作之前，請先確定您已完成[工作 4](#task-4---enable-secure-ldap-access-over-the-internet) 中所述的步驟。

為受管理的網域啟用了透過網際網路的安全 LDAP 存取後，您需要更新 DNS 以便用戶端電腦可以找到此受管理網域。在工作 4 的最後階段，[設定] 索引標籤的 [LDAPS 存取的外部 IP 位址] 中會顯示外部 IP 位址。

請設定外部 DNS 提供者，讓受管理網域的 DNS 名稱 (例如 'contoso100.com') 指向這個外部 IP 位址。在我們的範例中，我們需要建立下列 DNS 項目︰

    contoso100.com  -> 52.165.38.113

這樣就大功告成了。您現在已準備好可使用安全 LDAP 透過網際網路連線到受管理網域。

> [AZURE.WARNING] 請記住，用戶端電腦必須信任 LDAPS 憑證的簽發者，才能成功使用 LDAPS 連線到受管理網域。如果您使用企業憑證授權單位或公開的受信任憑證授權單位，那麼這不是問題，因為用戶端電腦會信任這些憑證簽發者。如果您使用自我簽署憑證，則必須在用戶端電腦的受信任憑證存放區中安裝自我簽署憑證的公開部分 (也就是匯出時不含私密金鑰)。

<br>

## 相關內容

- [Administer an Azure AD Domain Services managed domain (管理 Azure AD 網域服務受管理的網域) ](active-directory-ds-admin-guide-administer-domain.md)

<!---HONumber=AcomDC_0706_2016-->