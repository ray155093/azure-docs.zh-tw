---
title: "修復 Azure 資訊安全中心的 OS 弱點 | Microsoft Docs"
description: "本文件說明如何實作 Azure 資訊安全中心建議的**修復 OS 弱點**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: e6b251d5b97c57b3b6f79d14e53fbed5ca37ecb0
ms.contentlocale: zh-tw
ms.lasthandoff: 06/17/2017


---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>修復 Azure 資訊安全中心的 OS 弱點
Azure 資訊安全中心每天會分析可能造成虛擬機器 (VM) 更容易受到攻擊的 VM 作業系統 (OS) 組態，並建議進行組態變更來處理這些弱點。 當 VM 的 OS 設定不符合建議的設定規則時，資訊安全中心會建議您解決這些弱點。

> [!NOTE]
> 如需受監視之特定設定的詳細資訊，請參閱[建議的設定規則清單](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。
>
>

## <a name="implement-the-recommendation"></a>實作建議

> [!NOTE]
> 本文件將使用範例部署來介紹服務。  本文件不是一份逐步解說指南。
>
>

1. 在 [建議] 刀鋒視窗中，選取 [修復 OS 弱點]。
   ![修復 OS 弱點][1]

    [修復 OS 弱點] 刀鋒視窗會開啟並列出您的 VM 和不符合建議的設定規則的 OS 設定。  針對每部 VM，此刀鋒視窗會識別︰

   * **失敗規則** -- VM 的 OS 設定不符合的規則數目。
   * **上次掃描時間** -- 資訊安全中心上次掃描 VM OS 設定的日期與時間。
   * **狀態** -- 弱點的目前狀態：

     * 未處理：尚未解決的弱點
     * 進行中：正在將建議套用到弱點，您不需要採取任何動作
     * 已解決：弱點已解決 (問題已解決時，該項目會呈現灰色)。
   * **嚴重性** -- 所有弱點設為「低」嚴重性，表示弱點應解決，但不需要立即處理。

2. 選取 VM。 該 VM 的刀鋒視窗會開啟並顯示失敗的規則。
   ![失敗的設定規則][2]

3. 選取規則。 在此範例中，我們選取 [密碼必須符合複雜性需求] 。 隨即開啟說明失敗的規則和影響的刀鋒視窗。 檢閱詳細資訊，並考慮如何套用作業系統設定。
  ![失敗規則的說明][3]

  資訊安全中心會使用一般設定列舉 (CCE) 指派設定規則的唯一識別碼。 此刀鋒視窗上提供下列資訊︰

  - 名稱 -- 規則的名稱
  - 嚴重性 -- 嚴重、重要或警告的 CCE 嚴重性值
  - CCIED -- 規則的 CCE 唯一識別碼
  - 說明 -- 規則的說明
  - 弱點 -- 不套用規則的弱點或風險說明
  - 影響 -- 套用規則時的業務影響
  - 預期值 -- 資訊安全中心對照規則分析 VM OS 設定時的預期值
  - 規則作業 -- 資訊安全中心對照規則分析 VM OS 設定時使用的規則作業
  - 實際值 -- 對照規則分析 VM OS 設定時的傳回值
  - 評估結果 -- 分析的結果︰通過、失敗

## <a name="see-also"></a>另請參閱
本文說明了如何實作資訊安全中心建議的「修復 OS 弱點」。 您可在[這裡](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)檢閱設定規則集。 資訊安全中心會使用 CCE (一般設定列舉) 指派設定規則的唯一識別碼。 如需詳細資訊，請造訪 [CCE](https://nvd.nist.gov/cce/index.cfm) 網站。

若要深入了解資訊安全中心，請參閱下列資源：

* [Azure 資訊安全中心支援的平台](security-center-os-coverage.md) - 提供支援的 Windows 與 Linux VM 清單。
* [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) - 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [在 Azure 資訊安全中心管理安全性建議](security-center-recommendations.md) - 了解建議如何協助您保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) - 了解如何監視 Azure 資源的健全狀況。
* [在 Azure 資訊安全中心管理與回應安全性警示](security-center-managing-and-responding-alerts.md) - 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) - 了解如何監視合作夥伴解決方案的健康情況狀態。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) - 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) - 尋找有關 Azure 安全性與合規性的部落格文章。

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png

