<properties 
   pageTitle="在 StorSimple Virtual Array 上安裝更新 | Microsoft Azure"
   description="描述如何透過 StorSimple Virtual Array Web UI，使用入口網站和 Hotfix 方法套用更新"
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
   ms.date="06/16/2016"
   ms.author="alkohli" />

# 在 StorSimple Virtual Array 上安裝更新

## 概觀

本文說明使用本機 Web UI，在 StorSimple Virtual Array 上安裝更新所需的步驟。此程序需時不到 2 分鐘。您需要套用軟體更新或 Hotfix，以便讓您的 StorSimple Virtual Array 保持在最新狀態。

請記住，安裝更新或 Hotfix 會重新啟動您的裝置。假設 StorSimple Virtual Array 是單一節點裝置，就會中斷任何進行中的 I/O，裝置也會停機。

套用更新之前，建議您先讓主機上的磁碟區或共用離線，再讓裝置離線。這樣可以讓資料損毀的可能性降至最低。

## 使用本機 Web UI 
若要安裝最新的更新，您必須使用本機 Web UI 來套用 Hotfix 或更新 (您目前無法使用 Azure 傳統入口網站來安裝更新)。

使用本機 Web UI 時有兩個步驟︰

- 下載更新或 Hotfix
- 安裝更新或 Hotfix

### 下載更新或 Hotfix

請執行下列步驟，從 Microsoft Update Catalog 下載軟體更新。

#### 下載更新或 Hotfix

1. 啟動 Internet Explorer 並瀏覽至 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)。

2. 如果這是您在此電腦上第一次使用 Microsoft Update Catalog，請在系統提示您安裝 Microsoft Update Catalog 附加元件時，按一下 [安裝]。
   
	![安裝目錄](./media/storsimple-ova-install-update-01/install-catalog.png)

3. 在 Microsoft Update Catalog 的搜尋方塊中，輸入您要下載的 Hotfix 知識庫 (KB) 編號。輸入 **3160441** (適用於 Update 0.2) 或 **3160441** (適用於 Update 0.1)，然後按一下 [搜尋]。

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

4. 重新啟動完成後，您就會進入登入頁面。接著驗證軟體版本。移至 [維護] > [軟體更新]。軟體版本應顯示為 **10.0.10280.0** (適用於 Update 0.2) 或 **10.0.10279.0** (適用於 Update 0.1)。

	> [AZURE.NOTE] 本機 Web UI 和 Azure 傳統入口網站中報告軟體版本的方式略有不同。例如，本機 Web UI 報告的是 **10.0.0.0.10279**，而相同版本在 Azure 傳統入口網站是 **10.0.10279.0** 。

	![更新裝置](./media/storsimple-ova-install-update-01/update9m.png)

## 後續步驟

深入了解[管理您的 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

<!---HONumber=AcomDC_0622_2016-->