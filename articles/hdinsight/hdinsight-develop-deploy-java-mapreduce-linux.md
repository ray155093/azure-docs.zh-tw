---
title: "針對以 Linux 為基礎的 HDInsight 開發 Java MapReduce 程式 | Microsoft Docs"
description: "了解如何開發 Java MapReduce 程式，並將這些程式部署到以 Linux 為基礎的 HDInsight。"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: e9b31ebc4c63cb779c8573511101aef991cbbe4a
ms.lasthandoff: 02/21/2017


---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>在 HDInsight Linux 上開發 Hadoop 的 Java MapReduce 程式

了解如何使用 Apache Maven 來建立 Java 結構的 MapReduce 應用程式，然後在以 Linux 為基礎的 HDInsight 中的 Hadoop 叢集上部署並執行該應用程式。

## <a name="a-nameprerequisitesaprerequisites"></a><a name="prerequisites"></a>必要條件

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 或更新版本 (或同等功能版本，例如 OpenJDK)。
    
    > [!NOTE]
    > HDInsight 版本 3.4 和先前版本會使用 Java 7。 HDInsight 3.5 會使用 Java 8。

* [Apache Maven](http://maven.apache.org/)

* **Azure 訂用帳戶**

* **Azure CLI**

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

## <a name="configure-environment-variables"></a>設定環境變數
當您安裝 Java 和 JDK 時可能會設定下列環境變數。 不過，您應該檢查它們是否存在，以及它們是否包含您系統的正確值。

* `JAVA_HOME` - 應該指向已安裝 Java 執行階段環境 (JRE) 的目錄。 例如，在 OS X、Unix 或 Linux 系統上，它的值應該類似 `/usr/lib/jvm/java-7-oracle`。 在 Windows 中，它的值應該類似 `c:\Program Files (x86)\Java\jre1.7`

* `PATH` - 應該包含下列路徑：
  
  * `JAVA_HOME` (或對等的路徑)

  * `JAVA_HOME\bin` (或對等的路徑)

  * 已安裝 Maven 的目錄

## <a name="create-a-maven-project"></a>建立 Maven 專案

1. 從終端機工作階段，或開發環境的命令列中，將目錄變更為您想要儲存此專案的位置。

2. 使用隨 Maven 一起安裝的 `mvn` 命令來產生專案的結構。

   ```
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    此命令會使用 **artifactID** 參數所指定的名稱 (此範例中為 **wordcountjava**) 來建立目錄。此目錄包含下列項目：

   * `pom.xml` - [專案物件模型 (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)，包含用來建置專案的資訊和組態詳細資料。

   * `src` - 包含應用程式的目錄。

3. 刪除 `src/test/java/org/apache/hadoop/examples/apptest.java` 檔案，因為在此範例中不會用到此檔案。

## <a name="add-dependencies"></a>新增相依性

1. 編輯 `pom.xml` 檔案，並在 `<dependencies>` 區段內新增下列文字：
   
   ```xml
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-examples</artifactId>
        <version>2.5.1</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-client-common</artifactId>
        <version>2.5.1</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.5.1</version>
        <scope>provided</scope>
    </dependency>
   ```

    這會定義含有特定版本 (列於&lt;version\> 內) 的必要程式庫 (列於 &lt;artifactId\> 內)。 在編譯期間，會從預設的 Maven 儲存機制下載這些相依性。 您可以使用 [Maven 儲存機制搜尋](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) (英文) 檢視詳細資訊。
   
    `<scope>provided</scope>` 會告訴 Maven 這些相依性不應該和應用程式一起封裝，因為 HDInsight 叢集會在執行階段提供這些相依性。

2. 將下列項目新增至 `pom.xml` 檔案。 此文字必須位於檔案中的 `<project>...</project>` 標籤內，例如在 `</dependencies>` 和 `</project>` 之間。

   ```xml
    <build>
        <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
            <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                </transformer>
            </transformers>
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
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
            <source>1.7</source>
            <target>1.7</target>
            </configuration>
        </plugin>
        </plugins>
    </build>
   ```

    第一個外掛程式會設定用於建置 uberjar (有時稱為 fatjar) 的 [Maven Shade 外掛程式](http://maven.apache.org/plugins/maven-shade-plugin/)，uberjar 內含應用程式需要的相依性。 這麼做也可以防止 jar 封裝中具有重複的授權，以免在某些系統中造成問題。

    第二個外掛程式會設定目標 Java 版本。

    > [!NOTE]
    > HDInsight 3.4 和先前版本會使用 Java 7。 HDInsight 3.5 會使用 Java 8。

3. 儲存 `pom.xml` 檔案。

## <a name="create-the-mapreduce-application"></a>建立 MapReduce 應用程式

1. 移至 `wordcountjava/src/main/java/org/apache/hadoop/examples` 目錄，並將 `App.java` 檔案重新命名為 `WordCount.java`。

2. 在文字編輯器中開啟 `WordCount.java` 檔案，並使用下列文字來取代內容：
   
   ```java
   package org.apache.hadoop.examples;

   import java.io.IOException;
   import java.util.StringTokenizer;
   import org.apache.hadoop.conf.Configuration;
   import org.apache.hadoop.fs.Path;
   import org.apache.hadoop.io.IntWritable;
   import org.apache.hadoop.io.Text;
   import org.apache.hadoop.mapreduce.Job;
   import org.apache.hadoop.mapreduce.Mapper;
   import org.apache.hadoop.mapreduce.Reducer;
   import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
   import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
   import org.apache.hadoop.util.GenericOptionsParser;

   public class WordCount {

       public static class TokenizerMapper
           extends Mapper<Object, Text, Text, IntWritable>{

       private final static IntWritable one = new IntWritable(1);
       private Text word = new Text();

       public void map(Object key, Text value, Context context
                       ) throws IOException, InterruptedException {
           StringTokenizer itr = new StringTokenizer(value.toString());
           while (itr.hasMoreTokens()) {
           word.set(itr.nextToken());
           context.write(word, one);
           }
       }
   }

   public static class IntSumReducer
           extends Reducer<Text,IntWritable,Text,IntWritable> {
       private IntWritable result = new IntWritable();

       public void reduce(Text key, Iterable<IntWritable> values,
                           Context context
                           ) throws IOException, InterruptedException {
           int sum = 0;
           for (IntWritable val : values) {
           sum += val.get();
           }
          result.set(sum);
          context.write(key, result);
       }
   }

   public static void main(String[] args) throws Exception {
       Configuration conf = new Configuration();
       String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
       if (otherArgs.length != 2) {
           System.err.println("Usage: wordcount <in> <out>");
           System.exit(2);
       }
       Job job = new Job(conf, "word count");
       job.setJarByClass(WordCount.class);
       job.setMapperClass(TokenizerMapper.class);
       job.setCombinerClass(IntSumReducer.class);
       job.setReducerClass(IntSumReducer.class);
       job.setOutputKeyClass(Text.class);
       job.setOutputValueClass(IntWritable.class);
       FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
       FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
       System.exit(job.waitForCompletion(true) ? 0 : 1);
       }
   }
   ```
   
    請注意，封裝名稱是 `org.apache.hadoop.examples`，而類別名稱是 `WordCount`。 提交 MapReduce 作業時會用到這些名稱。

3. 儲存檔案。

## <a name="build-the-application"></a>建置應用程式

1. 切換至 `wordcountjava` 目錄 (如果您尚未在該目錄中)。

2. 使用下列命令來建置含有應用程式的 JAR 檔案：

   ```
   mvn clean package
   ```

    此命令會清除任何先前的組建成品、下載任何尚未安裝的相依性，然後建置並封裝應用程式。

3. 一旦命令完成之後，`wordcountjava/target` 目錄就會包含名為 `wordcountjava-1.0-SNAPSHOT.jar` 的檔案。
   
   > [!NOTE]
   > `wordcountjava-1.0-SNAPSHOT.jar` 檔案是一個 uberjar，其中不只含有 WordCount 作業，還有該作業在執行階段所需的相依性。

## <a name="a-iduploadaupload-the-jar"></a><a id="upload"></a>上傳 jar

使用以下命令將 jar 檔案上傳至 HDInsight 前端節點。

   ```bash
   scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

此命令會將檔案從本機系統複製到前端節點。

> [!NOTE]
> 如果您使用密碼來保護 SSH 帳戶，系統就會提示您輸入密碼。 如果您使用 SSH 金鑰，您可能必須使用 `-i` 參數和私密金鑰的路徑。 例如， `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`。


## <a name="a-namerunarun-the-mapreduce-job"></a><a name="run"></a>執行 MapReduce 工作

1. 如下列文章所述，使用 SSH 連接至 HDInsight：
   
   * [從 Linux、Unix、OS X 和 Bash on Windows 10 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [從 Windows 在 HDInsight 上搭配使用 SSH (PuTTY) 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 在 SSH 工作階段中，使用以下命令執行 MapReduce 應用程式：
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    此命令會啟動 WordCount MapReduce 應用程式。 輸入檔為 **/example/data/gutenberg/davinci.txt**，而輸出會儲存於 **/example/data/wordcountout**。 輸入檔和輸出都會儲存至叢集的預設儲存體中。

3. 作業完成之後，請使用以下命令來檢視結果：
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    您應該會收到一份單字和計數的清單，其中含有類似下列文字的值：
   
        zeal    1
        zelus   1
        zenith  2

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>接續步驟

在本文件中，您已學到如何開發 Java MapReduce 工作。 請參閱下列文件，了解其他的 HDInsight 使用方式。

* [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
* [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)

如需詳細資訊，也請參閱 [Java 開發人員中心](https://azure.microsoft.com/develop/java/)。

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx


