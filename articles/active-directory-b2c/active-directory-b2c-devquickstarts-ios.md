---
title: Azure Active Directory B2C：使用協力廠商程式庫從 iOS 應用程式呼叫 Web API | Microsoft Docs
description: 本文將示範如何使用協力廠商程式庫建立 iOS「待辦事項清單」應用程式，以使用 OAuth 2.0 持有人權杖呼叫 Node.js Web API。
services: active-directory-b2c
documentationcenter: ios
author: brandwe
manager: mbaldwin
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: objectivec
ms.topic: hero-article
ms.date: 07/26/2016
ms.author: brandwe

---
# Azure AD B2C：使用協力廠商程式庫從 iOS 應用程式呼叫 Web API
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Microsoft 身分識別平台會使用開放式標準，例如 OAuth2 和 OpenID Connect。這可讓開發人員運用他們想要與我們的服務整合的任何程式庫。為了協助開發人員使用我們的平台搭配其他程式庫，我們撰寫了幾個逐步解說，示範如何設定協力廠商程式庫以連接到 Microsoft 身分識別平台。大部分實作 [RFC6749 OAuth2 規格](https://tools.ietf.org/html/rfc6749)的程式庫都能連接到 Microsoft 身分識別平台。

如果您是 OAuth2 或 OpenID Connect 新手，此範例組態可能諸多不太適合您。建議您查看[我們在此記載的通訊協定簡短概觀](active-directory-b2c-reference-protocols.md)。

> [!NOTE]
> 我們的平台中有些功能沒有採用這些標準的運算式 (例如條件式存取和 Intune 原則管理)，所以會要求您使用開放原始碼 Microsoft Azure 身分識別程式庫。
> 
> 

B2C 平台並未支援 Azure Active Directory 的所有案例和功能。如果要判斷是否應該使用 B2C 平台，請閱讀 [B2C 限制](active-directory-b2c-limitations.md)。

## 取得 Azure AD B2C 目錄
您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄為所有使用者、應用程式、群組等項目的容器。如果您還沒有此資源，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)再繼續進行。

## 建立應用程式
接著，您必須在 B2C 目錄中建立應用程式。這會提供必要資訊給 Azure AD，讓它與應用程式安全地通訊。在此案例中，因為應用程式與 Web API 會組成一個邏輯應用程式，所以將由單一**應用程式識別碼**代表。如果要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必：

* 在應用程式中加入**行動裝置**。
* 複製指派給您的應用程式的**應用程式識別碼**。稍後您也會需要此資訊。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 建立您的原則
在 Azure AD B2C 中，每個使用者經驗皆由[原則](active-directory-b2c-reference-policies.md)所定義。此應用程式包含一個身分識別體驗：合併登入和註冊。您必須為每個類型建立此原則，如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。建立此原則時，請務必：

* 在原則中選擇 [顯示名稱] 和註冊屬性。
* 在每個原則中，選擇 [顯示名稱] 和 [物件識別碼] 應用程式宣告。您也可以選擇其他宣告。
* 在您建立每個原則之後，請複製原則的 [名稱]。其前置詞應該為 `b2c_1_`。您稍後需要用到此原則名稱。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的原則後，就可以開始建置您的應用程式。

## 下載程式碼
本教學課程的程式碼保留在 [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)。若要遵循執行，您可以[用 .zip 格式下載應用程式構](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)或加以複製：

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

或者只要下載完整的程式碼並立即開始著手使用︰

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## 下載協力廠商程式庫 nxoauth2 並啟動工作區
在此逐步解說中，我們將使用 GitHub 提供的 OAuth2Client，這是適用於 Mac OS X & iOS 的 OAuth2 程式庫 (Cocoa & Cocoa Touch)。此程式庫是以 OAuth2 規格的第 10 版草稿為基礎。它會實作原生應用程式設定檔，並支援使用者授權端點。我們需要上述一切，才能與 Microsoft 身分識別平台整合。

### 使用 CocoaPods 將程式庫新增至您的專案
CocoaPods 是 Xcode 專案的相依性管理員。它會自動管理上述安裝步驟。

```
$ vi Podfile
```
將下列內容新增至此 Podfile：

```
 platform :ios, '8.0'

 target 'SampleforB2C' do

 pod 'NXOAuth2Client'

 end
```

現在使用 Cocoapods 載入該 Podfile。這會建立您將載入的新 XCode Workspace。

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## 專案結構
我們在基本架構中為專案設定下列結構︰

* 具有工作窗格的 [主要檢視]
* 所選工作相關資料的 [新增工作檢視]
* 可讓使用者登入應用程式的 [登入檢視。

我們會跳入專案中的各種檔案，以新增驗證。程式碼的其他部分 (如視覺化程式碼) 與身分識別無關，所以不會提供給您。

## 建立您的應用程式的 `settings.plist` 檔案
如果我們有集中位置可放置我們的組態值，就比較容易設定應用程式。也可協助您了解每項設定在您的應用程式中的作用。我們將會利用「屬性清單」將這些值提供給應用程式。

* 在您的應用程式工作區中建立/開啟 `Supporting Files` 之下的 `settings.plist` 檔案
* 輸入下列值 (我們很快會逐一詳細說明)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

讓我們詳細了解這些值。

對於 `authURL`、`loginURL`、`bhh`、`tokenURL`，您會發現您需要填入您的租用戶名稱。這是已指派給您的 B2C 租用戶的租用戶名稱。例如，`kidventusb2c.onmicrosoft.com`。如果您使用開放原始碼 Microsoft Azure 身分識別程式庫，我們會使用中繼資料端點為您提取此資料。我們已努力完成為您擷取這些值的工作。

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

`keychain` 值是一個容器，NXOAuth2Client 程式庫將用來建立金鑰鏈來儲存您的權杖。如果您想要取得跨多個應用程式的 SSO，可以在每個應用程式中指定相同的金鑰鏈，以及要求在您的 XCode 權利中使用該金鑰鏈。這涵蓋於 Apple 文件中。

在每個 URL 結尾的 `<policy name>` 是您放置先前建立之原則的地方。應用程式會根據流程呼叫這些原則。

`taskAPI` 是我們將使用 B2C 權杖呼叫的 REST 端點，以便新增工作或查詢現有的工作。這已特別針對此範例進行設定。您不需要加以變更，範例即可運作。

這些值的其餘部分都必須使用此程式庫，並且只要為您建立位置，即可將這些值送至內容。

我們現已建立 `settings.plist` 檔案，我們需要程式碼來讀取它。

## 設定 AppData 類別以讀取我們的設定
讓我們製作簡單的檔案，其只會剖析我們先前所建立的 `settngs.plist` 檔案，並且讓這些設定可在未來用於任何類別。因為我們不想在每次類別要求時建立一份新資料，所以我們將使用「單例」模式，而且只傳回每次對設定提出要求時建立的相同執行個體

* 建立 `AppData.h` 檔案︰

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* 建立 `AppData.m` 檔案︰

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

我們現在只要在任何類別中呼叫 `  AppData *data = [AppData getInstance];` (如下所示)，即可輕鬆地取得我們的資料。

## 在 AppDelegate 中設定 NXOAuth2Client 程式庫
NXOAuthClient 程式庫要求設定一些值。完成後，您可以使用所取得的權杖來呼叫 REST API。因為我們知道會在載入應用程式時隨時呼叫 `AppDelegate`，所以將我們的組態值放入該檔案是很合理的。

* 開啟 `AppDelegate.m` 檔案
* 匯入我們稍後將使用的一些標頭檔。

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* 在 AppDelegate 中新增 `setupOAuth2AccountStore` 方法

我們需要建立 AccountStore，然後將我們剛從 `settings.plist` 檔案讀入的資料饋送給它。

此時您應注意一些有關 B2C 服務的事務，讓此程式碼更容易了解︰

1. Azure AD B2C 使用查詢參數所提供的「原則」來為您的要求提供服務。這可讓 Azure Active Directory 做為僅供您的應用程式使用的獨立服務。為了提供這些額外的查詢參數，我們必須提供 `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` 方法與我們的自訂原則參數。
2. Azure AD B2C 使用範圍的方式非常類似於其他 OAuth2 伺服器。不過，由於使用 B2C 的關鍵主要在於驗證使用者與存取資源，所以絕對需要某些範圍才能讓流程正確運作。這是 `openid` 範圍。我們的 Microsoft 身份識別 SDK 會自動為您提供 `openid` 範圍，所以您不會在我們的 SDK 組態中看到該範圍。不過，由於我們使用協力廠商程式庫，所以需要指定此範圍。

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
接下來，務必在 AppDelegate 中的 `didFinishLaunchingWithOptions:` 方法之下呼叫該範圍。

```
[self setupOAuth2AccountStore];
```


## 建立 `LoginViewController` 我們將用來處理驗證要求的類別
我們會使用 Web 檢視進行帳戶登入。這可讓我們提示使用者提供其他因素 (例如已設定的簡訊) 或將錯誤訊息傳回給使用者。我們會設定 Web 檢視，然後撰寫程式碼，以從 Microsoft 身分識別服務處理將會在 Web 檢視中發生的回呼。

* 建立 `LoginViewController.h` 類別

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

我們將建立下列每個方法。

> [!NOTE]
> 務必將 `loginView` 繫結至您的腳本內的實際 Web 檢視。否則，您就不會有在開始驗證時快顯的 Web 檢視。
> 
> 

* 建立 `LoginViewController.m` 類別
* 新增一些變數，以在我們進行驗證時傳遞狀態

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* 覆寫 Web 檢視方法來處理驗證

當使用者需要如上所述進行登入時，我們必須告訴 Web 檢視我們所要的行為。您可以只剪下並貼上下列程式碼。

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* 撰寫程式碼來處理 OAuth2 要求的結果

我們需要程式碼來處理 Web 檢視傳回的重新導向 URL。如果不成功，我們會再試一次。同時，文件庫會提供您可在主控台中看到或以非同步方式處理的錯誤。

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* 設定通知處理站。

我們會如同在上述 `AppDelegate` 中建立相同的方法，但這次我們將新增一些 `NSNotification` 來告知我們服務發生什麼狀況。我們會設定觀察者，以在權杖發生任何變更時告訴我們。取得權杖後，我們會讓使用者回到 `masterView`。

```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* 新增程式碼，以便在針對 sign-native 起始要求時處理使用者

讓我們建立每當我們提出驗證要求時所要呼叫的方法。這會是實際建立 Web 檢視的方法

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* 最後，讓我們在每次 `LoginViewController` 載入時，呼叫我們上面撰寫的所有方法。我們的做法是將這些方法新增至 Apple 提供給我們的 `viewDidLoad` 方法

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

您現在已建立起我們與應用程式互動以便登入的主要方式。我們必須在登入之後，使用我們所收到的權杖。於是，我們將建立一些會呼叫 REST API 的協助程式碼，以便我們使用此程式庫。

## 建立 `GraphAPICaller` 類別以處理我們對 REST API 的要求
我們每次載入我們的應用程式時會載入一個組態。一旦擁有權杖後，我們現在需要進行一些處理動作。

* 建立 `GraphAPICaller.h` 檔案

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

您可從此程式碼看到，我們將建立兩個方法︰一個會從 API 取得工作，另一個會將工作新增至 API。

我們現已設定好我們的介面，讓我們新增實際的實作︰

* 建立 `GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## 執行範例應用程式
最後，在 Xcode 中建置並執行應用程式。註冊或登入應用程式，並為登入的使用者建立工作。登出後，再以不同使用者身分重新登入，然後為該使用者建立工作。

請注意，這些工作會依每位使用者儲存於 API，因為 API 會從它收到的存取權杖中擷取使用者的身分識別。

## 後續步驟
您現在可以進入更進階的 B2C 主題。您可以嘗試：

[從 Node.js Web 應用程式呼叫 Node.js Web API]()

[自訂 B2C 應用程式的 UX]()

<!---HONumber=AcomDC_1005_2016-->