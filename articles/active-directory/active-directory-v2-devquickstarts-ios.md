<properties
	pageTitle="Azure AD v2.0 iOS 應用程式 | Microsoft Azure"
	description="如何建置可使用個人 Microsoft 帳戶及工作或學校帳戶登入使用者的 iOS 應用程式 (採用協力廠商程式庫)。"
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/28/2016"
	ms.author="brandwe"/>

# 使用 v2.0 端點透過圖形 API 將登入新增至使用協力廠商程式庫的 iOS 應用程式

Microsoft 身分識別平台會使用開放式標準，例如 OAuth2 和 OpenID Connect。這可讓開發人員運用他們想要與我們的服務整合的任何程式庫。為了協助開發人員使用我們的平台搭配其他程式庫，我們撰寫了幾個逐步解說，示範如何設定協力廠商程式庫以連接到 Microsoft 身分識別平台。大部分實作 [RFC6749 OAuth2 規格](https://tools.ietf.org/html/rfc6749)的程式庫都能連接到 Microsoft 身分識別平台。

此應用程式可讓使用者登入其組織，然後使用圖形 API 在其組織中搜尋其他使用者。

如果您是 OAuth2 或 OpenID Connect 新手，此範例組態可能諸多不太適合您。建議您查看[我們在此記載的通訊協定簡短概觀](active-directory-v2-protocols-oauth-code.md)。


> [AZURE.NOTE]
    我們的平台中有些功能沒有採用這些標準的運算式 (例如條件式存取和 Intune 原則管理)，所以會要求您使用開放原始碼 Microsoft Azure 身分識別程式庫。

> [AZURE.NOTE] 
    v2.0 端點並未支援所有的 Azure Active Directory 案例和功能。若要判斷是否應該使用 v2.0 端點，請閱讀相關的 [v2.0 限制](active-directory-v2-limitations.md)。

## 下載
本教學課程的程式碼保留在 [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2)。若要遵循執行，您可以[用 .zip 格式下載應用程式的基本架構](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip)，或複製基本架構：

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

或者只需下載並立即開始使用︰

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## 註冊應用程式
在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com) 建立新的應用程式，或遵循下列[詳細步驟](active-directory-v2-app-registration.md)。請確定：

- 將指派給您應用程式的**應用程式識別碼**複製起來，您很快會需要用到這些識別碼。
- 為您的應用程式新增**行動**平台。
- 從入口網站複製完整的**重新導向 URI**。您必須使用 `urn:ietf:wg:oauth:2.0:oob` 的預設值。


## 下載協力廠商程式庫 nxoauth2 並啟動工作區

在此逐步解說中，我們將使用 GitHub 提供的 OAuth2Client，這是適用於 Mac OS X & iOS 的 OAuth2 程式庫 (Cocoa & Cocoa Touch)。此程式庫是以 OAuth2 規格的第 10 版草稿為基礎。它會實作原生應用程式設定檔，並支援使用者授權端點。我們需要上述一切，才能與 Microsoft 身分識別平台整合。

### 使用 CocoaPods 將程式庫加入至您的專案

CocoaPods 是 Xcode 專案的相依性管理員。它會自動管理上述安裝步驟。

```
$ vi Podfile
```
將下列加入此 Podfile：

```
 platform :ios, '8.0'
 
 target 'QuickStart' do
 
 pod 'NXOAuth2Client'
 
 end
```

現在使用 Cocoapods 載入該 Podfile。這會建立您將載入的新 XCode Workspace。

```
$ pod install
...
$ open QuickStart.xcworkspace
```

## 專案結構

我們在基本架構中為專案設定下列結構︰

* 具有 UPN 搜尋功能的「主要檢視」
* 所選使用者的相關資料的「詳細資料檢視」
* 可讓使用者登入應用程式來查詢圖形的「登入檢視」。

我們會跳入基本架構中的各種檔案，以加入驗證。程式碼的其他部分 (如視覺化程式碼) 與身分識別無關，所以不會提供給您。

## 在程式庫中設定 settings.plst 檔案

-	在 QuickStart 專案中，開啟 plist 檔案 `settings.plist`。取代區段中的元素值，以反映您在 Azure 入口網站中所輸入的值。每當使用 ADAL 時，您的程式碼便會參考這些值。
    -	`clientId` 是指您從入口網站複製的應用程式 clientId。
    -	`redirectUri` 是入口網站所提供的重新導向 url。

## 在 LoginViewController 中設定 NXOAuth2Client 程式庫

NXOAuthClient 程式庫要求設定一些值。完成後，您可以使用取得的權杖呼叫圖形 API。因為我們知道會在需要驗證時隨時呼叫 `LoginView`，所以將的我們組態值放入該檔案是很合理的。
* 開啟 `LoginViewController.m` 檔案

* 讓我們在程式碼中加入一些值，以便針對驗證和授權設定內容。我會在底下詳細說明這些值。

```objc
NSString *scopes = @"offline_access User.ReadBasic.All";
NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
NSString *loginURL = @"https://login.microsoftonline.com/common/login";
NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
NSURL *myRequestedUrl;
NSURL *myLoadedUrl;
bool loginFlow = FALSE;
bool isRequestBusy;
NSURL *authcode;
```

讓我們詳細了解這些值。

我們所設定的第一個字串適用於 `scopes`。我們對此應用程式要求的範圍是 `User.ReadBasic.All`，這可讓我們讀取我們的目錄中所有使用者的基本設定檔。您可以深入了解可用來 [搭配 Microsft 圖形使用](https://graph.microsoft.io/docs/authorization/permission_scopes)的所有範圍。

對於 `authURL`、`loginURL`、`bhh`、`tokenURL`，您應該使用上面提供的值。如果您使用開放原始碼 Microsoft Azure 身分識別程式庫，我們會使用中繼資料端點為您提取此資料。我們已努力完成為您擷取這些值的工作。

`keychain` 值是一個容器，NXOAuth2Client 程式庫將用來建立可儲存您的權杖的金鑰鏈。如果您想要取得跨多個應用程式的 SSO，可以在每個應用程式中指定相同的金鑰鏈，以及要求在您的 XCode 權利中使用該金鑰鏈。這涵蓋於 Apple 文件中。

這些值的其餘部分都必須使用此程式庫，並且只要為您建立位置，即可將這些值送至內容。

* 建立 URL 快取

在載入檢視後一律會呼叫的 `(void)viewDidLoad()` 內，我們會準備可供我們使用的快取。

新增下列程式碼：

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];
    
}
```

* 建立我們將用於登入的 Web 檢視。

我們會使用 Web 檢視進行帳戶登入。這可讓我們提示使用者提供其他因素 (例如已設定的簡訊) 或將錯誤訊息傳回給使用者。我們會設定 Web 檢視，然後撰寫程式碼，以從 Microsoft 身分識別服務處理將會在 Web 檢視中發生的回呼。

```objc
-(void)requestOAuth2Access {
    //in order to login to Mircosoft APIs using OAuth2 we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client
                                       
                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

* 覆寫 Web 檢視方法來處理驗證

當使用者需要如上所述進行登入時，我們必須告訴 Web 檢視我們所要的行為。您可以只剪下並貼上下列程式碼。

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
    
    // We get the auth token from a redirect so we need to handle that in the webview.
    
    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }
    
    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];
    
    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {
    
    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);
    
    // The webview is where all the communication happens. Slightly complicated.
    
    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);
    
    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    
    return YES;

}
```

* 撰寫程式碼來處理 OAuth2 要求的結果

我們需要程式碼來處理 Web 檢視傳回的重新導向 URL。如果不成功，我們會再試一次。同時，文件庫會提供您可在主控台中看到或以非同步方式處理的錯誤。

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {
    
    AppData* data = [AppData getInstance];
    
    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

* 設定 OAuth 內容 (稱為「帳戶存放區」)

您可以在共用帳戶存放區上，針對您想要從您的應用程式存取的每個服務呼叫 `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]`。帳戶類型是字串，其可做為特定服務的識別碼。由於我們正在存取 圖形 API，所以我們將繼續執行並將它稱為 `"myGraphService"`。接著，我們會設定觀察者，以在權杖發生任何變更時告訴我們。取得權杖後，我們會讓使用者回到 `masterView`。



```objc
- (void)setupOAuth2AccountStore {
    

        AppData* data = [AppData getInstance];
    
    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];
    
    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {
                                                              
                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];
    
    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## 設定 MasterView 以從圖形 API 搜尋及顯示使用者

在格線中顯示傳回資料的 MVC 應用程式超出本逐步解說的範圍，而且有許多線上教學課程說明如何建置一個。我們會在基本架構檔案中為您提供此程式碼。不過，我們必須在此 MVC 應用程式中處理一些事項︰

* 當使用者在搜尋欄位中輸入資料時進行攔截
* 將資料的物件提供給 MasterView，以便在格線中顯示結果

我們會在下方進行這些動作。

* 加入檢查以查看我們是否已登入

如果使用者未登入，應用程式沒什麼作為，所以檢查快取中是否有權杖是明智之舉。如果沒有，我們會重新導向至 LoginView 讓使用者登入。如果您還記得，在檢視載入時執行動作的最佳方式是使用 Apple 提供的 `viewDidLoad()` 方法。

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    
    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    
        if (accounts.count == 0) {
        
        dispatch_async(dispatch_get_main_queue(),^ {
            
            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

* 在收到資料時更新資料表檢視

當我們從圖形 API 取回資料時，我們需要顯示所傳回的資料。為了簡單起見，以下是可更新資料表的全部程式碼。您可以直接在 MVC 未定案程式碼中貼上正確的值。

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];
    
    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }
    
    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];

    
    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];
    
    return cell;
}

```

* 提供有人在搜尋欄位中輸入資料時呼叫圖形 API 的方法

有人在方塊中輸入搜尋資料時，我們需要將該資料塞到圖形 API。為了更妥善區分查閱功能與簡報功能，我們建置另一個稱為 `GraphAPICaller` 的類別 (我們將在下方建置)。現在，我們繼續進行並撰寫程式碼，以便將任何搜尋字元饋送至圖形 API。我們的做法是提供稱為 `lookupInGraph` 的方法，其採用我們想要搜尋的字串。

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {
        
    };
    

    
        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {
                
                
                upnArray = returnedUpns;
                
                
            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];
                
                [alertView setDelegate:self];
                
                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }
            
            
        }];
    
    
} 
```

## 撰寫協助程式類別以存取圖形 API

這是我們的應用程式的要點而其餘就是將程式碼插入 Apple 提供的預設 MVC 模式從，這裡我們會撰寫程式碼，以在使用者輸入並傳回該資料時查詢圖形。我們將顯示程式碼並予以詳細說明。

* 建立名為 `GraphAPICaller.h` 的新 Objective C 標頭檔並加入下列程式碼：

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

如您所見，我們會指定可採用字串並傳回 completionBlock 的方法。此 completionBlock (您可能已經猜到) 會提供可隨著使用者搜尋即時填入資料的物件，藉此更新資料表。


* 建立名為 `GraphAPICaller.m` 的新 Objective C 檔案並加入下列方法：

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }
    
    AppData* data = [AppData getInstance];
    
    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
    
    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];
    
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);
                           
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
                           
                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)
                           
                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];
                           
                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];
                               
                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];
                               
                               
                               [Users addObject:s];
                           }
                           
                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }
                       
                   }];
}

```

我們會詳細解說這個方法

此程式碼的要點在於 `NXOAuth2Request` 方法，該方法會採用我們稍早在 settings.plist 檔案中定義的參數。第一個步驟是建構正確的圖形 API 呼叫。因為我們正在呼叫 `/users`，所以我們會藉由將它附加到我們的圖形 API 資源和版本來進行指定。將這些放在外部設定生活中是很合理的，因為這些會隨著 API 演進而改變。


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

接下來，我們需要指定我們也會提供給圖形 API 呼叫的參數。切記不要將參數放在資源端點中，因為它會在執行階段針對任何非 URI 符合字元而虛設。必須在參數中提供所有的查詢程式碼。

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

您可能發現這會呼叫我們尚未撰寫的 `convertParamsToDictionary` 方法。讓我們立即在檔案的結尾這樣做︰

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];
    
        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];
    
           [dictionary setValue:query forKey:@"$filter"];


    
    return dictionary;
}

```
接下來，我們將使用 `NXOAuth2Request` 方法，以從 API 取回 JSON 格式的資料。

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);
                           
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

最後，讓我們查看如何將資料傳回至 MasterViewController。我們會取回序列化的資料，而該資料必須還原序列化並載入至 MainViewController 可取用的物件。基於此目的，基本架構具有可建立使用者物件的 `User.m/h` 檔案。我們會以圖形中的資訊填入該使用者物件。

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
                           
                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)
                           
                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];
                           
                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];
                               
                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];
                               
                               
                               [Users addObject:s];
```


## 執行範例

如果您使用基本架構或遵循本逐步解說，則您的應用程式現在應會執行。啟動模擬器，然後按一下 [登入] 以使用應用程式。

## 取得產品的安全性更新

我們鼓勵您造訪[此頁面](https://technet.microsoft.com/security/dd252948)並訂閱資訊安全摘要報告警示，以在安全性事件發生時收到通知。

<!---HONumber=AcomDC_0629_2016-->