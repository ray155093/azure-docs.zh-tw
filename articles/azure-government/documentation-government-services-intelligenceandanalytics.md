---
title: "Azure Government 智慧 + 分析 | Microsoft Docs"
description: "這為 Azure Government 的開發應用程式提供功能和指引的比較"
services: azure-government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 0233aa66bc4f4f135456ec15bd09756e63192b14
ms.lasthandoff: 03/06/2017


---
# <a name="azure-government-intelligence--analytics"></a>Azure Government 智慧 + 分析
本文概述 Azure Government 環境的智慧和分析服務、變化以及考量。

## <a name="hdinsight"></a>HDInsight
Linux Standard 的 HDInsight 已在 Azure Government 中正式推出。 您可以在<a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>這裡</a>取得一個示範，以了解如何使用 HDInsight 在 Azure Government 上建置以資料為中心的解決方案。

即將推出 Linux Premium 上的 HDInsight。

### <a name="variations"></a>變化
下列 HDInsight 功能目前無法在 Azure Government 中使用。

* HDInsight 無法在 Windows 上使用。
* Azure Data Lake Store 目前無法在 Azure Government 中使用。 Azure Blob 儲存體是目前唯一可用的儲存體選項。

Azure Government 中的 Log Analytics URL 不同：

| 服務類型 | Azure 公用 | Azure Government |
| --- | --- | --- |
| HDInsight Cluster | \*.azurehdinsight.net | \*.azurehdinsight.us |

如需詳細資訊，請參閱 [HDInsight 公開文件](../hdinsight/hdinsight-hadoop-introduction.md)。

## <a name="power-bi"></a>Power BI
Power BI 美國政府版本已正式上市，可做為 Office 365 美國政府社群訂用帳戶的一部分。 您可以在<a href=https://powerbi.microsoft.com/en-us/documentation/powerbi-service-govus-overview/>這裡</a>了解 Power BI 美國政府版本。 您可以在<a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>這裡</a>取得一個示範，以了解如何使用 Power BI 在 Azure Government 上建置以資料為中心的解決方案。

即將推出 Power BI Embedded。

### <a name="variations"></a>變化

Power BI 的 URL 與美國政府版本的不同：

| 服務類型 | Power BI 社群 | Power BI 美國政府版本 |
| --- | --- | --- |
| Power BI URL | app.powerbi.com | app.powerbigov.us |

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。 </a>

