<properties 
	pageTitle="新結構描述版本 2016-06-01 | Microsoft Azure" 
	description="了解如何撰寫最新版邏輯應用程式的 JSON 定義" 
	authors="jeffhollan" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jehollan"/>
	
# 新結構描述版本 2016-06-01

邏輯應用程式的新結構描述和 API 版本有一些增強功能，可提升邏輯應用程式的可靠性和易用性。其中有 3 個主要的差異：

1. 新增範圍，也就是包含動作集合的動作。
1. 條件和迴圈是第一級動作
1. 透過 `runAfter` 屬性 (取代了 `dependsOn`)，執行順序變得更加詳細

如需從 2015-08-01-preview 結構描述的邏輯應用程式升級為 2016-06-01 結構描述的相關資訊，[請查看下面的升級章節。](#upgrading-to-2016-06-01-schema)


## 1\.範圍

此結構描述的其中一項最大變更，就是新增了範圍以及能夠讓動作彼此套疊。在將一組動作群組在一起時，或是需要讓動作彼此套疊 (例如，條件可包含另一個條件) 時，這會很有幫助。範圍語法的詳細資料可在[這裡](app-service-logic-loops-and-scopes.md)找到，但下面有提供範圍的簡單範例︰


```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## 2\.條件和迴圈的變更

在舊版結構描述中，條件和迴圈是與單一動作相關聯的參數。此限制已在本結構描述中解除，現在，條件和迴圈會以某種動作的形式來出現。您可以在[本文](app-service-logic-loops-and-scopes.md)找到詳細資訊，下面則會顯示條件動作的簡單範例︰

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## 3\.RunAfter 屬性

新的 `runAfter` 屬性將會取代 `dependsOn`，以便讓執行順序更加精確。`dependsOn` 是「動作已執行並成功」的代名詞，但是，您常常需要在前一個動作成功、失敗或略過時執行動作。`runAfter` 將可讓您獲得彈性。此物件會指定所有要跟隨執行的動作名稱，並定義可接受的觸發狀態陣列。例如，如果您想要在步驟 A 成功和步驟 B 成功或失敗之後執行，則可建構下列 `runAfter` 屬性︰

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## 升級為 2016-06-01 結構描述

升級為新的 2016-06-01 結構描述只需要幾個步驟。關於結構描述中變更的詳細資料可以在[本文](app-service-logic-schema-2016-04-01.md)找到。升級程序包含執行升級指令碼、儲存為新的邏輯應用程式，以及可能要視需要覆寫舊的邏輯應用程式。

1. 開啟您目前的邏輯應用程式。
1. 按一下工具列中的 [更新結構描述] 按鈕
   
    ![][1]
   
    隨即會傳回升級後的定義。如有需要，您可以將此定義複製並貼到資源定義，但我們**強烈建議**您使用 [另存新檔] 按鈕，以確保連線參考資料在升級後的邏輯應用程式中全都有效。
1. 在升級後的刀鋒視窗工具列中按一下 [另存新檔] 按鈕。
1. 填寫名稱和邏輯應用程式的狀態，並按一下 [建立] 以部署升級的邏輯應用程式。
1. 確認升級後的邏輯應用程式如預期般運作。

    >[AZURE.NOTE] 如果您使用手動或要求觸發程序，新的邏輯應用程式中的回呼 URL 將會變更。使用新的 URL 來確認它能全程正常運作，而且您可以複製現有邏輯應用程式來保留先前的 URL。

1. (選擇性) 使用工具列中的 [複製] 按鈕 (緊鄰上圖中的 [更新結構描述] 圖示)，以新的結構描述版本覆寫先前的邏輯應用程式。如果您想要保留邏輯應用程式的相同資源識別碼，或要求觸發程序 URL，才有必要這麼做。

### 升級工具注意事項

#### 條件對應

此工具會盡力將 true 和 false 分支的動作一起分組在升級後定義的範圍內。明確地說，`@equals(actions('a').status, 'Skipped')` 的設計工具模式應顯示為 `else` 動作。不過，如果工具偵測到無法辨識的模式，將可能會為 true 和 false 分支建立不同的條件。升級後如有需要，可以重新對應動作。

#### ForEach 與條件
  
每個項目有一個條件的 foreach 迴圈，其先前的模式可以複寫在有篩選動作的新結構描述中。升級時應該會自動進行此作業。條件會在 foreach 迴圈之前變成篩選動作 (以便只傳回符合條件的項目陣列)，而且該陣列會傳遞至 foreach 動作。您可以在[本文](app-service-logic-loops-and-scopes.md)檢視這方面的範例

#### 資源標籤

資源標籤會在升級時移除，因此您必須在升級後的工作流程之中再次加以設定。

## 其他變更

### 手動觸發程序已重新命名為要求觸發程序

`manual` 類型已被取代，並重新命名為種類為 `http` 的 `request`。這會與觸發程序用來建置的模式類型更加一致。

### 新的「篩選」動作

如果您正在使用大型陣列並需要將其篩選為一組較少的項目，則可以使用新的「篩選」類型。它能接受陣列和條件，並且會評估每個項目的條件並傳回符合條件的項目陣列。

### ForEach 和 until 動作限制

foreach 和 until 迴圈僅限用於單一動作。

### 動作的 TrackedProperties

動作現在可以有一個稱為 `trackedProperties` 的額外屬性 (`runAfter` 和 `type` 的同層級項目)。此物件會指定要包含在工作流程期間所發出的 Azure 診斷遙測之特定動作的輸入或輸出。例如：

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## 後續步驟
- [使用邏輯應用程式工作流程定義](app-service-logic-author-definitions.md)
- [建立邏輯應用程式部署範本](app-service-logic-create-deploy-template.md)


<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png

<!---HONumber=AcomDC_0727_2016-->