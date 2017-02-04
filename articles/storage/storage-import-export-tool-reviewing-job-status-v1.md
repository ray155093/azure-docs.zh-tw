---
title: "利用複製記錄檔檢閱作業狀態 | Microsoft Docs"
description: "了解如何使用執行匯入或匯出作業時建立的記錄檔來查看匯入匯出作業的狀態。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 25cd0308115994463bd50562594d4e2bb88e8286
ms.openlocfilehash: 66d7690249cb653dfb8d2e591d1ce66162d98a7e


---

# <a name="reviewing-job-status-with-copy-log-files"></a>利用複製記錄檔檢閱作業狀態
當 Microsoft Azure 匯入/匯出服務處理與匯入或匯出作業相關聯的磁碟機時，它會將複製記錄檔寫入到您匯入或匯出 blob 的儲存體帳戶。 記錄檔包含每個已匯入或匯出檔案的詳細狀態。 查詢已完成作業的狀態時會傳回每個複製記錄檔的 URL；如需詳細資訊，請參閱[取得作業](/rest/api/storageservices`Get-Job3)。  
  
 以下是包含兩個磁碟機之匯入作業的複製記錄檔的範例 URL：  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath /waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 如需複製記錄檔的格式和狀態碼的完整清單，請參閱[匯入匯出服務記錄檔格式](storage-import-export-file-format-log.md)。  
  
## <a name="see-also"></a>另請參閱  
 [設定 Azure 匯入匯出工具](storage-import-export-tool-setup-v1.md)   
 [針對匯入作業準備硬碟](storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 [修復匯入作業](storage-import-export-tool-repairing-an-import-job-v1.md)   
 [修復匯出作業](storage-import-export-tool-repairing-an-export-job-v1.md)   
 [針對 Azure 匯入匯出工具進行疑難排解](storage-import-export-tool-troubleshooting-v1.md)



<!--HONumber=Dec16_HO2-->


