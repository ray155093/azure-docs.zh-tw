---
title: "搭配使用 Azure Data Lake Store 與 Azure HDInsight 上的 Apache Storm"
description: "了解如何從 HDInsight 上的 Apache Storm 拓撲將資料寫入到 Azure Data Lake Store。 本文件與相關聯的範例會示範如何使用 HdfsBolt 元件來寫入至 Data Lake Store。"
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/03/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 376415d34592d18de00513ee9142512eb716e426
ms.lasthandoff: 03/04/2017


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight-java"></a>搭配 Apache Storm 與 HDInsight 使用 Azure Data Lake Store (Java)

Azure Data Lake Store 是 HDFS 相容的雲端儲存體服務，可為資料提供高輸送量、可用性、持久性及可靠性。 在本文件中，您將了解如何使用以 Java 為基礎的 Storm 拓撲，將資料寫入 Azure Data Lake Store。 此文件中使用 [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 元件的步驟，此元件是提供來做為 Apache Storm 的一部分。

> [!IMPORTANT]
> 本文件中使用的範例拓撲需依賴 Storm on HDInsight 叢集隨附的元件，而且可能需要進行修改才能在搭配其他 Apache Storm 叢集使用時使用 Azure Data Lake Store。

## <a name="how-to-work-with-azure-data-lake-store"></a>如何使用 Azure Data Lake Store

Data Lake Store 對 HDInsight 來說會顯示為 HDFS 相容檔案系統，因此您可以 Storm-HDFS Bolt 來寫入它。 從 HDInsight 使用 Azure Data Lake 時，您可以使用 `adl://` 的檔案配置。

* 如果 Data Lake 儲存體是叢集的主要儲存體，請使用 `adl:///`。 這是 Azure Data Lake 中叢集儲存體的根。 這可能會轉譯為 Data Lake 儲存體帳戶中 /clusters/CLUSTERNAME 的路徑。
* 如果 Data Lake 儲存體是叢集的次要儲存體，請使用 `adl://DATALAKEACCOUNT.azuredatalakestore.net/`。 這個 URI 會指定要寫入資料的 Data Lake 儲存體帳戶。 資料是從 Data Lake Store 的根開始寫入。

    > [!NOTE]
    > 您也可以使用這個 URI 格式，來將資料儲存到包含您叢集主要儲存體的 Data Lake Store 帳戶。 這可讓您將資料儲存於包含 HDInsight 的目錄路徑以外的位置。

下列 Java 程式碼示範如何使用 Storm-HDFS Bolt，將資料寫入名為 `MYDATALAKE` 之 Data Lake Store 帳戶上名為 `/stormdata` 的目錄。

```java
// 1. Create sync and rotation policies to control when data is synched
//    (written) to the file system and when to roll over into a new file.
SyncPolicy syncPolicy = new CountSyncPolicy(1000);
FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
// 2. Set the format. In this case, comma delimited
RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
// 3. Set the directory name. In this case, '/stormdata/'
FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
// 4. Create the bolt using the previously created settings,
//    and also tell it the base URL to your Data Lake Store.
// NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
HdfsBolt adlsBolt = new HdfsBolt()
    .withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
        .withRecordFormat(recordFormat)
        .withFileNameFormat(fileNameFormat)
        .withRotationPolicy(rotationPolicy)
        .withSyncPolicy(syncPolicy);
// 4. Give it a name and wire it up to the bolt it accepts data
//    from. NOTE: The name used here is also used as part of the
//    file name for the files written to Data Lake Store.
builder.setBolt("ADLStoreBolt", adlsBolt, 1)
    .globalGrouping("finalcount");
```

下列 YAML 示範如何從 Flux 架構使用 Storm HDFS Bolt：

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - KB

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

  - id: "rotationAction"
    className: "org.apache.storm.hdfs.common.rotation.MoveFileAction"
    configMethods:
      - name: "toDestination"
        args: ["${hdfs.dest.dir}"]

# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1
```

> [!NOTE]
> 本文件中的範例會使用 Flux 架構。

## <a name="prerequisites"></a>必要條件

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 或更新版本。 HDInsight 3.5 需要 Java 8。

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Storm on HDInsight 叢集 3.5 版。 若要建立新的 Storm on HDInsight 叢集，請使用 [使用 Azure 來搭配使用 HDInsight 與 Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) 文件中的步驟。

### <a name="configure-environment-variables"></a>設定環境變數

當您在開發工作站上安裝 Java 和 JDK 時可能會設定下列環境變數。 不過，您應該檢查它們是否存在，以及它們是否包含您系統的正確值。

* **JAVA_HOME** - 應該指向已安裝 Java 執行階段環境 (JRE) 的目錄。 例如，在 Unix 或 Linux 散發套件上，它的值應該類似 `/usr/lib/jvm/java-8-oracle` 在 Windows 中，它的值應該類似 `c:\Program Files (x86)\Java\jre1.8`。
* **PATH** - 應該包含下列路徑：
  
  * **JAVA\_HOME** (或對等的路徑)
  * **JAVA\_HOME\bin** (或對等的路徑)
  * 已安裝 Maven 的目錄

## <a name="topology-implementation"></a>拓撲實作

本文件中使用的範例是以 Java 撰寫，並使用下列元件：

* **TickSpout**：產生拓撲中其他元件所使用的資料。
* **PartialCount**：計算 TickSpout 所產生的事件。
* **FinalCount**：彙總來自 PartialCount 的計數資料。
* **ADLStoreBolt**：使用 [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 元件，將資料寫入至 Azure Data Lake Store。

包含此拓撲的專案可從 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store)下載。

## <a name="build-and-package-the-topology"></a>建置和封裝拓撲

1. 從 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 下載範例專案到開發環境。

2. 在編輯器中開啟 `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` 檔案，然後尋找包含 `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")` 的程式碼行。 將 **MYDATALAKE** 變更為建立 HDInsight 伺服器時所使用的 Azure Data Lake Store 名稱。

3. 從命令提示字元、終端機或 Shell 工作階段，將目錄變更為所下載專案的根目錄，然後執行下列命令來建置和封裝拓撲。
   
        mvn compile
        mvn package
   
    建置和封裝完成之後，會有名為 `target` 的新目錄，其中包含名為 `StormToDataLakeStore-1.0-SNAPSHOT.jar` 的檔案。 這會包含已編譯的拓撲。

## <a name="deploy-and-run-the-topology"></a>部署並執行拓撲

1. 使用下列命令將拓撲複製到 HDInsight 叢集。 將 **USER** 取代為建立叢集時所使用的 SSH 使用者名稱。 將 **CLUSTERNAME** 取代為叢集的名稱。
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    出現提示時，請輸入建立叢集的 SSH 使用者時所使用的密碼。 如果您使用公開金鑰而非密碼，則可能必須使用 `-i` 參數來指定相對應私密金鑰的路徑。
   
   > [!NOTE]
   > 如果您使用 Windows 用戶端進行開發，則可能沒有 `scp` 命令。 若是如此，您可以使用 `pscp`，其可從下列位置取得： [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

2. 上傳完成後，使用下列命令來透過 SSH 連接到 HDInsight 叢集。 將 **USER** 取代為建立叢集時所使用的 SSH 使用者名稱。 將 **CLUSTERNAME** 取代為叢集的名稱。
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    出現提示時，請輸入建立叢集的 SSH 使用者時所使用的密碼。 如果您使用公開金鑰而非密碼，則可能必須使用 `-i` 參數來指定相對應私密金鑰的路徑。
   
   > [!NOTE]
   > 如果您使用 Windows 用戶端進行開發，請依照 [從 Windows 使用 SSH 連接至 Linux 型 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) 中的資訊，來取得使用 PuTTY 用戶端連接到叢集的資訊。

3. 連接之後，使用下列命令來建立名為 `dev.properties` 的檔案：

        nano dev.properties

4. 使用下列文字做為 `dev.properties` 檔案的內容：

        hdfs.write.dir: /stormdata
        hdfs.url: adl:///
    
    若要儲存檔案，使用 __Ctrl + X__，然後是 __Y__，最後按 __Enter__。 此檔案中的值會設定 Data Lake Store URL，以及要寫入資料的目錄名稱。

3. 使用下列命令來啟動拓撲：
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /datalakewriter.yaml --filter dev.properties

    此命令會使用 Flux 架構來啟動拓撲。 拓撲是由 jar 中包含的 `datalakewriter.yaml` 檔案所定義。 `dev.properties` 檔案會傳遞來做為篩選，而檔案中包含的值是透過拓撲來讀取。

## <a name="view-output-data"></a>檢視輸出資料

若要檢視資料，請使用下列命令：

    hdfs dfs -ls /stormdata/

這會顯示檔案所建立的拓撲清單。

如果 Data Lake Store 不是叢集的預設儲存體，請使用下列命令來檢視資料：

    hdfs dfs -ls adl://MYDATALAKE.azuredatalakestore.net/stormdata/

在上一個命令中，使用 Data Lake Store 帳戶來取代 __MYDATALAKE__。

下列清單是前一個命令所傳回的資料範例：

    Found 30 items
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-13-1488568412603.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-14-1488568415055.txt

## <a name="stop-the-topology"></a>停止拓撲

Storm 拓撲會一直執行，直到其停止或叢集遭到刪除。 若要停止拓撲，請使用下列資訊。

**如果是以 Linux 為基礎的 HDInsight**：

在連往叢集的 SSH 工作階段中，使用下列命令：

    storm kill datalakewriter

## <a name="delete-your-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>後續步驟

現在，您已了解如何使用 Storm 來寫入至 Azure Data Lake Store，接下來請探索其他 [HDInsight 的 Storm 範例](hdinsight-storm-example-topology.md)。


