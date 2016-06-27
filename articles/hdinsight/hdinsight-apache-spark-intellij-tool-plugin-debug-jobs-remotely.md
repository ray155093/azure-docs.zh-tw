 <properties
	pageTitle="使用 IntelliJ IDEA 的 HDInsight 外掛程式在遠端偵錯 HDInsight Spark 叢集上執行的應用程式| Microsoft Azure"
	description="了解如何使用 IntelliJ IDEA 的 HDInsight 外掛程式在遠端偵錯 HDInsight Spark 叢集上執行的應用程式。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2016"
	ms.author="nitinme"/>


# 使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式在遠端偵錯 HDInsight Spark Linux 叢集的 Spark 應用程式

本文提供有關如何使用 IntelliJ IDEA 的 HDInsight 工具外掛程式，來提交 HDInsight Spark 叢集上的 Spark 作業，然後從桌上型電腦遠端偵錯的逐步指引。若要這樣做，您必須執行下列高階步驟：

1. 建立站對站或點對站 Azure 虛擬網路。本文件中的步驟假設您使用站對站網路。

2. 在 Azure HDInsight 中建立 Spark 叢集是站對站 Azure 虛擬網路的一部分。

3. 請確認叢集前端節點與您的桌上型電腦之間的連線。

4. 在 IntelliJ IDEA 中建立 Scala 應用程式，並設定它以進行遠端偵錯。

5. 執行和偵錯應用程式。

##必要條件

* Azure 訂閱。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* HDInsight Linux 上的 Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。
 
* Oracle Java Development Kit。您可以從[這裡](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)加以安裝。
 
* IntelliJ IDEA。本文章使用 15.0.1 版。您可以從[這裡](https://www.jetbrains.com/idea/download/)加以安裝。
 
* IntelliJ IDEA 的 HDInsight Tools 外掛程式。如需安裝外掛程式的指示，請參閱[安裝 IntelliJ IDEA 的 HDInsight Tools 外掛程式](hdinsight-apache-spark-intellij-tool-plugin.md#install-hdinsight-tools-plugin-for-intellij-idea)。
 
* 在 Windows 電腦上執行 Spark Scala 應用程式以進行遠端偵錯時，可能會發生 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的例外狀況，此例外狀況發生的原因是因為 Windows 上缺少 WinUtils.exe。若要解決這個錯誤，您必須[從這裡下載可執行檔](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)並將其放至 **C:\\WinUtils\\bin** 之類的位置。然後，您必須新增環境變數 **HADOOP\_HOME**，並將變數的值設為 **C\\WinUtils**。

## 步驟 1：建立 Azure 虛擬網路

請依照下列連結的指示建立 Azure 虛擬網路，然後確認桌上型電腦和 Azure 虛擬網路之間的連線。

* [使用 Azure 入口網站建立具有站對站 VPN 連線的 VNet](..\vpn-gateway\vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [使用 PowerShell 建立具有站對站 VPN 連線的 VNet](..\vpn-gateway\vpn-gateway-create-site-to-site-rm-powershell.md)
* [使用 PowerShell 設定虛擬網路的點對站連線](..\vpn-gateway\vpn-gateway-howto-point-to-site-rm-ps.md)

## 步驟 2：建立 HDInsight Spark 叢集

您也應該在 Azure HDInsight 上建立 Apache Spark 叢集，這是您建立的 Azure 虛擬網路的一部分。使用可於[在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-provision-linux-clusters.md)取得的資訊。做為選擇性組態的一部分，選取您在上一個步驟中建立的 Azure 虛擬網路。

## 步驟 3：請確認叢集前端節點與您的桌上型電腦之間的連線

1. 取得前端節點的 IP 位址。開啟叢集的 Ambari UI。從叢集刀鋒視窗中，按一下 [儀表板]。

	![尋找前端節點 IP](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)

2. 從 Ambari UI 的右上角，按一下 [主機]。

	![尋找前端節點 IP](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)

3. 您應該會看到前端節點、背景工作角色節點和 zookeeper 節點的清單。前端節點有 **hn*** 前置詞。按一下第一個前端節點。

	![尋找前端節點 IP](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)

4. 在開啟的頁面底部，從 [摘要] 方塊複製前端節點的 IP 位址和主機名稱。

	![尋找前端節點 IP](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)

5. 包含前端節點的 IP 位址和主機名稱，到您想要從中執行和遠端偵錯 Spark 作業的電腦上的**主機**檔案。這可讓您使用 IP 位址和主機名稱，與前端節點通訊。

	1. 以提高的權限開啟 [記事本]。從 [檔案] 功能表中，按一下 [開啟] 然後瀏覽至主機檔案的位置。在 Windows 電腦上，是 `C:\Windows\System32\Drivers\etc\hosts`。

	2. 將下列項目新增至**主機**檔案。

			# For headnode0
			192.xxx.xx.xx hn0-nitinp
			192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

			# For headnode1
			192.xxx.xx.xx hn1-nitinp
			192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net


5. 從您連接到 HDInsight 叢集使用之 Azure 虛擬網路的電腦，確認您可以使用 IP 位址和主機名稱 ping 兩個前端節點。

6. 利用[使用 SSH 連線到 HDInsight 叢集](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)中的指示，將 SSH 連線至叢集前端節點。從叢集前端節點，ping 桌上型電腦的 IP 位址。您應該測試指派至電腦的這兩個 IP 位址的連線能力，一個用於網路連接，而另一個則用於電腦連接的 Azure 虛擬網路。

7. 對其他前端節點重複這些步驟。

## 步驟 4：使用 HDInsight IntelliJ IDEA 中外掛程式建立 Spark Scala 應用程式，並設定它以進行遠端偵錯

1. 啟動 IntelliJ IDEA，並建立新的專案。在新增專案對話方塊中選取下列選項，然後按 [下一步]。

	![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* 從左窗格中，選取 [HDInsight]。
	* 從右窗格中，選取 [HDInsight 上的 Spark (Scala)]。
	* 按 [下一步]。

2. 在下一個視窗中，提供專案詳細資料。

	* 提供專案名稱和專案位置。
	* 對於 [專案 SDK]，請確定您提供的 Java 版本大於 7。
	* 對於 [Scala SDK]，請依序按一下 [建立]、[下載]，然後選取要使用的 Scala 版本。**確定您未使用 2.11.x 版**。此範例使用 **2.10.6** 版。

		![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-version.png)

	* 對於 [Spark SDK]，請從[這裡](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)下載並使用 SDK。您也可以略過此項，並改用 [Spark Maven Repository](http://mvnrepository.com/search?q=spark)，不過請確定您已安裝正確的 Maven 儲存機制，以便開發 Spark 應用程式。(例如，您需要先確定您有安裝 Spark Streaming 組件，如果您使用 Spark Streaming 的話；也請確定您使用標示為 Scala 2.10 的儲存機制 - 請勿使用標示為 Scala 2.11 的儲存機制)。

		![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)

	* 按一下 [完成]。

3. Spark 專案會自動為您建立構件。若要查看構件，請遵循下列步驟。

	1. 在 [檔案] 功能表中，按一下 [專案結構]。
	2. 在 [專案結構] 對話方塊中，按一下 [構件]，以查看建立的預設構件。

		![建立 JAR](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)

	您也可以建立自己的構件，方法是按一下 [+] 圖示，在上方映像中反白顯示。

4. 在 [Project Structure (專案結構)] 對話方塊中，按一下 [Project (專案)]。如果 [Project SDK (專案 SDK)] 設定為 1.8，請確定 [Project language level (專案語言層級)]設為 [7 - Diamonds, ARM, multi-catch, etc (7 - Diamonds、ARM、Multi-Catch 等)]。

	![設定專案語言層級](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/set-project-language-level.png)

4. 將程式庫新增至專案。若要新增程式庫，以滑鼠右鍵按一下專案樹狀結構中的專案名稱，然後按一下 [Open Module Settings (開啟模組設定)]。在 [Project Structure (專案結構)] 對話方塊中，從左窗格中按一下 [Libraries (程式庫)]，按一下 (+) 符號，然後按一下 [From Maven (從 Maven)]。

	![新增程式庫](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

	在 [Download Library from Maven Repository (從 Maven 儲存機制下載程式庫)] 對話方塊中，搜尋並新增下列程式庫。

	* `org.scalatest:scalatest_2.10:2.2.1`
	* `org.apache.hadoop:hadoop-azure:2.7.1`

5. 從叢集前端節點複製 `yarn-site.xml` 和 `core-site.xml`，並將它新增至專案。使用下列命令來複製檔案。您可以使用 [Cygwin](https://cygwin.com/install.html) 以執行下列 `scp` 命令，從叢集前端節點複製檔案。

		scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

	因為我們已經將叢集前端節點 IP 位址及主機名稱新增至桌上型電腦的主機檔案，因此可以下列方式使用 **scp** 命令。

		scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
		scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

	在您專案樹狀結構的 **/src** 資料夾下複製這些檔案，以將它們新增至專案，例如 `<your project directory>\src`。

6. 更新 `core-site.xml` 以進行下列變更。

	1. `core-site.xml` 包含與叢集相關聯的儲存體帳戶的加密金鑰。在您新增至專案的 `core-site.xml` 中，以與預設儲存體帳戶相關聯的實際儲存體金鑰取代加密金鑰。請參閱[管理儲存體存取金鑰](..\storage\storage-create-storage-account.md#manage-your-storage-account)。

			<property>
	      		<name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
	      		<value>access-key-associated-with-the-account</value>
	    	</property>

	2. 從 `core-site.xml` 移除下列項目。

			<property>
      			<name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
      			<value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
			</property>

			<property>
		      	<name>fs.azure.shellkeyprovider.script</name>
		      	<value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
		    </property>

			<property>
      			<name>net.topology.script.file.name</name>
      			<value>/etc/hadoop/conf/topology_script.py</value>
    		</property>

	3. 儲存檔案。

7. 新增您應用程式的主要類別。從 [Project Explorer (專案總管)] 中，以滑鼠右鍵按一下 [src]、指向 [New (新增)]，然後按一下 [Scala class (Scala 類別)]。

	![新增原始程式碼](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

8. 在 [Create New Scala Class (建立新的 Scala 類別)] 對話方塊中，提供一個名稱，並針對 [Kind (種類)] 選取 [Object (物件)]，然後按一下 [確定]。

	![新增原始程式碼](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

9. 在 `MyClusterAppMain.scala` 檔案中，貼上下列程式碼。此程式碼會建立 Spark 內容，並且從 `SparkSample` 物件啟動 `executeJob` 方法。


		import org.apache.spark.{SparkConf, SparkContext}

		object SparkSampleMain {
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("SparkSample")
		                              .set("spark.hadoop.validateOutputSpecs", "false")
		    val sc = new SparkContext(conf)
		
		    SparkSample.executeJob(sc,
		                           "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
		                           "wasb:///HVACOut")
		  }
		}

10. 重複上述的步驟 8 和 9，以新增稱為 `SparkSample` 的新 Scala 物件。對這個類別新增下列程式碼。此程式碼會從 HVAC.csv (所有 HDInsight Spark 叢集上均有提供) 讀取資料、擷取在 CSV 的第七個資料行中只有個位數的資料列，並將輸出寫入到叢集預設儲存體容器下的 **/HVACOut**。

		import org.apache.spark.SparkContext
	
		object SparkSample {
		  def executeJob (sc: SparkContext, input: String, output: String): Unit = {
		    val rdd = sc.textFile(input)
		
		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
		
		    val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
		    println(s)
		
		    rdd1.saveAsTextFile(output)
		    //rdd1.collect().foreach(println)
		  }
		
		}

11. 重複上述的步驟 8 和 9，以新增稱為 `RemoteClusterDebugging` 的新類別。這個類別會實作 Spark 測試架構，用於偵錯應用程式。將下列程式碼新增至 `RemoteClusterDebugging` 類別。

		import org.apache.spark.{SparkConf, SparkContext}
		import org.scalatest.FunSuite
		
		class RemoteClusterDebugging extends FunSuite {
		
		  test("Remote run") {
		    val conf = new SparkConf().setAppName("SparkSample")
		                              .setMaster("yarn-client")
		                              .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
		                              .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
		                              .setJars(Seq("""C:\WORK\IntelliJApps\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
		                              .set("spark.hadoop.validateOutputSpecs", "false")
		    val sc = new SparkContext(conf)
		
		    SparkSample.executeJob(sc,
		      "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
		      "wasb:///HVACOut")
		  }
		}

	這裡有幾個重點值得注意：
	
	* 對於 `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`，請確定 Spark 組件 JAR 可用於指定路徑的叢集存放區。
	* 對於 `setJars`，指定將會建立構件 jar 的位置。通常是 `<Your IntelliJ project directory>\out<project name>_DefaultArtifact\default_artifact.jar`。 


11. 在 `RemoteClusterDebugging` 類別中，以滑鼠右鍵按一下 `test` 關鍵字，然後選取 [Create RemoteClusterDebugging Configuration (建立 RemoteClusterDebugging 組態)]。

	![建立遠端組態](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

12. 在對話方塊中，提供組態的名稱，然後選取 [Test kind (測試種類)] 做為 [Test name (測試名稱)]。對於所有其他值保留預設值，按一下 [Apply (套用)]，然後按一下 [確定]。

	![建立遠端組態](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)



13. 您現在應該會在功能表列中看到 [Remote Run (遠端執行)] 組態下拉式清單。

	![建立遠端組態](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## 步驟 5：在偵錯模式中執行應用程式

1. 在您的 IntelliJ IDEA 專案中，開啟 `SparkSample.scala` 並且在 'val rdd1' 旁邊建立中斷點。在建立中斷點的快顯功能表中，選取 **executeJob 函數中的一行**。

	![新增中斷點](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)

2. 按一下 [Remote Run (遠端執行)] 組態下拉式清單旁的 [Debug Run (偵錯執行)] 按鈕，開始執行應用程式。

	![在偵錯模式中執行程式](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)

3. 當程式執行觸達中斷點時，您應該會在下方窗格中看到 [Debugger (偵錯工具)] 索引標籤。

	![在偵錯模式中執行程式](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)

4. 按一下 (**+**) 圖示，以新增監看式，如下圖所示。

	![在偵錯模式中執行程式](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

	這裡因為應用程式在建立變數 `rdd1` 之前中斷，我們可以使用此監看式看到變數 `rdd` 中的前 5 個資料列。按 **ENTER** 鍵。

	![在偵錯模式中執行程式](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

	您在上述映像中看到的內容是在執行階段，您可以查詢 terrabytes 的資料，並且針對應用程式的進度進行偵錯。例如，您可以在上述映像中顯示的輸出，看見輸出的第一個資料列是標頭。有鑑於此，您可以修改應用程式程式碼，視需要略過標頭資料列。

5. 您現在可以按一下 [Resume Program (繼續程式)] 圖示，以繼續執行應用程式。

	![在偵錯模式中執行程式](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)

6. 如果應用程式順利完成，應該會看到類似下列的輸出。

	![在偵錯模式中執行程式](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

 

## <a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### 案例

* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)

* [使用 HDInsight 中的 Spark 進行網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 建立及執行應用程式

* [使用 Scala 建立獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)

* [利用 Livy 在 Spark 叢集上遠端執行作業](hdinsight-apache-spark-livy-rest-interface.md)

### 工具和擴充功能

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [搭配 Jupyter Notebook 使用外部套件](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)

* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0615_2016-->