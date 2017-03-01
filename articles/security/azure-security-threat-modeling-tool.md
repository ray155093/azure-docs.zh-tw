---
title: "Microsoft 威脅模型化工具 - Azure | Microsoft Docs"
description: "Microsoft 威脅模型化工具的主頁面，包含大多數所產生之暴露威脅的風險降低措施"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 2ad59f9b463671e2b74708871a9a9faae9bd24eb
ms.openlocfilehash: b75de472508bbf44c6e02e0d751054f37b0e1b8c
ms.lasthandoff: 02/21/2017


---

# <a name="microsoft-threat-modeling-tool"></a>Microsoft 威脅模型化工具 
威脅模型化工具是 Microsoft 安全性開發生命週期 (SDL) 的核心元素。 它可讓軟體架構設計人員及早識別和降低潛在安全性問題的風險，以在問題相對簡單且符合成本效益時加以解決。 因此，可大幅降低總開發成本。 此外，我們在設計此工具時已考慮到非安全性專家的問題，因此可讓所有開發人員更加方便地建立威脅模型，為他們提供清楚說明如何建立和分析威脅模型的指導方針。 

此工具可讓任何人：
* 溝通有關其系統的安全性設計
* 使用已證實的方法分析這些設計中的潛在安全性問題
* 建議和管理安全性問題的風險降低措施

以下列舉部分工具功能和創新︰
* **自動化︰**繪製模型的指引和意見反應
* **每個元素的 STRIDE：**引導式的威脅與降低風險分析
* **報告︰**驗證階段的安全性活動和測試
* **獨特的方法︰**可讓使用者更加看懂和了解威脅
* **專為開發人員所設計並聚焦於軟體上︰**許多方法都聚焦在資產或攻擊者上。 我們則聚焦在軟體上。 我們以所有軟體開發人員和架構設計人員都熟悉的活動為基礎來建置 -- 例如繪製其軟體架構的圖片
* **聚焦在設計分析︰**「威脅模型化」一詞指的可能是需求或設計分析技巧。 有時指的是這兩個情況的複雜混合。 Microsoft SDL 威脅模型化方法是聚焦在設計分析技巧

## <a name="threats"></a>威脅

威脅模型化工具可協助您回答某些問題，例如下列問題︰

* 攻擊者如何變更驗證資料？
* 如果攻擊者可以讀取使用者設定檔資料，會有什麼影響？
* 如果對於使用者設定檔資料庫的存取遭拒會發生什麼事？

為了協助您更加清楚地闡述上面指出的這些問題，Microsoft 使用了 STRIDE 模型，它會將不同類型的威脅分類，並簡化整體安全性對話。

| 類別 | 說明 |
| -------- | ----------- |
| 詐騙 | 涉及非法存取然後使用其他使用者的驗證資訊，例如使用者名稱和密碼 |
| 竄改 | 涉及惡意修改資料。 範例包括未經授權變更持續性資料 (例如保存在資料庫中的資料)，以及修改在兩部電腦之間透過開放式網路 (例如網際網路) 流動的資料 |
| 否認性 | 與拒絕執行動作但沒有其他任何一方有辦法另外證明的使用者有關，比方說，使用者在無法追蹤禁止作業的系統中執行非法作業。 不可否認性是指系統對抗否認性威脅的能力。 例如，購買商品的使用者可能必須在收到商品時簽名。 然後，廠商可以使用簽名收據做為使用者已收到包裹的證據 |
| 資訊洩漏 | 涉及將資訊暴露給不應該具有其存取權的個人，例如，使用者可以讀取它們未被授權存取的檔案，或入侵者能夠讀取在兩部電腦之間傳輸的資料 |
| 阻斷服務 | 阻斷服務 (DoS) 攻擊可阻斷對有效使用者提供的服務，例如，藉由讓網頁伺服器暫時無法存取或無法使用。 您必須防止特定類型的 DoS 威脅，以便提升系統的可用性和可靠性 |
| 提高權限 | 未授權的使用者取得授權的存取權，因此有足夠存取權危害或摧毀整個系統。 提高權限威脅包含下列情況：攻擊者已有效地滲透所有系統防禦，並成為受信任系統本身的一部分，這確實是危險的情況 |

## <a name="mitigations"></a>風險降低

威脅模型化工具風險降低措施會根據 Web 應用程式安全性架構來分類，其包含下列類別︰

| 類別 | 說明 |
| -------- | ----------- |
| [稽核和記錄](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-auditing-and-logging) | 誰在何時做了什麼？ 稽核和記錄是指應用程式記錄安全性相關事件的方式 |
| [驗證](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authentication) | 你是誰？ 驗證是某實體證明另一個實體身分識別的程序 (通常透過使用者名稱和密碼等認證) |
| [授權](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authorization) | 您該怎麼辦？ 授權是應用程式針對資源和作業提供存取控制的方式 | 
| [通訊安全性](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-communication-security) | 您在與誰對話？ 通訊安全性可確保所有通訊是在最安全的情況下進行的 |
| [組態管理](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-configuration-management) | 應用程式的執行身分為何？ 其所連線到的資料庫為何？ 應用程式的管理方式為何？ 如何保護這些設定？ 組態管理指的是應用程式處理這些作業問題的方式 | 
| [碼編譯](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-cryptography) | 如何保護機密資料 (機密性)？ 如何防止資料或程式庫遭到竄改 (完整性)？ 如何為必須是密碼編譯增強式的隨機值提供種子？ 密碼編譯是指應用程式強制執行機密性與完整性的方式 | 
| [例外狀況管理](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-exception-management) | 當應用程式中的方法呼叫失敗時，應用程式會如何處理？ 您要顯示多少資料？ 您要對使用者傳回容易理解的錯誤資訊嗎？ 您要將重要例外狀況資訊傳回給呼叫者嗎？ 應用程式會正常地失敗嗎？ |
| [輸入驗證](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-input-validation) | 您如何知道應用程式收到的輸入是有效且安全的？ 輸入驗證指的是應用程式如何先篩選、消除或拒絕輸入再進行其他處理。 請考慮透過進入點限制輸入並透過退出點編碼輸出。 您是否信任來源的資料，例如來自資料庫和檔案共用？ |
| [敏感性資料](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-sensitive-data) | 應用程式如何處理敏感性資料？ 敏感性資料指的是應用程式如何處理記憶體中、透過網路或持續性存放區中必須受到保護的任何資料 | 
| [工作階段管理](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-session-management) | 應用程式如何處理和保護使用者工作階段？ 工作階段是指使用者與 Web 應用程式之間的一系列相關互動 | 

這可協助您識別︰ 

* 最常犯的錯誤
* 最可行的改進措施

因此，您可以使用這些類別來聚焦在您的安全性工作並設定其優先順序，以便在您知道輸入驗證、身分驗證和授權類別中所發生的最普遍安全性問題時，可從中來開始著手。 如需詳細資訊，請按一下[這裡](https://www.google.com/patents/US7818788)

## <a name="next-steps"></a>後續步驟
請查看下列實用連結，以立即開始使用︰
* [下載連結](https://www.microsoft.com/download/details.aspx?id=49168)
* [開始使用](https://msdn.microsoft.com/magazine/dd347831.aspx)
* [核心訓練](https://www.microsoft.com/download/details.aspx?id=16420)

當您進入其中時，可以看看某些威脅模型化工具專家做了些什麼︰
* [Threats Manager](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Simone Curzi 安全性部落格](https://simoneonsecurity.com/)
