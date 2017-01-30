---
title: "Azure 匯入/匯出工具匯入作業命令的快速參考 | Microsoft Docs"
description: "適用於匯入作業的 Azure 匯入/匯出工具命令常用命令參考"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 41bc5585f3d9dea2a08dc8a6bc1e4fdf9f0c8fc4
ms.openlocfilehash: 79b1d3f92671638ba43bac2f5428a1f903d11080


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>匯入作業的常用命令快速參考

本文章提供一些常用命令的快速參考。 如需詳細使用方式，請參閱[針對匯入作業準備硬碟](storage-import-export-tool-preparing-hard-drives-import.md)。

## <a name="import-job-quick-reference"></a>匯入作業快速參考

第一個工作階段︰

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

第二個工作階段︰

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

中止第一個工作階段︰

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

繼續最近中斷的工作階段︰

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

將磁碟機新增至最新的工作階段︰

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>後續步驟

[針對匯入作業準備硬碟的簡單工作流程](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)



<!--HONumber=Dec16_HO3-->


