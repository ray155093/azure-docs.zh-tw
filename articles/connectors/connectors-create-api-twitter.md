---
title: 了解如何在邏輯應用程式中使用 Twitter 連接器 | Microsoft Docs
description: 搭配 REST API 參數來使用 Twitter 連接器的概觀
services: ''
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: deonhe

---
# 開始使用 Twitter 連接器
使用 Twitter 連接器，您可以：

* 張貼推文並取得推文
* 存取時間軸、好友和追隨者
* 執行以下所述的任何其他動作和觸發程序

若要使用[任何連接器](apis-list.md)，您必須先建立邏輯應用程式。您可以從[立即建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)來開始。

## 連接到 Twitter
您必須先建立與服務的「連線」，才能透過邏輯應用程式存取任何服務。[連線](connectors-overview.md)可讓邏輯應用程式與另一個服務連線。

### 建立 Twitter 連線
> [!INCLUDE [建立 Twitter 連線的步驟](../../includes/connectors-create-api-twitter.md)]
> 
> 

## 使用 Twitter 觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。[深入了解觸發程序](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

在此範例中，我將示範如何使用 [當有新推文張貼時] 觸發程序來搜尋 #Seattle，而如果找到 #Seattle，使用推文中的文字更新 Dropbox 中的檔案。在企業範例中，您可以搜尋您的公司名稱，並以推文中的文字更新 SQL Database。

1. 在 Logic Apps 設計工具的搜尋方塊中輸入 twitter，然後選取 [Twitter - 當有新推文張貼時] 觸發程序。  
   ![Twitter 觸發程序影像 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. 在 [搜尋文字] 控制項中輸入 #Seattle  
   ![Twitter 觸發程序影像 2](./media/connectors-create-api-twitter/trigger-2.png)  

此時，邏輯應用程式已設有觸發程序，該觸發程序會開始執行工作流程中的其他觸發程序和動作。

> [!NOTE]
> 為了讓邏輯應用程式能正常運作，它必須包含至少一個觸發程序和一個動作。請依照下一節中的步驟新增動作。
> 
> 

## 新增條件
因為我們只對於有 50 個以上跟隨者的使用者的推文有興趣，所以必須先將確認追隨者數目的條件新增至邏輯應用程式。

1. 選取 [+ 新的步驟] 來新增您想要在新推文中找到 #Seattle 時採取的動作。  
   ![Twitter 動作影像 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. 選取 [新增條件] 連結。  
   ![Twitter 條件影像 1](../../includes/media/connectors-create-api-twitter/condition-1.png)  
   這會開啟可供您檢查條件的 [條件] 控制項，例如「等於」、「小於」、「大於」、「包含」等等。   
   ![Twitter 條件影像 2](../../includes/media/connectors-create-api-twitter/condition-2.png)  
3. 選取 [選擇值] 控制項。在此控制項中，您可以從任何先前的動作或觸發程序選取一或多個屬性，做為其條件會評估為 true 或 false 的值。  
   ![Twitter 條件影像 3](../../includes/media/connectors-create-api-twitter/condition-3.png)  
4. 選取 [...] 展開屬性清單，以便查看所有的可用屬性。  
   ![Twitter 條件影像 4](../../includes/media/connectors-create-api-twitter/condition-4.png)  
5. 選取 [追隨者計數] 屬性。  
   ![Twitter 條件影像 5](../../includes/media/connectors-create-api-twitter/condition-5.png)  
6. 請注意，[追隨者計數] 屬性現在位於值控制項。  
   ![Twitter 條件影像 6](../../includes/media/connectors-create-api-twitter/condition-6.png)  
7. 從運算子清單選取 [大於]。  
   ![Twitter 條件影像 7](../../includes/media/connectors-create-api-twitter/condition-7.png)  
8. 輸入 50 做為「大於」運算子的運算元。現已新增此條件。使用上方功能表上的 [儲存] 連結來儲存您的工作。  
   ![Twitter 條件影像 8](../../includes/media/connectors-create-api-twitter/condition-8.png)  

## 使用 Twitter 動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

您現已新增觸發程序，請遵循下列步驟來新增動作，該動作將會張貼包含觸發程序所找到之推文內容的新推文。基於本逐步解說的目的，只會張貼有 50 個以上跟隨者的使用者的推文。

在下一個步驟中，您將新增 Twitter 動作，該動作將使用有 50 個以上跟隨者的使用者所張貼的每則推文的某些屬性來張貼推文。

1. 選取 [新增動作]。這會開啟搜尋控制項，您可以在其中搜尋其他動作和觸發程序。  
   ![Twitter 條件影像 9](../../includes/media/connectors-create-api-twitter/condition-9.png)  
2. 在搜尋方塊中輸入 twitter，然後選取 [Twitter - 張貼推文] 動作。這會開啟 [張貼推文] 控制項，您將在其中輸入所張貼推文的所有詳細資料。  
   ![Twitter 動作影像 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)  
3. 選取 [推文文字] 控制項。前述動作和觸發程序的所有輸出現在可顯示在邏輯應用程式中。您可以選取上述任何輸出並使用它們做為新推文的部分推文文字。  
   ![Twitter 動作影像 2](../../includes/media/connectors-create-api-twitter/action-2.png)  
4. 選取 [使用者名稱]
5. 在推文文字控制項中輸入「說：」。在使用者名稱之後執行此動作。
6. 選取「推文文字」。  
   ![Twitter 動作影像 3](../../includes/media/connectors-create-api-twitter/action-3.png)  
7. 儲存您的工作並傳送具有 #Seattle 雜湊標籤的推文，以啟動您的工作流程。

## 技術詳細資料
以下是有關這個連接支援的觸發程序、動作和回應的詳細資料︰

## Twitter 觸發程序
Twitter 連接器具有下列觸發程序︰

| 觸發程序 | 說明 |
| --- | --- |
| [當有新推文張貼時](connectors-create-api-twitter.md#when-a-new-tweet-is-posted) |當有符合指定搜尋查詢的新推文張貼時，此作業就會觸發流程。 |

## Twitter 動作
Twitter 連接器具有下列動作︰

| 動作 | 說明 |
| --- | --- |
| [取得使用者的時間軸](connectors-create-api-twitter.md#get-user-timeline) |這項作業會取得指定使用者所張貼的最新推文清單。 |
| [取得首頁時間軸](connectors-create-api-twitter.md#get-home-timeline) |這項作業會取得我和我的跟隨者最新的推文及轉推推文。 |
| [搜尋推文](connectors-create-api-twitter.md#search-tweets) |這項作業會取得符合搜尋查詢的相關推文清單。 |
| [取得跟隨者](connectors-create-api-twitter.md#get-followers) |這項作業會取得關注指定使用者的使用者清單。 |
| [取得我的跟隨者](connectors-create-api-twitter.md#get-my-followers) |這項作業會取得正在關注我的使用者清單。 |
| [取得正在關注的對象](connectors-create-api-twitter.md#get-following) |這項作業會取得指定使用者所關注的人員清單。 |
| [取得我正在關注的對象](connectors-create-api-twitter.md#get-my-following) |這項作業會取得我正在關注的使用者清單。 |
| [取得使用者](connectors-create-api-twitter.md#get-user) |這項作業會取得指定使用者的設定檔詳細資料，例如使用者名稱、描述、追隨者計數等等。 |
| [張貼推文](connectors-create-api-twitter.md#post-a-tweet) |這項作業會張貼新推文。 |

## 動作詳細資料
以下是此連接器動作和觸發程序以及其回應的詳細資料︰

### 取得使用者的時間軸
這項作業會取得指定使用者所張貼的最新推文清單。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| userName* |使用者名稱 |使用者的 Twitter 控制代碼 |
| maxResults |結果數目上限 |要傳回的推文數目上限 |

* 表示這是必要屬性

#### 輸出詳細資料
TweetModel：推文物件的表示方式

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| TweetText |字串 |推文的文字內容 |
| TweetId |字串 |推文的識別碼 |
| CreatedAt |字串 |張貼推文的時間 |
| RetweetCount |integer |推文的轉推總次數 |
| TweetedBy |字串 |張貼推文的使用者名稱 |
| MediaUrls |array |與推文一起張貼的媒體 Url |
| TweetLanguageCode |字串 |推文的語言代碼 |
| TweetInReplyToUserId |字串 |目前回覆推文的推文作者使用者識別碼 |
| Favorited |布林值 |指出推文是否標示為喜歡 |
| UserMentions |array |推文中提及的使用者清單 |
| OriginalTweet |未定義 |從中轉推目前推文的原始推文 |
| UserDetails |未定義 |推文的使用者詳細資料 |

### 取得首頁時間軸
這項作業會取得我和我的跟隨者最新的推文及轉推推文。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| maxResults |結果數目上限 |要傳回的推文數目上限 |

* 表示這是必要屬性

#### 輸出詳細資料
TweetModel：推文物件的表示方式

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| TweetText |字串 |推文的文字內容 |
| TweetId |字串 |推文的識別碼 |
| CreatedAt |字串 |張貼推文的時間 |
| RetweetCount |integer |推文的轉推總次數 |
| TweetedBy |字串 |張貼推文的使用者名稱 |
| MediaUrls |array |與推文一起張貼的媒體 Url |
| TweetLanguageCode |字串 |推文的語言代碼 |
| TweetInReplyToUserId |字串 |目前回覆推文的推文作者使用者識別碼 |
| Favorited |布林值 |指出推文是否標示為喜歡 |
| UserMentions |array |推文中提及的使用者清單 |
| OriginalTweet |未定義 |從中轉推目前推文的原始推文 |
| UserDetails |未定義 |推文的使用者詳細資料 |

### 搜尋推文
這項作業會取得符合搜尋查詢的相關推文清單。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| searchQuery* |搜尋文字 |搜尋「快樂時光」、#haiku、愛或恨等詞彙 |
| maxResults |結果數目上限 |要傳回的推文數目上限 |

* 表示這是必要屬性

#### 輸出詳細資料
TweetModel：推文物件的表示方式

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| TweetText |字串 |推文的文字內容 |
| TweetId |字串 |推文的識別碼 |
| CreatedAt |字串 |張貼推文的時間 |
| RetweetCount |integer |推文的轉推總次數 |
| TweetedBy |字串 |張貼推文的使用者名稱 |
| MediaUrls |array |與推文一起張貼的媒體 Url |
| TweetLanguageCode |字串 |推文的語言代碼 |
| TweetInReplyToUserId |字串 |目前回覆推文的推文作者使用者識別碼 |
| Favorited |布林值 |指出推文是否標示為喜歡 |
| UserMentions |array |推文中提及的使用者清單 |
| OriginalTweet |未定義 |從中轉推目前推文的原始推文 |
| UserDetails |未定義 |推文的使用者詳細資料 |

### 取得跟隨者
這項作業會取得關注指定使用者的使用者清單。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| userName* |使用者名稱 |使用者的 Twitter 控制代碼 |
| maxResults |結果數目上限 |要傳回的使用者數目上限 |

* 表示這是必要屬性

#### 輸出詳細資料
UserDetailsModel：Twitter 使用者詳細資料

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| FullName |字串 |使用者名稱 |
| 位置 |字串 |使用者的位置 |
| 識別碼 |integer |使用者的 Twitter 識別碼 |
| UserName |字串 |使用者的畫面名稱 |
| FollowersCount |integer |跟隨者數目 |
| 說明 |字串 |使用者描述 |
| StatusesCount |integer |使用者狀態計數 |
| FriendsCount |integer |好友數目 |
| FavouritesCount |integer |使用者喜歡的推文數目 |
| ProfileImageUrl |字串 |設定檔影像的 Url |

### 取得我的跟隨者
這項作業會取得正在關注我的使用者清單。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| maxResults |結果數目上限 |要取得的使用者數目上限 |

* 表示這是必要屬性

#### 輸出詳細資料
UserDetailsModel：Twitter 使用者詳細資料

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| FullName |字串 |使用者名稱 |
| 位置 |字串 |使用者的位置 |
| 識別碼 |integer |使用者的 Twitter 識別碼 |
| UserName |字串 |使用者的畫面名稱 |
| FollowersCount |integer |跟隨者數目 |
| 說明 |字串 |使用者描述 |
| StatusesCount |integer |使用者狀態計數 |
| FriendsCount |integer |好友數目 |
| FavouritesCount |integer |使用者喜歡的推文數目 |
| ProfileImageUrl |字串 |設定檔影像的 Url |

### 取得正在關注的對象
這項作業會取得指定使用者所關注的人員清單。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| userName* |使用者名稱 |使用者的 Twitter 控制代碼 |
| maxResults |結果數目上限 |要傳回的使用者數目上限 |

* 表示這是必要屬性

#### 輸出詳細資料
UserDetailsModel：Twitter 使用者詳細資料

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| FullName |字串 |使用者名稱 |
| 位置 |字串 |使用者的位置 |
| 識別碼 |integer |使用者的 Twitter 識別碼 |
| UserName |字串 |使用者的畫面名稱 |
| FollowersCount |integer |跟隨者數目 |
| 說明 |字串 |使用者描述 |
| StatusesCount |integer |使用者狀態計數 |
| FriendsCount |integer |好友數目 |
| FavouritesCount |integer |使用者喜歡的推文數目 |
| ProfileImageUrl |字串 |設定檔影像的 Url |

### 取得我正在關注的對象
這項作業會取得我正在關注的使用者清單。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| maxResults |結果數目上限 |要傳回的使用者數目上限 |

* 表示這是必要屬性

#### 輸出詳細資料
UserDetailsModel：Twitter 使用者詳細資料

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| FullName |字串 |使用者名稱 |
| 位置 |字串 |使用者的位置 |
| 識別碼 |integer |使用者的 Twitter 識別碼 |
| UserName |字串 |使用者的畫面名稱 |
| FollowersCount |integer |跟隨者數目 |
| 說明 |字串 |使用者描述 |
| StatusesCount |integer |使用者狀態計數 |
| FriendsCount |integer |好友數目 |
| FavouritesCount |integer |使用者喜歡的推文數目 |
| ProfileImageUrl |字串 |設定檔影像的 Url |

### 取得使用者
這項作業會取得指定使用者的設定檔詳細資料，例如使用者名稱、描述、追隨者計數等等。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| userName* |使用者名稱 |使用者的 Twitter 控制代碼 |

* 表示這是必要屬性

#### 輸出詳細資料
UserDetailsModel：Twitter 使用者詳細資料

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| FullName |字串 |使用者名稱 |
| 位置 |字串 |使用者的位置 |
| 識別碼 |integer |使用者的 Twitter 識別碼 |
| UserName |字串 |使用者的畫面名稱 |
| FollowersCount |integer |跟隨者數目 |
| 說明 |字串 |使用者描述 |
| StatusesCount |integer |使用者狀態計數 |
| FriendsCount |integer |好友數目 |
| FavouritesCount |integer |使用者喜歡的推文數目 |
| ProfileImageUrl |字串 |設定檔影像的 Url |

### 張貼推文
這項作業會張貼新推文。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| tweetText |推文文字 |要張貼的文字 |
| body |媒體 |要張貼的媒體 |

* 表示這是必要屬性

#### 輸出詳細資料
TweetResponseModel：代表已張貼推文的模型

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| TweetId |字串 |所擷取推文的識別碼 |

### 當有新推文張貼時
當有符合指定搜尋查詢的新推文張貼時，此作業就會觸發流程。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| searchQuery* |搜尋文字 |搜尋「快樂時光」、#haiku、愛或恨等詞彙 |

* 表示這是必要屬性

#### 輸出詳細資料
TriggerBatchResponse[TweetModel]

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

## HTTP 回應
上述動作和觸發程序可以傳回一或多個下列的 HTTP 狀態碼︰

| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。發生未知錯誤。 |
| 預設值 |作業失敗。 |

## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->
