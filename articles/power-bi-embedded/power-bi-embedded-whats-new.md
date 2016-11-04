---
title: Power BI Embedded 新功能
description: 取得 Power BI Embedded 新功能的最新資訊
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton

---
# <a name="what's-new-in-power-bi-embedded"></a>Power BI Embedded 新功能
**Power BI Embedded** 的更新會定期發行。 但是，並非所有發行版本都會包含供使用者使用的新功能，某些版本著重在後端服務功能。 我們將會在這裡反白顯示對於使用者的功能。 請務必經常回來查看。

## <a name="august-31st,-2016"></a>2016 年 8 月 31 日
這個版本中包含︰

* 支援 [進階篩選和頁面瀏覽](power-bi-embedded-interact-with-reports.md)的全新 JavaScript SDK。
* 「加拿大中部」資料中心內現已支援 Power BI Embedded。 查看 [資料中心狀態](https://azure.microsoft.com/status/)。

## <a name="july-11th,-2016"></a>2016 年 7 月 11 日
這個版本中包含︰

* **好消息！**  Power BI Embedded service 不再是預覽 - 現在已 GA (正式推出)。  
* 所有 REST API 已從 **/beta** 移至 **/v1.0**。
* .NET 和 JavaScript SDK 已針對 **v1.0**更新。
* 現在可以直接使用 API 金鑰來驗證 power BI API 呼叫。 只有內嵌需要應用程式權杖。 在這個過程中，佈建和開發權杖已在 v1.0 API 中被取代，但是它們會繼續在 Beta 版中運作，直到 12/30/2016 為止。 若要深入了解，請參閱 [使用 Power BI Embedded 驗證和授權](power-bi-embedded-app-token-flow.md)。
* 應用程式權杖和內嵌報告的資料列層級安全性 (RLS) 支援。 若要深入了解，請參閱 [Power BI Embedded 的資料列層級安全性](power-bi-embedded-rls.md)。
* 已針對所有 **v1.0** API 呼叫，更新範例應用程式。
* Azure SDK、PowerShell 和 CLI 的 Power BI Embedded 支援。
* 使用者可以將視覺效果資料匯出到 **.csv**。
* Power BI Embedded 現在受到與 Microsoft Azure 相同的所有語言/地區設定的支援。 若要深入了解，請參閱 [Azure - 語言](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx)。

<!--HONumber=Oct16_HO2-->


