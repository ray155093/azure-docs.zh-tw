---
title: "Azure Active Directory 報告保留原則 | Microsoft Docs"
description: "Azure Active Directory 中報告資料的保留原則"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 8a7d1bd1db8abdac5b741d79b10c9b363afecd24
ms.openlocfilehash: 9f8d9c845c391ec24aa3b265aa3078840fe3240a


---
# <a name="azure-active-directory-report-retention-policies"></a>Azure Active Directory 報告保留原則
*這份文件是 [Azure Active Directory 報告指南](active-directory-reporting-guide.md)的一部分。*


本主題為您提供 Azure Active Directory 中各種活動報告的資料保留相關常見問題的解答。 

如何才能開始收集活動資料？

| Azure AD 版本 | 開始收集 |
| :--              | :--   |
|Premium 和 Premium 2 | 當您註冊授權時 |
| 免費 | 首次開啟 [Azure Active Directory 刀鋒視窗](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)或使用[報告 API](https://aka.ms/aadreports)  |


何時可在 Azure 入口網站中取得您的活動資料？

- **立即** - 如果您已在 Azure 傳統入口網站中使用報告
- **2 小時內** - 如果您尚未在 Azure 傳統入口網站中開啟報告功能

如何才能開始收集安全性訊號？  
對於安全性訊號，收集程序會在您選擇使用 Identity Protection 中心時啟動。 

收集的資料會儲存多久？

**活動報告**    

| 報告 | Azure AD Free | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| 目錄稽核 | 7 天 | 30 天 | 30 天 |
| 登入活動 |    7 天 | 30 天 | 30 天 |

**安全性訊號**

| 報告 | Azure AD Free | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| 有風險的登入 | 7 天 | 30 天 | 90 天 |





<!--HONumber=Dec16_HO5-->


