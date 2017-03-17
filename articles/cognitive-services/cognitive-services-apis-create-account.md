---
title: "在 Azure 入口網站中建立辨識服務 API 帳戶 | Microsoft Docs"
description: "如何在 Azure 入口網站中建立 Microsoft 辨識服務 API 帳戶。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: garye;gibattag
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: a19f56f0b87d992e445034989bd35a827a72f367
ms.lasthandoff: 03/03/2017


---

# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>在 Azure 入口網站中建立辨識服務 API 帳戶

若要使用 Microsoft 辨識服務 API，您必須先在 Azure 入口網站中建立帳戶。

1. 登入 [Azure 入口網站](http://portal.azure.com)。

2. 按一下 [+ 新增]。

3. 選取 [智慧 + 分析]，然後選取 [辨識服務 API (預覽)]。

    ![選取辨識服務 API](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. 在 [建立] 頁面上，提供下列資訊：

    -   **帳戶名稱：**帳戶的名稱。 我們建議使用描述性的名稱，例如 *AFaceAPIAccount*。

    -   **訂用帳戶：**選取其中一個您至少具備參與者權限的可用 Azure 訂用帳戶。

    -   **API 類型：**選擇您想要使用的辨識服務 API。 如需各種可用辨識服務 API 的相關詳細資訊，請參閱[辨識服務](https://azure.microsoft.com/services/cognitive-services/)網站。

    ![選取 API 類型](media/cognitive-services-apis-create-account/list-of-apis.png)

    -   **定價層：**辨識服務帳戶的費用取決於實際使用方式與您選擇的選項。 如需每個 API 定價的相關詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/)。

    -   **資源群組：**資源群組是共用相同生命週期、權限及原則的資源集合。 若要深入了解資源群組，請參閱[透過入口網站管理 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。

    -   **資源群組位置：**這只有在選取的 API 為全域 (未繫結至特定位置) 的情況下才為必要。 不過，如果 API 為全域且未繫結至特定位置，您必須為資源群組指定將會放置與辨識服務 API 帳戶相關聯之中繼資料的位置。 這個位置對於帳戶的執行階段可用性將不會有任何影響。 若要深入了解資源群組，請參閱[透過入口網站管理 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。

    -   **API 設定：**根據預設，帳戶建立將會停用，直到您的 [Azure 帳戶管理員](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles)明確啟用它為止。

        此設定變更只會套用至目前於左側面板上選取的 API 類型及位置或資源群組位置。

        ![建立辨識服務 API 帳戶](media/cognitive-services-apis-create-account/create-account.png)

        > [!NOTE]
        > 若您收到更新設定失敗的通知，便代表您沒有以[帳戶管理員](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator#types-of-azure-admin-accounts)的身分登入。 帳戶管理員必須遵循先前的步驟以啟用建立。
        >
        > ![更新設定失敗訊息](./media/cognitive-services-apis-create-account/updatefailed.png)
        
        在某些情況下，帳戶管理員可能無法存取訂用帳戶。 若是那種情況，請要求服務管理員遵循[新增或變更管理訂用帳戶或服務的 Azure 系統管理員角色](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)文件中的步驟。
        
        若要尋找您訂用帳戶的帳戶管理員或服務管理員，請在 [Azure 入口網站](https://portal.azure.com)中選取您的訂用帳戶，然後選取 [屬性]。 [帳戶管理員] 和 [服務管理員] 的資訊會顯示在 [屬性] 刀鋒視窗的底部。
        
        ![訂用帳戶屬性](./media/cognitive-services-apis-create-account/subscription-properties.png)
    
    Microsoft 可能會使用您傳送至辨識服務的資料來改善 Microsoft 產品和服務。 如需詳細資訊，請參閱線上服務條款中的 [Microsoft 辨識服務區段](http://www.microsoft.com/Licensing/product-licensing/products.aspx)
   。

5. 若要將帳戶釘選到 Azure 入口網站儀表板，請按一下 [釘選到儀表板]。

6. 按一下 [建立]  來建立帳戶。

成功部署辨識服務帳戶之後，請按一下儀表板中的磚以檢視帳戶資訊。

您可以使用 [概觀] 區段中的 [端點 URL]，以及 [金鑰] 區段中的金鑰，來開始在應用程式中進行 API 呼叫。

![顯示帳戶資訊](media/cognitive-services-apis-create-account/display-account.png)

![顯示帳戶金鑰](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>後續步驟

- 如需所有可用 Microsoft 辨識服務的相關詳細資訊，請參閱[辨識服務](https://azure.microsoft.com/services/cognitive-services/)。

- 如需使用部分範例辨識服務 API 的快速入門指南，請參閱：
    - [開始使用文字分析 API 來偵測情緒、主要片語、主題和語言](cognitive-services-text-analytics-quick-start.md)
    - [辨識服務建議 API 的快速入門指南](cognitive-services-recommendations-quick-start.md)
