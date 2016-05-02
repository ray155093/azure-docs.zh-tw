<properties
	pageTitle="定義實驗室原則 |Microsoft Azure"
	description="了解如何定義實驗室原則，例如 VM 大小、每位使用者的 VM 數目上限，以及自動關機。"
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
	ms.date="04/13/2016"
	ms.author="tarcher"/>

# 定義實驗室原則

> [AZURE.NOTE] 按一下下列連結可檢視本文隨附的影片：[如何撰寫自訂構件](/documentation/videos/how-to-set-vm-policies-in-a-devtest-lab)

## 概觀

研發/測試實驗室可讓您指定重要的原則，以控管使用您的實驗室及其 VM 的方式。例如，您可以設定建立 VM 所允許的 VM 大小規則、可以建立的 VM 數目臨界值，以及自動啟動/停止實驗室 VM 的排程工作。

## 存取實驗室的原則

若要檢視 (及變更) 實驗室的原則，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 點選 [設定]。

	![設定](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. 在 [設定] 刀鋒視窗上，有一組設定名為 [VM Policies]\(VM 原則)。

	![Settings](./media/devtest-lab-set-lab-policy/policies.png)

	從下列清單中點選所需的原則，深入了解設定它的相關資訊：

	- [允許的 VM 大小](#set-allowed-vm-sizes) - 選取實驗室中允許的 VM 大小清單。使用者只能從此清單中建立 VM。

	- [每位使用者的 VM 數目上限](#set-maximum-vms-per-user) - 指定實驗室可以建立的 VM 數目上限，以及一位使用者可以建立的 VM 數目上限。

	- [允許的 VM 總數](#set-total-vms-allowed) - 指定實驗室可以建立的 VM 數目上限，以及一位使用者可以建立的 VM 數目上限。

	- [自動關機](#set-auto-shutdown) - 指定目前實驗室的 VM 必須關機的時間。

	- [自動啟動](#set-auto-start) - 指定目前實驗室 VM 必須關機的時間。

## 設定允許的 VM 大小

設定允許的 VM 大小的原則有助於將實驗室的成本浪費降至最低，方式是讓您指定實驗室中允許的 VM 大小。如果啟用此原則，就只能使用此清單中的 VM 大小來建立 VM。

1. 在實驗室的 [設定] 刀鋒視窗的 [VM Policies]\(VM 原則) 下，點選 [Allowed VM Sizes]\(允許的 VM 大小)。

	![Settings](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
 
1. 點選 [開啟] 來啟用此原則，以及點選 [關閉] 來停用它。

1. 如果啟用這個原則，請點選一或多個可在實驗室中建立的 VM 大小。

1. 點選 [儲存]。

## 設定每位使用者的 VM 數目上限

**每位使用者的 VM 數目上限**原則可讓您指定個別使用者可以建立的 VM 數目上限。達到使用者限制時，如果使用者嘗試建立新的 VM，就會顯示錯誤訊息，指出無法建立 VM。

1. 在實驗室的 [設定] 刀鋒視窗的 [VM Policies]\(VM 原則) 下，點選 [Maximum VMs per user]\(每位使用者的 VM 數目上限)。

	![Settings](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. 點選 [開啟] 來啟用此原則，以及點選 [關閉] 來停用它。

1. 如果啟用這個原則，可在 [Maximum VMs allowed per User]\(每位使用者所允許的 VM 數目上限) 文字方塊中輸入數值，指出使用者可建立的 VM 數目上限。如果您輸入的數字無效，UI 將會顯示此欄位允許的最大數目。

1. 點選 [儲存]。

## 設定允許的 VM 總數

**允許的 VM 總數**原則可讓您指定目前實驗室可以建立的 VM 數目上限。達到實驗室限制時，如果使用者嘗試建立新的 VM，就會顯示錯誤訊息，指出無法建立 VM。

1. 在實驗室的 [設定] 刀鋒視窗的 [VM Policies]\(VM 原則) 下，點選 [Total VMs allowed]\(允許的 VM 總數)。

	![Settings](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)

1. 點選 [開啟] 來啟用此原則，以及點選 [關閉] 來停用它。

1. 如果啟用這個原則，可在 [Total VMs allowed in this lab]\(這個實驗室允許的 VM 總數) 文字方塊中輸入數值，指出目前實驗室可以建立的 VM 數目上限。如果您輸入的數字無效，UI 將會顯示此欄位允許的最大數目。

1. 點選 [儲存]。

## 設定自動關機

自動關機原則有助於將實驗室的成本浪費降至最低，方式是讓您指定這個實驗室中 VM 的關機時間。

1. 在實驗室的 [設定] 刀鋒視窗的 [VM Policies]\(VM 原則) 下，點選 [Auto shutdown]\(自動關機)。

	![Settings](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. 點選 [開啟] 來啟用此原則，以及點選 [關閉] 來停用它。

1. 如果啟用這個原則，您可指定目前實驗室中所有 VM 關機的當地時間。

1. 點選 [儲存]。

1. 根據預設，這個原則一經啟用，就會套用到目前實驗室的所有 VM。若要移除特定 VM 的這項設定，請開啟 VM 的刀鋒視窗並變更其 [Auto Shutdown]\(自動關機) 設定。

## 設定自動啟動

自動啟動原則可讓您指定目前實驗室 VM 應該啟動的時間。

1. 在實驗室的 [設定] 刀鋒視窗的 [VM Policies]\(VM 原則) 下，點選 [自動啟動]。

	![Settings](./media/devtest-lab-set-lab-policy/auto-start.png)

1. 點選 [開啟] 來啟用此原則，以及點選 [關閉] 來停用它。

1. 如果啟用這個原則，您可指定適用的本機排程啟動時間和星期幾。

1. 點選 [儲存]。

1. 這個原則一經啟用，就不會自動套用到目前實驗室中的任何 VM。若要將這項設定套用至特定的 VM，請開啟 VM 的刀鋒視窗並變更其 [自動啟動] 設定。

## 後續步驟

實驗室一旦定義並套用了各種 VM 原則設定，接下來就要嘗試一些作業：

- [建立自訂映像](./devtest-lab-create-template.md) - 當您建立 VM 時，您要指定一個基本映像，它可以是自訂映像或 Marketplace 映像。本文會示範如何從 VHD 檔案建立自訂的映像。
- [設定 Marketplace 映像](./devtest-lab-configure-marketplace-images.md) - 研測實驗室支援根據 Azure Marketplace 映像建立新的 VM。本文會示範在實驗室中建立新的 VM 時，如何指定可以使用哪些 Azure Marketplace 映像 (如果有的話)。
- [在實驗室中建立 VM](./devtest-lab-add-vm-with-artifacts.md) - 這篇文章會示範如何從基本映像 (自訂或 Marketplace) 建立新的 VM，以及如何使用 VM 中的構件。

<!---HONumber=AcomDC_0420_2016-->