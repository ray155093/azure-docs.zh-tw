<properties
	pageTitle="在邏輯應用程式中新增循環觸發程序 | Microsoft Azure"
	description="循環觸發程序的概觀，以及如何搭配 Azure 邏輯應用程式使用。"
	services=""
	documentationCenter=""
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# 開始使用循環觸發程序

透過循環觸發程序，您可以在雲端建立強大的工作流程。

例如，您可以：

- 排程讓工作流程每天執行 SQL 預存程序。
- 以電子郵件傳送過去一週內所有關於特定雜湊標記之推文的摘要。

若要使用邏輯應用程式中的循環觸發程序來開始作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 使用循環觸發程序

觸發程序是一個事件，可用來啟動邏輯應用程式中定義的工作流程。[深入了解觸發程序](connectors-overview.md)。

以下是如何在邏輯應用程式中設定循環觸發程序的範例順序：

1. 將**循環**觸發程序新增為邏輯應用程式中的第一個步驟。
2. 填入循環間隔的參數。

邏輯應用程式現在會在每個時間間隔之後啟動執行。

![HTTP 觸發程序](./media/connectors-native-recurrence/using-trigger.png)

## 觸發程序詳細資料

循環觸發程序具有下列可設定的屬性。

它會在指定的時間間隔之後引發邏輯應用程式。標示 * 代表必要欄位。

|顯示名稱|屬性名稱|說明|
|---|---|---|
|頻率 *|frequency|時間單位：`Second`、`Minute`、`Hour`、`Day` 或 `Year`。|
|間隔 *|interval|給定循環頻率的間隔。|
|時區|timeZone|如果提供開始時間時未指定 UTC 時差，將會使用此時區。|
|開始時間|startTime|[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的開始時間。|
<br>


## 後續步驟

立即試用平台和[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。您可以查看我們的 [API 清單](apis-list.md)，以探索邏輯應用程式中其他可用的連接器。

<!---HONumber=AcomDC_0810_2016------>