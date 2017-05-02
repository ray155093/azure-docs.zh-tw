---
title: "在 Azure Marketplace 進行 Marketo 潛在客戶管理之說明  | Microsoft Docs"
description: "本文逐步指導發佈者如何運用 Marketo 建立潛在客戶管理。"
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
ms.openlocfilehash: 77c9bdf6b2634e222592ac0818f608ee3cb3fe09
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-marketo"></a>Marketo 潛在客戶管理說明

本文件提供您指示，指導您如何設定 Marketo 系統，進而讓 Microsoft 提供您業務潛在客戶。 

1. 請移至 Marketo 中的「Design Studio」。 <br/>
![Marketo 安裝映像](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

2. 按一下「新表單」 <br/>
![Marketo 新表單映像](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

3. 填寫新表單快顯視窗中的欄位 <br/>
![Marketo 建立表單映像](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4. 在下一個表單中按一下 [完成]。 別擔心格式或欄位。 <br/>
![Marketo 完成表單映像](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5. 核准並關閉。

6. 在下一個頁面，請移至 [內嵌程式碼] 區段。<br/>
![Marketo 內嵌程式碼映像](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7. 您將看到與下列範例類似的內嵌程式碼︰ 

   ` <script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    `<form id="mktoForm_1179"></form> `

    `<script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>`

8. 使用內嵌程式碼中可用的值，來填入Cloud Partner 入口網站上 Marketo 欄位中的伺服器識別碼、Munchkin 識別碼，以及表單識別碼<b></b>。 <br\> <br/>

  下列範例將協助您判斷上述範例資料的識別碼。 <br\>

  伺服器識別碼 = ys12 <br\>

 munchkin 識別碼 = 123-PQR-789 <br\>

 表單識別碼 = 1179 <br\> <br/>

