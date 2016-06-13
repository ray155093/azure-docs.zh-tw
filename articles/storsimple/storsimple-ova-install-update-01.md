<properties 
   pageTitle="在 StorSimple Virtual Array 上安裝 Update 0.1 | Microsoft Azure"
   description="描述如何使用 StorSimple Virtual Array Web UI 以使用入口網站和 Hotfix 方法套用 Update 0.1。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/26/2016"
   ms.author="alkohli" />

# 在 StorSimple Virtual Array 上安裝 Update 0.1

## 概觀

本文會說明在 StorSimple Virtual Array 上安裝 Update 0.1 所需要的步驟。它會描述透過 Azure 傳統入口網站和透過本機 Web UI 的更新程序。每個更新程序需時不到 2 分鐘。

一般必須套用軟體更新或 Hotfix，以將 StorSimple Virtual Array 保持在最新狀態。一般情況下，我們建議您透過 Azure 傳統入口網站安裝更新。不過，當入口網站無法使用時，您可以使用本機 Web UI 來套用 Hotfix 或更新。

請記住，安裝更新或 Hotfix 會重新啟動您的裝置。假設 StorSimple Virtual Array 是單一節點裝置，就會中斷任何進行中的 IO，裝置也會停機。

套用更新之前，建議您先讓主機上的磁碟區或共用離線，再讓裝置離線。這樣可以讓資料損毀的可能性降至最低。

## 使用 Azure 傳統入口網站

我們建議您透過 Azure 傳統入口網站安裝更新。入口網站的程序需要使用者掃描、下載然後安裝更新。請執行下列步驟以安裝更新或 Hotfix。

#### 透過 Azure 傳統入口網站安裝更新

1. 在 [裝置] 頁面上，選取要安裝更新的裝置。

2. 巡覽至 [裝置] > [維護] > [軟體更新]。

3. 如有軟體更新可用，您會看到一則訊息。若要檢查更新，也可以按一下頁面底部的 [掃描更新]。

	![掃描更新](./media/storsimple-ova-install-update-01/aupdate1m.png)

4. 按一下頁面底部的 [下載更新]。對話方塊會通知使用者更新會造成干擾。假設 StorSimple Virtual Array 是單一節點裝置，裝置會在更新之後重新啟動。這會中斷任何進行中的 IO。按一下核取圖示啟動作業，以下載可用的更新。

	![確認下載更新](./media/storsimple-ova-install-update-01/aupdate3m.png)

	更新下載完成後，您會收到通知。

	![下載更新](./media/storsimple-ova-install-update-01/aupdate5m.png)

5. 按一下頁面底部的 [安裝更新]，開始更新裝置。對話方塊會再次出現。按一下核取圖示開始作業，以安裝更新。
 
 	![確認安裝更新](./media/storsimple-ova-install-update-01/aupdate6m.png)
 
 
	作業建立之後，您會收到通知。

	![安裝更新](./media/storsimple-ova-install-update-01/aupdate8m.png)
	
6. 按一下 [檢視工作] 連結移至 [工作] 頁面，並監視安裝狀態。您可以按一下 [詳細資料]，隨時取得更新作業的詳細資訊。

	![監視更新](./media/storsimple-ova-install-update-01/aupdate9m.png)

6. 安裝完成後 (作業狀態 100%)，請移至 [裝置] > [維護] > [軟體更新]。軟體版本應顯示為 **10.0.10279.0**。

	![確認更新](./media/storsimple-ova-install-update-01/aupdate13m.png)

## 使用本機 Web UI 

無法使用 Azure 傳統入口網站時，請使用本機 Web UI 套用更新。使用本機 Web UI 時有兩個步驟︰

- 下載更新或 Hotfix
- 安裝更新或 Hotfix

### 下載更新或 Hotfix

請執行下列步驟，從 Microsoft Update Catalog 下載軟體更新。

#### 下載更新或 Hotfix

1. 啟動 Internet Explorer 並巡覽至 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)。

2. 如果這是您在此電腦上第一次使用 Microsoft Update Catalog，請在系統提示您安裝 Microsoft Update Catalog 附加元件時，按一下 [安裝]。
   
	![安裝目錄](./media/storsimple-ova-install-update-01/install-catalog.png)

3. 在 Microsoft Update Catalog 的 [搜尋] 方塊中，輸入您要下載的 Hotfix 知識庫 (KB) 編號 (例如 **3160441**)，然後按一下 [搜尋]。

    隨即顯示 Hotfix 清單，例如 **StorSimple Virtual Array Update 0.1**。

    ![搜尋目錄](./media/storsimple-ova-install-update-01/download1.png)

4. 按一下 [新增]。更新便會新增到購物籃中。

5. 按一下 [**檢視購物籃**]。

6. 按一下 [下載]。指定或**瀏覽**至您想要儲存下載項目的本機位置。更新便會下載到指定的位置，並放在與更新名稱相同的子資料夾中。資料夾也可以複製到裝置可連線的網路共用位置。


### 安裝更新或 Hotfix

安裝更新或 Hotfix 之前，請確定更新或 Hotfix 已下載至您主機的本機上，或是可透過網路共用存取。請執行下列步驟以安裝更新或 Hotfix。

#### 安裝更新或 Hotfix

1. 在本機 Web UI 中，移至 [維護] > [軟體更新]。

2. 在 [更新檔案路徑] 中，輸入更新或 Hotfix 的檔案名稱。如果更新或 Hotfix 的安裝檔案是放在網路共用上，您也可以瀏覽至該檔案。按一下 [Apply (套用)]。

	![更新裝置](./media/storsimple-ova-install-update-01/update1m.png)

3.  警告隨即出現。假設這是單一節點裝置，套用更新後，裝置就會重新啟動並停機。按一下核取圖示。

	![更新裝置](./media/storsimple-ova-install-update-01/update4m.png)

4. 更新將會啟動。裝置成功更新之後就會重新啟動。這段時間無法存取本機 UI。

    ![更新裝置](./media/storsimple-ova-install-update-01/update6m.png)

4. 重新啟動完成後，您就會進入登入頁面。接著驗證軟體版本。移至 [維護] > [軟體更新]。組建編號應該是 **10.0.0.0.10279**。

	> [AZURE.NOTE] 本機 Web UI 和 Azure 傳統入口網站中報告軟體版本的方式略有不同。本機 Web UI 報告的是 **10.0.0.0.10279**，相同版本在 Azure 傳統入口網站是 **10.0.10279.0** 。

	![更新裝置](./media/storsimple-ova-install-update-01/update9m.png)

## 後續步驟

深入了解[使用 Web UI 來管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

<!---HONumber=AcomDC_0601_2016-->