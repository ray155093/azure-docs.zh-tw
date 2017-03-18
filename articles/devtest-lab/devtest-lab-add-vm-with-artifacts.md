---
title: "在 Azure DevTest Labs 中管理 VM 構件 | Microsoft Docs"
description: "了解如何在 Azure DevTest Labs 中管理 VM 構件"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 576509ce-6a33-4c26-87c7-de8b40271efa
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2d298c210fa46cc4473fc7e8809e55bfc102481d
ms.openlocfilehash: c970bca8f6ab9e97741370f74efc5ee66e592468
ms.lasthandoff: 02/27/2017


---
# <a name="manage-vm-artifacts-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中管理 VM 構件
Azure DevTest Labs「構件」可讓您指定在佈建 VM 時所執行的「動作」。 

構件動作可以執行諸如下列動作的程序：執行 Windows PowerShell 指令碼、執行 Bash 命令，以及安裝軟體。 

構件「參數」  可讓您自訂適用於特定案例的構件。

本文說明如何在實驗室中管理 VM 的構件。

## <a name="add-an-existing-artifact-to-a-vm"></a>將現有的構件加入至 VM
建立 VM 時，您可以加入現有的構件。 每個實驗室都會包括來自公用研發/測試實驗室構件儲存機制的構件，以及您已建立並加入至您自己之構件儲存機制的構件。
若要了解如何建立構件，請參閱 [了解如何撰寫您自己的構件以用於研發/測試實驗室](devtest-lab-artifact-author.md)文章。

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
1. 從實驗室清單中，選取您想要使用之 VM 所在的實驗室。  
1. 選取 [我的虛擬機器]。
1. 選取所需的 VM。
1. 選取 [構件]。 
1. 選取 [套用構件]。
1. 在 [套用構件] 刀鋒視窗中，選取您要新增到 VM 的構件。
1. 在 [新增構件] 刀鋒視窗中，輸入必要的參數值以及任何您所需的選用參數。  
1. 選取 [新增] 以新增構件，然後返回 [套用構件] 刀鋒視窗。
1. 視需要繼續為您的 VM 加入構件。
1. 加入構件之後，您可以 [變更構件的執行順序](#change-the-order-in-which-artifacts-are-run)。 您也可以返回來 [檢視或修改構件](#view-or-modify-an-artifact)。
1. 當您新增好構件時，選取 [套用]

## <a name="change-the-order-in-which-artifacts-are-run"></a>變更構件的執行順序
根據預設，構件的動作是依照它們加入 VM 的順序來執行。 下列步驟說明如何變更構件的執行順序。

1. 在 [套用構件] 刀鋒視窗頂端，選取會指出已新增至 VM 之構件數目的連結。
   
    ![新增至 VM 的構件數目](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在 [選取的構件] 刀鋒視窗中，將構件拖放到所需的順序。 **附註︰**如果您在拖曳構件時發生問題，請確定您是從構件左側進行拖曳。 
1. 完成時選取 [確定]  。  

## <a name="view-or-modify-an-artifact"></a>檢視或修改構件
下列步驟說明如何檢視或修改構件的參數︰

1. 在 [套用構件] 刀鋒視窗頂端，選取會指出已新增至 VM 之構件數目的連結。
   
    ![新增至 VM 的構件數目](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在 [選取的構件] 刀鋒視窗中，選取您想要檢視或編輯的構件。  
1. 在 [新增構件] 刀鋒視窗中，進行任何所需的變更，然後選取 [確定] 以關閉 [新增構件] 刀鋒視窗 。
1. 選取 [確定] 以關閉 [選取的構件] 刀鋒視窗。

## <a name="save-azure-resource-manager-template"></a>儲存 Azure Resource Manager 範本
Azure Resource Manager 範本提供宣告式方法來定義可重複的部署。 下列步驟說明如何為建立的 VM 儲存 Azure Resource Manager 範本。
儲存之後，您便可以使用 Azure Resource Manager 範本[透過 Azure PowerShell 部署新的 VM](../azure-resource-manager/resource-group-overview.md#template-deployment)。

1. 在 [虛擬機器] 刀鋒視窗上，選取 [檢視 ARM 範本]。
2. 在 [檢視 Azure Resource Manager 範本] 刀鋒視窗中，選取範本文字。
3. 將選取的文字複製到剪貼簿。
4. 選取 [確定] 以關閉 [檢視 Azure Resource Manager 範本] 刀鋒視窗。
5. 開啟文字編輯器。
6. 從剪貼簿貼上範本文字。
7. 儲存檔案供稍後使用。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>後續步驟
* 了解如何 [為您的 DevTest Labs VM 建立自訂構件](devtest-lab-artifact-author.md)。

