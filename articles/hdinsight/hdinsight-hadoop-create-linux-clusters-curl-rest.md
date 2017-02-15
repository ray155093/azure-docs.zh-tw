---
title: "在 HDInsight 中使用 cURL 與 Azure REST API 在 Linux 上建立 Hadoop、HBase 或 Storm 叢集 | Microsoft Docs"
description: "了解如何使用 cURL、Azure 資源管理員範本以及 Azure REST API 來建立以 Linux 為基礎的 HDInsight 叢集。 您可以指定叢集類型 (Hadoop、HBase、或 Storm) 或使用指令碼來安裝自訂元件。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 98be5893-2c6f-4dfa-95ec-d4d8b5b7dcb5
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cc59d7785975e3f9acd574b516d20cd782c22dac
ms.openlocfilehash: c7ad743ee68f83853bc450c3ef6ca779e01fd51f


---
# <a name="create-linux-based-clusters-in-hdinsight-using-curl-and-the-azure-rest-api"></a>在 HDInsight 中使用 cURL 和 Azure REST API 建立以 Linux 為基礎的叢集
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure REST API 可讓您對裝載於 Azure 平台的服務執行管理作業，包括建立新的資源，例如以 Linux 為基礎的 HDInsight 叢集。 本文件中，您將了解如何建立 Azure 資源管理員範本來設定 HDInsight 叢集和相關聯的儲存體，然後使用 cURL 將範本部署到 Azure REST API，以建立新的 HDInsight 叢集。

> [!IMPORTANT]
> 本文件中的步驟使用 HDInsight 叢集的背景工作節點預設數目 (4)。 如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB ram 的前端節點大小。
>
> 如需節點大小和相關成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。
>
>

## <a name="prerequisites"></a>必要條件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **Azure CLI**。 Azure CLI 用來建立服務主體，此主體再用於為 Azure REST API 的要求產生驗證權杖。

    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]
* **cURL**。 此公用程式由封裝管理系統提供，也可以從 [http://curl.haxx.se/](http://curl.haxx.se/)下載。

  > [!NOTE]
  > 如果您使用 PowerShell 執行本文件中的命令，您必須先移除依預設建立的 `curl` 別名。 當您從 PowerShell 提示字元使用 `curl` 命令時，此別名會使用 PowerShell Cmdlet 的 Invoke-WebRequest，而不是 cURL，造成本文件中使用的許多命令傳回錯誤。
  >
  > 如果要移除此別名，請從 PowerShell 提示字元執行下列命令：
  >
  > `Remove-item alias:curl`
  >
  > 移除別名後，就應該能夠使用已安裝在您系統上的 cURL 版本。
  >
  >

### <a name="access-control-requirements"></a>存取控制需求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-template"></a>建立範本
Azure 資源管理範本是描述**資源群組**與其中所有資源 (例如 HDInsight) 的 JSON 文件。這個以範本為基礎的方法可讓您在一個範本中定義 HDInsight 所需的所有資源，並透過可將變更套用至群組的**部署**，整體管理群組的變更。

範本通常分成兩個部分：範本自己，以及依您的組態而填入特定值的參數檔。 例如，叢集名稱、系統管理員名稱和密碼。 直接使用 REST API 時，您必須將這些值合併到一個檔案中。 此 JSON 文件的格式為：

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

例如，以下是 [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)提供的範本和參數檔合併工具，它會建立以 Linux 為基礎的叢集，並使用密碼保護 SSH 使用者帳戶。

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {

                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {

                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

本文件中的步驟引用此範例。 在文件的最後，以您要用於叢集的值取代 *Parameters* 區段中的預留位置 **value** 。

## <a name="login-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶
依照[從 Azure 命令列介面 (Azure CLI) 連接到 Azure 訂用帳戶](../xplat-cli-connect.md)中記載的步驟，使用 `azure login` 命令連接到您的訂用帳戶。

## <a name="create-a-service-principal"></a>建立服務主體
> [!NOTE]
> 以下步驟是*使用 Azure CLI 建立用來存取資源的服務主體*文件中[使用密碼建立服務主體](../resource-group-authenticate-service-principal-cli.md#create-service-principal-with-password)一節所提供之資訊的簡易版。 這些步驟可建立新的服務主體，用來驗證用於建立 Azure 資源 (例如 HDInsight 叢集) 的 REST API 要求。
>
>

1. 從命令提示字元、終端機工作階段或殼層，使用下列命令列出您的 Azure 訂用帳戶。

        azure account list

    在清單中，選取您要使用的訂用帳戶，並記下 **Id** 資料行。 這是 **訂用帳戶 ID** ，將用於本文件的大部分步驟中。
2. 在 Azure Active Directory 中建立新的應用程式。

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>

    將 `--name`、`--home-page`、`--identifier-uris` 的值取代為您自己的值。 為新的 Active Directory 項目提供密碼。

   > [!NOTE]
   > 由於您是透過服務主體建立此驗證用應用程式，`--home-page` 和 `--identifier-uris` 值不需要參考架設在網際網路上的實際網頁，只要是唯一 URI 即可。
   >
   >

    儲存傳回資料中的 **AppId** 值。

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
3. 使用先前傳回的 **AppId** 值建立服務主體。

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745

     儲存傳回資料中的 **Object Id** 值。

        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
4. 使用先前傳回的 **Object ID** 值將 **Owner** 角色指派給服務主體。 您也必須使用稍早取得的 **訂用帳戶 ID** 。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/

    此命令完成之後，服務主體就會具備所指定訂用帳戶 ID 的擁有者存取權。

## <a name="get-an-authentication-token"></a>取得驗證權杖
1. 使用下列命令找出訂用帳戶的 **Tenant ID** 。

        azure account show -s <subscription ID>

    在傳回的資料中找到 **Tenant ID**。

        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK
2. 使用 Azure REST API 產生新的權杖。

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"

    以先前取得或使用的值取代 **TenantID**、**AppID**、**password**。

    如果這個要求成功，您會收到 200 系列的回應，且回應主體會包含 JSON 文件。

    這個要求所傳回的 JSON 文件將包含名為 **access_token** 的元素；這個元素的值是存取權杖，必須用來驗證本文件接下來各節所使用的要求。

        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

## <a name="create-a-resource-group"></a>建立資源群組
使用下列命令建立新的資源群組。 您必須先建立群組，才能建立 HDInsight 叢集等資源。

* 以建立服務主體時所收到的訂用帳戶 ID 取代 **SubscriptionID** 。
* 以上一步收到的存取權杖取代 **AccessToken** 。
* 以您要在其中建立資源群組和資源的資料中心取代 **DataCenterLocation** 。 例如 'South Central US'。
* 以您要用於此群組的名稱取代 **ResourceGroupName** 。

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

如果這個要求成功，您會收到 200 系列的回應，且回應主體會包含 JSON 文件，內含群組的相關資訊。 `"provisioningState"` 元素包含的值為 `"Succeeded"`。

## <a name="create-a-deployment"></a>建立部署
使用下列命令將叢集組態 (範本和參數值) 部署到資源群組。

* 以先前使用的值取代 **SubscriptionID** 和 **AccessToken**。
* 以您在上一節建立的資源群組名稱取代 **ResourceGroupName** 。
* 以您要用於此部署的名稱取代 **DeploymentName** 。

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> 如果您已將包含範本和參數的 JSON 文件儲存到檔案，您可以使用下列命令，而不是 `-d "{ template and parameters}"`：
>
> `--data-binary "@/path/to/file.json"`
>
>

如果這個要求成功，您會收到 200 系列的回應，且回應主體會包含 JSON 文件，內含部署作業的相關資訊。

> [!IMPORTANT]
> 請注意，部署已送出，但目前尚未完成。 部署通常需要大約 15 分鐘才會完成。
>
>

## <a name="check-the-status-of-a-deployment"></a>檢查部署的狀態
若要檢查部署的狀態，請使用下列命令：

* 以先前使用的值取代 **SubscriptionID** 和 **AccessToken**。
* 以您在上一節建立的資源群組名稱取代 **ResourceGroupName** 。

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

這會傳回包含部署作業相關資訊的 JSON 文件。 `"provisioningState"` 元素會包含部署的狀態；如果狀態包含的值為 `"Succeeded"`，表示部署已順利完成。 現在，您的叢集應該可供使用。

## <a name="next-steps"></a>後續步驟
既然您已成功建立 HDInsight 叢集，請使用下列內容來了解如何使用您的叢集。

### <a name="hadoop-clusters"></a>Hadoop 叢集
* [〈搭配 HDInsight 使用 Hivet〉](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase 叢集
* [開始在 HDInsight 上使用 HBase](hdinsight-hbase-tutorial-get-started-linux.md)
* [在 HDInsight 上開發適用於 HBase 的 Java 應用程式](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm 叢集
* [在 HDInsight 上開發適用於 Storm 的 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [在 HDInsight 上的 Storm 中使用 Python 元件](hdinsight-storm-develop-python-topology.md)
* [在 HDInsight 上使用 Storm 部署和監視拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)



<!--HONumber=Nov16_HO3-->


