---
title: "使用 Azure 受管理的應用程式 | Microsoft Docs"
description: "描述客戶如何從已發佈的檔案建立 Azure 受管理的應用程式。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/17/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 041254b07584a52ae92e603f60a439050b747af1
ms.contentlocale: zh-tw
ms.lasthandoff: 05/18/2017


---
# <a name="consume-an-azure-managed-application"></a>使用 Azure 受管理的應用程式

如[受管理的應用程式概觀](managed-application-overview.md)文章中所述，端對端體驗中有兩種案例。 其中一個是發行者 (或稱為 ISV)，是要建立受管理的應用程式供客戶使用。 第二個是受管理應用程式的客戶或取用者。 本文是涵蓋第二個案例，說明客戶如何使用 ISV 所提供的受管理應用程式。

目前，您可以使用 Azure CLI 或 Azure 入口網站來使用受管理的應用程式。 

## <a name="create-the-managed-application-using-cli"></a>使用 CLI 建立受管理的應用程式 

您必須取得想要使用之設備的設備定義識別碼。

有兩種方式可以使用 Azure CLI 來建立受管理的應用程式。 其中一個是使用一般範本部署命令，另一個是使用僅針對此用途提供的新命令。

### <a name="create-using-template-deployment-command"></a>使用範本部署命令來建立

您要部署廠商所建立的 applianceMainTemplate.json 檔案。

您要建立兩個資源群組。 第一個資源群組是建立設備資源 (Microsoft.Solutions/appliances) 的位置。 第二個資源群組包含 mainTemplate.json 中定義的所有資源。 此資源群組是由 ISV 所管理。

```azurecli
az group create --name mainResourceGroup --location westcentralus    
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> 使用 `westcentralus` 作為資源群組的位置。
>


接下來，使用下列命令在 ainResourceGroup 中部署 applianceMainTemplate.json︰

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri  
```

上述的範本執行時，它會提示您提供範本中所定義的參數值。 除了在範本中佈建資源所需的參數之外，您需要兩個金鑰參數值︰

- managedResourceGroupId - 資源群組的識別碼，當中會建立 applianceMainTemplate.json 中所定義的資源。 識別碼的格式是 `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`。 在上述範例中，則為 `managedResourceGroup` 的識別碼。
- applianceDefinitionId - 受管理應用程式定義資源的識別碼。 這個值是由 ISV 所提供。 

> [!NOTE] 
> ISV 必須將存取權授與建立設備定義資源的資源群組。 會在 ISV 訂用帳戶中建立設備定義資源。 因此，客戶租用戶中的使用者、使用者群組或應用程式需要這項資源的讀取權限。 

順利完成部署之後，您就會看到在 **mainResourceGroup** 中建立了設備資源。 會在 **managedResourceGroup** 中建立 StorageAccount 資源。

### <a name="create-the-managed-application-using-create-command"></a>使用 create 命令來建立受管理的應用程式

您可以使用 `az managedapp create` 命令，從受管理的應用程式定義建立受管理的應用程式。 

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus" 
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401" 
       --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401" 
       --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401" 
       --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}" 
       --debug
```

**appliance-definition-Id** - 在先前步驟中建立之設備定義的資源識別碼。 若要取得這個識別碼，請執行下列命令：

```azurecli
az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
```

此命令會傳回設備定義。 您需要 **Id** 屬性的值。

**managed-rg-id** - 資源群組的名稱，當中會建立 applianceMainTemplate.json 中所定義的所有資源。 此資源群組是受管理的資源群組，並由發行者所管理。 如果它不存在，將會為您建立。

**resource-group** - 資源群組，當中會建立設備資源。 Microsoft.Solutions/appliance 資源存在於此資源群組。 

**parameters** - applianceMainTemplate.json 中所定義之資源所需的參數。

## <a name="create-the-managed-application-using-portal"></a>使用入口網站建立受管理的應用程式

在入口網站中也存在使用 ISV 所發佈之受管理應用程式的支援。 所應遵循的步驟如下︰

從 Azure 入口網站的 [建立] 刀鋒視窗中選取 [受服務類別目錄管理的應用程式] -

![](./media/managed-application-consumption/create-service-catalog-managed-application.png)

接下來，您會看到不同 ISV/合作夥伴所提供之優惠的清單。 選取您想要建立的項目，然後按一下 [建立]

![](./media/managed-application-consumption/select-offer.png)

按一下 [建立] 之後，在開啟的刀鋒視窗中提供佈建資源所需的參數。 

![](./media/managed-application-consumption/input-parameters.png)

提供值之後，按一下 [確定]。 會依據您所提供的輸入將範本進行驗證。 如果驗證成功，就會啟動範本部署。 部署完成之後，範本中定義的適當資源會佈建在您提供的受管理資源群組。

## <a name="known-issues"></a>已知問題

此預覽版本包含下列問題︰

* 如果您在設備建立期間收到 500 個內部伺服器錯誤，可能是間歇性問題。 如果您遇到這個問題，請重試作業。
* 受管理的資源群組需要新的資源群組。 使用現有的資源群組會導致部署失敗。
* 包含 Microsoft.Solutions/appliances 資源的資源群組應該建立在 **westcentralus** 位置中。

## <a name="next-steps"></a>後續步驟

* 如需受管理應用程式的簡介，請參閱 [Azure 受管理的應用程式概觀](managed-application-overview.md)。
* 若要了解廠商體驗，請參閱[建立及發佈 Azure 受管理應用程式](managed-application-publishing.md)。
