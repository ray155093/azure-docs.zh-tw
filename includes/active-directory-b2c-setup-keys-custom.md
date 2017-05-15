
### <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>將簽署和加密金鑰新增至 B2C 租用戶以供自訂原則使用

1. 瀏覽至您的 Azure AD B2C 租用戶設定中的 [身分識別體驗架構] 刀鋒視窗。
1. 選取 `Policy Keys` 以檢視您的租用戶中可用的金鑰。 如果 `B2C_1A_TokenSigningKeyContainer` 存在，請略過此金鑰。
1. 建立 `TokenSigningKeyContainer`  
 * 按一下 `+Add`
 * 選項 > `Generate`
 * 名稱 >`TokenSigningKeyContainer` 可以自動新增前置詞 B2C_1A_。
 * 金鑰類型 > `RSA`
 * 日期 - 使用預設值
 * 金鑰使用方式 > `Signature`
1. 按一下 `Create`
1. 如果存在名為 `B2C_1A_TokenEncryptionKeyContainer` 的金鑰，請略過此金鑰。
1. 建立 `TokenEncryptionKeyContainer`。
 * 選項 > `Generate`
 * 名稱 >`TokenSigningKeyContainer` 可以自動新增前置詞 B2C_1A_。
 * 金鑰類型 > `RSA`
 * 日期 - 使用預設值
 * 金鑰使用方式 > `Encryption`
1. 按一下 `Create`


[!TIP]
如果您希望提供社交識別提供者或同盟識別提供者給使用者，則後續步驟為「選擇性」。  首先從 Facebook 開始了解外部識別提供者與使用自訂原則的 Azure AD B2C。

1. 建立 `FacebookSecret`。  雖為選擇性，建議利用此步驟立即測試您的外部同盟能力。  這會建立進一步與其他識別碼提供者一起開發原則的穩固起始點
 * 按一下 `+Add`
 * 選項 > `Manual`
 * 名稱 > `FacebookSecret` 可以自動新增前置詞 B2C_1A_。
 * 祕密 > 輸入 developers.facebook.com 提供給您的 FacebookSecret。  *這不是您的 Facebook 應用程式識別碼*
 * 金鑰使用方式 > 簽章
1. 按一下 `Create` 並確認建立，請記下名稱

[!NOTE]
如果您使用 Azure AD B2C 內建原則，您通常會針對內建和自訂原則使用相同的祕密。 
