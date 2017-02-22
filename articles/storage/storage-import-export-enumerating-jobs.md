---
title: "列舉 Azure 匯入/匯出服務中的作業 | MicrosoftDocs"
description: "了解如何列舉所有訂用帳戶中的 Azure 匯入/匯出服務作業。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 65d8912c4eced92206ee1262f3e3307b3162cbd6


---

# <a name="enumerating-jobs"></a>列舉作業
若要列舉訂用帳戶中的所有作業，請呼叫 [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List)作業。 `List Jobs` 會傳回一份作業以及下列屬性︰

-   作業的類型 (匯入或匯出)

-   目前的作業狀態

-   作業的相關聯儲存體帳戶

## <a name="see-also"></a>另請參閱
 [使用匯入/匯出服務 REST API](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


