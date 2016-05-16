<properties
	pageTitle="在 Log Analytics 中建立自訂的儀表板 | Microsoft Azure"
	description="本指南可幫助您了解 Log Analytics 儀表板如何以視覺效果顯示所有儲存的記錄搜尋，讓您以單一方式檢視您的環境。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# 在 Log Analytics 中建立自訂的儀表板

本指南可幫助您了解 Log Analytics 儀表板如何以視覺效果顯示所有儲存的記錄搜尋，讓您以單一方式檢視您的環境。

![範例儀表板](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

## 如何建立儀表板？

若要開始，請按一下左側導覽上的 [概觀] 按鈕，移至 [OMS 概觀]。您會在左側看到 [我的儀表板] 磚。按一下磚，可向下鑽研儀表板。

![概觀](./media/log-analytics-dashboards/oms-dashboards-overview.png)



## 新增磚

在儀表板中，磚是由您儲存的記錄搜尋所形成。OMS 內建許多預先儲存的記錄搜尋，讓您可以隨時開始。您會看到下列圖示大綱，說明如何開始。

![圖示](./media/log-analytics-dashboards/oms-dashboards-pictorial.png)

在 [我的儀表板] 檢視中，只要按一下頁面底部的 [自訂] 齒輪，就可以進入自訂模式。在頁面右側開啟的面板會顯示工作區所有儲存的記錄搜尋。

![新增磚 1](./media/log-analytics-dashboards/oms-dashboards-add-tile1.png)

若要以磚的視覺效果顯示儲存的記錄搜尋，只要將搜尋拖曳到左側的空白區域即可。當您拖曳時，搜尋就會變成磚。

![新增磚 2](./media/log-analytics-dashboards/oms-dashboards-add-tile2.png)

![新增磚 3](./media/log-analytics-dashboards/oms-dashboards-add-tile3.png)


## 編輯磚

在 [我的儀表板] 檢視中，只要按一下頁面底部的 [自訂] 齒輪，就可以進入自訂模式。按一下您想要編輯的磚。右側面板即會變更為可編輯狀態，並提供一組選項：

![編輯磚](./media/log-analytics-dashboards/oms-dashboards-edit-tile.png)

### 磚視覺效果#
有兩種磚視覺效果可供您選擇：

|圖表類型|作用|
|---|---|
|![長條圖](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png)|顯示已儲存記錄搜尋之結果的時間軸，或是依欄位顯示結果清單 (根據您的記錄搜尋是否根據欄位彙總結果而定)。
|![計量](./media/log-analytics-dashboards/oms-dashboards-metric.png)|在圖格中以數字顯示記錄搜尋結果總數。度量磚可讓您設定一個閾值，當達到該閾值時就讓磚發亮顯示。|

### 閾值
您可以使用「度量」視覺效果在磚上建立閾值。請選取磚，在磚上建立閾值。選擇是否要在值高於或低於選擇的閾值時讓磚發亮顯示，然後在下面設定閾值。

## 排列儀表板
若要排列儀表板，請瀏覽到 [我的儀表板] 檢視，然後按一下頁面底部的 [自訂] 齒輪，進入自訂模式。按一下並拖曳您想要移動的磚，然後將磚移動到您想要的位置。

![排列儀表板](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## 移除磚
若要移除磚，請瀏覽到 [我的儀表板] 檢視，然後按一下頁面底部的 [自訂] 齒輪，進入自訂模式。選取您想要移除的圖格，然後在右側面板上選取 [移除圖格]。

![移除磚](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## 後續步驟

- 在 Log Analytics 中建立警示，以產生通知並修復問題。

<!---HONumber=AcomDC_0504_2016-->