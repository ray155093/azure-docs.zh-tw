<properties
	pageTitle="CDN - 藉由壓縮檔案來改善效能"
	description="您可以藉由壓縮檔案來改善檔案傳輸速度並增加頁面載入效能。"
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016" 
	ms.author="casoper"/>

# 藉由壓縮檔案來改善效能

本主題討論如何透過壓縮檔案改善檔案傳輸速度以及提升頁面載入效能。

CDN 有兩種方式可以支援壓縮：

- 您可以在原始伺服器上啟用壓縮，此時 CDN 會依預設支援壓縮並將壓縮的檔案傳送到用戶端。
- 您可以直接在 CDN 邊緣伺服器上啟用壓縮，此時 CDN 會壓縮檔案並將其提供給使用者。

## 啟用壓縮

> [AZURE.NOTE] 標準和高階 CDN 層提供相同的壓縮功能，但兩者的使用者介面不同。如需有關標準和高階 CDN 層之間的差異的詳細資訊，請參閱 [Azure CDN 概觀](cdn-overview.md)。

### 標準層

1. 在 [CDN 設定檔] 刀鋒視窗中，按一下您要管理的 CDN 端點。

	![[CDN 設定檔] 刀鋒視窗端點](./media/cdn-file-compression/cdn-endpoints.png)

	隨即開啟 [CDN 端點] 刀鋒視窗。

2. 按一下 [設定] 按鈕。

	![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-file-compression/cdn-config-btn.png)

	隨即開啟 [CDN 組態] 刀鋒視窗。

3. 開啟 [**壓縮**]。

	![CDN 壓縮的選項](./media/cdn-file-compression/cdn-compress-standard.png)

4. 請使用預設的類型，或者移除或新增檔案類型以修改清單。

5. 完成變更之後，按一下 [**儲存**] 按鈕。

### 高階層

1. 在 [CDN 設定檔] 刀鋒視窗中，按一下 [**管理**] 按鈕。

	![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-file-compression/cdn-manage-btn.png)

	隨即開啟 CDN 管理入口網站。

2. 將滑鼠移至 [**HTTP 大型**] 索引標籤上，然後將滑鼠移至 [**快取設定**] 飛出視窗上。按一下 [**壓縮**]。

	隨即顯示 [壓縮] 的選項。

	![檔案壓縮](./media/cdn-file-compression/cdn-compress-files.png)

3. 按一下 [啟用壓縮] 選項按鈕以啟用壓縮。在 [檔案類型] 文字方塊中，輸入您想要壓縮成逗號分隔清單 (無空格) 的 MIME 類型。

4. 完成變更之後，按一下 [更新] 按鈕。


## 壓縮程序

### 概觀

1. 要求者會傳送內容的要求。

2. Edge Server 會檢查有無 **Accept-Encoding** 標頭。
	1. 若包含此標頭，它會識別要求的壓縮方法。
		> [AZURE.NOTE] 支援的壓縮方法是 **gzip**、**deflate** 和 **bzip2**。
	2. 若遺漏此標頭，這種類型的要求會以未壓縮的格式提供。
	
3.	最接近的邊緣 POP 會檢查快取狀態、壓縮方法以及它是否依然具有有效的存留時間。
	1.	快取**遺漏**：如果未快取要求的版本，要求會轉送到原始來源。
	2.	快取**命中**：如果要求的版本是以要求的壓縮方法快取，則 Edge Server 會立即將壓縮的內容傳送至用戶端。
	3.	快取**命中**：如果使用不同的壓縮方法快取，則 Edge Server 會將資產轉碼為要求的壓縮方法。
	4.	快取**命中**︰如果以未壓縮的格式快取檔案，即會執行檢查，以判斷要求是否符合 Edge Server 壓縮 (請見下文注意事項)。如果適用，Edge Server 將會壓縮檔案並將其提供給用戶端。否則會傳回未壓縮的內容。
		
> [AZURE.IMPORTANT] 若要符合壓縮，檔案必須︰
>
> - 超過 128 個位元組。
> - 小於 1 MB。
> - 已[設定壓縮](#enabling-compression)的 MIME 類型。

### 資料表

下表描述每個案例的 CDN 壓縮行為。

#### 已停用壓縮或檔案不適合進行壓縮

|要求的格式|快取的檔案|CDN 回應|注意事項|
|----------------|-----------|------------|-----|
|已壓縮|已壓縮|已壓縮|支援格式之間的 CDN 轉碼|
|已壓縮|未壓縮|未壓縮| |	
|已壓縮|不快取|已壓縮或未壓縮|取決於原始回應|
|未壓縮|已壓縮|未壓縮|CDN 會回到未壓縮版本的原始狀態|
|未壓縮|未壓縮|未壓縮| |	
|未壓縮|不快取|未壓縮| |

#### 啟用壓縮和檔案適合進行壓縮

|要求的格式|快取的檔案|CDN 回應|注意事項|
|----------------|-----------|------------|-----|
|已壓縮|已壓縮|已壓縮|支援格式之間的 CDN 轉碼|
|已壓縮|未壓縮|已壓縮|CDN 執行壓縮|
|已壓縮|不快取|已壓縮|如果來源傳回未壓縮，則 CDN 會執行壓縮|
|未壓縮|已壓縮|未壓縮|CDN 執行解壓縮|
|未壓縮|未壓縮|未壓縮| |	
|未壓縮|不快取|未壓縮| |	


## 注意事項

1. 隨著新端點的部署，CDN 組態變更會需要一些時間才能傳播至整個網路。在大部分情況下，變更會在 90 分鐘內套用完成。如果這是您第一次設定 CDN 端點壓縮，您應該考慮先等候 1-2 小時，確定壓縮設定已傳播至 POP 再進行疑難排解。
2. Edge Server 上只能快取一個檔案版本 (壓縮或未壓縮)。不同版本的要求將會導致 Edge Server 轉碼內容。
3. 對於啟用媒體服務 CDN 的串流端點，壓縮功能依預設會針對下列內容類型啟用：application/vnd.ms-sstr+xml, application/dash+xml,application/vnd.apple.mpegurl, application/f4m+xml。您不能使用 Azure 入口網站啟用/停用上述類型的壓縮。  
4. 如果可能，不建議對 ZIP、MP3、MP4、JPG 等壓縮格式套用壓縮。

## 另請參閱
- [CDN 檔案壓縮疑難排解](cdn-troubleshoot-compression.md)    

<!---HONumber=AcomDC_0504_2016-->