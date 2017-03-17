---
title: "Azure 虛擬機器擴展集：最基本的可行擴展集 | Microsoft Docs"
description: "了解如何建立最基本的可行擴展集範本"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/14/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: eee63d76941f327304348262f00afafe8413cb6b
ms.lasthandoff: 03/09/2017


---

# <a name="about-this-tutorial"></a>關於本教學課程

[Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)是部署相關資源群組的絕佳方式。 本教學課程系列說明如何建立最基本的可行擴展集範本，以及如何修改此範本來配合各種案例。 所有範例皆來自這個 [Github 儲存機制](https://github.com/gatneil/mvss)。 本逐步解說中所示的每項差異都是在此儲存機制的分支之間執行 `git diff` 的結果。 這些範本和差異預期是簡單而非完整的範例。 如需較完整的擴展集範本範例，請參閱 [Azure 快速入門範本 GitHub 儲存機制 (英文)](https://github.com/Azure/azure-quickstart-templates)，然後搜尋包含 `vmss` 字串的資料夾。

## <a name="a-minimum-viable-scale-set"></a>最基本的可用擴展集

如需我們的最基本可用擴展集範本，請參閱[這裡](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json)。 如果您已經熟悉這些範本，您可以放心地跳到＜後續步驟＞一節，了解如何針對其他案例修改此範本。 不過，如果您較不熟悉這些範本，您可能會覺得這個逐步說明相當有用。 為了開始進行，讓我們檢查差異來逐步建立此範本 (`git diff master minimum-viable-scale-set`)：

首先，我們會定義範本的 `$schema` 和 `contentVersion`。 `$schema` 會定義範本語言的版本，並用於 Visual Studio 語法醒目提示及類似的驗證功能。 `contentVersion` 實際上 Azure 完全不使用。 取而代之的是，它是用來協助您記錄這是哪一個範本版本。

```diff
+{
+  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
+  "contentVersion": "1.0.0.0",
```

接著，我們會定義兩個參數：`adminUsername` 和 `adminPassword`。 參數是使用者在部署時指定的值。 `adminUsername` 就是一個 `string`，但由於 `adminPassword` 是密碼，因此我們賦予它的類型是 `securestring`。 稍後，我們會看到這些參數被傳送到擴展集組態。

```diff
+  "parameters": {
+    "adminUsername": {
+      "type": "string"
+    },
+    "adminPassword": {
+      "type": "securestring"
+    }
+  },
```

Resource Manager 範本也可讓您定義以後要在範本中使用的變數。 在此範例中，我們不使用任何變數，因此我們將 JSON 物件保留空白。

```diff
+  "variables": {},
```

接著，我們會有範本的資源，供我們定義實際要部署的項目。 與 `parameters` 和 `variables` (這些是 JSON 物件) 不同，`resources` 是一個 JSON 物件的 JSON 清單。

```diff
+  "resources": [
```

所有資源都必須要有 `type`、`name`、`apiVersion` 及 `location`。 我們的第一個資源的類型為 `Microsft.Network/virtualNetwork`、名稱為 `myVnet` 且 apiVersion 為 `2016-03-30`。 若要了解資源類型的最新 API 版本，請參考 [Azure REST API 文件](https://docs.microsoft.com/rest/api/)。

```diff
+    {
+      "type": "Microsoft.Network/virtualNetworks",
+      "name": "myVnet",
+      "apiVersion": "2016-12-01",
```

為了指定虛擬網路的位置，我們會使用 [Resource Manager 範本函式](../azure-resource-manager/resource-group-template-functions.md)，這些函式必須以引號和方括號括住，就像這樣：`"[<template-function>]"`。 在此案例中，我們使用 resourceGroup 函式，此函式不接受任何引數並且會傳回 JSON 物件，此物件含有這項部署之目的地資源群組的相關中繼資料。 資源群組是由使用者在部署時所設定。 我們會接著使用 `.location` 來為此 JSON 物件編製索引，以從此 JSON 物件取得位置。

```diff
+      "location": "[resourceGroup().location]",
```


每個 Resource Manager 資源都有自己的 `properties` 區段，用於該資源的特定組態。 在此案例中，我們要指定虛擬網路應該有一個使用私人 IP 位址範圍 `10.0.0.0/16` 的子網路。 擴展集一律是包含在一個子網路內。 它不能跨子網路。

```diff
+      "properties": {
+        "addressSpace": {
+          "addressPrefixes": [
+            "10.0.0.0/16"
+          ]
+        },
+        "subnets": [
+          {
+            "name": "mySubnet",
+            "properties": {
+              "addressPrefix": "10.0.0.0/16"
+            }
+          }
+        ]
+      }
+    },
```

除了必要的 `type`、`name`、`apiVersion` 及 `location` 屬性之外，每個資源也可視需要擁有一個 `dependsOn` 字串清單，用來指定部署此資源之前，必須先從這項部署完成哪些其他資源。 在此案例中，這個清單中只有一個元素，即上述的虛擬網路。 之所以指定這項相依性，是因為擴展集必須先有網路存在，才能建立任何 VM。 如此一來，擴展集才能從先前在網路屬性中指定的 IP 位址範圍，為這些 VM 提供私人 IP 位址。 dependsOn 清單中每個字串的格式為 `<type>/<name>` (與我們先前在虛擬網路資源定義中使用的 `type` 和 `name` 相同)。

```diff
+    {
+      "type": "Microsoft.Compute/virtualMachineScaleSets",
+      "name": "myScaleSet",
+      "apiVersion": "2016-04-30-preview",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Network/virtualNetworks/myVnet"
+      ],
```

擴展集需要知道要建立的 VM 大小 (即「SKU 名稱」)，以及要建立多少個這類 VM (即「SKU 容量」)。 若要查看有哪些可用的 VM 大小，請參考 [VM 大小文件](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes)。

```diff
+      "sku": {
+        "name": "Standard_A1",
+        "capacity": 2
+      },
```

擴展集也需要知道如何處理擴展集上的更新。 目前有兩個選項：`Manual` 和 `Automatic`。 如需有關兩者之間差異的詳細資訊，請參閱有關[如何升級擴展集](./virtual-machine-scale-sets-upgrade-scale-set.md)的文件。

```diff
+      "properties": {
+        "upgradePolicy": {
+          "mode": "Manual"
+        },
```

擴展集也需要知道要放置 VM 的作業系統。 在這裡，我們會使用已完全修補的 Ubuntu 16.04-LTS 映像來建立 VM。

```diff
+        "virtualMachineProfile": {
+          "storageProfile": {
+            "imageReference": {
+              "publisher": "Canonical",
+              "offer": "UbuntuServer",
+              "sku": "16.04-LTS",
+              "version": "latest"
+            }
+          },
```

由於擴展集要部署多個 VM，因此我們會指定 `computerNamePrefix`，而不會指定每個 VM 名稱。 擴展集會針對每個 VM 在此名稱前置詞附加索引，因此 VM 名稱的格式會是 `<computerNamePrefix>_<auto-generated-index>`。 在此程式碼片段中，我們也會看到我們使用先前的參數來設定擴展集內所有 VM 的系統管理員使用者名稱和密碼。 我們會使用 `parameters` 範本函式來執行這項操作，此函式會接受一個指定所要參考之參數的字串，然後輸出該參數的值。

```diff
+          "osProfile": {
+            "computerNamePrefix": "vm",
+            "adminUsername": "[parameters('adminUsername')]",
+            "adminPassword": "[parameters('adminPassword')]"
+          },
```

最後，我們需要為擴展集內的 VM 指定網路組態。 在此案例中，我們只需要指定稍早所建立之子網路的識別碼，讓擴展集知道要將網路介面放在此子網路中。 我們可以使用 `resourceId` 範本函式來取得包含該子網路的虛擬網路識別碼。 此函式會接受資源的類型和名稱，然後傳回該資源的完整識別碼 (此識別碼的格式為：`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`)。 不過，只有虛擬網路的識別碼還不夠。 我們必須指定擴展集 VM 應該位於的特定子網路，因此我們會將 `/subnets/mySubnet` 串連至虛擬網路的識別碼。 結果是會產生子網路的完整識別碼。 我們會使用 `concat` 函式來執行這項操作，此函式會接受一系列字串，然後傳回其串連結果。

```diff
+          "networkProfile": {
+            "networkInterfaceConfigurations": [
+              {
+                "name": "myNic",
+                "properties": {
+                  "primary": "true",
+                  "ipConfigurations": [
+                    {
+                      "name": "myIpConfig",
+                      "properties": {
+                        "subnet": {
+                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                        }
+                      }
+                    }
+                  ]
+                }
+              }
+            ]
+          }
+        }
+      }
+    }
+  ]
+}

```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
