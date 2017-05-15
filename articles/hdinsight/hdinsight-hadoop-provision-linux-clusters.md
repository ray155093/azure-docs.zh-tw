---
title: "在 Azure HDInsight 中建立 Hadoop、HBase、Kafka、Storm 或 Spark 叢集 | Microsoft Docs"
description: "了解如何在 Linux 上使用瀏覽器、Azure CLI、Azure PowerShell、REST 或 SDK，為 HDInsight 建立 Hadoop、HBase、Storm 或 Spark 叢集。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 23a01938-3fe5-4e2e-8e8b-3368e1bbe2ca
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 9fc96db2b832f1e57813bebd2d46e4b78ed04677
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017


---
# <a name="create-hadoop-clusters-in-hdinsight"></a>在 HDInsight 中建立 Hadoop 叢集
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Hadoop 叢集由數個虛擬機器 (節點) 組成，可用於分散處理叢集上的作業。 Azure HDInsight 會將個別節點所安裝和設定的實作細節抽象化，您只需要提供一般設定資訊即可。 您會在本文中了解這些組態設定。

## <a name="basic-configurations"></a>基本組態

您可以使用 [快速建立] 或 [自訂]，從 [Azure 入口網站](https://portal.azure.com)建立 HDInsight 叢集。 本章節涵蓋 [快速建立] 選項中的基本組態設定。 [自訂] 選項包括下列其他組態︰

- [應用程式](#hdinsight-applications)
- [叢集大小](#cluster-size)
- 進階設定

  - [指令碼動作](#customize-clusters-using-script-action)
  - [虛擬網路](#use-virtual-network)

### <a name="subscription"></a>訂用帳戶 
每個 HDInsight 叢集都會繫結至一個 Azure 訂用帳戶。

### <a name="resource-group-name"></a>資源群組名稱
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 可協助您將應用程式中的資源做為群組使用，稱為 Azure 資源群組。 您可以在單一、協調的作業中，將應用程式的所有資源進行部署、更新、監視或刪除。

### <a name="cluster-name"></a>叢集名稱
叢集名稱用來識別叢集。 叢集名稱必須是全域唯一的，而且必須遵循下列命名方針︰

* 欄位必須是包含 3 到 63 個字元的字串。
* 欄位只能包含字母、數字和連字號。

### <a name="cluster-types"></a> 叢集類型
Azure HDInsight 目前提供下列的叢集類型，每種都有一組提供特定功能的元件：

| 叢集類型 | 功能 |
| --- | --- |
| [Hadoop](hdinsight-hadoop-introduction.md) |查詢和分析 (批次工作) |
| [HBase](hdinsight-hbase-overview.md) |NoSQL 資料儲存體 |
| [Storm](hdinsight-storm-overview.md) |即時事件處理 |
| [Spark](hdinsight-apache-spark-overview.md) |記憶體內處理、互動式查詢、微批次串流處理 |
| [Kafka (預覽)](hdinsight-apache-kafka-introduction.md) | 可用來建置即時串流資料管線和應用程式的分散式串流平台 |
| [R 伺服器](hdinsight-hadoop-r-server-overview.md) |各種不同的巨量資料統計資料、預測模型和機器學習功能 |
| [互動式 Hive (預覽)](hdinsight-hadoop-use-interactive-hive.md) |更快速之互動式 Hive 查詢的記憶體內快取 |

每個叢集類型都有自己叢集內的節點數目、叢集內的節點術語，以及每個節點類型的預設 VM 大小。 下表中各節點類型的節點數目位於括號中。

| 類型 | 節點 | 圖表 |
| --- | --- | --- |
| Hadoop |前端節點 (2)、資料節點 (1+) |![HDInsight Hadoop 叢集節點](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Hadoop-roles.png) |
| HBase |前端伺服器 (2)、區域伺服器 (1+)、主要/Zookeeper 節點 (3) |![HDInsight HBase 叢集節點](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-HBase-roles.png) |
| Storm |Nimbus 節點 (2)、監督員伺服器 (1+)、Zookeeper 節點 (3) |![HDInsight Storm 叢集節點](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Storm-roles.png) |
| Spark |前端節點 (2)、背景工作角色節點 (1+)、Zookeeper 節點 (3) (A1 Zookeeper VM 大小不限) |![HDInsight Spark 叢集節點](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Spark-roles.png) |

下表列出 HDInsight 的預設 VM 大小：

* 所有支援的區域，巴西南部和日本西部除外︰

  | 叢集類型 | Hadoop | HBase | Storm | Spark | R 伺服器 |
  | --- | --- | --- | --- | --- | --- |
  | 前端：預設 VM 大小 |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | 前端：建議的 VM 大小 |D3 v2、D4 v2、D12 v2 |D3 v2、D4 v2、D12 v2 |A3、A4、A5 |D12 v2、D13 v2、D14 v2 |D12 v2、D13 v2、D14 v2 |
  | 背景工作：預設 VM 大小 |D3 v2 |D3 v2 |D3 v2 |Windows：D12 v2；Linux：D4 v2 |Windows：D12 v2；Linux：D4 v2 |
  | 背景工作：建議的 VM 大小 |D3 v2、D4 v2、D12 v2 |D3 v2、D4 v2、D12 v2 |D3 v2、D4 v2、D12 v2 |Windows：D12 v2、D13 v2、D14 v2；Linux：D4 v2、D12 v2、D13 v2、D14 v2 |Windows：D12 v2、D13 v2、D14 v2；Linux：D4 v2、D12 v2、D13 v2、D14 v2 |
  | ZooKeeper：預設 VM 大小 | |A3 |A2 | | |
  | ZooKeeper：建議的 VM 大小 | |A3、A4、A5 |A2、A3、A4 | | |
  | 邊緣：預設 VM 大小 | | | | |Windows：D12 v2；Linux：D4 v2 |
  | 邊緣：建議的 VM 大小 | | | | |Windows：D12 v2、D13 v2、D14 v2；Linux：D4 v2、D12 v2、D13 v2、D14 v2 |
* 僅限巴西南部和日本西部 (此處沒有 v2 大小)︰

  | 叢集類型 | Hadoop | HBase | Storm | Spark | R 伺服器 |
  | --- | --- | --- | --- | --- | --- |
  | 前端：預設 VM 大小 |D3 |D3 |A3 |D12 |D12 |
  | 前端：建議的 VM 大小 |D3、D4、D12 |D3、D4、D12 |A3、A4、A5 |D12、D13、D14 |D12、D13、D14 |
  | 背景工作：預設 VM 大小 |D3 |D3 |D3 |Windows：D12；Linux：D4 |Windows：D12；Linux：D4 |
  | 背景工作：建議的 VM 大小 |D3、D4、D12 |D3、D4、D12 |D3、D4、D12 |Windows：D12、D13、D14；Linux：D4、D12、D13、D14 |Windows：D12、D13、D14；Linux：D4、D12、D13、D14 |
  | ZooKeeper：預設 VM 大小 | |A2 |A2 | | |
  | ZooKeeper：建議的 VM 大小 | |A2、A3、A4 |A2、A3、A4 | | |
  | 邊緣：預設 VM 大小 | | | | |Windows：D12；Linux：D4 |
  | 邊緣：建議的 VM 大小 | | | | |Windows：D12、D13、D14；Linux：D4、D12、D13、D14 |

> [!NOTE]
> 前端稱為 Storm 叢集類型的 Nimbus。 背景工作稱為 HBase 叢集類型的「區域」以及 Storm 叢集類型的「監督員」。

> [!IMPORTANT]
> 如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB RAM 的前端節點大小。
>
>

您可以使用[指令碼動作](#customize-clusters-using-script-action)，將 Hue 等其他元件新增至這些基本類型。

> [!IMPORTANT]
> HDInsight 叢集有各種不同類型，這些類型各自對應到叢集微調時所針對的工作負載或技術。 沒有任何支援方法可建立結合多個類型的叢集，例如在一個叢集上並存 Storm 和 HBase。
>
>

如果您的方案需要會分散到多個 HDInsight 叢集類型的技術，請建立 Azure 虛擬網路，然後在虛擬網路內建立必要的叢集類型。 此組態可讓叢集以及其中部署的任何程式碼直接彼此通訊。

如需如何搭配使用 Azure 虛擬網路與 HDInsight 的詳細資訊，請參閱[使用 Azure 虛擬網路擴充 HDInsight](hdinsight-extend-hadoop-virtual-network.md)。

如需在 Azure 虛擬網路內使用兩個叢集類型的範例，請參閱[使用 Storm 和 HBase 分析感應器資料](hdinsight-storm-sensor-data-analysis.md)。

### <a name="operating-system"></a>作業系統
您可以在 Linux 或 Windows 上建立 HDInsight 叢集。  如需有關作業系統版本的詳細資訊，請參閱[支援的 HDInsight 版本](hdinsight-component-versioning.md#supported-hdinsight-versions)。 HDInsight 3.4 及更新版本會使用 Ubuntu Linux 發行版本作為叢集的基礎作業系統。 

### <a name="version"></a>版本
這個選項可用來決定此叢集所需的 HDInsight 版本。 如需詳細資訊，請參閱[支援的 HDInsight 版本](hdinsight-component-versioning.md#supported-hdinsight-versions)。

### <a name="cluster-tiers"></a>叢集層

Azure HDInsight 提供兩種類型的巨量資料雲端提供項目：標準和進階。  如需詳細資訊，請參閱 [HDInsight 標準和 HDInsight 進階]](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)。

以下螢幕擷取畫面顯示選擇叢集類型的 Azure 入口網站資訊。

![HDInsight 進階組態](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


### <a name="credentials"></a>認證
使用 HDInsight 叢集，您可以在建立叢集期間設定兩個使用者帳戶：

* HTTP 使用者。 預設的使用者名稱為 *admin*。 使用 Azure 入口網站上的基本組態。 有時稱之為「叢集使用者」。
* SSH 使用者 (Linux 叢集)。 這可用來透過 SSH 連線到叢集。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

### <a name="location"></a>位置
不需要明確指定叢集的位置。 叢集會共用相同的預設儲存體位置。 如需支援的區域清單，請按一下 [HDInsight 價格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)中的 [區域] 下拉式清單。


### <a name="storage"></a>儲存體

原始的 Hadoop 分散式檔案系統 (HDFS) 會使用叢集上的多個本機磁碟。 HDInsight 會使用 [Azure 儲存體](hdinsight-hadoop-provision-linux-clusters.md#azure-storage)或 [Azure Data Lake Store](hdinsight-hadoop-provision-linux-clusters.md#azure-data-lake-store) 中的 Blob。 HDInsight 有一些使用 Data Lake Store 的特定需求。 如需詳細資訊，請參閱[使用 Azure 入口網站建立具有 Data Lake Store 的 HDInsight 叢集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)一節。

![HDInsight 儲存體](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)


#### <a name="azure-storage"></a>Azure 儲存體
Azure 儲存體是強大的一般用途儲存體解決方案，其完美整合了 HDInsight。 透過 HDFS 介面，HDInsight 中的完整元件集可直接處理 Blob 中儲存的結構化或非結構化資料。 將資料儲存在 Azure 儲存體中，可協助您安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。 HDInsight 僅支援__一般用途__的 Azure 儲存體帳戶。 目前不支援 __Blob 儲存體__帳戶類型。

在組態期間，您要指定 Azure 儲存體帳戶，並在該 Azure 儲存體帳戶中指定 Blob 容器。 叢集會以該 Blob 容器做為預設儲存位置。 您也可以選擇指定叢集可存取的其他 Azure 儲存體帳戶 (連結的儲存體)。 叢集也可以存取任何設有完整公用讀取權限或僅限對 Blob 之公用讀取權的 Blob 容器。

不建議您使用預設的 Blob 容器來儲存商務資料。 最好在每次使用後刪除預設的 Blob 容器，以減少儲存成本。 請注意，預設容器包含應用程式與系統記錄檔。 請務必先擷取記錄檔再刪除容器。

不支援多個叢集共用一個 Blob 容器。

如需有關使用 Azure 儲存體帳戶的詳細資訊，請參閱[搭配 HDInsight 使用 Azure 儲存體](hdinsight-hadoop-use-blob-storage.md)。

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
除了 Azure 儲存體，您還可以使用 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) 當做 HDInsight 中 HBase 叢集的預設儲存體帳戶，以及全部 4 種 HDInsight 叢集類型的連結儲存體。 如需詳細資訊，請參閱 [使用 Azure 入口網站建立具有 Data Lake Store 的 HDInsight 叢集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。

#### <a name="use-additional-storage"></a>其他儲存體

在某些情況下，您可能將更多儲存體加入至叢集。 例如，您可能有多個 Azure 儲存體帳戶用於不同的地理區域或不同的服務，但您想要全部透過 HDInsight 來分析。

當您建立 HDInsight 叢集時，或在建立叢集之後，您可以新增儲存體帳戶。  請參閱 [使用指令碼動作自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

如需有關次要 Azure 儲存體帳戶的詳細資訊，請參閱[使用 Azure 儲存體搭配 HDInsight](hdinsight-hadoop-use-blob-storage.md)。 如需次要 Data Lake 儲存體的詳細資訊，請參閱 [使用 Azure 入口網站建立 HDInsight 叢集與 Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。


#### <a name="use-hiveoozie-metastore"></a>Hive/Oozie 中繼存放區
如果想要在刪除 HDInsight 叢集之後保留 Hive 資料表，建議您使用自訂的中繼存放區。 您可以將該中繼存放區附加至另一個 HDInsight 叢集。

> [!IMPORTANT]
> 針對某個 HDInsight 叢集版本建立的 HDInsight 中繼存放區，不能在不同的 HDInsight 叢集版本之間共用。 如需 HDInsight 版本清單，請參閱[支援的 HDInsight 版本](hdinsight-component-versioning.md#supported-hdinsight-versions)。
>
>

中繼存放區包含 Hive 和 Oozie 中繼資料，例如 Hive 資料表、資料分割、結構描述和資料行。 中繼存放區能協助您保留 Hive 和 Oozie 中繼資料，因此在建立新叢集時，您便無需重建 Hive 資料表或 Oozie 工作。 根據預設，Hive 會使用內嵌的 Azure SQL 資料庫來儲存此資訊。 刪除叢集時，嵌入的資料庫將無法保留中繼資料。 如果您在已設定 Hive 中繼存放區的 HDInsight 叢集中建立 Hive 資料表，當您使用相同的 Hive 中繼存放區重建叢集時，仍會保留這些資料表。

HBase 叢集類型無法使用中繼存放區組態。

> [!IMPORTANT]
> 在建立自訂中繼存放區時，請勿使用包含破折號或連字號的資料庫名稱。 這可能會導致叢集建立程序失敗。
>
>

## <a name="install-hdinsight-applications"></a>安裝 HDInsight 應用程式

HDInsight 應用程式是使用者可以在以 Linux 為基礎的 HDInsight 叢集上安裝的應用程式。 Microsoft 獨立軟體廠商 (ISV) 或您可以自己開發這些應用程式。 如需詳細資訊，請參閱[在 Azure HDInsight 上安裝第三方 Hadoop 應用程式](hdinsight-apps-install-applications.md)。

大部分的 HDInsight 應用程式會安裝在空白的邊緣節點。  空白的邊緣節點是一部 Linux 虛擬機器，其中已安裝及設定和前端節點相同的用戶端工具。 您可以使用邊緣節點來存取叢集、測試用戶端應用程式，以及裝載用戶端應用程式。 如需詳細資訊，請參閱 [Use empty edge nodes in HDInsight (在 HDInsight 中使用空白的邊緣節點)](hdinsight-apps-use-edge-node.md)。

## <a name="configure-cluster-size"></a>設定叢集大小

客戶需根據叢集的生命期，就這些節點的使用量支付費用。 建立叢集後就開始計費，並在叢集刪除後停止計費。 無法取消配置或保留叢集。

不同的叢集類型具有不同的節點類型、節點數目和節點大小。 例如，Hadoop 叢集類型有兩個「前端節點」和預設的四個「資料節點」，而 Storm 叢集類型有兩個「Nimbus 節點」、三個「ZooKeeper 節點」和預設的四個「監督員節點」。 HDInsight 叢集的成本是由節點數和節點的虛擬機器大小來決定。 例如，如果您知道將會執行需要大量記憶體的作業，您可以選取具有較多記憶體的計算資源。 為了方便學習，建議您使用 1 個資料節點。 如需關於 HDInsight 定價的詳細資訊，請參閱 [HDInsight 定價](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)。

> [!NOTE]
> 叢集大小限制會隨著 Azure 訂用帳戶而有所不同。 若要提高限制，請與帳務支援人員連絡。
>
> 叢集使用的節點不會算做虛擬機器，因為節點所使用的虛擬機器映像是 HDInsight 服務的實作詳細資料。 節點所使用的計算核心則會算入訂用帳戶可用的計算核心總數。 當您建立 HDInsight 叢集時，可以在 [叢集大小] 刀鋒視窗的摘要區段中查看可用的核心數目，以及叢集將會使用的核心。
>
>

使用 Azure 入口網站設定叢集時，節點大小會透過 [節點定價層] 刀鋒視窗公開。 您也可以查看與不同節點大小相關聯的成本。 下列螢幕擷取畫面顯示 Linux 型 Hadoop 叢集的選項。

![HDinsight VM 節點大小](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

下表顯示 HDInsight 叢集支援的大小及提供的容量。

### <a name="standard-tier-a-series"></a>標準層級：A 系列
在傳統部署模型中，部分 VM 大小會與 PowerShell 和命令列介面 (CLI) 中的稍有不同。

* Standard_A3 是「大型」
* Standard_A4 是「特大型」

| 大小 | CPU 核心 | 記憶體 | NIC (最大) | 最大 磁碟大小 | 最大 資料磁碟 (每個 1023 GB) | 最大 IOPS (每個磁碟 500) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A3\大型 |4 |7 GB |2 |暫存 = 285 GB |8 |8x500 |
| Standard_A4\特大型 |8 |14 GB |4 |暫存 = 605 GB |16 |16x500 |
| Standard_A6 |4 |28 GB |2 |暫存 = 285 GB |8 |8x500 |
| Standard_A7 |8 |56 GB |4 |暫存 = 605 GB |16 |16x500 |

### <a name="standard-tier-d-series"></a>標準層級：D 系列
| 大小 | CPU 核心 | 記憶體 | NIC (最大) | 最大 磁碟大小 | 最大 資料磁碟 (每個 1023 GB) | 最大 IOPS (每個磁碟 500) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3 |4 |14 GB |4 |暫存 (SSD) = 200 GB |8 |8x500 |
| 標準_D4 |8 |28 GB |8 |暫存 (SSD) = 400 GB |16 |16x500 |
| 標準_D12 |4 |28 GB |4 |暫存 (SSD) = 200 GB |8 |8x500 |
| 標準_D13 |8 |56 GB |8 |暫存 (SSD) = 400 GB |16 |16x500 |
| Standard_D14 |16 |112 GB |8 |暫存 (SSD) = 800 GB |32 |32x500 |

### <a name="standard-tier-dv2-series"></a>標準層級：Dv2 系列
| 大小 | CPU 核心 | 記憶體 | NIC (最大) | 最大 磁碟大小 | 最大 資料磁碟 (每個 1023 GB) | 最大 IOPS (每個磁碟 500) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 GB |4 |暫存 (SSD) = 200 GB |8 |8x500 |
| Standard_D4_v2 |8 |28 GB |8 |暫存 (SSD) = 400 GB |16 |16x500 |
| Standard_D12_v2 |4 |28 GB |4 |暫存 (SSD) = 200 GB |8 |8x500 |
| Standard_D13_v2 |8 |56 GB |8 |暫存 (SSD) = 400 GB |16 |16x500 |
| Standard_D14_v2 |16 |112 GB |8 |暫存 (SSD) = 800 GB |32 |32x500 |

如需規劃使用這些資源時需注意的部署考量，請參閱 [虛擬機器的大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 如需各式大小的價格資訊，請參閱 [HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight)。   

> [!IMPORTANT]
> 如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB RAM 的前端節點大小。
>
>

建立叢集後就開始計費，並在叢集刪除後停止計費。 如需價格的詳細資訊，請參閱 [HDInsight 價格詳細資料](https://azure.microsoft.com/pricing/details/hdinsight/)。


> [!WARNING]
> 不支援在與 HDInsight 叢集不同的位置中使用其他儲存體帳戶。



## <a name="use-virtual-network"></a>使用虛擬網路
使用 [Azure 虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)，您可以建立安全、持續的網路，上面有您的解決方案所需的資源。 如需搭配虛擬網路使用 HDInsight 的詳細資訊 (包含虛擬網路的特定組態需求)，請參閱 [使用 Azure 虛擬網路延伸 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。


## <a name="customize-clusters-using-script-action"></a>使用指令碼動作來自訂叢集

您可以在建立期間使用指令碼來安裝其他元件或自訂組態。 這類指令碼可透過 **指令碼動作**叫用，指令碼動作是一個組態選項，其可從 Azure 入口網站、HDInsight Windows PowerShell Cmdlet 或 HDInsight .NET SDK 使用。 如需詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

您可以使用 Java 封存 (JAR) 檔案形式在叢集上執行一些原生 Java 元件 (例如 Mahout 和 Cascading)。 這些 JAR 檔案可以配送至 Azure 儲存體，並透過 Hadoop 作業提交機制提交至 HDInsight 叢集。 如需詳細資訊，請參閱 [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)。

> [!NOTE]
> 如果您在將 JAR 檔案部署至 HDInsight 叢集，或在 HDInsight 叢集上呼叫 JAR 檔案時發生問題，請連絡 [Microsoft 支援](https://azure.microsoft.com/support/options/)。
>
> Cascading 不受 HDInsight 支援，而且不符合「Microsoft 支援」的資格。 如需所支援元件的清單，請參閱 [HDInsight 所提供叢集版本的新功能](hdinsight-component-versioning.md)。
>
>

有時候，您可能要在建立程序期間設定下列組態檔：

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

如需詳細資訊，請參閱 [使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md)。

## <a name="cluster-creation-methods"></a>叢集建立方法
在本文中，您已了解建立以 Linux 為基礎的 HDInsight 叢集的基本資訊。 請利用下表，尋找如何使用最符合需求的方法建立叢集的具體資訊。

| 叢集建立方法 | Web 瀏覽器 | 命令列 | REST API | SDK | Linux、Mac OS X 或 Unix | Windows |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| [Azure 入口網站](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |✔ |✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |✔ |
| [Azure 資源管理員範本](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |

## <a name="troubleshoot"></a>疑難排解

如果您在建立 HDInsight 叢集時遇到問題，請參閱[存取控制需求](hdinsight-administer-use-portal-linux.md#create-clusters)。

## <a name="next-steps"></a>後續步驟

- [什麼是 HDInsight](hdinsight-hadoop-introduction.md)。
- [Hadoop 教學課程：開始在 HDInsight 中使用 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)。
