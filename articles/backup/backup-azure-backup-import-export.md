<properties
   pageTitle="Azure 備份 - 離線備份或使用 Azure 匯入/匯出服務進行初始植入 | Microsoft Azure"
   description="了解 Azure 備份如何讓您使用 Azure 匯入/匯出服務在網路上傳送資料。此文章說明如何使用 Azure 匯入/匯出服務離線植入初始備份資料"
   services="backup"
   documentationCenter=""
   authors="saurabhsensharma"
   manager="shivamg"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="08/16/2016"
   ms.author="jimpark;saurabhsensharma;nkolli;trinadhk"/>

# 在 Azure 備份中離線備份工作流程
Azure 備份內建數個可提升效率的功能，能在資料初始「完整」備份至 Azure 的期間節省網路和儲存體成本。初始「完整」備份通常會傳輸大量資料，因此需要較多網路頻寬，相較之下，後續備份只會傳輸差異/增量部分。不只是 Azure 備份會壓縮初始備份，離線植入程序也會提供一種機制，讓您可以透過磁碟將壓縮後的初始備份資料離線上傳至 Azure。

Azure 備份的離線植入程序與 [Azure 匯入/匯出服務](../storage/storage-import-export-service.md)緊密整合，可讓您使用磁碟將資料快速地傳輸到 Azure 之中。如果您的初始備份資料大小達到 TB，且需要透過高延遲和低頻寬網路傳輸時，您可以使用離線植入工作流程將初始備份複本送至 Azure 資料中心的一個或多個硬碟上。本文章提供完成此工作流程所需步驟的概觀。

## Overview

您可以使用 Azure 備份的離線植入功能和 Azure 匯入/匯出，透過磁碟簡單且直接地將資料離線上傳至 Azure。您不需透過網路傳輸初始完整複本，備份資料會寫入至*預備位置*。一旦複製到「預備位置」的初始作業完成，使用「Azure 匯入/匯出工具」即可將此資料寫入至一個或多個 SATA 磁碟機 (視初始備份大小而定)。這些磁碟機最終都會寄送到最近的 Azure 資料中心。

[Azure 備份的 2016 年 8 月更新 (和更新版本)](http://go.microsoft.com/fwlink/?LinkID=229525&clcid=0x409) 隨附名為 **AzureOfflineBackupDiskPrep** 的「Azure 磁碟準備工具」︰

   - 可協助您使用 Azure 匯入/匯出工具輕鬆地為磁碟機做好執行 Azure 匯入的準備
   - 可在 [Azure 傳統入口網站](https://manage.windowsazure.com)上自動建立 Azure 匯入/匯出服務的 Azure 匯入作業，而不是像舊版 Azure 備份一樣透過手動方式建立相同的作業

一旦將備份資料上傳至 Azure 的作業完成，Azure 備份就會將備份資料複製至備份保存庫，並排程增量備份。

  > [AZURE.NOTE] 若要使用 Azure 磁碟準備工具，請確定您已安裝 Azure 備份的 2016 年 8 月更新 (或更新版本)，並執行其工作流程的所有步驟。如果您使用舊版的 Azure 備份，則可以使用本文後面幾節會詳述的「Azure 匯入/匯出工具」準備 SATA 磁碟機。

## 必要條件

1. 請務必熟悉 Azure 匯入/匯出工作流程，相關資訊會在[這裡](../storage/storage-import-export-service.md)列出。
2. 起始工作流程之前，請確定已建立 Azure 備份保存庫、已下載保存庫認證、Azure 備份代理程式已安裝於您的 Windows Server/Windows 用戶端或 System Center Data Protection Manager (SCDPM) 伺服器，且電腦已註冊 Azure 備份保存庫。
3. 在您打算備份我們資料的電腦上，從[這裡](https://manage.windowsazure.com/publishsettings)下載 Azure 發佈檔案設定。
4. 準備可能是網路共用或電腦上其他磁碟機的「預備位置」。預備位置是「暫時性儲存體」，因此在此工作流程期間會暫時使用。請確定預備位置有足夠的磁碟空間來保存您的初始複本。例如：若您正在嘗試備份 500 GB 的檔案伺服器，請確定預備區域至少有 500 GB 的空間 (不過，由於會經過壓縮，實際使用量不會那麼多)。
5. 一或多個 3.5 吋內接式 SATA II/III 硬碟。只有 3.5 吋的內接式 SATA II/III 硬碟能夠用於 Azure 匯入/匯出服務。不支援大於 10 TB 的硬碟。這些硬碟可以是使用相容的 SATA 連接器在內部連接，或使用 SATA II/III USB 配接器在外部連接。如需服務所支援的最新硬碟和 SATA USB 配接器組合，請查看 [Azure 匯入/匯出文件](../storage/storage-import-export-service.md)。
6. 在 SATA 磁碟機寫入器所連接的電腦上啟用 BitLocker。
7. 從[這裡](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)將 Azure 匯入/匯出工具下載至 SATA 磁碟機寫入器所連接的電腦上。如果您已下載並安裝 Azure 備份的 2016 年 8 月更新 (或更新版本)，就不需要此步驟。

## 工作流程
本章節提供的資訊可讓您完成**離線備份**工作流程，以便將您的資料傳遞至 Azure 資料中心，並上傳至 Azure 儲存體。若您有關於匯入服務或處理程序任何層面的問題，請參閱上方的[匯入服務概觀](../storage/storage-import-export-service.md)參考文件。

### 初始離線備份

1. 作為排程備份的一部分，您會遇到下列畫面 (在 Windows Server、Windows 用戶端或 SCDPM 中)。

    ![匯入畫面](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)
    
    SCDPM 中的對應畫面如下所示。<br/> ![DPM 匯入畫面](./media/backup-azure-backup-import-export/dpmoffline.png)

    輸入的說明如下：

    - **預備位置** - 這是指初始備份所寫入的暫時儲存體位置。這可能是網路共用或在本機電腦上。如果「複本電腦」和「來源電腦」不同，則建議您指定預備位置的完整網路路徑。
    - **Azure 匯入作業名稱** - 這是指 Azure 匯入服務和 Azure 備份在追蹤使用磁碟傳送至 Azure 之資料的傳輸活動時所使用的唯一名稱。
    - **Azure 發佈設定** - 包含訂用帳戶設定檔相關資訊的 XML 檔案。檔案中也包含與訂用帳戶相關聯的安全認證。檔案可從[這裡](https://manage.windowsazure.com/publishsettings)下載。提供發佈設定檔案的本機路徑。
    - **Azure 訂用帳戶識別碼** - 打算起始 Azure 匯入作業的 Azure 訂用帳戶識別碼。如果您有多個 Azure 訂用帳戶，請使用要與匯入作業相關聯的識別碼。
    - **Azure 儲存體帳戶** - 所提供的 Azure 訂用帳戶中將會與 Azure 匯入作業相關聯的「傳統」類型儲存體帳戶。
    - **Azure 儲存體容器** - Azure 儲存體帳戶中匯入此作業資料之目的地儲存體 Blob 的名稱。
    
    > [AZURE.NOTE] 如果您已從[新的 Azure 入口網站](https://ms.portal.azure.com)在復原服務保存庫中為您的備份註冊伺服器，而且您不是在雲端服務提供者 (CSP) 訂用帳戶上，您仍可從新的 Azure 入口網站中建立「傳統」類型的儲存體帳戶，然後將它用於離線備份工作流程。
    
    請分別儲存所有資訊，因為需要在下列步驟中重新輸入此資訊。如果使用「Azure 磁碟準備工具」來準備磁碟，則只需要「預備位置」
    

2. 完成工作流程，並在 Azure 備份管理主控台中選取 [立即備份]，以起始離線備份複製。作為此步驟的一部分，初始備份會寫入預備區域。

    ![立即備份](./media/backup-azure-backup-import-export/backupnow.png)
    
    SCDPM 中的對應工作流程可透過以滑鼠右鍵按一下 [保護群組]，然後選擇 [建立復原點] 選項來啟用。在此步驟之後接著選擇 [線上保護] 選項。

    ![立即備份 DPM](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    一旦作業完成，預備位置便已就緒可供用於準備磁碟

    ![輸出](./media/backup-azure-backup-import-export/opbackupnow.png)

###使用「Azure 磁碟準備工具」準備 SATA 磁碟機和建立 Azure 匯入作業
「Azure 磁碟準備工具」可在 Microsoft Azure 復原服務代理程式 (2016 年 8 月更新和更新版本) 的安裝目錄取得，其路徑如下。

   \\Microsoft Azure Recovery Services *Agent\\Utils*

1. 瀏覽至上述目錄，並將 **AzureOfflineBackupDiskPrep** 目錄複製到掛接了要準備之磁碟機的「複本電腦」上。確認下列與「複本電腦」有關的事項

      - 提供給離線植入工作流程使用的「預備位置」，可使用**起始離線備份**工作流程期間所提供的相同網路路徑從「複本電腦」來存取
      
      - 電腦上會啟用 BitLocker。
      
      - 電腦可存取 Azure 入口網站
      
      必要時，「複本電腦」可以與「來源電腦」相同。

2. 在複本電腦上以 Azure 磁碟準備工具目錄做為目前的目錄來開啟提高權限的命令提示字元，並執行下列命令

      .\\AzureOfflineBackupDiskPrep.exe s:<預備位置路徑> [p:<PublishSettingsFile 的路徑>]

| 參數 | 說明
|-------------|-------------|
|s:<預備位置路徑> | 強制性輸入內容，用來提供起始離線備份期間所輸入的預備位置路徑 |
|p:<PublishSettingsFile 的路徑> | 選擇性輸入內容，用來提供起始離線備份期間所輸入的發佈設定檔案路徑 |

   > [AZURE.NOTE] 當「複本電腦」和「來源電腦」不同時，發佈設定檔案是強制性輸入內容

在執行命令時，此工具會要求選擇需要準備之磁碟機所對應的 Azure 匯入作業。如果只有一個與提供的預備位置相關聯的匯入作業，則會出現類似下面的畫面。

   ![Azure 磁碟準備工具的輸入](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. 輸入想要準備傳輸到 Azure 之掛接磁碟的**磁碟機代號** (不含結尾冒號)。之後，在出現提示時確認您要格式化磁碟機。

工具接著便會開始以備份資料準備磁碟。當工具顯示提示時，您可能需要連接額外的磁碟，以免所提供的磁碟沒有足夠空間來容納備份資料。<br/>

在工具順利執行結束時，您所提供的一或多個磁碟便已準備好可以寄送到 Azure，而且 Azure 傳統入口網站上會建立以您在**起始離線備份**工作流程期間所提供的名稱命名的匯入作業。此外，工具上還會顯示磁碟所要寄送到之 Azure 資料中心的寄送地址，以及用來找到 Azure 傳統入口網站上之匯入作業的連結。

   ![Azure 磁碟準備完成](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
4. 將磁碟寄送到工具所提供的地址，並保留追蹤號碼以供日後參考。<br/>
5. 在瀏覽至工具所顯示的連結時，您會進入在離線備份工作流程期間所指定的 **Azure 儲存體帳戶**。在下圖中，您可以看到儲存體帳戶的 [匯入/匯出] 索引標籤中新建立的匯入作業

   ![建立的匯入作業](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>
6. 按一下頁面底部的 [寄送資訊] 來更新 [連絡人詳細資料]，如下所示。當匯入作業完成時，Microsoft 會使用此資訊將磁碟寄回給您。

   ![連絡資訊](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>
7. 在下一個畫面中輸入 [寄送詳細資料]，如下所示。提供寄送到 Azure 資料中心之磁碟的對應貨運業者和追蹤號碼詳細資料
   
   ![寄送資訊](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### 完成工作流程
匯入作業完成後，儲存體帳戶中就會有初始備份資料可供使用。Microsoft Azure 復原服務代理程式接著會將此帳戶中的資料內容複製到備份保存庫或復原服務保存庫 (視何者較適合)。到了下一個排程的備份時間時，Azure 備份代理程式會透過初始備份複本執行增量備份。


> [AZURE.NOTE] 下列各節適用於無法存取「Azure 磁碟準備工具」的舊版 Azure 備份使用者

### 準備 SATA 磁碟機

1. 您可以將 [Microsoft Azure 匯入/匯出工具](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409)下載至*複本電腦*。請確定預備位置 (在上一個步驟中) 可從您打算執行下一組命令的電腦進行存取。必要時，複本電腦可以與來源電腦相同。

2. 解壓縮 *WAImportExport.zip* 檔案。執行「WAImportExport」工具，以格式化 SATA 磁碟機、將備份資料寫入 SATA 磁碟機並進行加密。執行下列命令之前請確定電腦上已啟用 BitLocker。<br/>

    *.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*
    
    > [AZURE.NOTE] 如果您已安裝 Azure 備份的 2016 年 8 月更新 (或更新版本)，請確認所輸入的「預備位置」和 [立即備份] 畫面上所顯示的位置相同，且包含「AIB」和「Base Blob」檔案。

| 參數 | 說明
|-------------|-------------|
| /j:<*JournalFile*>| 日誌檔案的路徑。每個磁碟機必須只有一個日誌檔案。日誌檔案不得位於目標磁碟機上。日誌檔案的副檔名是 .jrn，且作為執行此命令的一部分而建立。|
|/id:<*SessionId*> | 工作階段識別碼會識別*複製工作階段*。其用來確保正確復原中斷的複製工作階段。在複製工作階段中複製的檔案會儲存在以目標磁碟機上工作階段識別碼命名的目錄。|
| /sk:<*StorageAccountKey*> | 要匯入資料之儲存體帳戶的帳戶金鑰。金鑰必須相同，因為它是在備份原則/保護群組建立時輸入的。|
| /BlobType | 指定 **PageBlob**，此工作流程僅在指定 PageBlob 選項時才會成功。這不是預設選項，且應該在此命令中進行描述。 |
|/t:<*TargetDriveLetter*> | 目前複製工作階段中目標硬碟的磁碟機代號，不包含結尾的冒號。|
|/format | 當磁碟機需要進行格式化時請指定此參數；否則請省略。此工具格式化磁碟機之前，會提示您從主控台進行確認。若要隱藏確認，請指定 /silentmode 參數。|
|/encrypt | 當磁碟機尚未使用 BitLocker 加密，且需要透過工具加密時，請指定此參數。若已使用 BitLocker 加密磁碟機，則省略此參數並指定 /bk 參數，以提供現有的 BitLocker 金鑰。若您指定 /format 參數，則您也必須指定 /encrypt 參數。 |
|/srcdir:<*SourceDirectory*> | 包含檔案的來源目錄會複製至目標磁碟機。請確定此處指定的目錄名稱是完整路徑 (而非相對路徑)。|
|/dstdir:<*DestinationBlobVirtualDirectory*> | Microsoft Azure 儲存體帳戶中的目的地虛擬目錄路徑。指定目的地虛擬目錄或 blob 時，請確定使用有效的容器名稱。請記住容器名稱必須是小寫。此容器名稱應該與備份原則/保護群組建立期間所輸入的名稱相同|

  > [AZURE.NOTE] 日誌檔案會在擷取整個工作流程資訊的 WAImportExport 資料夾中進行建立。在 Azure 入口網站中建立匯入作業時，您需要此檔案。

  ![PowerShell 輸出](./media/backup-azure-backup-import-export/psoutput.png)

### 在 Azure 入口網站中建立匯入工作
1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中瀏覽至您的儲存體帳戶，接著按一下 [匯入/匯出]，然後按一下工作窗格中的 [建立匯入作業]。

    ![入口網站](./media/backup-azure-backup-import-export/azureportal.png)

2. 在精靈的步驟 1，指出您已備妥磁碟機並有可用的磁碟機日誌。在精靈的步驟 2 中，提供負責處理此匯入工作的人員連絡資訊。
3. 在步驟 3 中，上傳在上一個章節中您所取得的磁碟機日誌檔案。
4. 在步驟 4 中，為備份原則/保護群組建立期間所輸入的匯入作業輸入描述性名稱。您輸入的名稱只能包含小寫字母、數字、連字號和底線，必須以字母開頭，且不得包含空格。當作業正在進行中和一旦完成後，將會使用您所選的名稱來追蹤作業。
5. 接著，從清單中選取資料中心區域。資料中心區域將會指出您的包裹必須送達的資料中心和地址。

    ![DC](./media/backup-azure-backup-import-export/dc.png)

6. 在步驟 5 中，從清單中選取您的寄回貨運公司，並輸入貨運公司客戶編號。當匯入作業完成時，Microsoft 會透過此廠商將磁碟機寄還給您。

7. 運送磁碟，並輸入追蹤號碼以便追蹤運送狀態。一旦磁碟到達資料中心，其會複製到儲存體帳戶並更新狀態。

    ![完成狀態](./media/backup-azure-backup-import-export/complete.png)

### 完成工作流程
一旦儲存體帳戶中有初始備份資料可供使用時，Microsoft Azure 復原服務代理程式就會將此帳戶中的資料內容複製到備份保存庫或復原服務保存庫 (視何者較適合)。在下一步的排程備份時間，Azure 備份代理程式會透過初始備份複本執行增量備份。

## 後續步驟
- 若您有關於 Azure 匯入/匯出工作流程的任何問題，請參閱此[文章](../storage/storage-import-export-service.md)。
- 若您有關於工作流程的任何問題，請參閱 Azure 備份[常見問題集](backup-azure-backup-faq.md)的＜離線備份＞章節

<!---HONumber=AcomDC_0824_2016-->
