---
title: "針對 Azure Active Directory 存取問題進行疑難排解 | Microsoft Docs"
description: "了解存取組織線上資源發生問題時可採取的解決步驟。"
services: active-directory
keywords: "裝置型條件式存取、裝置註冊、啟用裝置註冊、裝置註冊和 MDM"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/11/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd2076f22c6048fda83d6da3b069e2805afb453f


---
# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>針對 Azure Active Directory 存取問題進行疑難排解
您嘗試存取組織的 SharePoint Online 內部網路，出現 [拒絕存取] 錯誤訊息。 您該怎麼辦？


本文章涵蓋存取組織線上資源發生問題時可協助解決的補救步驟。

有關解決 Azure Active Directory (Azure AD) 存取問題的說明，請移至文章中適合您的裝置平台的那一節︰

* Windows 裝置
* iOS 裝置 (即將推出 iPhone 和 iPad 方面的協助。)
* Android 裝置 (即將推出 Android 手機和平板電腦方面的協助。)

## <a name="access-from-a-windows-device"></a>從 Windows 裝置存取
如果您的裝置在下列其中一個平台上執行，請在後續幾節中查閱嘗試存取應用程式或服務時所顯示的錯誤訊息︰

* Windows 10
* Windows 8.1
* Windows 8
* Windows 7
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2

### <a name="device-is-not-registered"></a>裝置未註冊
如果未向 Azure AD 註冊您的裝置，且應用程式受裝置型原則所保護，您可能會看一個頁面顯示下列其中一個錯誤訊息︰

![因裝置未註冊而顯示的「您無法從這裡完成」訊息](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")

如果您的裝置已加入組織中的 Active Directory 網域，請嘗試下列做法︰

1. 確定您使用工作帳戶 (Active Directory 帳戶) 登入 Windows。
2. 透過虛擬私人網路 (VPN) 或 DirectAccess 連接到公司網路。
3. 連接之後，按 Windows 標誌鍵 + L 鍵來鎖定您的 Windows 工作階段。
4. 輸入您的工作帳戶認證，將 Windows 工作階段解除鎖定。
5. 等候一分鐘，然後再次嘗試存取應用程式或服務。
6. 如果您看到相同的頁面，請按一下 [更多詳細資料] 連結，然後備妥詳細資料來連絡您的系統管理員。

如果您的裝置未加入網域且執行的是 Windows 10，您有兩個選項︰

* 執行 Azure AD Join
* 將您的工作帳戶或學校帳戶新增至 Windows

如需這些選項有何差異的相關資訊，請參閱[在您的工作場所中使用 Windows 10 裝置](active-directory-azureadjoin-windows10-devices.md)。

若要執行加入 Azure AD，請在執行裝置的平台上執行下列步驟 (Windows Phone 上無法使用加入 Azure AD)。

**Windows 10 年度更新版**

1. 開啟 [設定]  應用程式。
2. 按一下 [帳戶] > [存取工作或學校]。
3. 按一下 [ **連接**]。
4. 按一下 [將此裝置加入 Azure AD]。
5. 向您的組織驗證、提供多重要素驗證 (若提示的話)，然後依照顯示的步驟進行。
6. 登出，然後使用您的工作帳戶登入。
7. 再次嘗試存取應用程式。

**Windows 10 2015 年 11 月更新**

1. 開啟 [設定]  應用程式。
2. 按一下 [系統] > [關於]。
3. 按一下 [加入 Azure AD] 。
4. 向您的組織驗證、提供多重要素驗證 (若提示的話)，然後依照顯示的步驟進行。
5. 登出，然後使用您的工作帳戶 (Azure AD 帳戶) 登入。
6. 再次嘗試存取應用程式。

若要新增您的工作帳戶或學校帳戶，請執行下列步驟︰

**Windows 10 年度更新版**

1. 開啟 [設定]  應用程式。
2. 按一下 [帳戶] > [存取工作或學校]。
3. 按一下 [ **連接**]。
4. 向您的組織驗證、提供多重要素驗證 (若提示的話)，然後依照顯示的步驟進行。
5. 再次嘗試存取應用程式。

**Windows 10 2015 年 11 月更新**

1. 開啟 [設定]  應用程式。
2. 按一下 [帳戶] > [您的帳戶]。
3. 按一下 [新增工作或學校帳戶] 。
4. 向您的組織驗證、提供多重要素驗證 (若提示的話)，然後依照顯示的步驟進行。
5. 再次嘗試存取應用程式。

如果您的裝置未加入網域且執行的是 Windows 8.1，若要執行 [加入工作場所] 並在 Microsoft Intune 中註冊，請執行下列步驟：

1. 開啟 [電腦設定] 。
2. 按一下 [網路] > [工作場所]。
3. 按一下 [ **加入**]。
4. 向您的組織驗證、提供多重要素驗證 (若提示的話)，然後依照顯示的步驟進行。
5. 按一下 [開啟] 。
6. 再次嘗試存取應用程式。

### <a name="browser-is-not-supported"></a>不支援瀏覽器
如果您嘗試使用下列其中一個瀏覽器來存取應用程式或服務，可能會拒絕存取︰

* 在 Windows 10 或 Windows Server 2016 中使用 Chrome、Firefox 或其他任何非 Microsoft Edge 與 Microsoft Internet Explorer 的瀏覽器
* 在 Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 中使用 Firefox

您會看到如下所示的錯誤頁面︰

![因瀏覽器不受支援而顯示的「您無法從這裡完成」訊息](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")

唯一的補救方式是根據您的裝置平台來使用應用程式所支援的瀏覽器。

## <a name="next-steps"></a>後續步驟
[Azure Active Directory 條件式存取](active-directory-conditional-access.md)




<!--HONumber=Nov16_HO2-->


