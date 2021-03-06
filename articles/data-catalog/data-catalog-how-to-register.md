---
title: "在 Azure 資料目錄中註冊資料來源 | Microsoft Docs"
description: "本文主要說明如何在 Azure 資料目錄中註冊資料來源，包括註冊期間擷取的中繼資料欄位。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: 3f7eb053db6b6a545990f31f64628594f7f3e2c1
ms.contentlocale: zh-tw
ms.lasthandoff: 06/06/2017


---
# <a name="register-data-sources-in-azure-data-catalog"></a>在 Azure 資料目錄中註冊資料來源
## <a name="introduction"></a>簡介
Azure 資料目錄是受到完整管理的雲端服務，可作為企業資料來源的註冊和探索系統。 換句話說，資料目錄可於協助人們探索、了解和使用資料來源，並可協助組織從現有的資料獲得更多價值。 透過資料目錄讓資料來源得以被探索的第一步是註冊該資料來源。

## <a name="register-data-sources"></a>註冊資料來源
註冊是指從資料來源擷取中繼資料並將該資料複製到資料目錄服務的程序。 資料會保留在它目前的位置，並且仍然在目前系統的系統管理員及原則的控制之下。

若要註冊資料來源，請執行下列作業：
1. 在 Azure 資料目錄入口網站中，啟動資料目錄的資料來源註冊工具。 
2. 使用您的工作或學校帳戶 (含有您用以登入入口網站的相同 Azure Active Directory 認證) 進行登入。
3. 選取您要註冊的資料來源。

如需更多的逐步詳細資料，請參閱[開始使用 Azure 資料目錄](data-catalog-get-started.md)教學課程。

在您註冊資料來源之後，目錄會追蹤其位置，並為其中繼資料編製索引。 使用者可以搜尋、瀏覽及探索資料來源，然後使用其選擇的應用程式或工具，透過資料來源的位置連線到該資料來源。

## <a name="supported-data-sources"></a>支援的資料來源
請參閱[資料目錄 DSR](data-catalog-dsr.md) 以取得目前支援的資料來源清單。

## <a name="structural-metadata"></a>結構化中繼資料
當您註冊資料來源時，註冊工具會擷取您所選取之物件結構的相關資訊。 這項資訊指的是結構化中繼資料。

對於所有物件而言，此結構化中繼資料將包含物件的位置，因此探索資料的使用者可以使用該資訊來連線至他們所選擇之用戶端工具中的物件。 其他的結構化中繼資料包含物件名稱和類型，以及屬性/資料行名稱和資料類型。

## <a name="descriptive-metadata"></a>描述性中繼資料
除了從資料來源擷取的核心結構化中繼資料之外，資料來源註冊工具也會擷取描述性中繼資料。 針對 SQL Server Analysis Services 和 SQL Server Reporting Services，這項中繼資料是取自這些服務所公開的 Description 屬性。 針對 SQL Server，會擷取使用 ms\_description 擴充屬性所提供的值。 針對 Oracle Database，資料來源註冊工具會從 ALL\_TAB\_COMMENTS 檢視擷取 COMMENTS 資料行。

除了從資料來源擷取的描述性中繼資料之外，使用者也可以使用資料來源註冊工具來輸入描述性中繼資料。 使用者可以新增標記，並且能夠識別所註冊之物件的專家。 所有的描述性中繼資料會與結構化中繼資料一同複製到資料目錄服務。

## <a name="include-previews"></a>包含預覽
根據預設，系統只會從資料來源擷取中繼資料，並複製到資料目錄服務，但如果您可以檢視其中所包含資料的範例，通常能更輕鬆地了解資料來源。

透過使用資料目錄的資料來源註冊工具，您可以在每個已註冊的資料表和檢視中，加入資料的快照預覽。 如果您選擇要在註冊期間包含預覽，註冊工具將包含每個資料表和檢視最多 20 筆記錄。 此快照之後會與結構化和描述性中繼資料一同複製到目錄。

> [!NOTE]
> 含有大量資料行的寬型資料表可能會在預覽中包含少於 20 筆的記錄。
>
>

## <a name="include-data-profiles"></a>包含資料設定檔
包含預覽可為在資料目錄中搜尋資料來源的使用者提供有用的內容；同樣地，包含資料設定檔也能讓使用者更輕鬆了解已探索的資料來源。

透過使用資料目錄的資料來源註冊工具，您可以為每個已註冊的資料表和檢視包含資料設定檔。 如果您選擇在註冊期間包含資料設定檔，則註冊工具會在每個資料表和檢視中包含關於資料的彙總統計資料，包括：

* 物件中的資料列數和資料大小。
* 資料和物件結構描述的最近更新日期。
* Null 記錄與資料行相異值的數目。
* 資料行的最小值、最大值、平均值和標準差值。

這些統計資料之後會與結構化和描述性中繼資料一同複製到目錄。

> [!NOTE]
> 文字和日期資料行不會包含其資料設定檔中的平均值或標準差值統計資料。
>
>

## <a name="update-registrations"></a>更新註冊
註冊資料來源讓您得以使用註冊期間擷取的中繼資料和選擇性預覽，藉此在資料目錄中探索資料來源。 如果在目錄中的資料來源需要更新 (例如，如果物件的結構描述已經變更、或應包含原先排除的資料表，或您想要更新在預覽中所包含的資料)，可以重新執行資料來源註冊工具。

重新註冊已註冊的資料來源會執行合併 “upsert” 作業：將會更新現有的物件，同時建立新的物件。 使用者透過資料目錄入口網站所提供的任何中繼資料都將保留。

## <a name="summary"></a>摘要
因為在資料目錄註冊資料來源會將結構化和描述性中繼資料從資料來源複製到目錄服務，所以可讓您更容易地探索及了解資料來源。 在您註冊資料來源之後，即可使用資料目錄入口網站來標註、管理及探索資料來源。

## <a name="next-steps"></a>後續步驟
如需註冊資料來源的詳細資訊，請參閱[開始使用 Azure 資料目錄](data-catalog-get-started.md)教學課程。

