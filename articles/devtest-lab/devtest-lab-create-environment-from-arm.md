---
title: "使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源 | Microsoft Docs"
description: "了解如何從 Azure Resource Manager 範本在 Azure DevTest Labs 中建立多個 VM 環境和 PaaS 資源"
services: devtest-lab,virtual-machines,visual-studio-online
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
ms.date: 01/31/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 0b402602ed80d9eef5313fb29ba2bd05644f11f8
ms.contentlocale: zh-tw
ms.lasthandoff: 05/15/2017


---

# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源

[Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)可讓您輕鬆地[建立，並將 VM 新增至實驗室](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-add-vm)。 這適用於一次建立一個 VM。 不過，如果環境包含多個 VM，則每個 VM 都必須分別建立。 針對多層式 Web 應用程式或 SharePoint 伺服器陣列的情況，需要有機制以允許在單一步驟中建立多個 VM。 使用 Azure Resource Manager 範本，您現在可以定義 Azure 方案的基礎結構和組態，並在一致的狀態中重複部署多個 VM。 此功能可提供下列優點：

- Azure Resource Manager 範本會直接從您的原始檔控制儲存機制 (GitHub 或 Team Services Git) 載入。
- 一旦設定後，您的使用者可以建立環境，方法為從 Azure 入口網站選取 Azure Resource Manager 範本，做為它們可以使用其他類型的 [VM 基底](./devtest-lab-comparing-vm-base-image-types.md)進行的動作。
- Azure PaaS 資源除了 IaaS VM 之外，可以從 Azure Resource Manager 範本的環境中佈建。
- 除了其他類型基底所建立的個別 VM 之外，可在實驗室中追蹤環境的成本。
- 使用者具有的環境 VM 原則控制項與單一實驗室 VM 所具有的相同。

> [!NOTE]
> 在此體驗中尚未支援透過 ARM 範本部署資源類型 Microsoft.DevTestLab/labs (或其巢狀資源類型，例如，Microsoft.DevTestLab/labs/virtualmachines)。 若要部署 VM，請務必使用 Microsoft.Compute/virtualmachines。 可以在 [Azure 快速入門範本資源庫](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-customdata/azuredeploy.json)中找到更多 ARM 範本範例。
>
>

## <a name="configure-azure-resource-manager-template-repositories"></a>設定 Azure Resource Manager 範本儲存機制

做為基礎結構即程式碼與組態即程式碼的其中一個最佳作法，應在原始檔控制中管理環境範本。 Azure DevTest Labs 採用這種作法，並直接從您的 GitHub 或 VSTS Git 儲存機制載入所有 Azure Resource Manager 範本。 有幾個規則可在儲存機制中組織您的 Azure Resource Manager 範本︰

- 主要的範本檔案必須命名為 `azuredeploy.json`。 

    ![金鑰 Azure Resource Manager 範本檔案](./media/devtest-lab-create-environment-from-arm/master-template.png)

- 如果您想要使用參數檔案中定義的參數值，參數檔案必須命名為 `azuredeploy.parameters.json`。
- 可以定義中繼資料來指定範本顯示名稱和描述。 此中繼資料必須在名為 `metadata.json` 的檔案中。 下列範例中繼資料檔案說明如何指定顯示名稱和描述︰ 

```json
{
 
"itemDisplayName": "<your template name>",
 
"description": "<description of the template>"
 
}
```

下列步驟將引導您使用 Azure 入口網站將儲存機制新增至您的實驗室。 

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
1. 從實驗室清單中，選取所需的實驗室。   
1. 在實驗室的刀鋒視窗上，選取 [組態和原則]。

    ![組態和原則](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. 從 [組態和原則] 設定清單中，選取 [儲存機制]。 [儲存機制] 刀鋒視窗會列出已新增至實驗室的儲存機制。 會為所有實驗室自動產生名為 `Public Repo` 的儲存機制，並連接到 [DevTest Labs GitHub 儲存機制](https://github.com/Azure/azure-devtestlab)，其中包含數個 VM 構件供您使用。

    ![公用儲存機制](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. 選取 [新增 +] 以新增您的 Azure Resource Manager 範本儲存機制。
1. 當第二個 [儲存機制] 刀鋒視窗開啟時，輸入必要資訊，如下所示︰
    - **名稱** - 輸入在實驗室中使用的儲存機制名稱。
    - **Git 複製 URL** - 輸入來自 GitHub 或 Visual Studio Team Services 的 Git HTTPS 複製 URL。  
    - **分支** - 輸入分支的名稱，以存取您的 Azure Resource Manager 範本定義。 
    - **個人的存取權杖** - 個人的存取權杖用來安全地存取您的儲存機制。 若要從 Visual Studio Team Services 取得您的權杖，請選取 [YourName > > 我的設定檔 > 安全性 > 公用存取權杖]**&lt;**。 若要從 GitHub 取得權杖，請選取 avatar 然後選取 [設定 > 公用存取權杖]。 
    - **資料夾路徑** - 使用兩個輸入欄位其中之一、輸入資料夾路徑，其開頭為正斜線 - / - 且與 Git 複製 URI 相關，至構件定義 (第一個輸入欄位) 或您的 Azure Resource Manager 範本定義。   
    
        ![公用儲存機制](./media/devtest-lab-create-environment-from-arm/repo-values.png)

1. 一旦輸入所有必要的欄位並通過驗證後，請選取 [儲存]。

下一節將引導您從 Azure Resource Manager 範本建立環境。

## <a name="create-an-environment-from-an-azure-resource-manager-template"></a>從 Azure Resource Manager 範本建立環境

一旦在實驗室中設定 Azure Resource Manager 範本儲存機制之後，您實驗室的使用者可以使用 Azure 入口網站以下列步驟建立環境︰

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
1. 從實驗室清單中，選取所需的實驗室。   
1. 在實驗室的刀鋒視窗上，選取 [新增+]**Add+**。
1. [選擇基底] 刀鋒視窗會顯示您可以與先列出的 Azure Resource Manager 範本搭配使用的基本映像。 選取所需的 Azure Resource Manager 範本。

    ![選擇基底](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. 在 [新增] 刀鋒視窗中，輸入**環境名稱**值。 環境名稱是實驗室中向您使用者顯示的內容。 會在 Azure Resource Manager 範本中定義其餘的輸入欄位。 如果在範本中定義預設值或 `azuredeploy.parameter.json` 檔案存在，則預設值會顯示在這些輸入欄位中。 針對安全字串類型的參數，您可以使用儲存在實驗室中的[個人密碼存放區](https://azure.microsoft.com/en-us/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)的密碼。

    ![新增刀鋒視窗](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > 有幾個參數值 (即使指定) 會顯示為空值。 因此，如果使用者將這些值指派給 Azure Resource Manager 範本中的參數，DevTest Labs 不會顯示值；而是顯示空白的輸入欄位，當中實驗室使用者需在建立環境時輸入值。
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. 選取 [新增] 以建立環境。 環境會立即啟動佈建，並在**我的虛擬機器**清單中有狀態顯示。 實驗室會自動建立新的資源群組，以佈建 Azure Resource Manager 範本中定義的所有資源。
1. 一旦建立環境後，在**我的虛擬機器**清單中選取環境，以開啟資源群組刀鋒視窗並瀏覽環境中佈建的資源。
    
    ![我的虛擬機器清單](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 按一下任何環境，以檢視可用的動作 - 例如套用構件、加入資料磁碟、變更自動關機時間等等。

    ![環境動作](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="next-steps"></a>後續步驟
* 一旦建立 VM 之後，您可以選取 VM 刀鋒視窗上的 [連接] 來連接至 VM。
* 瀏覽 [Azure 快速入門範本庫中的 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates)

