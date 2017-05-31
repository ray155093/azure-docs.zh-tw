---
title: "使用 Active directory 稽核記錄檔進行 Azure 記錄檔整合 (AZLOG) | Microsoft Docs"
description: "了解如何安裝 Azure 記錄整合服務，整合 Azure 的稽核記錄檔"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/09/2017
ms.author: barclayn
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5e135be1e21173add3236f851609f1df0a5b0dee
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---

#<a name="integrate-azure-active-directory-audit-logs"></a>整合 Azure Active Directory 稽核記錄

Azure Active Directory 稽核事件可協助您識別 Azure Active Directory 中發生的特殊權限動作。 您可以藉由檢視 [Azure Active Directory 稽核報告事件](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md)看到您可以追蹤的事件類型

>[!NOTE]
您應該先檢閱[入門](security-azure-log-integration-get-started.md)文章，並完成直到**安裝和驗證後**一節步驟 3 的所有步驟，再嘗試執行本文中的步驟。

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>整合 Azure Active Directory 稽核記錄的步驟

1. 開啟命令提示字元，並使用 cd 命令前往 **c:\Program Files\Microsoft Azure Log Integration**
2. 執行命令：

 ``azlog createazureid``

 此命令會提示您登入 Azure。 此命令接著會建立託管 Azure 訂用帳戶 (其中登入的使用者是系統管理員、共同管理員或擁有者) 的 Azure AD 租用戶中的 Azure Active Directory 服務主體。 若登入的使用者只是 Azure AD 租用戶中的來賓使用者，則此命令將會失敗。 對 Azure 的驗證會透過 Azure Active Directory (AD) 來進行。 建立 Azure 記錄整合的服務主體會建立 Azure AD 身分識別，以獲得 Azure 訂用帳戶的讀取權限。

3. 執行提供 tenantID 的命令。 您必須是租用戶系統管理員角色的成員才能執行命令。

``Azlog.exe authorizedirectoryreader tenantId``

範例

``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

檢查下列資料夾以確認 JSON 檔案建立在 Azure Active Directory 稽核記錄中：

* **C:\Users\azlog\AzureActiveDirectoryJson**
* **C:\Users\azlog\AzureActiveDirectoryJsonLD**

將標準的 SIEM 檔案轉寄站連接器指向適當資料夾，以透過管線將資料傳送至 SIEM 執行個體。 您可能需要根據所使用的 SIEM 產品進行某些欄位對應。

可透過 [Azure 記錄檔整合 MSDN 論壇](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)取得社群協助。 此論壇讓 AzLog 社群能針對如何充分利用 Azure 記錄檔整合，透過分享問題、 解答、 秘訣和技巧支援彼此。 此外，Azure 記錄整合小組會監視這個論壇，並且會盡全力提供協助。

您也可以建立[支援要求](../azure-supportability/how-to-create-azure-support-request.md)。 若要這樣做，請選取 [記錄整合] 作為您要求支援的服務。

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure 記錄整合，請參閱下列文件：

* [Azure 記錄檔的 Microsoft Azure 記錄整合](https://www.microsoft.com/download/details.aspx?id=53324) – Azure 記錄整合詳細資料、系統需求和安裝指示的下載中心。
* [Azure 記錄整合簡介](security-azure-log-integration-overview.md) – 這份文件為您介紹 Azure 記錄整合、其主要功能以及運作方式。
* [合作夥伴設定步驟](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 此部落格文章說明如何設定 Azure 記錄整合，以搭配使用合作夥伴解決方案 Splunk、HP ArcSight 和 IBM QRadar。
* [Azure 記錄整合常見問題集 (FAQ)](security-azure-log-integration-faq.md) - 此常見問題集會回答有關 Azure 記錄整合的問題。
* [以 Azure 記錄整合來整合資訊安全中心警示](../security-center/security-center-integrating-alerts-with-log-integration.md) - 這份文件說明如何將資訊安全中心警示以及 Azure 診斷和 Azure 稽核記錄檔所收集的虛擬機器安全性事件，與您的 Log Analytics 或 SIEM 方案進行同步處理。
* [Azure 診斷和 Azure 稽核記錄檔的新功能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – 此部落格文章為您介紹 Azure 稽核記錄檔和其他功能，協助您深入了解您的 Azure 資源的作業。

