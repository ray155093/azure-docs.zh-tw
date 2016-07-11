<properties
	pageTitle="開始在 Xamarin.Forms 應用程式中使用 Mobile Apps 的驗證 | Microsoft Azure"
	description="了解如何使用 Mobile Apps 透過眾多識別提供者驗證 Xamarin Forms 應用程式使用者，包括 AAD、Google、Facebook、Twitter 和 Microsoft。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="wesmc"/>

# 將驗證加入 Xamarin.Forms 應用程式中

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##概觀

本主題說明如何從用戶端應用程式驗證 App Service 行動應用程式的使用者。在本教學課程中，您將使用 App Service 支援的識別提供者，將驗證加入 Xamarin.Forms 快速入門專案中。由行動應用程式成功驗證並授權之後，就會顯示使用者識別碼值，而您也將可以存取受限制的資料庫資料。

##必要條件

為了讓本教學課程產生最佳結果，建議您先完成[建立 Xamarin.Forms 應用程式](app-service-mobile-xamarin-forms-get-started.md)教學課程。完成本教學課程之後，您將會有一個多平台 TodoList 應用程式的 Xamarin.Forms 專案。

如果您不要使用下載的快速入門伺服器專案，必須將驗證擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure 行動應用程式的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##註冊應用程式進行驗證，並設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##將驗證加入可攜式類別庫中

Mobile Apps 會使用 [MobileServiceClient] 的 [LoginAsync] 擴充方法，透過 App Service 驗證將使用者登入。這個範例使用伺服器管理的驗證流程，在應用程式中顯示提供者的登入介面。如需詳細資訊，請參閱[伺服器管理的驗證](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow)。若要在實際執行應用程式中提供更好的使用者體驗，您可以考慮改用[用戶端管理的驗證](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow)。

為了驗證 Xamarin Forms 專案，您將在應用程式的可攜式類別庫中定義 **IAuthenticate** 介面。您也會更新可攜式類別庫中定義的使用者介面，以新增 [登入] 按鈕，讓使用者按一下來開始驗證。驗證成功後，將會從行動應用程式後端載入資料。

您必須為應用程式支援的每個平台實作 **IAuthenticate** 介面。


1. 在 Visual Studio 或 Xamarin Studio 中，從名稱中有 **Portable** 的專案中開啟 App.cs，也就是可攜式類別庫專案，然後新增下列 `using` 陳述式︰

		using System.Threading.Tasks;

2. 在 App.cs 中，在 `App` 類別定義之前緊接著加入下列 `IAuthenticate` 介面定義。

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    }

3. 將下列靜態成員加入至 **App** 類別，以使用平台特定實作來初始化介面。

	    public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }

4. 從可攜式類別庫專案中開啟 TodoList.xaml，在 buttonsPanel 配置項目中，將下列 **Button** 項目新增至現有按鈕後面︰

      	<Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
			Clicked="loginButton_Clicked"/>

	此按鈕會向行動應用程式後端觸發伺服器管理的驗證。

5. 從可攜式類別庫專案中開啟 TodoList.xaml.cs，然後將下列欄位加入至 `TodoList` 類別︰

		// Track whether the user has authenticated. 
        bool authenticated = false;


6. 使用下列程式碼取代 **OnAppearing** 方法：

	    protected override async void OnAppearing()
	    {
	        base.OnAppearing();
	
	        // Refresh items only when authenticated.
	        if (authenticated == true)
	        {
	            // Set syncItems to true in order to synchronize the data 
	            // on startup when running in offline mode.
	            await RefreshItems(true, syncItems: false);

				// Hide the Sign-in button.
                this.loginButton.IsVisible = false;
	        }
	    }

	如此可確保只有在驗證使用者之後，才會從服務重新整理資料。

7. 將下列有關 **Clicked** 事件的處理常式加入至 **TodoList** 類別︰

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

8. 儲存變更，並建置可攜式類別庫專案，以驗證沒有錯誤。


##將驗證加入 Android 應用程式中

本節說明如何在 Android 應用程式專案中實作 **IAuthenticate** 介面。如果您不要支援 Android 裝置，請略過這一節。

1. 在 Visual Studio 或 Xamarin Studio 中，以滑鼠右鍵按一下 **droid** 專案，然後按一下 [設定為啟始專案]。

2. 按下 F5 在偵錯工具中啟動專案，然後確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。這是因為只有獲授權的使用者才能存取後端。

3. 開啟 Android 專案中的 MainActivity.cs，然後加入下列 `using` 陳述式：

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. 更新 **MainActivity** 類別來實作 **IAuthenticate** 介面，如下所示︰

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. 更新 **MainActivity** 類別，新增 **IAuthenticate** 介面所需的 **MobileServiceUser** 欄位和 **Authenticate** 方法，如下所示︰

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }


	如果您使用 Facebook 以外的識別提供者，請為 [MobileServiceAuthenticationProvider] 選擇不同的值。

6. 在 **MainActivity** 類別的 **OnCreate** 方法中，在呼叫 `LoadApplication()` 之前加入下列程式碼：

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

	如此可確保在應用程式載入之前初始化驗證器。

7. 重新建置應用程式，執行它，然後以您選擇的驗證提供者登入，並確認您能夠以已驗證的使用者身分存取資料表。

##將驗證加入 iOS 應用程式中

本節說明如何在 iOS 應用程式專案中實作 **IAuthenticate** 介面。如果您不要支援 iOS 裝置，請略過這一節。

1. 在 Visual Studio 或 Xamarin Studio 中，以滑鼠右鍵按一下 **iOS** 專案，然後按一下 [設定為啟始專案]。

2. 按下 F5 在偵錯工具中啟動專案，然後確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。這是因為只有獲授權的使用者才能存取後端。

4. 開啟 iOS 專案中的 AppDelegate.cs，然後加入下列 `using` 陳述式：

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. 更新 **AppDelegate** 類別來實作 **IAuthenticate** 介面，如下所示︰

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate

5. 更新 **AppDelegate** 類別，新增 **IAuthenticate** 介面所需的 **MobileServiceUser** 欄位和 **Authenticate** 方法，如下所示︰

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         

            return success;
        }

	如果您使用 Facebook 以外的識別提供者，請為 [MobileServiceAuthenticationProvider] 選擇不同的值。

6. 在 **FinishedLaunching** 方法中，在呼叫 `LoadApplication()` 之前新增下面這行程式碼：

        App.Init(this);

	如此可確保在應用程式載入之前初始化驗證器。

7. 重新建置應用程式，執行它，然後以您選擇的驗證提供者登入，並確認您能夠以已驗證的使用者身分存取資料表。


##將驗證加入 Windows 應用程式專案中

本節說明如何在 Windows 8.1 和 Windows Phone 8.1 應用程式專案中實作 **IAuthenticate** 介面。相同的步驟適用於通用 Windows 平台 (UWP) 專案。如果您不要支援 Windows 裝置，請略過這一節。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 **WinApp** 或 **WinPhone81** 專案，然後按一下 [設定為啟始專案]。

2. 按下 F5 在偵錯工具中啟動專案，然後確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。這是因為只有獲授權的使用者才能存取後端。

3. 開啟 Windows 應用程式專案的 MainPage.xaml.cs，然後加入下列 `using` 陳述式：

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using Windows.UI.Popups;
		using <your_Portable_Class_Library_namespace>;

	以您的可攜式類別庫的命名空間取代 `<your_Portable_Class_Library_namespace>`。

4. 更新 **MainPage** 類別來實作 **IAuthenticate** 介面，如下所示︰

	    public sealed partial class MainPage : IAuthenticate


5. 更新 **MainPage** 類別，新增 **IAuthenticate** 介面所需的 **MobileServiceUser** 欄位和 **Authenticate** 方法，如下所示︰

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
						success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
                
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

			// Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }


	如果您使用 Facebook 以外的識別提供者，請為 [MobileServiceAuthenticationProvider] 選擇不同的值。

6. 在 **MainPage** 類別的建構函式中，在呼叫 `LoadApplication()` 之前加入下面這行程式碼：

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
 
    以您的可攜式類別庫的命名空間取代 `<your_Portable_Class_Library_namespace>`。如果這是 WinApp 專案，您可以直接跳到步驟 8。下一個步驟只適用於 WinPhone81 專案，其中，您需要完成登入回呼。

7. (選擇性) 在 **WinPhone81** 專案中，開啟 App.xaml.cs，然後加入下列 `using` 陳述式：

		using Microsoft.WindowsAzure.MobileServices;
		using <your_Portable_Class_Library_namespace>;

	以您的可攜式類別庫的命名空間取代 `<your_Portable_Class_Library_namespace>`。

8.  將下列 **OnActivated** 方法覆寫加入至 **App** 類別：

		protected override void OnActivated(IActivatedEventArgs args)
		{
		    base.OnActivated(args);

			// We just need to handle activation that occurs after web authentication. 
		    if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
		    {
				// Get the client and call the LoginComplete method to complete authentication.
		        var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
		        client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
		    }
		}

	當此方法覆寫已存在時，只要新增上述程式碼片段中的條件式程式碼即可。

7. 重新建置應用程式，執行它，然後以您選擇的驗證提供者登入，並確認您能夠以已驗證的使用者身分存取資料表。

##後續步驟

現在您已經完成了這個基本驗證的教學課程，可以考慮繼續進行下列其中一個教學課程：

+ [將推播通知新增至應用程式](app-service-mobile-xamarin-forms-get-started-push.md)：了解如何將推播通知支援新增至應用程式，並設定行動應用程式後端以使用 Azure 通知中樞傳送推播通知。

+ [啟用應用程式的離線同步處理](app-service-mobile-xamarin-forms-get-started-offline-data.md)：了解如何使用行動應用程式後端，將離線支援加入至應用程式。離線同步處理可讓使用者與行動應用程式進行互動 - 檢視、新增或修改資料 - 即使沒有網路連線也可行。

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

<!---HONumber=AcomDC_0629_2016-->