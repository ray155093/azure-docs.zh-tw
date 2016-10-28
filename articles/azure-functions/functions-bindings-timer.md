<properties
	pageTitle="Azure Functions 計時器觸發程序 | Microsoft Azure"
	description="了解如何在 Azure Functions 中使用計時器觸發程序。"
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/>

# Azure Functions 計時器觸發程序

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

這篇文章說明如何在 Azure Functions 中設定計時器觸發程序。計時器觸發程序會根據排程，以單次或週期性方式呼叫函數。

[AZURE.INCLUDE [簡介](../../includes/functions-bindings-intro.md)]

## 計時器觸發程序的 function.json

「Function.json」檔案提供排程運算式。例如，下列排程會每分鐘執行函式一次︰

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

計時器觸發程序會自動處理多個執行個體向外延展︰只會跨所有執行個體執行特定計時器函式的單一執行個體。

## 排程運算式的格式

排程運算式是包含 6 個欄位的 [CRON 運算式](http://en.wikipedia.org/wiki/Cron#CRON_expression)︰`{second} {minute} {hour} {day} {month} {day of the week}`。

請注意，許多在線上找到的 cron 運算式都會省略 {秒} 欄位，因此如果您複製了這類運算式，就必須調整出額外的欄位。

以下是一些其他排程運算式的範例：

若要每隔 5 分鐘觸發一次︰

```json
"schedule": "0 */5 * * * *"
```

若要在每小時開始時觸發一次︰

```json
"schedule": "0 0 * * * *",
```

若要每隔 2 小時觸發一次：

```json
"schedule": "0 0 */2 * * *",
```

若要在上午 9 點到下午 5 點之間每隔一小時觸發一次：

```json
"schedule": "0 0 9-17 * * *",
```

若要在每天上午 9:30 觸發一次：

```json
"schedule": "0 30 9 * * *",
```

若要在每個工作天上午 9:30 觸發一次：

```json
"schedule": "0 30 9 * * 1-5",
```

## 計時器觸發程序 C# 程式碼範例

每次觸發函式時，此 C# 程式碼範例會寫入單一記錄檔。

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## 後續步驟

[AZURE.INCLUDE [後續步驟](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->