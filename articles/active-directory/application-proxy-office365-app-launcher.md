---
title: "使用 Azure AD 應用程式 Proxy 為您發佈的應用程式設定自訂首頁 | Microsoft Docs"
description: "涵蓋 Azure AD 應用程式 Proxy 連接器的基本概念。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: d543849ccdc3cd93845756954b063ae169b066d5
ms.openlocfilehash: 9f642b03b6c10f6312b2c31bbc810cb6701352e3


---

# <a name="set-a-custom-home-page-for-your-published-application-using-azure-ad-app-proxy"></a>使用 Azure AD 應用程式 Proxy 為您發佈的應用程式設定自訂首頁

此文章討論如何設定應用程式，以在使用者從 Azure AD 存取面板與 Office 365 應用程式啟動器存取您的應用程式時，將使用者導向到自訂首頁。

>[!NOTE]
>應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。
> 
 
使用 Azure AD PowerShell 模組，您可以定義自訂首頁 URL，讓使用者在您的應用程式中看到特定頁面，例如 https://expenseApp-contoso.msappproxy.net/login/login.aspx。

>[!NOTE]
>當您將已發佈應用程式的存取權提供給使用者時，我們會在 [Azure AD 存取面板](active-directory-saas-access-panel-introduction.md)與 [Office 365 應用程式啟動器](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher)中顯示您的應用程式。 
>

當使用者啟動您的應用程式時，系統預設會將它們導向到已發佈應用程式的根網域 URL。 登陸頁面通常是設定為首頁 URL。 例如，對於此後端應用程式 http://ExpenseApp，它是發佈為 https://expenseApp-contoso.msappproxy.net。 根據預設值，首頁 URL 是設定為 https://expenseApp-contoso.msappproxy.net。

## <a name="determine-the-home-page-url"></a>決定首頁 URL

設定首頁 URL 之前必須注意一些事項：

* 您必須確定您指定的路徑是根網域 URL 的子網域路徑。

 例如，若已發佈的應用程式可從首頁 URL https://intranet-contoso.msappproxy.net/ 存取，則您設定的首頁 URL 開頭必須是 https://intranet-contoso.msappproxy.net/。 
 
* 若首頁 URL 是 https://apps.contoso.com/app1/，則首頁 URL 的開頭必須是 https://apps.contoso.com/app1/。

* 若變更已發佈的應用程式，它可能會重設首頁 URL 的值。 因此，當您決定更新您的應用程式時，您應該重新檢查並視需要更新首頁 URL。


在下一節，我們將逐步說明如何為您發佈的應用程式設定自訂首頁 URL。 

## <a name="install-the-azure-ad-powershell-module"></a>安裝 Azure AD PowerShell 模組

使用 PowerShell 定義自訂首頁 URL 之前，您必須先安裝 Azure AD PowerShell 模組的非標準套件。  您可以從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0)下載此套件，它使用圖形 API 端點。 

**使用 PowerShell 來安裝套件：**

1. 開啟標準 PowerShell。
2. 執行以下命令：

```
 Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
 ```
 若您以非系統管理員身分執行此命令，您必須使用 _-scope currentuser_ 選項。
3. 在安裝期間，選取 [Y] 以從 Nuget.org 安裝兩個套件。  這兩個套件是使用該套件的必要項目。 

##<a name="set-a-custom-home-page-url-using-the-azure-ad-powershell-module"></a>使用 Azure AD PowerShell 模組設定自訂首頁 URL

現在您已安裝 Azure AD PowerShell 模組，您可以使用兩個簡單的步驟來設定首頁 URL。

1. 尋找您要更新的應用程式。
2. 更新應用程式的首頁 URL。

###<a name="step-1--find-the-objectid-of-the-application"></a>步驟 1 – 尋找應用程式的 ObjectID

首先，您必須取得應用程式的 ObjectID，然後依首頁搜尋應用程式。

1. 開啟 PowerShell。
2. 匯入 Azure AD 模組。
  
 ```
 Import-Module AzureAD
 ```
3. 登入 Azure AD 模組。  使用下面的 Cmdlet 並依照畫面上的指示執行。 請確定您以租用戶系統管理員身分登入。
 
 ```
 Connect-AzureAD
 ```
4. 下面的 Cmdlet 會根據包含 _sharepoint-iddemo_ 的首頁尋找應用程式。 這是您要編輯的應用程式。 您將需要以您應用程式適用的值來取代此值。
  
 ```
 Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
 ```
5. 您應該會看到如下回應的結果。 GUID (下面的 ObjectID 值) 是您將需要複製的項目。
 
 ```
 DisplayName : SharePoint
 Homepage    : https://sharepoint-iddemo.msappproxy.net/
 ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```
6. 複製 GUID (ObjectID) 值。 下一個步驟需要此項目。


###<a name="step-2--update-the-homepage-url"></a>步驟 2 – 更新首頁 URL

您使用相同的 PowerShell 模組來更新首頁 URL，就如同您用來尋找應用程式識別碼一樣應用程式。 登入到 PowerShell 之後，依照下列步驟執行：

1. 確認您有正確的應用程式，並將 _8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4_ 取代為您在上面的步驟 1 從應用程式複製的 ObjectID 值。 
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
 ```
 
 現在您已確認應用程式，您可以依照下列指示更新首頁。
 
2. 建立空白應用程式物件以存放您要進行的變更。 這只是一個要用來存放您要更新之值的變數，因此我們實際上不會建立任何項目。
  
 ```
 $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
 ```
3. 將首頁設定為您想要的值。 請記住，它必須是已發佈之應用程式的子網域路徑。 例如，若將首頁從 _https://sharepoint-iddemo.msappproxy.net/_ 變更為 _https://sharepoint-iddemo.msappproxy.net/hybrid/_，您的使用者將直接前往自訂首頁。
  
 ```
 $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
 ```
4. 您需要做的最後一件事是進行更新。 請務必使用您從上面的步驟 1 複製的 GUID。
  
 ```
 Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
 ```
5. 現在您需要透過再次啟動應用程式以確認自訂首頁，檢查變更是否成功。
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```

>[!NOTE]
>請記住，您對應用程式所做的任何變更可能都會重設首頁 URL。 在此案例中，您將需要重複此程序。

##<a name="next-steps"></a>後續步驟

[使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取](application-proxy-enable-remote-access-sharepoint.md)<br>
[在 Azure 入口網站中啟用應用程式 Proxy](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


