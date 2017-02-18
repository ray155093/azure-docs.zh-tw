---
title: "在 HDInsight 中建立 Windows 型 Hadoop 叢集 | Microsoft Docs"
description: "了解如何建立 Azure HDInsight 的叢集。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: a311f7e0-9333-4886-a726-def79e5db8cb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 6407c371bc51461a05429fabaf38d3f9bc80d32c
ms.openlocfilehash: f014b92516879a0255dda3736bf5af6142555d92


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight"></a>在 HDInsight 中建立 Windows 型 Hadoop 叢集

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Hadoop 叢集由數個虛擬機器 (節點) 組成，可用於分散處理叢集上的作業。 Azure 將個別節點安裝和設定的實作細節抽象化，因此您必須提供一般組態資訊。 您會在本文中學習這些組態設定。

> [!IMPORTANT]
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。 本文件的資訊是以 Windows 為基礎之 Azure HDInsight 叢集的特定資訊。 如需以 Linux 為基礎的叢集的詳細資訊，請參閱 [在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)。



## <a name="access-control-requirements"></a>存取控制需求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="cluster-types"></a>叢集類型
目前，HDInsight 提供四種不同類型的叢集，每種都有一組提供特定功能的元件。

| 叢集類型 | 功能 |
| --- | --- |
| Hadoop |查詢和分析 (批次工作) |
| HBase |NoSQL 資料儲存體 |
| Storm |即時事件處理 |
| Spark (預覽) |記憶體內處理、互動式查詢、微批次串流處理 |

每個叢集類型都有自己的節點數目、叢集內的節點術語，以及每個節點類型的預設 VM 大小。 下表中各節點類型的節點數目位於括號中。

| 類型 | 節點 (節點數目) | 圖表 |
| --- | --- | --- |
| Hadoop |前端節點 (2)、資料節點 (1+) |![HDInsight Hadoop 叢集節點](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png) |
| HBase |前端伺服器 (2)、區域伺服器 (1+)、主要/Zookeeper 節點 (3) |![HDInsight HBase 叢集節點](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png) |
| Storm |Nimbus 節點 (2)、監督員伺服器 (1+)、Zookeeper 節點 (3) |![HDInsight Storm 叢集節點](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png) |
| Spark |前端節點 (2)、背景工作角色節點 (1+)、Zookeeper 節點 (3) (A1 Zookeeper VM 大小不限) |![HDInsight Spark 叢集節點](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png) |

> [!IMPORTANT]
> 如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB RAM 的前端節點大小。
> 
> 

您可以使用 [指令碼動作](#customize-clusters-using-script-action)，將 Hue 或 R 等其他元件加入這些基本類型中。

> [!IMPORTANT]
> HDInsight 叢集有各種不同類型，這些類型各自對應到叢集微調時所針對的工作負載或技術。 沒有任何支援方法可建立結合多個類型的叢集，例如在一個叢集上並存 Storm 和 HBase。 
> 
> 

如果您的方案需要會分散到多個 HDInsight 叢集類型的技術，請建立 Azure 虛擬網路，然後在虛擬網路內建立必要的叢集類型。 這可讓叢集以及其中部署的任何程式碼直接彼此通訊。

如需如何搭配使用 Azure 虛擬網路與 HDInsight 的詳細資訊，請參閱[使用 Azure 虛擬網路擴充 HDInsight](hdinsight-extend-hadoop-virtual-network.md)。

如需在 Azure 虛擬網路內使用兩個叢集類型的範例，請參閱[使用 Storm 和 HBase 分析感應器資料](hdinsight-storm-sensor-data-analysis.md)。

## <a name="basic-configuration-options"></a>基本組態選項
下列是關於建立 HDInsight 叢集所需的基本組態選項。

### <a name="cluster-name"></a>叢集名稱
叢集名稱用來識別叢集。 叢集名稱必須是全域唯一的，而且必須遵循下列命名方針︰

* 欄位必須是包含 3 到 63 個字元的字串。
* 欄位只能包含字母、數字和連字號。

### <a name="cluster-type"></a>叢集類型
請參閱 [叢集類型](#cluster-types)。

### <a name="operating-system"></a>作業系統
HDInsight 叢集可以建立在下列兩個作業系統的其中之一上：

* Linux 上的 HDInsight。 HDInsight 提供在 Azure 上設定 Linux 叢集的選項。 如果您熟悉 Linux 或 Unix、要從現有的 Linux Hadoop 方案進行移轉，或想輕鬆整合針對 Linux 所建置的 Hadoop 生態系統元件，請設定 Linux 叢集。 如需詳細資訊，請參閱 [開始在 Linux 上的 HDInsight 中使用 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)。
* Windows 上的 HDInsight (Windows Server 2012 R2 Datacenter)。

### <a name="hdinsight-version"></a>HDInsight 版本
HDInsight 版本可用來決定要用於此叢集的 HDInsight 版本。 如需詳細資訊，請參閱 [HDInsight 中的 Hadoop 叢集版本和元件](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)。

### <a name="subscription-name"></a>訂用帳戶名稱
每個 HDInsight 叢集都會繫結至一個 Azure 訂用帳戶。

### <a name="resource-group-name"></a>資源群組名稱
使用 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)，您可以部署、更新、監視或刪除應用程式的所有資源。

### <a name="credentials"></a>認證
使用 HDInsight 叢集，您可以在建立叢集期間設定三個使用者帳戶：

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 可協助您將應用程式中的資源做為群組使用，稱為 Azure 資源群組。 您可以透過單一、協調的作業來部署、更新、監視或刪除應用程式的所有資源。
* HTTP 使用者。 預設使用者名稱是在 Azure 入口網站上基本組態中的 *admin* 。 有時稱預設為「叢集使用者」。
* RDP 使用者 (Windows 叢集)。 使用 RDP 連線到叢集。 當您建立帳戶時，必須將到期日設為從帳戶建立當天算起的 90 天內。
* SSH 使用者 (Linux 叢集)。 使用 SSH 連線到叢集。 叢集建立之後，您便可以依照 [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)中的步驟建立其他 SSH 使用者帳戶。

### <a name="data-source"></a>資料來源
原始的 Hadoop 分散式檔案系統 (HDFS) 會使用叢集上的多個本機磁碟。 HDInsight 會使用 Azure Blob 儲存體來儲存資料。 Azure Blob 儲存體是強大的一般用途儲存體解決方案，其完美整合了 HDInsight。 透過 HDFS 介面，HDInsight 中的完整元件集可直接處理 Blob 儲存體中的結構化或非結構化資料。 如果您將資料儲存在 Blob 儲存體中，您可以安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。

在設定期間，您必須指定 Azure 儲存體帳戶，並在該 Azure 儲存體帳戶中指定 Azure Blob 儲存體容器。 某些建立程序會要求事先建立 Azure 儲存體帳戶和 Blob 儲存體容器。 叢集會以該 Blob 儲存體容器做為預設儲存位置。 您也可以選擇指定叢集可存取的其他 Azure 儲存體帳戶 (連結的儲存體)。 叢集也可以存取任何設有完整公用讀取權限或僅限對 Blob 有公用讀取權的 Blob 儲存體容器。  如需詳細資訊，請參閱 [管理 Azure 儲存體資源的存取](../storage/storage-manage-access-to-resources.md)。

![HDInsight 儲存體](./media/hdinsight-provision-clusters/HDInsight.storage.png)

> [!NOTE]
> Blob 儲存體容器提供一組 Blob 群組，如下圖所示。
> 
> 

在設定期間，您必須指定 Azure 儲存體帳戶，並在該 Azure 儲存體帳戶中指定 Azure Blob 儲存體容器。 某些建立程序會要求事先建立 Azure 儲存體帳戶和 Blob 儲存體容器。 叢集會以該 Blob 儲存體容器做為預設儲存位置。 您也可以選擇指定叢集可存取的其他 Azure 儲存體帳戶 (連結的儲存體)。 叢集也可以存取任何設有完整公用讀取權限或僅限對 Blob 之公用讀取權的 Blob 容器。 如需詳細資訊，請參閱 [管理 Azure 儲存體資源的存取](../storage/storage-manage-access-to-resources.md)。

![Azure Blob 儲存體](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

不建議使用預設的 Blob 儲存體容器來儲存商務資料。 最好在每次使用後刪除預設的 Blob 儲存體容器，以減少儲存成本。 預設容器包含應用程式與系統記錄檔。 請務必先擷取記錄檔再刪除容器。

> [!WARNING]
> HDInsight 不支援多個叢集共用一個 Blob 儲存體容器。
> 
> 

如需有關次要 Blob 儲存體的詳細資訊，請參閱 [與 HDFS 相容的 Azure Blob 儲存體搭配 HDInsight 中的 Hadoop](hdinsight-hadoop-use-blob-storage.md)。

除了 Azure Blob 儲存體，您也可以使用 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) 當做 HDInsight 中 HBase 叢集的預設儲存體帳戶，以及全部&4; 種 HDInsight 叢集類型的連結儲存體。 如需詳細資訊，請參閱 [使用 Azure 入口網站建立具有 Data Lake Store 的 HDInsight 叢集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。

### <a name="location-region"></a>位置 (區域)
HDInsight 叢集與其預設儲存體帳戶必須並存於相同的 Azure 位置。

![Azure 區域](./media/hdinsight-provision-clusters/Azure.regions.png)

如需支援的區域清單，請按一下 [HDInsight 價格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)中的 [區域] 下拉式清單。

### <a name="node-pricing-tiers"></a>節點定價層
客戶需根據叢集的生命期，就這些節點的使用量支付費用。 建立叢集後就開始計費，並在叢集刪除後停止計費。 無法取消配置或保留叢集。

不同的叢集類型具有不同的節點類型、節點數目和節點大小。 例如，Hadoop 叢集類型有兩個*前端節點*和預設的四個*資料節點*，而 Storm 叢集類型有兩個 *Nimbus 節點*、三個 *Zookeeper 節點*和預設的四個*監督員節點*。 HDInsight 叢集的成本是由節點數和節點的虛擬機器大小來決定。 例如，如果您知道將會執行需要大量記憶體的作業，您可以選取具有較多記憶體的計算資源。 為了方便學習，建議使用&1; 個資料節點。 如需關於 HDInsight 定價的詳細資訊，請參閱 [HDInsight 定價](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)。

> [!NOTE]
> 叢集大小限制會隨著 Azure 訂用帳戶而有所不同。 若要提高限制，請與帳務支援人員連絡。
> 
> 叢集所使用的節點不會算做虛擬機器，因為節點所使用的虛擬機器映像是 HDInsight 服務的實作詳細資料。 然而，節點所使用的計算核心則會算入訂用帳戶可用的計算核心總數。 您可以在建立 HDInsight 叢集時，於 [節點定價層] 刀鋒視窗的摘要區段中查看可用的核心及叢集將會使用的核心數目。
> 
> 

使用 Azure 入口網站設定叢集時，可透過 [節點定價層]  刀鋒視窗得知節點大小。 您也可以查看與不同節點大小相關聯的成本。 下列螢幕擷取畫面顯示 Linux 型 Hadoop 叢集的選項。

![HDinsight VM 節點大小](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

下表顯示 HDInsight 叢集支援的大小及提供的容量。

### <a name="standard-tier-a-series"></a>標準層級：A 系列
在傳統部署模型中，部分 VM 大小會與 PowerShell 和 CLI 中的稍有不同。

* Standard_A3 是「大型」
* Standard_A4 是「特大型」

| 大小 | CPU 核心 | 記憶體 | NIC (最大) | 最大 磁碟大小 | 最大 資料磁碟 (每個&1023; GB) | 最大 IOPS (每個磁碟&500;) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A3\大型 |4 |7 GB |2 |暫存 = 285 GB |8 |8x500 |
| Standard_A4\特大型 |8 |14 GB |4 |暫存 = 605 GB |16 |16x500 |
| Standard_A6 |4 |28 GB |2 |暫存 = 285 GB |8 |8x500 |
| Standard_A7 |8 |56 GB |4 |暫存 = 605 GB |16 |16x500 |

### <a name="standard-tier-d-series"></a>標準層級：D 系列
| 大小 | CPU 核心 | 記憶體 | NIC (最大) | 最大 磁碟大小 | 最大 資料磁碟 (每個&1023; GB) | 最大 IOPS (每個磁碟&500;) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3 |4 |14 GB |4 |暫存 (SSD) =&200; GB |8 |8x500 |
| 標準_D4 |8 |28 GB |8 |暫存 (SSD) =&400; GB |16 |16x500 |
| 標準_D12 |4 |28 GB |4 |暫存 (SSD) =&200; GB |8 |8x500 |
| 標準_D13 |8 |56 GB |8 |暫存 (SSD) =&400; GB |16 |16x500 |
| Standard_D14 |16 |112 GB |8 |暫存 (SSD) =&800; GB |32 |32x500 |

### <a name="standard-tier-dv2-series"></a>標準層級：Dv2 系列
| 大小 | CPU 核心 | 記憶體 | NIC (最大) | 最大 磁碟大小 | 最大 資料磁碟 (每個&1023; GB) | 最大 IOPS (每個磁碟&500;) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 GB |4 |暫存 (SSD) =&200; GB |8 |8x500 |
| Standard_D4_v2 |8 |28 GB |8 |暫存 (SSD) =&400; GB |16 |16x500 |
| Standard_D12_v2 |4 |28 GB |4 |暫存 (SSD) =&200; GB |8 |8x500 |
| Standard_D13_v2 |8 |56 GB |8 |暫存 (SSD) =&400; GB |16 |16x500 |
| Standard_D14_v2 |16 |112 GB |8 |暫存 (SSD) =&800; GB |32 |32x500 |

如需規劃使用這些資源時需注意的部署考量，請參閱 [虛擬機器的大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 如需各式大小的價格資訊，請參閱 [HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight)。   

> [!IMPORTANT]
> 如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則必須選取具有至少 8 個核心和 14 GB RAM 的前端節點大小。
> 
> 

建立叢集後就開始計費，並在叢集刪除後停止計費。 如需價格的詳細資訊，請參閱 [HDInsight 價格詳細資料](https://azure.microsoft.com/pricing/details/hdinsight/)。

| 大小 | CPU 核心 | 記憶體 | NIC (最大) | 最大 磁碟大小 | 最大 資料磁碟 (每個&1023; GB) | 最大 IOPS (每個磁碟&500;) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 GB |4 |暫存 (SSD) =&200; GB |8 |8x500 |
| Standard_D4_v2 |8 |28 GB |8 |暫存 (SSD) =&400; GB |16 |16x500 |
| Standard_D12_v2 |4 |28 GB |4 |暫存 (SSD) =&200; GB |8 |8x500 |
| Standard_D13_v2 |8 |56 GB |8 |暫存 (SSD) =&400; GB |16 |16x500 |
| Standard_D14_v2 |16 |112 GB |8 |暫存 (SSD) =&800; GB |32 |32x500 |

如需規劃使用這些資源時需注意的部署考量，請參閱 [虛擬機器的大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 如需各式大小的價格資訊，請參閱 [HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight)。  

> [!IMPORTANT]
> 如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB RAM 的前端節點大小。
> 
> 

 建立叢集後就開始計費，並在叢集刪除後停止計費。 如需價格的詳細資訊，請參閱 [HDInsight 價格詳細資料](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="add-more-storage"></a>加入更多儲存體。
在某些情況下，您可能想在叢集中加入更多儲存體。 例如，您可能有多個 Azure 儲存體帳戶用於不同的地理區域或不同的服務，但您想要全部透過 HDInsight 來分析。

如需有關次要 Blob 儲存體的詳細資訊，請參閱 [使用與 HDFS 相容的 Azure Blob 儲存體搭配 HDInsight 中的 Hadoop](hdinsight-hadoop-use-blob-storage.md)。 如需次要 Data Lake Store 的詳細資訊，請參閱 [使用 Azure 入口網站建立具有 Data Lake Store 的 HDInsight 叢集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。

## <a name="use-a-hiveoozie-metastore"></a>使用 Hive/Oozie 中繼存放區
如果想要在刪除 HDInsight 叢集之後保留 Hive 資料表，以便日後將中繼存放區連接至另一個 HDInsight 叢集，強烈建議您使用自訂的中繼存放區。

> [!IMPORTANT]
> HDInsight 中繼存放區不具有回溯相容性。 例如，您無法使用 HDInsight 3.3 叢集的中繼存放區建立 HDInsight 3.2 叢集。
> 
> 

中繼存放區包含 Hive 和 Oozie 中繼資料，例如 Hive 資料表、資料分割、結構描述和資料行。 中繼存放區可協助您保留 Hive 和 Oozie 中繼資料。 在建立新叢集時，您無需重建 Hive 資料表或 Oozie 工作。 根據預設，Hive 會使用內嵌的 Azure SQL 資料庫來儲存此資訊。 刪除叢集時，嵌入的資料庫將無法保留中繼資料。 例如，如果您在具有 Hive 中繼存放區的叢集中建立 Hive 資料表，若您刪除並重新建立具有相同 Hive 中繼存放區的叢集，仍可以查看這些資料表。

HBase 叢集類型無法使用中繼存放區組態。

> [!IMPORTANT]
> 建立自訂中繼存放區時，請勿使用包含破折號或連字號的資料庫名稱，因為這會導致叢集建立程序失敗。
> 
> 

## <a name="use-azure-virtual-network"></a>使用 Azure 虛擬網路
[Azure 虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/) 可幫助您建立安全、持續的網路，上面有您的解決方案所需的資源。 使用虛擬網路，您可以：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。
  
    ![diagram of cloud-only configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)
* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。

| 站對站組態 | 點對站組態 |
| --- | --- |
| 在站對站組態中，您可以使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接到 Azure 虛擬網路。<br />![diagram of site-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) |在點對站組態中，您可以使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。<br />![diagram of point-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

以 Windows 為基礎的叢集需要傳統虛擬網路，而以 Linux 為基礎的叢集需要 Azure Resource Manager 虛擬網路。 如果您沒有正確的網路類型，當您建立叢集時就無法使用。

如需搭配虛擬網路使用 HDInsight 的詳細資訊，包括虛擬網路的特定組態需求，請參閱 [使用 Azure 虛擬網路擴充 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。

## <a name="customize-clusters-by-using-hdinsight-cluster-customization-bootstrap"></a>使用 HDInsight 叢集自訂功能 (Bootstrap) 來自訂叢集
有時候，您可能要設定下列組態檔：

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

若要在叢集存留期間保留變更，您可以在建立程序期間使用 HDInsight 叢集自訂。 您也可以在 linux 型叢集中使用 Ambari。 如需詳細資訊，請參閱 [使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md)。

> [!NOTE]
> Windows 型叢集無法保留重新安裝映像所造成的變更。 如需詳細資訊，請參閱 [角色執行個體由於作業系統升級而重新啟動](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)(英文)。 若要在叢集的存留期間保留變更，您必須在建立程序期間使用 HDInsight 叢集自訂。
> 
> 

## <a name="customize-clusters-by-using-script-action"></a>使用指令碼動作自訂叢集
您可以在建立期間使用指令碼來安裝其他元件或自訂組態。 這類指令碼可透過 **指令碼動作**叫用，指令碼動作是一個組態選項，其可從 Azure 入口網站、HDInsight Windows PowerShell Cmdlet 或 HDInsight .NET SDK 使用。 如需詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。

## <a name="cluster-creation-methods"></a>叢集建立方法
在本文中，您已了解建立 Windows 型 HDInsight 叢集的基本資訊。 請利用下表，尋找如何使用最符合需求的方法建立叢集的具體資訊。

| 叢集建立方法 | Web 瀏覽器 | 命令列 | REST API | SDK | Linux、Mac OS X 或 Unix | Windows |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| [Azure 入口網站](hdinsight-hadoop-create-windows-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-windows-clusters-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |✔ |✔ |
| [.NET SDK](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |✔ |
| [Azure 資源管理員範本](hdinsight-hadoop-create-windows-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |




<!--HONumber=Feb17_HO1-->


