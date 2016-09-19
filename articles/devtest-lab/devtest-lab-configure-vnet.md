<properties
	pageTitle="設定 Azure DevTest Labs 中的虛擬網路 | Microsoft Azure"
	description="了解如何設定現有的虛擬網路和子網路，並在具備 Azure DevTest Labs 的 VM 中使用它們"
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
	ms.date="09/06/2016"
	ms.author="tarcher"/>

# 設定 Azure DevTest Labs 中的虛擬網路

如[將具有構件的 VM 加入實驗室](devtest-lab-add-vm-with-artifacts.md)文章中所述，當您在實驗室中建立 VM 時，可以指定已設定的虛擬網路。例如，如果您需要使用以 ExpressRoute 或站台對站台 VPN 設定的虛擬網路從您的 VM 存取公司資源時，就可以這麼做。下列各節將說明如何將現有的虛擬網路加入至實驗室的虛擬網路設定，就能在建立 VM 時選擇它。

## 使用 Azure 入口網站設定適用於實驗室的虛擬網路
下列步驟會逐步引導您將現有虛擬網路 (和子網路) 加入至實驗室，以便在同一個實驗室中建立 VM 時加以使用。

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。

1. 從實驗室清單中，選取所需的實驗室。

1. 在實驗室的刀鋒視窗上，選取 [組態]。

1. 在實驗室的 [組態] 刀鋒視窗上，選取 [虛擬網路]。

1. 在 [虛擬網路] 刀鋒視窗中，您會看到您已針對目前的實驗室設定的虛擬網路清單，以及為實驗室所建立的預設虛擬網路。

1. 選取 [+ 新增]。

	![將現有的虛擬網路加入至您的實驗室](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
	
1. 在 [虛擬網路] 刀鋒視窗中，選取 [選取虛擬網路]。

	![選取現有的虛擬網路](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
	
1. 在 [選擇虛擬網路] 刀鋒視窗中，選取所需的虛擬網路。刀鋒視窗會顯示訂用帳戶中與實驗室位於相同區域下方的所有虛擬網路。

1. 選取虛擬網路之後，您會回到 [虛擬網路] 刀鋒視窗，有數個欄位是啟用狀態。

	![選取現有的虛擬網路](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)

1. 指定虛擬網路 / 實驗室組合的描述。

1. 若要允許在實驗室 VM 建立期間使用子網路，請選取 [在虛擬機器建立時使用] 選項。

1. 若要允許子網路中的公用 IP 位址，請選取 [允許公用 IP]。

1. 在 [每位使用者的最大虛擬機器數] 欄位中，針對每個子網路指定每位使用者的最大 VM 數。如果您想要不限數目的 VM 數，請將此欄位保留空白。

1. 選取 [**儲存**]。

1. 現在已設定虛擬網路，在建立 VM 時就能選取它。若要查看如何建立 VM 並指定虛擬網路，請參閱[將具有構件的 VM 加入實驗室](devtest-lab-add-vm-with-artifacts.md)一文。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 後續步驟

一旦您在實驗室中加入所需的虛擬網路之後，下一個步驟就是[將 VM 加入至實驗室](devtest-lab-add-vm-with-artifacts.md)。

<!---HONumber=AcomDC_0907_2016-->