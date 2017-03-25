---
title: "管理 Azure DevTest Labs 中的公式來建立 VM | Microsoft Docs"
description: "了解如何更新和移除 Azure DevTest Labs 公式"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fd334dc8cd22990456888146f41389054f2e8b29
ms.lasthandoff: 03/15/2017


---
# <a name="manage-azure-devtest-labs-formulas"></a>管理 Azure DevTest Labs 公式

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

[建立 Azure DevTest Labs 公式](devtest-lab-create-formulas.md#create-a-formula)一文逐步引導您完成在 Azure DevTest Labs 中建立公式的程序。 有公式之後，本文會逐步引導您管理公式。

## <a name="modify-a-formula"></a>修改公式
若要修改公式，請遵循下列步驟︰

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。  
4. 在實驗室的刀鋒視窗上，選取 [公式 (可重複使用的基底)] 。
   
    ![公式功能表](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在 [Lab formulas] \(實驗室公式)  刀鋒視窗上，選取您想要修改的公式。
6. 在 [更新公式] 刀鋒視窗上，進行所需的編輯，然後選取 [更新]。

## <a name="delete-a-formula"></a>刪除公式
若要刪除公式，請遵循下列步驟︰

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。  
4. 在實驗室的 [設定] 刀鋒視窗上，選取 [公式]。
   
    ![公式功能表](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在 [Lab formulas] \(實驗室公式)  刀鋒視窗上，選取您想要刪除之公式右邊的省略符號。
   
    ![公式功能表](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 在公式的操作功能表上，選取 [刪除] 。
   
    ![公式操作功能表](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 在刪除確認對話方塊上，選取 [是]  。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相關部落格文章
* [自訂映像或公式？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>後續步驟
建立要在建立 VM 時使用的公式之後，下一個步驟就是[將 VM 新增實驗室](devtest-lab-add-vm-with-artifacts.md)。


