---
title: "Azure 指引 | Microsoft Docs"
description: "Azure 的最佳做法和指引"
services: 
documentationcenter: na
author: bennage
manager: marksou
editor: 
tags: 
ms.assetid: de94c74a-fea7-4815-8484-553e421a7490
ms.service: guidance
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: christb
translationtype: Human Translation
ms.sourcegitcommit: 5f3ced657cf3d6587a63789b3dd3ca41cd2856f0
ms.openlocfilehash: 0061e1ff2ae2d6b8ed7b7c3bb60405e76d4cc91b


---
# <a name="azure-guidance"></a>Azure 指導
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Microsoft 模式和做法團隊是 Azure 客戶諮詢團隊的一部分。 我們的目的是協助開發人員、架構設計人員及 IT 專業人員可以成功使用 Microsoft Azure 平台。 我們建立了一套指導程序，提供在 Azure 上建置雲端解決方案的最佳做法。

## <a name="checklists"></a>檢查清單
這些清單是檢閱可用性和調整性基本層面的快速參考。 

* [可用性檢查清單][AvailabilityChecklist] 
  
    確保可用性的建議做法摘要。
* [延展性檢查清單][ScalabilityChecklist]
  
    設計和實作可調整服務和處理資料管理的建議做法摘要。

## <a name="best-practices-articles"></a>最佳做法文章
這些文章提供與雲端計算相關之重要通用概念的深入討論。 

* [API 設計][APIDesign] 
  
    設計 Web API 時需要考量的設計問題討論。
* [API 實作][APIImplementation] 
  
    實作及發佈 Web API 的一組建議做法。
* [API 安全性指導](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    驗證和授權考量的討論 (例如權杖類型、授權通訊協定、授權流程及威脅風險降低)。
* [自動調整指引][AutoscalingGuidance] 
  
    無需手動介入的調整解決方案考量摘要。
* [背景作業指引][BackgroundJobsGuidance] 
  
    實作應該在背景中執行之工作的可用選項和建議做法描述，與前景或互動作業獨立。
* [內容傳遞網路 (CDN) 指引][CDNGuidance] 
  
    使用 CDN 減少您應用程式中的負載，並提高可用性和效能的一般指導和建議的做法。
* [快取指引][CachingGuidance] 
  
    如何使用快取來改善系統的效能和調整性。
* [資料分割指引][DataPartitioningGuidance]
  
    資料分割使用的策略可以協助改善調整性、減少爭用並最佳化效能。
* [監視和診斷指引][MonitoringandDiagnosticsGuidance] 
  
    追蹤使用者利用您系統的方式、追蹤資源使用量的方式，以及通常監視您系統之健全狀況和效能的方式的指導。
* [建議的命名慣例][naming-conventions] 
  
    Azure 資源的建議命名慣例。
* [一般重試方針][RetryGeneralGuidance] 
  
    處理暫時性錯誤的一般概念討論。
* [重試服務的特定指引][RetryServiceSpecificGuidance]
  
    大多數 Azure 服務的重試摘要，包括協助您使用、調整，或擴充該服務的重試機制之相關資訊。

## <a name="scenario-guides"></a>案例指南
* [在 Azure 上執行 Elasticsearch][elasticsearch] 
  
    Elasticsearch 是可大幅調整的開放原始碼搜尋引擎和資料庫。 它很適用於需要對大型資料集所保存的資訊進行快速分析與探索的情況。 本指導方針探討設計 Elasticsearch 叢集時所需考量的一些重要層面。
* [多組織用戶共享應用程式的身分識別管理][identity-multitenant] 
  
    多租用戶是多個租用戶共用應用程式，但會彼此隔離的架構。 此指南將向您說明如何使用 [Azure Active Directory][AzureAD] 處理登入與驗證，於多組織用戶共享應用程式中管理使用者身分識別。
* [開發巨量資料解決方案](https://msdn.microsoft.com/library/dn749874.aspx)
  
    本指南會探討 HDInsight 的使用案例，例如互動探索、做為資料倉儲、進行 ETL 程序以及與現有的 BI 系統整合。 其中也包含了解巨量資料概念、規劃及設計巨量資料解決方案，以及實作這些解決方案的指導。

## <a name="patterns"></a>模式
* [雲端設計模式：雲端應用程式的規範架構指導](https://msdn.microsoft.com/library/dn568099.aspx)
  
    雲端設計模式是設計模式和相關指導主題的程式庫， 它以說明每個部分如何與雲端應用程式架構相符的方式，明確表達出套用模式的優點。
* [雲端應用程式的效能最佳化](https://github.com/mspnp/performance-optimization)
  
    本指導在探索會妨礙應用程式在負載之下進行調整的一般反向模式。 它包含了 8 個範例，說明反向模式和[初級效能分析](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md)，以及[評估關鍵評量效能](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md)的指南。

## <a name="reference-architectures"></a>參考架構
我們的參考架構會依案例編排。
每個架構都提供建議的做法、規定步驟，以及讓建議具體化的可執行元件。

目前的參考架構程式庫位於 [http://aka.ms/architecture](http://aka.ms/architecture)。

## <a name="resiliency-guidance"></a>復原指導
這些主題說明如何設計在分散式雲端環境中從失敗中恢復的應用程式。   

* [復原概觀][ResiliencyOvervew]
  
     如何在 Azure 平台中建置可從失敗中復原並繼續運成的應用程式。 說明一種從設計到實作、部署和作業的結構化復原方式。
* [復原檢查清單][resiliency-checklist]
  
    一份包含多項建議的檢查清單，可協助您規劃可能發生的各項失敗模式。
* [失敗模式分析][resiliency-fma] 
  
    失敗模式分析 (FMA) 是一項程序，以識別可能的失敗點數在系統中建置復原功能。 做為 FMA 程序的起點，本文包含可能的失敗模式#及其補救措施。 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Dec16_HO1-->


