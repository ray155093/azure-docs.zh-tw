> [!NOTE]
> 我們計劃改善這種經驗，並取代下列步驟。

### <a name="create-an-administrator-credential-in-the-azure-ad-b2c-tenant"></a>在 Azure AD B2C 租用戶中建立管理員認證。

在下一節中，您使用的認證必須使用您的 Azure AD B2C 租用戶網域。 若要這麼做，您需要使用這類認證建立系統管理員帳戶。 若要這樣做：

1. 在 [Azure 入口網站](https://portal.azure.com)中，切換至您的 Azure AD B2C 租用戶環境並開啟 Azure AD B2C 刀鋒視窗。 [示範操作方式。](..\articles\active-directory-b2c\active-directory-b2c-navigate-to-b2c-context.md)
1. 選取 [使用者和群組]。
1. 選取 [所有使用者]。
1. 按一下 [+ 新增使用者]。
    * 設定 [名稱] = `Admin`。
    * 設定 [使用者名稱] = `admin@{tenantName}.onmicrosoft.com`，其中 `{tenantName}` 是您的 Azure AD B2C 租用戶名稱。
1. 在 [目錄角色] 之下，選擇 [全域管理員] 並且按 [確定]。
1. 按一下 [建立] 來建立管理使用者。
1. 勾選 [顯示密碼] 並複製密碼。

### <a name="set-up-the-key-container"></a>設定金鑰容器

金鑰容器用來儲存金鑰。 若要設定一個︰

1. 開啟新的 PowerShell 命令提示字元。  其中一個方法是 [Windows 標誌鍵 + R]，輸入 `powershell`，然後按 Enter 鍵。
1. 下載此 Repro 以取得 Powershell ExploreAdmin 工具。

    ```powershell
    git clone https://github.com/Azure-Samples/active-directory-b2c-advanced-policies
    ```

1. 使用 ExploreAdmin 工具切換到資料夾。

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. 將 ExploreAdmin 工具匯入 Powershell。

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. 確認 `b2c_1a_TokenSigningKeyContainer` 尚未存在。  以您的租用戶名稱取代 `{tenantName}`。

    ```powershell
    Get-CpimKeyContainer -TenantId {tenantName}.onmicrosoft.com -StorageReferenceId b2c_1a_TokenSigningKeyContainer -ForceAuthenticationPrompt
    ```

    a. 登入提示出現時，請使用您在上一節中建立的管理帳戶。

    b.這是另一個 C# 主控台應用程式。 出現提示時，您必須輸入您的電話號碼，以設定 Multi-Factor Authentication。

    c. 出現提示時，變更您的密碼。

    d. **預期會出現錯誤！**  此錯誤應陳述找不到 `b2c_1a_TokenSigningKeyContainer`。  如果您已完成這些步驟而沒有任何錯誤，請略過本節的其餘部分。

1. 建立 `b2c_1a_TokenSigningKeyContainer`。  以您的租用戶名稱取代 `{tenantName}`。

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_TokenSigningKeyContainer  b2c_1a_TokenSigningKeyContainer rsa 2048 0 0
    ```

1. 建立 `b2c_1a_TokenEncryptionKeyContainer`。  以您的租用戶名稱取代 `{tenantName}`。

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com b2c_1a_TokenEncryptionKeyContainer b2c_1a_TokenEncryptionKeyContainer rsa 2048 0 0
    ```

1. 建立 `b2c_1a_FacebookSecret`。  以您的租用戶名稱取代 `{tenantName}`。

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_FacebookSecret  b2c_1a_FacebookSecret rsa 2048 0 0
    ```
