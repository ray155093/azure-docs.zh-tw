---
title: "使用範本建立 Azure HDInsight (Hadoop) | Microsoft Docs"
description: "了解如何使用 Azure 資源管理範本建立 Azure HDInsight 的叢集。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/14/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 37567bf014d1deb5bcd36af94924948550d55f8e
ms.lasthandoff: 03/21/2017


---
# <a name="create-hadoop-clusters-in-hdinsight-using-azure-resource-management-templates"></a>使用 Azure 資源管理範本在 HDInsight 中建立 Hadoop 叢集
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

了解如何使用 Azure 資源管理範本建立 HDInsight 叢集。 如需詳細資訊，請參閱 [使用 Azure 資源管理員範本部署應用程式](../azure-resource-manager/resource-group-template-deploy.md)。 若要了解其他叢集建立工具和功能，請按一下此頁面頂端的索引標籤，或參閱[叢集建立方法](hdinsight-hadoop-provision-linux-clusters.md#cluster-creation-methods)。

## <a name="prerequisites"></a>必要條件：
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

開始執行本文中的指示之前，您必須具備以下必要條件：

* [Azure 訂用帳戶](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* Azure PowerShell 和/或 Azure CLI

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

### <a name="access-control-requirements"></a>存取控制需求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-management-templates"></a>資源管理範本
Resource Manager 範本可讓您在單一的協調作業中，輕鬆地為您的應用程式建立 HDInsight 叢集、其依存的資源 (例如預設儲存體帳戶) 和其他資源 (例如使用 Apache Sqoop 所需的 Azure SQL Database)。 在範本中，您會定義應用程式所需的資源，並指定部署參數以針對不同的環境輸入值。 範本由 JSON 與運算式所組成，可讓您用來為部署建構值。

您可以在 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?term=hdinsight)中找到 HDInsight 範本範例。 使用跨平台 [VSCode](https://code.visualstudio.com/#alt-downloads) (具有 [Resource Manager 附檔名](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)) 或文字編輯器，將範本儲存至您工作站上的檔案。 您會了解如何使用各種方法來呼叫此範本。

如需 Resource Manager 範本的詳細資訊，請參閱下列文章：

* [編寫 Azure 資源管理員範本](../azure-resource-manager/resource-group-authoring-templates.md)
* [使用 Azure 資源管理員範本部署應用程式](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>產生範本

使用 Azure 入口網站，您可以設定叢集的所有屬性、儲存範本，然後加以部署。  因此，您可以重複使用範本。

**使用 Azure 入口網站產生範本**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左側功能表上的 [新增]、[智慧 + 分析]，然後按一下 [HDInsight]。
3. 遵循指示輸入屬性。 您可以使用 [快速建立] 或 [自訂] 選項。
4. 在 [摘要] 索引標籤中，按一下 [下載範本和參數]。

    ![HDInsight Hadoop 會建立叢集資源管理範本下載](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-download.png)

    它會列出範本檔案、參數檔案，以及要部署範本的程式碼範例：

    ![HDInsight Hadoop 會建立叢集資源管理範本下載選項](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-download-options.png)

    從這裡開始，您可以下載範本、將它儲存到您的範本庫或部署範本。

    若要存取範本庫中的範本，按一下左側窗格中的 [更多服務]，然後按一下 [範本] (位於 [其他] 類別下方)。

> [!Note]
> 範本必須與參數檔案一起使用。  否則，您可能會得到非預期的結果。  例如，除非您已在下載範本之前指定預設的 clusterKind 屬性值，否則該屬性值一定是 hadoop。



## <a name="deploy-with-powershell"></a>使用 PowerShell 部署

以下程序會在 HDInsight 中建立 Hadoop 叢集：

**使用 Resource Manager 範本部署叢集**

1. 將 [附錄 A](#appx-a-arm-template) 的 JSON 檔案儲存到您的工作站。 在 PowerShell 指令碼中，檔案名稱為「C:\HDITutorials-ARM\hdinsight-arm-template.json」。
2. 視需要設定參數和變數。
3. 使用下列 PowerShell 指令碼執行範本：

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>"
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

    PowerShell 指令碼只會設定叢集名稱。 儲存體帳戶名稱是硬式編碼於範本中。 系統會提示您輸入叢集使用者密碼 (預設使用者名稱為 admin) 和 SSH 使用者密碼 (預設 SSH 使用者名稱為 sshuser)。  

如需詳細資訊，請參閱[使用 PowerShell 進行部署](../azure-resource-manager/resource-group-template-deploy.md#deploy)。

## <a name="deploy-with-cli"></a>使用 CLI 部署
下列範例會藉由呼叫 Resource Manager 範本來建立叢集和其依存的儲存體帳戶與容器：

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"

系統會提示您輸入叢集名稱、叢集使用者密碼 (預設使用者名稱為 admin) 和 SSH 使用者密碼 (預設 SSH 使用者名稱為 sshuser)。 若要提供內嵌參數︰

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{\"clusterName\":{\"value\":\"hdi1229\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"},\"sshPassword\":{\"value\":\"Pass@word1\"}}'

## <a name="deploy-with-rest-api"></a>使用 REST API 進行部署
請參閱 [使用 REST API 進行部署](../azure-resource-manager/resource-group-template-deploy-rest.md)。

## <a name="deploy-with-visual-studio"></a>透過 Visual Studio 部署
有了 Visual Studio，您可以透過其使用者介面建立資源群組專案，並將其部署至 Azure。 選取要包含在您專案中的資源類型後，這些資源會自動新增至資源管理員範本中。 該專案也提供 PowerShell 指令碼來部署範本。

如需搭配資源群組使用 Visual Studio 的簡介，請參閱 [透過 Visual Studio 建立和部署 Azure 資源群組](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

## <a name="next-steps"></a>後續步驟
在本文中，您學到幾種建立 HDInsight 叢集的方法。 若要深入了解，請參閱下列文章：

* 如需透過 .NET 用戶端程式庫部署資源的範例，請參閱 [使用 .NET 程式庫與範本部署資源](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 如需部署應用程式的深入範例，請參閱 [透過可預測方式在 Azure 中佈建和部署微服務](../app-service-web/app-service-deploy-complex-application-predictably.md)。
* 如需將您的方案部署到不同環境的指引，請參閱 [Microsoft Azure 中的開發和測試環境](../solution-dev-test-environments.md)。
* 若要了解 Azure Resource Manager 範本的區段，請參閱 [編寫範本](../azure-resource-manager/resource-group-authoring-templates.md)。
* 如需可在 Azure Resource Manager 範本中使用的函式清單，請參閱 [範本函式](../azure-resource-manager/resource-group-template-functions.md)。

## <a name="appx-a-resource-manager-template"></a>附錄 A：Resource Manager 範本
下列 Azure Resource Manager 範本會建立 Linux 型 Hadoop 叢集與相依的 Azure 儲存體帳戶。

> [!NOTE]
> 範例包括 Hive 中繼存放區和 Oozie 中繼存放區的組態資訊。  請在使用範本之前先移除區段或設定區段。
>
>

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
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
        "defaultValue": "sshuser",
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
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [ "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]" ],
        "tags": {

        },
        "properties": {
            "clusterVersion": "3.4",
            "osType": "Linux",
            "tier": "standard",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                },
                "hive-site": {
                    "javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "javax.jdo.option.ConnectionUserName": "johndole",
                    "javax.jdo.option.ConnectionPassword": "myPassword$"
                },
                "hive-env": {
                    "hive_database": "Existing MSSQL Server database with SQL authentication",
                    "hive_database_name": "myhive20160901",
                    "hive_database_type": "mssql",
                    "hive_existing_mssql_server_database": "myhive20160901",
                    "hive_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "hive_hostname": "myadla0901dbserver.database.windows.net"
                },
                "oozie-site": {
                    "oozie.service.JPAService.jdbc.driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "oozie.service.JPAService.jdbc.url": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "oozie.service.JPAService.jdbc.username": "johndole",
                    "oozie.service.JPAService.jdbc.password": "myPassword$",
                    "oozie.db.schema.name": "oozie"
                },
                "oozie-env": {
                    "oozie_database": "Existing MSSQL Server database with SQL authentication",
                    "oozie_database_name": "myhive20160901",
                    "oozie_database_type": "mssql",
                    "oozie_existing_mssql_server_database": "myhive20160901",
                    "oozie_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "oozie_hostname": "myadla0901dbserver.database.windows.net"
                }            
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
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
                }
            ]
            }
        }
        }
    ],
    "outputs": {
        "cluster": {
        "type": "object",
        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
        }
    }
    }

