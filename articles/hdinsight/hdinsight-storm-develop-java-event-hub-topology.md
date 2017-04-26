---
title: "使用 Java 在事件中樞內透過 Storm on HDInsight 處理事件 | Microsoft Docs"
description: "了解如何使用 Maven 建立的 Java Storm 拓撲處理事件中樞資料。"
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: e650731c3186b47adeb0e799a852961c30338550
ms.lasthandoff: 04/12/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>使用 Storm on HDInsight 處理 Azure 事件中樞的事件 (Java)

了解如何透過 Storm on HDInsight 使用 Azure 事件中樞。 此範例使用 Java 架構的元件，在 Azure 事件中樞讀取和寫入資料。

Azure 事件中樞可讓您從網站、應用程式和裝置處理巨量資料。 事件中樞 Spout 可讓您輕鬆地使用 Apache Storm on HDInsight 進行即時分析資料。 您也可以使用事件中樞 Bolt 將資料從 Storm 寫入事件中樞。

## <a name="prerequisites"></a>必要條件

* Apache Storm on HDInsight cluster 3.5 版。 如需詳細資訊，請參閱[開始使用 Storm on HDInsight cluster](hdinsight-apache-storm-tutorial-get-started-linux.md)。

    > [!IMPORTANT]
    > Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 3.3 和 3.4 取代](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)。

* [Azure 事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)。

* [Oracle Java Developer Kit (JDK) 第 8 版](http://www.oracle.com/technetwork/java/javase/downloads/index.html)或同等版本，例如 [OpenJDK](http://openjdk.java.net/)。

* [Maven](https://maven.apache.org/download.cgi)：Maven 是 Java 專案的專案建置系統。

* 文字編輯器或整合開發環境 (IDE)。

    > [!NOTE]
    > 您的編輯器或 IDE 可能具有處理 Maven 的特定功能，但未記載在這份文件中。 如需編輯環境功能的詳細資訊，請參閱所使用產品的文件。

    * SSH 用戶端。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

* SCP 用戶端。 所有 Linux、Unix、OS X 系統 (包括 Bash on Windows 10) 皆提供 `scp` 命令。針對沒有 `scp` 命令的 Windows 系統，我們建議使用 PSCP。 可從 [PuTTY 下載頁面](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 取得 PSCP。

## <a name="understanding-the-example"></a>了解範例

[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) 範例包含兩個拓撲：

**com.microsoft.example.EventHubWriter** 將隨機資料寫入 Azure 事件中樞。 資料由 Spout 產生，而且是隨機裝置識別碼和裝置值。 所以它是模擬會發出字串識別碼和數值的部分硬體。

**com.microsoft.example.EventHubReader** 會從事件中樞讀取資料，並將其儲存至 /devicedata 目錄中的叢集預設儲存體。

資料會在寫入事件中樞之前格式化為 JSON 文件，當讀取器讀取時會從 JSON 剖析，並且進入 Tuple。 JSON 格式如下所示：

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>專案組態

`POM.xml` 檔案包含此 Maven 專案的組態資訊。 有趣的部分是：

#### <a name="hortonworks-repository"></a>Hortonworks 存放庫

HDInsight 是以 Hortonworks Data Platform 為基礎。 為確定您的專案與搭配 HDInsight 3.5 使用的 Storm 和 Hadoop 版本相容，下一節會設定專案以使用來自 Hortonworks 的資料：

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>http://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>http://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

#### <a name="the-eventhubs-storm-spout-dependency"></a>EventHubs Storm Spout 相依性

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

這個 xml 會定義 eventhubs 套件的相依性，當中包含用以從「事件中樞」讀取的 Spout 和寫入「事件中樞」的 Bolt。

> [!NOTE]
> 本文件稍後會安裝此套件。

#### <a name="the-hdfsbolt-and-wasb-components"></a>HdfsBolt 和 WASB 元件

HdfsBolt 一般是用來將資料儲存至 Hadoop 分散式檔案系統 HDFS。 不過，HDInsight 叢集會使用 Azure 儲存體 (WASB) 做為預設的資料存放區，所以我們必須載入數個元件，讓 HdfsBolt 了解 WASB 檔案系統。

```xml
<!--HdfsBolt stuff -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-hdfs</artifactId>
    <!-- exclude these storm-hdfs dependencies since they are on the server -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
        </exclusion>
    </exclusions>
    <version>${storm.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>${hadoop.version}</version>
    <exclusions>
    <exclusion>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
    </exclusion>
    </exclusions>
</dependency>
```

> [!NOTE]
> 與舊版 HDInsight 搭配運作 (例如 3.2 版) 時，您必須手動註冊這些元件。 如需範例，請參閱範例儲存機制的 [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) 分支。

#### <a name="the-maven-compiler-plugin"></a>maven-compiler-plugin

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.3.2</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

這會將專案設定為產生 Java 8 的輸出，輸出將由 HDInsight 3.5 使用。

#### <a name="the-maven-shade-plugin"></a>maven-shade-plugin

```xml
<!-- build an uber jar -->
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-shade-plugin</artifactId>
<version>2.3</version>
<configuration>
    <transformers>
    <!-- Keep us from getting a can't overwrite file error -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
    <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
    </transformers>
    <!-- Keep us from getting a bad signature error -->
    <filters>
    <filter>
        <artifact>*:*</artifact>
        <excludes>
            <exclude>META-INF/*.SF</exclude>
            <exclude>META-INF/*.DSA</exclude>
            <exclude>META-INF/*.RSA</exclude>
        </excludes>
    </filter>
    </filters>
</configuration>
<executions>
    <execution>
    <phase>package</phase>
    <goals>
        <goal>shade</goal>
    </goals>
    </execution>
</executions>
</plugin>
```

這會將解決方案設定為把輸出封裝為 uber jar 。 jar 包含專案程式碼和必要的相依性。 它也用來：

* 重新命名相依性的授權檔案。
* 排除安全性/簽章。
* 請確定介面相同的多個實作會合併成一個項目。

這些組態設定可防止執行階段錯誤。

#### <a name="the-exec-maven-plugin"></a>exec-maven-plugin

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.2.1</version>
    <executions>
    <execution>
    <goals>
        <goal>exec</goal>
    </goals>
    </execution>
    </executions>
    <configuration>
    <executable>java</executable>
    <includeProjectDependencies>true</includeProjectDependencies>
    <includePluginDependencies>false</includePluginDependencies>
    <classpathScope>compile</classpathScope>
    <mainClass>${storm.topology}</mainClass>
    </configuration>
</plugin>
```

這個設定讓您可以輕鬆地在開發環境上本機執行拓撲。 您可以使用下列語法在本機執行拓撲：

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

例如， `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`。

#### <a name="the-resources-section"></a>資源區段

```xml
<resources>
    <resource>
    <directory>${basedir}/conf</directory>
    <filtering>false</filtering>
    <includes>
        <include>EventHubs.properties</include>
    </includes>
    </resource>
</resources>
```

這個設定會定義專案中包含的非 Java 資源。 例如，**EventHubs.properties** 檔案包含用來連線到 Azure 事件中樞的資訊。

## <a name="configure-environment-variables"></a>設定環境變數

當您在開發工作站上安裝 Java 和 JDK 時可能會設定下列環境變數。 不過，您應該檢查它們是否存在，以及它們是否包含您系統的正確值。

* **JAVA_HOME** - 應該指向已安裝 Java 執行階段環境 (JRE) 的目錄。 例如，在 Unix 或 Linux 散發套件上，它的值應該類似 `/usr/lib/jvm/java-7-oracle` 在 Windows 中，它的值應該類似 `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - 應該包含下列路徑：

  * **JAVA_HOME** (或對等的路徑)
  * **JAVA_HOME\bin** (或對等的路徑)
  * 已安裝 Maven 的目錄

## <a name="download-and-register-the-eventhub-components"></a>下載並註冊 EventHub 元件

1. 從 [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar) 下載 `storm-eventhubs-1.0.2-jar-with-dependencies.jar`。 這個檔案包含從 EventHubs 讀取和寫入的 Spout 和 Bolt 元件。

2. 在您的本機 Maven 儲存機制中使用下列命令註冊元件︰

        mvn install:install-file -Dfile=storm-eventhubs-1.0.2-jar-with-dependencies.jar -DgroupId=com.microsoft -DartifactId=eventhubs -Dversion=1.0.2 -Dpackaging=jar

    將 `-Dfile=` 參數修改為指向下載的檔案位置。

    此命令會將檔案安裝在本機 Maven 儲存機制，Maven 可在編譯時期找到 它。

## <a name="configure-event-hub"></a>設定事件中樞

事件中樞是此範例的資料來源。 使用下列步驟建立事件中樞。

1. 從 [Azure 傳統入口網站](https://manage.windowsazure.com)選取 [新增]  >  [服務匯流排]  >  [事件中樞]  > [自訂建立]。

2. 在 [新增事件中樞] 畫面上，輸入 [事件中樞名稱]。 選取要建立中樞的 [區域]，然後建立新的命名空間或選取現有的命名空間。 最後，按一下**箭頭**進行下一步。

    ![精靈頁面 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

   > [!NOTE]
   > 選取與 Storm on HDInsight 伺服器相同的 [位置]，可降低延遲和成本。

3. 在 [設定事件中樞] 畫面中，輸入 [資料分割計數] 及 [訊息保留] 值。 在此範例中，資料分割計數使用 10，訊息保留使用 1。 請記下資料分割計數，因為您稍後會用到這個值。

    ![精靈頁面 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

4. 建立事件中樞之後，依序選取命名空間、[事件中樞] ，然後選取您先前建立的事件中樞。
5. 選取 [設定]，然後使用下列資訊建立兩個新的存取原則：

    <table>
    <tr><th>名稱</th><th>權限</th></tr>
    <tr><td>寫入器</td><td>傳送</td></tr>
    <tr><td>讀取者</td><td>接聽</td></tr>
    </table>

    建立權限之後，在頁面底部選取 **儲存** 圖示。 這些共用的存取原則是用來讀取和寫入事件中樞。

    ![原則](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

6. 儲存原則之後，請使用頁面底部的 [共用存取金鑰產生器] 來擷取 [寫入器] 和 [讀取器] 原則的金鑰。 儲存這些金鑰。

## <a name="download-and-build-the-project"></a>下載和建置專案

1. 從 GitHub 下載專案： [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)。 您可以將封裝下載為 zip 封存，或使用 [git](https://git-scm.com/) 以在本機複製專案。

2. 使用下列項目建置和封裝專案：

        mvn package

    這個命令會下載必要的相依性、進行建置，然後封裝專案。 輸出會在 **/target** 目錄儲存為 **EventHubExample-1.0-SNAPSHOT.jar**。

## <a name="deploy-the-topologies"></a>部署拓撲

此專案所建立的 jar 包含兩種拓撲；**com.microsoft.example.EventHubWriter** 和 **com.microsoft.example.EventHubReader**。 應該先啟動 EventHubWriter 拓撲，因為它會將事件寫入事件中樞，然後由 EventHubReader 讀取。

1. 使用 SCP 將 jar 封裝複製到您的 HDInsight 叢集。 將 USERNAME 取代為用於您的叢集的 SSH 使用者。 將 CLUSTERNAME 取代為 HDInsight 叢集的名稱：

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    如果您針對 SSH 帳戶使用密碼，系統會提示您輸入密碼。 如果您搭配帳戶使用 SSH 金鑰，可能需要使用 `-i` 參數來指定金鑰檔的路徑。 例如， `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`。

    此命令會將檔案複製到叢集上 SSH 使用者的主目錄中。

2. 完成上傳檔案之後，使用 SSH 以連接到 HDInsight 叢集。 將 **USERNAME** 取代為您的 SSH 登入名稱。 將 **CLUSTERNAME** 取代為 HDInsight 叢集名稱：

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!NOTE]
    > 如果您針對 SSH 帳戶使用密碼，系統會提示您輸入密碼。 如果您搭配帳戶使用 SSH 金鑰，可能需要使用 `-i` 參數來指定金鑰檔的路徑。 下列範例會從 `~/.ssh/id_rsa` 載入私密金鑰：
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. 使用下列命令以啟動拓撲：

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    這些命令會使用易記名稱 "reader" 和 "writer" 啟動拓撲。

4. 等候一分鐘讓拓樸產生資料。 使用下列命令來確認資料是否已寫入 HDInsight 儲存體：

        hdfs dfs fs -ls /devicedata

    此命令會傳回檔案清單，類似以下文字：

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

   > [!NOTE]
   > 某些檔案可能會顯示大小為 0，因為它們是由 EventHubReader 建立，但是資料尚未儲存至該處。

    您可以使用下列命令檢視這些檔案的內容：

        hdfs dfs -text /devicedata/*.txt

    這會傳回類似以下文字的資料︰

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    第一個資料行包含裝置識別碼值，第二個資料行是裝置值。

5. 使用下列命令以停止拓撲：

        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshooting"></a>疑難排解

如果在 /devicedata 目錄找不到檔案，使用 Storm UI 尋找拓撲所傳回的任何錯誤。

如需使用 Storm UI 的詳細資訊，請參閱下列主題：

* 如果您是在 HDInsight 叢集上使用 **以 Linux 為基礎的** Storm，請參閱 [部署和管理以 Linux 為基礎的 HDInsight 上的 Apache Storm 拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

* 如果您是在 HDInsight 叢集上使用 **以 Windows 為基礎的** Storm，請參閱 [部署和管理以 Windows 為基礎的 HDInsight 上的 Apache Storm 拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="next-steps"></a>後續步驟

* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

