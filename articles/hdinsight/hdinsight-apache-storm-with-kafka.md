---
title: "使用 Apache Kafka 搭配 Storm on HDInsight | Microsoft Docs"
description: "Apache Kafka 會隨著 Apache Storm on HDInsight 一起安裝。 了解如何使用 Storm 隨附的 KafkaBolt 和 KafkaSpout 元件來寫入 Kafka，然後從中讀取。 並且了解如何使用 Flux 架構來定義及提交 Storm 拓撲。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: dcda5e27cbcadff054c8085b72a1b6fb1c07b889
ms.lasthandoff: 03/25/2017

---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>使用 Apache Kafka (預覽) 搭配 Storm on HDInsight

Apache Kafka 是適用於 HDInsight 的發佈-訂閱訊息解決方案。 Apache Storm 是可用來即時分析資料的分散式系統。 本文件將示範如何使用 Storm on HDInsight 來讀取和處理來自 Kafka on HDInsight 的資料。 本文件中的範例會使用 Java 型 Storm 拓撲，其依賴 Apache Storm 提供的 Kafka Spout 和 Bolt 元件。

> [!NOTE]
> 本文件中的步驟建立 Azure 資源群組，其中包含 Storm on HDInsight 和 Kafka on HDInsight cluster 叢集。 這兩個叢集都位於 Azure 虛擬網路中，可讓 Storm 叢集直接與 Kafka 叢集通訊。
> 
> 當您完成本文件中的步驟時，請記得刪除叢集，以避免產生過多的費用。

## <a name="prerequisites"></a>必要條件

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.8 或更新版本。 或同等功能版本，例如 [OpenJDK](http://openjdk.java.net/)。
  
    > [!NOTE]
    > 本文件中的步驟使用 HDInsight 3.5 叢集，而該叢集使用 Java 8。

* [Maven 3.x](http://maven.apache.org/) - Java 應用程式的組建管理套件。

* 文字編輯器或 Java IDE

* SSH 用戶端 (您需要 `ssh` 和 `scp` 命令) - 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="create-the-clusters"></a>建立叢集

Apache Kafka on HDInsight 不提供透過公用網際網路存取 Kafka 訊息代理程式。 任何 Kafka 相關項目必須位於與 Kafka 叢集中節點相同的 Azure 虛擬網路。 例如，Kafka 和 Storm 叢集均位於 Azure 虛擬網路中。 下圖顯示叢集之間的通訊流動方式︰

![Azure 虛擬網路中的 Storm 和 Kafka 叢集圖表](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Kafka 本身受限於虛擬網路內的通訊，但叢集上的 SSH 和 Ambari 等其他服務可以透過網際網路存取。 如需有關適用於 HDInsight 的公用連接埠詳細資訊，請參閱 [HDInsight 所使用的連接埠和 URI](hdinsight-hadoop-port-settings-for-services.md)。


雖然您可以手動建立 Azure 虛擬網路、Kafka 和 Storm 叢集，但使用 Azure Resource Manager 範本更輕鬆。 使用下列步驟將 Azure 虛擬網路、Kafka 和 Storm 叢集部署到 Azure 訂用帳戶。

1. 使用以下按鈕，在 Azure 入口網站中登入 Azure 並開啟範本。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager 範本位於 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet.json**。

2. 使用下列指引來填入 [自訂部署] 刀鋒視窗上的項目︰
   
    ![HDInsight 自訂部署](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **資源群組**：建立群組或選取現有的群組。 此群組包含 HDInsight 叢集。
   
    * **位置**：選取在地理上靠近您的位置。 此位置必須符合 [設定] 區段中的位置。

    * **基底叢集名稱**︰此值會做為 Storm 和 Kafka 叢集的基底名稱。 例如，輸入 **hdi** 可建立名為 **storm-hdi** 的 Storm 叢集以及名為 **kafka-hdi** 的 Kafka 叢集。
   
    * **叢集登入使用者名稱**：Storm 和 Kafka 叢集的系統管理員使用者名稱。
   
    * **叢集登入密碼**：Storm 和 Kafka 叢集的系統管理員使用者密碼。
    
    * **SSH 使用者名稱**︰建立 Storm 和 Kafka 叢集的 SSH 使用者。
    
    * **SSH 密碼**：Storm 和 Kafka 叢集的 SSH 使用者密碼。
    
    * **位置**︰叢集建立所在的區域。

3. 讀取**條款及條件**，然後選取 [我同意上方所述的條款及條件]。

4. 最後，核取 [釘選到儀表板]，然後選取 [購買]。 大約需要 20 分鐘的時間來建立叢集。

建立資源後，您會重新導向至資源群組的刀鋒視窗，其中內含叢集和 Web 儀表板。

![Vnet 和叢集的資源群組刀鋒視窗](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> 請注意，HDInsight 叢集的名稱是 **storm-BASENAME** 和 **kafka-BASENAME**，其中 BASENAME 是您提供給範本的名稱。 連接到叢集時，您會在稍後步驟中使用這些名稱。

## <a name="get-the-code"></a>取得程式碼

在 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) 可取得本文件所述範例的程式碼。

## <a name="understanding-the-code"></a>了解程式碼

此專案包含兩種拓撲︰

* **KafkaWriter**︰由 **writer.yaml** 檔案定義，此拓撲會使用 Apache Storm 隨附的 KafkaBolt 將隨機句子寫入 Kafka。
  
    此拓撲使用自訂 **SentenceSpout** 元件來產生隨機句子。

* **KafkaReader**︰由 **reader.yaml** 檔案定義，此拓撲會使用 Apache Storm 隨附的 KafkaSpout 來讀取 Kafka 中的資料，然後將資料記錄至 stdout。
  
    此拓撲使用自訂 **PrinterBolt** 元件來記錄從 Kafka 讀取的資料。

### <a name="flux"></a>Flux

拓撲是使用 [Flux](https://storm.apache.org/releases/1.0.1/flux.html) 來定義的。 Storm 0.10.x 引進了 Flux，可讓您區隔拓撲組態與程式碼。 若為使用 Flux 架構的拓撲，拓撲定義於 YAML 檔案中。 YAML 檔案可以納為拓撲的一部分，或可在您將拓撲提交到 Storm 伺服器時指定。 Flux 也支援執行階段的變數替代 (在此範例中使用)。

這兩個拓撲應該有下列環境變數︰

* **KAFKATOPIC**：拓樸所讀取/寫入的 Kafka 主題名稱。

* **KAFKABROKERS**：Kafka 訊息代理程式執行所在的主機。 KafkaBolt 在寫入 Kafka 時會使用訊息代理程式資訊。

* **KAFKAZKHOSTS**：Zookeeper 執行所在的主機。

本文件中的步驟示範如何設定這些環境變數。

## <a name="create-a-kafka-topic"></a>建立 Kafka 主題

1. 使用 SSH 連線到 Kafka 叢集。 將 `USERNAME` 替換為建立叢集時所使用的 SSH 使用者名稱。 將 `BASENAME` 替換為建立叢集時所使用的基底名稱。
   
        ssh USERNAME@kafka-BASENAME-ssh.azurehdinsight.net
   
    出現提示時，請輸入您在建立叢集時所使用的密碼。
   
    如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 從連到 Kafka 叢集的 SSH 連線中，使用下列命令來設定適用於 HTTP 登入和叢集名稱的變數。 本節中的其他步驟會用到這些值。

  ```bash
  ADMIN='admin' #replace with the name of the admin account for the cluster
  PASSWORD='password' #replace with the password for the admin account
  ```

3. 使用下列命令來安裝 `jq` 公用程式、擷取叢集名稱，並設定 `KAFKAZKHOSTS` 變數：

  ```bash
  sudo apt -y install jq
  CLUSTERNAME=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name'`
  KAFKAZKHOSTS=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`
  ```

    使用下列命令來擷取叢集名稱：

  ```bash
  echo $CLUSTERNAME
  ```

    此命令的輸出類似下列範例：

  ```bash
  kafka-myhdi
  ```

    使用下列命令來確認已正確設定 `KAFKAZKHOSTS`：

  ```bash
  echo $KAFKAZKHOSTS
  ```

    此命令的輸出類似下列範例：

  ```bash
  zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181
  ```

    儲存 Kafka 叢集名稱和 Zookeeper 主機資訊，因為在 Storm 叢集上啟動拓撲時會用到這些值。

    > [!NOTE]
    > 先前的命令使用 __http://headnodehost:8080/__，這可直接連接到 Ambari。 如果您需要透過網際網路從叢集外部擷取此資訊，您必須改用 __https://kafka-BASENAME.azurehdinsight.net/__。

4. 使用下列命令在 Kafka 中建立主題：

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic stormtest --zookeeper $KAFKAZKHOSTS
  ```

    此命令會使用 `$KAFKAZKHOSTS` 中儲存的主機資訊連接到 Zookeeper，然後建立名為 **stormtest** 的 Kafka 主題。 您可以確認使用下列命令建立的主題可列出主題︰

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
  ```

    此命令的輸出會列出 Kafka 主題，其中應包含新的 **stormtest** 主題。

讓連往 Kafka 叢集的 SSH 連線保持作用中，因為您可以使用它來驗證 Storm 拓撲是否會將訊息寫入主題。

## <a name="download-and-compile-the-project"></a>下載並編譯專案

1. 在開發環境中，從 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) 下載專案，開啟命令列，並將目錄變更為您下載專案的位置。

    需要一些時間才可檢查程式碼並了解專案的運作方式。

2. 從 **hdinsight-storm-java-kafka** 目錄，使用下列命令來編譯專案並建立部署套件︰

  ```bash
  mvn clean package
  ```

    封裝程序會在 `target` 目錄中建立名為 `KafkaTopology-1.0-SNAPSHOT.jar` 的檔案。

3. 使用下列命令將套件複製到 Storm on HDInsight 叢集。 將 **USERNAME** 替換為叢集的 SSH 使用者名稱。 將 **BASENAME** 替換為您在建立叢集時使用的基底名稱。

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    出現提示時，請輸入您在建立叢集時所使用的密碼。

4. 使用下列命令，將 `set-env-variables.sh` 檔案從 `scripts` 專案的目錄複製到 Storm 叢集︰

  ```bash
  scp ./scripts/set-env-variables.sh USERNAME@storm-BASENAME-ssh.azurehdinsight.net:set-env-variables.sh
  ```

    此指令碼可設定 Storm 拓撲用來與 Kafka 叢集通訊的環境變數。

## <a name="start-the-writer"></a>開始寫入器

1. 使用下列命令來透過 SSH 連接到 Storm 叢集。 將 **USERNAME** 替換為建立叢集時所使用的 SSH 使用者名稱。 將 **BASENAME** 替換為建立叢集時使用的基底名稱。

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    出現提示時，請輸入您在建立叢集時所使用的密碼。
   
    如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 在連往 Storm 叢集的 SSH 連線中，使用下列命令來執行 `set-env-variables.sh` 指令碼：

  ```bash
  chmod +x set-env-variables.sh
  . ./set-env-variables.sh KAFKACLUSTERNAME PASSWORD
  ```

    將 __KAFKACLUSTERNAME__ 取代為 Kafka 叢集的名稱。 將 __PASSWORD__ 替換為 Kafka 叢集的系統管理員登入密碼。

    指令碼會連接到 Kafka 叢集並擷取 Kafka 訊息代理程式和 Zookeeper 主機清單。 此資訊接著會儲存在 Storm 拓撲所使用的環境變數。

    指令碼的輸出類似下列範例：

        Checking for jq: install ok installed
        Exporting variables:
        $KAFKATOPIC=stormtest
        $KAFKABROKERS=wn0-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092
        $KAFKAZKHOSTS=zk1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk3-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk5-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181

3. 在連往 Storm 叢集的 SSH 連線中，使用下列命令來啟動寫入器拓撲：

        storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml -e

    此命令使用的參數如下：

    * `org.apache.storm.flux.Flux`︰使用 Flux 來設定及執行此拓撲。

    * `--remote`：將拓撲提交至 Nimbus。 拓撲會分散於叢集中的背景工作節點。

    * `-R /writer.yaml`︰使用 `writer.yaml` 檔案來設定拓撲。 `-R` 表示此資源包含在 jar 檔案中。 剛檔案位於 jar 的根目錄中，所以 `/writer.yaml` 是它的路徑。

    * `-e`︰使用環境變數替代。 Flux 會挑選您之前設定的 $KAFKABROKERS 和 $KAFKATOPIC 值，並在 reader.yaml 檔案中使用這些值來取代 `${ENV-KAFKABROKER}` 和 `${ENV-KAFKATOPIC}` 項目。

5. 拓撲啟動後，切換至連往 Kafka 叢集的 SSH 連線，並使用下列命令來檢視寫入至 **stormtest** 主題的訊息︰

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtest
  ```

    此命令會使用 Kafka 隨附的指令碼來監視主題。 稍後，它應該會開始傳回已寫入主題的隨機句子。 輸出類似於下列範例：

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    使用 Ctrl + c 來停止指令碼。

## <a name="start-the-reader"></a>開始讀取器

1. 在 Storm 叢集的 SSH 工作階段中，使用下列命令來啟動讀取器拓撲：

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml -e
  ```

2. 拓撲啟動後，請開啟 Storm UI。 此 Web UI 位於 https://storm-BASENAME.azurehdinsight.net/stormui。 將 __BASENAME__ 替換為建立叢集時使用的基底名稱。 

    出現提示時，使用建立叢集時所用的系統管理員登入名稱 (預設為 `admin`) 和密碼。 您將會看到類似下列影像的網頁︰

    ![Storm UI](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. 從 Storm UI 選取 [拓樸摘要] 區段中的 __kafka-reader__ 連結，以顯示 __kafka-reader__ 拓樸相關資訊。

    ![Storm Web UI 的拓撲摘要區段](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. 選取 [Bolt (所有時間)] 區段中的 __logger-bolt__ 連結，以顯示 logger-bolt 元件的執行個體相關資訊。

    ![Bolt 區段中的 Logger-bolt 連結](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. 在 [執行程式] 區段中，選取 [連接埠] 欄中的連結，以顯示此元件執行個體的記錄資訊。

    ![執行程式連結](./media/hdinsight-apache-storm-with-kafka/executors.png)

    記錄檔包含從 Kafka 主題讀取的資料記錄檔。 記錄檔中的資訊類似下列文字︰

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>停止拓撲

在連往 Storm 叢集的 SSH 工作階段中，使用下列命令來停止 Storm 拓撲：

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

因為本文件中的步驟會在相同的 Azure 資源群組中建立兩個叢集，您可以在 Azure 入口網站中刪除資源群組。 這可移除依循本文件建立的所有資源、Azure 虛擬網路，以及叢集所使用的儲存體帳戶。

## <a name="next-steps"></a>後續步驟

如需更多可搭配 Storm on HDInsight 使用的拓撲範例，請參閱 [Storm 拓撲和元件範例](hdinsight-storm-example-topology.md)。

如需部署和監視以 Linux 為基礎的 HDInsight 上的拓撲相關資訊，請參閱[部署和管理以 Linux 為基礎的 HDInsight 上的 Apache Storm 拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)


