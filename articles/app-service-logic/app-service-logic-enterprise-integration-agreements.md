<properties 
	pageTitle="夥伴與企業整合套件的概觀 | Microsoft Azure App Service" 
	description="了解如何使用夥伴搭配企業整合套件與 Logic Apps" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>


# 了解合約與企業整合套件

## 概觀
合約是企業對企業 (B2B) 通訊的基石，讓商務實體可使用業界標準通訊協定順暢地進行通訊。

## 什麼是合約？

就企業整合套件觀點來看，合約是 B2B 交易夥伴之間的通訊協議。合約是以夥伴想要達成的通訊為基礎，並且是通訊協定或傳輸特定的。

企業整合支援下列三種通訊協定/傳輸標準：

- [AS2](./app-service-logic-enterprise-integration-as2.md)
- [X12](./app-service-logic-enterprise-integration-x12.md)
- [EDIFACT](./app-service-logic-enterprise-integration-edifact.md) (敬請期待)

## 為什麼要使用合約
以下是一些使用合約的常見優點：
- 可讓不同的組織和企業能夠利用已知格式來交換資訊。
- 進行 B2B 交易時，可以提升效率
- 在建立企業整合應用程式時輕鬆地建立、管理和使用它們

## 如何建立合約
- [建立 AS2 合約](./app-service-logic-enterprise-integration-as2.md)
- [建立 X12 合約](./app-service-logic-enterprise-integration-x12.md)

## 如何使用合約
建立合約之後，您可以透過 Azure 入口網站使用它來建立具備 B2B 功能的 [Logic Apps](./app-service-logic-what-are-logic-apps.md "了解 Logic Apps")。

## 如何編輯合約
您可以依照下列步驟來編輯任何合約：
1. 選取整合帳戶，其中包含您想要修改的合約。
2. 選取 [合約] 圖格
3. 在 [合約] 刀鋒視窗中選取您想要修改的合約
4. 從上方功能表中選取 [編輯]
5. 在開啟的 [編輯] 功能表上進行變更，然後選取 [確定] 按鈕以儲存變更

## 如何刪除合約
您可以依照下列步驟，從包含您想要刪除合約的整合帳戶中，刪除任何合約：
1. 選取 [合約] 圖格
2. 在 [合約] 刀鋒視窗中選取您想要刪除的合約
3. 從上方功能表中選取 [刪除]
4. 確認您真的想要刪除該合約
5. 請注意，該合約不會再列於 [合約] 刀鋒視窗上
 

## 後續步驟
- [建立 AS2 合約](./app-service-logic-enterprise-integration-as2.md)

<!---HONumber=AcomDC_0727_2016-->