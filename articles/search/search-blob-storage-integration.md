---
title: "將 Azure 搜尋服務新增至 Blob 儲存體 | Microsoft Docs"
description: "使用 Azure 搜尋服務 HTTP REST API 在程式碼中建立索引。"
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
ms.service: search
ms.topic: article
ms.date: 05/04/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 0cd0cbb05c465d32a9ef02f9350ebdf9ccea36c5
ms.contentlocale: zh-tw
ms.lasthandoff: 05/08/2017

---

# <a name="searching-blob-storage-with-azure-search"></a>使用 Azure 搜尋服務搜尋 Blob 儲存體

搜尋 Azure Blob 儲存體中儲存的各種內容類型可能是難以解決的問題。 不過，使用 Azure 搜尋服務，您只要按幾下，即可編製索引並搜尋 Blob 內容。 在 Blob 儲存體上搜尋需要佈建 Azure 搜尋服務。 在[價格頁面](https://aka.ms/azspricing)可以找到 Azure 搜尋服務的各種服務限制和定價層。

## <a name="what-is-azure-search"></a>何謂 Azure 搜尋服務？
[Azure 搜尋服務](https://aka.ms/whatisazsearch)是一個搜尋解決方案，可讓開發人員輕鬆新增 Web 和行動應用程式的強大全文檢索搜尋體驗。 Azure 搜尋服務不需要管理任何搜尋基礎結構，同時可提供 [99.9% 的運作時間 SLA](https://aka.ms/azuresearchsla)。

透過 56 種語言的進階支援，Azure 搜尋服務可以分析您的內容，並以智慧方式處理特定語言結構建構。 Azure 搜尋服務也提供各種工具來打造豐富的搜尋使用者經驗。 使用 Azure 搜尋服務新增功能很簡單，例如多面向導覽、自動提示搜尋建議，以及使用者介面的結果醒目提示。 若要了解 Azure 搜尋服務的功能，您可以閱讀服務的[文件](https://aka.ms/azsearchdocs)。

## <a name="crack-open-and-search-through-the-content-of-enterprise-document-formats"></a>直接開啟並搜尋企業文件格式的內容
透過 Azure Blob 儲存體中的[文件擷取](https://aka.ms/azsblobindexer)支援，Azure 搜尋服務可以為 Blob 中儲存的各種檔案類型內容編製索引︰
- PDF
- Microsoft Office：DOCX/DOC、XLSX/XLS、PPTX/PPT、MSG (Outlook 電子郵件)
- HTML
- 純文字檔案

擷取這些檔案類型的文字和中繼資料，即可利用單一查詢輕鬆搜尋多個檔案格式，以找出最相關的文件 (無論何種類型)。 藉由為 Microsoft Office 文件、PDF 和電子郵件的內容和中繼資料編製索引，可以使用 Blob 儲存體和 Azure 搜尋服務建置強大的企業容管理解決方案。

## <a name="search-through-your-blob-metadata"></a>搜尋您的 Blob 中繼資料
若要簡化任何內容類型的 Blob 排序，常見案例是為每個 Blob 的自訂、使用者定義 Blob 中繼資料以及系統屬性編製索引。 如此一來，每個 Blob 的資訊都會在 Blob 中編製索引 (無論何種文件類型)，因此您可以輕鬆地將所有的 Blob 儲存體內容排序和進行層面分類。

[深入了解如何編製 Blob 中繼資料的索引。](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>影像搜尋
Azure 搜尋服務的全文檢索搜尋、多面向導覽和排序功能現在可套用至 Blob 中儲存的影像中繼資料。

如果從 Microsoft 辨識服務使用[電腦視覺 API](https://www.microsoft.com/cognitive-services/computer-vision-api) 預先處理這些影像，則可以為在每個影像中找到的視覺內容 (包括 OCR 和手寫辨識) 編製索引。 我們正努力將 OCR 和其他影像處理功能直接新增到 Azure 搜尋服務，如果您對這些功能有興趣，請在我們的 [UserVoice](https://aka.ms/azsuv) 上提出要求或[傳送電子郵件給我們](mailto:azscustquestions@microsoft.com)。

## <a name="index-and-search-through-json-blobs"></a>檢索和搜尋 JSON Blob
可以將 Azure 搜尋服務設定為擷取在包含 JSON 的 Blob 中找到的結構化內容。 Azure 搜尋服務可以讀取 JSON Blob，並將結構化內容剖析成 Azure 搜尋服務文件的適當欄位。 Azure 搜尋服務也會採用包含 JSON 物件陣列的 Blob，並將每個元素對應至不同的 Azure 搜尋服務文件。

請注意，JSON 剖析目前無法透過入口網站設定。 [深入了解 Azure 搜尋服務中的 JSON 剖析。](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>快速入門
您可以直接從 Blob 儲存體入口網站刀鋒視窗將 Azure 搜尋服務新增至 Blob。

![](./media/search-blob-storage-integration/blob-blade.png)

按一下 [新增 Azure 搜尋服務] 選項會啟動一個流程，您可以在其中選取現有的 Azure 搜尋服務或建立新的服務。 如果您建立新的服務，您將會跳脫儲存體帳戶的入口網站經驗。 您必須重新瀏覽至儲存體入口網站刀鋒視窗，然後重新選取 [新增 Azure 搜尋服務] 選項，便可在其中選取現有的服務。

### <a name="next-steps"></a>後續步驟
閱讀完整的[文件](https://aka.ms/azsblobindexer)以深入了解 Azure 搜尋服務 Blob 索引子。

