<properties
	pageTitle="使用 Azure 入口網站中的 HDInsight 管理以 Linux 為基礎的 Handooop 叢集 | Microsoft Azure"
	description="了解如何使用 Azure 入口網站來監視和管理以 Linux 為基礎的 HDInsight 叢集。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="larryfr"/>

#使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-portal-management-selector.md)]


使用 [Azure 入口網站][preview-portal]，您可以佈建和管理 Azure HDInsight 中的 Linux 型 Hadoop 叢集。

> [AZURE.NOTE] 此文件中的步驟是針對 Linux 型 Hadoop 叢集。如需使用 Windows 型叢集的相關資訊，請參閱[使用 Azure 入口網站管理 HDInsight 上的 Hadoop 叢集](hdinsight-administer-use-management-portal.md)。


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## 用來管理 HDInsight 的其他工具
除了 Azure 入口網站之外，還有其他工具可以用來管理 HDInsight。

- [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)：Azure CLI 是跨平台的命令列工具，可讓您管理 Azure 服務。

- [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)：Azure PowerShell 提供 PowerShell Cmdlet 讓您管理 Azure 服務。

## 必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## 建立 HDInsight 叢集

您可以從 Azure 入口網站建立 HDInsight 叢集。詳情請參閱[使用 Azure 入口網站在 HDInsight 中建立 Linux 型叢集](hdinsight-hadoop-create-linux-clusters-portal.md)文件。

## 管理叢集

從 Azure 入口網站中選取叢集將會顯示叢集的基本資訊，例如名稱、資源群組、作業系統和叢集 URL (用來存取適用於 Linux 型叢集的 Ambari Web )。

![叢集詳細資料](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

使用下列資訊，了解在刀鋒視窗頂端以及 [程式集] 和 [快速連結] 區段中的圖示：

* __設定__和__所有設定__：顯示該叢集的 [設定] 刀鋒視窗，可讓您存取該叢集的詳細組態資訊。

* __儀表板__、__叢集儀表板__和 __URL__：這些方法都可以存取叢集儀表板。根據叢集類型，您可能會看到叢集的儀表板清單。例如，當您選取__儀表板__圖示時，Spark 叢集類型會顯示儀表板清單，而 Hadoop 叢集則會開啟 Ambari Web UI。

* __安全殼層__：使用 SSH 存取叢集前端節點所需的資訊。

* __調整叢集__：可讓您變更此叢集的背景工作節點數目。

* __刪除__：刪除 HDInsight 叢集。

* __快速入門 (![雲和雷電圖示 = 快速入門](./media/hdinsight-administer-use-portal-linux/quickstart.png))__：顯示可協助您開始使用 HDInsight 的資訊。

* __使用者 (![使用者圖示](./media/hdinsight-administer-use-portal-linux/users.png))__：可讓您設定 Azure 訂用帳戶上其他使用者對此叢集的「入口網站管理」權限。

	> [AZURE.IMPORTANT] 這_只_會影響在 Azure 入口網站對此叢集的存取和權限，對於連線到 HDInsight 叢集或將工作提交到 HDInsight 叢集的使用者沒有影響。

* __標記 (![標記圖示](./media/hdinsight-administer-use-portal-linux/tags.png))__：標記可讓您設定索引鍵/值組，以定義雲端服務的自訂分類。例如，您可建立名為 __project__ 的索引鍵，然後使用與特定專案相關聯之所有服務的通用值。

* __文件__：Azure HDInsight 文件的連結。

> [AZURE.IMPORTANT] 若要管理 HDInsight 叢集所提供的服務，您必須使用 Ambari Web 或 Ambari REST API。如需使用 Ambari 的詳細資訊，請參閱[使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

### 設定

選取__設定__圖示或__所有設定__連結，就會顯示 [設定] 刀鋒視窗。之前在 [程式集] 區域討論的幾個可用的函式也可在這裡使用，例如縮放比例或安全殼層資訊。您也可以從 [設定] 存取下列項目︰

* __稽核記錄檔__︰有利於診斷叢集健全狀況問題的記錄資訊。

* __叢集登入__︰顯示可以使用 HTTPS 存取叢集的 [叢集登入使用者名稱] 和 [遠端位址]。

* __外部中繼存放區__︰顯示叢集使用的外部中繼存放區資訊，如果有的話。如果您未在叢集組態期間設定自訂的中繼存放區，就不會顯示任何資訊。

* __指令碼動作__︰顯示這個叢集上執行過的指令碼動作資訊。您也可以執行新的指令碼動作，並保存或刪除之前執行過的指令碼動作。如需指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

* __應用程式__︰顯示叢集上已安裝的應用程式資訊，並讓您從 Azure Marketplace 將新的應用程式加入叢集中。

* __Azure 儲存體金鑰__︰顯示叢集使用的 Azure 儲存體帳戶資訊。選取儲存體帳戶就會載入所選帳戶的 [儲存體帳戶] 刀鋒視窗。

* __叢集 AAD 身分識別__︰顯示這個 HDInsight 叢集的服務主體資訊。服務是用於存取 Azure Data Lake Store。如果在叢集建立期間您沒有建立叢集與 Azure Data Lake Store 的關聯性，這個刀鋒視窗的項目就會顯示 [未設定]。如需使用 Azure Data Lake Store 與 HDInsight 的詳細資訊，請參閱[建立使用 Data Lake Store 的 HDInsight 叢集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。

### <a name="scaling"></a>調整

若要使用入口網站調整叢集，請選取您的 HDInsight 叢集，然後選取 [調整叢集]。輸入您要為此叢集設定的__背景工作節點數目__，然後按一下 [儲存]。

![調整 ui 的影像](./media/hdinsight-administer-use-portal-linux/scaling.png)

如需如何調整作業的詳細資訊，請參閱[在 Linux 上使用 HDInsight 的相關資訊](hdinsight-hadoop-linux-information.md#scaling)。

## 監視叢集

HDInsight 叢集刀鋒視窗的 [使用量] 區段會顯示以下資訊：訂用帳戶可搭配 HDInsight 使用的核心數目，以及配置給此叢集的核心數目和它們在此叢集中配置給節點的方式。

![使用量資訊](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT] 若要監視 HDInsight 叢集所提供的服務，您必須使用 Ambari Web 或 Ambari REST API。如需使用 Ambari 的詳細資訊，請參閱[使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

## 後續步驟
在本文中，您已了解如何使用 Azure 入口網站建立 HDInsight 叢集，以及如何開啟 Hadoop 命令列工具。若要深入了解，請參閱下列文章：

* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)
* [佈建 HDInsight 叢集](hdinsight-provision-clusters.md)
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)
* [開始使用 Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight 提供 Hadoop 的什麼版本？](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->