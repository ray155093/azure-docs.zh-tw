<properties
	pageTitle="管理用來建立 VM 的 Azure DevTest Labs 自訂映像 | Microsoft Azure"
	description="了解如何從 VHD 檔案或從 Azure DevTest Labs 的現有 VM 建立自訂映像"
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
	ms.date="09/07/2016"
	ms.author="tarcher"/>

# 管理用來建立 VM 的 Azure DevTest Labs 自訂映像

在 Azure DevTest Labs 中，自訂映像可讓您快速建立 VM，而不需等待在目標電腦上安裝所有必要的軟體。自訂映像可讓您在 VHD 檔案中預先安裝您需要的所有軟體，然後再使用該 VHD 檔案建立 VM。因為已安裝軟體，VM 建立的速度會更快。此外，您還能使用自訂映像來複製 VM，亦即從 VM 建立一個自訂映像，然後再從該自訂映像建立 VM。

在本文中，您將了解：

- [從 VHD 檔案建立自訂映像](#create-a-custom-image-from-a-vhd-file) ，以從該自訂映像建立 VM。
- [從 VM 建立自訂映像](#create-a-custom-image-from-a-vm)以快速複製 VM。

## 從 VHD 檔案建立自訂映像

在本節中，您將了解如何從 VHD 檔案建立自訂映像。您必須能夠存取有效的 VHD 檔案，才能執行本節的所有步驟。


1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。

1. 從實驗室清單中，選取所需的實驗室。

1. 在實驗室的刀鋒視窗上，選取 [組態]。

1. 在實驗室的 [組態] 刀鋒視窗上，選取 [自訂映像]。

1. 在 [自訂映像] 刀鋒視窗上，選取 [+ 自訂映像]。

    ![加入自訂映像](./media/devtest-lab-create-template/add-custom-image.png)

1. 輸入自訂映像的名稱。這個名稱會在建立 VM 時顯示於基本映像清單中。

1. 輸入自訂映像的描述。這個描述會在建立 VM 時顯示於基本映像清單中。

1. 選取 [VHD 檔案]。

1. 如果未列出您有權存取的 VHD 檔案，請依照[上傳 VHD 檔案](#upload-a-vhd-file)一節中的指示來新增該檔案，完成之後再回到這裡。

1. 選取所需的 VHD 檔案。

1. 選取 [確定]，以關閉 [VHD 檔案] 刀鋒視窗。

1. 選取 [作業系統組態]。

1. 在 [作業系統組態] 索引標籤上，選取 [Windows] 或 [Linux]。

1. 如果選取 [Windows]，請透過核取方塊來指定 *Sysprep* 是否已在電腦上執行。

1. 選取 [確定]，以關閉 [作業系統組態] 刀鋒視窗。

1. 選取 [確定]，以建立自訂映像。

1. 請移至[後續步驟](#next-steps)一節。

###上傳 VHD 檔案

若要加入自訂映像，您必須具有 VHD 檔案的存取權。

1. 在 [VHD 檔案] 刀鋒視窗中，選取 [使用 PowerShell 上傳 VHD 檔案]。

    ![上傳映像](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. 下一個刀鋒視窗將顯示用來修改和執行 PowerShell 指令碼的指示，該指令碼會將 VHD 檔案上傳到您的 Azure 訂用帳戶。**附註：**根據 VHD 檔案的大小與您的連接速度而定，這個程序的時間可能非常冗長。

## 從 VM 建立自訂映像
如果您有已設定的 VM，您可以從該 VM 建立自訂映像，之後再使用該自訂映像建立其他完全相同的 VM。下列步驟說明如何從 VM 建立自訂映像︰

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。

1. 從實驗室清單中，選取所需的實驗室。

1. 在實驗室的刀鋒視窗中，選取 [我的虛擬機器]。
 
1. 在 [我的虛擬機器] 刀鋒視窗中，選取要從中建立自訂映像的 VM。

1. 在 VM 的刀鋒視窗中，選取 [建立自訂映像 (VHD)]。

	![建立自訂映像的功能表項目](./media/devtest-lab-create-template/create-custom-image.png)

1. 在 [建立映像] 刀鋒視窗上，輸入自訂映像的名稱和描述。此資訊會在建立 VM 時顯示於基底清單中。

	![建立自訂映像刀鋒視窗](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. 選取 sysprep 是否在 VM 上執行。如果 sysprep 未在 VM 上執行，請指定您是否要在從這個自訂映像建立 VM 時讓 sysprep 執行。

1. 完成時選取 [確定]，以建立自訂映像。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 相關部落格文章

- [自訂映像或公式？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [在 Azure DevTest Labs 之間複製自訂映像](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##後續步驟

一旦新增要在建立 VM 時使用的自訂映像之後，下一個步驟就是[將 VM 加入實驗室](./devtest-lab-add-vm-with-artifacts.md)。

<!----HONumber=AcomDC_0907_2016-->