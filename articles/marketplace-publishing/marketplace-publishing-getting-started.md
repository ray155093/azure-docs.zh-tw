<properties
   pageTitle="概述如何建立及部署優惠至 Marketplace | Microsoft Azure"
   description="了解在 Azure Marketplace 中成為核准的 Microsoft 開發人員，以及建立及部署虛擬機器映像、範本、資料服務或開發人員服務所需的步驟"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/05/2016"
   ms.author="hascipio" />

# 如何在 Azure Marketplace 中發佈和管理供應項目
此文章可協助開發人員建立解決方案、部署並管理他們在 Azure Marketplace 中列出的解決方案，讓其他的 Azure 客戶和合作夥伴可進行購買及利用。

首先，您需要以發行者的身分，定義您的公司提供的解決方案類型。Azure Marketplace 支援多種解決方案，您必須為每個解決方案進行略為不同的工作，才能成功發佈到 Marketplace。

## 供應項目類型
|供應項目類型| 定義 |
|---|---|
|虛擬機器映像 | 預先配置的虛擬機器 (VM) 映像，包含完整安裝的作業系統以及一或多個應用程式。虛擬機器映像供應項目包含單一 VM 映像或是由解決方案範本繫結在一起的多個 VM 映像。虛擬機器映像 (「映像」) 提供在 Azure 虛擬機器服務中建立及部署虛擬機器所需的資訊。「映像」是由作業系統虛擬硬碟和零或多個資料磁碟虛擬硬碟組成。客戶可以從單一「映像」部署一些虛擬機器。|
|開發人員服務| 完全管理的服務，由資訊工作者、商業分析師、開發人員或 IT 專家用於客戶應用程式開發或系統管理。開發人員服務提供可讓客戶在 Azure 上快速部署雲端規模應程式的功能。客戶必須有 Azure 訂用帳戶才能購買開發人員服務。如「Microsoft Azure Marketplace 發行者合約」所述，發行者負責計量客戶的開發人員服務使用量以及向 Microsoft 報告使用量資訊。|
|解決方案範本|可參考一個或多個獨特供應項目的「Azure Resource Manager (ARM) 解決方案範本」，包括發行者發行的供應項目在內，讓 Azure 客戶能夠以單一、協調的方式部署一或多個供應項目。|

不同類型的解決方案之間有一些相同的步驟。本文提供簡短的概觀，讓您了解每種類型的解決方案必須完成哪些步驟。

## 1\.必要條件

> [AZURE.NOTE] 開始在 Azure Marketplace 上進行任何工作之前，必須取得預先核准。這不適用於資料服務發佈者。

1. [適用於 Microsoft Azure 認證的預先核准](marketplace-publishing-azure-certification.md)
2. [建立與註冊 Microsoft 開發人員帳戶](marketplace-publishing-accounts-creation-registration.md)
3. [滿足非技術性必要條件](marketplace-publishing-pre-requisites.md)

## 2\.發佈您的供應項目
### 2\.1 完成特定供應項目的技術性必要條件
- [VM 技術性必要條件](marketplace-publishing-vm-image-creation-prerequisites.md)
- [解決方案範本技術性必要條件](marketplace-publishing-solution-template-creation-prerequisites.md)

### 2\.2 建立您的供應項目
1. 使用下列的特定供應項目指南建立您的供應項目。
    - [建立您的 VM 供應項目](marketplace-publishing-vm-image-creation.md)
    - [建立您的解決方案範本供應項目](marketplace-publishing-solution-template-creation.md)
2. [建立您的供應項目行銷內容](marketplace-publishing-push-to-staging.md)

### 2\.3 在預備環境中測試您的供應項目
- [在預備環境中測試您的 VM 優惠](marketplace-publishing-vm-image-test-in-staging.md)
- [在預備環境中測試您的解決方案範本供應項目](marketplace-publishing-solution-template-test-in-staging.md)

### 2\.4 將您的供應項目部署至 Marketplace
- [將您的供應項目部署至 Azure Marketplace](marketplace-publishing-push-to-production.md)

### 虛擬機器映像專用 ###
- [建立內部部署 VM 映像](marketplace-publishing-vm-image-creation-on-premise.md)
- [在 Azure Preview 入口網站中建立執行 Windows 的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


- [疑難排解 Marketplace 中常見的發佈問題](marketplace-publishing-support-common-issues.md)
- 若要深入了解使用的入口網站，請參閱[您需要的入口網站](marketplace-publishing-portals.md)


## 3\.供應項目的後期發佈管理
- [虛擬機器優惠的後期製作指南](marketplace-publishing-vm-image-post-publishing.md)
- [如何更新優惠或 SKU 的非技術性詳細資料](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
- [如何從 Azure Marketplace 刪除供應項目或 SKU](marketplace-publishing-vm-image-post-publishing.md#4-how-to-delete-a-live-offer-or-sku-from-the-azure-marketplace)
- [如何變更雲端解決方案提供者轉售商獎勵](marketplace-publishing-csp-incentive.md)
- [了解賣方 Insights 報告](marketplace-publishing-report-seller-insights.md)
- [了解付款報告](marketplace-publishing-report-payout.md)
- [以發佈者身分取得支援](marketplace-publishing-get-publisher-support.md)

## 其他資源
- [設定 Azure PowerShell](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0706_2016-->