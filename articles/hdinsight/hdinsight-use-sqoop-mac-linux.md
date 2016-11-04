---
title: 在以 Linux 為基礎的 HDInsight 上使用 Hadoop Sqoop | Microsoft Docs
description: 了解如何在 HDInsight 叢集上 Linux 架構的 Hadoop 與 Azure SQL Database 之間執行 Sqoop 匯入和匯出。
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
author: Blackmist
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: larryfr

---
# 在 HDInsight 上將 Sqoop 與 Hadoop 搭配使用 (SSH)
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Sqoop，在 HDInsight 叢集與 Azure SQL Database 或 SQL Server Database 之間進行匯入和匯出。

> [!NOTE]
> 本文中的步驟使用 SSH 來連線至 Linux 架構的 HDInsight 叢集。Windows 用戶端也可以使用 Azure PowerShell 和 HDInsight .NET SDK 在以 Linux 為基礎的叢集上使用 Sqoop。使用索引標籤選取器，以開啟這些文章。
> 
> 

## 必要條件
開始進行本教學課程之前，您必須具備下列條件：

* **HDInsight 中的 Hadoop 叢集**和__Azure SQL Database__︰本文件中的步驟是以使用[建立叢集與 SQL Database](hdinsight-use-sqoop.md#create-cluster-and-sql-database) 文件來建立的叢集和資料庫為基礎。如果您已經有 HDInsight 叢集和 SQL Database，就可以將這些用來替代本文件中使用的值。
* **工作站**：具有 SSH 用戶端的電腦。

## 安裝 FreeTDS
1. 使用 SSH 來連線至 Linux 架構的 HDInsight 叢集。連接時要使用的位址為 `CLUSTERNAME-ssh.azurehdinsight.net`，而連接埠為 `22`。
   
    如需有關使用 SSH 連線至 HDInsight 的詳細資訊，請參閱下列文件：
   
   * **Linux、Unix 或 OS X 用戶端**：請參閱[從 Linux、OS X 或 Unix 連接至 Linux 架構的 HDInsight 叢集](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)
   * **Windows 用戶端**：請參閱[從 Windows 連接至 Linux 架構的 HDInsight 叢集](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)
2. 使用下列命令來安裝 FreeTDS：
   
        sudo apt-get --assume-yes install freetds-dev freetds-bin
   
    在數個步驟中將使用 FreeTDS 來連接到 SQL Database。

## 在 SQL Database 中建立資料表
> [!IMPORTANT]
> 如果您使用的 HDInsight 叢集和 SQL Database 是使用[建立叢集與 SQL Database](hdinsight-use-sqoop.md) 中的步驟所建立，請忽略本節中的步驟，因為在該文件的步驟中已建立資料庫和資料表。
> 
> 

1. 從對 HDInsight 的 SSH 連線中，使用下列命令來連接到 SQL Database 伺服器，並建立將在這些步驟的其餘部分使用的資料表 ︰
   
        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest
   
    您將收到類似以下的輸出：
   
        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>
2. 在 `1>` 提示字元輸入下列幾行：
   
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
   
    輸入 `GO` 陳述式後，將評估先前的陳述式。首先，建立 **mobiledata** 資料表，然後將叢集索引加入至該資料表 (SQL Database 所需)。
   
    使用下列命令來確認已建立資料表：
   
        SELECT * FROM information_schema.tables
        GO
   
    您應該會看到如下所示的輸出：
   
        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE
3. 在 `1>` 提示字元輸入 `exit` 以結束 tsql 公用程式。

## Sqoop export
1. 從對 HDInsight 的 SSH 連線中，使用下列命令來確認 Sqoop 看得見您的 SQL Database：
   
        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
   
    這應該會傳回一份資料庫清單，其中包含您稍早建立的 **sqooptest** 資料庫。
2. 使用下列命令，將資料從 **hivesampletable** 匯出至 **mobiledata** 資料表：
   
        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1
   
    這會指示 Sqoop 連接至 SQL Database、**sqooptest** 資料庫，然後將資料從 **wasbs:///hive/warehouse/hivesampletable** (*hivesampletable* 的實體檔案) 匯出至 **mobiledata** 資料表。
3. 在命令完成後，使用下列程式碼連接至使用 TSQL 的資料庫：
   
        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest
   
    連線之後，使用下列陳述式來確認資料已匯出到 **mobiledata** 資料表：
   
        SELECT * FROM mobiledata
        GO
   
    您應會看到資料表中的資料清單。輸入 `exit` 以結束 tsql 公用程式。

## Sqoop import
1. 使用下列程式碼將資料從 SQL Database 中的 **mobiledata** 資料表匯入至 HDInsight 上的 **wasbs:///tutorials/usesqoop/importeddata** 目錄：
   
        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
   
    匯入的資料會有以定位字元分隔的欄位，以及以換行字元終止的行。
2. 匯入完成後，使用下列命令來列出新目錄中的資料：
   
        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

## 使用 SQL Server
您也可以使用 Sqoop 從 SQL Server 匯入和匯出資料 (在資料中心或在 Azure 中裝載的虛擬機器上)。使用 SQL Database 和 SQL Server 之間的差異如下：

* HDInsight 與 SQL Server 必須位於相同的 Azure 虛擬網路
  
  > [!NOTE]
  > HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。
  > 
  > 
  
    在您的資料中心裡使用 SQL Server 時，必須將虛擬網路設定為*站對站*或*點對站*。
  
  > [!NOTE]
  > 使用**點對站**虛擬網路時，SQL Server 必須執行 VPN 用戶端組態應用程式；您可從 Azure 虛擬網路組態的 [儀表板] 存取此應用程式。
  > 
  > 
  
    如需建立和設定虛擬網路的詳細資訊，請參閱[虛擬網路設定工作](../services/virtual-machines.md)。
* SQL Server 也必須設定為允許 SQL 驗證。如需詳細資訊，請參閱[選擇驗證模式](https://msdn.microsoft.com/ms144284.aspx)
* 您可能必須設定 SQL Server 以接受遠端連線。如需詳細資訊，請參閱[如何疑難排解 SQL Server Database 引擎連線](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)
* 您必須在 SQL Server 中使用公用程式 (例如 **SQL Server Management Studio** 或 **tsql**) 建立 **sqooptest** 資料庫 - 使用 Azure CLI 的步驟只適用於 Azure SQL Database
  
    用以建立 **mobiledata** 資料表的 TSQL 陳述式類似用於 SQL Database 的陳述式，但建立叢集索引除外- 這不是 SQL Server 的必要作業：
  
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
* 從 HDInsight 連線到 SQL Server 時，您可能必須使用 SQL Server 的 IP 位址 (除非您已設定網域名稱系統 (DNS)) 來解析 Azure 虛擬網路上的名稱。例如：
  
        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

## 限制
* 大量匯出 - 使用 Linux 型 HDInsight，用來將資料匯出至 Microsoft SQL Server 或 Azure SQL Database 的 Sqoop 連接器目前不支援大量插入。
* 批次處理 - 使用 Linux 型 HDInsight，執行插入時若使用 `-batch` 參數，Sqoop 將會執行多個插入，而不是批次處理插入作業。

## 後續步驟
現在，您已了解如何使用 Sqoop。若要深入了解，請參閱：

* [搭配 HDInsight 使用 Oozie][hdinsight-use-oozie]：在 Oozie 工作流程中使用 Sqoop 動作。
* [使用 HDInsight 分析航班延誤資料][hdinsight-analyze-flight-data]：使用 Hive 分析航班誤點資料，然後使用 Sqoop 將資料匯出至 Azure SQL Database。
* [將資料上傳至 HDInsight][hdinsight-upload-data]：尋找可將資料上傳至 HDInsight/Azure Blob 儲存體的其他方法。

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0914_2016-->