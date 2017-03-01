---
title: "在 Azure 中備份應用程式"
description: "了解如何在 Azure App Service 中建立應用程式的備份。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: b00c320eeb1e8b30a0fad9634844ceeca60d1c29
ms.lasthandoff: 01/20/2017


---
# <a name="back-up-your-app-in-azure"></a>在 Azure 中備份應用程式
[Azure App Service](../app-service/app-service-value-prop-what-is.md) 中的備份和還原功能可讓您以手動或自動方式輕鬆建立應用程式備份。 您可以將應用程式還原至先前的狀態，或根據原始應用程式的其中一個備份來建立新的應用程式。 

如需從備份還原應用程式的相關資訊，請參閱 [在 Azure 中還原應用程式](web-sites-restore.md)。

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>備份什麼項目
App Service 可以備份下列資訊：

* 應用程式設定
* 檔案內容
* 任何連接到您應用程式的 Azure SQL Database 或 MySQL (ClearDB) 資料庫 (您可以選擇要將哪些資料庫納入備份)

這項資訊會備份至您指定的 Azure 儲存體帳戶和容器。 

> [!NOTE]
> 每個備份都是應用程式的完整離線複本，而不是增量更新。
> 
> 

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>需求和限制
* 若要使用「備份與還原」功能，App Service 方案必須屬於 **標準** 層或更高階層。 如需有關調整 App Service 方案以使用更高階層的詳細資訊，請參閱 [在 Azure 中調整應用程式規模](web-sites-scale.md)。 請注意，**進階**層所允許的每日備份數量比**標準**層多。
* 您需要與您即將備份之應用程式隸屬於相同訂用帳戶的 Azure 儲存體帳戶和容器。 如需 Azure 儲存體帳戶的詳細資訊，請參閱本文結尾處的 [連結](#moreaboutstorage) 。
* 備份上限是 10GB 的應用程式和資料庫內容。 如果備份大小超過此限制，您會收到錯誤。 

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>建立手動備份
1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的應用程式的刀鋒視窗，選取 [設定]，然後選取 [備份]。 [備份]  刀鋒視窗隨即顯示。
   
    ![Backups page][ChooseBackupsPage]
   
   > [!NOTE]
   > 如果您看到下列訊息，請按一下訊息升級您的 App Service 方案，才可以繼續進行備份。
   > 如需詳細資訊，請參閱 [在 Azure 中調整應用程式規模](web-sites-scale.md) 。  
   > ![Choose storage account](./media/web-sites-backup/01UpgradePlan.png)
   > 
   > 
2. 在 [備份] 刀鋒視窗中，按一下 [儲存體: 未設定] 以設定儲存體帳戶。
   
    ![Choose storage account][ChooseStorageAccount]
3. 選取 [儲存體帳戶] 和 [容器]，以選擇您的備份目的地。 此儲存體帳戶必須與您要備份之應用程式隸屬於相同的訂用帳戶。 如果您希望的話，也可以在個別的刀鋒視窗中，建立新的儲存體帳戶或新的容器。 完成後，按一下 [選取] 。
   
    ![Choose storage account](./media/web-sites-backup/02ChooseStorageAccount1.png)
4. 在仍處於開啟狀態的 [設定備份設定] 刀鋒視窗中，按一下 [資料庫設定]，然後選取您想要包含在備份中的資料庫 (SQL 資料庫或 MySQL)，然後按一下 [確定]。  
   
    ![Choose storage account](./media/web-sites-backup/03ConfigureDatabase.png)
   
   > [!NOTE]
   > 若要讓資料庫出現在此清單中，其連接字串必須存在於您的應用程式之 [應用程式設定] 刀鋒視窗的 [連接字串] 區段中。
   > 
   > 
5. 在 [設定備份設定] 刀鋒視窗中，按一下 [儲存]。    
6. 在 [備份] 刀鋒視窗的命令列中，按一下 [立即備份]。
   
    ![BackUpNow button][BackUpNow]
   
    在備份過程中，您將會看見進度訊息。

設定備份的儲存體帳戶和容器之後，您隨時可以進行手動備份。  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>設定自動備份
1. 在 [備份] 刀鋒視窗中，按一下 [排程: 未設定]。 
   
    ![Choose storage account](./media/web-sites-backup/05ScheduleBackup.png)
2. 在 [備份排程設定] 刀鋒視窗中，將 [排定的備份] 設定為 [開啟]，然後視需要設定備份排程並按一下 [確定]。
   
    ![Enable automated backups][SetAutomatedBackupOn]
3. 在仍處於開啟狀態的 [設定備份設定] 刀鋒視窗中，按一下 [儲存體設定]，然後選取「儲存體帳戶」和「容器」來選擇您的備份目的地。 此儲存體帳戶必須與您要備份之應用程式隸屬於相同的訂用帳戶。 如果您希望的話，也可以在個別的刀鋒視窗中，建立新的儲存體帳戶或新的容器。 完成後，按一下 [選取] 。
   
    ![Choose storage account](./media/web-sites-backup/02ChooseStorageAccount1.png)
4. 在 [設定備份設定] 刀鋒視窗中，按一下 [資料庫設定]，然後選取您想要包含在備份中的資料庫 (SQL 資料庫或 MySQL)，然後按一下 [確定]。  
   
    ![Choose storage account](./media/web-sites-backup/03ConfigureDatabase.png)
   
   > [!NOTE]
   > 若要讓資料庫出現在此清單中，其連接字串必須存在於您的應用程式之 [應用程式設定] 刀鋒視窗的 [連接字串] 區段中。
   > 
   > 
5. 在 [設定備份設定] 刀鋒視窗中，按一下 [儲存]。    

<a name="partialbackups"></a>

## <a name="backup-just-part-of-your-app"></a>只備份應用程式的一部分
您有時不想要備份應用程式上的所有項目。 以下是一些範例：

* 您為包含永不變更的靜態內容 (例如舊部落格文章或影像) 的應用程式 [設定每週備份](web-sites-backup.md#configure-automated-backups) 。
* 您的應用程式的內容超過 10GB (亦即一次可以備份的最大數量)。
* 您不想要備份記錄檔。

部分備份可讓您選擇只針對想要備份的檔案進行備份。

### <a name="exclude-files-from-your-backup"></a>從備份中排除檔案
若要從備份中排除檔案和資料夾，請在應用程式的 D:\home\site\wwwroot 資料夾中建立 `_backup.filter` 檔案，並在其中指定您想要排除的檔案和資料夾的清單。 透過 [Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console)即可輕鬆存取此檔案。 

假設您有一個應用程式，其中包含過去幾年的記錄檔和靜態影像，且以後不再變更。 您已完整備份應用程式，包括舊的影像。 現在您想要每天備份應用程式，但不想付費儲存從未變更的記錄檔或靜態影像檔案。

![記錄資料夾][LogsFolder]
![映像資料夾][ImagesFolder]

下列步驟顯示如何從備份中排除這些檔案。

1. 移至 `http://{yourapp}.scm.azurewebsites.net/DebugConsole` ，然後識別您想要從備份中排除的資料夾。 在此範例中，您想要排除下列檔案和資料夾 (如該 UI 所示)：
   
        D:\home\site\wwwroot\Logs
        D:\home\LogFiles
        D:\home\site\wwwroot\Images\2013
        D:\home\site\wwwroot\Images\2014
        D:\home\site\wwwroot\Images\brand.png
   
    [AZURE.NOTE] 最後一行顯示您可以排除個人檔案以及資料夾。
2. 建立名為 `_backup.filter` 的檔案並將上述清單放入此檔案中，但請移除 `D:\home`。 一行列出一個目錄或檔案。 因此，檔案的內容應該如下：
   
    \site\wwwroot\Logs  \LogFiles  \site\wwwroot\Images\2013  \site\wwwroot\Images\2014  \site\wwwroot\Images\brand.png
3. 使用 [ftp](web-sites-deploy.md#ftp) 或任何其他方法，將此檔案上傳至網站的 `D:\home\site\wwwroot\` 目錄。 如果您希望的話，也可以直接在 `http://{yourapp}.scm.azurewebsites.net/DebugConsole` 中建立該檔案，然後在該處插入內容。
4. 以平常執行備份的相同方式執行備份：[手動](#create-a-manual-backup)或[自動](#configure-automated-backups)。

現在，將會從備份中排除 `_backup.filter` 中指定的所有檔案和資料夾。 在此範例中，將不再備份記錄檔和 2013年和 2014 年影像檔，以及 brand.png。

> [!NOTE]
> 以您 [還原定期備份](web-sites-restore.md)的相同方式還原您網站的部分備份。 還原程序將執行正確事項。
> 
> 還原完整備份時，網站上的所有內容都會取代為備份中的內容。 如果檔案在網站上，但不在備份裡，即會遭到刪除。 但是，還原部分備份時，位於其中一個黑名單目錄或任何黑名單檔案中的任何內容都會保持原狀。
> 
> 

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>備份的儲存方式
建立應用程式的一或多個備份之後，這些備份就會顯示在您儲存體帳戶以及應用程式的 [容器]  刀鋒視窗中。 在儲存體帳戶中，每個備份都會有一個包含備份資料的 .zip 檔案，以及一個包含 .zip 檔案內容資訊清單的 .xml 檔案。 如果您要存取備份而不實際執行應用程式還原，則可以將這些檔案解壓縮並加以瀏覽。

應用程式的資料庫備份則儲存在 .zip 檔案的根目錄中。 若是 SQL 資料庫，這會是 BACPAC 檔案 (無副檔名)，而且可以匯入。 若要根據 BACPAC 匯出內容建立新的 SQL 資料庫，請參閱 [匯入 BACPAC 檔案以建立新的使用者資料庫](http://technet.microsoft.com/library/hh710052.aspx)。

> [!WARNING]
> 對 **websitebackups** 容器中的檔案進行任何變更，都可能導致備份失效，進而無法還原。
> 
> 

<a name="nextsteps"></a>

## <a name="next-steps"></a>後續步驟
如需從備份還原應用程式的相關資訊，請參閱 [在 Azure 中還原應用程式](web-sites-restore.md)。 您也可以使用 REST API 來備份及還原 App Service 應用程式 (請參閱 [使用 REST 來備份及還原 App Service 應用程式](websites-csm-backup.md))。

> [!NOTE]
> 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](https://azure.microsoft.com/try/app-service/)，即可在 App Service 中立即建立短期入門 Web 應用程式。 不需要信用卡；沒有承諾。
> 
> 

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png


