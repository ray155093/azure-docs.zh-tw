---
title: "開始使用 Apache Kafka on HDInsight | Microsoft Docs"
description: "了解建立與使用 Kafka on HDInsight 的基本概念。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/09/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2d744e753224e4ce98680d3228914fd89e87eba4
ms.openlocfilehash: 70d910f61fa414a0c58a22057133ef3b899dac05

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>開始使用 Apache Kafka (預覽) on HDInsight

[Apache Kafka](https://kafka.apache.org) 是 HDInsight 提供的開放原始碼分散式串流平台。 它通常做為訊息代理程式，因為它提供了類似於發佈-訂閱訊息佇列的功能。 在本文件中，您會了解如何建立 Kafka on HDInsight 叢集，然後從 Java 應用程式傳送和接收資料。

> [!NOTE]
> HDInsight 目前提供兩個 Kafka 版本；0.9.0 (HDInsight 3.4) 和 0.10.0 (HDInsight 3.5)。 本文中的步驟假設您使用 Kafka on HDInsight 3.5。

## <a name="prerequisite"></a>必要條件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

您必須具備下列先決條件，才能順利完成本 Apache Storm 教學課程：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* **熟悉 SSH 和 SCP**。 如需使用 SSH 和 SCP 搭配 HDInsight 的詳細資訊，請參閱下列文件：
  
   * **Linux、Unix 或 OS X 用戶端**：請參閱 [從 Linux、OS X 或 Unix 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
   
   * **Windows 用戶端**：請參閱 [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 或同等功能版本，例如 OpenJDK。

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>存取控制需求

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>建立 Kafka 叢集

請使用下列步驟建立 Kafka on HDInsight：

1. 從 [Azure 入口網站](https://portal.azure.com)選取 [+ 新增]、[情報 + 分析] 及 [HDInsight]，然後選取 [HDInsight]。
   
    ![建立 HDInsight 叢集](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. 在 [新的 HDInsight 叢集建立] 刀鋒視窗中，輸入 [叢集名稱]，然後選取要搭配此叢集使用的 [訂用帳戶]。
   
    ![選取訂用帳戶](./media/hdinsight-apache-kafka-get-started/new-hdinsight-cluster-blade.png)

3. 使用 [選取叢集類型]，並且在 [叢集類型設定] 刀鋒視窗上選取下列值︰
   
    * **叢集類型**：Kafka

    * **版本**：Kafka 0.10.0 (HDI 3.5)

    * **叢集層**：標準
     
    最後，使用 [選取] 按鈕來儲存設定。
     
    ![選取叢集類型](./media/hdinsight-apache-kafka-get-started/cluster-type.png)

4. 使用 [認證] 來設定叢集登入和 SSH 使用者認證。  使用 [選取] 按鈕來儲存設定。
   
    > [!NOTE]
    > 使用 HTTPS 透過網際網路存取叢集時，會使用叢集登入。 SSH 使用者用來連接到叢集並以互動方式執行命令。
   
    ![設定叢集登入](./media/hdinsight-apache-kafka-get-started/cluster-credentials.png)
   
    如需使用 SSH 搭配 HDInsight 的詳細資訊，請參閱下列文件：
   
    * [從 Linux、Unix 或 Mac 用戶端搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
   
    * [從 Windows 用戶端搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

5. 使用 [資料來源] 來設定叢集的主要資料存放區。 從 [資料來源] 刀鋒視窗，使用下列資訊來建立叢集的資料存放區︰
   
    * 選取 [新建]，然後輸入儲存體帳戶的名稱。
    
    * 選取 [位置]，然後選取在地理上靠近您的位置。 這個位置用來建立儲存體帳戶與 HDInsight 叢集。
     
   最後，使用 [選取] 按鈕來儲存設定。
     
    ![設定儲存體](./media/hdinsight-apache-kafka-get-started/configure-storage.png)

6. 使用 [價格]，然後將 [背景工作節點數目] 設定為 2。 使用 2 個背景工作節點可減少叢集的成本，並足以用於這個範例。 使用 [選取] 按鈕來儲存設定。
   
    ![價格](./media/hdinsight-apache-kafka-get-started/pricing.png)
   
    > [!NOTE]
    > 顯示於入口網站的價格可能與螢幕擷取畫面中的價格不同。

7. 使用 [資源群組] 來建立群組，然後在欄位中輸入名稱。 並且選取 [釘選到儀表板]。 完成時，選取 [建立] 來建立叢集。
   
    ![資源群組欄位](./media/hdinsight-apache-kafka-get-started/resource-group.png)
   
    > [!NOTE]
    > 建立叢集可能需要花費 20 分鐘的時間。

## <a name="connect-to-the-cluster"></a>連接到叢集

從您的用戶端，使用 SSH 連接到叢集。 如果您在 Windows 10 上使用 Linux、Unix、MacOS 或 Bash，請使用下列命令︰

    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net

將 **SSHUSER** 替換為您在叢集建立期間提供的 SSH 使用者名稱。 將 **CLUSTERNAME** 取代為叢集的名稱。

出現提示時，輸入您用於 SSH 帳戶的密碼。

如需使用 SSH 搭配 HDInsight 的詳細資訊，請參閱下列文件：

* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="a-idgetkafkainfoaget-the-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>取得 Zookeeper 和訊息代理程式主機資訊

使用 Kafka 時，您必須知道兩個主機值；Zookeeper 主機和訊息代理程式主機。 這些主機可搭配 Kafka API 以及 Kafka 隨附的許多公用程式使用。

使用下列步驟來建立包含主機資訊的環境變數。 這些環境變數使用於本文件中的步驟。

1. 在連往叢集的 SSH 連線中，使用下列命令來安裝 `jq` 公用程式。 此公用程式用來剖析 JSON 文件，而且在擷取訊息代理程式主機資訊時很有用︰
   
        sudo apt -y install jq

2. 使用下列命令，以擷取自 Ambari 的資訊設定環境變數。 將 __KAFKANAME__ 取代為 Kafka 叢集的名稱。 將 __PASSWORD__ 替換為您在建立叢集時使用的登入 (admin) 密碼。

        export KAFKAZKHOSTS=`curl --silent -u admin:PASSWORD -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

        export KAFKABROKERS=`curl --silent -u admin:PASSWORD -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

        echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
        echo '$KAFKABROKERS='$KAFKABROKERS

    以下文字是 `$KAFKAZKHOSTS` 的內容範例：
   
        zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181
   
    以下文字是 `$KAFKABROKERS` 的內容範例：
   
        wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092
   
    > [!WARNING]
    > 請勿認為從此工作階段傳回的資訊永遠都正確無誤。 如果您調整叢集，則會新增或移除新的訊息代理程式。 如果發生失敗且節點遭到取代，則節點的主機名稱可能會變更。 
    > 
    > 您應在使用 Zookeeper 和訊息代理程式主機資訊不久前一律擷取該資訊，以確保您具備有效的資訊。

## <a name="create-a-topic"></a>建立主題

Kafka 會將資料串流儲存在名為 topics 的類別中。 經由叢集前端節點的 SSH 連線，使用 Kafka 所提供的指令碼來建立主題︰

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

此命令會使用 `$KAFKAZKHOSTS` 中儲存的主機資訊連接到 Zookeeper，然後建立名為 **test** 的 Kafka 主題。 您可以確認使用下列指令碼建立的主題可列出主題︰

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS

此命令的輸出會列出 Kafka 主題，其中包含 **test** 主題。

## <a name="produce-and-consume-records"></a>產生和取用記錄

Kafka 會在主題中儲存「記錄」。 記錄是由「產生者」產生，並由「取用者」取用。 產生者會從 Kafka「訊息代理程式」擷取記錄。 HDInsight 叢集中的每個背景工作節點都是 Kafka 訊息代理程式。

使用下列步驟，將記錄儲存至您稍早建立的 test 主題，然後利用取用者進行讀取︰

1. 在 SSH 工作階段中，使用 Kafka 提供的指令碼將記錄寫入主題︰
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
   
    您不會在此命令後返回提示字元。 反而，輸入一些文字訊息，然後使用 **Ctrl + C** 停止傳送至主題。 每一行都會以個別的記錄傳送。

2. 使用 Kafka 提供的指令碼來讀取主題中的記錄︰
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
   
    這將會擷取主題中的記錄並加以顯示。 使用 `--from-beginning` 告知取用者從串流的開頭開始，所以會擷取所有的記錄。

3. 使用 __Ctrl + C__ 來停止取用者。

## <a name="producer-and-consumer-api"></a>產生者和取用者 API

您也可以利用 [Kafka API](http://kafka.apache.org/documentation#api)，以程式設計方式產生和取用記錄。 使用下列步驟進行下載，並建置 Java 型生產者和取用者︰

1. 從 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) 下載範例。 在生產者/取用者範例中，使用 `Producer-Consumer` 目錄中的專案。 請務必查看程式碼來了解此範例的運作方式。 其中包含下列類別：
   
    * **執行** - 根據命令列引數啟動取用者或產生者。

    * **產生者** - 將 1,000,000 筆記錄儲存至主題。

    * **取用者** - 讀取主題中的記錄。

2. 從開發環境中的命令列，將目錄變更為範例的 `Producer-Consumer` 目錄位置，然後使用下列命令來建立 jar 套件︰
   
        mvn clean package
   
    此命令會建立名為 `target` 的新目錄，其中包含名為 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 的檔案。

3. 使用下列命令將 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 檔案複製到 HDInsight 叢集：
   
        scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
   
    以叢集的 SSH 使用者取代 **USERNAME**，並以叢集的名稱取代 **CLUSTERNAME**。 出現提示時，請輸入 SSH 使用者的密碼。

4. `scp` 命令完成檔案複製後，請使用 SSH 連接到叢集，然後使用下列命令將記錄寫入您稍早建立的 test 主題。
   
        ./kafka-producer-consumer.jar producer $KAFKABROKERS
   
    這將會啟動產生者並寫入記錄。 您可以在顯示的計數器中查看已寫入多少筆記錄。

    > [!NOTE]
    > 如果您收到權限遭拒錯誤，請使用下列命令讓檔案得以執行︰```chmod +x kafka-producer-consumer.jar```

5. 完成此程序後，請使用下列命令從主題讀取︰
   
        ./kafka-producer-consumer.jar consumer $KAFKABROKERS
   
    已讀取的記錄以及記錄計數隨即顯示。 您可能會看到已記錄 1,000,000 筆以上，因為我們使用先前步驟中的指令碼將數筆記錄傳送至主題。

6. 使用 __Ctrl + C__ 來結束取用者。

### <a name="multiple-consumers"></a>多個取用者

Kafka 的重要概念是取用者會在讀取記錄時使用取用者群組 (依群組識別碼定義)。 使用相同群組的多個取用者會使從主題的讀取達到負載平衡；每個取用者都會收到部分的記錄。 若要在運作中查看此資訊，請使用下列步驟︰

1. 開啟叢集的新 SSH 工作階段，您便有兩個工作階段。 在每個工作階段中，使用下列命令來啟動具有相同取用者群組識別碼的取用者︰
   
        ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup

    > [!NOTE]
    > 由於這是新的 SSH 工作階段，您需要使用[取得 Zookeeper 和訊息代理程式主機資訊](#getkafkainfo)一節中的命令來設定 `$KAFKABROKERS`。

2. 觀看每個工作階段計算其從主題接收的記錄。 這兩個工作階段的總數應該與您先前從一個取用者收到的數量相同。

透過主題的資料分割處理相同群組內的用戶端取用。 稍早建立的 `test` 主題有 8 個資料分割。 如果您開啟 8 個 SSH 工作階段並在所有工作階段中啟動某個取用者，則每個取用者都會從主題的單一資料分割讀取記錄。

> [!IMPORTANT]
> 一個取用者群組中的取用者執行個體不得超過資料分割。 在此範例中，一個取用者群組可以包含最多 8 個取用者，因為這是主題中的資料分割數目。 或者，您可以有多個取用者群組，其各有不超過 8 個取用者。

Kafka 中儲存的記錄會依照其在資料分割內接收的順序儲存。 若要達到依序傳遞「資料分割內」的記錄，請建立取用者群組，其中的取用者執行個體數目與資料分割數目相符。 若要達到依序傳遞「主題內」的記錄，請建立只有一個取用者執行個體的取用者群組。

## <a name="streaming-api"></a>串流 API

串流 API 已新增至 0.10.0 版中的 Kafka；舊版依賴 Apache Spark 或 Storm 進行串流處理。

1. 如果您已經這麼做，請從 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) 下載範例。 在串流範例中，使用 `streaming` 目錄中的專案。 請務必查看程式碼來了解此範例的運作方式。 
   
    此專案只包含一個類別 (`Stream`)，該類別會從先前建立的 `test` 主題讀取記錄。 它會計算已讀取的字數，並發出每個單字並計入名為 `wordcounts` 的主題。 本節中稍後的步驟會建立 `wordcounts` 主題。

2. 從開發環境中的命令列，將目錄變更 `Streaming` 目錄的位置，然後使用下列命令來建立 jar 套件︰
   
        mvn clean package
   
    此命令會建立名為 `target` 的新目錄，其中包含名為 `kafka-streaming-1.0-SNAPSHOT.jar` 的檔案。

3. 使用下列命令將 `kafka-streaming-1.0-SNAPSHOT.jar` 檔案複製到 HDInsight 叢集：
   
        scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
   
    以叢集的 SSH 使用者取代 **USERNAME**，並以叢集的名稱取代 **CLUSTERNAME**。 出現提示時，請輸入 SSH 使用者的密碼。

4. `scp` 命令完成檔案複製後，請使用 SSH 連接到叢集，然後使用下列命令來啟動串流程序：
   
        ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
   
    此命令會在背景中啟動串流程序。

5. 使用以下命令將訊息傳送至 `test` 主題。 以下是串流範例所處理的內容：
   
        ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &

6. 使用下列命令來檢視寫入 `wordcounts` 主題的輸出︰
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
   
    > [!NOTE]
    > 我們必須告知取用者列印金鑰 (其中包含文字值)，以及告知還原序列化程式使用金鑰和值，以便檢視資料。
   
    輸出大致如下：
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
        a       13805
        snow    13637
   
    請注意，每次遇到一個單字，計數就會遞增。

7. 使用 __Ctrl + C__ 結束取用者，然後使用 `fg` 命令將串流背景工作帶回前景。 使用 __Ctrl + C__ 加以結束。

## <a name="next-steps"></a>後續步驟

在本文件中，您已學會使用 Apache Kafka on HDInsight 的基本概念。 使用下列各項來深入了解 Kafka 的使用方式︰

* kafka.apache.org 上的 [Apache Kafka 文件](http://kafka.apache.org/documentation.html)。
* [使用 MirrorMaker 建立 Apache Kafka on HDInsight 複本](hdinsight-apache-kafka-mirroring.md)
* [使用 Apache Storm 搭配 Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md)
* [使用 Apache Spark 搭配 Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md)




<!--HONumber=Nov16_HO3-->


