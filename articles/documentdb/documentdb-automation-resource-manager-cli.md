---
title: "DocumentDB 自動化 - Resource Manager - CLI | Microsoft Docs"
description: "使用 Azure 資源管理員範本或 CLI 來部署 DocumentDB 資料庫帳戶。 DocumentDB 是適用於 JSON 資料的雲端式 NoSQL 資料庫。"
services: documentdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: eae5eec6-0e27-442c-abfc-ef6b7fd3f8d2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/11/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 0782000e87bed0d881be5238c1b91f89a970682c
ms.openlocfilehash: 686b26f082951a71ad21f653c8086061afa56b59


---
# <a name="automate-documentdb-account-creation-using-azure-cli-and-azure-resource-manager-templates"></a>使用 Azure CLI 和 Azure Resource Manager 範本自動建立 DocumentDB 帳戶
> [!div class="op_single_selector"]
> * [Azure 入口網站](documentdb-create-account.md)
> * [Azure CLI 和 ARM](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

本文將說明如何使用 Azure Resource Manager 範本來建立 Azure DocumentDB 帳戶，或使用「Azure 命令列介面」(CLI) 來直接建立此帳戶。 若要使用 Azure 入口網站建立 DocumentDB 帳戶，請參閱 [使用 Azure 入口網站建立 DocumentDB 資料庫帳戶](documentdb-create-account.md)。

DocumentDB 資料庫帳戶是目前唯一可以使用 Resource Manager 範本和 Azure CLI 來建立的 DocumentDB 資源。

## <a name="getting-ready"></a>準備就緒
在您能夠搭配 Azure 資源群組使用 Azure CLI 之前，必須備妥正確的 Azure CLI 版本以及 Azure 帳戶。 如果您沒有 Azure CLI，請 [安裝它](../xplat-cli-install.md)。

### <a name="update-your-azure-cli-version"></a>更新 Azure CLI 版本
在命令提示字元中輸入 `azure --version`，即可以查看您是否已經安裝 0.10.4 版或更新版本。 系統可能會在此步驟中提示您參與 Microsoft Azure CLI 資料收集，您可以選取 y 或 n 來選擇加入或退出。

    azure --version
    0.10.4 (node: 4.2.4)

如果您的版本不是 0.10.4 或更新版本，就必須[安裝 Azure CLI](../xplat-cli-install.md)，或者使用其中一個原生安裝程式來更新，或是透過 **npm**，藉由輸入 `npm update -g azure-cli` 來更新或輸入 `npm install -g azure-cli` 來安裝。

### <a name="set-your-azure-account-and-subscription"></a>設定 Azure 帳戶和訂用帳戶
如果您還沒有 Azure 訂用帳戶，但是有 Visual Studio 訂用帳戶，請啟用您的 [Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。 或者申請 [免費試用](https://azure.microsoft.com/pricing/free-trial/)。

您需要有公司帳戶或學校帳戶或是 Microsoft 帳戶識別碼 ，才能使用 Azure 資源管理範本。 如果您有這其中一個帳戶，請輸入下列命令：

    azure login

這會產生下列輸出：

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin.
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> 如果您沒有 Azure 帳戶，就會看到錯誤訊息，指出您需要不同類型的帳戶。 若要從目前的 Azure 帳戶建立一個帳戶，請參閱 [在 Azure Active Directory 中建立工作或學校身分識別](../virtual-machines/virtual-machines-windows-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
>
>

在瀏覽器中開啟 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ，然後輸入命令輸出中提供的代碼。

![顯示 Microsoft Azure CLI 之裝置登入畫面的螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

一旦輸入代碼之後，請選取您想要在瀏覽器中使用的身分識別，並視需要提供您的使用者名稱和密碼。

![顯示可在其中選取與您想要使用之 Azure 訂用帳戶相關聯的 Microsoft 身分識別帳戶的螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

當您成功登入之後，將會收到下列確認畫面，接著就能關閉瀏覽器視窗。

![顯示確認登入 Microsoft Azure 跨平台命令列介面的螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

命令殼層也會提供下列輸出：

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

除了此處所述的互動式登入方法之外，還有一些其他的 Azure CLI 登入方法可供使用。 如需其他方法的詳細資訊以及處理多個訂用帳戶的相關資訊，請參閱 [從 Azure 命令列介面 (Azure CLI) 連接到 Azure 訂用帳戶](../xplat-cli-connect.md)。

### <a name="switch-to-the-azure-cli-resource-group-mode"></a>切換至 Azure CLI 資源群組模式
根據預設，Azure CLI 會在服務管理模式 (**asm** 模式) 下啟動。 輸入下列內容以切換至資源群組模式。

    azure config mode arm

這會提供下列輸出：

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

您可以藉由輸入 `azure config mode asm`，視需要切換回預設的命令集。

### <a name="create-or-retrieve-your-resource-group"></a>建立或擷取您的資源群組
若要建立 DocumentDB 帳戶，您必須先有資源群組。 如果您已經知道想要使用的資源群組名稱，則跳至 [步驟 2](#create-documentdb-account-cli)。

若要檢閱您目前所有的資源群組清單，請執行下列命令，並記下您想要使用的資源群組名稱：

    azure group list

若要建立資源群組，請執行下列命令、指定要建立的新資源群組名稱，以及要在其中建立資源群組的區域：

    azure group create <resourcegroupname> <resourcegrouplocation>

* `<resourcegroupname>` 只能使用英數字元、句號、底線、'-' 字元和括號，且不能以句號結尾。
* `<resourcegrouplocation>` 必須是已正式推出 DocumentDB 的其中一個區域。 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。

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

## <a name="understanding-resource-manager-templates-and-resource-groups"></a>了解 Resource Manager 範本和資源群組
大部分的應用程式在建置時會使用不同資源類型的組合 (例如，一或多個 DocumentDB 帳戶、儲存體帳戶、虛擬網路或內容傳遞網路)。 預設 Azure 服務管理 API 和 Azure 入口網站可使用 service-by-service 方法代表這些項目。 這個方法會要求您部署和個別管理個別服務 (或尋找執行這項作業的其他工具)，而不是做為部署的單一邏輯單元。

*Azure Resource Manager 範本*能夠以宣告的方式，讓您部署和管理這些不同的資源成為單一邏輯部署單元。 您不是逐一使用命令以命令方式告訴 Azure 要部署的項目，而是在 JSON 檔案中描述整個部署內容 (所有資源和相關設定，以及部署參數)，然後告訴 Azure 將這些資源當作一個群組來部署。

如需深入了解 Azure 資源群組及其功能，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。 如果您有興趣了解如何編寫範本，請參閱 [編寫 Azure 資源管理員範本](../azure-resource-manager/resource-group-authoring-templates.md)。

## <a name="a-idquick-create-documentdb-accountatask-create-a-single-region-documentdb-account"></a><a id="quick-create-documentdb-account"></a>工作：建立單一區域 DocumentDB 帳戶
使用本節中的指示來建立單一區域 DocumentDB 帳戶。 使用 Azure CLI 搭配或不搭配 Resource Manager 範本，均可建完成此操作。

### <a name="a-idcreate-single-documentdb-account-cli-arma-create-a-single-region-documentdb-account-using-azure-cli-without-resource-manager-templates"></a><a id="create-single-documentdb-account-cli-arm"></a> 使用 Azure CLI 不搭配 Resource Manager 範本來建立單一區域 DocumentDB 帳戶
在命令提示字元中輸入下列命令，於新的或現有的資源群組中建立 DocumentDB 帳戶：

> [!TIP]
> 如果您在 Azure PowerShell 或 Windows PowerShell 中執行此命令，將會收到關於未預期之權杖的錯誤。 請改為在 Windows 命令提示字元中執行此命令。
>
>

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

* `<resourcegroupname>` 只能使用英數字元、句號、底線、'-' 字元和括號，且不能以句號結尾。
* `<resourcegrouplocation>` 是目前資源群組的區域。
* `<ip-range-filter>` 會以 CIDR 形式指定一組 IP 位址或 IP 位址範圍，以作為所指定資料庫帳戶的允許用戶端 IP 清單。 IP 位址/範圍必須以逗號分隔，而且不得包含任何空格。 如需詳細資訊，請參閱 [DocumentDB 防火牆支援](documentdb-firewall-support.md)
* `<databaseaccountname>` 只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。
* `<databaseaccountlocation>` 必須是已正式推出 DocumentDB 的其中一個區域。 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。

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

在此命令返回之後，該帳戶會有數分鐘的時間處於「正在建立」狀態，然後才會變更成可供使用的「線上」狀態。 您可以在 [Azure 入口網站](https://portal.azure.com)中的 [DocumentDB 帳戶] 刀鋒視窗上，檢查帳戶的狀態。

### <a name="a-idcreate-single-documentdb-account-cli-arma-create-a-single-region-documentdb-account-using-azure-cli-with-resource-manager-templates"></a><a id="create-single-documentdb-account-cli-arm"></a> 使用 Azure CLI 搭配 Resource Manager 範本來建立單一區域 DocumentDB 帳戶
本節中的指示說明如何利用 Azure Resource Manager 範本和選擇性參數檔案 (這兩者都是 JSON 檔案) 來建立 DocumentDB 帳戶。 使用範本可讓您確實說明所需的資訊，並可重複使用而不會出現任何錯誤。

建立含有下列內容的本機範本檔案。 將檔案命名為 azuredeploy.json。

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

failoverPriority 必須設定為 0，因為這是單一區域帳戶。 failoverPriority 為 0 表示將此區域保留為 [DocumentDB 帳戶的寫入區域][scaling-globally]。
您可以在命令列中輸入值，或是建立參數檔來指定值。

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
            }
        }
    }

在 azuredeploy.parameters.json 檔案中，將 `"samplearmacct"` 的值欄位更新成您想要使用的資料庫名稱，然後儲存檔案。 `"databaseAccountName"` 只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。 將 `"locationName1"` 的值欄位更新成您要建立 DocumentDB 帳戶的區域。

若要在資源群組中建立 DocumentDB 帳戶，請執行下列命令，並提供範本檔案的路徑、參數檔案的路徑或參數值、要部署於其中的資源群組名稱，以及部署名稱 (-n 是選擇性的)。

使用參數檔案：

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` 是步驟 1 中建立的 azuredeploy.json 檔案的路徑。 如果您的路徑名稱含有空格，請使用雙引號括住此參數。
* `<PathToParameterFile>` 是步驟 1 中建立的 azuredeploy.parameters.json 檔案的路徑。 如果您的路徑名稱含有空格，請使用雙引號括住此參數。
* `<resourcegroupname>` 是要在其中加入 DocumentDB 資料庫帳戶的現有資源群組名稱。
* `<deploymentname>` 是部署的選擇性名稱。

範例輸入：

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

或者，若要指定資料庫帳戶名稱參數而不使用參數檔案，並且改為取得值的提示，請執行下列命令：

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

範例輸入，其中顯示提示以及名為 samplearmacct 的資料庫帳戶項目：

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

佈建帳戶時，您會收到下列資訊：

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

在此命令返回之後，該帳戶會有數分鐘的時間處於「正在建立」狀態，然後才會變更成可供使用的「線上」狀態。 您可以在 [Azure 入口網站](https://portal.azure.com)中的 [DocumentDB 帳戶] 刀鋒視窗上，檢查帳戶的狀態。

## <a name="a-idquick-create-documentdb-with-mongodb-api-accountatask-create-a-single-region-documentdb-with-support-for-mongodb-account"></a><a id="quick-create-documentdb-with-mongodb-api-account"></a>工作：建立支援 MongoDB 的單一區域 DocumentDB 帳戶
您可以使用本節中的指示來建立支援 MongoDB 的單一區域 DocumentDB 帳戶。 使用 Azure CLI 搭配 Resource Manager 範本，即可完成此操作。

### <a name="a-idcreate-single-documentdb-with-mongodb-api-account-cli-arma-create-a-single-region-documentdb-with-support-for-mongodb-account-using-azure-cli-with-resource-manager-templates"></a><a id="create-single-documentdb-with-mongodb-api-account-cli-arm"></a> 使用 Azure CLI 搭配 Resource Manager 範本來建立支援 MongoDB 的單一區域 DocumentDB 帳戶
本節中的指示說明如何利用 Azure Resource Manager 範本和選擇性參數檔案 (這兩者都是 JSON 檔案) 來建立支援 MongoDB 的 DocumentDB 帳戶。 使用範本可讓您確實說明所需的資訊，並可重複使用而不會出現任何錯誤。

建立含有下列內容的本機範本檔案。 將檔案命名為 azuredeploy.json。

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
                "kind": "MongoDB",
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

kind 必須設定為 MongoDB，以指定此帳戶將支援 MongoDB API。 如果未指定任何 kind 屬性，則預設會是原生 DocumentDB 帳戶。

failoverPriority 必須設定為 0，因為這是單一區域帳戶。 failoverPriority 為 0 表示將此區域保留為 [DocumentDB 帳戶的寫入區域][scaling-globally]。
您可以在命令列中輸入值，或是建立參數檔來指定值。

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
            }
        }
    }

在 azuredeploy.parameters.json 檔案中，將 `"samplearmacct"` 的值欄位更新成您想要使用的資料庫名稱，然後儲存檔案。 `"databaseAccountName"` 只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。 將 `"locationName1"` 的值欄位更新成您要建立 DocumentDB 帳戶的區域。

若要在資源群組中建立 DocumentDB 帳戶，請執行下列命令，並提供範本檔案的路徑、參數檔案的路徑或參數值、要部署於其中的資源群組名稱，以及部署名稱 (-n 是選擇性的)。

使用參數檔案：

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` 是步驟 1 中建立的 azuredeploy.json 檔案的路徑。 如果您的路徑名稱含有空格，請使用雙引號括住此參數。
* `<PathToParameterFile>` 是步驟 1 中建立的 azuredeploy.parameters.json 檔案的路徑。 如果您的路徑名稱含有空格，請使用雙引號括住此參數。
* `<resourcegroupname>` 是要在其中加入 DocumentDB 資料庫帳戶的現有資源群組名稱。
* `<deploymentname>` 是部署的選擇性名稱。

範例輸入：

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

或者，若要指定資料庫帳戶名稱參數而不使用參數檔案，並且改為取得值的提示，請執行下列命令：

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

範例輸入，其中顯示提示以及名為 samplearmacct 的資料庫帳戶項目：

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

佈建帳戶時，您會收到下列資訊：

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

在此命令返回之後，該帳戶會有數分鐘的時間處於「正在建立」狀態，然後才會變更成可供使用的「線上」狀態。 您可以在 [Azure 入口網站](https://portal.azure.com)中的 [DocumentDB 帳戶] 刀鋒視窗上，檢查帳戶的狀態。

## <a name="a-idcreate-multi-documentdb-accountatask-create-a-multi-region-documentdb-account"></a><a id="create-multi-documentdb-account"></a>工作：建立多區域 DocumentDB 帳戶
DocumentDB 能夠跨各個不同的 [Azure 區域](https://azure.microsoft.com/regions/#services)[全球發佈您的資料][distribute-globally]。 建立 DocumentDB 帳戶時，可以指定服務要存在於哪些區域。 使用本節中的指示來建立多區域 DocumentDB 帳戶。 使用 Azure CLI 搭配或不搭配 Resource Manager 範本，均可建完成此操作。

### <a name="a-idcreate-multi-documentdb-account-clia-create-a-multi-region-documentdb-account-using-azure-cli-without-resource-manager-templates"></a><a id="create-multi-documentdb-account-cli"></a> 使用 Azure CLI 不搭配 Resource Manager 範本來建立多區域 DocumentDB 帳戶
在命令提示字元中輸入下列命令，於新的或現有的資源群組中建立 DocumentDB 帳戶：

> [!TIP]
> 如果您在 Azure PowerShell 或 Windows PowerShell 中執行此命令，將會收到關於未預期之權杖的錯誤。 請改為在 Windows 命令提示字元中執行此命令。
>
>

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation1>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<databaseaccountlocation2>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

* `<resourcegroupname>` 只能使用英數字元、句號、底線、'-' 字元和括號，且不能以句號結尾。
* `<resourcegrouplocation>` 是目前資源群組的區域。
* `<ip-range-filter>` 會以 CIDR 形式指定一組 IP 位址或 IP 位址範圍，以作為所指定資料庫帳戶的允許用戶端 IP 清單。 IP 位址/範圍必須以逗號分隔，而且不得包含任何空格。 如需詳細資訊，請參閱 [DocumentDB 防火牆支援](documentdb-firewall-support.md)
* `<databaseaccountname>` 只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。
* `<databaseaccountlocation1>` 和 `<databaseaccountlocation2>` 必須是 DocumentDB 已正式運作的區域。 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。

範例輸入：

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

在此命令返回之後，該帳戶會有數分鐘的時間處於「正在建立」狀態，然後才會變更成可供使用的「線上」狀態。 您可以在 [Azure 入口網站](https://portal.azure.com)中的 [DocumentDB 帳戶] 刀鋒視窗上，檢查帳戶的狀態。

### <a name="a-idcreate-multi-documentdb-account-cli-arma-create-a-multi-region-documentdb-account-using-azure-cli-with-resource-manager-templates"></a><a id="create-multi-documentdb-account-cli-arm"></a> 使用 Azure CLI 搭配 Resource Manager 範本來建立多區域 DocumentDB 帳戶
本節中的指示說明如何利用 Azure Resource Manager 範本和選擇性參數檔案 (這兩者都是 JSON 檔案) 來建立 DocumentDB 帳戶。 使用範本可讓您確實說明所需的資訊，並可重複使用而不會出現任何錯誤。

建立含有下列內容的本機範本檔案。 將檔案命名為 azuredeploy.json。

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
                        }
                    ]
                }
            }
        ]
    }

上述範本檔案可用來建立搭配兩個區域的 DocumentDB 帳戶。 若要建立搭配更多區域的帳戶，請將其新增到 "locations" 陣列並新增對應的參數。

其中一個區域的 failoverPriority 值必須為 0，以表示將此區域保留為 [DocumentDB 帳戶的寫入區域][scaling-globally]。 容錯移轉優先順序值在位置之間必須是唯一的，且最高的容錯移轉優先順序值必須小於區域總數。 您可以在命令列中輸入值，或是建立參數檔來指定值。

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
            }
        }
    }

在 azuredeploy.parameters.json 檔案中，將 `"samplearmacct"` 的值欄位更新成您想要使用的資料庫名稱，然後儲存檔案。 `"databaseAccountName"` 只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。 將 `"locationName1"` 和 `"locationName2"` 的值欄位更新成您要建立 DocumentDB 帳戶的區域。

若要在資源群組中建立 DocumentDB 帳戶，請執行下列命令，並提供範本檔案的路徑、參數檔案的路徑或參數值、要部署於其中的資源群組名稱，以及部署名稱 (-n 是選擇性的)。

使用參數檔案：

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` 是步驟 1 中建立的 azuredeploy.json 檔案的路徑。 如果您的路徑名稱含有空格，請使用雙引號括住此參數。
* `<PathToParameterFile>` 是步驟 1 中建立的 azuredeploy.parameters.json 檔案的路徑。 如果您的路徑名稱含有空格，請使用雙引號括住此參數。
* `<resourcegroupname>` 是要在其中加入 DocumentDB 資料庫帳戶的現有資源群組名稱。
* `<deploymentname>` 是部署的選擇性名稱。

範例輸入：

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

或者，若要指定資料庫帳戶名稱參數而不使用參數檔案，並且改為取得值的提示，請執行下列命令：

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

範例輸入，其中顯示提示以及名為 samplearmacct 的資料庫帳戶項目：

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

佈建帳戶時，您會收到下列資訊：

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
    data:    locationName2        String  eastus
    info:    group deployment create command OK

如果您遇到錯誤，請參閱 [疑難排解](#troubleshooting)。  

在此命令返回之後，該帳戶會有數分鐘的時間處於「正在建立」狀態，然後才會變更成可供使用的「線上」狀態。 您可以在 [Azure 入口網站](https://portal.azure.com)中的 [DocumentDB 帳戶] 刀鋒視窗上，檢查帳戶的狀態。

## <a name="troubleshooting"></a>疑難排解
如果您在建立資源群組或資料庫帳戶時收到錯誤 (例如 `Deployment provisioning state was not successful` )，您有一些疑難排解選項可用。

> [!NOTE]
> 在資料庫帳戶名稱中提供不正確的字元，或提供無法使用 DocumentDB 的位置將會導致部署錯誤。 資料庫帳戶名稱只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。 所有有效的資料庫帳戶位置都會列在 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)上。
>
>

* 如果您的輸出包含下列 `Error information has been recorded to C:\Users\wendy\.azure\azure.err`，則檢閱 azure.err 檔案中的錯誤資訊。
* 您可在資源群組的記錄檔中找到有用的資訊。 若要檢視記錄檔，請執行下列命令：

        azure group log show <resourcegroupname> --last-deployment

    範例輸入：

        azure group log show new_res_group --last-deployment

    如需詳細資訊，則請參閱 [在 Azure 中疑難排解資源群組部署](../azure-resource-manager/resource-manager-common-deployment-errors.md) 。
* Azure 入口網站中也會提供錯誤資訊，如下列螢幕擷取畫面所示。 若要瀏覽至錯誤資訊：按一下動態工具列中的 [資源群組]、選取發生錯誤的資源群組，接著在 [資源群組] 刀鋒視窗的 [基本功能] 區域中按一下 [上次部署] 的日期，然後在 [部署記錄] 刀鋒視窗中選取失敗的部署，之後在 [部署] 刀鋒視窗中按一下有紅色驚嘆號的 [作業詳細資料]。 失敗部署的狀態訊息會顯示在 [作業詳細資料] 刀鋒視窗中。

    ![顯示如何瀏覽至部署錯誤訊息的 Azure 入口網站螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## <a name="next-steps"></a>後續步驟
您已有了 DocumentDB 帳戶，下一步是建立 DocumentDB 資料庫。 您可以使用下列其中一個動作來建立資料庫：

* Azure 入口網站，如[使用 Azure 入口網站建立 DocumentDB 集合和資料庫](documentdb-create-collection.md)所述。
* GitHub 上 [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) 儲存機制之 [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) 專案中的 C# .NET 範例。
* [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 DocumentDB 有.NET、Java、Python、Node.js 和 JavaScript API SDK。

建立您的資料庫之後，您必須在資料庫中[新增一或多個集合](documentdb-create-collection.md)，然後在集合中[新增文件](documentdb-view-json-document-explorer.md)。

在集合中有了文件之後，您便可以藉由使用入口網站中的[查詢總管](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 或其中一個 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)，針對文件使用 [DocumentDB SQL](documentdb-sql-query.md) 來[執行查詢](documentdb-sql-query.md#executing-sql-queries)。

若要深入了解 DocumentDB，請探索以下資源：

* [DocumentDB 的學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [DocumentDB 資源模型和概念](documentdb-resources.md)

如需您可以使用的其他範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet



<!--HONumber=Jan17_HO2-->


