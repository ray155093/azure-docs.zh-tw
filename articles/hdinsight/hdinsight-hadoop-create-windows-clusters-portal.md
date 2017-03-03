---
title: "在 HDInsight 中建立 Windows 型 Hadoop 叢集 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站在 HDInsight 中建立 Windows 型 Hadoop。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ede2e4ec5f3414d1c8a17f4c120011eba0d9a6ca
ms.openlocfilehash: a9d7a4372693ad021a898fae9818f2d037d42547
ms.lasthandoff: 01/20/2017


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>使用 Azure 入口網站在 HDInsight 中建立 Windows 型 Hadoop 叢集

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

了解如何使用 Azure 入口網站在 HDInsight 中建立 Windows 型 Hadoop 叢集。 

本文的資訊僅適用於以 Windows 為基礎的 HDInsight 叢集。 如需建立 Linux 型叢集的詳細資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立 Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。

> [!IMPORTANT]
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

## <a name="prerequisites"></a>必要條件：
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

開始執行本文中的指示之前，您必須擁有以下項目：

* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

### <a name="access-control-requirements"></a>存取控制需求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>建立叢集
**建立 HDInsight 叢集**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下 [新增]、[智慧 + 分析] 及 [HDInsight]。
3. 輸入或選取下列值：
   
   * **叢集名稱**︰命名您的叢集。
   * **訂用帳戶**：選取用於此叢集的 Azure 訂用帳戶。
   * **叢集組態**：

     * **叢集類型**：請為本教學課程選取 [Hadoop]。
     * **作業系統**：請為本教學課程選取 [Windows]。
     * **版本**︰選取 [Hadoop 2.7.0 (HDI 3.3)]。  這是 Windows 型 Hadoop 叢集的最新版本。
     * **叢集層**：請為本教學課程選取 [標準]。

   * **認證**：

     * **叢集登入使用者名稱**︰預設名稱是**admin**。 
     * **叢集登入密碼**：輸入一個密碼。
     * **啟用遠端桌面**︰本教學課程不需要遠端桌面。

   * **資料來源**：

     * **選取方法**︰選取 [來自所有訂用帳戶]。
     * **選取儲存體帳戶**︰按一下 [新建]，然後輸入預設儲存體帳戶的名稱。
     * **預設容器**︰根據預設，預設容器會使用和叢集相同的名稱。
     * **位置**：選擇靠近您的位置。  叢集和預設儲存體帳戶都會使用這位置。
   * **叢集大小**：

     * **背景工作節點數**︰本教學課程只需要 1 個節點。
   * **進階組態**︰您可以在本教學課程跳過這個部分。
   * **資源群組**：選取 [新建]，然後輸入一個名稱。

4. 選取 [釘選到儀表板]，然後按一下 [建立]。 如果您選取 [釘選到「開始面板」]，您入口網站的「開始面板」會新增該叢集的磚。 大約需要 15-20 分鐘的時間來建立叢集。使用「開始面板」上的磚，或頁面左邊的 [通知] 項目，以檢查佈建進度。
5. 建立完成後，在「開始面板」按一下該叢集磚，以啟動叢集刀鋒視窗。 此叢集刀鋒視窗提供該叢集的基本資訊，如名稱、其所屬的資源群組、位置、作業系統、叢集儀表板 URL 等。

## <a name="customize-clusters"></a>自訂叢集
* 請參閱 [使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md)。
* 請參閱 [使用指令碼動作自訂 Windows 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。

## <a name="next-steps"></a>後續步驟
在本文中，您學到幾種建立 HDInsight 叢集的方法。 若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - 了解如何開始使用 HDInsight 叢集
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md) - 了解如何以程式設計方式提交工作至 HDInsight
* [使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集](hdinsight-administer-use-management-portal.md)


