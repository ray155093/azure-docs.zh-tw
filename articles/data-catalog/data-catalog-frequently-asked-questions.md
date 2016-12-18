---
title: "Azure 資料目錄的常見問題集 | Microsoft Docs"
description: "與 Azure 資料目錄相關的常見問題集，包括資料來源探索、註解和管理功能。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/04/2016
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 76cc9c79851cc040e6a36c5499a77c0d95948154


---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure 資料目錄的常見問題集
這篇文章提供與 Microsoft **Azure 資料目錄** 服務相關的常見問題集解答。

## <a name="q-what-is-azure-data-catalog"></a>問：什麼是 Azure 資料目錄？
答：Microsoft Azure 資料目錄是裝載於 Microsoft Azure 雲端中全面管理的服務，可作為企業資料來源的註冊系統和探索系統。 Azure 資料目錄可讓任何使用者 – 從分析師、資料科學家到開發人員 – 註冊、探索、了解及取用資料來源。

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>問：Azure 資料目錄可解決客戶面臨的什麼挑戰？
Azure 資料目錄可讓使用者探索並了解企業資料來源，以解決資料來源探索和「黑暗資料」的挑戰。

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>問：Azure 資料目錄的目標對象是誰？
Azure 資料目錄強化技術性和非技術性使用者的能力，包括：

* 資料開發人員、BI 和分析專業人員：負責產生資料和分析內容供他人使用
* 資料負責人：這些人了解資料、其意義、用法和用途
* 資料取用者：這些人需要能夠透過他們所選擇的工具，輕鬆地尋找、了解並連線至工作所需的資料
* 中央 IT：這些人需要提供數百個資料來源供商務使用者探索，也需要持續監督資料的使用情況和由誰使用

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>問：Azure 資料目錄的區域可用性如何？
目前只有下列資料中心提供 Azure 資料目錄服務：

* 美國西部
* 美國東部
* 西歐
* 北歐
* 澳洲東部
* 東南亞

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>問：Azure 資料目錄中的資料資產數目有何限制？
免費版 Azure 資料目錄僅限於 5,000 個已註冊的資料資產。

標準版 Azure 資料目錄支援多達 100,000 個已註冊的資料資產。

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>問：支援什麼資料來源和資產類型？
請參閱 [資料目錄 DSR](data-catalog-dsr.md) 以取得目前支援的資料來源清單。

## <a name="q-how-do-i-request-support-for-another-data-source"></a>問：如何要求另一個資料來源的支援？
您可以在 [Azure 資料目錄論壇](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)提交功能要求與其他意見。

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>問：如何開始使用 Azure 資料目錄？
建議依照 [開始使用資料目錄](data-catalog-get-started.md)中的指示開始使用。 本文是服務中各項功能的端對端概觀。

## <a name="q-how-do-i-register-my-data"></a>問：如何註冊我的資料？
若要在 Azure 資料目錄中註冊您的資料，請從 Azure 資料目錄入口網站的 [發佈] 區域啟動 Azure 資料目錄註冊工具。 在 Azure 資料目錄發佈應用程式中，使用您用來存取 Azure 資料目錄入口網站的相同認證登入，然後選取資料來源和您想要註冊的特定資產。

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>問：從已註冊的資料資產中會擷取哪些屬性？
具體的屬性隨資料來源而不同，一般而言，Azure 資料目錄發佈服務會擷取下列資訊：

* 資產名稱
* 資產類型
* 資產描述
* 屬性/資料行名稱
* 屬性/資料行資料類型
* 屬性/資料行描述

> [!IMPORTANT]
> 使用 Azure 資料目錄註冊資料資產並不會將資料移動或複製至雲端。 從資料來源註冊資產會將資產的中繼資料複製到 Azure，但資料仍保留在現有的資料來源位置。 此規則唯一的例外狀況，在於使用者註冊資產時選擇上傳預覽記錄或資料設定檔。 包含預覽時，最多會從每個資產複製 20 筆記錄，並在 Azure 資料目錄中儲存為快照。 包含資料設定檔時，系統將會計算彙總資訊 (例如資料表大小、每資料行 null 值百分比，以及各資料行的最小值、最大值和平均值)，並將其加入目錄所存放的中繼資料。
> 
> 

<br/>

> [!NOTE]
> 若資料來源 (例如 SQL Server Analysis Services) 具第一級 **Description** 屬性，Azure 資料目錄發佈應用程式就會擷取該屬性值。 SQL Server 關聯式資料庫缺少第一級的 **Description** 屬性，因此 Azure 資料目錄發行應用程式會從物件和資料行的 ms_description 擴充屬性擷取值。 如需詳細資訊，請參閱 TechNet [使用資料庫物件的擴充屬性](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx)。
> 
> 

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>問：新註冊的資產經過多久才會出現在 Azure 資料目錄中？
使用 Azure 資料目錄註冊資產之後，可能需要經過 5-10 秒才會在 Azure 資料目錄入口網站中顯示這些資產。

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>問：如何加註和充實已註冊的資料資產的中繼資料？
若要為已註冊的資產提供中繼資料，最簡單的方法是在 Azure 資料目錄入口網站中選取資產，然後在所選物件的屬性窗格或結構描述窗格中，輸入中繼資料值。

在註冊過程中，您也可以提供一些中繼資料，例如專家和標記。 在 Azure 資料目錄發行服務中提供的值，將會套用到當時註冊的所有資產。 若要在入口網站中檢視最近註冊的物件以另外加註，請在 Azure 資料目錄發行應用程式的最後一個畫面選取 [檢視入口網站]  按鈕。

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>問：如何刪除我已註冊的資料物件？
您可以從 Azure 資料目錄中刪除物件，只要在入口網站中選取物件，然後按一下 [刪除]  按鈕。 這會從 Azure 資料目錄中移除物件的中繼資料，但不會影響實際的基礎資料來源。

## <a name="q-what-is-an-expert"></a>問：什麼是專家？
專家是指對資料物件具有獨特見解的人。 一個物件可能有多個專家。 專家不見得是物件的「擁有者」。專家只是知道應該如何運用資料的人。

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>問：如果我遇到問題，如何與 Azure 資料目錄小組分享資訊？
請使用 Azure 資料目錄論壇來報告問題、分享資訊及發問。 論壇位於 http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

## <a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>問：Azure 資料目錄可以處理我想要使用的其他資料來源嗎？
我們正積極將更多資料來源加入 Azure 資料目錄。 如果有您希望我們支援的資料來源，請在 [Azure 資料目錄論壇](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)提出建議 (如果已建議，請表達支持)。

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>問：Azure 資料目錄與 Power BI for Office 365 中的資料目錄有何關係？
您可將 Azure 資料目錄視為進化版的「資料目錄」。 Azure 資料目錄在資料來源發行和探索方面提供類似的功能，但著重於更廣泛的案例，且不依賴 Office 365。 在 Azure 資料目錄公開推出不久之後，這兩個目錄將合併成單一服務。

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>問：使用者向 Azure 資料目錄註冊資產需要什麼權限？
執行 Azure 資料目錄註冊工具的使用者，在資料來源上需要有權限從來源讀取中繼資料。 如果使用者也選擇包含預覽，則使用者必須也有權限從註冊的物件讀取資料。

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>問：Azure 資料目錄也會支援內部部署嗎？
Azure 資料目錄是可以同時使用雲端和內部部署資料來源的雲端服務，提供混合式資料來源探索解決方案。 目前並未規劃推出可採內部部署執行的 Azure 資料目錄服務版本。

## <a name="q-can-we-extract-more-richer-metadata-from-the-data-sources-we-register"></a>問：我們是否可以從我們所註冊的資料來源中擷取更多/更豐富的中繼資料？
我們正積極擴充 Azure 資料目錄的功能。 若您有希望在註冊時從資料來源擷取的其他中繼資料，請在 [Azure 資料目錄論壇](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)提出建議 (如果已有人建議，請投票支持)。 未來，我們將允許協力廠商透過擴充性 API 加入新的資料來源類型。

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>問：如何限制已註冊的資料資產的可見性，以限制只有特定人員才能探索它們？
答：在 Azure 資料目錄中選取資料資產，然後按一下 [取得擁有權] 按鈕。 Azure 資料目錄中的資料資產擁有者可以變更可見性設定，可能變更為允許所有目錄使用者探索擁有的資產，或限定只有特定使用者才能看見。

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>問：如何更新資料資產的註冊，讓資料來源中的變更反映在目錄中？
答：若要更新已註冊在目錄中的資料資產的中繼資料，只要重新註冊包含這些資產的資料來源即可。 資料來源中的任何變更 (例如在資料表或檢視中加入或移除資料行) 都會在目錄中更新，但會保留使用者提供的任何註解。

## <a name="q-my-question-isnt-answered-here-what-should-i-do"></a>問：這裡沒有解答我的問題 – 我該怎麼辦？
請前往 [Azure 資料目錄論壇](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)。 那裡提出的問題會在這裡找到答案。




<!--HONumber=Nov16_HO3-->


