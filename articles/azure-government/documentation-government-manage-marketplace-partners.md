---
title: "適用於 Azure Government 合作夥伴的 Azure Marketplace | Microsoft Docs"
description: "本文提供 Azure Government 的功能比較和應用程式開發指引。"
services: azure-government
cloud: gov
documentationcenter: 
author: tsingh
manager: asimm
ms.assetid: 7790d3c5-d0fa-4662-b4f0-a174cb7d6c9f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: zakramer;tsingh;divacc
translationtype: Human Translation
ms.sourcegitcommit: 0839e8e57b2149e50d4512d28b1e57e6592be458
ms.openlocfilehash: 70821864520ff18ba8c64be0ea66376bccee7148


---
# <a name="azure-marketplace-for-azure-government"></a>適用於 Azure Government 的 Azure Marketplace
如果您是 Azure Government 合作夥伴，並且想要在 Azure Marketplace 發佈供應項目，請參閱本文中的詳細資料。

Azure Government Marketplace 中目前支援 BYOL VM 映像和解決方案範本。 您最好先檢閱過解決方案範本再發佈至 Azure Government，以確保它可在 Azure 公用和 Azure Government 雲端上運作。 如果您只要發佈 VM 映像，您可以前往更下面的發佈步驟。

## <a name="pre-publishing-validation-for-solution-templates"></a>解決方案範本的發佈前驗證

在 Azure Government 中發佈解決方案範本之前，建議您檢查幾個常見的最佳作法領域，以確保您的範本可在 Azure 公用雲端和 Azure Government 中運作。

1.  確認端點未硬式編碼到適用於 Azure 公用雲端的解決方案範本。 這些端點將不適用於任何其他 Azure 環境。 相反地，請修改解決方案範本，以要求 API 呼叫來提取有效的端點︰  

  範例：

  不正確的 VHD uri (硬式編碼) "uri": "[concat('https://', variables('storageAccountName'), '.blob.core.windows.net/',  '/osdisk.vhd')]",

  正確的 VHD uri (參照)

  "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'osdisk.vhd')]",

  經過修正的語法將確保範本可在任何雲端 (政府、公用 Azure、AzureStack、中國等) 上運作

2.  確認解決方案範本中所用的資源，可於您要發佈到的最高雲端中使用。
Azure 和 API 版本中的每秒要求數目

    在入口網站中使用資源總管識別 Azure Government 的可用性︰

  1.    登入 Azure Government 入口網站
  2.    啟動資源總管，按照此處 (https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-supported-services#supported-regions) 所列的步驟進行

  最常使用的擴充功能；Fairfax 中的可用性  

  | 發佈者/類型 | Fairfax 中的可用版本 |
  | --- | --- |
  | Microsoft.OSTCExtensions/CustomScriptForLinux | 1; 1.1; 1.2.2.0; 1.3.0.2; 1.4.1.0; 1.5.2.0 |
  | Microsoft.Compute/CustomScriptExtension | 1.2; 1.3; 1.4; 1.7; 1.8 |
  | Microsoft.Azure.Extensions/DockerExtension | 無法使用 |
  | Microsoft.Azure.Extensions/CustomScript | 2.0.2 |
  | Microsoft.OSTCExtensions/LinuxDiagnostic | 2.0.9005; 2.1.9005; 2.2.9005; 2.3.9011 |
  | Microsoft.Powershell/DSC | 2.19.0.0 |

> [!NOTE]
> 如果您在允許值清單中放入位置，則必須定期更新此清單以因應新增的區域。  


## <a name="publishing"></a>發佈
> [!NOTE]
> 如果您還不是 Azure 認證 Marketplace 夥伴，請完成示範如何[發佈和管理供應項目](../marketplace-publishing/marketplace-publishing-getting-started.md)的步驟再繼續進行。
>
>

### <a name="step-1"></a>步驟 1
登入 [Azure 發佈](https://publish.windowsazure.com)。

### <a name="step-2"></a>步驟 2
按一下您想要發佈的供應項目。

### <a name="step-3"></a>步驟 3
按一下 [SKU]，然後選取 [Azure Government 雲端] 方塊。

> [!NOTE]
> 只支援自備授權 (BYOL) SKU。  此選項不適用於隨用隨付 (PayG) SKU。
>
>

![指出 [SKU] 和 [Azure Government 雲端] 選項位置的供應項目頁面](./media/government-manage-marketplace-partner-1.png)

### <a name="step-4"></a>步驟 4
按一下 [+ 新增認證] 連結，可為供應項目的任何認證新增連結。

![具有 [新增認證] 連結的供應項目頁面](./media/government-manage-marketplace-partner-2.png)

### <a name="step-5"></a>步驟 5
若要在發佈入口網站中測試您的映像，請索取 Microsoft Azure Government 的[試用帳戶](https://azuregov.microsoft.com/trial/azuregovtrial)。

您為美國聯邦、州、地方政府或原住民部落提供服務的合作夥伴資格，已經過驗證，並將透過電子郵件提供確認。  您的試用帳戶在 3-5 個工作天內將可供使用。

### <a name="step-6"></a>步驟 6
按一下 [發佈]，然後按一下 [推送至預備環境]。

![[發佈] 和 [推送至預備環境] 選項](./media/government-manage-marketplace-partner-3.png)

系統會提示您輸入允許清單內的訂用帳戶，他們有權存取預備環境中的供應項目。 輸入訂用帳戶 ID (來自於新取得的 Azure Government 試用帳戶)。

![用來指定可存取供應項目之訂用帳戶識別碼的提示](./media/government-manage-marketplace-partner-4.png)

### <a name="step-7"></a>步驟 7
在供應項目順利推送至預備環境後，您可以使用 Azure Government 試用帳戶，登入 [Azure 入口網站](https://portal.azure.us)來測試您的映像。

### <a name="step-8"></a>步驟 8
使用試用的訂用帳戶驗證過映像後，就可以按一下 [發佈] 並要求核准進入生產環境，來讓供應項目即時可用。

![要求核准進入生產環境的命令](./media/government-manage-marketplace-partner-5.png)

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 [Microsoft Azure Government 部落格](https://blogs.msdn.microsoft.com/azuregov/)。



<!--HONumber=Dec16_HO3-->


