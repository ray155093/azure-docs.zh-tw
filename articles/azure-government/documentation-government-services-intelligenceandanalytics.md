---
title: "Azure Government 智慧 + 分析 | Microsoft Docs"
description: "這為 Azure Government 的開發應用程式提供功能和指引的比較"
services: Azure-Government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: 0302a44bb42200c07a313bf2862479c1722af8c4
ms.openlocfilehash: 2a8769f7a611fb22e39db166d3416728f57cbbf4


---
# <a name="azure-government-intelligence--analytics"></a>Azure Government 智慧 + 分析
本文概述 Azure Government 環境的智慧和分析服務、變化以及考量。

## <a name="azure-hdinsight"></a>Azure HDInsight
HDInsight 已在 Azure Government 中正式推出。

### <a name="variations"></a>變化
下列 HDInsight 功能目前無法在 Azure Government 中使用。

* HDInsight 無法在 Windows 上使用。
* Azure Data Lake Store 目前無法在 Azure Government 中使用。 Azure Blob 儲存體是目前唯一可用的儲存體選項。
* Azure Active Directory Domain Services 整合尚無法使用。 若要建立安全的 Hadoop 叢集而不使用 Active Directory Domain Services，請選取下列其中一個設定案例︰

1. HDINSIGHT 與 AZURE IAAS 上執行之 ACTIVE DIRECTORY 的整合

  這是截至目前為止最簡單的 HDInsight 與 Active Directory 整合架構。 架構圖如下所示。 在此架構中，您會將 Active Directory 網域控制站放在 Azure 中的一或多個 VM 上執行。 這些 VM 通常會位於虛擬網路內。 您可以設定新的虛擬網路，在其中放置您的 HDInsight 叢集。 為了讓 Hdinsight 能夠看到 Active Directory，您必須使用 [VNET 對 VNET 對等互連] (https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal) 將這些虛擬網路對等互連。 
  
  必須在 Active Directory 上設定的必要條件
     * 您必須建立組織單位，以在其中放置叢集所用的 HDInsight 叢集 VM 和服務主體。 
     * 必須設定 LDAPS，以便與 Active Directory 進行通訊。 用來設定 LDAPS 的憑證必須是真正的憑證 (而非自我簽署憑證)。
     * 必須在網域上建立反向 DNS 區域，以供 HDI 子網路的 IP 位址範圍使用。
     * 需要服務帳戶或使用者帳戶，以用來建立 HDInsight 叢集。 此帳戶必須具備下列權限
        * 在組織單位內建立服務主體物件和電腦物件的權限。
        * 建立反向 DNS Proxy 規則的權限。
        * 將電腦加入 Active Directory 網域的權限。
        
2. 透過 VPN 設定的 HDINSIGHT 與內部部署 ACTIVE DIRECTORY 整合
  
  此架構就像 1 號架構。 唯一的差別在於，在此案例中，您的 Active Directory 會在內部部署，而且 HDInsight 與 Active Directory 之間的通訊，是透過 [Azure 到內部部署網路之間的 VPN 連線] 來達成 (https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction)。 此設定的架構圖如下所示。 
  
  必須在內部部署 Active Directory 上設定的必要條件
     * 您必須建立組織單位，以在其中放置叢集所用的 HDInsight 叢集 VM 和服務主體。
     * 必須設定 LDAPS，以便與 Active Directory 進行通訊。 用來設定 LDAPS 的憑證必須是真正的憑證 (而非自我簽署憑證)。
     * 必須在網域上建立反向 DNS 區域，以供 HDI 子網路的 IP 位址範圍使用。
     * 需要服務帳戶或使用者帳戶，以用來建立 HDInsight 叢集。 此帳戶必須具備下列權限
        * 在組織單位內建立服務主體物件和電腦物件的權限。
        * 建立反向 DNS Proxy 規則的權限。
        * 將電腦加入 Active Directory 網域的權限。


Azure Government 中的 Log Analytics URL 不同：

| 服務類型 | Azure 公用 | Azure Government |
| --- | --- | --- |
| HDInsight Cluster | \*.azurehdinsight.net | \*.azurehdinsight.us |

如需詳細資訊，請參閱 [Azure HDInsight 公開文件](../hdinsight/hdinsight-hadoop-introduction.md)。


## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格</a>。



<!--HONumber=Jan17_HO2-->


