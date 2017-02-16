---
title: "使用 PowerShell 管理 Azure 服務匯流排 | Microsoft Docs"
description: "使用 PowerShell 指令碼管理服務匯流排"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: aff8e2ce-bc8b-489f-aca9-a18782be0375
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 61f31c8ad0463776937f366d145595f04cc42d2e
ms.openlocfilehash: 24dd8757942488aa8364cc6cf968cfc17299699f


---
# <a name="manage-service-bus-with-powershell"></a>使用 PowerShell 管理服務匯流排
## <a name="overview"></a>概觀
Microsoft Azure PowerShell 是一種指令碼環境，可讓您在 Azure 中用來控制和自動化工作負載的部署與管理。 本文說明如何使用 PowerShell，透過本機 Azure PowerShell 主控台來佈建及管理服務匯流排實體，例如命名空間、佇列和事件中樞。

## <a name="prerequisites"></a>必要條件
開始進行本文章之前，您必須具備下列必要條件：

* Azure 訂用帳戶。 Azure 是訂閱型平台。 如需取得訂用帳戶的詳細資訊，請參閱[購買選項][Purchase Options]、[成員優惠][Member Offers]或[免費試用版][Free Trial]。
* 具備 Azure PowerShell 的電腦。 如需指示，請參閱[安裝和設定 Azure PowerShell][Install and configure Azure PowerShell]。
* 大致了解 PowerShell 指令碼、NuGet 封裝和 .NET Framework。

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>包括對服務匯流排之 .NET 組件的參考
可用於管理服務匯流排的 PowerShell Cmdlet 數目有限。 若要佈建不是透過現有 Cmdlet 公開的實體，您可以使用[服務匯流排 NuGet 套件][Service Bus NuGet package]中的服務匯流排的 .NET 用戶端。

首先，請確定指令碼可以找到與 NuGet 封裝一起安裝的 **Microsoft.ServiceBus.dll** 組件。 為了要有使用彈性，指令碼會執行這些步驟：

1. 判斷叫用的路徑。
2. 周遊路徑，直到找到名為 `packages`的資料夾為止。 當您安裝 NuGet 封裝時，會建立這個資料夾。
3. 以遞迴方式搜尋 `packages` 資料夾，以搜尋名為 **Microsoft.ServiceBus.dll**的組件。
4. 參考組件，以供稍後使用這些類型。

以下是如何使用 PowerShell 指令碼實作這些步驟的方式：

```powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Host "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Host "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>佈建服務匯流排命名空間
兩個 PowerShell Cmdlet 支援服務匯流排命名空間作業。 不需要使用 .NET SDK API，您可以使用 [Get-AzureSBNamespace][Get-AzureSBNamespace] 和 [New-AzureSBNamespace][New-AzureSBNamespace]。

這個範例會在指令碼中建立幾個區域變數：`$Namespace` 和 `$Location`。

* `$Namespace` 為我們想要使用之服務匯流排命名空間的名稱。
* `$Location` 會識別指示碼在其中佈建命名空間的資料中心。
* `$CurrentNamespace` 儲存指令碼擷取 (或建立) 的參考命名空間。

在實際的指令碼中，`$Namespace` 和 `$Location` 可以參數的方式傳遞。

這部分的指令碼會執行下列作業：

1. 嘗試擷取具有所提供之名稱的服務匯流排命名空間。
2. 如果找到命名空間，它會回報找到的項目。
3. 如果找不到命名空間，它會建立命名空間，然後擷取新建立的命名空間。
   
    ```powershell
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
   
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Host "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

若要佈建其他服務匯流排實體，請從 SDK 建立 [NamespaceManager][NamespaceManager] 類別的執行個體。
您可以使用 [Get-AzureSBAuthorizationRule][Get-AzureSBAuthorizationRule] Cmdlet 來擷取用來提供連接字串的授權規則。 我們將會在 `$NamespaceManager` 變數中儲存對 `NamespaceManager` 執行個體的參照。 我們稍後將在指令碼中使用 `$NamespaceManager` 來佈建其他實體。

```powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Host "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Host "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>佈建其他服務匯流排實體
若要佈建其他實體 (例如佇列、主題和「事件中樞」)，請使用[適用於服務匯流排的 .NET API][適用於服務匯流排的 .NET API]。 本文僅著重於事件中樞，但其他實體的步驟很類似。 此外，在此文章的結尾處會參照更多詳細範例 (包括其他實體)。

這部分的指令碼會再建立四個區域變數。 這些變數可用來具現化 `EventHubDescription` 物件。 指令碼會執行下列作業：

1. 使用 `NamespaceManager` 物件時，請查看 `$Path` 所識別的事件中樞是否存在。
2. 如果事件中樞不存在，請建立 `EventHubDescription` 並傳遞給 `NamespaceManager` 類別的 `CreateEventHubIfNotExists` 方法。
3. 判斷事件中樞可用之後，請使用 `ConsumerGroupDescription` 和 `NamespaceManager` 建立取用者群組。
   
    ```powershell
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
   
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Host "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Host "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription)
        Write-Host "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
   
    # Create the consumer group if it doesn't exist
    Write-Host "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription)
    Write-Host "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>將命名空間移轉到另一個 Azure 訂用帳戶
下列的命令順序會從一個 Azure 訂用帳戶的命名空間移到另一個。 若要執行這項作業，命名空間必須已經是作用中，而且執行 PowerShell 命令的使用者必須是來源與目標訂用帳戶的系統管理員。

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>後續步驟
此文章提供您使用PowerShell 佈建服務匯流排實體的基本大綱。 您可以使用 .NET 用戶端程式庫執行的任何動作，也都可以使用 PowerShell 指令碼來執行。

這些部落格文章中有更多詳細的範例可用：

* [如何使用 PowerShell 指令碼來建立服務匯流排佇列、主題及訂閱](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [如何使用 PowerShell 指令碼來建立服務匯流排命名空間與事件中樞](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

也有一些現成的指令碼可供下載：

* [服務匯流排 PowerShell 指令碼](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Purchase Options]: http://azure.microsoft.com/pricing/purchase-options/
[Member Offers]: http://azure.microsoft.com/pricing/member-offers/
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Service Bus NuGet package]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/Get-AzureSBNamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/get-azuresbauthorizationrule
[NamespaceManager]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager



<!--HONumber=Jan17_HO2-->


