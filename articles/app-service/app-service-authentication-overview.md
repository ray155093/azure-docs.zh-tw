<properties
	pageTitle="Azure App Service 中的驗證和授權 | Microsoft Azure"
	description="Azure App Service 的驗證/授權功能的概念性參考和概觀"
	services="app-service"
	documentationCenter=""
	authors="mattchenderson"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="04/25/2016"
	ms.author="mahender"/>
    
# Azure App Service 中的驗證與授權

## 什麼是 App Service 驗證 / 授權？

App Service 驗證 / 授權是一項功能，可讓您的應用程式登入使用者，而不需在 app 後端進行任何程式碼變更。它提供簡單的方法來保護您的應用程式，以及使用每位使用者的資料。

App Service 會使用同盟身分識別，第三方識別提供者可在此儲存帳戶和驗證使用者。應用程式依賴提供者的身分識別資訊，而不是儲存該資訊本身。App Service 支援五個現成的識別提供者：_Azure Active Directory_、_Facebook_、_Google_、_Microsoft 帳戶_及 _Twitter_。App Service 會使用同盟身分識別，第三方識別提供者可在此儲存帳戶和驗證使用者。您也可以藉由整合其他識別提供者或[您個人自訂的身分識別解決方案][custom-auth]來擴充內建的支援。

如果您想要立即開始，請參閱下列教學課程︰

- [將驗證新增至 iOS 應用程式][iOS] (或 [Android]、[Windows]、[Xamarin.iOS]、[Xamarin.Android]、[Xamarin.Forms]，或 [Cordova])
- [Azure App Service 中 API Apps 的使用者驗證][apia-user]

## App Service 中驗證的運作方式

若要使用其中一個識別提供者進行驗證，您首先需要設定識別提供者來了解您的應用程式。識別提供者接著將為您提供識別碼和密碼，您需要將這兩者再次提供給 App Service。這樣會完成信任關係，並讓 App Service 驗證來自識別提供者的使用者判斷提示，例如驗證權杖。

若要使用其中一個提供者以登入使用者，必須將使用者重新導向至該提供者的登入端點。如果客戶正在使用網頁瀏覽器，您可以讓App Service 自動將所有未驗證使用者直接導向至登入端點。否則，您會需要將客戶導向至 `{your App Service base URL}/.auth/login/<provider>`，其中 `<provider>` 是 Aad、Facebook、Google、Microsoft，和 Twitter的其中一個。以下各節涵蓋行動及 API 案例。

使用者透過網頁瀏覽器和您的應用程式進行互動時會產生 Cookie 集，以便在瀏覽應用程式時，能夠維持在已驗證狀態。對於其他用戶端類型，例如行動類型，會將 JSON Web 權杖 (JWT) 核發給應存在於 `X-ZUMO-AUTH` 標頭的用戶端。Mobile Apps 用戶端 SDK 將會為您處理這項工作。或者，Azure Active Directory 身分識別權杖或存取權杖可能直接包含在 `Authorization` 標頭中，做為[持有人權杖](https://tools.ietf.org/html/rfc6750)。

App Service 將對應用程式所核發的任何 Cookie 或權杖進行驗證，讓使用者接受驗證。若要限制誰可以存取應用程式，請參閱以下的[授權](#authorization)一節。

### 使用提供者 SDK 的行動驗證

在後端將一切都設定就緒之後，您就可以修改行動用戶端，以使用 App Service 進行登入。有以下兩種方法：

- 利用指定的識別提供者發佈的 SDK 來建立身分識別，然後取得 App Service 的存取權。
- 使用單一行程式碼，讓 Mobile Apps 用戶端 SDK 登入使用者。

>[AZURE.TIP] 大部分的應用程式都應該使用提供者 SDK 來獲得更自然的登入體驗，以及利用重新整理支援和其他提供者專屬的權益。

使用提供者 SDK 可讓登入體驗與 app 執行所在的平台作業系統更緊密地進行互動。這也會提供您一個提供者權杖以及用戶端上的一些使用者資訊，讓它更容易取用圖表 API 和自訂使用者體驗。您有時會在部落格和論壇上看到這稱為「用戶端流程」或「用戶端導向的流程」，因為用戶端上的程式碼正在處理登入，而且用戶端程式碼可以存取提供者權杖。

一旦取得提供者權杖之後，它需要傳送給 App Service 以進行驗證。當 App Service 驗證權杖之後，它會建立新的 App Service 權杖，該權杖會傳回至用戶端。Mobile Apps 用戶端 SDK 提供 Helper 方法來管理此交換，並自動將權杖附加至應用程式後端的所有要求。如果開發人員選擇這樣做，他們也可以保留對提供者權杖的參考。

### 不使用提供者 SDK 的行動驗證

如果您不想設定提供者 SDK，可允許 App Service Mobile Apps 為您執行登入。Mobile Apps 用戶端 SDK 將為您選擇的提供者開啟網頁檢視，然後完成登入。您有時會在部落格和論壇上看到這稱為「伺服器流程」或「伺服器導向流程」。因為伺服器正在管理登入，而用戶端 SDK 永遠都不會收到提供者權杖。

啟動此流程所需的程式碼會包含於適用於每個平台的驗證教學課程中。在流程結束時，用戶端 SDK 會擁有 App Service 權杖，而該權杖會自動附加至對應用程式後端的所有要求。

### 服務對服務驗證

除了讓使用者存取您的應用程式外，您也可以讓其他信任的應用程式呼叫您個人的 API。例如，您可以讓一個 App Service 應用程式呼叫其他應用程式中的 API。在此案例中，您可以使用服務帳戶認證 (而非使用者認證) 來取得權杖。在 Azure Active Directory 用語中，服務帳戶亦稱為「服務主體」，使用這類帳戶的驗證又稱為「服務對服務案例」。

>[AZURE.IMPORTANT] 由於他們是在客戶的裝置上執行，因此行動應用程式「不算」是信任的應用程式，且不應使用服務主體流程。反之，他們應該使用以上詳述的其中一個使用者流程。

在服務對服務的案例下，App Service 可以使用 Azure Active Directory 保護您的應用程式。呼叫的應用程式只需要藉由提供來自 AAD 的用戶端識別碼和用戶端密碼，提供 AAD 服務主體的授權權杖。[API Apps 的服務主體驗證][apia-service]教學課程中有講述這個使用 ASP.NET API 應用程式之案例的範例。

如果您想要處理服務對服務案例，但不要使用 App Service 驗證，請使用用戶端憑證或基本驗證。如需 Azure 中用戶端憑證的詳細資訊，請參閱[如何設定 Web Apps 的 TLS 相互驗證](../app-service-web/app-service-web-configure-tls-mutual-auth.md)。如需 ASP.NET 中基本驗證的詳細資訊，請參閱[ASP.NET Web API 2 中的驗證篩選](http://www.asp.net/web-api/overview/security/authentication-filters)。

App Service 邏輯應用程式至 API 應用程式的服務帳戶驗證屬於特殊案例，會在[將您裝載在 App Service 上的自訂 API 與邏輯應用程式一起使用](../app-service-logic/app-service-logic-custom-hosted-api.md)中詳細說明。

## <a name="authorization"></a>App Service 中授權的運作方式

您可以完整控制允許哪些要求存取您的應用程式。可以使用下列任何一個行為對 App Service 驗證 / 授權進行設定︰

- 只允許通過驗證的要求進入您的應用程式。

	如果 App Service 收到來自瀏覽器的匿名要求，便會將其重新導向至您所選識別提供者的登入頁面。如果要求來自行動裝置，將會傳回 HTTP 401 未經授權的回應。

	使用此選項，您完全不需要在應用程式撰寫任何驗證程式碼。如果您需要更細部的授權，您的程式碼中會包含使用者的相關資訊。

- 允許所有要求進入您的應用程式，但只讓通過驗證的要求生效，並在 HTTP 標頭中傳遞驗證資訊。

	此選項會將您的應用程式程式碼的授權決策延後。它提供更大的彈性來處理匿名要求，但需要您撰寫程式碼。
	
- 允許所有要求進入您的應用程式，不對要求中的驗證資訊採取任何動作。

	在此情況下，會關閉驗證/授權功能。將驗證和授權工作全部交由應用程式程式碼來處理。

上述的行為是由入口網站中的**當要求未經驗證時應採取的動作**選項所控制。必須對所有要求進行驗證，才能為其中一個提供者選擇「登入方式…」。「允許要求 (無動作)」會將授權決策延後提供給您的程式碼，但仍會提供驗證資訊。如果您想要程式碼能夠處理所有要求，可以停用驗證/授權功能。

## 在應用程式中使用您的使用者身分識別

App Service 會使用特殊標頭，將某些使用者資訊傳遞至您的應用程式。從外部來的要求不允許這些標頭，而且只會在由 App Service 驗證/授權設定時，才會顯示這些標頭。某些範例標頭包括︰

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

以任何語言或架構撰寫的程式碼可以從這些標頭中取得所需的資訊。針對 ASP.NET 4.6 應用程式，ClaimsPrincipal 會自動設定適當的值。

您的應用程式也可透過應用程式 `/.auth/me` 端點上的 HTTP GET，取得使用者的其他詳細資料。如果將有效的權杖包含在要求內，這將傳回 JSON 裝載，內含有關所使用的提供者、基礎提供者權杖，以及某些其他使用者資訊的詳細資料。Mobile Apps 伺服器 SDK 提供使用這項資料 ([Node.JS](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md/#howto-tables-getidentity)、[.NET](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md/#user-info)) 的 Helper 方法。

## 文件和其他資源

### 識別提供者
下列教學課程示範如何設定 App Service 來運用不同的驗證提供者：

- [如何設定您的 App 以使用 Azure Active Directory 登入][AAD]
- [如何設定 App 以使用 Facebook 登入][Facebook]
- [如何設定 App 以使用 Google 登入][Google]
- [如何設定 App 以使用 Microsoft 帳戶登入][MSA]
- [如何設定 App 以使用 Twitter 登入][Twitter]

除了這裡提供的身分識別系統，如果您想要使用其他的身分識別系統，也可以利用 [Mobile Apps .NET 伺服器 SDK 中的預覽自訂驗證支援][custom-auth]，這份資料可用於 Web、行動、或 API Apps。

### 行動應用程式
下列教學課程示範如何使用伺服器導向流程，將驗證新增至您的行動用戶端︰

- [將驗證新增至您的 iOS 應用程式][iOS]
- [將驗證新增至您的 Android 應用程式][Android]
- [將驗證新增至您的 Windows App][Windows]
- [將驗證新增至 Xamarin.iOS 應用程式][Xamarin.iOS]
- [將驗證新增至 Xamarin.Android 應用程式][Xamarin.Android]
- [將驗證加入 Xamarin.Forms 應用程式中][Xamarin.Forms]
- [將驗證新增至您的 Cordova 應用程式][Cordova]

如果您想要使用 AAD 的用戶端導向流程︰

- [使用 Active Directory Authentication Library for iOS][ADAL-iOS]
- [使用 Active Directory Authentication Library for Android][ADAL-Android]
- [使用 Active Directory Authentication Library for Windows 和 Xamarin][ADAL-dotnet]

### API 應用程式
下列教學課程示範如何保護您的 API 應用程式︰

- [Azure App Service 中 API Apps 的使用者驗證][apia-user]
- [在 Azure App Service 中 API Apps 的服務主體驗證][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md/#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md/#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md/#adal

<!---HONumber=AcomDC_0504_2016-->