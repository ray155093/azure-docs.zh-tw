---
title: "在 Azure 資訊安全中心啟用資料收集 | Microsoft Docs"
description: " 了解如何在 Azure 資訊安全中心啟用資料收集。 "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7e9ad8cd8c77c57c37dc208b86b3727a4e1dc7b5
ms.contentlocale: zh-tw
ms.lasthandoff: 06/17/2017


---
# <a name="enable-data-collection-in-azure-security-center"></a>在 Azure 資訊安全中心啟用資料收集

> [!NOTE]
> 從 2017 年 6 月初開始，資訊安全中心會使用 Microsoft Monitoring Agent 來收集和儲存資料。 如需詳細資訊，請參閱 [Azure 資訊安全中心平台移轉](security-center-platform-migration.md)。 本文中的資訊說明轉換至 Microsoft Monitoring Agent 後的資訊安全中心功能。
>
>

資訊安全中心會收集虛擬機器 (VM) 的資料，以便評估其安全性狀態、提供安全性建議，並對您發出威脅警示。 當您第一次存取資訊安全中心時，可以選擇為訂用帳戶中的所有 VM 啟用資料收集。 如果未啟用資料收集，資訊安全中心建議您在該訂用帳戶的安全性原則中開啟資料收集。

在啟用資料收集之後，資訊安全中心會在所有現有支援的 Azure 虛擬機器和任何新建立的虛擬機器上佈建 Microsoft Monitoring Agent。 Microsoft Monitoring Agent 會掃描各種安全性相關設定。 此外，作業系統會引發事件記錄事件。 這類資料的範例包括︰作業系統類型和版本、作業系統記錄檔 (Windows 事件記錄檔)、執行中程序、電腦名稱、IP 位址、已登入的使用者和租用戶識別碼。 Microsoft Monitoring Agent 會讀取事件記錄項目和設定，並將該資料複製到您的工作區進行分析。 Microsoft Monitoring Agent 也會將損毀傾印檔案複製到工作區。

如果您是使用免費層的資訊安全中心，可以在安全性原則中關閉資料收集，以停用虛擬機器的資料收集。 停用資料收集會限制 VM 的安全性評估。 如需詳細資訊，請參閱[停用資料收集](#disabling-data-collection)。 即使已停用資料集合，仍然會啟用 VM 磁碟快照集和構件集合。 資訊安全中心標準層上的訂用帳戶需要資料收集。

> [!NOTE]
> 深入了解資訊安全中心的免費和標準[定價層](security-center-pricing.md)。
>
>

## <a name="implement-the-recommendation"></a>實作建議

> [!NOTE]
> 本文件將使用範例部署來介紹服務。 本文件不是一份逐步解說指南。
>
>

1. 在 [建議] 刀鋒視窗中，選取 [啟用訂用帳戶的資料收集]。  這會開啟 [開啟資料收集] 刀鋒視窗。
   ![建議刀鋒視窗][2]
2. 在 [開啟資料收集]  刀鋒視窗中，選取您的訂用帳戶。 該訂用帳戶的 [安全性原則]  刀鋒視窗隨即開啟。
3. 在 [安全性原則] 刀鋒視窗上，選取 [資料收集] 底下的 [開啟] 以自動收集記錄檔。 開啟資料收集會將監視擴充功能佈建在訂用帳戶的所有目前支援和新支援的 VM 上。
4. 選取 [ **儲存**]。
5. 選取 [確定] 。

## <a name="disabling-data-collection"></a>停用資料收集
如果您是使用免費層的資訊安全中心，可以隨時在安全性原則中關閉資料收集，以停用虛擬機器的資料收集。 資訊安全中心標準層上的訂用帳戶需要資料收集。

1. 返回 [資訊安全中心] 刀鋒視窗，然後選取 [原則] 圖格。 這會開啟 [安全性原則 - 定義每個訂用帳戶的原則] 刀鋒視窗。
   ![選取原則圖格][5]
2. 在 [安全性原則 - 定義每個訂用帳戶的原則] 刀鋒視窗中，選取要停用資料收集的訂用帳戶。
3. 該訂用帳戶的 [安全性原則] 刀鋒視窗隨即開啟。  選取 [資料收集] 底下的 [關閉]。
4. 在最上方的功能區中，選取 [儲存]。

## <a name="next-steps"></a>後續步驟
本文章說明如何實作資訊安全中心建議的「啟用資料收集」。 若要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) --了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助您保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)-- 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健康狀態。
- [Azure 資訊安全中心資料安全性](security-center-data-security.md) - 了解資訊安全中心如何管理及保護其中的資料。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)-- 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-- 取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

