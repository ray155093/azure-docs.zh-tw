<properties
	pageTitle="Azure Mobile Engagement Android SDK 整合"
	description="Android SDK for Azure Mobile Engagement 的最新更新和程序"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="piyushjo" />

# 版本資訊

## 4\.2.3 (08/10/2016)

- 不會再鎖定 WIFI。
- 修正在 init 之前呼叫 getDeviceId 時的死結問題 (4.2.0 中引進的錯誤)。

## 4\.2.2 (2016/05/17)

- 穩定性改進。

## 4\.2.1 (2016/05/10)

- 安全性︰停用 Web 檢視本機檔案存取權限。
- 安全性︰移除 `EngagementPreferenceActivity` 類別，其可擴充過時且不安全的 `PreferenceActivity` 類別。
- 安全性︰現在會記載觸達活動以使用 `exported="false"`，此旗標也可用於先前的 SDK 版本。

## 4\.2.0 (03/11/2016)

- SDK 現在是在 MIT 中授權的。
- 允許在 SDK 初始化階段指定自訂裝置識別碼。

## 4\.1.5 (02/01/2016)

- 穩定性改進。

## 4\.1.4 (01/26/2016)

- 穩定性改進。

## 4\.1.3 (12/9/2015)

- 穩定性改進。

## 4\.1.2 (11/25/2015)

- 穩定性改進。

## 4\.1.1 (11/04/2015)

- 穩定性改進。

## 4\.1.0 (2015/08/25)

- 處理 Android M 的新權限模型。
- 除了使用 `AndroidManifest.xml` 之外，現在可以在執行階段設定定位功能。
- 修正權限錯誤：如果您使用 `ACCESS_FINE_LOCATION`，就不再需要 `ACCESS_COARSE_LOCATION`。
- 穩定性改進。

## 4\.0.0 (07/06/2015)

-   內部通訊協定會變更以讓分析和推播更可靠。
-   原生推送 (GCM/ADM) 現在也用於應用程式通知，因此您必須為任何類型的推送行銷活動設定原生推送認證。
-   修正大圖片通知：它們只會在推播之後顯示 10 秒。
-   修正網頁檢視中的錯誤：按一下連結也會執行預設動作 URL。
-   修正與本機儲存體管理相關的罕見損毀。
-   修正動態組態字串管理。
-   更新 EULA。

## 3\.0.0 (2015/02/17)

-   Azure Mobile Engagement 的最初發行版本
-   appId 組態取代為連接字串組態。
-   已移除從傳送與接收任意 XMPP 實體之任意 XMPP 訊息的 API。
-   已移除在裝置之間傳送與接收訊息的 API。
-   增強安全性。
-   已移除 Google Play 和 SmartAd 追蹤。

<!---HONumber=AcomDC_0810_2016---->