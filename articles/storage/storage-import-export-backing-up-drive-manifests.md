---
title: "備份 Azure 匯入/匯出磁碟機資訊清單 | MicrosoftDocs"
description: "了解如何自動備份 Microsoft Azure 匯入/匯出服務的磁碟機資訊清單。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 3d3abfb93ba09aa9f09258743ef6d4c1fc932ae5


---

# <a name="backing-up-drive-manifests"></a>備份磁碟機資訊清單
可以藉由在 [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) 或 [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) 作業中將 `BackupDriveManifest` 屬性設定為 `true` 來自動備份磁碟機資訊清單。 根據預設不會備份磁碟機資訊清單。 磁碟機資訊清單備份會在與工作相關聯的儲存體帳戶內，在容器中儲存為區塊 Blob。 根據預設，容器名稱是 `waimportexport`，但是您可以在呼叫 `Put Job` 或 `Update Job Properties` 作業時，於 `DiagnosticsPath` 屬性中指定不同的名稱。 備份資訊清單 blob 會以下列格式命名︰`waies/jobname_driveid_timestamp_manifest.xml`。

 您可以藉由呼叫 [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) 作業來擷取工作的備份磁碟機資訊清單 URI。 會在每個磁碟機的 `ManifestUri` 屬性中傳回 Blob URI。

## <a name="see-also"></a>另請參閱
 [使用匯入/匯出服務 REST API](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


