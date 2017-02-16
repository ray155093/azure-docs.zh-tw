---
title: "在 Azure 中還原應用程式"
description: "了解如何從備份還原您的應用程式。"
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: af79132b5830b1782850c8738f1d8bd5ca0b38d9
ms.openlocfilehash: b60009a93bd2c857acfc4d1856d8f1afb7fff595


---
# <a name="restore-an-app-in-azure"></a>在 Azure 中還原應用程式
本文說明如何在 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中還原您先前備份的應用程式 (請參閱[在 Azure 中備份應用程式](web-sites-backup.md))。 您可以依需求將應用程式及其連結的資料庫 (SQL Database 或 MySQL) 還原成先前的狀態，或是根據您其中一個原始的應用程式備份來建立新應用程式。 建立與最新版本平行執行的新應用程式，對進行 A/B 測試來說，會相當有用。

從備份還原是提供給在**標準**和**進階**層中執行的應用程式使用。 如需有關相應增加應用程式的詳細資訊，請參閱 [在 Azure 中調整應用程式規模](web-sites-scale.md)。 **進階**層所允許執行的每日備份數量比**標準**層多。

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>從現有備份還原應用程式
1. 在「Azure 入口網站」中您應用程式的 [設定] 刀鋒視窗上，按一下 [備份] 以顯示 [備份] 刀鋒視窗。 然後，在命令列中按一下 [立即還原]  。
   
    ![選擇立即還原][ChooseRestoreNow]
2. 在 [還原]  刀鋒視窗中，先選取備份來源。
   
    ![](./media/web-sites-restore/021ChooseSource.png)
   
    [應用程式備份]  選項會顯示目前應用程式的所有現有備份，您可以輕鬆地選取其中一個。
    [儲存體]  選項可讓您從您訂用帳戶中任何現有 Azure 儲存體帳戶和容器中，選取任何備份 ZIP 檔案。
    如果您想要還原另一個應用程式的備份，請使用 [儲存體]  選項。
3. 接著，在 [還原目的地] 中指定應用程式還原目的地。
   
    ![](./media/web-sites-restore/022ChooseDestination.png)
   
   > [!WARNING]
   > 如果您選擇 [覆寫]，則目前應用程式中的所有現有資料都會被清除。 按一下 [確定] 之前，請確定這確實是您想要執行的動作。
   > 
   > 
   
    您可以選取 [現有應用程式]  ，將應用程式備份還原到相同資源群組中的另一個應用程式。 使用此選項之前，您應該已經在資源群組中，建立具有應用程式備份中所定義之資料庫組態的鏡像資料庫組態的另一個應用程式。
4. 按一下 [確定] 。

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>從儲存體帳戶下載或刪除備份
1. 從 Azure 入口網站的主要 [瀏覽] 刀鋒視窗中，選取 [儲存體帳戶]。
   
    隨即會出現您現有的儲存體帳戶清單。
2. 選取包含您想要下載或刪除之備份的儲存體帳戶。
   
    儲存體帳戶的刀鋒視窗隨即顯示。
3. 在儲存體帳戶刀鋒視窗中，選取所需的容器
   
    ![檢視容器][ViewContainers]
4. 選取您想要下載或刪除的備份檔案。
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. 按一下 [下載] 或 [刪除]，視您要進行的作業而定。  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>監視還原作業
1. 若要查看有關應用程式還原作業成功或失敗的詳細資訊，請瀏覽至 Azure 入口網站中的 [活動記錄] 刀鋒視窗。
   
    [活動記錄] 刀鋒視窗會顯示您的所有作業，以及層級、狀態、資源和時間詳細資料。
2. 向下捲動以尋找所要的還原作業，並按一下以選取它。

[詳細資料] 刀鋒視窗將會顯示與該還原作業有關的可用資訊。

## <a name="next-steps"></a>後續步驟
您也可以使用 REST API 來備份及還原 App Service 應用程式 (請參閱 [使用 REST 來備份及還原 App Service 應用程式](websites-csm-backup.md))。

> [!NOTE]
> 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。 不需要信用卡；沒有承諾。
> 
> 

<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png



<!--HONumber=Nov16_HO5-->


