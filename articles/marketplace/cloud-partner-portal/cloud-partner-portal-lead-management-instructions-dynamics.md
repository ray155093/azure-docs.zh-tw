---
title: "在 Azure Marketplace 運用 Dynamics CRM Online進行潛在客戶管理之說明  | Microsoft Docs"
description: "本文逐步指導發佈者如何運用 Dynamics CRM Online 建立潛在客戶管理。"
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 05b076bf173d0fbff4baaf44c57ce215dbf23979
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-dynamics-crm-online"></a>運用 Dynamics CRM Online進行潛在客戶管理之說明

本文件提供您指示，指導您如何設定 Dynamics CRM Online 系統，進而讓 Microsoft 提供您業務潛在客戶。

> [!NOTE]
> 執行下列步驟所需的使用者權限︰如果要安裝解決方案，您必須具有 Dynamics CRM Online 執行個體管理員身分；如果要為潛在客戶服務建立新的服務帳戶，您必須具有租用戶系統管理員身分。

安裝解決方案： <br/>
1. 下載[Microsoft Marketplace 潛在客戶寫入器解決方案](https://testdriveaccount.blob.core.windows.net/testdrivecon/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)，並儲存在本機。

2. 前往 Dynamics CRM Online 設定。  <br/>
(如果您沒有看到下方映像中的選項，表示您沒有擁有所需的權限) <br/>
![Dynamics 安裝映像](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.    按一下 [匯入]，並選取您在步驟 1 中下載的解決方案。  <br/>
![Dynamics 匯入映像](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.    完成安裝解決方案。

使用者權限： <br/>
我們需要您與我們共用服務帳戶資訊，如此我們才能授權將潛在用戶寫入您的 Dynamics CRM 執行個體。 下列步驟會引導您建立服務帳戶及指派權限。 <br/>
1.    移至 [Microsoft Office 365 管理員入口網站](https://go.microsoft.com/fwlink/?LinkId=225975)。

2.    按一下 [管理員]圖格： <br/>
![Dynamics 管理員設定映像](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3.    按一下 [新增使用者]。<br/>
![Dynamics 新增使用者映像](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4.    為潛在客戶寫入器服務建立新使用者。 請確認下列 
*  提供密碼，然後取消勾選「讓這名使用者，第一次登入時變更密碼 」
*     角色選取為「使用者 (無管理員存取權)」
*     如下所示選取產品授權。 (將依據您所選取的授權向您收取費用；解決方案將與 Dynamics CRM Online 基本授權一起使用) <br/>
![Dynamics 新增使用者映像](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

5.  前往 Dynamics CRM Online，前往 [設定] -> [安全性]。<br/>
![Dynamics 安全性設定映像](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

6.  選取您在步驟 4 中建立的使用者，在上方功能區中，按一下 [管理角色] 並指派角色為 Microsoft Marketplace 潛在客戶寫入器，如下所示： <br/>
![Dynamics 管理角色映像](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)<br/>

此角色由您匯入的解決方案所建立，僅擁有寫入潛在客戶的權限，並知道解決方案版本，以確保相容性。

7.  若是 Dynamics 365 8.2 版本，您匯入的解決方案所建立的 Microsoft Marketplace 潛在客戶寫入器角色需要額外權限。 在 [安全性] 中，選取安全性角色，並尋找 Microsoft Marketplace 潛在客戶寫入器的角色，選取該角色，並在核心紀錄標籤頁中為使用者實體 UI 設定，變更建立/讀取/寫入設定，如下所示： <br/>
![Dynamics 安全性角色映像](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)<br/>
![Dynamics 管理安全性角色映像](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)<br/>


最後，在 Cloud Partner 入口網站上的 Url 、使用者名稱及密碼欄位輸入您的存取資訊。
