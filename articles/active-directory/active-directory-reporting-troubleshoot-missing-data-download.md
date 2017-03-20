---
title: "疑難排解︰已下載的 Azure Active Directory 活動記錄中的遺漏資料 - 預覽 | Microsoft Docs"
description: "提供您已下載的 Azure Active Directory 活動記錄預覽中遺漏資料的解決方案。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: e0d65edcb7c14114565402038b0958c3a2ffb477
ms.lasthandoff: 03/10/2017


---

# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>我在已下載的 Azure Active Directory 活動記錄中找不到任何資料


## <a name="symptoms"></a>徵兆

我已下載活動記錄 (稽核或登入)，卻沒看到我所選擇時間的所有記錄。 原因為何？ 

 ![報告](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>原因

當您在 Azure 入口網站中下載活動記錄時，我們會將級別限制為 120K 筆記錄，依最新記錄排序。 

## <a name="resolution"></a>解決方案

您可以利用 [Azure AD 報告 API](active-directory-reporting-api-getting-started.md) 在任何指定時間點擷取最多一萬筆記錄。 我們建議的方法是定期執行指令碼，呼叫報告 API 以增量方式來擷取一段時間的記錄 (例如，每天或每週)。

## <a name="next-steps"></a>後續步驟
請參閱 [Azure Active Directory 報告常見問題集](active-directory-reporting-faq.md)。


