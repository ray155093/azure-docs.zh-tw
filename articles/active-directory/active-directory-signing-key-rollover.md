<properties
	pageTitle="Azure AD 中的簽署金鑰變換 | Microsoft Azure"
	description="本文討論 Azure Active directory 的簽署金鑰變換最佳做法"
	services="active-directory"
	documentationCenter=".net"
	authors="gsacavdm"
	manager="krassk"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="gsacavdm"/>

# Azure Active Directory 中的簽署金鑰變換

本主題討論 Azure Active Directory (Azure AD) 中用來簽署安全性權杖的公開金鑰須知事項。請務必注意這些金鑰會定期變換，且在緊急狀況下可以立即變換。所有使用 Azure AD 的應用程式都應該能夠以程式設計方式處理金鑰變換程序。請繼續閱讀以了解金鑰的運作方式、如何評估變換對應用程式的影響，以及必要時如何更新應用程式來處理金鑰變換。

> [AZURE.IMPORTANT] 下一次的簽署金鑰變換是排定在 2016 年 8 月 15 日，將 **不會**影響用戶端應用程式、從資源庫新增的應用程式 (包括自訂)、透過應用程式 Proxy 發佈的應用程式或 B2C 租用戶中的應用程式。

## Azure AD 中簽署金鑰的概觀

Azure AD 會使用根據業界標準所建置的公開金鑰加密技術，建立 Azure AD 本身和使用 Azure AD 的應用程式之間的信任。實際上，其運作方式如下：Azure AD 使用由公開和私密金鑰組所組成的簽署金鑰。當使用者登入使用 Azure AD 來進行驗證的應用程式時，Azure AD 會建立包含使用者相關資訊的安全性權杖。此權杖會先由 Azure AD 使用其私密金鑰進行簽署，再傳送回到應用程式。若要確認該權杖有效且確實來自 Azure AD，應用程式必須使用由 Azure AD 公開，且包含在租用戶的 [OpenID Connect 探索文件](http://openid.net/specs/openid-connect-discovery-1_0.html) 或 SAML/WS-Fed [同盟中繼資料文件](active-directory-federation-metadata.md)中的公開金鑰來驗證權杖的簽章。

基於安全考量，Azure AD 的簽署金鑰會定期變換，且在緊急情況下可以立即變換。任何與 Azure AD 整合的應用程式均應準備好處理金鑰變換事件，不論其可能發生頻率為何。如果沒有，應用程式又嘗試使用過期的金鑰來驗證權杖上的簽章，登入要求便會失敗。

OpenID Connect 探索文件和同盟中繼資料文件中永遠有一個以上的有效金鑰可用。應用程式應該要已準備好使用文件中指定的任何金鑰，因為某個金鑰可能很快就得要替換，而另一個可能會取代它，依此類推。

## 如何評估您的應用程式是否會受到影響，以及如何因應

應用程式處理金鑰變換的方式取決於各種變數，例如應用程式類型或使用的身分識別通訊協定和程式庫。下列各節會評估最常見的應用程式類型是否會受到金鑰變換所影響，並提供如何更新應用程式以支援自動變換或手動更新金鑰的指引。

* [使用 .NET OWIN OpenID Connect、WS-Fed 或 WindowsAzureActiveDirectoryBearerAuthentication 中介軟體的 Web 應用程式/API](#owin)
* [使用 .NET Core OpenID Connect 或 JwtBearerAuthentication 中介軟體的 Web 應用程式/API](#owincore)
* [使用 Node.js passport-azure-ad 模組的 Web 應用程式/API](#passport)
* [使用 Visual Studio 2015 建立的 Web 應用程式/API](#vs2015)
* [使用 Visual Studio 2013 建立的 Web 應用程式](#vs2013)
* [使用 Visual Studio 2013 建立的 Web API](#vs2013_webapi)
* [使用 Visual Studio 2012 建立的 Web 應用程式](#vs2012)
* [使用 Visual Studio 2010、2008 或使用 Windows Identity Foundation 建立的 Web 應用程式](#vs2010)
* [使用任何其他程式庫或手動實作任何支援的通訊協定的 Web 應用程式/API](#other)

### <a name="owin"></a>使用 .NET OWIN OpenID Connect、WS-Fed 或 WindowsAzureActiveDirectoryBearerAuthentication 中介軟體的 Web 應用程式/API

如果您的應用程式使用 .NET OWIN OpenID Connect、WS-Fed 或 WindowsAzureActiveDirectoryBearerAuthentication 中介軟體，則它已經具有必要的邏輯可自動處理金鑰變換。

您可以應用程式的 Startup.cs 或 Startup.Auth.cs 中尋找下列任何程式碼片段，以確認您的應用程式使用任何這些項目

```
app.UseOpenIdConnectAuthentication(
	 new OpenIdConnectAuthenticationOptions
	 {
		 // ...
	 });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
	 // ...
 	});
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
	 new WindowsAzureActiveDirectoryBearerAuthenticationOptions
	 {
	 // ...
	 });
```

### <a name="owincore"></a>使用 .NET OWIN OpenID Connect 或 JwtBearerAuthentication 中介軟體的 Web 應用程式/API

如果您的應用程式使用 .NET Core OpenID Connect 或 JwtBearerAuthentication 中介軟體，則它已經具有必要的邏輯可自動處理金鑰變換。

您可以應用程式的 Startup.cs 或 Startup.Auth.cs 中尋找下列任何程式碼片段，以確認您的應用程式使用任何這些項目

```
app.UseOpenIdConnectAuthentication(
	 new OpenIdConnectAuthenticationOptions
	 {
		 // ...
	 });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
	 // ...
 	});
```

### <a name="passport"></a>使用 Node.js passport-ad 模組的 Web 應用程式/API

如果您的應用程式使用 Node.js passport-ad 模組，則它已經具有必要的邏輯可自動處理金鑰變換。

您可以在應用程式的 app.js 中搜尋下列程式碼片段，以確認您的應用程式使用 passport-ad

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
	//...
));
```

### <a name="vs2015"></a>使用 Visual Studio 2015 建立的 Web 應用程式/API

如果應用程式是使用 Visual Studio 2015 中的 Web 應用程式範本所建置，而且您選取了 [變更驗證] 功能表中的 [工作和學校帳戶]，則它已經具有必要的邏輯可自動處理金鑰變換。這個內嵌在 OWIN OpenID Connect 中介軟體中的邏輯會從 OpenID Connect 探索文件擷取並快取金鑰，還會定期重新整理金鑰。

如果您是以手動方式在方案中加入驗證，應用程式可能不會有所需的金鑰變換邏輯。您必須自行撰寫，或依照[使用任何其他程式庫或手動實作任何支援的通訊協定的 Web 應用程式/API](#other) 中的步驟進行。

### <a name="vs2013"></a>使用 Visual Studio 2013 建立的 Web 應用程式

如果應用程式是使用 Visual Studio 2013 中的 Web 應用程式範本所建置，而且您選取了 [變更驗證] 功能表中的 [組織帳戶]，則它已具有必要的邏輯可自動處理金鑰變換。此邏輯會將組織的唯一識別碼和簽署金鑰資訊儲存在兩個與專案相關聯的資料庫資料表中。您可以在專案的 Web.config 檔案中找到資料庫的連接字串。

如果您是以手動方式在方案中加入驗證，應用程式可能不會有所需的金鑰變換邏輯。您必須自行撰寫，或依照[使用任何其他程式庫或手動實作任何支援的通訊協定的 Web 應用程式/API](#other) 中的步驟進行。

下列步驟將協助您確認應用程式中的邏輯能正常運作。

1. 在 Visual Studio 2013 中開啟方案，然後按一下右側視窗上的 [伺服器總管] 索引標籤。
2. 依序展開 [資料連線]、[DefaultConnection] 和 [資料表]。找出 [IssuingAuthorityKeys] 資料表並以滑鼠右鍵按一下，然後按一下 [顯示資料表資料]。
3. [IssuingAuthorityKeys] 資料表中會有至少一個對應至金鑰指紋值的資料列。刪除資料表中的任何資料列。
4. 以滑鼠右鍵按一下 [租用戶] 資料表，然後按一下 [顯示資料表資料]。
5. [租用戶] 資料表中會有至少一個對應至唯一目錄租用戶識別碼的資料列。刪除資料表中的任何資料列。如果您未刪除 [租用戶] 資料表和 [IssuingAuthorityKeys] 資料表中的資料列，您會在執行階段收到錯誤。
6. 建置並執行應用程式。在登入帳戶之後，即可停止應用程式。
7. 返回 [伺服器總管]，並查看 [IssuingAuthorityKeys] 和 [租用戶] 資料表中的值。您將會發現，它們已自動重新填入同盟中繼資料文件中的適當資訊。

### <a name="vs2013"></a>使用 Visual Studio 2013 建立的 Web API

如果您使用 Web API 範本在 Visual Studio 2013 中建置了 Web API 應用程式，然後選取了 [變更驗證] 功能表中的 [組織帳戶]，則應用程式已擁有所需的邏輯。如果您以手動方式設定了驗證，請依照下列指示來了解如何設定 Web API 以自動更新其金鑰資訊。

下列程式碼片段示範如何從同盟中繼資料文件取得最新的金鑰，然後使用 [JWT 權杖處理常式](https://msdn.microsoft.com/library/dn205065.aspx)來驗證權杖。此程式碼片段假設您將會使用自己的快取機制來保留金鑰，以驗證日後來自 Azure AD 的權杖，不論它位於資料庫、組態檔或其他位置。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>使用 Visual Studio 2012 建立的 Web 應用程式

如果應用程式是在 Visual Studio 2012 中建置的，您大概是使用身分識別與存取工具來設定應用程式。您也可能是使用[驗證簽發者名稱登錄 (VINR)](https://msdn.microsoft.com/library/dn205067.aspx)。VINR 負責維護信任的識別提供者 (Azure AD) 的相關資訊以及用來驗證其所簽發之權杖的金鑰。VINR 也可透過下載與目錄相關聯的最新同盟中繼資料文件、使用最新文件檢查組態是否過期，以及視需要讓應用程式更新為使用新金鑰，讓您輕鬆地自動更新 Web.config 檔案中儲存的金鑰資訊。

如果您是使用 Microsoft 所提供的任何程式碼範例或逐步解說文件建立應用程式，則專案中已含有金鑰變換邏輯。您會發現專案中已存在下列程式碼。如果應用程式還沒有此邏輯，請遵循下列步驟，以新增此邏輯並確認它能正常運作。

1. 在 [方案總管] 中，新增適當專案的 **System.IdentityModel** 組件參考。
2. 開啟 **Global.asax.cs** 檔案，並新增下列 using 指示詞：
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. 在 **Global.asax.cs** 檔案中新增下列方法：
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. 在 **Global.asax.cs** 的 **Application\_Start()** 方法中，叫用 **RefreshValidationSettings()** 方法，如下所示︰
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

在遵循這些步驟之後，應用程式的 Web.config 將會以同盟中繼資料文件中的最新資訊進行更新，其中也包括最新的金鑰。此更新會在每次 IIS 回收應用程式集區時進行；依預設，IIS 設定為每隔 29 小時回收應用程式一次。

請遵循下列步驟來確認金鑰變換邏輯是否能運作。

1. 在確認應用程式是使用上述程式碼之後，開啟 **Web.config** 檔案並瀏覽至 **<issuerNameRegistry>** 區塊，具體而言請尋找下列幾行︰
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. 在 **<add thumbprint=””>** 設定中，將任何字元替換為其他字元以變更指紋值。儲存 **Web.config** 檔案。

3. 建置應用程式，然後加以執行。如果您可以完成登入程序，則應用程式已從目錄的同盟中繼資料文件下載所需資訊，而成功地更新金鑰。如果您在登入時發生問題，請確定應用程式的變更是否正確，方法是閱讀[使用 Azure AD 為 Web 應用程式新增登入](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect)主題，或是下載並檢查下列程式碼範例︰[Azure Active Directory 的多租用戶雲端應用程式](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b)。


### <a name="vs2010"></a>使用 Visual Studio 2008 或 2010 和 Windows Identity Foundation (WIF) v1.0 for .NET 3.5 建立的 Web 應用程式

如果您在 WIF v1.0 上建置應用程式，則沒有提供相關機制來將應用程式的組態自動重新整理為使用新的金鑰。若要更新金鑰，最簡單的方法是使用 WIF SDK 中內含的 FedUtil 工具，它可以擷取最新的中繼資料文件並更新組態。以下包含其相關指示。或者，您可以執行下列其中一項︰

- 遵循下面的＜手動擷取最新的金鑰並更新應用程式＞一節中的指示進行，並撰寫邏輯以程式設計方式執行步驟。
- 將應用程式更新至 .NET 4.5，其包含位於「系統」命名空間中的 WIF 的最新版本。然後，您可以使用[驗證簽發者名稱登錄 (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) 來執行應用程式組態的自動更新。


1. 確認 Visual Studio 2008 或 2010 的開發電腦上已安裝 WIF v1.0 SDK。如果您還未安裝，則可以[從這裡下載](https://www.microsoft.com/zh-TW/download/details.aspx?id=4451)。
2. 在 Visual Studio 中開啟方案，然後以滑鼠右鍵按一下適用的專案，並選取 [更新同盟中繼資料]。如果無法使用此選項，則表示尚未安裝 FedUtil 和/或 WIF v1.0 SDK。
3. 在提示中選取 [更新]，開始更新同盟中繼資料。如果您可以存取裝載應用程式的伺服器環境，則可以選擇性地使用 FedUtil 的[自動中繼資料更新排程器](https://msdn.microsoft.com/library/ee517272.aspx)。
4. 按一下 [完成] 以完成更新程序。

### <a name="other"></a>使用任何其他程式庫或手動實作任何支援的通訊協定的 Web 應用程式/API。

如果您使用其他程式庫，或手動實作任何支援的通訊協定，您必須檢閱文件庫或您的實作，以確保從 OpenID Connect 探索文件或同盟中繼資料文件擷取金鑰。檢查的方法之一是在您的程式碼或程式庫的程式碼中，搜尋是否有任何呼叫 OpenID 探索文件或同盟中繼資料文件的情況。

如果金鑰儲存在某處或硬式編碼在應用程式中，您可以手動擷取金鑰，並視情況來更新。**強烈建議您增強應用程式來支援自動變換**時使用本文描述的任何方法，以免未來如果 Azure AD 提高變換頻率或臨時需要緊急變換時，造成運作中斷和超出負荷。

若要手動從 OpenID 探索文件中擷取最新的金鑰︰

1. 在 Web 瀏覽器中，前往 `https://login.microsoftonline.com/your_directory_name/.well-known/openid-configuration`。您會看到 OpenID Connect 探索文件的內容。如需這份文件的詳細資訊，請參閱 [OpenID 探索文件規格](http://openid.net/specs/openid-connect-discovery-1_0.html)。
2. 複製 jwks\_uri 值中的連結
3. 在瀏覽器中開啟新索引標籤，並移至您剛才複製的 URL。您會看到 JSON Web 金鑰集文件的內容。
4. 為了將應用程式更新為使用新的金鑰，請找出每個 **x5x** 項目，然後複製每個項目的值。例如：
```
keys: [
	{
		kty: "RSA",
		use: "sig",
		kid: "MnC_VZcATfM5pOYiJHMba9goEKY",
		x5t: "MnC_VZcATfM5pOYiJHMba9goEKY",
		n: "vIqz-4-ER_vNW...ixLUQ",
		e: "AQAB",
		x5c: [
			"MIIC4jCCAcqgAw...dhXsIIKvJQ=="
		]
	},
```
5. 在複製 **<X509Certificate>** 項目值之後，開啟純文字編輯器，然後貼上值。務必要移除任何尾端空白字元，然後以 **.cer** 副檔名儲存檔案。

若要手動從同盟中繼資料文件中擷取最新的金鑰︰

1. 在 Web 瀏覽器中，前往 `https://login.microsoftonline.com/your_directory_name/federationmetadata/2007-06/federationmetadata.xml`。您將會看到同盟中繼資料 XML 文件的內容。如需這份文件的詳細資訊，請參閱[同盟中繼資料](active-directory-federation-metadata.md)主題。
2. 為了將應用程式更新為使用新的金鑰，請找出每個 **<RoleDescriptor>** 區塊，然後複製每個區塊的 **<X509Certificate>** 項目值。例如：
```
<RoleDescriptor xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType">
      <KeyDescriptor use="signing">
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
                <X509Data>
                    <X509Certificate>MIIDPjC…BcXWLAIarZ</X509Certificate>
```
3. 在複製 **<X509Certificate>** 項目值之後，開啟純文字編輯器，然後貼上值。務必要移除任何尾端空白字元，然後以 **.cer** 副檔名儲存檔案。

您剛剛建立了 X509 憑證以做為 Azure AD 的公開金鑰。透過使用憑證的詳細資料，例如其指紋和到期日，您可以使用手動方式或以程式設計方式檢查應用程式目前使用的憑證和指紋是否有效。

<!---HONumber=AcomDC_0706_2016-->