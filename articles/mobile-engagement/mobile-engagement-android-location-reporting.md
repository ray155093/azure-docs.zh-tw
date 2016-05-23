<properties
	pageTitle="Android Mobile Engagement 位置報告"
	description="Android for Azure Mobile Engagement 位置報告"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="04/20/2016"
	ms.author="piyushjo;ricksal" />

# Android 上的 Engagement 位置報告

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

本主題說明如何在 Android 應用程式中進行 Engagement 位置報告。

## 必要條件

[AZURE.INCLUDE [必要條件](../../includes/mobile-engagement-android-prereqs.md)]


## 位置報告

如果想要報告位置，您需要加入幾行組態 (在 `<application>` 和 `</application>` 標記之間)。

### 延遲區域位置報告

延遲區域位置報告允許報告國家、地區以及與裝置相關聯的位置。這類位置報告只會使用網路位置 (根據基地台識別碼或 WIFI)。每個工作階段最多報告一次裝置區域。絕不會使用 GPS，因此這類位置報告對於電池的影響很小 (但不是沒有)。

報告的區域用來計算關於使用者、工作階段、事件與錯誤的地理統計資料。它們也可用來做為觸達活動的準則。

若要啟用簡易區域位置報告，您可以使用此程序中先前所述的組態執行：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您還需要新增下列權限 (如果未有此權限)：

		<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

或者如果您已經在應用程式中使用 ``ACCESS_FINE_LOCATION``，則可以繼續使用。

### 即時位置報告

即時位置報告允許報告與裝置相關聯的緯度和經度。根據預設，這類位置報告只會使用網路位置 (根據基地台識別碼或 WIFI)，且只會在應用程式於前景中執行 (也就是在工作階段) 時，報告才會為作用中。

即時位置「不會」用來計算統計資料。其唯一用途，是允許在 Reach 活動中使用即時地理圍欄 <Reach-Audience-geofencing> 準則。

若要啟用即時位置報告，請一行程式碼加入至您在啟動器活動中設定 Engagement 連接字串的地方。結果如下所示：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您還需要新增下列權限 (如果未有此權限)：

		<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

或者如果您已經在應用程式中使用 ``ACCESS_FINE_LOCATION``，則可以繼續使用。

#### GPS 式報告

根據預設，即時位置報告只會使用網路位置。若要啟用 GPS 位置 (精準度大為提高)，請使用組態物件：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您還需要新增下列權限 (如果未有此權限)：

		<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### 背景報告

根據預設，即時位置報告只在應用程式在前景中執行 (也就是在工作階段) 時才會為作用中。若也要在背景中啟用報告，請使用此組態物件：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] 當應用程式在背景中執行，即使啟用 GPS，也只會報告網路位置。

如果使用者重新啟動裝置，就會停止背景位置報表，您可以加入以下內容，讓它在開機時自動重新啟動：

		<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			<intent-filter>
			    <action android:name="android.intent.action.BOOT_COMPLETED" />
			</intent-filter>
		</receiver>

您還需要新增下列權限 (如果未有此權限)：

		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### Android M 權限

從 Android M 開始，某些權限會在執行階段管理，而且需要使用者核准。

如果您針對 Android API 層級 23，新的應用程式安裝預設將會關閉執行階段權限。否則預設將會開啟。

使用者可以從裝置設定功能表啟用/停用這些權限。從系統功能表關閉權限會刪除應用程式的背景處理程序，這是一種系統行為，對於在背景中接收推播的功能不會有任何影響。

在 Mobile Engagement 位置報告的環境中，需要在執行階段核准的權限為：

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

您應該使用標準系統對話方塊向使用者要求權限。如果使用者核准，您必須告訴 ``EngagementAgent`` 即時將變更納入考量 (否則下次使用者啟動應用程式時會處理變更)。

以下是在應用程式要求權限並轉送結果 (如果 ``EngagementAgent`` 收到肯定) 時的程式碼範例：

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

<!---HONumber=AcomDC_0511_2016-->