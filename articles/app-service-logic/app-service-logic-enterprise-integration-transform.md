---
title: 企業整合套件的概觀 | Microsoft Docs
description: 利用企業整合套件的功能，使用 Microsoft Azure App service 啟用商務程序和整合案例
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe

---
# 具備 XML 轉換的企業整合
## 概觀
企業整合轉換連接器會將資料從某種格式轉換成其他格式。例如，傳入訊息中目前包含的日期是 YearMonthDay 格式。您可以使用轉換，將日期重新格式化為 MonthDayYear 格式。

## 轉換的作用為何？
轉換 (亦稱為對應) 由來源 XML 結構描述 (輸入) 和目標 XML 結構描述 (輸出) 所組成。您可以利用不同的內建功能來操控或控制資料，包括字串操作、條件式協議、算術運算式、日期時間格式器，甚至迴圈建構。

## 如何建立轉換？
您可以使用 Visual Studio [企業整合 SDK](https://aka.ms/vsmapsandschemas) 來建立轉換/對應。當您完成建立及測試轉換之後，可將轉換上傳到整合帳戶。

## 如何使用轉換
當您將轉換上傳到整合帳戶之後，您可以使用它來建立邏輯應用程式。每當觸發邏輯應用程式 (而且還有需要轉換的輸入內容) 時，邏輯應用程式接著便會執行您的轉換。

**以下是使用轉換的步驟**：

### 必要條件
在預覽中，您必須：

* [建立 Azure Functions 容器](https://ms.portal.azure.com/#create/Microsoft.FunctionApp "建立 Azure Functions 容器")
* [將函式加入至 Azure Functions 容器](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-transform-function%2Fazuredeploy.json "此範本會建立以 Webhook 為基礎的 C# Azure 函式並具備轉換功能，以便在邏輯應用程式整合案例中使用")
* 建立整合帳戶，並加入對應

> [!TIP]
> 請記下 Azure Functions 容器與 Azure 函式的名稱，您需要在下一個步驟中用到它們。
> 
> 

既然您已完成必要元件，就可以建立邏輯應用程式：

1. 建立邏輯應用程式並[將它連結到包含對應的整合帳戶](app-service-logic-enterprise-integration-accounts.md "了解如何將整合帳戶連結到邏輯應用程式")。
2. 將 [要求 - 收到 HTTP 要求時] 觸發程序新增到您的邏輯應用程式 ![](./media/app-service-logic-enterprise-integration-transforms/transform-1.png)
3. 先選取 [新增動作] 來新增 [轉換 XML] 動作 ![](./media/app-service-logic-enterprise-integration-transforms/transform-2.png)
4. 在搜尋方塊中輸入「轉換」文字，篩選所有動作以取得您想要使用的動作 ![](./media/app-service-logic-enterprise-integration-transforms/transform-3.png)
5. 選取 [轉換 XML] 動作 ![](./media/app-service-logic-enterprise-integration-transforms/transform-4.png)
6. 選取 [函式容器]，其中包含您將使用的函式。這是您稍早在這些步驟中建立的 Azure Functions 容器名稱。
7. 選取您要使用的 [函式]。這是您稍早建立的 Azure Function 名稱。
8. 新增您將轉換的 XML **內容**。請注意，您可以使用在 HTTP 要求中收到的任何 XML 資料做為**內容**。在此範例中，選取觸發邏輯應用程式的 HTTP 要求本文。
9. 選取您想要用來執行轉換的**對應**名稱。對應必須已經位於您的整合帳戶中。在先前步驟中，您已經為邏輯應用程式提供權限來存取包含對應的整合帳戶。
10. 儲存您的工作 ![](./media/app-service-logic-enterprise-integration-transforms/transform-5.png)

此時，您已完成設定對應。在真實世界應用程式中，您可能想要在 LOB 應用程式 (例如 SalesForce) 中儲存已轉換的資料。您可以輕鬆新增動作，來將轉換的輸出傳送到 Salesforce。

您現在可以藉由向 HTTP 端點提出要求來測試轉換。

## 功能和使用案例
* 在對應中建立轉換並不難，例如，只要在不同文件之間複製名稱和位址，即可完成。或者，您可以使用內建的對應作業，建立更複雜的轉換。
* 目前有多個對應作業或函數可供使用，包括字串、日期時間函數等等。
* 您可以在結構描述間執行直接的資料複製。在 SDK 內含的對應程式中，只要繪製一條線連接來源結構描述中的元素與其目的地結構描述中的對等項目，即可完成此動作。
* 建立對應時，您可以檢視圖形化對應，其中會顯示您所建立的所有關聯性和連結。
* 使用 [測試對應] 功能，以新增範例 XML 訊息。只要按一下滑鼠，您即可測試已建立的對應，並檢視產生的輸出。
* 上傳現有的對應
* 包括對 XML 格式的支援。

## 詳細資訊
* [深入了解企業整合套件](app-service-logic-enterprise-integration-overview.md "了解企業整合套件")
* [深入了解對應](app-service-logic-enterprise-integration-maps.md "了解企業整合對應")

<!---HONumber=AcomDC_0803_2016-->