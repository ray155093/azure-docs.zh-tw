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
	ms.date="05/16/2016"
	ms.author="chrande"/>

# Azure Functions 計時器觸發程序

這篇文章說明如何在 Azure Functions 中設定計時器觸發程序。計時器觸發程序會根據排程，以單次或週期性方式呼叫函數。

[AZURE.INCLUDE [簡介](../../includes/functions-bindings-intro.md)]

## 計時器觸發程序的 function.json

*function.json* 檔案提供一個排程運算式，以及一個指出是否應立即觸發函數的參數。

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
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

排程運算式可以是包含 6 個欄位的 [CRON 運算式](http://en.wikipedia.org/wiki/Cron#CRON_expression)：{秒} {分鐘} {小時} {日} {月} {星期幾}。許多在線上找到的 cron 運算式都會省略 {秒} 欄位，因此如果您複製了這類運算式，就必須調整出額外的欄位。

排程運算式格式也可以採用 *hh:mm:ss* 格式，以指定每次觸發函數之間的延遲。

以下是一些排程運算式範例。

若要每隔 5 分鐘觸發一次︰

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

若要立即觸發程序，然後每隔兩小時觸發一次︰

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

若要每隔 15 秒觸發一次︰

```json
"schedule": "00:00:15",
"runOnStartup": false,
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

<!---HONumber=AcomDC_0525_2016-->