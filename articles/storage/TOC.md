# 概觀

## [簡介](storage-introduction.md)
## [選擇 Blob、檔案或資料磁碟](storage-decide-blobs-files-disks.md)

# 開始使用

## [建立儲存體帳戶](storage-create-storage-account.md)

## Blob 儲存體
### [.NET](storage-dotnet-how-to-use-blobs.md)
### [Java](storage-java-how-to-use-blob-storage.md)
### [Node.js](storage-nodejs-how-to-use-blob-storage.md)
### [C++](storage-c-plus-plus-how-to-use-blobs.md)
### [PHP](storage-php-how-to-use-blobs.md)
### [Python](storage-python-how-to-use-blob-storage.md)
### [Ruby](storage-ruby-how-to-use-blob-storage.md)
### [iOS](storage-ios-how-to-use-blob-storage.md)
### [Xamarin](storage-xamarin-blob-storage.md)

## 佇列儲存體
### [.NET](storage-dotnet-how-to-use-queues.md)
### [Java](storage-java-how-to-use-queue-storage.md)
### [Node.js](storage-nodejs-how-to-use-queues.md)
### [C++](storage-c-plus-plus-how-to-use-queues.md)
### [PHP](storage-php-how-to-use-queues.md)
### [Python](storage-python-how-to-use-queue-storage.md)
### [Ruby](storage-ruby-how-to-use-queue-storage.md)

## 資料表儲存體
### [.NET](storage-dotnet-how-to-use-tables.md)
### [F#](/dotnet/articles/fsharp/using-fsharp-on-azure/table-storage)
### [Java](storage-java-how-to-use-table-storage.md)
### [Node.js](storage-nodejs-how-to-use-table-storage.md)
### [C++](storage-c-plus-plus-how-to-use-tables.md)
### [PHP](storage-php-how-to-use-table-storage.md)
### [Python](storage-python-how-to-use-table-storage.md)
### [Ruby](storage-ruby-how-to-use-table-storage.md)

## [檔案儲存體](storage-files-introduction.md)
### [入口網站](storage-file-how-to-use-files-portal.md)
### [.NET](storage-dotnet-how-to-use-files.md)
### [PowerShell](storage-file-how-to-use-files-powershell.md)
### [Windows](storage-file-how-to-use-files-windows.md)
### [Linux](storage-how-to-use-files-linux.md)
### [Mac](storage-file-how-to-use-files-mac.md)  
### [Java](storage-java-how-to-use-file-storage.md)
### [C++](storage-c-plus-plus-how-to-use-files.md)
### [Python](storage-python-how-to-use-file-storage.md)
### [建立檔案共用](storage-file-how-to-create-file-share.md)
### [常見問題集](storage-files-faq.md)
## 磁碟儲存體 
### [使用 Resource Manager 和 PowerShell 建立 VM](../virtual-machines/virtual-machines-windows-ps-create.md)
### [使用 Azure CLI 2.0 建立 Linux VM](../virtual-machines/linux/quick-create-cli.md)
### [使用 PowerShell 將受控磁碟連結至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)
### [在 Linux VM 中新增受控磁碟](../virtual-machines/linux/add-disk.md)
### [在 Windows 中建立 VHD 複本並儲存為受控磁碟](../virtual-machines/windows/snapshot-copy-managed-disk.md)
### [在 Linux 中建立 VHD 複本並儲存為受控磁碟](../virtual-machines/linux/snapshot-copy-managed-disk.md)
### [在 Resource Manager 範本中使用受控磁碟](storage-using-managed-disks-template-deployments.md)

# 作法
## [建立儲存體帳戶](storage-create-storage-account.md)
## 使用 Blob
### [服務概觀](https://msdn.microsoft.com/library/dd179376.aspx)
### [使用 Azure 搜尋服務搜尋 Blob 儲存體](../search/search-blob-storage-integration.md)
### [經常性存取和非經常性存取層級](storage-blob-storage-tiers.md)
### [自訂網域](storage-custom-domain-name.md)
### [使用 Azure CDN 透過 HTTPs 以自訂網域存取 blob](storage-https-custom-domain-cdn.md) 
### [匿名存取 Blob](storage-manage-access-to-resources.md)
### [範例](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)
## 使用佇列
### [概念](https://msdn.microsoft.com/library/dd179353.aspx)
### [範例](https://azure.microsoft.com/documentation/samples/?service=storage&term=queue)
## 使用資料表
### [概觀](https://msdn.microsoft.com/library/dd179463.aspx)
### [資料表設計指南](storage-table-design-guide.md)
### [範例](https://azure.microsoft.com/documentation/samples/?service=storage&term=table)
## 使用檔案
### [概觀](/rest/api/storageservices/File-Service-Concepts)
### [針對 Azure 檔案進行疑難排解 - Windows](storage-troubleshoot-windows-file-connection-problems.md)
### [針對 Azure 檔案進行疑難排解 - Linux](storage-troubleshoot-linux-file-connection-problems.md)
### [範例](https://azure.microsoft.com/documentation/samples/?service=storage&term=file)
## 使用磁碟
### [Windows VM 的磁碟和 VHD](storage-about-disks-and-vhds-windows.md)
### [Linux VM 的磁碟和 VHD](storage-about-disks-and-vhds-linux.md)
### [Azure 受控磁碟概觀](storage-managed-disks-overview.md)
### [將 Azure 虛擬機器移轉至 Azure 受控磁碟](../virtual-machines/windows/migrate-to-managed-disks.md)
### [從 AWS 和其他平台移轉至受控磁碟](../virtual-machines/windows/on-prem-to-azure.md)
### [關於 Azure IaaS VM 磁碟 的常見問題](storage-faq-for-disks.md)
### 進階儲存體
#### [VM 磁碟適用的高效能進階儲存體](storage-premium-storage.md)
#### [使用 Azure Site Recovery 移轉到進階儲存體](storage-migrate-to-premium-storage-using-azure-site-recovery.md)
#### [為高效能而設計](storage-premium-storage-performance.md)
### 標準儲存體
#### [符合成本效益的標準儲存體和非受控和受控 VM 磁碟](storage-standard-storage.md)
### 使用非受控磁碟
#### [移轉至進階儲存體](storage-migration-to-premium-storage.md)
#### [以遞增快照集備份非受控 VM 磁碟](storage-incremental-snapshots.md)
## 規劃和設計
### [複寫](storage-redundancy.md)
### [擴充和效能目標](storage-scalability-targets.md)
### [效能和延展性檢查清單](storage-performance-checklist.md)
### [並行](storage-concurrency.md)
## 開發
### 範例
#### [.NET](storage-samples-dotnet.md)
#### [Java](storage-samples-java.md)
### [使用 RA-GRS 設定 HA 應用程式](storage-designing-ha-apps-with-ragrs.md)
### [設定連接字串](storage-configure-connection-string.md)
### [使用儲存體模擬器](storage-use-emulator.md)
### [設定並擷取屬性和中繼資料](storage-properties-metadata.md)
## 管理
### [PowerShell](storage-powershell-guide-full.md)
### [Azure CLI 2.0](storage-azure-cli.md)
### [Azure CLI 1.0](storage-azure-cli-nodejs.md)
### [Azure 自動化](automation-manage-storage.md)
## 安全
### [安全性指南](storage-security-guide.md)
### [待用資料的加密](storage-service-encryption.md)
### [客戶金鑰的加密靜止](storage-service-encryption-customer-managed-keys.md)
### [共用金鑰驗證](/rest/api/storageservices/Authentication-for-the-Azure-Storage-Services)
### [共用存取簽章 (SAS)](storage-dotnet-shared-access-signature-part-1.md)
### [教學課程：使用 Azure 金鑰保存庫加密及解密 Blob](storage-encrypt-decrypt-blobs-key-vault.md)
### [需要安全傳輸](storage-require-secure-transfer.md)
### 用戶端加密
#### [.NET](storage-client-side-encryption.md)
#### [Java](storage-client-side-encryption-java.md)
#### [Python](storage-client-side-encryption-python.md)
## 監視及疑難排解
### [災害復原指引](storage-disaster-recovery-guidance.md)
### [備份和適用於 IAAS 磁碟的 DR](storage-backup-and-disaster-recovery-for-azure-iaas-disks.md)
### [針對儲存體總管進行疑難排解](storage-explorer-troubleshooting.md)
### 計量與記錄
#### [Storage Analytics](storage-analytics.md)
#### [啟用和檢視計量](storage-enable-and-view-metrics.md)
#### [監控、診斷及疑難排解](storage-monitoring-diagnosing-troubleshooting.md)
#### [疑難排解教學課程](storage-e2e-troubleshooting.md)
### [針對磁碟刪除錯誤進行疑難排解](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)
### [針對檔案儲存體進行疑難排解](storage-troubleshoot-file-connection-problems.md)
## 傳送資料
### [將資料移入和移出儲存體](storage-moving-data.md)
### [AzCopy on Windows](storage-use-azcopy.md)
### [AzCopy on Linux](storage-use-azcopy-linux.md)
### [使用匯入/匯出服務](storage-import-export-service.md)
### [使用匯入/匯出工具](storage-import-export-tool-how-to.md)
#### [設定匯入/匯出工具](storage-import-export-tool-setup.md)
#### [針對匯入作業準備硬碟](storage-import-export-tool-preparing-hard-drives-import.md)
##### [在匯入程序期間設定屬性和中繼資料](storage-import-export-tool-setting-properties-metadata-import.md)
##### [針對匯入作業準備硬碟的簡單工作流程](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
##### [匯入作業的常用命令快速參考](storage-import-export-tool-quick-reference.md)
#### [預覽匯出作業的磁碟機使用量](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [利用複製記錄檔檢閱作業狀態](storage-import-export-tool-reviewing-job-status-v1.md)
#### [修復匯入作業](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [修復匯出作業](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [針對匯入/匯出工具進行疑難排解](storage-import-export-tool-troubleshooting-v1.md)
#### [匯入匯出服務資訊清單檔案格式](storage-import-export-file-format-manifest.md)
#### [匯入/匯出服務中繼資料和屬性檔案格式](storage-import-export-file-format-metadata-and-properties.md)
#### [匯入/匯出服務記錄檔格式](storage-import-export-file-format-log.md)
### [使用匯入/匯出工具 (v1)](storage-import-export-tool-how-to-v1.md)
#### [設定匯入/匯出工具](storage-import-export-tool-setup-v1.md)
#### [針對匯入作業準備硬碟](storage-import-export-tool-preparing-hard-drives-import-v1.md)
##### [在匯入程序期間設定屬性和中繼資料](storage-import-export-tool-setting-properties-metadata-import-v1.md)
##### [針對匯入作業準備硬碟的簡單工作流程](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
##### [匯入作業的常用命令快速參考](storage-import-export-tool-quick-reference-v1.md)
#### [預覽匯出作業的磁碟機使用量](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [利用複製記錄檔檢閱作業狀態](storage-import-export-tool-reviewing-job-status-v1.md)
#### [修復匯入作業](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [修復匯出作業](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [針對匯入/匯出工具進行疑難排解](storage-import-export-tool-troubleshooting-v1.md)
#### [匯入匯出服務資訊清單檔案格式](storage-import-export-file-format-manifest.md)
#### [匯入/匯出服務中繼資料和屬性檔案格式](storage-import-export-file-format-metadata-and-properties.md)
#### [匯入/匯出服務記錄檔格式](storage-import-export-file-format-log.md)
### [使用 Azure 匯入/匯出服務 REST API](storage-import-export-using-the-rest-api.md)
#### [建立匯入作業](storage-import-export-creating-an-import-job.md)
#### [建立匯出作業](storage-import-export-creating-an-export-job.md)
#### [擷取作業的狀態資訊](storage-import-export-retrieving-state-info-for-a-job.md)
#### [列舉作業](storage-import-export-enumerating-jobs.md)
#### [取消和刪除作業](storage-import-export-cancelling-and-deleting-jobs.md)
#### [備份磁碟機資訊清單](storage-import-export-backing-up-drive-manifests.md)
#### [匯入/匯出作業的診斷和錯誤復原](storage-import-export-diagnostics-and-error-recovery.md)
# 參考
## [PowerShell](/powershell/module/azure.storage)
## [Azure CLI](/cli/azure/storage)
## .NET
### [資源管理員](/dotnet/api/microsoft.azure.management.storage)
### [資料移動](/dotnet/api/microsoft.windowsazure.storage.datamovement)
### [Blob、佇列、資料表和檔案](https://msdn.microsoft.com/library/azure/mt347887.aspx)
## [Java](http://azure.github.io/azure-storage-java/)
## [Node.js](http://azure.github.io/azure-storage-node)
## [Ruby](http://azure.github.io/azure-storage-ruby)
## [PHP](http://azure.github.io/azure-storage-php/)
## [Python](https://azure-storage.readthedocs.io/en/latest/index.html)
## [C++](http://azure.github.io/azure-storage-cpp)
## [iOS](http://azure.github.io/azure-storage-ios/)
## [Android](http://azure.github.io/azure-storage-android)
## REST
### [Blob、佇列、資料表和檔案](/rest/api/storageservices)
### [資源提供者](/rest/api/storagerp)
### [匯入/匯出](/rest/api/storageimportexport)

# 相關參考
## 傳統入口網站
### [建立儲存體帳戶](storage-create-storage-account-classic-portal.md)
### [啟用和檢視計量](storage-enable-and-view-metrics-classic-portal.md)
### [監控、診斷及疑難排解](storage-monitoring-diagnosing-troubleshooting-classic-portal.md)
### [疑難排解教學課程](storage-e2e-troubleshooting-classic-portal.md)

# 資源
## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=storage)
## [Azure 儲存體用戶端工具](storage-explorers.md)
## [論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
## [價格](https://azure.microsoft.com/pricing/details/storage/blobs/)
## [服務更新](https://azure.microsoft.com/updates/?product=storage)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=storage)

## Azure 儲存體總管
### [儲存體總管 (預覽)](../vs-azure-tools-storage-manage-with-storage-explorer.md)
### [使用儲存體總管來管理 Blob (預覽)](../vs-azure-tools-storage-explorer-blobs.md)
### [搭配使用儲存體總管 (預覽) 與 Azure 檔案儲存體](../vs-azure-tools-storage-explorer-files.md)

## NuGet 套件
### [適用於 .NET 的 Azure 儲存體用戶端程式庫](https://www.nuget.org/packages/WindowsAzure.Storage/)
### [Azure 儲存體資料移動程式庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)
### [Azure 資源管理員](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)

## 原始程式碼
### .NET
#### [Blob、佇列、資料表及檔案](https://github.com/Azure/azure-storage-net)
#### [資料移動](https://github.com/Azure/azure-storage-net-data-movement)
#### [資源提供者](https://github.com/Azure/azure-sdk-for-net)
### [Node.js](http://azure.github.io/azure-storage-node/)
### [Java](https://github.com/Azure/azure-storage-java)
### [C++](https://github.com/Azure/azure-storage-cpp)
### [PHP](https://github.com/Azure/azure-storage-php)
### [Python](https://github.com/Azure/azure-storage-python)
### [Ruby](https://github.com/Azure/azure-storage-ruby)
### [iOS](https://github.com/Azure/azure-storage-ios)
