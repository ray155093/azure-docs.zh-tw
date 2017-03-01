---
title: "管理 Azure Analysis Services | Microsoft Docs"
description: "瞭解如何以 Azure 管理 Analysis Services 伺服器。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 3992e327bbe887338234fc2d516b053f0750a151
ms.openlocfilehash: fdc4e495fb3fb99022b0f7c487001b2fba42cccc
ms.lasthandoff: 02/16/2017


---
# <a name="manage-analysis-services"></a>Azure Analysis Services
在 Azure 中建立 Analysis Services 伺服器之後，會有一些您必須立即或稍後執行的管理工作。 例如：執行資料重新整理處理作業、控制誰能夠存取您伺服器上的模型，或監視伺服器的健康狀態。 有些管理工作只能在 Azure 入口網站中執行，有些只能在 SQL Server Management Studio (SSMS) 中執行，也有些工作可以在這兩個位置中執行。

## <a name="azure-portal"></a>Azure 入口網站
[Azure 入口網站](http://portal.azure.com/) 可讓您建立與刪除伺服器、監視伺服器資源、變更大小，以及管理誰能夠存取您的伺服器。  如果您有一些問題，也可以提交支援要求。

![在 Azure 中取得伺服器名稱](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
在 Azure 中連線到您的伺服器，就如同連線到您自己組織中的伺服器執行個體。 您可從 SSMS 執行許多相同的工作，例如處理資料或建立處理指令碼、管理角色，以及使用 PowerShell。 [下載並安裝 SSMS](#download-and-install-ssms)。
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 其中一個更大的差異就是您用於連線至您伺服器的驗證。 若要連線到 Azure Analysis Services 伺服器，您必須選取 [Active Directory 密碼驗證]。

 使用 SSMS 時，請在第一次連線到您伺服器之前，先確定您的使用者名稱包含在「Analysis Services 管理員」群組中。 若要深入了解，請參閱本文稍後的[伺服器管理員](#server-administrators)。

### <a name="to-connect-with-ssms"></a>以 SSMS 連線
1. 連線之前，您必須先取得伺服器名稱。 在 [Azure 入口網站] > 伺服器 > [概觀]  >  [伺服器名稱] 中，複製伺服器名稱。
   
    ![在 Azure 中取得伺服器名稱](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. 在 SSMS > [物件總管] 中，按一下 [連線]  > [分析服務] 。
3. 在 [連線至伺服器] 對話方塊中，貼上伺服器名稱，然後在 [驗證] 中，選擇下列其中一項：
   
    [Active Directory 整合式驗證]：使用 Active Directory 單一登入 Azure Active Directory 同盟。
   
    [Active Directory 密碼驗證]：使用組織帳戶。 例如，當從未加入網域的電腦連線時。
   
    注意：如果您沒看到 Active Directory 驗證，可能需要更新為 [SSMS 最新版本](#download-and-install-ssms)。
   
    ![在 SSMS 中連線](./media/analysis-services-manage/aas-manage-connect-ssms.png)

由於在 Azure 中使用 SSMS 管理伺服器的方式與管理內部部署伺服器極為相似，因此本文不再詳述。 MSDN 中的 [Analysis Services 執行個體管理](https://msdn.microsoft.com/library/hh230806.aspx)提供所有您需要的協助。

## <a name="server-administrators-and-database-users"></a>伺服器管理員和資料庫使用者
在 Azure Analysis Services 中，有兩種類型的使用者：伺服器管理員和資料庫使用者。 這兩種類型的使用者都必須位於 Azure Active Directory 中，而且必須由組織的電子郵件地址或 UPN 指定。 這與內部部署表格式模型資料庫不同，這些資料庫藉由 Windows 網域使用者名稱來支援伺服器管理員和資料庫使用者。 若要深入了解，請參閱[管理 Azure Analysis Services 中的使用者](analysis-services-manage-users.md)。

## <a name="download-and-install-ssms"></a>下載並安裝 SSMS
[下載 SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。

在下載頁面上，您有兩個選項︰
 
* 發行候選版本 - 若僅連線到 Azure Analysis Services 預覽，建議使用。 此版本會取得最新的更新，最適合搭配 Azure Analysis Services 預覽。
* 最新實際執行版本 - 若同時連線到實際執行伺服器和 Azure Analysis Services 預覽，建議使用。 使用此版本時，可能需要編輯登錄以啟用 Azure Active Directory 驗證。

## <a name="enable-azure-active-directory-authentication"></a>啟用 Azure Active Directory 驗證

若要在登錄中啟用 SSMS 的 Azure Active Directory 驗證功能，請先建立一個名為 EnableAAD.reg 的文字檔，然後複製並貼上以下內容︰

```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

儲存並執行該檔案。

## <a name="troubleshooting-connection-problems"></a>連接問題的疑難排解
使用 SSMS 來連線到您的伺服器時，如果 (在步驟 3) 您嘗試使用非同盟帳戶或非 Azure Active Directory 中的帳戶來登入而無法連線，您可能需要清除您的登入快取。 請先關閉 SSMS，再依下列步驟操作。

1. 在「檔案總管」中，瀏覽至 `C:\Users\<user_name>\AppData\Local\`。
2. 刪除 [AADCacheOM] 資料夾。
3. 搜尋 [本機] 資料夾中是否有以 **omlibs-tokens-cache** 名稱作為開頭的 .dat 檔案 如果有找到任何檔案，請刪除它們。
4. 開啟 SSMS，然後重複上面[以 SSMS 連線](#to-connect-with-ssms)中的步驟。

## <a name="next-steps"></a>後續步驟
如果您尚未將表格式模型部署到新伺服器，現在正是時候。 若要深入了解，請參閱 [Deploy to Azure Analysis Services](analysis-services-deploy.md) (部署至 Azure Analysis Services)。

如果您已將模型部署至您的伺服器，您便可以透過用戶端或瀏覽器與伺服器連線。 若要深入了解，請參閱 [Get data from Azure Analysis Services server](analysis-services-connect.md) (從 Azure Analysis Services 伺服器取得資料)。


