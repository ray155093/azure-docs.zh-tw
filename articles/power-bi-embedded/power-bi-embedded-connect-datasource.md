<properties
   pageTitle="Microsoft Power BI Embedded - 連接到資料來源"
   description="Power BI Embedded，連接到資料來源"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# 連接到資料來源

使用 **Power BI Embedded** 可以將報表內嵌到您自己的應用程式。當您將 Power BI 報表內嵌到您的應用程式時，報告會以**匯入**資料複本的方式連接至基礎資料，或使用 **DirectQuery** **直接連接**到資料來源。

以下是使用**匯入**和 **DirectQuery** 之間的差異。

|Import | DirectQuery
|---|---
|資料表、資料行*和資料*匯入或複製到報表的資料集中。若要查看基礎資料發生的變更，您必須重新整理或再次匯入完整的目前資料集。|只有*資料表和資料行*匯入或複製到報表的資料集中。一律檢視最新的資料。

下一節會描述使用 **DirectQuery** 的優點和限制。

## 使用 DirectQuery 的優點

使用 **DirectQuery** 有兩項主要優點：

   -	**DirectQuery** 可讓您對非常大型的資料集建立視覺效果，否則不可能第一次就匯入所有資料。
   -	基礎資料變更需要重新整理資料，而某些報表如果要顯示目前的資料，還需要大量的資料傳輸，這都使得重新匯入資料不可行。相較之下，**DirectQuery** 報表一律使用目前的資料。

## DirectQuery 的限制

   使用 **DirectQuery** 有一些限制：

   -	所有資料表必須全都來自單一資料庫。
   -	如果查詢太複雜，就會發生錯誤。若要補救錯誤，您必須重構以簡化查詢。如果查詢一定要很複雜，就必須匯入資料，而不是使用 **DirectQuery**。
   -	關聯性篩選限於單向，而非雙向。
   -	您無法變更資料行的資料類型。
   -	根據預設，限制是置於量值允許的 DAX 運算式中。請參閱 [DirectQuery 和量值](#measures)。

<a name="measures"/>
## DirectQuery 和量值

為確保傳送至基礎資料來源的查詢都有可接受的效能，所以將限制加諸於量值之上。當使用 **Power BI Desktop** 時，進階使用者可以選擇 [檔案] > [選項和設定] > [選項]，選擇略過這項限制。在 [選項] 對話方塊中選擇 [DirectQuery]，並選取選項 [允許在 DirectQuery 模式中量值不受限制]。選取該選項後，即可使用對量值有效的任何 DAX 運算式。不過，使用者必須知道，當資料匯入時執行得很好的某些運算式，在 **DirectQuery** 模式中可能會造成後端來源的查詢非常慢。若要深入了解如何使用 **Power BI Desktop**，請參閱[開始使用 Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)。

## 另請參閱
- [開始使用 Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)
- [Power BI 桌面](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
- [開始使用 Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)

<!---HONumber=AcomDC_0420_2016-->