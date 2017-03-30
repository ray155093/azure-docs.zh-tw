---
title: "在 Azure DevTest Labs 中建立公式 | Microsoft Docs"
description: "了解如何建立 Azure DevTest Labs 公式和使用它們來建立新的 VM。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 4f9f86c2cc106aa96f27293a43cd784e2fb6fe76
ms.lasthandoff: 03/17/2017


---
# <a name="create-azure-devtest-labs-formulas"></a>建立 Azure DevTest Labs 公式

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

本文說明如何從基礎 (自訂映像、Marketplace 映像或另一個公式) 或現有的 VM 建立公式。 

## <a name="create-a-formula"></a>建立公式
具有 DevTest Labs「使用者」  權限的所有使用者，都可以使用公式作為基底來建立 VM。 有兩種方式可以建立公式： 

* 從基底開始 - 在您想要定義公式的所有特性時使用。
* 從現有實驗室 VM - 在您想要根據現有 VM 的設定建立公式時使用。

如需有關新增使用者和權限的詳細資訊，請參閱[在 Azure DevTest Labs 中新增擁有者和使用者](./devtest-lab-add-devtest-user.md)。

### <a name="create-a-formula-from-a-base"></a>從基底建立公式
下列步驟會引導您完成從自訂映像、Marketplace 映像或其他公式建立公式的程序。

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。

3. 從實驗室清單中，選取所需的實驗室。  

4. 在實驗室的刀鋒視窗上，選取 [公式 (可重複使用的基底)] 。
   
    ![公式功能表](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. 在 [公式] 刀鋒視窗上，選取 [+新增]。
   
    ![加入公式](./media/devtest-lab-create-formulas/add-formula.png)

6. 在 [選擇基底]  刀鋒視窗上，選取您想要用來建立公式的基底 (自訂映像、Marketplace 映像或公式)。
   
    ![基本清單](./media/devtest-lab-create-formulas/base-list.png)

7. 在 [建立公式]  刀鋒視窗上，指定下列值：
   
    * **公式名稱** - 輸入公式的名稱。 當您建立 VM 時，這個值會在顯示在基本映像清單中。 輸入名稱時會立即驗證，如果無效，則會出現訊息指出有效名稱的需求。
    * **描述** - 輸入公式的有意義描述。 當您建立 VM 時，可以從公式的操作功能表使用這個值。
    * **使用者名稱** - 輸入被授與系統管理員權限的使用者名稱。
    *  - 從下拉式清單中輸入或選取一個值，該值與您想用於指定使用者的密碼相關聯。 如需密碼的詳細資訊，請參閱 [Azure DevTest Labs︰個人密碼存放區](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/)。
    * **虛擬機器磁碟類型** - 指定 HDD (硬碟) 或 SSD (固態硬碟)，以表示使用此基本映像佈建的虛擬機器可使用哪種儲存磁碟類型。
    * **虛擬機器大小** - 選取其中一個預先定義的項目，以指定要建立之 VM 的處理器核心、RAM 大小和硬碟大小。 
    * **構件** - 選取以開啟 [新增構件] 刀鋒視窗，以選取並設定您想要新增至基本映像的構件。 如需構件的詳細資訊，請參閱[在 Azure DevTest Labs 中管理 VM 構件](./devtest-lab-add-vm-with-artifacts.md)。
    * **進階設定** - 選取以開啟 [進階] 刀鋒視窗，以進行下列設定︰
        * **虛擬網路** - 指定所需的虛擬網路。
        * **子網路** - 指定所需的子網路。    
        * **IP 位址組態** - 指定您想要公用、私人或共用 IP 位址。 如需共用 IP 位址的詳細資訊，請參閱[了解 Azure DevTest Labs 中的共用 IP 位址](./devtest-lab-shared-ip.md)。
        * **允許宣告此機器** -「允許宣告」機器表示建立機器時不會指派擁有權。 實驗室使用者將能夠在實驗室的刀鋒視窗中，取得 (「宣告」) 機器的擁有權。     
    * **映像** - 這個欄位會顯示您在前一個刀鋒視窗上選取的基本映像名稱。 
     
       ![建立公式](./media/devtest-lab-create-formulas/create-formula.png)

8. 選取 [建立]  以建立公式。

9. 公式建立之後會顯示在 [公式] 刀鋒視窗的清單中。

### <a name="create-a-formula-from-a-vm"></a>從 VM 建立公式
下列步驟會引導您完成根據現有 VM 建立公式的程序。 

> [!NOTE]
> VM 必須是在 2016 年 3 月 30 日以後建立的，才能從該 VM 建立公式。 
> 
> 

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。  
4. 在實驗室的 [概觀]  刀鋒視窗中，選取要從中建立公式的 VM。
   
    ![實驗室 VM](./media/devtest-lab-create-formulas/my-vms.png)
5. 在 VM 的刀鋒視窗上，選取 [建立公式 (可重複使用的基底)] 。
   
    ![建立公式](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. 在 [建立公式] 刀鋒視窗上，輸入新公式的 [名稱] 和 [描述]。
   
    ![建立公式刀鋒視窗](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. 選取 [確定]  以建立公式。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相關部落格文章
* [自訂映像或公式？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>後續步驟
- [在 Azure DevTest Labs 中將 VM 新增至實驗室](devtest-lab-add-vm.md)
- [管理 Azure DevTest Labs 公式](devtest-lab-manage-formulas.md)
