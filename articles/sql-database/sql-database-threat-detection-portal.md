---
title: "Azure 入口網站：設定威脅偵測 - SQL Database | Microsoft Docs"
description: "使用 Azure 入口網站來設定及管理威脅偵測"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: 
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
ms.openlocfilehash: 0222847321b82c9572e361cc5e578b779d859792
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-azure-sql-database-threat-detection-in-the-azure-portal"></a>在 Azure 入口網站中設定及管理 Azure SQL Database 威脅偵測

本主題說明如何設定威脅偵測及探索異常的資料庫活動。 如需 Azure SQL Database 威脅偵測的概觀，請參閱[威脅偵測概觀 (英文)](sql-database-threat-detection.md)。

## <a name="set-up-threat-detection-for-your-database"></a>設定資料庫的威脅偵測
1. 啟動 Azure 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。
2. 瀏覽至您要監視的 SQL Database 的組態刀鋒視窗。 在 [設定] 刀鋒視窗中，選取 [稽核和威脅偵測]。
   
    ![瀏覽窗格][1]
3. 在 [稽核與威脅偵測] 組態刀鋒視窗中，[開啟] 稽核，這會顯示威脅偵測設定。
   
    ![瀏覽窗格][2]
4. [開啟] 威脅偵測。
5. 設定在偵測到異常資料庫活動時將收到安全性警示的電子郵件清單。
6. 按一下 [稽核與威脅偵測] 刀鋒視窗中的 [儲存]，以儲存新的或已更新的稽核與威脅偵測原則。
   
    ![瀏覽窗格][3]

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>偵測到可疑事件時探索異常資料庫活動
1. 偵測到異常資料庫活動時，您將收到電子郵件通知。 <br/>
   電子郵件將提供可疑安全性事件的相關資訊，包括異常活動的性質、資料庫名稱、伺服器名稱和事件時間。 此外，還會提供可能原因和建議動作的相關資訊，以協助您調查和減輕資料庫的潛在威脅。<br/>
   
    ![導覽窗格][4]
2. 在電子郵件中，按一下 [Azure SQL 稽核記錄檔]  連結會啟動 Azure 入口網站，並顯示可疑事件前後的相關稽核記錄。
   
    ![導覽窗格][5]
3. 按一下稽核記錄以檢視可疑資料庫活動的詳細資訊，例如 SQL 陳述式、失敗原因和用戶端的 IP。
   
    ![導覽窗格][6]
4. 在 [稽核記錄] 刀鋒視窗中，按一下 [在 Excel 中開啟] 開啟預先設定的 Excel 範本，以匯入可疑事件前後的稽核記錄，執行更深入的分析。<br/>
   **附註：**在 Excel 2010 或更新版本中，必須要有 Power Query 和 [快速合併]**** 設定。
   
    ![瀏覽窗格][7]
5. 若要設定 [快速合併] 設定 - 在 [POWER QUERY] 功能區索引標籤中，選取 [選項] 以顯示 [選項] 對話方塊。 選取 [隱私權] 區段，選擇第二個選項 - [忽略隱私權等級並可能改善效能]：
   
    ![導覽窗格][8]
6. 若要載入 SQL 稽核記錄檔，請確定 [設定] 索引標籤中的參數已正確設定，然後選取 [資料] 功能區，並按一下 [全部重新整理] 按鈕。
   
    ![導覽窗格][9]
7. 結果會出現在 [SQL 稽核記錄檔]  工作表，可讓您對偵測到的異常活動執行更深入的分析，減輕應用程式中的安全性事件造成的影響。

## <a name="next-steps"></a>後續步驟

* 如需 SQL Database 威脅偵測的概觀，請參閱[威脅偵測 (英文)](sql-database-threat-detection.md)。
* 如需 SQL Database 稽核的概觀，請參閱[資料庫稽核 (英文)](sql-database-auditing.md)。


<!--Image references-->
[1]: ./media/sql-database-threat-detection-get-started/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection-get-started/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection-get-started/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection-get-started/4_td_email.png
[5]: ./media/sql-database-threat-detection-get-started/5_td_audit_records.png
[6]: ./media/sql-database-threat-detection-get-started/6_td_audit_record_details.png
[7]: ./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png
[8]: ./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png
[9]: ./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png

