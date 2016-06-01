<properties
	pageTitle="設定成本管理 | Microsoft Azure"
	description="了解如何設定您的研發/測試實驗室成本管理功能。"
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
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# 設定成本管理

## 概觀

研測實驗室的成本管理功能可協助您追蹤實驗室成本。本文會示範如何使用「每月估計成本趨勢」圖表，來檢視目前日曆月份的到目前為止的估計成本，以及目前日曆月份的預計月底成本。

## 啟用每月估計成本趨勢圖表

若要啟用每月估計成本趨勢圖表，請遵循下列步驟︰

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 點選 [設定]。

	![Settings](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. 在實驗室的 [設定] 刀鋒視窗的 [Cost Policies]\(成本原則) 下，點選 [Cost Thresholds]\(成本臨界值)。

	![功能表](./media/devtest-lab-configure-cost-management/menu.png)
 
1. 在 [Cost Thresholds]\(成本臨界值) 刀鋒視窗上，點選 [開啟] 啟用此功能，點選 [關閉] 則予以停用。

1. 點選 [儲存]。

啟用此功能之後，可能需要數小時的時間，圖表才會顯示您的預估和預計成本。這是因為服務每個小時都會執行一次來收集此資訊，但會在收集即時資料後方會執行數小時。例如，假設您在凌晨 1:00 啟動 VM。與該 VM 相關聯的成本可能會有數小時不會併入成本圖表中。
 
下列螢幕擷取畫面顯示成本圖表範例。

![成本圖表](./media/devtest-lab-configure-cost-management/graph.png)

[估計成本] 值是目前日曆月份的到目前為止的估計成本，而 [預計成本] 是整個目前日曆月份的預計成本。

如圖表上方所述，您在圖表中看到的成本是使用[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)優惠費率的「估計」成本。此外，以下「未」併入成本計算︰

- 您的優惠費率。目前，我們無法使用您與 Microsoft 或 Microsoft 合作夥伴協議的優惠費率 (顯示於您的訂用帳戶下方)。我們將使用隨用隨付費率。
- 您的稅率
- 您的折扣
- 您的帳單貨幣。目前，實驗室成本只會以美元貨幣顯示。

## 後續步驟

以下是接下來要嘗試的一些事項：

- [定義實驗室原則](./devtest-lab-set-lab-policy.md) - 了解如何設定用來管理您實驗室和其 VM 使用方式的各種原則。 
- [建立自訂映像](./devtest-lab-create-template.md) - 當您建立 VM 時，您要指定一個基本映像，其可以是自訂映像或 Marketplace 映像。本文會示範如何從 VHD 檔案建立自訂的映像。
- [設定 Marketplace 映像](./devtest-lab-configure-marketplace-images.md) - 研測實驗室支援根據 Azure Marketplace 映像建立新的 VM。本文會示範在實驗室中建立新的 VM 時，如何指定可以使用哪些 Azure Marketplace 映像 (如果有的話)。
- [在實驗室中建立 VM](./devtest-lab-add-vm-with-artifacts.md) - 示範如何從基本映像 (自訂或 Marketplace) 建立新的 VM，以及如何使用 VM 中的構件。

<!---HONumber=AcomDC_0518_2016-->