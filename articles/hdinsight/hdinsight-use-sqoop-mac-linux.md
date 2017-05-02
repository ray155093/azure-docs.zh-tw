---
title: "在以 Linux 為基礎的 HDInsight 上使用 Hadoop Sqoop | Microsoft Docs"
description: "了解如何在 HDInsight 叢集上 Linux 架構的 Hadoop 與 Azure SQL Database 之間執行 Sqoop 匯入和匯出。"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 6bb8058a74d3417c4972a9010ac9e17739f3e323
ms.lasthandoff: 04/17/2017


---
# <a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>在 HDInsight 上將 Sqoop 與 Hadoop 搭配使用 (SSH)

[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Sqoop，在 HDInsight 叢集與 Azure SQL Database 或 SQL Server 資料庫之間進行匯入和匯出。

> [!IMPORTANT]
> 本文件中的步驟只適用於使用 Linux 的 HDInsight 叢集。 Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 元件版本控制](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)。

## <a name="install-freetds"></a>安裝 FreeTDS

1. 使用 SSH 來連線至 Linux 架構的 HDInsight 叢集。 連接時要使用的位址為 `CLUSTERNAME-ssh.azurehdinsight.net`，而連接埠為 `22`。

    如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用下列命令來安裝 FreeTDS：

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    在數個步驟中將使用 FreeTDS 來連接到 SQL Database。

## <a name="create-the-table-in-sql-database"></a>在 SQL Database 中建立資料表

> [!IMPORTANT]
> 如果您使用在[建立叢集與 SQL database](hdinsight-use-sqoop.md) 中建立的 HDInsight 叢集和 SQL 資料庫，請略過這一節的步驟。 在[建立叢集與 SQL database](hdinsight-use-sqoop.md) 文件的步驟中所建立的資料庫和資料表。

1. 從 SSH 工作階段中，使用下列命令來連接到 SQL Database 伺服器。

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    您會收到如以下文字的輸出：

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

2. 在 `1>` 提示字元輸入下列查詢：

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    輸入 `GO` 陳述式後，將評估先前的陳述式。 首先，建立 **mobiledata** 資料表，然後將叢集索引加入至該資料表 (SQL Database 所需)。

    使用下列查詢來確認已建立資料表：

        SELECT * FROM information_schema.tables
        GO

    您會看到類似以下的文字：

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

3. 在 `exit` at the `1>` 以結束 tsql 公用程式。

## <a name="sqoop-export"></a>Sqoop export

1. 從對 HDInsight 的 SSH 連線中，使用下列命令來確認 Sqoop 看得見您的 SQL Database：

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    這個命令會傳回一份資料庫清單，其中包含您稍早建立的 **sqooptest** 資料庫。

2. 使用以下命令，將資料從 **hivesampletable** 匯出至 **mobiledata** 資料表：

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    這個命令會指示 Sqoop 連接至 **sqooptest** 資料庫。 Sqoop 接著會將從 **wasbs:///hive/warehouse/hivesampletable** 匯出的資料匯出至 **mobiledata** 資料表。

3. 在命令完成後，使用下列命令連接至使用 TSQL 的資料庫：

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    連線之後，使用下列陳述式來確認資料已匯出到 **mobiledata** 資料表：

        SELECT * FROM mobiledata
        GO

    您應會看到資料表中的資料清單。 輸入 `exit` 以結束 tsql 公用程式。

## <a name="sqoop-import"></a>Sqoop import

1. 使用下列命令將資料從 SQL Database 中的 **mobiledata** 資料表匯入至 HDInsight 上的 **wasbs:///tutorials/usesqoop/importeddata** 目錄：

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    資料中的欄位是以定位字元分隔，行是以換行字元終止。

2. 匯入完成後，使用下列命令來列出新目錄中的資料：

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

## <a name="using-sql-server"></a>使用 SQL Server

您也可以使用 Sqoop 從 SQL Server 匯入和匯出資料 (在資料中心或在 Azure 中裝載的虛擬機器上)。 使用 SQL Database 和 SQL Server 之間的差異如下：

* HDInsight 與 SQL Server 必須位於相同的 Azure 虛擬網路

  > [!NOTE]
  > HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。

    在您的資料中心裡使用 SQL Server 時，必須將虛擬網路設定為「站對站」或「點對站」。

  > [!NOTE]
  > 使用「點對站」虛擬網路時，SQL Server 必須執行 VPN 用戶端組態應用程式。 VPN 用戶端位於 Azure 虛擬網路組態的 [儀表板]。

    如需 Azure 虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

* SQL Server 也必須設定為允許 SQL 驗證。 如需詳細資訊，請參閱[選擇驗證模式](https://msdn.microsoft.com/ms144284.aspx)文件。

* 您可能必須設定 SQL Server 以接受遠端連線。 如需詳細資訊，請參閱[如何疑難排解 SQL Server Database Engine 連線](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)文件 (英文)。

* 在 SQL Server 中，使用公用程式 (例如 **SQL Server Management Studio** 或 **tsql**) 建立 **sqooptest** 資料庫。 使用 Azure CLI 的步驟只適用於 Azure SQL 資料庫。

    使用下列 TSQL 陳述式建立 **mobiledata** 資料表︰

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* 從 HDInsight 連線到 SQL Server 時，您可能必須使用 SQL Server 的 IP 位址。 例如：

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

## <a name="limitations"></a>限制

* 大量匯出 - 使用 Linux 型 HDInsight，用來將資料匯出至 Microsoft SQL Server 或 Azure SQL Database 的 Sqoop 連接器目前不支援大量插入。

* 批次處理 - 使用 Linux 型 HDInsight，執行插入時若使用 `-batch` 參數，Sqoop 將會執行多個插入，而不是批次處理插入作業。

## <a name="next-steps"></a>後續步驟

現在，您已了解如何使用 Sqoop。 若要深入了解，請參閱：

* [搭配 HDInsight 使用 Oozie][hdinsight-use-oozie]：在 Oozie 工作流程中使用 Sqoop 動作。
* [使用 HDInsight 分析航班延誤資料][hdinsight-analyze-flight-data]：使用 Hive 分析航班誤點資料，然後使用 Sqoop 將資料匯出至 Azure SQL Database。
* [將資料上傳至 HDInsight][hdinsight-upload-data]：尋找其他方法將資料上傳至 HDInsight/Azure Blob 儲存體。

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

