---
title: "在 HDInsight 中使用空白邊緣節點 | Microsoft Docs"
description: "如何將空白邊緣節點新增至可做為用戶端的 HDInsight 叢集，以及如何測試/主控 HDInsight 應用程式。"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a344a3a05c2a27e6b6500a6560c7fbb2096c9bfc
ms.openlocfilehash: 0303145ba0ce611242f218cdf6a2274e9e393fb4


---
# <a name="use-empty-edge-nodes-in-hdinsight"></a>在 HDInsight 中使用空白邊緣節點
了解如何將空白邊緣節點新增至以 Linux 為基礎的 HDInsight 叢集。 空白邊緣節點是一部 Linux 虛擬機器，其中已安裝及設定和前端節點相同的用戶端工具，但未執行任何 Hadoop 服務。 您可以使用邊緣節點來存取叢集、測試用戶端應用程式，以及裝載用戶端應用程式。 

您可以將空白邊緣節點新增至現有 HDInsight 叢集，以及在建立叢集時新增到新的叢集。 空白邊緣節點是使用 Azure Resource Manager 範本來新增。  下列範例示範如何使用範本來新增︰

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

如範例所示，您可以選擇性地呼叫[指令碼動作](hdinsight-hadoop-customize-cluster-linux.md)來執行其他設定，例如在邊緣節點中安裝 [Apache Hue](hdinsight-hadoop-hue-linux.md)。

建立邊緣節點之後，您可以使用 SSH 連線到邊緣節點，並執行用戶端工具來存取 HDInsight 中的 Hadoop 叢集。

## <a name="add-an-edge-node-to-an-existing-cluster"></a>將邊緣節點新增至現有叢集
在本節中，您會使用 Resource Manager 範本將邊緣節點新增至現有 HDInsight 叢集。  可在 [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode)中找到 Resource Manager 範本。 Resource Manager 範本會呼叫位於 https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh 的指令碼動作指令碼。 此指令碼不會執行任何動作。  其目的只是為了示範從 Resource Manager 範本呼叫指令碼動作。

**將空白邊緣節點新增至現有叢集**

1. 如果您還沒有 HDInsight 叢集，請予以建立。  請參閱 [Hadoop 教學課程：開始在 HDInsight 中使用以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)。
2. 按一下以下影像，在 Azure 入口網站中登入 Azure 並開啟 Azure Resource Manager 範本。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. 設定下列屬性：
   
   * **訂用帳戶**：選取用來建立叢集的 Azure 訂用帳戶。
   * **資源群組**：選取用於現有 HDInsight 叢集的資源群組。
   * **位置**：選取現有 HDInsight 叢集的位置。
   * **叢集名稱**︰輸入現有 HDInsight 叢集的名稱。
   * **邊緣節點大小**︰選取其中一個 VM 大小。
   * **邊緣節點前置詞**︰預設值是 **new**。  若使用預設值，邊緣節點名稱是 **new-edgenode**。  您可以從入口網站自訂前置詞。 您也可以從範本自訂完整名稱。
4. 核取 [我同意上方所述的條款及條件]，然後按一下 [購買] 以建立邊緣節點。

## <a name="add-an-edge-node-when-creating-a-cluster"></a>在建立叢集時新增邊緣節點
在本節中，您會使用 Resource Manager 範本對邊緣節點建立 HDInsight 叢集。  Resource Manager 範本可在 [Azure 快速入門範本資源庫](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)中找到。 Resource Manager 範本會呼叫位於 https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh 的指令碼動作指令碼。 此指令碼不會執行任何動作。  其目的只是為了示範從 Resource Manager 範本呼叫指令碼動作。

**將空白邊緣節點新增至現有叢集**

1. 如果您還沒有 HDInsight 叢集，請予以建立。  請參閱 [Hadoop 教學課程：開始在 HDInsight 中使用以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)。
2. 按一下以下影像，在 Azure 入口網站中登入 Azure 並開啟 Azure Resource Manager 範本。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. 設定下列屬性：
   
   * **訂用帳戶**：選取用來建立叢集的 Azure 訂用帳戶。
   * **資源群組**：建立用於叢集的新資源群組。
   * **位置**：選取資源群組的位置。
   * **叢集名稱**︰輸入要建立之新叢集的名稱。
   * **叢集登入使用者名稱**︰輸入 Hadoop HTTP 使用者名稱。  預設名稱為 **admin**。
   * **叢集登入密碼**︰輸入 Hadoop HTTP 使用者密碼。
   * **SSH 使用者名稱**︰輸入 SSH 使用者名稱。 預設名稱為 **sshuser**。
   * **SSH 密碼**︰輸入 SSH 使用者密碼。
   * **安裝指令碼動作**︰請保留預設值以便完成本教學課程。
     
     某些屬性已被硬式編碼在範本中︰叢集類型、叢集背景工作角色節點計數、邊緣節點大小與邊緣節點名稱。
4. 核取 [我同意上方所述的條款及條件]，然後按一下 [購買] 以建立具有邊緣節點的叢集。

## <a name="access-an-edge-node"></a>存取邊緣節點
邊緣節點 ssh 端點是 &lt;EdgeNodeName>.&lt;ClusterName>-ssh.azurehdinsight.net:22。  例如，new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22。

在 Azure 入口網站上，邊緣節點會顯示為應用程式。  入口網站可提供您相關資訊，以便使用 SSH 存取邊緣節點。

**確認邊緣節點 SSH 端點**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 開啟具有邊緣節點的 HDInsight 叢集。
3. 從叢集刀鋒視窗中，按一下 [應用程式]  。 您應該會看到邊緣節點。  預設名稱為 **new-edgenode**中找到。
4. 按一下邊緣節點。 您應該會看到 SSH 端點。

**在邊緣節點上使用 Hive**

1. 使用 SSH 連線到邊緣節點。  請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md) 或[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)。
2. 在使用 SSH 連線到邊緣節點之後，使用下列命令來開啟 Hive 主控台︰
   
        hive
3. 執行下列命令，以顯示叢集中的 Hive 資料表︰
   
        show tables;

## <a name="delete-an-edge-node"></a>刪除邊緣節點
您可以從 Azure 入口網站刪除邊緣節點。

**存取邊緣節點**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 開啟具有邊緣節點的 HDInsight 叢集。
3. 從叢集刀鋒視窗中，按一下 [應用程式]  。 您應該會看到邊緣節點清單。  
4. 以滑鼠右鍵按一下您想要刪除的邊緣節點，然後按一下 [刪除] 。
5. 按一下 [ **是** ] 以確認。

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何新增邊緣節點以及如何存取邊緣節點。 若要深入了解，請參閱下列文章：

* [安裝 HDInsight 應用程式](hdinsight-apps-install-applications.md)︰了解如何將 HDInsight 應用程式安裝到您的叢集。
* [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何將未發佈的 HDInsight 應用程式部署到 HDInsight。
* [發佈 HDInsight 應用程式](hdinsight-apps-publish-applications.md)︰了解如何將自訂 HDInsight 應用程式發佈至 Azure Marketplace。
* [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)︰了解如何定義 HDInsight 應用程式。
* [使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用指令碼動作來安裝其他應用程式。
* [使用 Resource Manager 範本在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)︰了解如何呼叫 Resource Manager 範本來建立 HDInsight 叢集。




<!--HONumber=Dec16_HO3-->


