---
title: "從 Windows 型 HDInsight 移轉至 Linux 型 HDInsight | Microsoft Docs"
description: "了解如何從 Windows 型 HDInsight 叢集移轉至 Linux 型 HDInsight 叢集。"
services: hdinsight
documentationcenter: 
author: bhanupr
manager: asadk
editor: bhanupr
ms.assetid: 60eb573c-e639-4815-9fc6-ea8b106d8dbc
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2017
ms.author: bhanupr
translationtype: Human Translation
ms.sourcegitcommit: 8c59375290e410c34ba25d4e5d8e8f9f8de0cafe
ms.openlocfilehash: ceb5f5c639633d7118a057927b236b51b54f8fa7
ms.lasthandoff: 02/04/2017


---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>將 HDInsight 叢集升級為更新的版本
若要充分利用最新的 HDInsight 功能，建議您將 HDInsight 叢集升級到最新的版本。 依照下面的指導方針升級您的 HDInsight 叢集版本。

> [!NOTE]
> HDInsight 叢集版本 3.2 與 3.3 已接近淘汰日期。 如需支援之 HDInsight 版本的詳細資訊，請參閱 [HDInsight 元件版本](hdinsight-component-versioning.md#supported-hdinsight-versions)。
>
>

## <a name="upgrade-tasks"></a>升級工作
升級 HDInsight 叢集的工作流程如下。

![升級工作流程圖表](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. 閱讀此文件的每一節，以了解在升級 HDInsight 叢集時，可能需要進行的變更。
2. 將叢集建立為測試/品質保證環境。 如需建立叢集的詳細資訊，請參閱[了解如何建立 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
3. 將現有的作業、資料來源與接收複製到新的環境。 請參閱[將資料複製到測試環境](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment)以取得詳細資料。
4. 執行驗證測試以確保您的工作在新叢集上會如預期般運作。


當您已驗證一切都會如預期般運作之後，請為移轉排定停機時間。 在此停機期間，請執行下列動作：

1.    備份所有儲存在本機叢集節點上的暫時性資料。 例如，如果您的資料是直接儲存在前端節點上。
2.    刪除現有的叢集。
3.    使用與先前叢集所使用之預設資料存放區相同的資料存放區，在具有最新 (或受支援) 之 HDI 版本的 VNET 子網路中建立叢集。 這將能允許新叢集針對現有的生產資料繼續運作。
4.    匯入任何已備份的暫時性資料。
5.    使用新叢集啟動工作/繼續處理。

## <a name="next-steps"></a>後續步驟
* [了解如何建立 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
* [從 Windows 用戶端使用 SSH 連線至 Linux 型叢集](hdinsight-hadoop-linux-use-ssh-windows.md)
* [從 Linux、Unix 或 Mac 用戶端使用 SSH 連線至 Linux 型叢集](hdinsight-hadoop-linux-use-ssh-unix.md)
* [使用 Ambari 管理 Linux 型叢集](hdinsight-hadoop-manage-ambari.md)


