---
title: "開始在 Azure 中自動調整規模 | Microsoft Docs"
description: "了解如何在 Azure 中調整資源的規模。"
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: rajram
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a0a420adc57a0bcbb3779183324ab7bfafcb5bf1
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="get-started-with-auto-scale-in-azure"></a>開始 Azure 中自動調整規模
本文說明如何在 Azure 入口網站中為您的資源設定自動調整規模。

Azure 監視器自動調整規模僅適用於虛擬機器擴展集 (VMSS)、雲端服務、App Service 方案及 App Service 環境。 

# <a name="lets-get-started"></a>開始使用

## <a name="discover-the-auto-scale-settings-in-your-subscriptions"></a>在您的訂用帳戶中探索自動調整規模
您可以在 Azure 監視器中探索適用於自動調整規模的所有資源。 請依照下列步驟進行逐步解說。

- 開啟 [Azure 入口網站][1]
- 按一下左側導覽窗格中的 [Azure 監視器] 圖示。
  ![啟動 Azure 監視器][2]
- 按一下 [自動調整規模] 設定，以檢視適用於自動調整規模的所有資源及其目前的自動調整規模狀態![探索 Azure 監視器中的自動調整規模][3]

您可以使用頂端的篩選窗格來縮小清單範圍，以選取特定資源群組中的資源、選取特定的資源類型或選取特定的資源。

針對每個資源，您可以找到目前的執行個體計數及其自動調整規模狀態。 自動調整規模狀態可以是

- 未設定︰您尚未針對此資源啟用自動調整規模設定
- 已啟用：您已針對此資源啟用自動調整規模設定
- 已停用：您已針對此資源停用自動調整規模設定

## <a name="create-your-first-auto-scale-setting"></a>建立您的第一個自動調整規模設定

現在讓我們逐步執行一個簡單的逐步解說，以建立您的第一個自動調整規模設定。

- 開啟 Azure 監視器中的 [自動調整規模] 刀鋒視窗，然後選取您想要調整的資源。 (下列步驟會使用與某個 Web 應用程式相關聯的 App Service 方案。 您可以[在 5 分鐘內，將您的第一個 ASP.NET Web 應用程式建立在 Azure 中][4])
- 在資源的 [調整規模設定] 刀鋒視窗中，注意目前的執行個體計數為 1。 按一下 [啟用自動調整規模]。
  ![適用於新 Web 應用程式的調整規模設定][5]
- 提供調整規模設定的名稱，然後按一下 [新增規則]。 請注意，調整規模規則選項會在右邊開啟為內容窗格。 預設會將選項設定為如果資源的 CPU 百分比超過 70%，就會將您的執行個體計數相應增加 1。 將其保留為預設值，然後按一下 [新增]。
  ![建立 Web 應用程式的調整規模設定][6]
- 您現在已建立第一個調整規模規則。 請注意，UX 建議了最佳作法，並指出「建議規則中至少有一個調整規模」。 若要執行此動作，請按一下 [新增規則]，然後將 [運算子] 設為 [小於]、將 [臨界值] 設為 [20]，以及將 [運算] 設為 [將計數減少]。 您現在應該會有一個調整規模設定，其會根據 CPU 使用量進行相應放大/相應縮小。
  ![根據 CPU 調整規模][8]
- 按一下 [儲存]

恭喜！ 您現在已成功建立第一個調整規模設定，可根據 CPU 使用量自動調整 Web 應用程式的規模。

> 注意︰相同的步驟都適用於開始使用 VMSS 或雲端服務角色。

# <a name="other-considerations"></a>其他考量
## <a name="scale-based-on-a-schedule"></a>根據排程進行調整
除了一律根據 CPU 調整規模，您也可以根據一週內的特定天數設定不同的調整規模。

- 按一下 [新增調整規模的條件]
- 設定調整規模模式和規則，與預設條件一樣
- 針對排程選取 [重複特定天數]
- 選取天數，以及應針對選取的天數套用調整規模條件的開始/結束時間。

![根據排程調整規模的條件][9]
## <a name="scale-differently-on-specific-dates"></a>在特定日期以不同方式調整規模
除了一律根據 CPU 調整規模，您也可以根據特定的日期設定不同的調整規模。

- 按一下 [新增調整規模的條件]
- 設定調整規模模式和規則，與預設條件一樣
- 針對排程選取 [指定開始/結束日期]
- 選取開始/結束日期，以及應針對選取的日期套用調整規模條件的開始/結束時間。

![根據日期調整規模的條件][10]

## <a name="view-the-scale-history-of-your-resource"></a>檢視資源的調整規模歷程記錄
每當相應增加/減少您的資源時，就會在活動記錄中記錄一個事件。 您可以藉由切換至 [執行歷程記錄] 索引標籤，來檢視過去 24 小時資源的調整規模歷程記錄。

![執行記錄][11]

如果您想要檢視完整的調整規模歷程記錄 (最多 90 天)，可按一下 [按一下此處以查看詳細資料]。 這將會啟動活動記錄，其中含有您的資源且分類為預先選取的「自動調整規模」。

## <a name="view-the-scale-definition-of-the-resource"></a>檢視資源的調整規模定義
自動調整規模設定是一個 ARM 資源。 您可以藉由切換至 [JSON] 索引標籤，在 JSON 中檢視調整規模定義。

![調整規模定義][12]

您可以視需要直接在 JSON 中進行變更。 這些變更將會在儲存時反映出來。

## <a name="disable-autoscale-and-manually-scale-your-instances"></a>停用自動調整規模並手動調整執行個體規模
有時您可能想要停用目前的調整規模設定，並手動調整資源規模。

按一下頂端的 [停用自動調整規模] 按鈕。
![停用自動調整規模][13]

請注意，此選項會停用您的設定，而您仍能在您再次啟用自動調整規模之後恢復它。 您現在可以設定想要手動調整規模的執行個體數目。

![設定手動調整規模][14]

您隨時都能藉由依序按一下 [啟用自動調整規模] 和 [儲存]，來恢復自動調整規模。

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png
