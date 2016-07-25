<properties
	pageTitle="如何為 PowerApps 和邏輯流程自訂 Swagger 定義 | Microsoft Azure"
	description="檢視 Swagger 使用 PowerApps 和邏輯流程時所需的結構描述擴充功能"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/12/2016"
   ms.author="mandia"/>


# 為 PowerApps 和邏輯流程自訂 Swagger 定義

>[AZURE.IMPORTANT] 本主題已移至 powerapps.microsoft.com，位於：[為 PowerApps 和流程自訂 Swagger 定義](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)。請移至 PowerApps 以取得最新版本。此 Azure 連結會被封存。


## 為您的 API 撰寫 Swagger 2.0 API 定義

若要了解如何將 Swagger 加入至您的 WebAPI，請參閱 [Swashbuckle][1]。

## 結構描述延伸模組
除了標準 Swagger 規格以外，還有一些其他的 Swagger 擴充功能可在建立 PowerApps 和邏輯流程的自訂 API 時使用。本節會列出並描述這些擴充功能。

##### x-ms-summary
描述實體顯示名稱的字串，這些實體在 Swagger 規格中未定義摘 `summary` 欄位。例如 [參數名稱]。

##### x-ms-visibility
這個值描述是否在邏輯流程設計工具中顯示實體。可以使用下列值：

- 「無」(預設值)
- 「進階」
- 「內部」- 邏輯流程的設計工具不會顯示這些作業

如果作業標示為「重要」，邏輯流程用戶端應該要反白顯示這些作業。

##### x-ms-trigger
定義這項作業是否可用為邏輯流程的觸發程序。選項包括：
	
- 無 (預設值)：作業不能用為觸發程序。
- 單一：這項作業也可用為觸發程序。
- 批次處理：這項作業可用為觸發程序。此外，這項作業以 JSON 'array' 物件回應，然後邏輯流程引發陣列中每個項目的觸發程序。


##### x-ms-dynamic-values
這是邏輯流程設計工具的提示：API 提供這個參數的動態允許值清單。邏輯流程設計工具可以叫用這個欄位值定義的作業，並從結果擷取可能的值。邏輯流程設計工具接著將這些值顯示為使用者的選項。

值是包含下列屬性的物件：
	
- `operationId`：比對已叫用作業 operationId 的字串
- `parameters`：物件屬性定義作業所需參數的物件
- `value-collection`：在回應承載中評估物件陣列的路徑字串
- `value-path`：參考參數值的 "value-collection" 內部物件中的路徑字串。
- `value-title`：參考值描述的 "value-collection" 內部物件中的路徑字串。


範例：

```javascript
"/api/tables/{table}/items": {
  "post": {
    "operationId": "TableData_CreateItem",
    "summary": "Create an object in {Salesforce}",
    "parameters": [
      {
        "name": "table",
        "x-ms-summary": "Object Type",
        "x-ms-dynamic-values": {
          "operationId": "TableMetadata_ListTables",      // operation that needs to be invoked
          "parameters": { },                              // parameters for the above operation, if any
          "value-collection": "values",                   // field that contains the collection
          "value-path": "Name",                           // field that contains the value
          "value-title": "DisplayName"                    // field that contains a display name for the value
      }
      // ...
    ]
    // ...
  }
  // ...
}
```

在此範例中，swagger 定義了在 Salesforce 中建立新物件的 `TableData_CreateItem` 作業。

Salesforce 有很多內建物件。`x-ms-dynamic-values` 在這裡用來協助設計工具釐清 Salesforce 的內建物件清單。它會呼叫 `TableMetadata_ListTables` 以取得清單。

##### x-ms-dynamic-schema
這是邏輯流程設計工具的提示：這個參數 (或回應) 之結構描述的本質是動態的。它可以叫用這個欄位值定義的作業，並以動態方式探索結構描述。然後顯示適當的 UI 以接受使用者輸入，或顯示可用的欄位。

範例：

```javascript
{
  "name": "item",
  "in": "body",
  "required": true,
  "x-ms-dynamic-schema": {
    "operationId": "Metadata_GetTableSchema",
    "parameters": {
      "tablename": "{table}"              // the value that the user has selected from the above parameter
    },
    "value-path": "Schema"                // the field that contains the JSON schema
  }
},
```

這在作業輸入為動態的案例中很有用。例如，考量 SQL 的案例。每個資料表的結構描述都不相同。因此，當使用者選取特定的資料表時，邏輯流程設計工具就必須了解資料表的結構，以便顯示資料行名稱。本文中，如果 swagger 定義具有 `x-ms-dynamic-schema`，它就會呼叫對應的作業擷取結構描述。


<!--Reference links in article-->
[1]: https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md

<!---HONumber=AcomDC_0713_2016-->