---
title: "機密資料絕不儲存在 Azure remoteapp 的自訂映像上 | Microsoft Docs"
description: "深入了解在 Azure RemoteApp 自訂映像中儲存資料的指導方針"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 5a19903b-15f9-49d9-9bc1-ae80f2671aa1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 6cc74e3d3bd704dab1a43b66374b51c1f3e2a0a2


---
# <a name="never-store-sensitive-data-on-custom-images"></a>機密資料絕不要儲存在自訂映像上
> [!IMPORTANT]
> Azure RemoteApp 即將中止。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
> 
> 

當您將自己的應用程式裝載在 Azure RemoteApp 中時，第一個步驟就是建立自訂映像。 我們會使用這個自訂映像建立 VM 執行個體，向您的使用者提供應用程式。 自訂映像應該只包含應用程式，絕對不要包含可能遺失的機密資料，例如 SQL 資料庫、人事檔案，或像是 QuickBooks 公司檔案的特殊資料檔案。 所有的機密資料都應位於 Azure RemoteApp 外部的檔案伺服器上，可以是另一個 Azure VM，或在 SQL Azure 中。 映像應該只裝載連接到資料來源及顯示資料的應用程式。 如需詳細資訊，請參閱 [Azure RemoteApp 映像需求](remoteapp-imagereqs.md) 。 

若要了解不該儲存機密資料的原因，您需要了解 Azure RemoteApp 的運作方式。 當建立或更新集合時，幕後會建立多個映像的複製品或複本。 所有這些 VM 執行個體都是和自訂映像完全一致的確切複本，當使用者啟動應用程式時，他們就會連接到這些 VM 執行個體的其中之一。 但是相同的執行個體沒有任何保證，也不應該有什麼影響，因為它們是非永續性的。 裝載應用程式的 VM 執行個體是非永續性的，可以損毀或刪除，例如，在集合更新期間。 

集合一經佈建且使用者開始連接到 VM，使用者資料就是永續性且受保護的，因為它會儲存在 VHD 的個別儲存體中，我們稱之為[使用者設定檔磁碟 (UPD)](remoteapp-upd.md)，這是 c:\users\<使用者設定檔> 中的使用者設定檔。 當應用程式啟動時，就會掛載 UPD，作業系統會視同本機使用者設定檔處理。 深入了解 [Azure RemoteApp 如何儲存使用者資料和設定](remoteapp-upd.md)。

不該在映像的資料範例︰

* 供使用者存取的共用資料
* SQL DB 或 QuickBooks DB
* D:\ 中的所有資料

可以位在預設設定檔，要複製到每個使用者 UPD 的範例資料︰

* 每個使用者的組態檔
* 使用者需要保留在 UPD 中的指令碼

重點︰

* 建立自訂映像時，可能遺失的機密資料絕不儲存在映像上。
* 機密資料應該一律放在雲端的個別檔案伺服器、個別 Azure VM 上，一律是在 Azure RemoteApp 中裝載應用程式的 VM 執行個體外部。 
* 使用者資料儲存和保存在使用者設定檔磁碟 (UPD)




<!--HONumber=Dec16_HO2-->


