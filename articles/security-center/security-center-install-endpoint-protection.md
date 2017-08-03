---
title: "在 Azure 資訊安全中心安裝端點保護 | Microsoft Docs"
description: "本文件說明如何實作 Azure 資訊安全中心建議的「安裝端點保護」。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: efb86a0ae362c30a6772c391a499154b7ae2a697
ms.contentlocale: zh-tw
ms.lasthandoff: 07/12/2017

---
# <a name="install-endpoint-protection-in-azure-security-center"></a>在 Azure 資訊安全中心安裝端點保護
如果尚未啟用 Endpoint Protection，則 Azure 資訊安全中心建議您在 Azure 虛擬機器 (VM) 上安裝 Endpoint Protection。 這項建議僅適用於 Windows VM。

> [!NOTE]
> 此範例部署會使用 Microsoft Antimalware。 請參閱 [Azure 資訊安全中心中的夥伴整合](security-center-partner-integration.md#partners-that-integrate-with-security-center)以取得與資訊安全中心整合的夥伴清單。  
>
>

## <a name="implement-the-recommendation"></a>實作建議

> [!NOTE]
> 本文件將使用範例部署來介紹服務。  本文件不是一份逐步解說指南。
>
>

1. 在 [建議] 刀鋒視窗中，選取 [安裝端點保護]。
   ![選取安裝端點保護][1]
2. [安裝 Endpoint Protection] 刀鋒視窗隨即開啟，並顯示沒有 Endpoint Protection 的 VM 清單。 從清單中選取您想要安裝 Endpoint Protection 的 VM，然後按一下 [在 VM 上安裝]。
   ![選取要安裝 Endpoint Protection 的 VM][2]
3. [選取 Endpoint Protection] 刀鋒視窗隨即開啟，讓您選取想要使用的 Endpoint Protection 解決方案。 在此範例中，讓我們選取 [Microsoft Antimalware] 。
   ![][3]
4. 將會顯示 Endpoint Protection 解決方案的其他相關資訊。 選取 [ **建立**]。
   ![建立反惡意程式碼解決方案][4]
5. 在 [加入擴充功能] 刀鋒視窗上輸入必要的組態設定，然後選取 [確定]。 若要深入了解組態設定，請參閱 [預設和自訂的反惡意程式碼組態](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration)。

[Microsoft Antimalware](../security/azure-security-antimalware.md)現在已在選取的 VM 上使用。

## <a name="see-also"></a>另請參閱
本文說明了如何實作資訊安全中心建議的「安裝端點保護」。 若要深入了解如何在 Azure 中啟用反惡意程式碼軟體，請參閱下列文件：

* [適用於雲端服務和虛擬機器的 Microsoft Antimalware](../security/azure-security-antimalware.md) -- 了解如何部署 Microsoft Antimalware。

若要深入了解資訊安全中心，請參閱下列文件：

* [設定 Azure 資訊安全中心的安全性原則](security-center-policies.md) -- 了解如何設定安全性原則。
* [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健全狀況。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) -- 尋找有關 Azure 安全性與相容性的部落格文章。

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png

