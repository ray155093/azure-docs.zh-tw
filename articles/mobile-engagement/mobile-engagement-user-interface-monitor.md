---
title: "Azure Mobile Engagement 使用者介面 - 監視"
description: "了解如何使用 Azure Mobile Engagement 監視應用程式的即時資料"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: b91ad89a-b89d-4377-abb0-cc2d16a2836d
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 838079ed1317aca19ee8e5c5721efb0d93dd97e6


---
# <a name="how-to-monitor-real-time-data-about-your-application"></a>如何監視應用程式的即時資料
本文說明 **Mobile Engagement** 入口網站的 [監視] 索引標籤。 使用 **Mobile Engagement** 入口網站可監視與管理您的行動應用程式。 請注意，若要開始使用入口網站，您必須先建立 **Azure Mobile Engagement** 帳戶。 

UI 的 [監視] 區段提供即時分析資訊，並可讓您設定在 UI 之 [分析](mobile-engagement-user-interface-analytics.md) 區段中過去可用的大多數相同功能達到臨界值時發出警示。 請參閱 **概念** 主題中的 [詞彙](http://go.microsoft.com/fwlink/?LinkId=525555) ﹐了解「分析和監視」中術語和縮寫的定義 (例如：「作用中使用者」、「新增使用者」、「保留使用者」、「工作階段」、「使用者路徑圖表」、「使用者地圖」、「追蹤 URL」、「趨勢」、「活動」、「事件」、「工作」、「錯誤」、「額外資訊」、「損毀」和「應用程式資訊」)。

> [!NOTE]
> 許多 **Mobile Engagement** 入口網站 UI 的區段含有 [顯示說明] 按鈕。 按該按鈕，可獲得關於區段的詳細內容資訊。
> 
> 

## <a name="monitor---sessions-jobs-events-errors-and-crashes"></a>監視 - 工作階段、工作、事件、錯誤和損毀
您可以看到工作階段中和特定螢幕上目前有多少位使用者，或是目前有多少位使用者執行特定動作。 您可以檢視除以「工作階段」、「工作」、「事件」、「錯誤」和「損毀」的使用者活動。 您可以查看目前的資訊，並顯示最後一個小時、一天或一週的資訊。 您可以查看每種類別中的所有資訊，或依特定「工作階段」、「工作」、「事件」、「錯誤」和「損毀」中進行排序。  即時監視適用於在事件 (例如推播行銷活動) 期間用來查看傳送推播通知後是否有立即作用的報升。

![Monitor1][14]  

## <a name="troubleshooting-with-monitor---events---details"></a>監視 - 事件 - 詳細資料疑難排解
從測試裝置產生應用程式中的事件以及在 [監視 - 事件 - 詳細資料] 中尋找它是一種最簡單的方法，可尋找測試裝置的裝置識別碼，以及確認 Azure Mobile Engagement 分析、監視和區段整合可以從您的應用程式運作。 測試裝置的裝置識別碼之後，即可在 [我的帳戶 - 裝置] 中將它加入測試裝置。 如果您無法產生事件，請確定已使用 SDK 將 Azure Mobile Engagement 正確地整合到 Android/iOS/Web/Windows/Windows Phone 應用程式。

如需詳細資訊，請參閱 [SDK 文件][連結 5]

![Monitor2][15]  

## <a name="troubleshooting-with-monitor---crashes---details"></a>監視 - 損毀 - 詳細資料疑難排解
您可以從 [監視 - 損毀 - 詳細資料] 檢閱應用程式的損毀資訊，以協助判斷您應用程式的損毀原因。 您也應該在 Android/iOS/Web/Windows/Windows Phone 之每個 SDK 版本的版本資訊中，查閱每個 SDK 版本的已知問題。

如需詳細資訊，請參閱 [SDK 文件 - 版本資訊][連結 5]

![Monitor3][16]

## <a name="monitor---alerts"></a>監視 - 警示
您也可以指定透過電子郵件或立即訊息自動傳送給您之警示的條件  (支援任何 XMPP 相容服務，例如 Google 的 GTalk 或 Apple 的 iChat)。警示是根據一個預先定義的偵測臨界值，而此臨界值大於 (>) 或小於 (<) 每秒、分鐘或小時特定數目的工作階段、工作、事件、錯誤或損毀。 警示可以監視某指定類型的所有活動，或只監視特定工作、事件或錯誤活動。 

您也可以指定最小偵測率，這是分隔相同警示之兩個通知的最小時間量 (分鐘)，藉此確定觸發警示時，指定的每幾分鐘不會收到 1 個以上的通知。

![Monitor4][17]

## <a name="see-also"></a>另請參閱
* [概念][連結 6]
* [疑難排解指南服務][連結 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[連結 1]: mobile-engagement-user-interface.md
[連結 2]: mobile-engagement-troubleshooting-guide.md
[連結 3]: mobile-engagement-how-tos.md
[連結 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[連結 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[連結 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[連結 7]: https://account.windowsazure.com/PreviewFeatures
[連結 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[連結 9]: http://azure.microsoft.com/services/mobile-engagement/
[連結 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[連結 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[連結 12]: mobile-engagement-user-interface-navigation.md
[連結 13]: mobile-engagement-user-interface-home.md
[連結 14]: mobile-engagement-user-interface-my-account.md
[連結 15]: mobile-engagement-user-interface-analytics.md
[連結 16]: mobile-engagement-user-interface-monitor.md
[連結 17]: mobile-engagement-user-interface-reach.md
[連結 18]: mobile-engagement-user-interface-segments.md
[連結 19]: mobile-engagement-user-interface-dashboard.md
[連結 20]: mobile-engagement-user-interface-settings.md
[連結 21]: mobile-engagement-troubleshooting-guide-analytics.md
[連結 22]: mobile-engagement-troubleshooting-guide-apis.md
[連結 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[連結 24]: mobile-engagement-troubleshooting-guide-service.md
[連結 25]: mobile-engagement-troubleshooting-guide-sdk.md
[連結 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[連結 27]: ../mobile-engagement-how-tos-first-push.md
[連結 28]: ../mobile-engagement-how-tos-test-campaign.md
[連結 29]: ../mobile-engagement-how-tos-personalize-push.md
[連結 30]: ../mobile-engagement-how-tos-differentiate-push.md
[連結 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[連結 32]: ../mobile-engagement-how-tos-text-view.md
[連結 33]: ../mobile-engagement-how-tos-web-view.md



<!--HONumber=Nov16_HO3-->


