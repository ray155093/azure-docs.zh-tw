<properties
	pageTitle="管理 DevTest Labs 公式來建立 VM | Microsoft Azure"
	description="了解如何建立、更新和移除研測實驗室公式，以及如何加以使用來建立新的 VM。"
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

# 管理研發/測試實驗室公式來建立 VM

## 概觀

公式 (如[自訂映像](./devtest-lab-create-template.md)和 [Marketplace 映像](./devtest-lab-configure-marketplace-images.md)) 提供進行快速 VM 佈建的機制。研發/測試實驗室中的公式是用來建立實驗室 VM 的預設屬性值清單。透過公式建立 VM 時，可以依現況使用預設值，或修改預設值。

在本文中，您將了解如何執行下列工作：

- [建立新的公式](#create-a-new-formula)
- [使用公式建立新的 VM](#use-a-formula-to-create-a-new-vm)
- [修改公式](#modify-a-formula)
- [刪除公式](#delete-a-formula)

> [AZURE.NOTE] 公式與[自訂映像](./devtest-lab-create-template.md)的相似處，在於每個都可讓您透過用來佈建 VM 的 VHD 建立基本映像。若要協助決定哪一個適用於您的特定環境，請參閱文章：[比較研發/測試實驗室中的自訂映像和公式](./devtest-lab-comparing-vm-base-image-types.md)。

## 建立新的公式
具有研發/測試實驗室「使用者」權限的所有使用者都可以使用公式作為基礎以在實驗室中建立 VM。有兩種方式可以建立公式：

- 從頭 - 在您想要從頭定義公式的所有特性時使用。
- 從現有實驗室 VM - 在您想要根據現有 VM 的設定建立公式時使用。

### 從頭建立新的公式
下列步驟會引導您完成從頭建立新公式的程序。

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 所選實驗室的 [設定] 刀鋒視窗隨即顯示。

1. 在實驗室的 [設定] 刀鋒視窗上，點選 [公式]。

    ![公式功能表](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. 在 [Lab formulas]\(實驗室公式) 刀鋒視窗上，點選 [+ 加入]。

    ![加入新的公式](./media/devtest-lab-manage-formulas/add-formula.png)

1. 在 [選擇基底] 刀鋒視窗上，點選您想要用來建立公式的 [自訂映像] 或 [Marketplace 映像]。

    ![基本清單](./media/devtest-lab-manage-formulas/base-list.png)

1. 在 [建立公式] 刀鋒視窗上，指定下列值：

	- **公式名稱** - 輸入公式的名稱。這個值將會在建立 VM 時顯示於基本映像清單中。名稱會在您加以輸入時進行驗證，如果無效，則會出現訊息表示有效名稱的需求。
	- **描述** - 輸入公式的有意義描述。當您建立 VM 時，可以從公式的操作功能表使用這個值。
	- **映像** - 這個欄位會顯示您在前一個刀鋒視窗上選取的基本映像名稱。 
	- **VM 大小** - 點選其中一個預先定義的項目，這些項目可以指定處理器核心、RAM 大小，以及所要建立 VM 的硬碟大小。
	- **虛擬網路** - 點選並選取所需的虛擬網路。
	- **子網路** - 點選並選取所需的子網路。
	- **公用 IP 位址** - 如果將實驗室原則設定為允許所選子網路的公用 IP 位址，請選取 [是] 或 [否]，來指定您是否想要讓 IP 位址成為公用的位址。否則，這個選項會停用並選取為 [否]。
	- **構件** - 點選並從構件清單中選取和設定您想要加入基本映像中的構件。請注意，不會顯示作為安全字串的構件參數，因為公式不會儲存任何安全字串值。 

    	![建立公式](./media/devtest-lab-manage-formulas/create-formula.png)

1. 點選 [建立] 建立公式。成功建立公式之後，該公式會列在 [Lab formulas]\(實驗室公式) 刀鋒視窗上。

	![新建的公式](./media/devtest-lab-manage-formulas/newly-created-formula.png)

### 從實驗室 VM 建立新的公式
下列步驟會引導您完成根據現有 VM 建立公式的程序。

> [AZURE.NOTE] 只有 2016 年 3 月 30 日之後建立的 VM 才支援從實驗室 VM 建立新的公式。

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 在實驗室刀鋒視窗上，找出標題為 [我的 VM] 的區段，然後按一下您想要用來建立新公式的 VM。

	![實驗室 VM](./media/devtest-lab-manage-formulas/my-vms.png)

1. 在 VM 的 [設定] 刀鋒視窗上，點選 [建立公式]。

	![建立公式](./media/devtest-lab-manage-formulas/create-formula-menu.png)

1. 在 [建立公式] 刀鋒視窗上，輸入新公式的 [名稱] 和 [描述]，或點選 [確定]。成功建立公式之後，該公式會列在 [Lab formulas]\(實驗室公式) 刀鋒視窗上。

	![建立公式刀鋒視窗](./media/devtest-lab-manage-formulas/create-formula-blade.png)

## 修改公式
若要修改公式，請遵循下列步驟︰

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 在實驗室的 [設定] 刀鋒視窗上，點選 [公式]。

    ![公式功能表](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. 在 [Lab formulas]\(實驗室公式) 刀鋒視窗上，點選您想要修改的公式。

1. 在 [Update formula]\(更新公式) 刀鋒視窗上，進行所需的編輯，然後點選 [更新]。

## 刪除公式 
若要刪除公式，請遵循下列步驟︰

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 在實驗室的 [設定] 刀鋒視窗上，點選 [公式]。

    ![公式功能表](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. 在 [Lab formulas]\(實驗室公式) 刀鋒視窗上，按一下您想要刪除之公式右邊的省略符號。

    ![公式功能表](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)

1. 在公式的操作功能表上，選取 [刪除]。

    ![公式操作功能表](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)

1. 在刪除確認對話方塊上點選 [是]。

    ![公式操作功能表](./media/devtest-lab-manage-formulas/formula-delete-confirmation.png)

## 後續步驟
建立要在建立 VM 時使用的公式之後，下一個步驟就是[將 VM 加入實驗室](./devtest-lab-add-vm-with-artifacts.md)。

<!---HONumber=AcomDC_0518_2016-->