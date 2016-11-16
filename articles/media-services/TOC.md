# [概觀](media-services-overview.md)
## [概念](media-services-concepts.md)


# 開始使用
## [建立和管理帳戶](media-services-portal-create-account.md)
## [設定開發環境](media-services-set-up-computer.md)
## 傳遞隨選影片
### [入口網站](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## 執行即時串流
### [入口網站](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# 作法
## 管理
### [在入口網站中管理串流端點](media-services-portal-manage-streaming-endpoints.md)
### 管理實體
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [以 PowerShell 管理帳戶](media-services-manage-with-powershell.md)
### [以 Media Encoder Standard 裁剪影片](media-services-crop-video.md)
### [如何：更換儲存體存取金鑰之後更新媒體服務](media-services-roll-storage-access-keys.md)
### [配額和限制](media-services-quotas-and-limitations.md)
### 篩選器
#### [使用 Azure 媒體服務 .NET SDK 建立篩選器](media-services-dotnet-dynamic-manifest.md)
#### [如何使用媒體編碼器標準為資產編碼](media-services-rest-encode-asset.md)
### 以程式設計方式連線
#### [.NET](media-services-dotnet-connect-programmatically.md)
#### [REST](media-services-rest-connect-programmatically.md)

## 上傳內容
### 將檔案上傳至帳戶
#### [入口網站](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [複製現有的 Blob](media-services-copying-existing-blob.md)

## 編碼
### [內容](media-services-encode-asset.md)
#### 使用媒體編碼器標準為資產編碼
##### [入口網站](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
#### [如何搭配 .NET 使用媒體編碼器標準產生縮圖](media-services-dotnet-generate-thumbnail-with-mes.md)
#### [進階編碼](media-services-advanced-encoding-with-mes.md)
##### [媒體編碼器高階工作流程](media-services-encode-with-premium-workflow.md)
##### [媒體編碼器高階工作流程教學課程](media-services-media-encoder-premium-workflow-tutorials.md)
##### [使用工作流程設計工具建立進階編碼工作流程](media-services-workflow-designer.md)
##### [進階工作流程與多個輸入](media-services-media-encoder-premium-workflow-multiplefilesinput.md)

#### 結構描述 
#####[媒體編碼器標準](media-services-mes-schema.md)
#####[輸入中繼資料](media-services-input-metadata-schema.md)
#####[輸出中繼資料](media-services-output-metadata-schema.md)

#### 舊式編碼器
##### [使用 Azure 媒體封裝工具](media-services-static-packaging.md)

### [即時串流](media-services-manage-channels-overview.md)
#### [內部部署編碼器](media-services-live-streaming-with-onprem-encoders.md)
#### 內部部署編碼器教學課程
##### [入口網站](media-services-portal-live-passthrough-get-started.md)
##### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
#### [使用雲端編碼器即時串流](media-services-manage-live-encoder-enabled-channels.md)
#### 雲端編碼器教學課程
##### [入口網站](media-services-portal-creating-live-encoder-enabled-channel.md)
##### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
#### [設定內部部署編碼器與雲端編碼器搭配運作](media-services-live-encoders-overview.md)
#### [處理長時間執行的作業](media-services-dotnet-long-operations.md)
#### [分散式 MP4 即時內嵌規格](media-services-fmp4-live-ingest-overview.md)
#### [動態封裝](media-services-dynamic-packaging-overview.md)

### 媒體處理
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)

### 設定單一位元速率即時串流的編碼器
#### [Elemental Live 編碼器](media-services-configure-elemental-live-encoder.md)
#### [FMLE 編碼器](media-services-configure-fmle-live-encoder.md)
#### [NewTek TriCaster 編碼器](media-services-configure-tricaster-live-encoder.md)
#### [Wirecast 編碼器](media-services-configure-wirecast-live-encoder.md)

## [保護](media-services-content-protection-overview.md)
### [在入口網站中設定內容保護](media-services-portal-protect-content.md)
### [為串流設定 AES-128 清除金鑰](media-services-protect-with-aes128.md)
### [使用 AMS REST API 以儲存體加密來加密您的內容](media-services-rest-storage-encryption.md)
### [媒體服務 PlayReady 授權範本概觀](media-services-playready-license-template-overview.md)
### [DRM 授權傳遞](media-services-deliver-keys-and-licenses.md)
### [使用合作夥伴將 Widevine 授權傳遞到 Azure 媒體服務](media-services-licenses-partner-integration.md)
### [使用 PlayReady 和/或 Widevine 動態一般加密](media-services-protect-with-drm.md)
### [使用 Azure 媒體服務串流以 Apple FairPlay 保護的 HLS 內容](media-services-protect-hls-with-fairplay.md)
### [具有多重 DRM 及存取控制的 CENC：Azure 與 Azure 媒體服務的參考設計和實作](media-services-cenc-with-multidrm-access-control.md)

### 資產傳遞
#### 設定資產傳遞原則
##### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
##### [REST](media-services-rest-configure-asset-delivery-policy.md)
### 建立 ContentKeys
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### 設定內容金鑰授權原則
#### [入口網站](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)

## [分析](media-services-analytics-overview.md)
### [利用索引器 2 處理](media-services-process-content-with-indexer2.md)
### [利用索引器處理](media-services-index-content.md)
### [利用 Hyperlapse 處理](media-services-hyperlapse-content.md)
### [利用臉部偵測器處理](media-services-face-and-emotion-detection.md)
### [利用動態偵測器處理](media-services-motion-detection.md)
### [利用臉部修訂處理](media-services-face-redaction.md)
### [利用視訊縮圖處理](media-services-video-summarization.md)
### [利用 OCR 處理](media-services-video-optical-character-recognition.md)

## 調整
### [媒體處理](media-services-scale-media-processing-overview.md)
#### [入口網站](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
#### [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
### 串流端點
#### [入口網站](media-services-portal-scale-streaming-endpoints.md)

## [傳遞內容](media-services-deliver-content-overview.md)
### [篩選器和動態資訊清單概觀](media-services-dynamic-manifest-overview.md)
### 建立篩選器
#### [.NET](media-services-dotnet-dynamic-manifest.md)
#### [REST](media-services-rest-dynamic-manifest.md)
### 發佈內容
#### [入口網站](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [依下載進行傳遞](media-services-deliver-asset-download.md)
### [容錯移轉串流案例](media-services-implement-failover.md)

## 取用
### [使用現有播放器來播放媒體](media-services-playback-content-with-existing-players.md)
### [使用媒體播放器來播放媒體](media-services-develop-video-players.md)
### 其他播放選項
#### [Smooth Streaming Windows 市集應用程式](media-services-build-smooth-streaming-apps.md)
#### [具有 DASH.js 的 HTML5 應用程式](media-services-embed-mpeg-dash-in-html5.md)
#### [Adobe 開放原始碼媒體架構播放器](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [在用戶端插入廣告](media-services-inserting-ads-on-client-side.md)

## 整合
### [媒體服務延伸模組中的 CDN 快取原則](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### [授權 Microsoft†" Smooth Streaming Client Porting Kit](media-services-sspk.md)
### [管理跨多個儲存體帳戶的資產](meda-services-managing-multiple-storage-accounts.md)
### [使用 Axinom 將 Widevine 授權傳遞到 Azure 媒體服務](media-services-axinom-integration.md)
### [使用 castLabs 將 Widevine 授權傳遞到 Azure 媒體服務](media-services-castlabs-integration.md)
### [Widevine 授權範本概觀](media-services-widevine-license-template-overview.md)

## 監視
### 檢查作業進度
#### [REST](media-services-rest-check-job-progress.md)
#### [入口網站](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [佇列儲存體以監視作業通知](media-services-dotnet-check-job-progress-with-queues.md)

## 疑難排解
### [常見問題集](media-services-frequently-asked-questions.md)
### [即時串流的疑難排解指南](media-services-troubleshooting-live-streaming.md)
###[錯誤碼](media-services-error-codes.md)
###[重試邏輯](media-services-retry-logic-in-dotnet-sdk.md)

# 參考
## [版本資訊](media-services-release-notes.md)
## [.NET](media-services-dotnet-how-to-use.md)
## [REST](media-services-rest-how-to-use.md)
## [Media Encoder Premium Workflow 格式和轉碼器](media-services-premium-workflow-encoder-formats.md)
## [媒體編碼器標準格式和轉碼器](media-services-media-encoder-standard-formats.md)

# 資源
## [價格](https://azure.microsoft.com/pricing/details/media-services/)
## [Azure 媒體服務社群](media-services-community.md)











<!--HONumber=Nov16_HO2-->


