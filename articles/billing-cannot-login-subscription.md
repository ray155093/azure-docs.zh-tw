<properties
	pageTitle="我無法登入來管理我的 Azure 訂用帳戶 | Microsoft Azure"
	description="說明疑難排解一些常見 Azure 訂用帳戶登入問題的資訊"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2016"
	ms.author="genli"/>

# 我無法登入來管理我的 Azure 訂用帳戶

本文將引導您完成一些可解決登入問題的最常見方法。

> [AZURE.NOTE] 如果您對於本文任何內容有需要進一步的協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## Azure 入口網站

| 名稱 | 說明 | URL |
|---|---|---|
| Azure 入口網站 |一個可供您佈建和管理 Azure 資源的中央位置 | [https://portal.azure.com](https://portal.azure.com) |
| Azure 傳統入口網站 | 舊的 Azure 入口網站 |[https://manage.windowsazure.com](https://manage.windowsazure.com) |
| Azure 帳戶中心 | 一個可供您追蹤 Azure 使用情況及管理訂用帳戶的中央位置 |[https://account.windowsazure.com/Subscriptions](https://account.windowsazure.com/Subscriptions) ||

## 徵兆︰頁面在載入狀態停止回應

這個問題可能是由影響您網際網路瀏覽器的問題所造成。

若要解決此問題，請依指定順序嘗試下列方法。在您執行每個方法之後，請嘗試重新連線到入口網站上的登入頁面。

-	重新整理頁面。
-	使用不同的網際網路瀏覽器。
-	如果您使用的是 Microsoft Internet Explorer，請使用「InPrivate 瀏覽」模式來瀏覽至 Azure 入口網站。若要這樣做，請遵循下列步驟：

	A.按一下 [工具] ![工具按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png) > [安全性] > [InPrivate 瀏覽]。

	B.瀏覽至 [Azure 入口網站][](https://portal.azure.com) 或 [Azure 傳統入口網站][](https://manage.windowsazure.com)，然後登入入口網站。

## 徵兆︰錯誤訊息「找不到任何訂閱」

如果帳戶沒有足夠的使用者權限，就可能發生此問題。帳戶管理員只能存取[帳戶中心](https://account.windowsazure.com/)，而服務管理員 (SA) 和共同管理員 (CA) 則只能存取 [Azure 入口網站](https://portal.azure.com)或 [Azure 傳統入口網站](https://manage.windowsazure.com/)。

**案例 1：在 Azure 入口網站或 Azure 傳統入口網站收到錯誤訊息**

若要解決此問題，請為帳戶[新增共同管理員或擁有者角色](billing-add-change-azure-subscription-administrator.md)。

**案例 2：在 Azure 帳戶中心收到錯誤訊息**

請檢查您所使用的帳戶是否為帳戶管理員。若要確認誰是帳戶管理員，請依照下列步驟操作︰

1.	登入 [Azure 入口網站](https://portal.azure.com)。
2.	在 [中樞] 功能表中，選取 [訂用帳戶]。
3.	選取您想要檢查的訂用帳戶，然後選取 [設定]。
4.	選取 [屬性]。該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員] 方塊中。

## 徵兆：自動將您登入為不同的使用者

如果您在網際網路瀏覽器中使用多個使用者帳戶，就可能發生此問題。

若要解決此問題，請嘗試下列其中一個方法：

- 登出入口網站，然後以您想要使用的帳戶重新登入。
-	清除快取並刪除網際網路 Cookie。若要在 Internet Explorer 中這樣做，請按一下 [工具] ![工具按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png) > [網際網路選項] > [刪除]，請確定已選取暫存檔、Cookie、密碼及瀏覽歷程記錄的核取方塊，然後按一下 [刪除]。

-	重設 Internet Explorer 設定來還原您所做的任何個人設定。若要這樣做，請按一下 [工具] ![工具按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png)> [網際網路選項] > [進階] > 選取 [刪除個人設定] 方塊 > [重設]。

-	以「InPrivate 模式」瀏覽至 Azure 入口網站。若要這樣做，請按一下 [工具] ![工具按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png) > [安全性] > [InPrivate 瀏覽]。

## Microsoft 帳戶與組織帳戶之比較

您的 Microsoft 帳戶是您搭配密碼用來登入所有 Windows Live 程式或服務 (例如 Outlook、Hotmail、MSN 或 OneDrive) 的電子郵件地址。您可以使用您名下的電子郵件地址建立 Microsoft 帳戶，包括公司的電子郵件地址都適用。更多詳細資料請參閱 [www.microsoft.com/account](http://www.microsoft.com/account)。

如果您的帳戶與組織帳戶相關聯，請選取正確的登入選項，如下所示。如需有關使用組織帳戶的詳細資訊，請參閱[以組織身分註冊 Azure](./active-directory/sign-up-organization.md)。

![登入頁面](./media/billing-cannot-login-subscription/signin.png)

> [AZURE.NOTE] 如果您仍有其他問題，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

<!---HONumber=AcomDC_0914_2016-->