---
title: "搭配 Kafka 使用 Azure HDInsight 上的 Apache Spark | Microsoft Docs"
description: "了解如何使用 Spark on HDInsight 來讀取資料並寫入至 Kafka on HDInsight 叢集。 此範例使用 Jupyter Notebook 中的 Scala，將隨機資料寫入 Kafka on HDInsight，然後使用 Spark 串流加以讀回。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 50a9c3929a4d3194c3786a3d4f6cdd1b73fb5867
ms.openlocfilehash: 0cb9d65e33bf6f6b67e2a74b7e4634aba3f2359b

---
# <a name="use-apache-spark-with-kafka-preview-on-hdinsight"></a>使用 Apache Spark 搭配 Kafka (預覽) on HDInsight

Apache Spark 可用於以串流方式將資料送入或送出 Apache Kafka。 在本文件中，了解如何使用 Jupyter Notebook 從 Spark on HDInsight，以串流方式將資料送入和送出 Kafka。

> [!NOTE]
> 本文件中的步驟建立 Azure 資源群組，其中包含 Spark on HDInsight 和 Kafka on HDInsight cluster 叢集。 這兩個叢集都位於 Azure 虛擬網路中，可讓 Spark 叢集直接與 Kafka 叢集通訊。
> 
> 當您完成本文件中的步驟時，請記得刪除叢集，以避免產生過多的費用。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶

* SSH 用戶端 (您需要 `ssh` 和 `scp` 命令) - 如需使用 SSH 搭配 HDInsight 的詳細資訊，請參閱下列文件：

    * [從 Linux、Unix 及 Mac 作業系統搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [從 Windows 搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

* [cURL](https://curl.haxx.se/) - 跨平台公用程式，用於提出 HTTP 要求。

* [jq](https://stedolan.github.io/jq/) - 跨平台公用程式，用於剖析 JSON 文件。

## <a name="create-the-clusters"></a>建立叢集

Apache Kafka on HDInsight 不提供透過公用網際網路存取 Kafka 訊息代理程式。 任何 Kafka 相關項目必須位於與 Kafka 叢集中節點相同的 Azure 虛擬網路。 例如，Kafka 和 Spark 叢集均位於 Azure 虛擬網路中。 下圖顯示叢集之間的通訊流動方式︰

![Azure 虛擬網路中的 Spark 和 Kafka 叢集圖表](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 本身受限於虛擬網路內的通訊，但叢集上的 SSH 和 Ambari 等其他服務可以透過網際網路存取。 如需有關適用於 HDInsight 的公用連接埠詳細資訊，請參閱 [HDInsight 所使用的連接埠和 URI](hdinsight-hadoop-port-settings-for-services.md)。

雖然您可以手動建立 Azure 虛擬網路、Kafka 和 Spark 叢集，但使用 Azure Resource Manager 範本更輕鬆。 使用下列步驟將 Azure 虛擬網路、Kafka 和 Spark 叢集部署到 Azure 訂用帳戶。

1. 使用以下按鈕，在 Azure 入口網站中登入 Azure 並開啟範本。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager 範本位於 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet.json**。

2. 使用下列資訊來填入 [自訂部署] 刀鋒視窗上的項目︰
   
    ![HDInsight 自訂部署](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **資源群組**：建立群組或選取現有的群組。 此群組包含 HDInsight 叢集。

    * **位置**：選取在地理上靠近您的位置。 此位置必須符合 [設定] 區段中的位置。

    * **基底叢集名稱**︰此值會做為 Spark 和 Kafka 叢集的基底名稱。 例如，輸入 **hdi** 可建立名為 spark-hdi__ 的 Spark 叢集以及名為 **kafka-hdi** 的 Kafka 叢集。

    * **叢集登入使用者名稱**：Spark 和 Kafka 叢集的系統管理員使用者名稱。

    * **叢集登入密碼**：Spark 和 Kafka 叢集的系統管理員使用者密碼。

    * **SSH 使用者名稱**︰建立 Spark 和 Kafka 叢集的 SSH 使用者。

    * **SSH 密碼**：Spark 和 Kafka 叢集的 SSH 使用者密碼。

    * **位置**︰叢集建立所在的區域。

3. 讀取**條款及條件**，然後選取 [我同意上方所述的條款及條件]。

4. 最後，核取 [釘選到儀表板]，然後選取 [購買]。 大約需要 20 分鐘的時間來建立叢集。

建立資源後，您會重新導向至資源群組的刀鋒視窗，其中內含叢集和 Web 儀表板。

![Vnet 和叢集的資源群組刀鋒視窗](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> 請注意，HDInsight 叢集的名稱是 **spark-BASENAME** 和 **kafka-BASENAME**，其中 BASENAME 是您提供給範本的名稱。 連接到叢集時，您會在稍後步驟中使用這些名稱。

## <a name="get-the-code"></a>取得程式碼

在 [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) 可取得本文件所述範例的程式碼。

## <a name="understand-the-code"></a>了解程式碼

此範例會使用 Jupyter Notebook 中的 Scala 應用程式。 Notebook 中的程式碼依賴下列資料片段︰

* __Kafka 訊息代理程式__：在 Kafka 叢集的每個 背景工作節點上執行的訊息代理程式程序。 將資料寫入 Kafka 之產生者元件所需的訊息代理程式清單。

* __Zookeeper 主機__：從 Kafka 取用 (讀取) 資料時會使用 Kafka 叢集的 Zookeeper 主機。

* __主題名稱__︰用於寫入和讀取資料的主題名稱。 此範例需要名為 `sparktest` 的主題。

如需如何取得 Kafka 訊息代理程式及 Zookeeper 主機資訊的相關資訊，請參閱 [Kafka 主機資訊](#kafkahosts)一節。

Notebook 中的程式碼會執行下列工作：

* 建立取用者，該取用者會從名為 `sparktest` 的 Kafka 主題讀取資料、計算資料中的每個單字，並將字數統計儲存到名為 `wordcounts` 的暫存資料表。

* 建立產生者，該產生者會將隨機句子寫入名為 `sparktest` 的 Kafka 主題。

* 選取 `wordcounts` 資料表中的資料，以便顯示計數。

專案中的每個資料格都包含註解或文字區段，以說明程式碼的作用。

##<a name="a-idkafkahostsakafka-host-information"></a><a id="kafkahosts"></a>Kafka 主機資訊

建立可搭配 Kafka on HDInsight 運作的應用程式時，您應該進行的第一件事是取得 Kafka 叢集的 Kafka 訊息代理程式和 Zookeeper 主機資訊。 用戶端應用程式可使用此資訊來與 Kafka 通訊。

> [!NOTE]
> 無法透過網際網路直接存取 Kafka 訊息代理程式和 Zookeeper 主機。 任何使用 Kafka 的應用程式必須在 Kafka 叢集上或在與 Kafka 叢集相同的 Azure 虛擬網路內執行。 在此情況下，範例會在相同虛擬網路中的 Spark on HDInsight 叢集上執行。

從開發環境，使用下列命令來擷取訊息代理程式和 Zookeeper 資訊。 將 __PASSWORD__ 替換為您在建立叢集時使用的登入 (admin) 密碼。 將 __BASENAME__ 替換為您在建立叢集時使用的基底名稱。

* 若要取得 __Kafka 訊息代理程式__資訊︰

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'

    > [!IMPORTANT]
    > 從 Windows PowerShell 使用此命令時，您可能會收到有關殼層引用的錯誤。 若是如此，請使用下列命令：`curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")'`

* 若要取得 __Zookeeper 主機__資訊：

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'
    
    > [!IMPORTANT]
    > 從 Windows PowerShell 使用此命令時，您可能會收到有關殼層引用的錯誤。 若是如此，請使用下列命令：`curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")'`

兩個命令都會傳回類似下列文字的資訊：

* __Kafka 訊息代理程式__：`wn0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092`

* __Zookeeper 主機__：`zk0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk2-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181`

> [!IMPORTANT]
> 請儲存此資訊，本文件的幾個步驟中將會用到。

## <a name="use-the-jupyter-notebook"></a>使用 Jupyter Notebook

若要使用範例 Jupyter Notebook，您必須將它上傳至 Spark 叢集上的 Jupyter Notebook 伺服器。 使用下列步驟來上船 Notebook：

1. 在網頁瀏覽器中，使用下列 URL 連接到 Spark 叢集上的 Jupyter Notebook 伺服器。 將 __BASENAME__ 替換為您在建立叢集時使用的基底名稱。

        https://spark-BASENAME.azurehdinsight.net/jupyter

    出現提示時，輸入您在建立叢集時所使用的叢集登入 (admin) 和密碼。

2. 從頁面的右上方，使用 [上載] 按鈕來上傳 `KafkaStreaming.ipynb` 檔案。 在檔案瀏覽器對話方塊中選取此檔案，然後選取 [開啟]。 

    ![使用上傳按鈕來選取和上傳 Notebook](./media/hdinsight-apache-spark-with-kafka/upload-button.png)

    ![選取 KafkaStreaming.ipynb 檔案](./media/hdinsight-apache-spark-with-kafka/select-notebook.png)

3. 在 Notebook 清單中尋找 __KafkaStreaming.ipynb__ 項目，然後選取旁邊的 [上載] 按鈕。

    ![使用 KafkaStreaming.ipynb 項目旁邊的 [上載] 按鈕，將它上傳至 Notebook 伺服器](./media/hdinsight-apache-spark-with-kafka/upload-notebook.png)

4. 上傳檔案之後，選取 __KafkaStreaming.ipynb__ 項目來開啟 Notebook。 若要完成此範例，請依循 Notebook 中的指示。

## <a name="delete-the-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

因為本文件中的步驟會在相同的 Azure 資源群組中建立兩個叢集，您可以在 Azure 入口網站中刪除資源群組。 刪除群組，即可移除依循本文件建立的所有資源、Azure 虛擬網路，以及叢集所使用的儲存體帳戶。

## <a name="next-steps"></a>後續步驟

在本文件中，您已了解如何使用 Spark 來讀取和寫入至 Kafka。 使用下列連結來探索使用 Kafka 的其他方式︰

* [開始使用 Apache Kafka on HDInsight](hdinsight-apache-kafka-get-started.md)
* [使用 MirrorMaker 建立 Apache Kafka on HDInsight 複本](hdinsight-apache-kafka-mirroring.md)
* [使用 Apache Storm 搭配 Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md)




<!--HONumber=Feb17_HO2-->


