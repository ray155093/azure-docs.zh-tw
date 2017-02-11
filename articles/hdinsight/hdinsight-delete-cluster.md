---
title: "如何刪除 HDInsight 叢集 | Microsoft Docs"
description: "您可用來刪除 HDInsight 叢集的各種方式相關資訊。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 67442bf4b04f6f3799d30f7ce26547c8145d9168


---
# <a name="how-to-delete-an-hdinsight-cluster"></a>如何刪除 HDInsight 叢集
HDInsight 叢集的費用是從建立叢集時開始計算，而於刪除叢集時停止，並以每分鐘按比例計費，因此不再使用您的叢集時應該一律刪除。 在本文件中，您將了解如何使用 Azure 入口網站、Azure PowerShell 和 Azure CLI 來刪除叢集。

> [!IMPORTANT]
> 刪除 HDInsight 叢集並不會刪除與叢集相關聯的 Azure 儲存體帳戶。 這可讓您保留及重複使用叢集所儲存的任何資料。
> 
> 

## <a name="azure-portal"></a>Azure 入口網站
1. 登入 [Azure 入口網站](https://portal.azure.com) ，然後選取您的 HDInsight 叢集。 如果您的 HDInsight 叢集並未釘選於儀表板上，您可以使用導覽列右側的搜尋欄位 (放大鏡圖示) 依名稱搜尋它。
   
    ![入口網站搜尋](./media/hdinsight-delete-cluster/navbar.png)
2. 當叢集的刀鋒視窗開啟之後，選取 [刪除] 圖示。 出現提示時，選取 [是] 以刪除叢集。
   
    ![刪除圖示](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell
在 PowerShell 提示中，使用下列命令來刪除叢集︰

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。

## <a name="azure-cli"></a>Azure CLI
在提示中，使用下列命令來刪除叢集︰

    azure hdinsight cluster delete CLUSTERNAME

將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。




<!--HONumber=Nov16_HO3-->


