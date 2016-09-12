<properties 
	pageTitle="搭配 Azure MFA 使用行動應用程式做為連絡方式" 
	description="此頁面會顯示使用者如何使用行動應用程式做為 Azure MFA 的主要連絡方式。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenp" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="billmath"/>

# 搭配 Azure Multi-Factor Authentication 使用行動應用程式做為連絡方式

如果您要使用 Microsoft 驗證器應用程式做為主要連絡方式，您可以使用這份文件。它會引導您設定 Multi-Factor Authentication，以利用行動應用程式做為主要連絡方式。

Microsoft 驗證器應用程式適用於 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。

## 使用 Microsoft 驗證器做為連絡方法


- 在 [其他安全性驗證] 頁面上，從下拉式清單選取 [行動裝置應用程式]。


![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- 選取 [通知] 或 [單次密碼]，然後按一下 [設定]。
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


## 手動新增帳戶
如果想要手動新增帳戶，請選取手動輸入帳戶的按鈕。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

現在如果您擁有已有 Azure MFA 的帳戶，請輸入顯示條碼的同一頁所提供的代碼和 URL。此資料會填入行動應用程式中的代碼和 URL 方塊。隨即會開始啟用程序。

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

完成後，請按一下 [與我連絡]。這會傳送通知或驗證碼到您的電話。按一下 [驗證]。



 

<!---HONumber=AcomDC_0831_2016-->