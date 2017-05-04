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
ms.date: 01/05/2017
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 49ffe988867ba70df5bd7757a64edc9aa813071d
ms.lasthandoff: 04/29/2017


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>如何在 Azure Marketplace 中發佈和管理供應項目
此文章可協助開發人員建立解決方案、部署並管理他們在 Azure Marketplace 中列出的解決方案，讓其他 Azure 客戶與合作夥伴可進行購買及使用。

## <a name="what-is-the-azure-marketplace"></a>什麼是 Azure Marketplace？
身為 Azure 的發佈者，Azure Marketplace 可讓您發佈及販售創新解決方案或服務給其他想要快速開發其雲端應用程式和行動解決方案的開發者、ISV、IT 專業人員。 如果您的解決方案是以商務使用者為目標，則您可能要考慮 [AppSource](http://appsource.microsoft.com) Marketplace。


## <a name="supported-types-of-solutions"></a>支援的解決方案類型
首先，您需要以發行者的身分，定義您的公司提供的解決方案類型。 Azure Marketplace 支援下列幾種供應項目：

|解決方案類型|虛擬機器|解決方案範本|
|---|---|---|
|定義|預先配置的映像，包含完整安裝的作業系統以及一或多個應用程式。 虛擬機器映像提供在 Azure 虛擬機器服務中建立及部署虛擬機器所需的資訊。|可參考一或多個獨特 Azure 服務的資料結構 (包含由其他賣家發佈的服務)，讓 Azure 訂戶能以單一、協調的方式部署一或多個供應項目。|
|範例|**例如** ，身為 Azure 的發佈者，您已建立並驗證帶有創新資料庫服務的 VM，此 VM 令人讚賞，使其他 Azure 訂戶願意購買此 VM 並部署至其雲端服務環境。|**例如**，身為 Azure 發佈者，您組合了 Azure 的幾項服務，以便快速部署負載平衡、強化安全性且高可用性的雲端服務。 其他 Azure 訂戶可藉由取得符合其目標的解決方案範本來節省時間，而不必手動尋找、取得、部署及設定相同或類似的 Azure 服務。|

> [!NOTE]
> 請注意，有些步驟會在不同類型的解決方案間共用，但有些步驟則因解決方案類型而有所不同。 本文提供簡短的概觀，讓您了解每種類型的解決方案必須完成哪些步驟。

## <a name="how-to-publish-a-solution"></a>如何發佈解決方案
![draw](media/marketplace-publishing-getting-started/img01.png)

### <a name="1-nominate-your-solution-for-pre-approval"></a>1.提名您的解決方案以供預先核准
- 在[這裡](https://createopportunity.azurewebsites.net)完成 **Microsoft Azure 虛擬機器認證**解決方案提名表單

>[!NOTE]
> 如果您與合作夥伴帳戶經理或 DX 夥伴經理接洽，請要求對方在 Azure 認證計劃中提名您的解決方案，或前往 [Microsoft Azure 認證](http://createopportunity.azurewebsites.net)網頁，填妥申請表單並在 [Microsoft 贊助者連絡人] 欄位中輸入您的夥伴客戶經理或 DX 夥伴經理的電子郵件。

如果符合 [Azure Marketplace 參與原則](http://go.microsoft.com/fwlink/?LinkID=526833)的資格條件且您的應用程式獲得核准，我們就會開始與您合作將您的解決方案上架到 Azure Marketplace。

### <a name="2-register-your-account-as-a-microsoft-seller"></a>2.將您的帳戶註冊為 Microsoft 賣方
- [將您的 Microsoft 帳戶註冊為 Microsoft 開發人員帳戶](marketplace-publishing-accounts-creation-registration.md)

### <a name="3-publish-your-solution"></a>3.發佈您的解決方案
1. 滿足非技術性需求
  - [滿足非技術性必要條件](marketplace-publishing-pre-requisites.md)
  - [VM 技術性必要條件](marketplace-publishing-vm-image-creation-prerequisites.md)
  - [解決方案範本技術性必要條件](marketplace-publishing-solution-template-creation-prerequisites.md)
2. 建立您的供應項目
  - [虛擬機器](marketplace-publishing-vm-image-creation.md)
  - [解決方案範本](marketplace-publishing-solution-template-creation.md)
3. [建立您的供應項目行銷內容](marketplace-publishing-push-to-staging.md)
4. 在預備環境中測試您的供應項目
  - [在預備環境中測試您的 VM 優惠](marketplace-publishing-vm-image-test-in-staging.md)
  - [在預備環境中測試您的解決方案範本供應項目](marketplace-publishing-solution-template-test-in-staging.md)
5. [將您的供應項目部署至 Azure Marketplace](marketplace-publishing-push-to-production.md)


### <a name="virtual-machine-image-specific"></a>虛擬機器映像專用
* [建立內部部署 VM 映像](marketplace-publishing-vm-image-creation-on-premise.md)
* [在 Azure 入口網站中建立執行 Windows 的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [在 Azure 入口網站中建立執行 Linux 的虛擬機器](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [針對 VHD 建立常見問題進行疑難排解](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="how-to-manage-your-solution"></a>如何管理您的解決方案
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
* [了解付款報告](marketplace-publishing-report-payout.md)
* [以發佈者身分取得支援](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>其他資源
* [設定 Azure PowerShell](marketplace-publishing-powershell-setup.md)

