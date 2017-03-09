
---
title: "在 Azure Active Directory 中啟用企業狀態漫遊 | Microsoft Docs"
description: "Windows 裝置中企業狀態漫遊設定的常見問題集。 企業狀態漫遊提供使用者跨 Windows 裝置的一致體驗，並且減少設定新的裝置所需的時間。"
services: active-directory
keywords: "企業狀態漫遊, windows 雲端, 如何啟用企業狀態漫遊"
documentationcenter: 
author: ningtan
manager: femila
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 0628dafc9cc4cb05e678f741d913e00116013459
ms.openlocfilehash: 435f801b254a6c423a6ec3e96037ade62e14af29
ms.lasthandoff: 01/05/2017


---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>在 Azure Active Directory 中啟用企業狀態漫遊
企業狀態漫遊適用於具有 Premium Azure Active Directory (Azure AD) 訂用帳戶的任何組織。 如需如何取得 Azure AD 訂用帳戶的詳細資訊，請參閱 [Azure AD 產品](https://azure.microsoft.com/services/active-directory)頁面。

當您啟用企業狀態漫遊時，貴組織將會自動獲得 Azure Rights Management 的免費但有使用限制的訂用帳戶授權。 此免費訂用帳戶會限制為加密和解密企業設定以及企業狀態漫遊服務所同步的應用程式資料；您必須擁有付費的訂用帳戶，才能使用 Azure Rights Management 的完整功能。

取得進階的 Azure AD 訂用帳戶之後，請依照下列步驟來啟用企業狀態漫遊：

1. 登入 Azure 傳統入口網站。
2. 在左側選取 [ACTIVE DIRECTORY] ，然後選取您要啟用企業狀態漫遊的目錄。
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. 移至最上層的 [設定]  索引標籤。
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4. 向下捲動頁面並選取 [使用者可以同步設定及企業應用程式資料]，然後按一下 [儲存]。
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

對於以企業狀態漫遊服務漫遊設定的 Windows 10 裝置，裝置必須使用 Azure AD 身分識別進行驗證。 對於已加入 Azure AD 的裝置，使用者的主要登入是 Azure AD 身分識別，所以不需要額外組態。 針對使用傳統內部部署 Active Directory 的裝置，IT 系統管理員必須 [將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)。

## <a name="sync-data-storage"></a>同步處理資料儲存體
企業狀態漫遊資料裝載於一或多個 [Azure 區域](https://azure.microsoft.com/regions/)，這些區域與 Azure Active Directory 執行個體中設定的國家/區域值有最佳的配對狀態。 企業狀態漫遊的資料是根據三個主要地理區域來分割︰北美洲、EMEA 和 APAC。 適用於租用戶的企業狀態漫遊資料是位於本機的地理區域中，並不會跨區域複寫。  例如，已將國家/地區值設定為其中一個 EMEA 國家/地區 (例如「法國」或「尚比亞」) 的客戶，會將他們的資料裝載於歐洲的一或多個 Azure 區域中。  已在 Azure AD 中將國家/地區值設定為其中一個北美洲國家/地區 (例如「美國」或「加拿大」) 的客戶，會將他們的資料裝載於美國境內的一或多個 Azure 區域中。  已在 Azure AD 中將國家/地區值設定為其中一個 APAC 國家/地區 (例如「澳洲」或「紐西蘭」) 的客戶，會將他們的資料裝載於亞洲的一或多個 Azure 區域中。  南美洲國家/地區和南極大陸資料將裝載於美國境內的一或多個 Azure 區域中。  國家/地區值是在 Azure AD 目錄建立程序期間所建立，之後無法修改。 

如果您需要資料儲存體位置的詳細資料，請向 [Azure 支援](https://azure.microsoft.com/support/options/)提出票證。

## <a name="manage-enterprise-state-roaming"></a>管理企業狀態漫遊
Azure AD 全域管理員可以在 Azure 傳統入口網站中啟用和停用企業狀態漫遊。
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

全域管理員可以限制要同步到特定資源性群組的設定。

全域管理員也可以在 Active Directory 執行個體 [使用者] 清單中選取特定的使用者，然後按一下 [裝置] 索引標籤並選取檢視 [裝置同步設定與企業應用程式資料]，藉以檢視每一使用者裝置同步狀態報告。
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

## <a name="data-retention"></a>資料保留
透過企業狀態漫遊同步處理至 Azure 的資料將會無限期保留，除非執行手動刪除作業，或有問題的資料被判斷為已過時。 

**明確刪除︰** 當 Azure 管理員刪除使用者或目錄時，或管理員明確要求刪除資料時，即會刪除資料。

* **使用者刪除**：在 Azure AD 中刪除使用者時，會將使用者帳戶漫遊資料標示為刪除，並且將會在 90 至 180 天內加以刪除。 
* **目錄刪除**：在 Azure AD 中刪除整個目錄是即時作業。 與該目錄相關聯的所有設定資料都將標示為刪除，並且將會在 90 至 180 天內加以刪除。 
* **依要求刪除**：如果 Azure AD 管理員想要手動刪除特定使用者的資料或設定資料，管理員可以向 [Azure 支援](https://azure.microsoft.com/support/)提出票證。 

**刪除過時的資料**：一年 (「保留期限」) 未存取的資料會被視為過時，可能會從 Azure 中刪除。 保留期限可能有所變更，但不會小於 90 天。 過時的資料可能是一組特定的 Windows/應用程式設定或使用者的所有設定。 例如：

* 如果沒有任何裝置存取特定設定集合 (例如，從裝置移除應用程式，或針對所有使用者的裝置停用如「主題」的設定群組)，則該集合在保留期限之後就會變成過時，可能會遭到刪除。 
* 如果使用者在其所有裝置上已關閉設定同步處理，則不會存取任何設定資料，而且該使用者的所有設定資料將會變成過時，而且可能在保留期限之後刪除。 
* 如果 Azure AD 目錄管理員針對整個目錄關閉企業狀態漫遊，則該目錄中的所有使用者會停止同步處理設定，且所有使用者的所有設定資料會變成過時，並且可能在保留期限之後刪除。 

**復原已刪除的資料**：無法設定資料保留期原則。 一旦資料永久刪除，就無法復原。 不過，務必記得設定資料只會從 Azure 中刪除，不會從使用者裝置中刪除。 如果任何裝置稍後重新連線至企業狀態漫遊服務，設定會再次同步處理並儲存在 Azure 中。

## <a name="related-topics"></a>相關主題
* [企業狀態漫遊概觀](active-directory-windows-enterprise-state-roaming-overview.md)
* [設定和資料漫遊常見問題集](active-directory-windows-enterprise-state-roaming-faqs.md)
* [設定同步處理的群組原則和 MDM 設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 漫遊設定參考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [疑難排解](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

