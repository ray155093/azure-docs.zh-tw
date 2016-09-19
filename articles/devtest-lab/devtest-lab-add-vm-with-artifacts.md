<properties
	pageTitle="將具有構件的 VM 加入 Azure DevTest Labs 中的實驗室 | Microsoft Azure"
	description="了解如何將具有構件的 VM 加入 Azure DevTest Labs"
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
	ms.date="08/30/2016"
	ms.author="tarcher"/>

# 將具有構件的 VM 加入 Azure DevTest Labs 中的實驗室

> [AZURE.VIDEO how-to-create-vms-with-artifacts-in-a-devtest-lab]

您會在實驗室中，從一個「基底」([自訂映像](./devtest-lab-create-template.md)、[公式](./devtest-lab-manage-formulas.md)或 [Marketplace 映像](./devtest-lab-configure-marketplace-images.md)) 建立 VM。

研發/測試實驗室的「構件」可讓您指定在建立 VM 時所執行的「動作」。

構件動作可以執行諸如下列動作的程序：執行 Windows PowerShell 指令碼、執行 Bash 命令，以及安裝軟體。

構件「參數」可讓您自訂適用於特定案例的構件。

本文將示範如何使用構件，在實驗室中建立 VM。

## 加入具有構件的 VM

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。

1. 從實驗室清單中，選取您想要在其中建立 VM 的實驗室。

1. 在實驗室的 [概觀] 刀鋒視窗中，選取 [+ 虛擬機器]。![加入 VM 按鈕](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. 在 [選擇基底] 刀鋒視窗中，選取 VM 的基底。

1. 在 [虛擬機器] 刀鋒視窗的 [虛擬機器名稱] 文字方塊中，輸入新虛擬機器的名稱。

	![實驗室 VM 刀鋒視窗](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)

1. 輸入**使用者名稱**，此名稱將被授與虛擬機器上的系統管理員權限。

1. 如果您想使用儲存在「密碼存放區」的密碼，請選取 [使用密碼存放區中的密碼]，並指定對應您的密碼的金鑰值。否則，請在標示為 [輸入值] 的文字欄位中輸入密碼。
 
1. 選取 [虛擬機器大小]，然後選取其中一個預先定義的項目，這些項目可以指定處理器核心、RAM 大小，以及要建立的 VM 的硬碟大小。

1. 選取 [虛擬網路]，然後選取所需的虛擬網路。

1. 選取 [子網路]，然後選取子網路。

1. 如果將實驗室原則設定為允許所選子網路的公用 IP 位址，請選取 [是] 或 [否]，來指定您是否想要讓 IP 位址成為公用的位址。否則，這個選項會停用並選取為 [否]。

1. 選取 [構件]，從構件清單中選取並設定您想要加入至基本映像的構件。**附註：**如果您剛接觸到研發/測試實驗室或設定構件，請先跳到[將現有的構件加入至 VM](#add-an-existing-artifact-to-a-vm) 一節，完成該節之後再返回此處。

1. 如果您想要檢視或複製 Azure Resource Manager 範本，請跳至[儲存 Azure Resource Manager 範本](#save-arm-template)一節，然後在完成時回到這裡。

1. 選取 [建立]，將指定的 VM 加入實驗室。

1. [實驗室] 刀鋒視窗會顯示 VM 的建立狀態，其狀態會先是 [正在建立]，在啟動該 VM 之後才會變成 [正在執行]。

1. 請移至[後續步驟](#next-steps)一節。

## 將現有的構件加入至 VM

建立 VM 時，您可以加入現有的構件。每個實驗室都會包括來自公用研發/測試實驗室構件儲存機制的構件，以及您已建立並加入至您自己之構件儲存機制的構件。若要了解如何建立構件，請參閱[了解如何撰寫您自己的構件以用於研發/測試實驗室](devtest-lab-artifact-author.md)文章。

1. 在 [虛擬機器] 刀鋒視窗中，選取 [構件]。

1. 在 [加入構件] 刀鋒視窗中，選取所需的構件。

	![加入構件刀鋒視窗](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. 輸入必要的參數值以及任何您所需的選用參數。

1. 選取 [加入] 以加入構件，並返回 [加入構件] 刀鋒視窗。

1. 視需要繼續為您的 VM 加入構件。

1. 加入構件之後，您可以[變更構件的執行順序](#change-the-order-in-which-artifacts-are-run)。您也可以返回來[檢視或修改構件](#view-or-modify-an-artifact)。

## 變更構件的執行順序

根據預設，構件的動作是依照它們加入 VM 的順序來執行。下列步驟說明如何變更構件的執行順序。

1. 在 [加入構件] 刀鋒視窗頂端，選取會指出已加入 VM 的構件數目的連結。

    ![新增至 VM 的構件數目](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. 若要指定構件執行的順序，請將構件拖放到預期的順序。**附註︰**如果您在拖曳構件時發生問題，請確定您是從構件左側進行拖曳。

1. 完成時選取 [確定]。

## 檢視或修改構件

下列步驟說明如何檢視或修改構件的參數︰

1. 在 [加入構件] 刀鋒視窗頂端，選取會指出已加入 VM 的構件數目的連結。

    ![新增至 VM 的構件數目](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. 在 [選取的構件] 刀鋒視窗中，選取您想要檢視或編輯的構件。

1. 在 [加入構件] 刀鋒視窗中，進行任何所需的變更，然後選取 [確定] 以關閉 [加入構件] 刀鋒視窗 。

1. 選取 [確定] 以關閉 [選取的構件] 刀鋒視窗。

## 儲存 Azure Resource Manager 範本

Azure Resource Manager 範本提供宣告式方法來定義可重複的部署。下列步驟說明如何為建立的 VM 儲存 Azure Resource Manager 範本。儲存之後，您可以使用 Azure Resource Manager 範本[透過 Azure PowerShell 部署新的 VM](../resource-group-overview.md#template-deployment)。

1. 在 [虛擬機器] 刀鋒視窗中，選取 [檢視 ARM 範本]。

1. 在 [檢視 Azure Resource Manager 範本] 刀鋒視窗中，選取範本文字。

1. 將選取的文字複製到剪貼簿。

1. 選取 [確定] 以關閉 [檢視 Azure Resource Manager 範本] 刀鋒視窗。

1. 開啟文字編輯器。

1. 從剪貼簿貼上範本文字。

1. 儲存檔案供稍後使用。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 後續步驟

- 一旦建立 VM 之後，您可以選取 VM 刀鋒視窗上的 [連接] 來連接至 VM。
- 了解如何[為您的 DevTest Labs VM 建立自訂構件](devtest-lab-artifact-author.md)。
- 瀏覽 [DevTest Labs ARM 快速入門範本資源庫](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)

<!----HONumber=AcomDC_0907_2016-->