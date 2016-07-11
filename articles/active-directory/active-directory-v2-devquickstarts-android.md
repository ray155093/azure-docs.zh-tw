<properties
	pageTitle="Azure AD v2.0 Android 應用程式 | Microsoft Azure"
	description="如何建置可在個人 Microsoft 帳戶及工作或學校帳戶登入使用者並使用協力廠商程式庫呼叫圖形 API 的 Android 應用程式。"
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="brandwe"/>

#  使用 v2.0 端點透過圖形 API 將登入新增至使用協力廠商程式庫的 Android 應用程式

Microsoft 身分識別平台會使用開放式標準，例如 OAuth2 和 OpenID Connect。這可讓開發人員運用他們想要與我們的服務整合的任何程式庫。為了協助開發人員使用我們的平台搭配其他程式庫，我們撰寫了幾個逐步解說，示範如何設定協力廠商程式庫以連接到 Microsoft 身分識別平台。大部分實作 [RFC6749 OAuth2 規格](https://tools.ietf.org/html/rfc6749)的程式庫都能連接到 Microsoft 身分識別平台。

此應用程式可讓使用者登入其組織，然後使用圖形 API 在組織中搜尋自己。

如果您是 OAuth2 或 OpenID Connect 新手，此範例組態可能諸多不太適合您。建議您查看[我們在此記載的通訊協定簡短概觀](active-directory-v2-protocols-oauth-code.md)。


> [AZURE.NOTE]
	我們的平台中有些功能沒有採用這些標準的運算式 (例如條件式存取和 Intune 原則管理)，所以會要求您使用開放原始碼 Microsoft Azure 身分識別程式庫。


> [AZURE.NOTE]
	v2.0 端點並未支援所有的 Azure Active Directory 案例和功能。若要判斷是否應該使用 v2.0 端點，請閱讀相關的 [v2.0 限制](active-directory-v2-limitations.md)。


## 下載
本教學課程的程式碼保留在 [GitHub](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git)。若要遵循執行，您可以[用 .zip 格式下載應用程式的基本架構](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git/archive/skeleton.zip)，或複製基本架構：

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

或者只需下載並立即開始使用︰

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## 註冊應用程式
在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com) 建立新的應用程式，或遵循下列[詳細步驟](active-directory-v2-app-registration.md)。請確定：

- 將指派給您應用程式的**應用程式識別碼**複製起來，您很快會需要用到這些識別碼。
- 為您的應用程式新增**行動**平台。
- 從入口網站複製完整的**重新導向 URI**。您必須使用 `https://login.microsoftonline.com/common/oauth2/nativeclient` 的預設值。


## 下載協力廠商程式庫 nxoauth2 並啟動工作區

在此逐步解說中，我們將使用 GitHub 中的 OIDCAndroidLib，這是以 Google 的 OpenID Connect 程式碼為基礎的 OAuth2 程式庫。它會實作原生應用程式設定檔，並支援使用者授權端點。我們需要上述一切，才能與 Microsoft 身分識別平台整合。

*  複製

首先，將 OIDCAndroidLib 儲存機制複製到您的電腦。

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

### 設定您的 Android Studio 環境

*  建立「建立新的 Gettingstartedwithdata 專案」這個專案，然後遵循預設精靈。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

*  我認為設定專案模組的最簡單方式，就是將複製的儲存機制移至專案位置以設定模組。您也可以先建立專案，然後將專案直接複製到專案位置。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

接下來，從關聯式功能表開啟專案模組設定，或使用 `Ctrl + Alt + Maj + S` 快速鍵。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

移除預設應用程式模組，因為我們只想要專案容器設定。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

我們現在必須將模組從複製的儲存機制匯入至目前的專案

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

> 對 `oidlib-sample` 模組重複這些步驟

檢查 `oidlib-sample` 模組的 oidclib 相依性

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

按一下 [確定] 並等候 gradle 同步處理

您的 settings.gradle 應如下所示：

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

*  建置應用程式範例，確定此範例正確執行。

您尚未能夠將此範例使用於 Azure Active Directory。我們需要先設定一些端點。這是為了確保在我們開始自訂範例應用程式前，Android Studio 沒有問題。

在 Android Studio 中建置 `oidlib-sample` 並當作目標執行

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

*  清除

您可以放心地刪除從專案中移除模組時所留下的 `app ` 目錄，因為 Android Studio 不會基於安全考量而刪除它。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

您也可以開啟 [編輯組態] 功能表，移除從專案中移除模組時所留下的執行組態。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## 設定範例的端點

您現在已順利執行 `oidlib-sample`，讓我們編輯某些端點以便使用於 Azure Active Directory。

* 設定您的用戶端

開啟 `oidc_clientconf.xml` 檔案並進行下列變更：

1. 由於我們目前只使用 OAuth2 流量來取得權杖及呼叫圖形 API，所以將用戶端設定成只進行 OAuth2。後面的範例中會示範如何使用 OIDC。

```xml
    <bool name="oidc_oauth2only">true</bool>
```

2. 設定您從註冊入口網站收到的用戶端識別碼。

```xml
    <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
    <string name="oidc_clientSecret"></string>
```

3. 設定您從註冊入口網站收到的重新導向 URI。

```xml
    <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
```

4. 設定您需要的範圍，以便存取 圖形 API。

```xml
    <string-array name="oidc_scopes">
        <item>openid</item>
        <item>User.ReadBasic.All</item>
        <item>offline_access</item>
    </string-array>
```

我們在此設定 `oidc_scopes`。我們對此應用程式要求的範圍是 `User.ReadBasic.All`，這可讓我們讀取我們的目錄中所有使用者的基本設定檔。您可以深入了解可用來 [搭配 Microsft 圖形使用](https://graph.microsoft.io/docs/authorization/permission_scopes)的所有範圍。

如果您希望 `openid` 或 `offline_access` 的說明成為 OpenID Connect 中的範圍，請查看[我們在此記載的通訊協定簡短概觀](active-directory-v2-protocols-oauth-code.md)。

* 設定用戶端端點

開啟 `oidc_endpoints.xml` 檔案並進行下列變更：

```xml
<!-- Stores OpenID Connect provider endpoints. -->
<resources>
    <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
    <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
    <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
    <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
</resources>
```

如果您使用 OAuth2 做為通訊協定，則不得變更這些端點。

> [AZURE.NOTE] 
Azure Active Directory 目前不支援 `userInfoEndpoint` 和 `revocationEndpoint` 的端點，所以我們將保留 example.com 的預設值，以提供此範例中沒有的實用提醒。


## 設定圖形 API 呼叫

開啟 `HomeActivity.java` 檔案並進行下列變更：

```Java
   //TODO: set your protected resource url
    private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
```

我們在此進行簡單的圖形 API 呼叫，以便傳回我們的資訊

## 大功告成！

這就是您所需進行的所有變更！ 執行應用程式 `oidlib-sample` 並按一下登入。O

順利通過驗證後，請按 [要求受保護的資源] 按鈕，以測試您對圖形 API 的呼叫。

## 取得產品的安全性更新

我們鼓勵您造訪[此頁面](https://technet.microsoft.com/security/dd252948)並訂閱資訊安全摘要報告警示，以在安全性事件發生時收到通知。

<!---HONumber=AcomDC_0629_2016-->