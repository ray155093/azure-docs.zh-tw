<properties
	pageTitle="每月估計成本趨勢 | Microsoft Azure"
	description="了解 DevTest Labs 每月估計成本趨勢圖表。"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# 每月估計成本趨勢

## 概觀

研測實驗室的成本管理功能可協助您追蹤實驗室成本。本文會示範如何使用「每月估計成本趨勢」圖表，來檢視目前日曆月份的到目前為止的估計成本，以及目前日曆月份的預計月底成本。

## 檢視每月估計成本趨勢圖表

若要檢視「每月估計成本趨勢」圖表，請依照下列步驟操作︰

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 選取 [瀏覽]，然後從清單中選取 [DevTest Labs]。

1. 從實驗室清單中，選取所需的實驗室。

1. 選取 [Settings] (設定)。

	![Settings](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. 在實驗室的 [設定] 刀鋒視窗的 [成本管理] 底下，選取 [臨界值]。

	![功能表](./media/devtest-lab-configure-cost-management/menu.png)
 
1. 下列螢幕擷取畫面顯示成本圖表範例。

    ![成本圖表](./media/devtest-lab-configure-cost-management/graph.png)

「估計成本」值是目前日曆月份到目前為止的估計成本，而「預計成本」則是整個目前日曆月份的預計成本 (使用前 5 天的實驗室成本計算得出)。
 
請注意，成本金額會無條件進位到下一個整數。例如：

- 5\.01 會無條件進位到 6 
- 5\.50 會無條件進位到 6
- 5\.99 會無條件進位到 6

如圖表上方所述，您在圖表中看到的成本是使用[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)優惠費率的「估計」成本。此外，以下「未」併入成本計算︰

- 目前不支援 CSP 和 Dreamspark 訂用帳戶，因為 DevTest Labs 使用 [Azure 計費 API](../billing-usage-rate-card-overview.md) 來計算實驗室成本，而 Azure 計費 API 並不支援的 CSP 或 Dreamspark 訂用帳戶。
- 您的優惠費率。目前，我們無法使用您與 Microsoft 或 Microsoft 合作夥伴協議的優惠費率 (顯示於您的訂用帳戶下方)。我們將使用隨用隨付費率。
- 您的稅率
- 您的折扣
- 您的帳單貨幣。目前，實驗室成本只會以美元貨幣顯示。

## 後續步驟

以下是接下來要嘗試的一些事項：

- [定義實驗室原則](./devtest-lab-set-lab-policy.md) - 了解如何設定用來管理您實驗室及其 VM 使用方式的各種原則。 
- [建立自訂映像](./devtest-lab-create-template.md) - 建立 VM 時，您需指定一個基本映像，這可以是自訂映像或 Marketplace 映像。本文會示範如何從 VHD 檔案建立自訂的映像。
- [設定 Marketplace 映像](./devtest-lab-configure-marketplace-images.md) - DevTest Labs 支援根據 Azure Marketplace 映像建立新的 VM。本文會示範在實驗室中建立新的 VM 時，如何指定可以使用哪些 Azure Marketplace 映像 (如果有的話)。
- [在實驗室中建立 VM](./devtest-lab-add-vm-with-artifacts.md) - 示範如何從基本映像 (自訂或 Marketplace) 建立新的 VM，以及如何在 VM 中與構件搭配運作。

<!---HONumber=AcomDC_0608_2016-->