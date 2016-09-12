<properties 
	pageTitle="適用於行動電話的 Microsoft 驗證器應用程式" 
	description="了解如何升級至最新版的 Azure Authenticatior。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="femila" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/22/2016" 
	ms.author="billmath"/>

# Microsoft 驗證器

Microsoft 驗證器應用程式提供額外一層安全性，可用於您的 Azure 帳戶 (例如 bsimon@contoso.onmicrosoft.com)、內部部署工作帳戶 (例如 bsimon@contoso.com)、或 Microsoft 帳戶 (例如 bsimon@outlook.com)。

## 下載 Microsoft 驗證器應用程式

Microsoft 驗證器應用程式適用於 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。

## Microsoft 驗證器應用程式的運作方式
此應用程式的運作方式，是將通知推送至您的智慧型手機或平板電腦上的 Microsoft 驗證器應用程式。您只要點選應用程式中的 [驗證] 進行驗證。

或者，應用程式也可以使用驗證碼。您只需在登入畫面出現提示時輸入應用程式提供的驗證碼。

這些 2 種不同模式分述如下︰

**通知** - 在此模式中，Microsoft 驗證器應用程式可防止未經授權存取帳戶並停止詐騙交易。使用推播通知至您的電話或已註冊的裝置即可進行。只需檢視通知，如果合法，則選取 [驗證]。否則，您可選擇 [拒絕]，或選擇拒絕並回報詐騙通知。如需回報詐騙通知的相關資訊，請參閱＜如何使用 Multi-Factor Authentication 的拒絕和回報詐騙功能＞。

**一次性密碼** - 在此模式中，Microsoft 驗證器應用程式可以作為軟體權杖來產生 OATH 驗證碼。然後，可以連同使用者名稱和密碼一起輸入此驗證碼，以提供第二種形式的驗證。

## 使用 QR 代碼掃描器新增帳戶至 Microsoft 驗證器應用程式

- 在已安裝 Microsoft 驗證器應用程式的電話上，啟動該應用程式並按一下 "+" 以新增帳戶。接下來，請指定您想要新增公司帳戶或學校帳戶，這將會啟動 QR 代碼掃描器。如果您的相機未正常運作，您可以選取手動輸入公司資訊。[手動新增帳戶](#adding-an-account-manually)。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- 掃描 [設定行動裝置應用程式] 畫面上隨附的 QR 代碼圖。按一下 [完成] 以關閉 QR 代碼畫面。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- 如果您無法取得要掃描的 QR 代碼，請手動輸入資訊。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- 在電話上，它就會開始啟動，完成此作業後，請按一下 [與我連絡]。這會傳送通知或驗證碼到您的電話。按一下 [驗證]。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- 某些公司於驗證時可能會需要輸入 PIN。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)


- 當這項作業完成之後，您便可以按一下 [關閉]。此時，您的驗證應會成功。
- 現在建議您輸入您的行動電話號碼，以防萬一您無法存取行動應用程式。
- 從下拉式清單中指定您的國家/地區，然後在國家/地區旁邊的方塊中輸入您的行動電話號碼。按一下 [下一步]。
- 此時，您已設定好連絡方式，現在可以設定非瀏覽器應用程式 (例如 Outlook 2010 或更舊版本) 的應用程式密碼。如果不使用這些應用程式，請按一下 [完成]。否則，請繼續下一個步驟。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- 如果您正在使用這類應用程式，則複製提供的應用程式密碼並將此密碼貼到非瀏覽器應用程式中。如需有關個別應用程式 (如 Outlook 和 Lync) 的步驟，請參閱＜如何將您電子郵件中的密碼變更為應用程式密碼＞及＜如何將您應用程式中的密碼變更為應用程式密碼＞。
- 按一下 [完成]。


## 手動新增帳戶至 Microsoft 驗證器應用程式
如果想要手動新增帳戶，請選取手動輸入帳戶的按鈕。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

現在如果您擁有已有 Azure MFA 的帳戶，請輸入顯示條碼的同一頁所提供的代碼和 URL。此資料會填入行動應用程式中的代碼和 URL 方塊。隨即會開始啟用程序。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

完成後，請按一下 [與我連絡]。這會傳送通知或驗證碼到您的電話。按一下 [驗證]。

## 移至新的 Microsoft 驗證器應用程式

隨著 Microsoft 驗證器應用程式的推出，舊的 Azure Authenticator 應用程式將被取代。Azure 驗證器應用程式仍可繼續運作，但您應該決定移至新的 Microsoft 驗證器應用程式，這篇文章才對您有所幫助。


### 如何移至新的 Microsoft 驗證器應用程式 

**步驟 1：**安裝 Microsoft 驗證器。


**步驟 2：**使用新的應用程式啟動您的帳戶

首先確定，您必須有 QR 代碼或程式碼和 URL，以便手動輸入您要加入至應用程式的帳戶。

> [AZURE.NOTE] 不確定如何取得 QR 代碼？ 請連絡支援人員尋求協助。
> 
> 無法使用新的應用程式啟動您的帳戶？ 請連絡支援人員。
>


一旦您的面前有 QR 代碼，請啟動應用程式。按一下 +。

然後請指定您想要新增公司帳戶或學校帳戶。這會啟動相機來掃描 QR 代碼。如果您無法掃描 QR 代碼，您永遠可以手動輸入。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

若要確認已成功啟動帳戶，請確認新帳戶出現在 [帳戶] 頁面上。


針對您要移轉到新應用程式的所有帳戶執行此步驟。



**步驟 3：**從您的電話解除安裝舊版的 Multi-Factor Authentication 應用程式。

一旦將所有帳戶加入至新應用程式，請從您的電話解除安裝舊版的應用程式。



## 如何使用條碼掃描器新增帳戶



- 首先，請移至您的安全性驗證設定頁面。如需如何進入此頁面的詳細資訊，請參閱[變更安全性設定](multi-factor-authentication-end-user-manage-settings.md)。

- 按一下 [設定] 按鈕。
 
![Add Account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- 這樣會顯示在上面具有條碼的螢幕。
  
![掃描條碼](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- 現在開啟 Microsoft 驗證器應用程式，您應該會進入 [帳戶] 頁面。您會在這裡看到您已設定的帳戶的清單。如果您想要新增帳戶，請按一下 + 符號，然後指定您想要新增公司帳戶或學校帳戶。這會開啟掃描器。

- 掃描條碼。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- 請等候帳戶啟用。

- 就這麼簡單。您現在應該會在 [帳戶] 頁面上看到新的帳戶。

![Add Account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![Add Account](./media/multi-factor-authentication-azure-authenticator/accounts.png)

## 如何手動新增 Azure 帳戶

如果您想要手動新增帳戶，可以執行下列動作：

- 首先，請移至您的安全性驗證設定頁面。如需如何進入此頁面的詳細資訊，請參閱[變更安全性設定](multi-factor-authentication-end-user-manage-settings.md)。

- 按一下 [設定] 按鈕。
 
![Add Account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- 這樣會顯示在上面具有條碼的螢幕。請注意條碼底下的代碼和 URL。
  
![掃描條碼](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- 現在開啟 Microsoft 驗證器應用程式，您應該會進入 [帳戶] 頁面。您會在這裡看到您已設定的帳戶的清單。如果您想要新增帳戶，請按一下 + 符號，然後指定您想要新增公司帳戶或學校帳戶。這會開啟掃描器。這會開啟掃描器。

![Add Account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 按一下底部的 [手動輸入代碼]。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- 輸入顯示條碼的同一頁所提供的代碼和 URL。此資料會填入行動應用程式中的代碼和 URL 方塊。隨即會開始啟用程序。

![Add Account](./media/multi-factor-authentication-azure-authenticator/manual.png)

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

- 請等候帳戶啟用。

- 就這麼簡單。您現在應該會在 [帳戶] 頁面上看到新的帳戶。

![Add Account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![Add Account](./media/multi-factor-authentication-azure-authenticator/accounts.png)


## 如何使用 TouchID 新增帳戶
在 iOS 上的 Microsoft 驗證器行動應用程式支援 Touch ID。Azure Multi-Factor Authentication 可讓組織除了持有已註冊的裝置之外，還可以要求 PIN。使用這項新功能，裝置已啟用 Touch ID 的 iOS 使用者就不需要再輸入 PIN。設定完成後，使用者只要掃描其指紋即可，不需要輸入 PIN 並且點選核准。

設定 Touch ID 搭配 Azure 驗證器非常簡單。您剛剛完成 PIN 的一般驗證挑戰，如果您的裝置支援 Touch ID，我們會自動為您設定。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

往後當您被要求驗證登入時，您只要點選收到的推播通知並且掃描您的指紋即可，不需要輸入您的 PIN。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## 如何刪除帳戶

若要從 Microsoft 驗證器應用程式移除個別帳戶，只需點選帳戶即可。您會取得 [刪除] 選項。

![移除帳戶](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0831_2016-->