---
title: "威脅偵測 - Azure SQL Database | Microsoft Docs"
description: "威脅偵測會偵測異常資料庫活動，指出資料庫有潛在的安全性威脅。"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: eadaa3e7a279b6b92da1d0c026c3002297dfd298
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-threat-detection"></a>SQL Database 威脅偵測

威脅偵測會偵測異常資料庫活動，指出資料庫有潛在的安全性威脅。  「威脅偵測」目前為預覽狀態。

## <a name="overview"></a>概觀

威脅偵測提供新的一層安全性，在發生異常活動時會提供安全性警示，讓客戶偵測並回應潛在威脅。  使用者可以使用 [SQL Database 稽核](sql-database-auditing.md)來探索可疑的事件，以判斷事件的原因是否是有人嘗試存取、破壞或利用資料庫中的資料。
您不必是安全性專家，也不需要管理進階的安全性監視系統，威脅偵測讓您輕鬆解決資料庫的潛在威脅。

威脅偵測會偵測異常資料庫活動，指出潛在的 SQL 插入式攻擊。 SQL 插入式攻擊是網際網路上常見的 Web 應用程式安全性問題之一，用於攻擊資料導向應用程式。 攻擊者利用應用程式弱點將惡意的 SQL 陳述式插入應用程式輸入欄位，以破壞或修改資料庫中的資料。

## <a name="next-steps"></a>後續步驟

* 若要設定及管理威脅偵測，請參閱[在 Azure 入口網站中設定及管理威脅偵測](sql-database-threat-detection-portal.md)。

