---
title: "Azure Cosmos DB 自動化 - 管理區域 | Microsoft Docs"
description: "使用 Azure CLI 1.0 與 Azure Resource Manager，來管理 Azure Cosmos DB 資料庫帳戶中的區域。"
services: cosmosdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 7f765c17-8549-4108-9475-46394fc3a218
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: dimakwan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 961336e94dca6672dfd6751c4ba27c3997058377
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="automate-azure-cosmos-db-account-region-management-using-azure-cli-10-and-azure-resource-manager-templates"></a>使用 Azure CLI 1.0 和 Azure Resource Manager 範本自動化 Azure Cosmos DB 帳戶的區域管理

本文將示範如何使用 Azure CLI 1.0 命令和 Azure Resource Manager 範本，來新增/移除 Azure Cosmos DB DocumentDB API 帳戶中的區域。 您也可以透過 [Azure 入口網站](../cosmos-db/tutorial-global-distribution-documentdb.md)完成區域管理。 請注意，下列教學課程中的命令不允許您變更不同區域的容錯移轉優先順序。 只能新增或移除讀取區域。 無法新增/移除資料庫帳戶 (容錯移轉優先順序 0) 的寫入區域。

Azure Cosmos DB 資料庫帳戶是目前唯一可以使用 [Azure Resource Manager 範本和 Azure CLI 1.0](documentdb-automation-resource-manager-cli.md) 來建立/修改的 Azure Cosmos DB 資源。 

若要使用 CLI 2.0 來建立 Azure Cosmos DB DocumentDB API、資料表 API、圖形 API 或 Mongo DB 帳戶，請參閱[使用 Azure CLI 建立 Azure DocumentDB 帳戶](documentdb-automation-resource-manager-cli.md)。

## <a name="getting-ready"></a>準備就緒

在您能夠搭配 Azure 資源群組使用 Azure CLI 1.0 之前，必須備妥正確的 Azure CLI 1.0 版本以及 Azure 帳戶。 如果您沒有 Azure CLI 1.0，請[安裝它](../cli-install-nodejs.md)。

### <a name="update-your-azure-cli-10-version"></a>更新 Azure CLI 1.0 版本

在命令提示字元中輸入 `azure --version`，即可以查看您是否已經安裝 0.10.4 版或更新版本。 系統可能會在此步驟中提示您參與 Microsoft Azure CLI 1.0 資料收集，您可以選取 y 或 n 來選擇加入或退出。

    azure --version
    0.10.4 (node: 4.2.4)

如果您的版本不是 0.10.4 或更新版本，就必須[安裝 Azure CLI 1.0](../cli-install-nodejs.md)，或者使用其中一個原生安裝程式來更新，或是透過 **npm**，藉由輸入 `npm update -g azure-cli` 來更新或輸入 `npm install -g azure-cli` 來安裝。

### <a name="set-your-azure-account-and-subscription"></a>設定 Azure 帳戶和訂用帳戶

如果您還沒有 Azure 訂用帳戶，但是有 Visual Studio 訂用帳戶，請啟用您的 [Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。 或者申請 [免費試用](https://azure.microsoft.com/pricing/free-trial/)。

您需要有公司帳戶或學校帳戶或是 Microsoft 帳戶識別碼 ，才能使用 Azure 資源管理範本。 如果您有這其中一個帳戶，請輸入下列命令。

    azure login

這會產生下列輸出： 

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> 如果您沒有 Azure 帳戶，就會看到錯誤訊息，指出您需要不同類型的帳戶。 若要從目前的 Azure 帳戶建立一個帳戶，請參閱 [在 Azure Active Directory 中建立工作或學校身分識別](../virtual-machines/windows/create-aad-work-id.md)。

在瀏覽器中開啟 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ，然後輸入命令輸出中提供的代碼。

![顯示 Microsoft Azure CLI 1.0 之裝置登入畫面的螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

一旦輸入代碼之後，請選取您想要在瀏覽器中使用的身分識別，並視需要提供您的使用者名稱和密碼。

![顯示可在其中選取與您想要使用之 Azure 訂用帳戶相關聯的 Microsoft 身分識別帳戶的螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

當您成功登入之後，將會收到下列確認畫面，接著就能關閉瀏覽器視窗。

![顯示確認登入 Microsoft Azure 跨平台命令列介面的螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

命令殼層也會提供下列輸出。

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

除了此處所述的互動式登入方法之外，還有一些其他的 Azure CLI 1.0 登入方法可供使用。 如需其他方法的詳細資訊以及處理多個訂用帳戶的相關資訊，請參閱 [從 Azure 命令列介面 (Azure CLI 1.0) 連線到 Azure 訂用帳戶](../xplat-cli-connect.md)。

### <a name="switch-to-azure-cli-10-resource-group-mode"></a>切換至 Azure CLI 1.0 資源群組模式

根據預設，Azure CLI 1.0 會在服務管理模式 (**asm** 模式) 下啟動。 輸入下列內容以切換至資源群組模式。

    azure config mode arm

這會提供下列輸出：

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

您可以藉由輸入 `azure config mode asm`，視需要切換回預設的命令集。

### <a name="create-or-retrieve-your-resource-group"></a>建立或擷取您的資源群組

若要建立 Azure Cosmos DB 帳戶，您必須先有資源群組。 如果您已經知道想要使用的資源群組名稱，則跳至 [步驟 2](#create-documentdb-account-cli)。 

若要檢閱您目前所有的資源群組清單，請執行下列命令，並記下您想要使用的資源群組名稱： 

    azure group list

若要建立新的資源群組，請執行下列命令、指定要建立的新資源群組名稱，以及要在其中建立資源群組的區域： 

    azure group create <resourcegroupname> <resourcegrouplocation>

 - `<resourcegroupname>` 只能使用英數字元、句號、底線、'-' 字元和括號，且不能以句號結尾。 
 - `<resourcegrouplocation>` 必須是已正式推出 Azure Cosmos DB 的其中一個區域。 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。

範例輸入：

    azure group create new_res_group westus

這會產生下列輸出：

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

如果您遇到錯誤，請參閱 [疑難排解](#troubleshooting)。 

## <a name="understanding-azure-resource-manager-templates-and-resource-groups"></a>了解 Azure Resource Manager 範本和資源群組

大部分的應用程式在建置時會使用不同資源類型的組合 (例如，一或多個 Azure Cosmos DB 帳戶、儲存體帳戶、虛擬網路或內容傳遞網路)。 預設 Azure 服務管理 API 和 Azure 入口網站可使用 service-by-service 方法代表這些項目。 這個方法會要求您部署和個別管理個別服務 (或尋找執行這項作業的其他工具)，而不是做為部署的單一邏輯單元。

 ，將這些不同的資源宣告為一個邏輯部署單元，然後就能進行部署和管理。 請不要以命令方式告訴 Azure 逐一部署命令，您應該在 JSON 檔案描述整個部署過程 -- 所有資源和相關設定以及部署參數 -- 然後告訴 Azure 將這些資源視為一個群組加以部署。

如需深入了解 Azure 資源群組及其功能，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。 如果您有興趣了解如何編寫範本，請參閱 [編寫 Azure 資源管理員範本](../azure-resource-manager/resource-group-authoring-templates.md)。


## <a id="add-region-documentdb-account"></a>工作：將區域新增至 Azure Cosmos DB DocumentDB API 帳戶

Azure Cosmos DB 能夠跨各個不同的 [Azure 區域](https://azure.microsoft.com/regions/#services)[全域散發您的資料][distribute-globally]。 本節中的指示說明如何使用 Azure CLI 1.0 和 Resource Manager 範本，在現有的 Azure Cosmos DB 帳戶中新增讀取區域。 使用 Azure CLI 1.0 搭配或不搭配 Resource Manager 範本，均可建完成此操作。

### <a id="add-region-documentdb-account-cli"></a>使用 Azure CLI 1.0 不搭配 Resource Manager 範本來將區域新增至 Azure Cosmos DB 帳戶

在命令提示字元中輸入下列命令，在新的或現有的資源群組中現有的 Azure Cosmos DB 帳戶中新增區域。 請注意，"locations" 陣列應反映 Azure Cosmos DB 帳戶內除了要加入之新區域以外的目前區域組態。 下列範例顯示在帳戶中新增第二個區域的命令。

讓容錯移轉優先順序值符合現有的組態。 其中一個區域的 failoverPriority 值必須為 0，以表示將此區域保留為 [Azure Cosmos DB 帳戶的寫入區域][scaling-globally]。 容錯移轉優先順序值在位置之間必須是唯一的，且最高的容錯移轉優先順序值必須小於區域總數。 新的區域將會是「讀取」區域，而且容錯移轉優先順序值必須大於 0。

> [!TIP]
> 如果您在 Azure PowerShell 或 Windows PowerShell 中執行此命令，將會收到關於未預期之權杖的錯誤。 請改為在 Windows 命令提示字元中執行此命令。

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<newdatabaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

 - `<resourcegroupname>` 只能使用英數字元、句號、底線、'-' 字元和括號，且不能以句號結尾。
 - `<resourcegrouplocation>` 是目前資源群組的區域。
 - `<ip-range-filter>` 會以 CIDR 形式指定一組 IP 位址或 IP 位址範圍，以作為所指定資料庫帳戶的允許用戶端 IP 清單。 IP 位址/範圍必須以逗號分隔，而且不得包含任何空格。 如需詳細資訊，請參閱 [Azure Cosmos DB 防火牆支援](documentdb-firewall-support.md)
 - `<databaseaccountname>` 只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。
 - `<databaseaccountlocation>` 必須是已正式推出 Azure Cosmos DB 的其中一個區域。 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。
 - `<newdatabaseaccountlocation>` 是要新增的新區域，而且必須是已正式推出 Azure Cosmos DB 的其中一個區域。 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。


在 Azure Cosmos DB 帳戶中新增 "East US" (美國東部) 區域做為讀取區域的輸入範例： 

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"},{\"locationName\":\"eastus\",\"failoverPriority\":\"1\"}"]}"

若已佈建新的帳戶，這將會產生下列輸出：

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

如果您遇到錯誤，請參閱 [疑難排解](#troubleshooting)。 

在此命令返回之後，該帳戶會有數分鐘的時間處於「正在建立」狀態，然後才會變更成可供使用的「線上」狀態。 您可以在 [Azure 入口網站](https://portal.azure.com)中的 [Azure Cosmos DB 帳戶] 刀鋒視窗上，檢查帳戶的狀態。

### <a id="add-region-documentdb-account-cli-arm"></a>使用 Azure CLI 1.0 搭配 Resource Manager 範本來將區域新增至 Azure Cosmos DB 帳戶

本節中的指示說明如何利用 Azure Resource Manager 範本和選擇性參數檔案 (這兩者都是 JSON 檔案)，在現有的 Azure Cosmos DB 帳戶中新增區域。 使用範本可讓您確實說明所需的資訊，並可重複使用而不會出現任何錯誤。

讓容錯移轉優先順序值符合現有的組態。 其中一個區域的 failoverPriority 值必須為 0，以表示將此區域保留為 [Azure Cosmos DB 帳戶的寫入區域][scaling-globally]。 容錯移轉優先順序值在位置之間必須是唯一的，且最高的容錯移轉優先順序值必須小於區域總數。 新的區域將會是「讀取」區域，而且容錯移轉優先順序值必須大於 0。

建立一個類似下面的本機範本檔案，符合您目前的 Azure Cosmos DB 區域組態。 "locations" 陣列應包含資料庫帳戶中所有現有的區域，以及要加入的新區域。 將檔案命名為 azuredeploy.json。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            },
            "locationName2": {
                "type": "string"
            },
            "newLocationName": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        },
                        {
                            "failoverPriority": 1,
                            "locationName": "[parameters('locationName2')]"
                        },
                        {
                            "failoverPriority": 2,
                            "locationName": "[parameters('newLocationName')]"
                        }
                    ]
                }
            }
        ]
    }

上述的範本檔案範例示範 Azure Cosmos DB 帳戶中要加入新區域的位置已經有 2 個區域。

您可以在命令列中輸入參數值，或是建立參數檔來指定值。

若要建立參數檔案，請將下列內容複製到新檔案，然後將檔案命名為 azuredeploy.parameters.json。 如果您計劃在命令提示字元中指定資料庫帳戶名稱，就可以繼續執行而不需建立此檔案。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            },
            "locationName2": {
                "value": "eastus"
            },
            "newLocationName": {
                "value": "northeurope"
            }
        }
    }

在 azuredeploy.parameters.json 檔案中，將 `"databaseAccountName"` 的值欄位更新成您想要使用的資料庫名稱，然後儲存檔案。 `"databaseAccountName"` 只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。 將 `"locationName1"` 和 `"locationName2"` 的值欄位更新為 Azure Cosmos DB 帳戶所在的區域。 將 `"newLocationName"` 的值欄位更新為您想要新增的區域。

若要在資源群組中建立 Azure Cosmos DB 帳戶，請執行下列命令，並提供範本檔案的路徑、參數檔案的路徑或參數值、要部署於其中的資源群組名稱，以及部署名稱 (-n 是選擇性的)。 

使用參數檔案：

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` 是步驟 1 中建立的 azuredeploy.json 檔案的路徑。 如果您的路徑名稱含有空格，請使用雙引號括住此參數。
 - `<PathToParameterFile>` 是步驟 1 中建立的 azuredeploy.parameters.json 檔案的路徑。 如果您的路徑名稱含有空格，請使用雙引號括住此參數。
 - `<resourcegroupname>` 是要在其中加入 Azure Cosmos DB 資料庫帳戶的現有資源群組名稱。 
 - `<deploymentname>` 是部署的選擇性名稱。

範例輸入： 

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

或者，若要指定資料庫帳戶名稱參數而不使用參數檔案，並且改為取得值的提示，請執行下列命令：

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

範例輸入，其中顯示提示以及名為 samplearmacct 的資料庫帳戶項目：

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

佈建帳戶時，您將會收到下列資訊： 

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    + 
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.Azure Cosmos DB/databaseAccounts' provisioning status is Running
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    data:    newLocationName      String  eastus
    data:    databaseAccountName  String  samplearmacct
    info:    group deployment create command OK

如果您遇到錯誤，請參閱 [疑難排解](#troubleshooting)。  

在此命令返回之後，該帳戶會有數分鐘的時間處於「正在建立」狀態，然後才會變更成可供使用的「線上」狀態。 您可以在 [Azure 入口網站](https://portal.azure.com)中的 [Azure Cosmos DB 帳戶] 刀鋒視窗上，檢查帳戶的狀態。

## <a id="remove-region-documentdb-account"></a>工作︰從 Azure Cosmos DB 帳戶移除區域

Azure Cosmos DB 能夠跨各個不同的 [Azure 區域](https://azure.microsoft.com/regions/#services)[全域散發您的資料][distribute-globally]。 本節中的指示說明如何使用 Azure CLI 1.0 和 Resource Manager 範本，從現有的 Azure Cosmos DB 帳戶移除區域。 使用 Azure CLI 1.0 搭配或不搭配 Resource Manager 範本，均可建完成此操作。

### <a id="remove-region-documentdb-account-cli"></a>使用 Azure CLI 1.0 不搭配 Resource Manager 範本來從 Azure Cosmos DB 帳戶移除區域

若要從現有的 Azure Cosmos DB 帳戶移除區域，可以使用 Azure CLI 1.0 執行下列命令。 "locations" 陣列應只包含移除區域後仍要保留的區域。 **省略的位置將會從 Azure Cosmos DB 帳戶中移除**。 在命令提示字元中輸入下列命令。

其中一個區域的 failoverPriority 值必須為 0，以表示將此區域保留為 [Azure Cosmos DB 帳戶的寫入區域][scaling-globally]。 容錯移轉優先順序值在位置之間必須是唯一的，且最高的容錯移轉優先順序值必須小於區域總數。 

> [!TIP]
> 如果您在 Azure PowerShell 或 Windows PowerShell 中執行此命令，將會收到關於未預期之權杖的錯誤。 請改為在 Windows 命令提示字元中執行此命令。

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

 - `<resourcegroupname>` 只能使用英數字元、句號、底線、'-' 字元和括號，且不能以句號結尾。
 - `<resourcegrouplocation>` 是目前資源群組的區域。
 - `<ip-range-filter>` 會以 CIDR 形式指定一組 IP 位址或 IP 位址範圍，以作為所指定資料庫帳戶的允許用戶端 IP 清單。 IP 位址/範圍必須以逗號分隔，而且不得包含任何空格。 如需詳細資訊，請參閱 [Azure Cosmos DB 防火牆支援](documentdb-firewall-support.md)
 - `<databaseaccountname>` 只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。
 - `<databaseaccountlocation>` 必須是已正式推出 Azure Cosmos DB 的其中一個區域。 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。

範例輸入： 

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"}"]}"

若已佈建新的帳戶，這將會產生下列輸出：

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

如果您遇到錯誤，請參閱 [疑難排解](#troubleshooting)。 

命令返回之後，該帳戶會有數分鐘的時間處於「正在更新」狀態，然後才會變更成可供使用的「線上」狀態。 您可以在 [Azure 入口網站](https://portal.azure.com)中的 [Azure Cosmos DB 帳戶] 刀鋒視窗上，檢查帳戶的狀態。

### <a id="remove-region-documentdb-account-cli-arm"></a>使用 Azure CLI 1.0 搭配 Resource Manager 範本來從 Azure Cosmos DB 帳戶移除區域

本節中的指示說明如何利用 Azure Resource Manager 範本和選擇性參數檔案 (這兩者都是 JSON 檔案)，從現有的 Azure Cosmos DB 帳戶移除區域。 使用範本可讓您確實說明所需的資訊，並可重複使用而不會出現任何錯誤。

其中一個區域的 failoverPriority 值必須為 0，以表示將此區域保留為 [Azure Cosmos DB 帳戶的寫入區域][scaling-globally]。 容錯移轉優先順序值在位置之間必須是唯一的，且最高的容錯移轉優先順序值必須小於區域總數。 

建立一個類似下面的本機範本檔案，符合您目前的 Azure Cosmos DB 區域組態。 "locations" 陣列應只包含移除區域後仍要保留的區域。 **省略的位置將會從 Azure Cosmos DB 帳戶中移除**。 將檔案命名為 azuredeploy.json。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

您可以在命令列中輸入參數值，或是建立參數檔來指定值。

若要建立參數檔案，請將下列內容複製到新檔案，然後將檔案命名為 azuredeploy.parameters.json。 如果您計劃在命令提示字元中指定資料庫帳戶名稱，就可以繼續執行而不需建立此檔案。 請務必在您的 Azure Resource Manager 範本中加入定義的必要參數。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

在 azuredeploy.parameters.json 檔案中，將 `"databaseAccountName"` 的值欄位更新成您想要使用的資料庫名稱，然後儲存檔案。 `"databaseAccountName"` 只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。 將 `"locationName1"` 的值欄位更新為在移除區域之後要讓 Azure Cosmos DB 帳戶存在的區域。

若要在資源群組中建立 Azure Cosmos DB 帳戶，請執行下列命令，並提供範本檔案的路徑、參數檔案的路徑或參數值、要部署於其中的資源群組名稱，以及部署名稱 (-n 是選擇性的)。 

使用參數檔案：

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` 是步驟 1 中建立的 azuredeploy.json 檔案的路徑。 如果您的路徑名稱含有空格，請使用雙引號括住此參數。
 - `<PathToParameterFile>` 是步驟 1 中建立的 azuredeploy.parameters.json 檔案的路徑。 如果您的路徑名稱含有空格，請使用雙引號括住此參數。
 - `<resourcegroupname>` 是要在其中加入 Azure Cosmos DB 資料庫帳戶的現有資源群組名稱。 
 - `<deploymentname>` 是部署的選擇性名稱。

範例輸入： 

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

或者，若要指定資料庫帳戶名稱參數而不使用參數檔案，並且改為取得值的提示，請執行下列命令：

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

範例輸入，其中顯示提示以及名為 samplearmacct 的資料庫帳戶項目：

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

佈建帳戶時，您將會收到下列資訊： 

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    + 
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

如果您遇到錯誤，請參閱 [疑難排解](#troubleshooting)。  

命令返回之後，該帳戶會有數分鐘的時間處於「正在更新」狀態，然後才會變更成可供使用的「線上」狀態。 您可以在 [Azure 入口網站](https://portal.azure.com)中的 [Azure Cosmos DB 帳戶] 刀鋒視窗上，檢查帳戶的狀態。

## <a name="troubleshooting"></a>疑難排解

如果您在建立資源群組或資料庫帳戶時收到錯誤 (例如 `Deployment provisioning state was not successful` )，您有一些疑難排解選項可用。 

> [!NOTE]
> 在資料庫帳戶名稱中提供不正確的字元，或提供無法使用 Azure Cosmos DB 的位置將導致部署錯誤。 資料庫帳戶名稱只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。 所有有效的資料庫帳戶位置都會列在 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)上。

- 如果您的輸出包含下列 `Error information has been recorded to C:\Users\wendy\.azure\azure.err`，則檢閱 azure.err 檔案中的錯誤資訊。

- 您可在資源群組的記錄檔中找到有用的資訊。 若要檢視記錄檔，請執行下列命令：

        azure group log show <resourcegroupname> --last-deployment

    範例輸入：azure group log show new_res_group --last-deployment

    如需詳細資訊，則請參閱 [在 Azure 中疑難排解資源群組部署](../azure-resource-manager/resource-manager-common-deployment-errors.md) 。

- Azure 入口網站中也會提供錯誤資訊，如下列螢幕擷取畫面所示。 若要瀏覽至錯誤資訊：按一下動態工具列中的 [資源群組]、選取發生錯誤的資源群組，接著在 [資源群組] 刀鋒視窗的 [基本功能] 區域中按一下 [上次部署] 的日期，然後在 [部署記錄] 刀鋒視窗中選取失敗的部署，之後在 [部署] 刀鋒視窗中按一下有紅色驚嘆號的 [作業詳細資料]。 失敗部署的狀態訊息會顯示在 [作業詳細資料] 刀鋒視窗中。 

    ![顯示如何瀏覽至部署錯誤訊息的 Azure 入口網站螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png) 

## <a name="next-steps"></a>後續步驟

現在您擁有 Azure Cosmos DB 帳戶，下一個步驟是建立 Azure Cosmos DB 資料庫。 您可以使用下列其中一個動作來建立資料庫：

- Azure 入口網站，如[使用 Azure 入口網站建立 Azure Cosmos DB 集合和資料庫](documentdb-create-collection.md)中所述。
- GitHub 上 [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) 儲存機制之 [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) 專案中的 C# .NET 範例。
- [Azure Cosmos DB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Azure Cosmos DB DocumentDB API 具有 .NET、Java、Python、Node.js 和 JavaScript API SDK。 

建立您的資料庫之後，您必須在資料庫中[新增一或多個集合](documentdb-create-collection.md)，然後在集合中[新增文件](documentdb-view-json-document-explorer.md)。 

當集合中具有文件之後，您便可藉由使用入口網站中的[查詢總管](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 或其中一個 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)，針對文件使用 [SQL](documentdb-sql-query.md) 來[執行查詢](documentdb-sql-query.md#ExecutingSqlQueries)。

如需深入了解 Azure Cosmos DB，請探索這些資源：

-    [Azure Cosmos DB 簡介](../cosmos-db/introduction.md)

如需您可以使用的其他範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet

