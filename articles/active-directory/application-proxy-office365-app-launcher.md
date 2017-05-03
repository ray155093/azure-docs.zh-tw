---
title: "使用 Azure AD 應用程式 Proxy 為發佈的應用程式設定自訂首頁 | Microsoft Docs"
description: "涵蓋 Azure AD 應用程式 Proxy 連接器的基本概念"
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
ms.date: 04/15/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 31e8e39580ed83f13fd3ffb9981221765063a0b7
ms.lasthandoff: 04/21/2017


---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 為發佈的應用程式設定自訂首頁

此文章討論如何設定應用程式，以在使用者從 Azure Active Directory (Azure AD) 存取面板與 Office 365 應用程式啟動器存取應用程式時，將使用者導向自訂首頁。

當使用者啟動應用程式時，預設會將他們導向已發佈應用程式的根網域 URL。 登陸頁面通常設定為首頁 URL。 例如，對於後端應用程式 http://ExpenseApp，URL 會發佈為 *https://expenseApp-contoso.msappproxy.net*。 根據預設，首頁 URL 會設定為 *https://expenseApp-contoso.msappproxy.net*。

使用 Azure AD PowerShell 模組，您可以定義執行個體的自訂首頁 URL，讓應用程式使用者抵達應用程式內的特定頁面，(例如 *https://expenseApp-contoso.msappproxy.net/login/login.aspx*)。

>[!NOTE]
>當您將已發佈應用程式的存取權提供給使用者時，應用程式會顯示在 [Azure AD 存取面板](active-directory-saas-access-panel-introduction.md)和 [Office 365 應用程式啟動器](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher)。

## <a name="before-you-start"></a>開始之前

### <a name="determine-the-home-page-url"></a>決定首頁 URL

設定首頁 URL 之前，請記住下列事項︰

* 確保您指定的路徑是根網域 URL 的子網域路徑。

  如果根網域 URL 是 https://apps.contoso.com/app1/，您設定的首頁 URL 開頭必須 https://apps.contoso.com/app1/。

* 若變更已發佈的應用程式，則變更可能會重設首頁 URL 的值。 當您未來更新應用程式時，您應該重新檢查並視需要更新首頁 URL。

### <a name="install-the-azure-ad-powershell-module"></a>安裝 Azure AD PowerShell 模組

使用 PowerShell 定義自訂首頁 URL 之前，請先安裝 Azure AD PowerShell 模組的非標準套件。 您可以從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0)下載此套件，其使用圖形 API 端點。 

若要安裝套件，請遵循下列步驟：

1. 開啟標準 PowerShell 視窗，然後執行下列命令：

    ```
     Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
    ```
    若您以非系統管理員身分執行此命令，請使用 `-scope currentuser` 選項。
2. 在安裝期間，選取 [Y] 以從 Nuget.org 安裝兩個套件。 兩個套件都是必要套件。 

## <a name="step-1-find-the-objectid-of-the-app"></a>步驟 1：尋找應用程式的 ObjectID

取得應用程式中的 ObjectID，然後依其首頁搜尋應用程式。

1. 開啟 PowerShell 並匯入 Azure AD 模組。

    ```
    Import-Module AzureAD
    ```

2. 以租用戶系統管理員身分登入 Azure AD 模組。

    ```
    Connect-AzureAD
    ```
3. 根據應用程式的首頁 URL 尋找它。 您可以在入口網站中找到 URL，請前往 [Azure Active Directory] > [企業應用程式] > [所有應用程式]。 這個範例會使用 sharepoint-iddemo。

    ```
    Get-AzureADApplications | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. 您應會取得如下所示的結果。 將 ObjectID GUID 複製以在下一節中使用。

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

## <a name="step-2-update-the-home-page-url"></a>步驟 2：更新首頁 URL

在您於步驟 1 所使用的相同 PowerShell 模組中，執行下列動作：

1. 確認您有正確的應用程式，並將 *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* 取代為您在前一個步驟中複製的GUID (ObjectID)。

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 您現在已確認應用程式，可以開始依照下列指示更新首頁。

2. 建立空白應用程式物件以存放您要進行的變更。  

 >[!NOTE]
 >這只是一個要用來存放您要更新之值的變數，因此我們實際上不會建立任何項目。

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. 將首頁 URL 設定為您想要的值。 此值必須是已發佈應用程式的子網域路徑。 例如，若將首頁 URL 從 *https://sharepoint-iddemo.msappproxy.net/* 變更為 *https://sharepoint-iddemo.msappproxy.net/hybrid/*，應用程式使用者將直接前往自訂首頁。

    ```
    $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. 使用您在「步驟 1：尋找應用程式的 ObjectID」中複製的 GUID (ObjectID)，進行更新。

    ```
    Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
    ```
5. 若要確認變更成功，請重新啟動應用程式。

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>您對應用程式所做的任何變更都可能會重設首頁 URL。 如果發生這種情況，請重複步驟 2。

## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取](application-proxy-enable-remote-access-sharepoint.md)
- [在 Azure 入口網站中啟用應用程式 Proxy](active-directory-application-proxy-enable.md)

