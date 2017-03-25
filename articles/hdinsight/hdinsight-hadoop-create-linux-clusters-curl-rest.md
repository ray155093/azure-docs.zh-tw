---
title: "使用 cURL 和 REST 建立 Azure HDInsight (Hadoop) | Microsoft Docs"
description: "了解如何使用 cURL、Azure Resource Manager 範本以及 Azure REST API 來建立 HDInsight 叢集。 您可以指定叢集類型 (Hadoop、HBase、或 Storm) 或使用指令碼來安裝自訂元件。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 98be5893-2c6f-4dfa-95ec-d4d8b5b7dcb5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: c8ed0760650f7fb3c85eef4ec7f72c60bb0efd00
ms.lasthandoff: 02/21/2017


---
# <a name="create-hdinsight-clusters-using-curl-and-the-azure-rest-api"></a>使用 cURL 和 Azure REST API 建立 HDInsight 叢集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

了解如何使用 Azure Resource Manager 範本和 Azure REST API 來建立 HDInsight 叢集。

Azure REST API 可讓您對裝載於 Azure 平台的服務執行管理作業，包括建立新的資源，例如 HDInsight 叢集。

> [!IMPORTANT]
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* **Azure CLI 2.0** (預覽)。 Azure CLI 用來建立服務主體，此主體再用於為 Azure REST API 的要求產生驗證權杖。 如需有關 Azure CLI 2.0 預覽的詳細資訊，請參閱[開始使用 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)。

* **cURL**。 此公用程式由封裝管理系統提供，也可以從 [http://curl.haxx.se/](http://curl.haxx.se/)下載。

  > [!NOTE]
  > 如果您使用 PowerShell 執行本文件中的命令，您必須先移除依預設建立的 `curl` 別名。 此別名會使用 Invoke-WebRequest，而不是 cURL。 如果您未移除此別名，可能就會收到本文件中所使用之部分命令的錯誤。
  >
  > 若要移除此別名，請從 PowerShell 提示字元執行下列命令：
  >
  > `Remove-item alias:curl`
  >
  > 移除別名後，就應該能夠使用已安裝在您系統上的 cURL 版本。

### <a name="access-control-requirements"></a>存取控制需求

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-template"></a>建立範本

Azure Resource Manager 範本是描述**資源群組**與其中所有資源 (例如 HDInsight) 的 JSON 文件。此範本型方法可讓您在一個範本中定義 HDInsight 所需的資源。

以下是 [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password) 提供的範本和參數檔合併工具，它會使用密碼來建立以 Linux 為基礎的叢集，以保護 SSH 使用者帳戶。

   ```json
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
                           "description": "The location where all azure resources are deployed."
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
                       "clusterVersion": "3.5",
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
   ```

本文件中的步驟引用此範例。 使用您叢集的值來取代 **Parameters** 區段中的範例值。

> [!IMPORTANT]
> 本範本使用 HDInsight 叢集的背景工作節點預設數目 (4)。 如果您規劃 32 個以上的背景工作節點，則必須選取具有至少 8 個核心和 14 GB RAM 的前端節點大小。
>
> 如需節點大小和相關成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="log-in-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶

請依照[開始使用 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) 所述的步驟操作，並使用 `az login` 命令連接到您的訂用帳戶。

## <a name="create-a-service-principal"></a>建立服務主體

> [!NOTE]
> 以下步驟是[使用 Azure CLI 建立用來存取資源的服務主體](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md#create-service-principal-with-password)文件中*使用密碼建立服務主體*一節的簡易版。 這些步驟會建立用來驗證 Azure REST API 的服務主體。

1. 從命令列中，使用下列命令列出您的 Azure 訂用帳戶。

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    在清單中，選取您要使用的訂用帳戶，並記下 **Subscription_ID** 和 __Tenant_ID__ 資料行。 儲存這些值。

2. 使用以下命令，在 Azure Active Directory 中建立應用程式。

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    將 `--display-name`、`--homepage`、`--identifier-uris` 的值取代為您自己的值。 為新的 Active Directory 項目提供密碼。

   > [!NOTE]
   > `--home-page` 和 `--identifier-uris` 值不需參考裝載於網際網路上的實際網頁。 它們必須是唯一的 URI。

   此命令傳回的值是新應用程式的 __App ID__。 儲存這個值。

3. 使用下列命令以 **App ID** 建立服務主體。

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     此命令傳回的值是 __Object ID__。 儲存這個值。

4. 使用 **Object ID** 值將 **Owner** 角色指派給服務主體。 使用稍早取得的**訂用帳戶 ID** 。

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>取得驗證權杖

使用下列命令來擷取驗證權杖︰

    curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=AppID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=password" \
    --data-urlencode "resource=https://management.azure.com/"

    Replace **TenantID**, **AppID**, and **password** with the values obtained or used previously.

    If this request is successful, you receive a 200 series response and the response body contains a JSON document.

    The JSON document returned by this request contains an element named **access_token**. The value of **access_token** is used to authentication requests to the REST API.

   ```json
   {
       "token_type":"Bearer",
       "expires_in":"3599",
       "expires_on":"1463409994",
       "not_before":"1463406094",
       "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
   }
   ```

## <a name="create-a-resource-group"></a>建立資源群組

使用下列命令來建立資源群組。

* 以建立服務主體時所收到的訂用帳戶 ID 取代 **SubscriptionID** 。
* 以上一步收到的存取權杖取代 **AccessToken** 。
* 以您要在其中建立資源群組和資源的資料中心取代 **DataCenterLocation** 。 例如 'South Central US'。
* 以您要用於此群組的名稱取代 **ResourceGroupName** 。

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

如果這個要求成功，您會收到 200 系列的回應，且回應主體會包含 JSON 文件，內含群組的相關資訊。 `"provisioningState"` 項目包含的值為 `"Succeeded"`。

## <a name="create-a-deployment"></a>建立部署

使用下列命令，將範本部署到資源群組。

* 以先前使用的值取代 **SubscriptionID** 和 **AccessToken**。
* 以您在上一節建立的資源群組名稱取代 **ResourceGroupName** 。
* 以您要用於此部署的名稱取代 **DeploymentName** 。

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> 如果您已將範本儲存到檔案，則可使用下列命令，而不是 `-d "{ template and parameters}"`：
>
> `--data-binary "@/path/to/file.json"`

如果這個要求成功，您會收到 200 系列的回應，且回應主體會包含 JSON 文件，內含部署作業的相關資訊。

> [!IMPORTANT]
> 部署已送出，但目前尚未完成。 部署通常需要大約 15 分鐘才會完成。

## <a name="check-the-status-of-a-deployment"></a>檢查部署的狀態

若要檢查部署的狀態，請使用下列命令：

* 以先前使用的值取代 **SubscriptionID** 和 **AccessToken**。
* 以您在上一節建立的資源群組名稱取代 **ResourceGroupName** 。

```bash
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

這個命令會傳回包含部署作業相關資訊的 JSON 文件。 `"provisioningState"` 項目包含部署的狀態。 如果這包含 `"Succeeded"` 的值，則部署已順利完成。

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

