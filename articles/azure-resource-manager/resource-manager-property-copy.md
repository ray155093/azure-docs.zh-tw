---
title: "Azure 資源屬性的多個複本 | Microsoft Docs"
description: "在 Azure Resource Manager 範本中，針對資源類型建立多個屬性執行個體。 在此案例中，您必須使用函式而非複製迴圈。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: da79ff58e892bde5a35456992105de9bdbe36364
ms.lasthandoff: 04/18/2017


---
# <a name="create-multiple-instances-of-property-on-resource-type"></a>建立資源類型的多個屬性執行個體
您只能在資源類型上使用 [copy](resource-group-create-multiple.md) ，不能在資源類型內的屬性上使用。 當您想要建立某些項目的多個執行個體，而這些項目屬於資源一部分時，此需求可能會產生問題。 常見的案例是針對一部虛擬機器建立多個資料磁碟。 您不能將複製與資料磁碟搭配使用，因為 dataDisks 是虛擬機器上的屬性，而不是它自己的資源類型。 您要改為建立具有所需磁碟陣列數目的陣列，然後傳入實際要建立的資料磁碟數量。 在虛擬機器定義中，使用 `take` 函式從陣列中取得實際需要的元素數目。

此模式的完整範例會顯示於 [Create a VM with a dynamic selection of data disks (使用動態選取的資料磁碟來建立 VM)](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) 範本中。

部署範本的相關區段如此文章所示。 已移除範本中的部分內容，以便醒目顯示動態建立多個資料磁碟時所涉及的區段。 

## <a name="define-template-with-variable-for-the-property"></a>定義含屬性變數的範本

若要建立含多個資料磁碟的虛擬機器，請新增能定義超過您所需資料磁碟數目的陣列變數。 在參數 `numDataDisks` 中，傳遞實際的資料磁碟數目以完成建立。 在陣列中使用 `take` 來指定要指派給虛擬機器的確切元素數目。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ... /* not all elements shown */
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        "storageProfile": {
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

## <a name="create-multiple-virtual-machines-with-multiple-data-disks"></a>建立含多個資料磁碟的虛擬機器

當您必須為屬性項目數變化不定的資源建立多個執行個體時，您可以使用 **take** 函式和 **copy** 項目。 例如，假設您需要建立多個虛擬機器，但每個虛擬機器的資料磁碟數不同。 

為了對每個資料磁碟賦予名稱以便識別相關聯的虛擬機器，請將您的資料磁碟陣列放在另一個範本。 納入虛擬機器名稱的參數，以及要傳回的資料磁碟數目。 在 [輸出] 區段中，傳回指定項目的數目。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

在父範本中，您要納入虛擬機器數目的參數，以及每個虛擬機器之資料磁碟數目的陣列。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

在 [資源] 區段中，為定義資料磁碟的範本部署多個執行個體。 

```json
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

在 [資源] 區段中，為虛擬機器部署多個執行個體。 對於資料磁碟，參考包含正確資料磁碟數和正確資料磁碟名稱的巢狀部署。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="next-steps"></a>後續步驟
* 如需建立多個資源的執行個體，請參閱[在 Azure Resource Manager 範本中部署資源的多個執行個體](resource-group-create-multiple.md)。
* 若要循序建立重複列舉的資源，請參閱 [Azure Resource Manager 範本的循序迴圈](resource-manager-sequential-loop.md)。
* 若要了解如何部署範本，請參閱 [使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。


