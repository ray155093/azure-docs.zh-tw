<properties
   	pageTitle="在 HDInsight 上安裝 Hadoop 應用程式 | Microsoft Azure"
   	description="了解如何在 HDInsight 應用程式上安裝 HDInsight 應用程式。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="05/18/2016"
   	ms.author="jgao"/>

# 安裝自訂 HDInsight 應用程式

HDInsight 應用程式是使用者可以在以 Linux 為基礎的 HDInsight 叢集上安裝的應用程式。Microsoft 獨立軟體廠商 (ISV) 或您可以自己開發這些應用程式。在本文中，您將學習如何在 HDInsight 上安裝尚未發佈至 Azure 入口網站的 HDInsight 應用程式。您將要安裝的應用程式是 [Hue](http://gethue.com/)。

如需其他相關文章，請參閱：

- [發佈 HDInsight 應用程式](hdinsight-apps-publish-applications.md)︰了解如何將自訂 HDInsight 應用程式發佈至 Azure Marketplace。
- [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)︰了解如何定義 HDInsight 應用程式。

 
## 必要條件

如果您想要在現有的 HDInsight 叢集上安裝 HDInsight 應用程式，您必須有 HDInsight 叢集。若要建立叢集，請參閱[建立叢集](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)。您也可以在建立 HDInsight 叢集時安裝 HDInsight 應用程式。


## 安裝 HDInsight 應用程式

HDInsight 應用程式可以在您建立叢集時安裝，或安裝至現有的 HDInsight 叢集。如需定義 ARM 範本，請參閱 [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)。

部署此應用程式 (Hue) 所需的檔案︰

- [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json)：可供安裝 HDInsight 應用程式的 ARM 範本。如需開發您自己的 ARM 範本，請參閱 [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)。
- [hue-install\_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh)：ARM 範本為了設定邊緣節點所呼叫的指令碼動作。 
- [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz)︰從 hui-install\_v0.sh 呼叫的 Hue 二進位檔。 
- [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz)︰從 hui-install\_v0.sh 呼叫的 Hue 二進位檔。 
- [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz)：從 hui-install\_v0.sh 呼叫的範例 Web 應用程式 (Tomcat)。

**將 Hue 安裝至現有的 HDInsight 叢集**

1. 按一下以下影像，在 Azure 入口網站中登入 Azure 並開啟 ARM 範本。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    此按鈕會在 Azure 入口網站上開啟 ARM 範本。ARM 範本位於 [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)。若要了解如何撰寫此 ARM 範本，請參閱 [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)。
    
2. 從 [參數] 刀鋒視窗，輸入下列資料：

    - **ClusterName**：輸入您要安裝應用程式的叢集名稱。此叢集必須是現有的叢集。
    
3. 按一下 [確定] 儲存參數。
4. 從 [自訂部署] 刀鋒視窗，輸入 [資源群組]。資源群組是聚集叢集、相依儲存體帳戶和其他資源的容器。必須使用與叢集相同的資源群組。
5. 按一下 [法律條款]，然後按一下 [建立]。
6. 確認已選取 [釘選到儀表板] 核取方塊，然後按一下 [建立]。您可以從釘選到入口網站儀表板和入口網站通知的圖格查看安裝狀態 (按一下入口網站頂端的鈴鐺圖示)。安裝此應用程式需要 10 分鐘左右。

**在建立叢集時安裝 Hue**

1. 按一下以下影像，在 Azure 入口網站中登入 Azure 並開啟 ARM 範本。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    此按鈕會在 Azure 入口網站上開啟 ARM 範本。ARM 範本位於 [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)。若要了解如何撰寫此 ARM 範本，請參閱 [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)。

2. 請依照指示來建立叢集和安裝 Hue。如需建立 HDInsight 叢集的詳細資訊，請參閱[在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

## 驗證安裝

您可以在 Azure 入口網站上檢查應用程式狀態以驗證應用程式安裝。此外，您也可以驗證所有如預期般出現的 HTTP 端點及網頁 (如果有的話)︰

**開啟 Hue 入口網站**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左側功能表中的 [HDInsight 叢集]。如果沒有看到該功能表，請按一下 [瀏覽]，然後按一下 [HDInsight 叢集]。
3. 按一下您已安裝應用程式的叢集。
4. 在 [設定] 刀鋒視窗中，按一下 [一般] 類別之下的 [應用程式]。您應該會看到 **Hue** 列在 [已安裝的應用程式] 刀鋒視窗中。
5. 按一下清單中的 **Hue** 以列出相關屬性。  
6. 按一下 [網頁] 連結來驗證網站；在瀏覽器中開啟 HTTP 端點以驗證 Hue Web UI，使用 [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) 或其他 [SSH 用戶端](hdinsight-hadoop-linux-use-ssh-unix.md)開啟 SSH 端點。
 
## 安裝疑難排解

請參閱「安裝 HDInsight 應用程式」中的[疑難排解](hdinsight-apps-install-applications.md#troubleshoot)一節。


## 後續步驟

- [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)︰了解如何開發 ARM 範本以供部署 HDInsight 應用程式。
- [發佈 HDInsight 應用程式](hdinsight-apps-publish-applications.md)︰了解如何將自訂 HDInsight 應用程式發佈至 Azure Marketplace。
- [使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用指令碼動作來安裝其他應用程式。
- [使用 ARM 範本在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)︰了解如何呼叫 ARM 範本來建立 HDInsight 叢集。

<!---HONumber=AcomDC_0518_2016-->