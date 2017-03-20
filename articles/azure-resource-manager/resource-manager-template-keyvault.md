---
title: "金鑰保存庫的 Resource Manager 範本 | Microsoft Docs"
description: "說明可透過範本部署金鑰保存庫的資源管理員結構描述。"
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: wpickett
editor: 
ms.assetid: 96433b1a-68ee-4292-85b6-a581618b921b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f2d009477a614c3b2876ce98a355d3775abf772b
ms.openlocfilehash: 04f2d5d8e501ebf41cf95ea925d238f64b096c1d
ms.lasthandoff: 02/27/2017


---
# <a name="key-vault-template-schema"></a>金鑰保存庫範本結構描述
建立金鑰保存庫。

## <a name="schema-format"></a>結構描述格式
若要建立金鑰保存庫，請將下列結構描述新增到範本的資源區段。

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForDiskEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>值
下表描述您在結構描述中必須設定的值。

| 名稱 | 值 |
| --- | --- |
| 類型 |例舉<br />必要<br />**Microsoft.KeyVault/vaults**<br /><br />要建立的資源類型。 |
| apiVersion |例舉<br />必要<br />**2015-06-01** 或 **2014-12-19-preview**<br /><br />要用來建立資源的應用程式開發介面 (API) 版本。 |
| 名稱 |String<br />必要<br />整個 Azure 中唯一的名稱。<br /><br />要建立的金鑰保存庫名稱。 請考慮搭配使用 [uniqueString](resource-group-template-functions.md#uniquestring) 函式與您的命名慣例來建立唯一名稱 (如下面範例所示)。 |
| location |String<br />必要<br />金鑰保存庫的有效區域。 若要判斷有效的區域，請參閱[支援的區域](resource-manager-supported-services.md#supported-regions)。<br /><br />要裝載金鑰保存庫的區域。 |
| properties |Object<br />必要<br />[屬性物件](#properties)<br /><br />指定要建立之金鑰保存庫類型的物件。 |
| 資源 |陣列<br />選用<br />允許的值︰[金鑰保存庫密碼資源](resource-manager-template-keyvault-secret.md)<br /><br />金鑰保存庫的子資源。 |

<a id="properties" />

### <a name="properties-object"></a>屬性物件
| 名稱 | 值 |
| --- | --- |
| enabledForDeployment |Boolean<br />選用<br />**True** 或 **False**<br /><br />指定是否要針對虛擬機器或 Service Fabric 部署啟用保存庫。 |
| enabledForTemplateDeployment |Boolean<br />選用<br />**True** 或 **False**<br /><br />指定是否要啟用保存庫，以便在 Resource Manager 範本部署中使用。 如需詳細資訊，請參閱 [在部署期間傳遞安全值](resource-manager-keyvault-parameter.md) |
| enabledForDiskEncryption |Boolean<br />選用<br />**True** 或 **False**<br /><br />指定是否要啟用保存庫來進行磁碟區加密。 |
| tenantId |String<br />必要<br />**全域唯一識別碼**<br /><br />訂用帳戶的租用戶識別碼。 您可以使用 [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) PowerShell Cmdlet 或 **azure account show** Azure CLI 命令來擷取此識別碼。 |
| accessPolicies |陣列<br />必要<br />[accessPolicies 物件](#accesspolicies)<br /><br />最多 16 個物件的陣列，可指定使用者或服務主體的權限。 |
| sku |Object<br />必要<br />[SKU 物件](#sku)<br /><br />金鑰保存庫的 SKU。 |

<a id="accesspolicies" />

### <a name="propertiesaccesspolicies-object"></a>properties.accessPolicies 物件
| 名稱 | 值 |
| --- | --- |
| tenantId |String<br />必要<br />**全域唯一識別碼**<br /><br />此存取原則中包含 **objectId** 之 Azure Active Directory 租用戶的租用戶識別碼 |
| objectId |String<br />必要<br />**全域唯一識別碼**<br /><br />Azure Active Directory 使用者或服務主體的物件識別碼，其將具備保存庫的存取權。 您可以從 [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) 或 [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) PowerShell Cmdlet，或者 **azure ad user** 或 **azure ad sp** Azure CLI 命令，來擷取此值。 |
| 權限 |Object<br />必要<br />[權限物件](#permissions)<br /><br />為 Active Directory 物件授與此保存庫相關的權限。 |

<a id="permissions" />

### <a name="propertiesaccesspoliciespermissions-object"></a>properties.accessPolicies.permissions 物件
| 名稱 | 值 |
| --- | --- |
| 金鑰 |陣列<br />必要<br />**所有**、**備份**、**建立**、**解密**、**刪除**、**加密**、**取得**、**匯入**、**列出**、**還原**、**簽署**、**解除包裝金鑰**、**更新**、**驗證**、**包裝金鑰**<br /><br />為此 Active Directory 物件授與此保存庫中金鑰相關的權限。 此值必須指定為一個或多個允許值的陣列。 |
| 密碼 |陣列<br />必要<br />**所有**、**刪除**、**取得**、**列出**、**設定**<br /><br />為此 Active Directory 物件授與此保存庫中密碼相關的權限。 此值必須指定為一個或多個允許值的陣列。 |

<a id="sku" />

### <a name="propertiessku-object"></a>properties.sku 物件
| 名稱 | 值 |
| --- | --- |
| 名稱 |例舉<br />必要<br />**standard** 或 **premium** <br /><br />要使用的 KeyVault 服務層。  Standard 支援密碼和軟體保護的金鑰  Premium 會新增對於 HSM 保護之金鑰的支援。 |
| family |例舉<br />必要<br />**A** <br /><br />要使用的 SKU 系列。 |

## <a name="examples"></a>範例
下列範例會部署金鑰保存庫和密碼。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForDiskEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForDiskEncryption": "[parameters('enableVaultForDiskEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

## <a name="quickstart-templates"></a>快速入門範本
下列快速入門範本會部署金鑰保存庫。

* [建立金鑰保存庫](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

## <a name="next-steps"></a>後續步驟
* 如需金鑰保存庫的一般資訊，請參閱[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)。
* 如需部署範本時參考金鑰保存庫密碼的範例，請參閱 [在部署期間傳遞安全值](resource-manager-keyvault-parameter.md)。


