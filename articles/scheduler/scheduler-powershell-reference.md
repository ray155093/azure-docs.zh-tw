---
title: 排程器 PowerShell Cmdlet 參考
description: 排程器 PowerShell Cmdlet 參考
services: scheduler
documentationcenter: .NET
author: krisragh
manager: dwrede
editor: ''

ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: krisragh

---
# 排程器 PowerShell Cmdlet 參考
下表說明並連結至 Azure 排程器中每個主要 Cmdlet 的參考頁面。

若要安裝 Azure PowerShell，並將它與 Azure 訂用帳戶建立關聯，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

如需有關 [Azure PowerShell Cmdlet](https://msdn.microsoft.com/library/mt125356\(v=azure.200\).aspx) 的詳細資訊，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)。

| Cmdlet | Cmdlet 說明 |
| --- | --- |
| [Disable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133\(v=azure.200\).aspx) |停用工作集合。 |
| [Enable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135\(v=azure.200\).aspx) |啟用工作集合。 |
| [Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125\(v=azure.200\).aspx) |取得排程器作業。 |
| [Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132\(v=azure.200\).aspx) |取得工作集合。 |
| [Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126\(v=azure.200\).aspx) |取得工作歷程記錄。 |
| [New-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136\(v=azure.200\).aspx) |建立 HTTP 工作。 |
| [New-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141\(v=azure.200\).aspx) |建立工作集合。 |
| [New-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134\(v=azure.200\).aspx) |建立服務匯流排佇列工作。 |
| [New-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142\(v=azure.200\).aspx) |建立服務匯流排主題工作。 |
| [New-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127\(v=azure.200\).aspx) |建立儲存體佇列工作。 |
| [Remove-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140\(v=azure.200\).aspx) |移除排程器工作。 |
| [Remove-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131\(v=azure.200\).aspx) |移除工作集合。 |
| [Set-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130\(v=azure.200\).aspx) |修改排程器 HTTP 工作。 |
| [Set-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129\(v=azure.200\).aspx) |修改工作集合。 |
| [Set-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143\(v=azure.200\).aspx) |修改服務匯流排佇列工作。 |
| [Set-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137\(v=azure.200\).aspx) |修改服務匯流排主題工作。 |
| [Set-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128\(v=azure.200\).aspx) |修改儲存體佇列工作。 |

如需更多詳細資訊，您可以執行下列任何 Cmdlet：

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## 另請參閱
 [排程器是什麼？](scheduler-intro.md)

 [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)

 [在 Azure 入口網站中開始使用排程器](scheduler-get-started-portal.md)

 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)

 [Azure 排程器 REST API 參考](https://msdn.microsoft.com/library/mt629143)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)

 [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0914_2016-->