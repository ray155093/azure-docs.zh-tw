<properties
	pageTitle="使用 HTTPS 保護應用程式的自訂網域 | Microsoft Azure"
	description="了解如何設定 SSL 憑證繫結，以在 Azure App Service 中保護應用程式的自訂網域名稱。您也將了解如何從多項工具取得 SSL 憑證。"
	services="app-service"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="cephalin"/>

# 使用 HTTPS 保護應用程式的自訂網域


> [AZURE.SELECTOR]
- [在 Azure 中購買 SSL 憑證](web-sites-purchase-ssl-web-site.md)
- [使用來自其他位置的 SSL 憑證](web-sites-configure-ssl-certificate.md)


這篇文章說明如何在 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中為使用自訂網域名稱的 Web 應用程式、行動應用程式後端或 API 應用程式啟用 HTTPS。本文涵蓋僅限伺服器驗證。如果您需要相互驗證 (包括用戶端驗證)，請參閱[如何設定 App Service 的 TLS 相互驗證](app-service-web-configure-tls-mutual-auth.md)。

若要使用 HTTPS 保護擁有自訂網域名稱的應用程式，您可以為該網域名稱新增憑證。根據預設，Azure 使用單一 SSL 憑證來保護 ***.azurewebsites.net** 萬用字元網域，因此您的用戶端已經可以在 **https://*&lt;appname>*.azurewebsites.net** 存取您的應用程式。但是，如果您想使用自訂網域 (例如 **contoso.com**、**www.contoso.com** 和 ***.contoso.com**)，則預設憑證無法提供保護。此外，如同所有[萬用字元憑證](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/)，預設憑證的安全性不如使用自訂網域和該自訂網域的憑證。

>[AZURE.NOTE] 您隨時可以在 [Azure 論壇](https://azure.microsoft.com/support/forums/)上取得 Azure 專家的協助。如需更個人化的支援，請移至 [Azure 支援](https://azure.microsoft.com/support/options/)，按一下**取得支援**。

<a name="bkmk_domainname"></a>
## 您需要什麼
若要使用 HTTPS 保護您的自訂網域名稱，您要在 Azure 中將自訂 SSL 憑證繫結至該自訂網域。繫結自訂網域憑證之前，必須執行下列動作：

- **設定自訂網域** - App Service 只允許為您的應用程式中已設定的網域名稱新增憑證。如需指示，請參閱[將自訂網域名稱對應至 Azure 應用程式](web-sites-custom-domain-name.md)。
- **相應增加至基本或更高層** - 較低定價層中的 App Service 方案不支援自訂 SSL 憑證。如需指示，請參閱[在 Azure 中相應增加應用程式的規模](web-sites-scale.md)。
- **取得 SSL 憑證** - 如果您還沒有 SSL 憑證，您必須向信任的[憑證授權單位](http://en.wikipedia.org/wiki/Certificate_authority) (CA) 取得一份。憑證必須符合下列所有要求：

	- 由信任的 CA (非私人 CA 伺服器) 簽署。
	- 包含一個私密金鑰。
	- 專用於金鑰交換和匯出至 .PFX 檔案。
	- 使用至少為 2048 位元的加密。
	- 其主體名稱符合需要保護的自訂網域。若要使用一份憑證保護多個網域，您必須使用萬用字元名稱 (例如 ***.contoso.com**) 或指定 subjectAltName 值。
	- 與您的 CA 使用的所有**[中繼憑證](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)**合併。否則，您可能會遇到某些用戶端上不可重複的互通性問題。

		>[AZURE.NOTE] 取得符合所有要求的 SSL 憑證最簡單的方式是[直接在 Azure 入口網站購買](web-sites-purchase-ssl-web-site.md)。本文將說明如何手動執行這項操作，然後將它繫結至您在 App Service 中的自訂網域。
		>	
		> **橢圓曲線密碼編譯 (ECC) 憑證**可搭配 App Service 使用，但不在本文討論範圍內。請洽詢您的 CA，了解建立 ECC 憑證的確切步驟。

<a name="bkmk_getcert"></a>
## 步驟 1.取得 SSL 憑證

由於 CA 提供不同價格點的多種 SSL 憑證類型，您應該先決定要購買何種類型的 SSL 憑證。如果要保護單一網域名稱 (**www.contoso.com**)，您只需要基本憑證。如果要保護多個網域名稱 (**contoso.com**、**www.contoso.com** 與 **mail.contoso.com**)，則您需要[萬用字元憑證](http://en.wikipedia.org/wiki/Wildcard_certificate)或包含[主體替代名稱](http://en.wikipedia.org/wiki/SubjectAltName) (`subjectAltName`) 的憑證。

一旦確定要購買的 SSL 憑證，就可以將憑證簽署要求 (CSR) 提交至 CA。當您從 CA 取得所要求的憑證後，必須從該憑證產生一個 .pfx 檔案。您可以使用您選擇的工具執行這些步驟。以下是一些常用工具的指示︰

- [Certreq.exe 步驟](#bkmk_certreq) - Certreq.exe 是建立憑證要求的 Windows 公用程式。自 Windows XP/Windows Server 2000 起，它已是 Windows 的一部分。
- [IIS Manager 步驟](#bkmk_iismgr) - 如果您已知如何操作，IIS Manager 是首選工具。
- [OpenSSL 步驟](#bkmk_openssl) - [開放原始碼的跨平台工具](https://www.openssl.org)。您可以使用此工具來取得任何平台的 SSL 憑證。
- [使用 OpenSSL 的 subjectAltName 步驟](#bkmk_subjectaltname) - 取得 `subjectAltName` 憑證的步驟。

如果您想先在 App Service 中測試設定後再購買憑證，您可以產生[自我簽署憑證](https://en.wikipedia.org/wiki/Self-signed_certificate)。本教學課程提供您兩種產生方式︰

- [自我簽署憑證，Certreq.exe 步驟](#bkmk_sscertreq)
- [自我簽署憑證，OpenSSL 步驟](#bkmk_ssopenssl)

<a name="bkmk_certreq"></a>
### 使用 Certreq.exe 取得憑證

1. 建立一個檔案 (例如 **myrequest.txt**)，將下列文字複製到該檔案，並將它儲存到工作目錄。將 `<your-domain>` 預留位置取代為您的應用程式自訂網域名稱。

		[NewRequest]
		Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
		Exportable = TRUE
		KeyLength = 2048              ; Required minimum is 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		HashAlgorithm = SHA256

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

	如需 CSR 選項及其他可用選項的詳細資訊，請參閱 [Certreq 參考文件](https://technet.microsoft.com/library/dn296456.aspx)。

4. 在命令提示字元中，`CD` 到您的工作目錄，並執行下列命令來建立 CSR：

		certreq -new myrequest.txt myrequest.csr

	**myrequest.csr** 現已建立到您目前的工作目錄中。

5. 將 **myrequest.csr** 提交至 CA 以取得 SSL 憑證。您可以上傳檔案，或從文字編輯器將其內容複製到 Web 表單。

	如需 Microsoft 信任的 CA 清單，請參閱 [Microsoft 信任的根憑證計劃︰參與者][cas]。

6. 一旦 CA 回覆您憑證 (.CER) 檔案，請將其儲存到您的工作目錄。然後執行下列命令來完成擱置的 CSR。

		certreq -accept -user <certificate-name>.cer

	此命令會將完成的憑證儲存在 Windows 憑證存放區。

6. 如果 CA 使用中繼憑證，請安裝後再繼續。這些憑證通常是從 CA 個別下載而得，並且有數種格式來用於不同的網頁伺服器類型。請選取 Microsoft IIS 適用的版本。

	下載憑證之後，請在 Windows 檔案總管中於各憑證上按一下滑鼠右鍵，然後選取 [安裝憑證]。使用 [憑證匯入精靈] 中的預設值，並持續選取 [下一步]，直到匯入完成。

7. 若要從憑證存放區匯出 SSL 憑證，請按 `Win`+`R` 並執行 **certmgr.msc** 以啟動憑證管理員。選取 [個人] > [憑證]。在 [簽發給] 資料行中，您應該會看到一個含有您自訂網域名稱的項目，[簽發者] 資料行中則是您用來產生憑證的 CA。

	![在這裡插入憑證管理員的影像][certmgr]

9. 在憑證上按一下滑鼠右鍵，並選取 [所有工作] > [匯出]。在 [憑證匯出精靈] 中，按 [下一步]，選取 [是，匯出私密金鑰]，然後再按一次 [下一步]。

	![匯出私密金鑰][certwiz1]

10. 選取 [個人資訊交換 - PKCS #12]、[如果可以則包含憑證路徑中的所有憑證] 和 [匯出所有延伸內容]。然後按 [下一步]。

	![包括所有憑證和延伸內容][certwiz2]

11. 選取 [密碼]，然後輸入並確認密碼。按 [下一步]。

	![指定密碼][certwiz3]

12. 為匯出的憑證提供路徑和檔名 (副檔名為 **.pfx**)。按一下 [下一步] 來完成。

	![提供檔案路徑][certwiz4]

您現在可以準備將匯出的 PFX 檔案上傳到 App Service。請參閱[步驟 2. 上傳和繫結自訂的 SSL 憑證](#bkmk_configuressl)。

<a name="bkmk_iismgr"></a>
### 使用 IIS 管理員取得憑證

1. 使用 IIS Manager 產生 CSR 以傳送至 CA。如需產生 CSR 的詳細資訊，請參閱[要求網際網路伺服器憑證 (IIS 7)][iiscsr]。

3. 將您的 CSR 提交至 CA 以取得 SSL 憑證。如需 Microsoft 信任的 CA 清單，請參閱 [Microsoft 信任的根憑證計劃︰參與者][cas]。


3. 使用 CA 回傳給您的憑證完成 CSR。如需完成 CSR 的詳細資訊，請參閱[安裝網際網路伺服器憑證 (IIS 7)][installcertiis]。

4. 如果 CA 使用中繼憑證，請安裝後再繼續。這些憑證通常是從 CA 個別下載而得，並且有數種格式來用於不同的網頁伺服器類型。請選取 Microsoft IIS 適用的版本。

	下載憑證之後，請在 Windows 檔案總管中於各憑證上按一下滑鼠右鍵，然後選取 [安裝憑證]。使用 [憑證匯入精靈] 中的預設值，並持續選取 [下一步]，直到匯入完成。

4. 從 IIS Manager 匯出 SSL 憑證。如需匯出憑證的詳細資訊，請參閱[匯出伺服器憑證 (IIS 7)][exportcertiis]。

	>[AZURE.IMPORTANT] 在 [憑證匯出精靈] 中，確定您選取 [是，匯出私密金鑰]。
	>
	>![匯出私密金鑰][certwiz1]
	>
	> 另請選取 [個人資訊交換 - PKCS #12]、[如果可以則包含憑證路徑中的所有憑證] 和 [匯出所有延伸內容]。
	>
	>![包括所有憑證和延伸內容][certwiz2]

您現在可以準備將匯出的 PFX 檔案上傳到 App Service。請參閱[步驟 2. 上傳和繫結自訂的 SSL 憑證](#bkmk_configuressl)。

<a name="bkmk_openssl"></a>
### 使用 OpenSSL 取得憑證

1. 在命令列終端機中，`CD` 到工作目錄，執行下列命令以產生私密金鑰和 CSR︰

		openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. 當系統提示時，輸入適當資訊。例如：

 		Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []:

	完成時，您的工作目錄應該有兩個檔案︰**myserver.key** 和 **server.csr**。**server.csr** 包含 CSR，而您稍後需要 **myserver.key**。

3. 將您的 CSR 提交至 CA 以取得 SSL 憑證。如需 Microsoft 信任的 CA 清單，請參閱 [Microsoft 信任的根憑證計劃︰參與者][cas]。


4. 一旦 CA 傳送要求的憑證給您，請將其儲存到您工作目錄中名為 **myserver.crt** 的檔案。如果您的 CA 以文字格式提供憑證，只要在文字編輯器中將該內容複製到 **myserver.crt** 並儲存。檔案看起來應該如下所示：

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

5. 在命令列終端機中，執行下列命令以從 **myserver.key** 和 **myserver.crt** 匯出 **myserver.pfx**：

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	當系統提示時，請定義保護該 .pfx 檔案的密碼。

	> [AZURE.NOTE] 如果您的 CA 使用中繼憑證，您必須使用 `-certfile` 參數包含它們。這些憑證通常是從 CA 個別下載而得，並且有數種格式來用於不同的網頁伺服器類型。選取副檔名為 `.pem` 的版本。
	>
	> 您的 `openssl -export` 命令看起來應該與下列範例類似，它會建立一個 .pfx 檔案，其中包含來自 **intermediate-cets.pem** 檔案的中繼憑證︰
	>  
	> `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

您現在可以準備將匯出的 PFX 檔案上傳到 App Service。請參閱[步驟 2. 上傳和繫結自訂的 SSL 憑證](#bkmk_configuressl)。

<a name="bkmk_subjectaltname"></a>
### 使用 OpenSSL 取得 SubjectAltName 憑證

1. 建立一個名為 **sancert.cnf** 的檔案，將下列文字複製到該檔案，並將它儲存到工作目錄：

		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default =
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default =
		localityName = Locality Name (eg, city)
		localityName_default =
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  =
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----

	在開頭為 `subjectAltName` 的一行，將該值取代為您想要保護的所有網域名稱 (除了 `commonName` 以外)。例如：

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	您不需要變更任何其他欄位，包括 `commonName`。在接下來的步驟中，系統會提示您指定它們。

1. 在命令列終端機中，`CD` 到您的工作目錄，並執行下列命令：

		openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. 當系統提示時，輸入適當資訊。例如：

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com

	一旦完成，您的工作目錄應該有兩個檔案︰**myserver.key** 和 **server.csr**。**server.csr** 包含 CSR，而您稍後需要 **myserver.key**。

3. 將您的 CSR 提交至 CA 以取得 SSL 憑證。如需 Microsoft 信任的 CA 清單，請參閱 [Microsoft 信任的根憑證計劃︰參與者][cas]。


4. 一旦 CA 傳送要求的憑證給您，請將其儲存到您名為 **myserver.crt** 的檔案。如果您的 CA 以文字格式提供憑證，只要在文字編輯器中將該內容複製到 **myserver.crt** 並儲存。檔案看起來應該如下所示：

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

5. 在命令列終端機中，執行下列命令以從 **myserver.key** 和 **myserver.crt** 匯出 **myserver.pfx**：

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	當系統提示時，請定義保護該 .pfx 檔案的密碼。

	> [AZURE.NOTE] 如果您的 CA 使用中繼憑證，您必須使用 `-certfile` 參數包含它們。這些憑證通常是從 CA 個別下載而得，並且有數種格式來用於不同的網頁伺服器類型。選取副檔名為 `.pem` 的版本。
	>
	> 您的 `openssl -export` 命令看起來應該與下列範例類似，它會建立一個 .pfx 檔案，其中包含來自 **intermediate-cets.pem** 檔案的中繼憑證︰
	>  
	> `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

您現在可以準備將匯出的 PFX 檔案上傳到 App Service。請參閱[步驟 2. 上傳和繫結自訂的 SSL 憑證](#bkmk_configuressl)。

<a name="bkmk_sscertreq"></a>
### 使用 Certreq.exe 產生自我簽署憑證 ###

>[AZURE.IMPORTANT] 自我簽署憑證僅供測試用。大部分的瀏覽器在瀏覽受自我簽署憑證保護的網站時會傳回錯誤。部分瀏覽器甚至會拒絕瀏覽網站。

1. 建立一個文字檔 (例如 **mycert.txt**)，將下列文字複製到該檔案，並將該檔案儲存到工作目錄。將 `<your-domain>` 預留位置取代為您的應用程式自訂網域名稱。

		[NewRequest]
		Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
		Exportable = TRUE
		KeyLength = 2048              ; KeyLength can be 2048, 4096, 8192, or 16384 (required minimum is 2048)
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		HashAlgorithm = SHA256
		RequestType = Cert            ; Self-signed certificate
		ValidityPeriod = Years
		ValidityPeriodUnits = 1

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

	重要的參數是 `RequestType = Cert`，其可指定自我簽署憑證。如需 CSR 選項及其他可用選項的詳細資訊，請參閱 [Certreq 參考文件](https://technet.microsoft.com/library/dn296456.aspx)。

4. 在命令提示字元中，`CD` 到您的工作目錄，並執行下列命令：

		certreq -new mycert.txt mycert.crt
	
	新的自我簽署憑證現在會安裝在憑證存放區中。

7. 若要從憑證存放區匯出憑證，請按 `Win`+`R` 並執行 **certmgr.msc** 以啟動憑證管理員。選取 [個人] > [憑證]。在 [簽發給] 資料行中，您應該會看到一個含有您自訂網域名稱的項目，[簽發者] 資料行中則是您用來產生憑證的 CA。

	![在這裡插入憑證管理員的影像][certmgr]

9. 在憑證上按一下滑鼠右鍵，並選取 [所有工作] > [匯出]。在 [憑證匯出精靈] 中，按 [下一步]，選取 [是，匯出私密金鑰]，然後再按一次 [下一步]。

	![匯出私密金鑰][certwiz1]

10. 選取 [個人資訊交換 - PKCS #12]、[如果可以則包含憑證路徑中的所有憑證] 和 [匯出所有延伸內容]。然後按 [下一步]。

	![包括所有憑證和延伸內容][certwiz2]

11. 選取 [密碼]，然後輸入並確認密碼。按 [下一步]。

	![指定密碼][certwiz3]

12. 為匯出的憑證提供路徑和檔名 (副檔名為 **.pfx**)。按一下 [下一步] 來完成。

	![提供檔案路徑][certwiz4]

您現在可以準備將匯出的 PFX 檔案上傳到 App Service。請參閱[步驟 2. 上傳和繫結自訂的 SSL 憑證](#bkmk_configuressl)。

<a name="bkmk_ssopenssl"></a>
###使用 OpenSSL 產生自我簽署憑證 ###

>[AZURE.IMPORTANT] 自我簽署憑證僅供測試用。大部分的瀏覽器在瀏覽受自我簽署憑證保護的網站時會傳回錯誤。部分瀏覽器甚至會拒絕瀏覽網站。

1. 建立名為 **serverauth.cnf** 的文字檔，將下列內容複製到該檔案，並儲存在工作目錄中︰

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your app's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

2. 在命令列終端機中，`CD` 到您的工作目錄，並執行下列命令：

		openssl req -sha256 -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	此命令會依據 **serverauth.cnf** 中的設定來建立兩個檔案︰**myserver.crt** (自我簽署憑證) 和 **myserver.key** (私密金鑰)。

3. 執行下列命令以將憑證匯出至 .pfx 檔案：

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	當系統提示時，請定義保護該 .pfx 檔案的密碼。

您現在可以準備將匯出的 PFX 檔案上傳到 App Service。請參閱[步驟 2. 上傳和繫結自訂的 SSL 憑證](#bkmk_configuressl)。

<a name="bkmk_configuressl"></a>
## 步驟 2.上傳和繫結自訂的 SSL 憑證

繼續之前，請檢閱[您需要什麼](#bkmk_domainname)一節來確認︰

- 您有對應至 Azure 應用程式的自訂網域，
- 您的應用程式是以**基本**或更高層的方案執行，以及
- 您有來自 CA 的自訂網域 SSL 憑證。
 
1.	在 [Azure 入口網站](https://portal.azure.com)中，移至應用程式的 [自訂網域及 SSL] 刀鋒視窗。

7.	按一下 [詳細資訊] > [上傳憑證]。

	![](./media/web-sites-configure-ssl-certificate/sslupload.png)

8.	選取您在[步驟 1](#bkmk_getcert) 匯出的.pfx 檔案，並指定您之前建立的密碼。接著按一下 [儲存] 以上傳憑證。您現在應該會在 [自訂網域及 SSL] 刀鋒視窗中看到您上傳的憑證。

	![](./media/web-sites-configure-ssl-certificate/sslcertview.png)

9. 在 [SSL 繫結] 區段中，選取網域名稱，SSL 憑證就會繫結在一起。您也可以選擇使用 SNI SSL 或以 IP 為主的 SSL。

	![](./media/web-sites-configure-ssl-certificate/sslbindcert.png)

	* **以 IP 為主的 SSL** 會將含有網域名稱和應用程式專用公用 IP 位址的憑證繫結至網域名稱。它是傳統的 SSL 繫結方法，App Service 會為繫結建立專用的 IP 位址。

	* [**SNI SSL**](https://en.wikipedia.org/wiki/Server_Name_Indication) 可將多個憑證繫結至多個網域。現今大部分的瀏覽器 (包括 Internet Explorer、Chrome、Firefox 和 Safari) 都支援 SNI SSL，但較舊的瀏覽器可能不支援。
 
10. 按一下 [儲存] 來完成。

## 步驟 3.變更網域名稱對應 (僅限以 IP 為主的 SSL)

如果您只使用 **SNI SSL** 繫結，請略過本節。在指派給您的應用程式的現有共用 IP 位址上，多個 **SNI SSL** 繫結可以共同運作。不過，如果您建立**以 IP 為主的 SSL** 繫結，App Service 會為繫結建立專用的 IP 位址，因為**以 IP 為主的 SSL** 需要一個專用 IP 位址。因為這個專用的 IP 位址，在以下狀況下，您必須進一步設定您的應用程式︰

- 您[使用 A 記錄將您的自訂網域對應至 Azure 應用程式](web-sites-custom-domain-name.md#a)，而且您剛新增一個**以 IP 為主的 SSL** 繫結。在此案例中，您需要遵循下列步驟來重新對應現有的 A 記錄，以指向專用的 IP 位址︰

	1. 設定以 IP 為主的 SSL 繫結後，請在您應用程式的 [設定] > [內容] 刀鋒視窗中尋找新的 IP 位址 ([使用外部網域] 刀鋒視窗中顯示的虛擬 IP 位址可能不是最新的)：
    
	    ![虛擬 IP 位址](./media/web-sites-configure-ssl-certificate/staticip.png)

	2. [將自訂網域名稱的 A 記錄重新對應至這個新 IP 位址](web-sites-custom-domain-name.md#a)。

- 您的應用程式中已經有一或多個 **SNI SSL** 繫結，而且您剛新增一個**以 IP 為主的 SSL** 繫結。完成繫結後，您的 *&lt;appname>*.azurewebsites.net 網域名稱就會指向新的 IP 位址。因此，[自訂網域中與 *&lt;appname>*.azurewebsites.net 的任何現有 CNAME 對應](web-sites-custom-domain-name.md#cname) (包括 **SNI SSL** 保護的) 也會接收只為**以 IP 為主的 SSL** 建立的新位址上的流量。在此案例中，您需要遵循下列步驟來將 **SNI SSL** 流量送回原始的共用 IP 位址︰

	1. 找出您應用程式的[自訂網域的所有 CNAME 對應](web-sites-custom-domain-name.md#cname)中含有 **SNI SSL** 繫結的部分。

	2. 將每個 CNAME 記錄重新對應至 **sni.**&lt;appname>.azurewebsites.net 而非 &lt;appname>.azurewebsites.net。

## 步驟 4.測試自訂網域的 HTTPS

現在只剩下確定 HTTPS 是否能用在您的自訂網域。在各種瀏覽器中，瀏覽至 `https://<your.custom.domain>` 以查看它是否能提供您的應用程式。

- 如果您的應用程式出現憑證驗證錯誤，您可能使用了自我簽署憑證。

- 如果不是，在您匯出 .pfx 憑證時可能遺漏了中繼憑證。返回[您需要什麼](#bkmk_domainname)，確認您的 CSR 符合 App Service 的所有要求。

<a name="bkmk_enforce"></a>
## 在應用程式上強制使用 HTTPS

如果您仍然想允許應用程式的 HTTP 存取，請略過此步驟。App Service *不*強制使用 HTTPS，因此訪客仍然可以使用 HTTP 存取您的應用程式。如果您想要強制應用程式使用 HTTPS，您可以在應用程式的 `web.config` 檔案中定義重寫規則。無論應用程式語言架構為何，每個 App Service 應用程式都有這個檔案。

> [AZURE.NOTE] 有語言特定的要求重新導向。ASP.NET MVC 可使用 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 篩選，而非 `web.config` 中的重寫規則 (請參閱[將安全的 ASP.NET MVC 5 應用程式部署到 Web 應用程式](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md))。

請遵循下列步驟：

1. 瀏覽至您應用程式的 Kudu 偵錯主控台。它的位址是 `https://<appname>.scm.azurewebsites.net/DebugConsole`。

2. 在偵錯主控台中，切換至 `D:\home\site\wwwroot`。

3. 按一下鉛筆按鈕以開啟 [`web.config`]。

	![](./media/web-sites-configure-ssl-certificate/openwebconfig.png)

	如果您使用 Visual Studio 或 Git 部署應用程式，App Service 會在應用程式根目錄中自動為您的 .NET、PHP、Node.js 或 Python 應用程式產生適當的 `web.config`。如果 `web.config` 不存在，請在網頁命令提示字元執行 `touch web.config` 來建立它。或者，您可以在本機專案中建立，並重新部署您的程式碼。

4. 如果您必須建立 `web.config`，請將下列程式碼複製到其中並將它儲存。如果您開啟現有的 web.config，您只需要將整個 `<rule>` 標記複製到您的 `web.config` 的 `configuration/system.webServer/rewrite/rules` 元素。

		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <rewrite>
		      <rules>
			    <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
		        <rule name="Force HTTPS" enabled="true">
		          <match url="(.*)" ignoreCase="false" />
		          <conditions>
		            <add input="{HTTPS}" pattern="off" />
		          </conditions>
		          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
		        </rule>
				<!-- END rule TAG FOR HTTPS REDIRECT -->
		      </rules>
		    </rewrite>
          </system.webServer>
		</configuration>

	每當使用者要求使用 HTTP 的頁面時，此規則就會將 HTTP 301 (永久重新導向) 傳回 HTTPS 通訊協定。它會從 http://contoso.com 重新導向到 https://contoso.com。

	>[AZURE.IMPORTANT] 如果您的 `web.config` 中有其他 `<rule>` 標記，請將複製的 `<rule>` 標記放在其他 `<rule>` 標記之前。

4. 在 Kudu 偵錯主控台儲存檔案。它應該會立即生效，並將所有要求重新導向至 HTTPS。

如需 IIS URL Rewrite 模組的詳細資訊，請參閱 [URL Rewrite](http://www.iis.net/downloads/microsoft/url-rewrite) (英文) 文件。

## 其他資源 ##
- [Microsoft Azure 信任中心](/support/trust-center/security/)
- [Azure 網站中解除鎖定的設定選項](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [啟用診斷記錄](web-sites-enable-diagnostic-log.md)
- [在 Azure App Service 中設定 Web 應用程式](web-sites-configure.md)
- [Azure 管理入口網站](https://manage.windowsazure.com)

>[AZURE.NOTE] 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期的入門應用程式。不需要信用卡；沒有承諾。

[customdomain]: web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://social.technet.microsoft.com/wiki/contents/articles/31634.microsoft-trusted-root-certificate-program-participants-v-2016-april.aspx
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/web-sites-configure-ssl-certificate/staticip.png
[website]: ./media/web-sites-configure-ssl-certificate/sslwebsite.png
[scale]: ./media/web-sites-configure-ssl-certificate/sslscale.png
[standard]: ./media/web-sites-configure-ssl-certificate/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/web-sites-configure-ssl-certificate/sslconfig.png
[uploadcert]: ./media/web-sites-configure-ssl-certificate/ssluploadcert.png
[uploadcertdlg]: ./media/web-sites-configure-ssl-certificate/ssluploaddlg.png
[sslbindings]: ./media/web-sites-configure-ssl-certificate/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/web-sites-configure-ssl-certificate/waws-certmgr.png
[certwiz1]: ./media/web-sites-configure-ssl-certificate/waws-certwiz1.png
[certwiz2]: ./media/web-sites-configure-ssl-certificate/waws-certwiz2.png
[certwiz3]: ./media/web-sites-configure-ssl-certificate/waws-certwiz3.png
[certwiz4]: ./media/web-sites-configure-ssl-certificate/waws-certwiz4.png

<!---HONumber=AcomDC_0810_2016---->