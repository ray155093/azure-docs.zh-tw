<properties 
	pageTitle="匯出 Azure Resource Manager 範本 | Microsoft Azure" 
	description="使用 Azure Resource Manager 從現有資源群組匯出範本。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/10/2016" 
	ms.author="tomfitz"/>

# 從現有資源匯出 Azure Resource Manager 範本

了解如何建構 Azure Resource Manager 範本可能相當艱鉅，但是 Resource Manager 可協助您進行這項工作，方法是讓您從您的訂用帳戶中的現有資源匯出範本。您可以使用產生的範本了解範本語法，或視需要自動重新部署解決方案。

在本教學課程中，您會透過入口網站建立儲存體帳戶，並匯出該儲存體帳戶的範本。然後您會修改資源群組，方法是將虛擬網路新增至其中，並匯出新的範本，表示其目前的狀態。雖然本主題著重於簡化基礎結構，您可以使用這些相同的步驟匯出更複雜的解決方案的範本。

## 建立儲存體帳戶

1. 在 [Azure 入口網站](https://portal.azure.com)中，依序按一下 [新增]、[資料 + 儲存體]，和 [儲存體帳戶]。

      ![建立儲存體](./media/resource-manager-export-template/create-storage.png)

2. 使用名稱 **storage**、您的姓名縮寫和日期建立儲存體帳戶。儲存體帳戶名稱在 Azure 中必須是唯一的，因此如果名稱已在使用中，請嘗試變化。對於資源群組，請使用 **ExportGroup**。您可以對其他屬性使用預設值。選取 [**建立**]。

      ![提供儲存體的值](./media/resource-manager-export-template/provide-storage-values.png)

部署完成後，您的訂用帳戶會包含儲存體帳戶。

## 匯出範本以供部署
   
1. 瀏覽至您的新資源群組的 [資源群組] 刀鋒視窗。您會發現已列出最後部署的結果。選取此連結。

      ![資源群組刀鋒視窗](./media/resource-manager-export-template/resource-group-blade.png)
   
2. 您會看到群組的部署歷程記錄。在您的案例中，可能只列出一個部署。選取此部署。

     ![上次部署](./media/resource-manager-export-template/last-deployment.png)

3. 會顯示部署的摘要。摘要包含部署和其作業的狀態，與您為參數所提供的值。若要查看用於部署的範本，請選取 [檢視範本]。

     ![檢視部署摘要](./media/resource-manager-export-template/deployment-summary.png)

4. Resource Manager 會為您擷取 5 個檔案。如下：

   1. 用於定義解決方案之基礎結構的範本。當您透過入口網站建立儲存體帳戶時，Resource Manager 會使用範本來部署它，並且儲存該範本供日後參考。

   2. 您可以在部署期間用來傳入值的參數檔案。它包含您在第一次部署時提供的值，但是您可以在重新部署範本時變更這些值。

   3. 您可以為了部署範本而使用的 Azure PowerShell 指令碼檔案。

   4. 您可以為了部署範本而使用的 Azure CLI 指令碼檔案。
   
   5. 您可以為了部署範本而使用的 .NET 類別。

     這些檔案可以透過刀鋒視窗的連結取得。依預設，會選取範本。
     
       ![檢視範本](./media/resource-manager-export-template/view-template.png)
     
     請特別注意範本。您的範本應該如下所示：
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "name": {
              "type": "String"
            },
            "accountType": {
              "type": "String"
            },
            "location": {
              "type": "String"
            },
            "encryptionEnabled": {
              "defaultValue": false,
              "type": "Bool"
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "sku": {
                "name": "[parameters('accountType')]"
              },
              "kind": "Storage",
              "name": "[parameters('name')]",
              "apiVersion": "2016-01-01",
              "location": "[parameters('location')]",
              "properties": {
                "encryption": {
                  "services": {
                    "blob": {
                      "enabled": "[parameters('encryptionEnabled')]"
                    }
                  },
                  "keySource": "Microsoft.Storage"
                }
              }
            } 
          ]
        }
   
     請注意，它會定義儲存體帳戶名稱、類型、位置的參數，以及是否啟用加密 (具有預設值 **false**)。在 [資源] 區段中，您會看到要部署之儲存體帳戶的定義。
     
方括弧會包含在部署期間評估的運算式。上述的方括弧運算式是用來在部署期間取得參數值。有更多的運算式可供您使用，而且您稍後會在本主題中看到其他運算式的範例。如需完整清單，請參閱 [Azure Resource Manager 範本函數](resource-group-template-functions.md)。
   
若要深入了解範本的結構，請參閱[編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。

## 新增虛擬網路

您在上一節中下載的範本表示該原始部署的基礎結構，但是它不會計入您在部署後所做的任何變更。為了說明這個問題，讓我們修改資源群組，方法是透過入口網站新增虛擬網路。

1. 在 [資源群組] 刀鋒視窗中，選取 [新增] 並且從可用的資源挑選 [虛擬網路]。
   
2. 將您的虛擬網路命名為 **VNET**，並且對其他屬性使用預設值。選取 [**建立**]。

      ![設定警示](./media/resource-manager-export-template/create-vnet.png)
   
3. 虛擬網路成功部署到您的資源群組之後，請再次查看部署歷程記錄。您現在會看到兩個部署。選取最新的部署。

      ![部署歷程記錄](./media/resource-manager-export-template/deployment-history.png)
   
4. 查看該部署的範本。請注意，它只會定義您為了新增虛擬網路所做的變更。

一般的最佳做法是使用範本，該範本在單一作業中部署您的解決方案的所有基礎結構，而不是記住許多不同的部署範本。


## 匯出資源群組的範本

雖然每個部署只會顯示您對資源群組所做的變更，您隨時可以匯出範本，以顯示整個資源群組的屬性。

1. 若要檢視資源群組的範本，請選取 [匯出範本]。

      ![匯出資源群組](./media/resource-manager-export-template/export-resource-group.png)

2. 您會再次看到 5 個檔案，可用來重新部署解決方案，但是這次的範本有點不同。此範本只包含 2 個參數 (一個用於儲存體帳戶名稱，一個用於虛擬網路名稱)。

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },
        
     Resource Manager 未擷取在部署期間使用的範本。而是根據資源的目前組態產生新的範本。Resource Manager 不知道您想要傳入那些值做為參數，因此它根據資源群組中的值，硬式編碼大部分的值。例如，儲存體帳戶位置和複寫值設為：
     
        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. 下載範本，您就可以在本機使用。

      ![下載範本](./media/resource-manager-export-template/download-template.png)

4. 尋找您所下載的 .zip 檔案，並將內容解壓縮。您可以使用此下載的範本重新部署您的基礎結構。

## 後續步驟

恭喜！ 您已經了解如何從您在入口網站中建立的資源匯出範本。

- 在本教學課程的第二個部分中，您將自訂您剛剛下載的範本，方法是新增更多參數，並且透過指令碼重新部署它。請參閱[自訂和重新部署匯出範本](resource-manager-customize-template.md)。
- 若要查看如何透過 PowerShell 匯出範本，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](powershell-azure-resource-manager.md)。
- 若要查看如何透過 Azure CLI 匯出範本，請參閱[搭配使用 Mac、Linux 和 Windows 適用的 Azure CLI 與 Azure Resource Manager](xplat-cli-azure-resource-manager.md)。 

<!---HONumber=AcomDC_0511_2016-->