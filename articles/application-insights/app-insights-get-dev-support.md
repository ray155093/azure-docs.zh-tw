---
title: 如何從 Application Insights 開發團隊取得技術支援 | Microsoft Docs
description: 當您需要有來自 Application Insights 開發團隊的特殊支援的案例，這便是您提交詳細資料以取得支援的方式。
services: application-insights
documentationcenter: ''
author: alexbulankou
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/01/2016
ms.author: albulank

---
# 如何從 Application Insights 開發團隊取得技術支援
當您對於 [Visual Studio Application Insights](app-insights-overview.md) 有技術問題，以下是可取得協助的選項：

## 1\.檢查文件
* 遺漏資料？ 勾選：[取樣][](app-insights-sampling.md)、[配額和節流][](app-insights-pricing.md)。
* 疑難排解：[ASP.NET](app-insights-troubleshoot-faq.md) | [Java](app-insights-java-troubleshoot.md)

## 2\.搜尋論壇
* [MSDN 論壇](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
* [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

## 3\.Azure 支援計劃？
有時候您會想要開發人員調查您的特定案例。

如果您有 [Microsoft Azure 的支援計劃](https://azure.microsoft.com/support/plans/)，您可以[開啟支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## 4\.連絡 Application Insights 團隊
如果您沒有支援計劃，在準備通用版本上市里程碑的同時，我們的開發團隊會很樂於為 Application Insights 客戶提供盡最大努力的支援。我們即將推出**新的支援選項**：您可以透過在 Azure 入口網站上提交意見表單向我們描述您的問題，並由 Application Insights 團隊的開發人員與您連絡來協助解決您的問題。

1. 在 [Application Insights 入口網站](https://portal.azure.com)中，按一下位於右上角的笑臉：  
   
    ![[意見反應] 按鈕](./media/app-insights-get-dev-support/01.png)
2. 在註解方塊中，請務必在第一行指定 **AppInsights**，然後加入下列資訊：
   
    ```
   
    AppInsights   
    ikey: <instrumentation key>   
    sdk: <SDK that you are using, including name and version>  
    issue: <please describe the problem you are having>
   
    ```   
   
    ![[意見反應] 對話方塊](./media/app-insights-get-dev-support/02.png)
3. 勾選 [是，可以寄送電子郵件給您]。
   
    ![[提交] 區段](./media/app-insights-get-dev-support/03.png)

Application Insights 團隊工程師會立即與您連絡。由於我們盡最大努力提供這項服務，此時不會提供正式的 SLA。

<!---HONumber=AcomDC_0615_2016-->