---
title: "在 Azure Marketplace 運用 Azure 資料表進行潛在客戶管理之說明  | Microsoft Docs"
description: "本文逐步指導發佈者如何運用 Azure 資料表儲存體建立潛在客戶管理。"
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
ms.openlocfilehash: 45aa2e0b178969215df9ae305d0a13a350ae0fda
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-azure-table"></a>運用 Azure 資料表進行潛在客戶管理之說明

本文件提供您指示，指導您如何設定 Azure 資料表，進而讓 Microsoft 提供您業務潛在客戶。 如果您想要針對您儲存潛在客戶的方式進行自訂，Azure 資料表是最佳預設選擇。

1. 如果您沒有 Azure 帳戶，請首先[建立一個帳戶](https://azure.microsoft.com/pricing/free-trial/)。

2. 建立好您的 Azure 帳戶後，請登入 [Azure 入口網站](https://portal.azure.com)並建立儲存體帳戶。 請參閱下面的螢幕擷取畫面說明，並前往[深入了解儲存體價格](https://azure.microsoft.com/pricing/details/storage/)。 <br/>
  ![Azure 儲存體建立流程圖](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

3. 接下來，複製連接字串金鑰，並將它貼到 Cloud Partner 入口網站上的儲存體帳戶連接字串<b></b>欄位。 <br/> 
  ![Azure 儲存體金鑰映像](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

  最終連接字串範例︰ <br/>
`{"connectionString":"DefaultEndpointsProtocol=https;AccountName=leadaccount;AccountKey=ObS0EW7tDmXrC8oNeG6IRHpx2IUioBQTQynQcR/MUMqrNqQ/RC6zctP8HfucNJO+ond7dJHTROO9ziiPNspjEg=="}`

您可以使用 [Azure 儲存體 Explorer](http://azurestorageexplorer.codeplex.com/) (第三方應用程式) 或任何其他工具來檢視儲存體資料表中的資料，或將資料匯出。

### <a name="optional-azure-functions--azure-table"></a>**(選用)** Azure Functions + Azure 資料表
如果您想要自訂您收到潛在客戶的方式，[Azure Functions](https://azure.microsoft.com/services/functions/) 服務提供自動化潛在客戶產生程序的最大化彈性。 以下是如何建立 Azure 函式 (每隔五分鐘執行一次計時器)、在 Azure 資料表中尋找新記錄、使用 SendGrid 服務的免費層傳送簡易電子郵件通知的範例。

1. 在您的 Azure 訂閱中[建立](https://portal.azure.com/#create/SendGrid.SendGrid)免費的 SendGrid 服務帳戶。
  
    ![建立 SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)
  
2. 建立 SendGrid API 金鑰 (按一下 [管理金鑰] 移至 SendGrid UI、按一下 [設定]、[API 金鑰] 和建立具有郵件傳送 -> 完整存取權的金鑰，儲存 API)。
 
    ![SendGrid API 金鑰](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)

3. 使用名為「消耗計畫」的主機計畫選項，[建立](https://portal.azure.com/#create/Microsoft.FunctionApp) Azure 函數應用程式。 
  
    ![Azure 函式建立](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)

4. 建立新的函式定義。 
  
    ![Azure 函式定義建立](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)

5. 如果您想要在此建立在特定時間傳送更新的函式，選取 TimerTrigger-CSharp 做為起始選項。
  
    ![Azure 函式時間觸發程序選項](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)
  
6. 將「Develop」程式碼，更換為下表其中一個程式碼。 您可能想要編輯符合您心目中的寄件者和接收者電子郵件位址。
這僅是範例程式碼，若有需要，您可以進行變更，藉此完善您的程式碼。
  
        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;

        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }

        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";

            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();

            // Check how many rows were added
            int rowsCount = rowsList.Count;
            
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");

                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };

                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);

                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }
   ![Azure 函式程式碼片段](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)

7. 按一下 [整合] 和 [輸入] 來定義 Azure 資料表連接︰
  
    ![Azure 函式整合](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)

8. 按一下 [新增] 來輸入資料表名稱，並定義連接字串。
  
    ![Azure 函式資料表連接](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9. 現在將輸出定義為 SendGrid，並保留所有預設值。
  
    ![SendGrid 輸出](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)
    ![SendGrid 輸出的預設值](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. 使用「SendGridApiKey」名稱，及在 SendGrid UI 中 API 金鑰取得的值，新增 SendGrid API 金鑰至函數應用程式設定
  
    ![SendGrid 管理](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![SendGrid managekey](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

全都設定好後，整合區段應包含如下的程式碼：

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }
  
最後您必須回到函式 Develop UI，並按一下 [執行] 來啟動計時器。 現在已經設定完成，只要出現新的潛在客戶，您將會收到通知。

