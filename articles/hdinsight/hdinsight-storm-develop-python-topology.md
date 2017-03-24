---
title: "在 HDinsight 上於 Storm 拓撲中使用 Python 元件 | Microsoft Docs"
description: "了解如何在 Azure HDInsight 上從 Apache Storm 中Python 元件。 您將了解如何從 Java 和 Clojure 型 Storm 拓撲中使用 Python 元件。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: f6bc08230384b637f6b7b83fe32915ab09647d37
ms.lasthandoff: 01/18/2017


---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>在 HDInsight 上使用 Python 開發 Apache Storm 拓撲

Apache Storm 支援多種語言，甚至可讓您將數種語言的元件結合成一個拓撲。 本文件中，您將了解如何在 HDInsight 上於 Java 和 Clojure 型 Storm 拓撲中使用 Python 元件。

> [!IMPORTANT]
> 本文件提供有關使用 Windows 和 Linux 型 HDInsight 叢集的步驟。 Linux 是 HDInsight 3.4 版或更新版本上唯一使用的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

## <a name="prerequisites"></a>必要條件

* Python 2.7 或更新版本
* Java JDK 1.7 或更新版本
* [Leiningen](http://leiningen.org/)

## <a name="storm-multi-language-support"></a>Storm 多語言支援

Storm 設計為可以與使用任何程式設計語言撰寫的元件一起使用，但元件必須了解如何使用 [Storm 的 Thrift 定義](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)。 在 Python 中，Apache Storm 專案隨附一個模組，可讓您輕鬆地與 Strom 互動。 您可以在 [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)找到此模組。

由於 Apache Storm 是在 Java 虛擬機器 (JVM) 上執行的 Java 程序，以其他語言撰寫的元件會以子程序方式執行。 JVM 中執行的 Storm 程式碼會使用透過 stdin/stdout 傳送的 JSON 訊息，與這些子程序進行通訊。 如需各元件之間通訊的詳細資訊，請參閱 [多語言通訊協定](https://storm.apache.org/documentation/Multilang-protocol.html) 文件。

### <a name="the-storm-module"></a>Storm 模組
Storm 模組 (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)，提供建立 Python 元件 (使用 Storm) 所需的位元。

例如，其中有 `storm.emit` 可發出 Tuple、`storm.logInfo` 可寫入記錄檔。 建議您詳閱這個檔案，了解它所提供的內容。

## <a name="challenges"></a>挑戰
您可以使用 **storm.py** 模組建立 Python spout 來取用資料、建立 bolt 來處理資料，但是，負責設置元件之間通訊的整個 Storm 拓撲定義，仍是使用 Java 或 Clojure 撰寫。 此外，如果您使用 Java，您也必須建立 Java 元件做為 Python 元件的介面。

另外，因為 Storm 叢集是以分散式方式執行，您必須確定任何 Python 元件所需的任何模組，可供叢集的所有背景工作角色節點使用。 對於多語言的資源，Storm 沒有辦法輕鬆完成此工作，您必須將所有相依性納入拓撲的 jar 檔案中，或在叢集的每一個背景工作角色節點上手動安裝相依性。

### <a name="java-vs-clojure-topology-definition"></a>Java 與Clojure 拓撲定義的比較
在兩種定義拓撲的方法，Clojure 是目前為止最簡單/最乾淨的方法，因為您可以在拓樸定義中直接參考 python 元件。 對於 Java 型拓樸定義，您也必須定義 Java 元件來處理一些工作，例如在 Python 元件傳回的 Tuple 中宣告欄位。

本文件說明這兩種方法，並附上範例專案。

## <a name="python-components-with-a-java-topology"></a>使用 Java 拓撲的 Python 元件
> [!NOTE]
> 此範例可於 [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) 的 **JavaTopology** 目錄中取得。 這是一個 Maven 型專案。 如果您不熟悉 Maven，請參閱 [在 HDInsight 上使用 Apache Storm 開發以 Java 為基礎的拓撲](hdinsight-storm-develop-java-topology.md) ，以取得有關為 Storm 拓撲建立 Maven 專案的詳細資訊。
> 
> 

使用 Python (或其他 JVM 語言元件) 的 Java 型拓樸乍看之下是使用 Java 元件，但如果查看每個 Java spout/bolt，您會看到類似下列的程式碼：

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Java 在這裡叫用 Python，並執行含有實際 blot 邏輯的指令碼。 Java spout/bolt (針對此範例) 只是在基礎 Python 元件將發出的 Tuple 中宣告欄位。

在此範例中，實際 Python 檔案儲存在 `/multilang/resources` 目錄中。 **pom.xml** 中會參考 `/multilang` 目錄：

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir}/multilang</directory>
    </resource>
</resources>

這會將 `/multilang` 資料夾中的所有檔案，併入到從這個專案建置的 jar 中。

> [!IMPORTANT]
> 請注意，這僅指定 `/multilang` 目錄，而不是 `/multilang/resources`。 Storm 預期非 JVM 資源都在 `resources` 目錄中，因此在內部已尋找過它。 將元件放在此資料夾中，可讓您在 Java 程式碼中直接依名稱參考。 例如， `super("python", "countbolt.py");`。 另一種看法是 Storm 存取多語言的資源時會將 `resources` 目錄視為根目錄 (/)。
> 
> 針對此範例專案，`storm.py` 模組位於 `/multilang/resources` 目錄中。
> 
> 

### <a name="build-and-run-the-project"></a>建置並執行專案
若要在本機執行此專案，只要使用下列 Maven 命令建置，並以本機模式執行即可：

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

使用 ctrl+c 可結束程序。

若要將專案部署至執行 Apache Storm 的 HDInsight 叢集，請使用下列步驟：

1. 建置 uber jar：
   
        mvn package
   
    這樣會在此專案的 `/target` 目錄中建立名為 **WordCount--1.0-SNAPSHOT.jar** 的檔案。
2. 使用下列其中一種方法，將 jar 檔案上傳至 Hadoop 叢集：
   
   * **以 Linux 為基礎的** HDInsight 叢集：使用 `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` 將 jar 檔案複製到叢集，並以您的 SSH 使用者名稱取代 USERNAME，以 HDInsight 叢集名稱取代 CLUSTERNAME。
     
       檔案上傳完成之後，使用 SSH 連接到叢集，然後使用 `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`
   * **以 Windows 為基礎的** HDInsight 叢集：在瀏覽器中移至 HTTPS://CLUSTERNAME.azurehdinsight.net/，以連接到 Storm 儀表板。 以您的 HDInsight 叢集名稱取代 CLUSTERNAME，在出現提示時，提供系統管理員名稱和密碼。
     
       使用表單執行下列動作：
     
     * **Jar 檔案**：選取 [瀏覽]，然後選取 **wordcount-1.0-SNAPSHOT.jar** 檔案
     * **類別名稱**：輸入 `com.microsoft.example.WordCount`
     * **其他參數**：輸入易記名稱來識別拓撲，例如 `wordcount`
       
       最後，選取 [提交]  啟動拓撲。

> [!NOTE]
> Storm 拓撲啟動之後會一直執行到停止 (刪除) 為止。若要停止拓撲，請從命令列 (例如，Linux 叢集的 SSH 工作階段) 使用 `storm kill TOPOLOGYNAME` 命令，或使用 Storm UI 選取拓撲，然後選取 [刪除] 按鈕。
> 
> 

## <a name="python-components-with-a-clojure-topology"></a>使用 Clojure 拓撲的 Python 元件
> [!NOTE]
> 此範例可於 [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) 的 **ClojureTopology** 目錄中取得。
> 
> 

此拓撲由 [Leiningen](http://leiningen.org) 建立，用於[建立新的 Clojure 專案](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project)。 之後，已建立結構的專案經過下列修改：

* **project.clj**：加入 Storm 的相依性，並排除部署至 HDInsight 伺服器時可能造成問題的項目。
* **resources/resources**：Leiningen 建立預設的 `resources` 目錄，不過，這裡儲存的檔案似乎會新增至根據這個專案所建立的 jar 檔案的根目錄，但 Storm 需要這些檔案位於名為 `resources` 的子目錄中。 因此加入一個子目錄，Python 檔案就儲存在 `resources/resources`中。 在執行階段，這當成存取 Python 元件時的根目錄 (/)。
* **src/wordcount/core.clj**：此檔案包含拓撲定義，而且由 **project.clj** 檔案參考。 如需有關使用 Clojure 定義 Storm 拓撲的詳細資訊，請參閱 [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html)。

### <a name="build-and-run-the-project"></a>建置並執行專案
**如果要在本機建置並執行專案**，請使用下列命令：

    lein clean, run

若要停止拓撲，請使用 **Ctrl+C**。

**如果要建置 Uberjar 並部署至 HDInsight**，請使用下列步驟：

1. 建立包含拓撲和必要相依性的 uberjar：
   
        lein uberjar
   
    這會建立在 `wordcount-1.0-SNAPSHOT.jar` 的 `target\uberjar+uberjar` 目錄中取得。
2. 使用下列其中一種方法，將拓撲部署至 HDInsight 叢集來執行：
   
   * **以 Linux 為基礎的 HDInsight**
     
     1. 使用 `scp`將檔案複製到 HDInsight 叢集前端節點。 例如：
        
             scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
        
         以您叢集的 SSH 使用者取代 USERNAME，並以您的 HDInsight 叢集名稱取代 CLUSTERNAME。
     2. 將檔案複製到叢集之後，使用 SSH 連接到叢集並提交工作。 如需有關搭配使用 SSH 與 HDInsight 的資訊，請參閱下列其中一份文件：
        
        * [從 Linux、Unix 或 OS X 搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [從 Windows 搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)
     3. 連線之後，使用下列命令啟動拓撲：
        
             storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
   * **以 Windows 為基礎的 HDInsight**
     
     1. 在瀏覽器中移至 HTTPS://CLUSTERNAME.azurehdinsight.net/，以連接到 Storm 儀表板。 以您的 HDInsight 叢集名稱取代 CLUSTERNAME，在出現提示時，提供系統管理員名稱和密碼。
     2. 使用表單執行下列動作：
        
        * **Jar 檔案**：選取 [瀏覽]，然後選取 **wordcount-1.0-SNAPSHOT.jar** 檔案
        * **類別名稱**：輸入 `wordcount.core`
        * **其他參數**：輸入易記名稱來識別拓撲，例如 `wordcount`
          
          最後，選取 [提交]  啟動拓撲。

> [!NOTE]
> Storm 拓撲啟動之後會一直執行到停止 (刪除) 為止。若要停止拓撲，請從命令列 (Linux 叢集的 SSH 工作階段) 使用 `storm kill TOPOLOGYNAME` 命令，或使用 Storm UI 選取拓撲，然後選取 [刪除] 按鈕。
> 
> 

## <a name="next-steps"></a>後續步驟
本文件中，您已了解如何從 Storm 拓撲中使用 Python 元件。 請參閱下列文件，了解搭配使用 Python 與 HDInsight 的其他方式。

* [如何使用 Python 串流處理 MapReduce 工作](hdinsight-hadoop-streaming-python.md)
* [如何在 Pig 和 Hive 中使用 Python 使用者定義函數 (UDF)](hdinsight-python.md)


