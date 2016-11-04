---
title: Resource Manager 範本的最佳做法 | Microsoft Docs
description: 簡化 Azure Resource Manager 範本的指導方針。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2016
ms.author: tomfitz

---
# 建立 Azure Resource Manager 範本的最佳做法
下列指導方針可協助您建立可靠又容易使用的 Resource Manager 範本。這些指導方針只是提供建議，而不是絕對需求。這些指導方針可能需要根據您的案例稍加變化。

## 資源名稱
您通常會使用以下三種資源名稱︰

1. 必須是唯一的資源名稱。
2. 不需要是唯一的資源名稱，但是您想要提供以有助於識別內容的名稱。
3. 可以是一般的資源名稱。

如需建立命名慣例的說明，請參閱 [Infrastructure naming guidelines (基礎結構命名指導方針)](virtual-machines/virtual-machines-windows-infrastructure-naming-guidelines.md)。如需有關資源名稱限制的詳細資訊，請參閱 [Recommended naming conventions for Azure resources (Azure 資源的建議命名慣例)](./guidance/guidance-naming-conventions.md)。

### 唯一的資源名稱
您必須為任何有資料存取端點的資源類型，提供唯一的資源名稱。一些需要唯一名稱的常見類型包括︰

* 儲存體帳戶
* 網站
* SQL Server
* 金鑰保存庫
* Redis 快取
* 批次帳戶
* 流量管理員
* 搜尋服務
* HDInsight 叢集

此外，儲存體帳戶名稱必須是小寫、長度不超過 24 個字元，而且不包含任何連字號。

您可以建立會使用 [uniqueString()](resource-group-template-functions.md#uniquestring) 函式的變數來產生名稱，而不是為這些資源名稱提供參數，並在部署期間試圖猜測其唯一的名稱。通常，您也會想要將前置詞或後置詞加入 **uniqueString** 結果，以便能查看名稱來更輕鬆地判斷其資源類型。例如，您可以為有下列變數的儲存體帳戶，產生唯一的名稱。

    "variables": {
        "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
    }

有 uniqueString 前置詞的儲存體帳戶，不會在相同的機架上叢集化。

### 用於識別的資源名稱
對於您想要命名但不用保證其唯一性的資源類型，只需提供可識別其內容和資源類型的名稱即可。建議您提供描述性名稱，以協助您從資源名稱清單中辨識該名稱。如果您在部署期間需要不同的資源名稱，請針對該名稱使用參數︰

    "parameters": {
        "vmName": { 
            "type": "string",
            "defaultValue": "demoLinuxVM",
            "metadata": {
                "description": "The name of the VM to create."
            }
        }
    }

如果您不需要在部署期間傳遞名稱，可以使用變數︰

    "variables": {
        "vmName": "demoLinuxVM"
    }

或，硬式編碼的值︰

    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "demoLinuxVM",
      ...
    }

### 一般資源名稱
若為主要透過另一個資源來存取的資源類型，您可以使用在範本中硬式編碼的一般名稱。例如，您可能不會想對 SQL Server 上的防火牆規則提供可自訂的名稱。

    {
        "type": "firewallrules",
        "name": "AllowAllWindowsAzureIps",
        ...
    }

## 參數
1. 儘可能減少使用參數。如果您可以使用變數或常值，請這樣做。只針對下列情況提供參數︰
   
   * 您希望會隨環境改變的設定 (例如，SKU、大小或容量)。
   * 您希望能方便識別而指定的資源名稱。
   * 您經常用來完成其他工作 (例如，系統管理員使用者名稱) 的值。
   * 機密資料 (例如密碼)
   * 建立多個資源類型執行個體時要使用的數字或值陣列。
2. 參數名稱應要放在 **camelCasing** 後面。
3. 在中繼資料中提供每個參數的描述。
   
        "parameters": {
            "storageAccountType": {
                "type": "string",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }
4. 定義參數的預設值 (密碼和 SSH 金鑰除外)。
   
        "parameters": {
            "storageAccountType": {
                "type": "string",
                "defaultValue": "Standard_GRS",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }
5. 所有密碼都要使用 **securestring**。
   
        "parameters": {
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        }
6. 儘可能避免使用參數來指定 **location**。請改為使用資源群組的 location 屬性。針對所有資源使用 **resourceGroup().location** 運算式，就會將範本中的資源當成資源群組部署在相同的位置。
   
        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              ...
          }
        ]
   
     如果只在有限的位置支援某資源類型，請考慮直接在範本中指定有效的位置。如果您必須使用 location 參數，請儘可能和可能位於相同位置的資源一起共用該參數值。這個方法可以將使用者必須為每種資源類型提供位置的情況減至最少。
7. 資源類型的 API 版本要避免使用參數或變數。資源屬性和值可能會隨版本號碼而不同。將 API 版本設定為參數或變數時，程式碼編輯器中的 Intellisense 會無法判斷正確的結構描述。請改為將 API 版本硬式編碼在範本中。

## 變數
1. 您需要在範本中使用一次以上的值，才使用變數。只會使用一次的值，採硬式編碼的值會您的範本較容易閱讀。
2. 您不能使用 variables 區段中的 [reference](resource-group-template-functions.md#reference) 函式。reference 函式會從資源的執行階段狀態衍生其值，但變數是在初始的範本剖析期間解析。請改為直接在範本的 **resources** 或 **outputs** 區段中，建構需要 **reference** 函式的值。
3. 如[資源名稱](#resource-names)中所示，包含變數以用於必須是唯一的資源名稱。
4. 您可以將變數組成複雜物件。您可以使用 **variable.subentry** 格式，參考來自複雜物件的值。群組變數可協助您追蹤相關的變數，並提升範本的可讀性。
   
        "variables": {
            "storage": {
                "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
                "type": "Standard_LRS"
            }
        },
        "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storage').name]",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "sku": {
                  "name": "[variables('storage').type]"
              },
              ...
          }
        ]
   
   > [!NOTE]
   > 複雜物件包含的運算式不可以參考來自複雜物件的值。請針對此目的，定義個別的變數。
   > 
   > 
   
     如需使用複雜物件做為變數的更進階範例，請參閱 [Azure Resource Manager 範本中的共用狀態](best-practices-resource-manager-state.md)。

## 資源
1. 針對範本中的每個資源指定 **comments**，協助其他參與者了解資源的用途。
   
        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "comments": "This storage account is used to store the VM disks",
              ...
          }
        ]
2. 使用標記將中繼資料新增至資源，可讓您新增其他有關資源的資訊。例如，您可以將中繼資料加入資源，提供帳單詳細資料。如需詳細資訊，請參閱[使用標記組織您的 Azure 資源](resource-group-using-tags.md)。
3. 如果您使用「公用端點」 (例如 Blob 儲存體公用端點)，範本中的命名空間「請不要採硬式編碼」。使用 **reference** 函式，動態擷取命名空間。這樣做可讓您將範本部署到不同的公用命名空間環境中，而不需要手動變更範本中的端點。將 apiVersion 設定成和您在範本中用於 storageAccount 相同的版本。
   
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }
   
     如果將該儲存體帳戶部署在相同的範本中，當您參考資源時，即不需要指定提供者命名空間。簡化的語法如下︰
   
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }
   
     如果您使用公用命名空間在範本中設定其他的值，請據以變更以反映相同的 reference 函式。例如，虛擬機器 diagnosticsProfile 的 storageUri 屬性。
   
        "diagnosticsProfile": {
            "bootDiagnostics": {
                "enabled": "true",
                "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
            }
        }
   
     您也可以「參考」不同的資源群組中現有的儲存體帳戶。

        "osDisk": {
            "name": "osdisk", 
            "vhd": {
                "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
            }
        }

1. 只有在應用程式要求時，才將 publicIPAddresses 指派給虛擬機器。若要連線以進行偵錯、管理或系統管理目的，請使用 inboundNatRules、virtualNetworkGateways 或 jumpbox。
   
     如需有關如何連線至虛擬機器的詳細資訊，請參閱︰
   
   * [Running VMs for an N-tier architecture on Azure (在 Azure 上執行多層式架構的 VM)](./guidance/guidance-compute-3-tier-vm.md)
   * [在 Azure Resource Manager 中設定虛擬機器的 WinRM 存取](virtual-machines/virtual-machines-windows-winrm.md)
   * [允許使用 Azure 入口網站從外部存取您的 VM](virtual-machines/virtual-machines-windows-nsg-quickstart-portal.md)
   * [允許使用 PowerShell 從外部存取您的 VM](virtual-machines/virtual-machines-windows-nsg-quickstart-powershell.md)
   * [開啟連接埠與端點](virtual-machines/virtual-machines-linux-nsg-quickstart.md)
2. publicIPAddresses 的 **domainNameLabel** 屬性必須是唯一的。domainNameLabel 的長度必須介於 3 到 63 個字元之間，還要遵循 `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` 這個規則運算式指定的規則。由於在下面的範例中，uniqueString 函式會產生長度為 13 個字元的字串，所以假設已經檢查 dnsPrefixString 前置詞字串不超過 50 個字元且符合上述規則。
   
        "parameters": {
            "dnsPrefixString": {
                "type": "string",
                "maxLength": 50,
                "metadata": {
                    "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
                }
            }
        },
        "variables": {
            "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
        }
3. 將密碼新增至 **customScriptExtension** 時，在 protectedSettings 中使用 **commandToExecute** 屬性。
   
        "properties": {
            "publisher": "Microsoft.OSTCExtensions",
            "type": "CustomScriptForLinux",
            "settings": {
                "fileUris": [
                    "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
                ]
            },
            "protectedSettings": {
                "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
            }
        }
   
   > [!NOTE]
   > 為了確保做為參數傳遞至 virtualMachines/擴充功能的密碼會經過加密，您必須使用相關擴充功能的 protectedSettings 屬性。
   > 
   > 

## 輸出
如果範本建立會任何的新 **publicIPAddresses**，則應要有一個 **output** 區段，提供所建立 IP 位址和完整網域的詳細資料，以在部署之後輕鬆擷取這些詳細資料。參考資源時，請使用當時用來建立該資源的 API 版本。

```
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## 單一範本或巢狀範本
若要部署解決方案，您可以使用單一範本或有多個巢狀範本的主要範本。巢狀範本常見於更進階的案例。巢狀範本包含下列優點︰

1. 可以將解決方案分解為多個目標元件
2. 可以搭配不同的主要範本重複使用巢狀範本

當您決定將範本設計分解成多個巢狀範本時，下列指導方針有助於將設計標準化。這些指導方針是以[設計 Azure Resource Manager 範本模式](best-practices-resource-manager-design-templates.md)文件為基礎。建議的設計包含下列範本。

* **主要範本** (azuredeploy.json)。用於輸入參數。
* **共用的資源範本**。部署所有其他資源可以使用的共用資源 (例如虛擬網路、可用性設定組)。dependsOn 運算式會強制在其他範本之前部署此範本。
* **選擇性資源範本**。根據參數 (例如 jumpbox) 有條件地部署資源
* **成員資源範本**。應用程式層內的每個執行個體類型都有自己的組態。您可以在層次內定義不同的執行個體類型 (例如，第一個執行個體建立新的叢集，而將其他執行個體加入現有的叢集)。每個執行個體類型會有自己的部署範本。
* **指令碼**。廣泛可重複使用的指令碼適用於各種個執行個體類型 (例如，將其他磁碟初始化和格式化)。針對特定自訂用途而建立的自訂指令碼，會隨各種執行個體類型而不同。

![巢狀範本](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

如需詳細資訊，請參閱[透過 Azure 資源管理員使用連結的範本](resource-group-linked-templates.md)。

## 有條件地連結到巢狀範本
您可以在範本的 URI 當中使用參數，有條件地連結到巢狀範本。

    "parameters": {
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },
    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                }
            }
        }
    ]

## 範本格式
1. 最好是透過 JSON 驗證程式來傳遞您的範本，將可能會在部署期間造成錯誤的多餘逗號、括號、括弧移除。請嘗試在您喜愛的編輯環境 (Visual Studio 程式碼、Atom、Sublime Text、Visual Studio 等) 中使用 [JSONlint](http://jsonlint.com/) 或 linter 封裝
2. 也建議您將 JSON 格式化以更方便閱讀。您可以對本機的編輯器使用 JSON 格式器封裝。在 Visual Studio 中，利用 **Ctrl+K、Ctrl+D** 來格式化文件。在 VS Code 中，使用 **Alt+Shift+F**。如果您的本機編輯器並不會格式化文件，您可以使用[線上格式器](https://www.bing.com/search?q=json+formatter)。

## 後續步驟
1. 如需設計虛擬機器解決方案架構的指引，請參閱 [Running a Windows VM on Azure (在 Azure 上執行 Windows VM)](./guidance/guidance-compute-single-vm.md) 和 [Running a Linux VM on Azure (在 Azure 上執行 Linux VM)](./guidance/guidance-compute-single-vm-linux.md)。
2. 如需有關設定儲存體帳戶的詳細資訊，請參閱 [Microsoft Azure 儲存體效能與延展性檢查清單](storage/storage-performance-checklist.md)。
3. 如需虛擬網路的說明，請參閱 [Networking infrastructure guidelines (網路基礎結構指導方針)](virtual-machines/virtual-machines-windows-infrastructure-networking-guidelines.md)。

<!---HONumber=AcomDC_0720_2016-->