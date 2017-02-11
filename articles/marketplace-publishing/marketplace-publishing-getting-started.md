---
title: "概述如何建立及部署優惠至 Marketplace | Microsoft Docs"
description: "了解在 Azure Marketplace 中成為核准的 Microsoft 開發人員，以及建立及部署虛擬機器映像、範本、資料服務或開發人員服務所需的步驟"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: aed47a7e9aa06e48199a71f67ae6d82bcfe27c9a


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>如何在 Azure Marketplace 中發佈和管理供應項目
此文章可協助開發人員建立解決方案、部署並管理他們在 Azure Marketplace 中列出的解決方案，讓其他 Azure 客戶與合作夥伴可進行購買及使用。

## <a name="what-is-the-azure-marketplace"></a>什麼是 Azure Marketplace？
Azure 的訂閱者可以在 Azure Marketplace 找到服務，用來協助他們開發內部部署或雲端型解決方案和應用程式。 然後使用 [Azure 認證](http://azure.com/certified) 的服務當做建置組塊，快速開發用於企業營運和其他 Azure 訂用帳戶的創新應用程式或服務。

身為 Azure 的發佈者，Azure Marketplace 可讓您發佈及販售創新解決方案或服務給其他想要快速開發其雲端應用程式和行動解決方案的開發者、ISV、IT 專業人員。

## <a name="supported-types-of-offers"></a>支援的供應項目類型
首先，您需要以發行者的身分，定義您的公司提供的解決方案類型。 Azure Marketplace 支援三種供應項目：

* **虛擬機器映像** 是預先配置的映像，包含完整安裝的作業系統以及一或多個應用程式。 虛擬機器映像提供在 Azure 虛擬機器服務中建立及部署虛擬機器所需的資訊。

  > [!NOTE]
  > **例如** ，身為 Azure 的發佈者，您已建立並驗證帶有創新資料庫服務的 VM，此 VM 令人讚賞，使其他 Azure 訂戶願意購買此 VM 並部署至其雲端服務環境。
  >
  >
* **開發人員服務** 是用於應用程式開發或系統管理、完全受管理的服務。 它們提供的功能使得 Azure 上雲端規模的應用程式得以快速開發。

  > [!NOTE]
  > **例如** ，身為 Azure 的發佈者，您已開發可根據歷史資料提供預測的 API 存取服務 (裝載於 Azure 或其他位置)。 而且這個服務是其他建立解決方案的 Azure 訂閱者人員可能想要使用的服務。 您可以將此服務部署至 Azure Marketplace 供其他人尋找、購買，並用在其各自的服務中。
  >
  >
* **解決方案範本** 是可參考一個或多個獨特 Azure 服務的資料結構 (包含由其他賣家發佈的服務)，讓 Azure 訂戶能以單一、協調的方式部署一或多個供應項目。

  > [!NOTE]
  > **例如** ，身為 Azure 的發佈者，您組合了 Azure 上的幾個服務，只需按幾下滑鼠便可以更快速地部署安全、高可用性且負載平衡的雲端服務。 其他 Azure 訂戶發現這個解決方案範本的價值，能夠為他們節省時間，不必手動識別和設定相同或類似的 Azure 服務。
  >
  >

不同類型的解決方案之間有一些相同的步驟。 本文提供簡短的概觀，讓您了解每種類型的解決方案必須完成哪些步驟。

## <a name="1-pre-requisites"></a>1.必要條件
> [!NOTE]
> 開始在 Azure Marketplace 上進行任何工作之前，必須先通過核准 ( [預先核准](http://azure.com/certified))。
>
>

1. [適用於 Microsoft Azure 認證的預先核准](marketplace-publishing-azure-certification.md)
2. [建立與註冊 Microsoft 開發人員帳戶](marketplace-publishing-accounts-creation-registration.md)
3. [滿足非技術性必要條件](marketplace-publishing-pre-requisites.md)

## <a name="2-publishing-your-offer"></a>2.發佈您的供應項目
### <a name="21-complete-offer-specific-technical-pre-requisites"></a>2.1 完成特定供應項目的技術性必要條件
* [VM 技術性必要條件](marketplace-publishing-vm-image-creation-prerequisites.md)
* [解決方案範本技術性必要條件](marketplace-publishing-solution-template-creation-prerequisites.md)

### <a name="22-create-your-offer"></a>2.2 建立您的供應項目
1. 使用下列的特定供應項目指南建立您的供應項目。
   * [建立您的 VM 供應項目](marketplace-publishing-vm-image-creation.md)
   * [建立您的解決方案範本供應項目](marketplace-publishing-solution-template-creation.md)
2. [建立您的供應項目行銷內容](marketplace-publishing-push-to-staging.md)

### <a name="23-test-your-offer-in-staging"></a>2.3 在預備環境中測試您的供應項目
* [在預備環境中測試您的 VM 優惠](marketplace-publishing-vm-image-test-in-staging.md)
* [在預備環境中測試您的解決方案範本供應項目](marketplace-publishing-solution-template-test-in-staging.md)

### <a name="24-deploy-your-offer-to-the-marketplace"></a>2.4 將您的供應項目部署至 Marketplace
* [將您的供應項目部署至 Azure Marketplace](marketplace-publishing-push-to-production.md)
* [疑難排解 Marketplace 中常見的發佈問題](marketplace-publishing-support-common-issues.md)
* 若要深入了解使用的入口網站，請參閱 [您需要的入口網站](marketplace-publishing-portals.md)

### <a name="virtual-machine-image-specific"></a>虛擬機器映像專用
* [建立內部部署 VM 映像](marketplace-publishing-vm-image-creation-on-premise.md)
* [在 Azure 入口網站中建立執行 Windows 的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [在 Azure 入口網站中建立執行 Linux 的虛擬機器](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [針對 VHD 建立常見問題進行疑難排解](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="3-post-publishing-management-of-your-offer"></a>3.供應項目的後期發佈管理
* [虛擬機器優惠的後期製作指南](marketplace-publishing-vm-image-post-publishing.md)
* [如何更新優惠或 SKU 的非技術性詳細資料](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
* [如何更新供應項目或 SKU 的技術性詳細資料](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
* [如何在已列出的供應項目下新增 SKU](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
* [如何變更已列出 SKU 的資料磁碟計數](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
* [如何從 Azure Marketplace 刪除已列出的供應項目](marketplace-publishing-vm-image-post-publishing.md)
* [如何從 Azure Marketplace 刪除已列出的 SKU](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
* [如何從 Azure Marketplace 刪除已列出的 SKU 目前的版本](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
* [如何將列出價格還原成生產環境值](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
* [如何將計費模式還原成生產環境值](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
* [如何將已列出的 SKU 的可見性設定還原成生產環境值](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
* [如何變更雲端解決方案提供者轉售商獎勵](marketplace-publishing-csp-incentive.md)
* [了解賣方 Insights 報告](marketplace-publishing-report-seller-insights.md)
* [了解付款報告](marketplace-publishing-report-payout.md)
* [以發佈者身分取得支援](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>其他資源
* [設定 Azure PowerShell](marketplace-publishing-powershell-setup.md)



<!--HONumber=Nov16_HO3-->


