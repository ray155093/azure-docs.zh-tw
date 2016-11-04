---
title: 在 Azure 資訊安全中心套用系統更新 | Microsoft Docs
description: 本文件說明如何實作 Azure 資訊安全中心建議的「套用系統更新」和「在系統更新之後重新開機」。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2016
ms.author: terrylan

---
# 在 Azure 資訊安全中心套用系統更新
Azure 資訊安全中心每日監視 Windows 和 Linux 虛擬機器 (VM) 是否有遺漏的作業系統更新。資訊安全中心會根據 Windows VM 上設定的服務，從 Windows Update 或 Windows Server Update Services (WSUS) 擷取可用的安全性和重大更新清單。資訊安全中心也會檢查 Linux 系統中的最新更新。如果您的 VM 遺漏系統更新，資訊安全中心會建議您套用系統更新

> [!NOTE]
> 本文件將使用範例部署來介紹服務。這不是逐步指南。
> 
> 

## 實作建議
1. 在 [建議] 刀鋒視窗中，選取 [套用系統更新]。![套用系統更新][1]
2. [套用系統更新] 刀鋒視窗會開啟，顯示 VM 遺漏的系統更新的清單。選取 VM。![選取 VM][2]
3. 隨即開啟一個刀鋒視窗，其中顯示該 VM 的遺漏更新的清單。選取系統更新。在此範例中，我們選取 KB3156016。![遺漏的安全性更新][3]
4. 請依照 [安全性更新] 刀鋒視窗中的步驟，套用遺漏的更新。![安全性更新][4]

## 在系統更新之後重新開機
1. 返回 [建議] 刀鋒視窗。在您套用系統更新之後會產生新的項目，稱為**在系統更新之後重新開機**。此項目可讓您知道您需要重新啟動 VM 以完成套用系統更新的程序。![在系統更新之後重新開機][5]
2. 選取 [在系統更新之後重新開機]。這會開啟 [重新啟動正在等待以完成系統更新] 刀鋒視窗，其中顯示 VM，您必須加以重新啟動才能完成套用系統清單更新的程序。![重新啟動擱置中][6]

從 Azure 重新啟動 VM 以完成程序。

## 另請參閱
如要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) --了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健康狀態。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) -- 尋找有關 Azure 安全性與相容性的部落格文章。

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]: ./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png

<!---HONumber=AcomDC_0803_2016-->