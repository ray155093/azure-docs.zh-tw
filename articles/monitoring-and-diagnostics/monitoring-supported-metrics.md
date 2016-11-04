---
title: Azure 監視器度量 - 每個資源類型所支援的度量 | Microsoft Docs
description: AAzure 監視器每一個資源類型的可用度量清單。
author: johnkemnetz
manager: rboucher
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: johnkem

---
# <a name="supported-metrics-with-azure-monitor"></a>支援 Azure 監視器的度量
Azure 監視器提供數種與度量進行互動的方式，包括在入口網站中製作度量圖表、透過 REST API 存取度量，或使用 PowerShell 或 CLI 查詢度量。 以下是目前可供 Azure 監視器度量管線使用的所有度量完整清單。

> [!NOTE]
> 其他度量可在入口網站中或使用舊版 API 提供。 這份清單僅包含使用彙總 Azure 監視器度量管線的公開預覽時可供使用的公開預覽度量。
> 
> 

## <a name="microsoft.batch/batchaccounts"></a>Microsoft.Batch/batchAccounts
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| CoreCount |核心計數 |Count |總計 |Batch 帳戶中的核心總數 |
| TotalNodeCount |節點計數 |Count |總計 |Batch 帳戶中的節點總數 |
| CreatingNodeCount |建立節點計數 |Count |總計 |正在建立的節點數目 |
| StartingNodeCount |啟動節點計數 |Count |總計 |啟動的節點數目 |
| WaitingForStartTaskNodeCount |等候啟動工作節點計數 |Count |總計 |等待啟動工作完成的節點數目 |
| StartTaskFailedNodeCount |啟動工作失敗的節點計數 |Count |總計 |啟動工作失敗的節點數目 |
| IdleNodeCount |閒置的節點計數 |Count |總計 |閒置節點的數目 |
| OfflineNodeCount |離線節點計數 |Count |總計 |離線節點的數目 |
| RebootingNodeCount |重新啟動節點計數 |Count |總計 |重新啟動節點的數目 |
| RebootingNodeCount |重新安裝映像節點計數 |Count |總計 |重新安裝映像的節點數目 |
| RunningNodeCount |執行節點計數 |Count |總計 |執行節點的數目 |
| LeavingPoolNodeCount |離開集區節點計數 |Count |總計 |離開集區的節點數目 |
| UnusableNodeCount |無法使用的節點計數 |Count |總計 |無法使用的節點數目 |
| TaskStartEvent |工作啟動的事件 |Count |總計 |已啟動的工作總次數 |
| TaskCompleteEvent |工作完成事件 |Count |總計 |已完成的工作總次數 |
| TaskFailEvent |工作失敗事件 |Count |總計 |失敗狀態中已完成的工作總數 |
| PoolCreateEvent |集區建立事件 |Count |總計 |已建立集區的總數 |
| PoolResizeStartEvent |調整集區大小啟動事件 |Count |總計 |已啟動的調整集區大小總次數 |
| PoolResizeCompleteEvent |調整集區大小完成事件 |Count |總計 |已完成的調整集區大小總次數 |
| PoolDeleteStartEvent |集區刪除啟動事件 |Count |總計 |已啟動的集區刪除總次數 |
| PoolDeleteCompleteEvent |集區刪除完成事件 |Count |總計 |已完成的集區刪除總次數 |

## <a name="microsoft.cache/redis"></a>Microsoft.Cache/redis
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| connectedclients |連線的用戶端 |Count |最大值 | |
| totalcommandsprocessed |總作業數 |Count |總計 | |
| cachehits |快取點擊 |Count |總計 | |
| cachemisses |快取遺漏 |Count |總計 | |
| getcommands |取得 |Count |總計 | |
| setcommands |設定 |Count |總計 | |
| evictedkeys |收回的金鑰 |Count |總計 | |
| totalkeys |索引鍵總計 |Count |最大值 | |
| expiredkeys |到期的金鑰 |Count |總計 | |
| usedmemory |已使用的記憶體 |位元組 |最大值 | |
| usedmemoryRss |已用的記憶體 RSS |位元組 |最大值 | |
| serverLoad |伺服器負載 |百分比 |最大值 | |
| cacheWrite |快取寫入 |每秒位元組 |最大值 | |
| cacheRead |快取讀取 |每秒位元組 |最大值 | |
| percentProcessorTime |CPU |百分比 |最大值 | |
| connectedclients0 |連線的用戶端 (分區 0) |Count |最大值 | |
| totalcommandsprocessed0 |總作業數 (分區 0) |Count |總計 | |
| cachehits0 |快取點擊 (分區 0) |Count |總計 | |
| cachemisses0 |快取遺漏 (分區 0) |Count |總計 | |
| getcommands0 |取得 (分區 0) |Count |總計 | |
| setcommands0 |設定 (分區 0) |Count |總計 | |
| evictedkeys0 |收回的金鑰 (分區 0) |Count |總計 | |
| totalkeys0 |索引鍵總計 (節點 0) |Count |最大值 | |
| expiredkeys0 |到期的金鑰 (分區 0) |Count |總計 | |
| usedmemory0 |已用的記憶體 (分區 0) |位元組 |最大值 | |
| usedmemoryRss0 |已用的記憶體 RSS (分區 0) |位元組 |最大值 | |
| serverLoad0 |伺服器負載 (分區 0) |百分比 |最大值 | |
| cacheWrite0 |快取寫入 (分區 0) |每秒位元組 |最大值 | |
| cacheRead0 |快取讀取 (分區 0) |每秒位元組 |最大值 | |
| percentProcessorTime0 |CPU (分區 0) |百分比 |最大值 | |
| connectedclients1 |連線的用戶端 (分區 1) |Count |最大值 | |
| totalcommandsprocessed1 |總作業數 (分區 1) |Count |總計 | |
| cachehits1 |快取點擊 (分區 1) |Count |總計 | |
| cachemisses1 |快取遺漏 (分區 1) |Count |總計 | |
| getcommands1 |取得 (分區 1) |Count |總計 | |
| setcommands1 |設定 (分區 1) |Count |總計 | |
| evictedkeys1 |收回的金鑰 (分區 1) |Count |總計 | |
| totalkeys1 |索引鍵總計 (節點 1) |Count |最大值 | |
| expiredkeys1 |到期的金鑰 (分區 1) |Count |總計 | |
| usedmemory1 |已用的記憶體 (分區 1) |位元組 |最大值 | |
| usedmemoryRss1 |已用的記憶體 RSS (分區 1) |位元組 |最大值 | |
| serverLoad1 |伺服器負載 (分區 1) |百分比 |最大值 | |
| cacheWrite1 |快取寫入 (分區 1) |每秒位元組 |最大值 | |
| cacheRead1 |快取讀取 (分區 1) |每秒位元組 |最大值 | |
| percentProcessorTime1 |CPU (分區 1) |百分比 |最大值 | |
| connectedclients2 |連線的用戶端 (分區 2) |Count |最大值 | |
| totalcommandsprocessed2 |總作業數 (分區 2) |Count |總計 | |
| cachehits2 |快取點擊 (分區 2) |Count |總計 | |
| cachemisses2 |快取遺漏 (分區 2) |Count |總計 | |
| getcommands2 |取得 (分區 2) |Count |總計 | |
| setcommands2 |設定 (分區 2) |Count |總計 | |
| evictedkeys2 |收回的金鑰 (分區 2) |Count |總計 | |
| totalkeys2 |索引鍵總計 (節點 2) |Count |最大值 | |
| expiredkeys2 |到期的金鑰 (分區 2) |Count |總計 | |
| usedmemory2 |已用的記憶體 (分區 2) |位元組 |最大值 | |
| usedmemoryRss2 |已用的記憶體 RSS (分區 2) |位元組 |最大值 | |
| serverLoad2 |伺服器負載 (分區 2) |百分比 |最大值 | |
| cacheWrite2 |快取寫入 (分區 2) |每秒位元組 |最大值 | |
| cacheRead2 |快取讀取 (分區 2) |每秒位元組 |最大值 | |
| percentProcessorTime2 |CPU (分區 2) |百分比 |最大值 | |
| connectedclients3 |連線的用戶端 (分區 3) |Count |最大值 | |
| totalcommandsprocessed3 |總作業數 (分區 3) |Count |總計 | |
| cachehits3 |快取點擊 (分區 3) |Count |總計 | |
| cachemisses3 |快取遺漏 (分區 3) |Count |總計 | |
| getcommands3 |取得 (分區 3) |Count |總計 | |
| setcommands3 |設定 (分區 3) |Count |總計 | |
| evictedkeys3 |收回的金鑰 (分區 3) |Count |總計 | |
| totalkeys3 |索引鍵總計 (節點 3) |Count |最大值 | |
| expiredkeys3 |到期的金鑰 (分區 3) |Count |總計 | |
| usedmemory3 |已用的記憶體 (分區 3) |位元組 |最大值 | |
| usedmemoryRss3 |已用的記憶體 RSS (分區 3) |位元組 |最大值 | |
| serverLoad3 |伺服器負載 (分區 3) |百分比 |最大值 | |
| cacheWrite3 |快取寫入 (分區 3) |每秒位元組 |最大值 | |
| cacheRead3 |快取讀取 (分區 3) |每秒位元組 |最大值 | |
| percentProcessorTime3 |CPU (分區 3) |百分比 |最大值 | |
| connectedclients4 |連線的用戶端 (分區 4) |Count |最大值 | |
| totalcommandsprocessed4 |總作業數 (分區 4) |Count |總計 | |
| cachehits4 |快取點擊 (分區 4) |Count |總計 | |
| cachemisses4 |快取遺漏 (分區 4) |Count |總計 | |
| getcommands4 |取得 (分區 4) |Count |總計 | |
| setcommands4 |設定 (分區 4) |Count |總計 | |
| evictedkeys4 |收回的金鑰 (分區 4) |Count |總計 | |
| totalkeys4 |索引鍵總計 (節點 4) |Count |最大值 | |
| expiredkeys4 |到期的金鑰 (分區 4) |Count |總計 | |
| usedmemory4 |已用的記憶體 (分區 4) |位元組 |最大值 | |
| usedmemoryRss4 |已用的記憶體 RSS (分區 4) |位元組 |最大值 | |
| serverLoad4 |伺服器負載 (分區 4) |百分比 |最大值 | |
| cacheWrite4 |快取寫入 (分區 4) |每秒位元組 |最大值 | |
| cacheRead4 |快取讀取 (分區 4) |每秒位元組 |最大值 | |
| percentProcessorTime4 |CPU (分區 4) |百分比 |最大值 | |
| connectedclients5 |連線的用戶端 (分區 5) |Count |最大值 | |
| totalcommandsprocessed5 |總作業數 (分區 5) |Count |總計 | |
| cachehits5 |快取點擊 (分區 5) |Count |總計 | |
| cachemisses5 |快取遺漏 (分區 5) |Count |總計 | |
| getcommands5 |取得 (分區 5) |Count |總計 | |
| setcommands5 |設定 (分區 5) |Count |總計 | |
| evictedkeys5 |收回的金鑰 (分區 5) |Count |總計 | |
| totalkeys5 |索引鍵總計 (節點 5) |Count |最大值 | |
| expiredkeys5 |到期的金鑰 (分區 5) |Count |總計 | |
| usedmemory5 |已用的記憶體 (分區 5) |位元組 |最大值 | |
| usedmemoryRss5 |已用的記憶體 RSS (分區 5) |位元組 |最大值 | |
| serverLoad5 |伺服器負載 (分區 5) |百分比 |最大值 | |
| cacheWrite5 |快取寫入 (分區 5) |每秒位元組 |最大值 | |
| cacheRead5 |快取讀取 (分區 5) |每秒位元組 |最大值 | |
| percentProcessorTime5 |CPU (分區 5) |百分比 |最大值 | |
| connectedclients6 |連線的用戶端 (分區 6) |Count |最大值 | |
| totalcommandsprocessed6 |總作業數 (分區 6) |Count |總計 | |
| cachehits6 |快取點擊 (分區 6) |Count |總計 | |
| cachemisses6 |快取遺漏 (分區 6) |Count |總計 | |
| getcommands6 |取得 (分區 6) |Count |總計 | |
| setcommands6 |設定 (分區 6) |Count |總計 | |
| evictedkeys6 |收回的金鑰 (分區 6) |Count |總計 | |
| totalkeys6 |索引鍵總計 (節點 6) |Count |最大值 | |
| expiredkeys6 |到期的金鑰 (分區 6) |Count |總計 | |
| usedmemory6 |已用的記憶體 (分區 6) |位元組 |最大值 | |
| usedmemoryRss6 |已用的記憶體 RSS (分區 6) |位元組 |最大值 | |
| serverLoad6 |伺服器負載 (分區 6) |百分比 |最大值 | |
| cacheWrite6 |快取寫入 (分區 6) |每秒位元組 |最大值 | |
| cacheRead6 |快取讀取 (分區 6) |每秒位元組 |最大值 | |
| percentProcessorTime6 |CPU (分區 6) |百分比 |最大值 | |
| connectedclients7 |連線的用戶端 (分區 7) |Count |最大值 | |
| totalcommandsprocessed7 |總作業數 (分區 7) |Count |總計 | |
| cachehits7 |快取點擊 (分區 7) |Count |總計 | |
| cachemisses7 |快取遺漏 (分區 7) |Count |總計 | |
| getcommands7 |取得 (分區 7) |Count |總計 | |
| setcommands7 |設定 (分區 7) |Count |總計 | |
| evictedkeys7 |收回的金鑰 (分區 7) |Count |總計 | |
| totalkeys7 |索引鍵總計 (節點 7) |Count |最大值 | |
| expiredkeys7 |到期的金鑰 (分區 7) |Count |總計 | |
| usedmemory7 |已用的記憶體 (分區 7) |位元組 |最大值 | |
| usedmemoryRss7 |已用的記憶體 RSS (分區 7) |位元組 |最大值 | |
| serverLoad7 |伺服器負載 (分區 7) |百分比 |最大值 | |
| cacheWrite7 |快取寫入 (分區 7) |每秒位元組 |最大值 | |
| cacheRead7 |快取讀取 (分區 7) |每秒位元組 |最大值 | |
| percentProcessorTime7 |CPU (分區 7) |百分比 |最大值 | |
| connectedclients8 |連線的用戶端 (分區 8) |Count |最大值 | |
| totalcommandsprocessed8 |總作業數 (分區 8) |Count |總計 | |
| cachehits8 |快取點擊 (分區 8) |Count |總計 | |
| cachemisses8 |快取遺漏 (分區 8) |Count |總計 | |
| getcommands8 |取得 (分區 8) |Count |總計 | |
| setcommands8 |設定 (分區 8) |Count |總計 | |
| evictedkeys8 |收回的金鑰 (分區 8) |Count |總計 | |
| totalkeys8 |索引鍵總計 (節點 8) |Count |最大值 | |
| expiredkeys8 |到期的金鑰 (分區 8) |Count |總計 | |
| usedmemory8 |已用的記憶體 (分區 8) |位元組 |最大值 | |
| usedmemoryRss8 |已用的記憶體 RSS (分區 8) |位元組 |最大值 | |
| serverLoad8 |伺服器負載 (分區 8) |百分比 |最大值 | |
| cacheWrite8 |快取寫入 (分區 8) |每秒位元組 |最大值 | |
| cacheRead8 |快取讀取 (分區 8) |每秒位元組 |最大值 | |
| percentProcessorTime8 |CPU (分區 8) |百分比 |最大值 | |
| connectedclients9 |連線的用戶端 (分區 9) |Count |最大值 | |
| totalcommandsprocessed9 |總作業數 (分區 9) |Count |總計 | |
| cachehits9 |快取點擊 (分區 9) |Count |總計 | |
| cachemisses9 |快取遺漏 (分區 9) |Count |總計 | |
| getcommands9 |取得 (分區 9) |Count |總計 | |
| setcommands9 |設定 (分區 9) |Count |總計 | |
| evictedkeys9 |收回的金鑰 (分區 9) |Count |總計 | |
| totalkeys9 |索引鍵總計 (節點 9) |Count |最大值 | |
| expiredkeys9 |到期的金鑰 (分區 9) |Count |總計 | |
| usedmemory9 |已用的記憶體 (分區 9) |位元組 |最大值 | |
| usedmemoryRss9 |已用的記憶體 RSS (分區 9) |位元組 |最大值 | |
| serverLoad9 |伺服器負載 (分區 9) |百分比 |最大值 | |
| cacheWrite9 |快取寫入 (分區 9) |每秒位元組 |最大值 | |
| cacheRead9 |快取讀取 (分區 9) |每秒位元組 |最大值 | |
| percentProcessorTime9 |CPU (分區 9) |百分比 |最大值 | |

## <a name="microsoft.cognitiveservices/accounts"></a>Microsoft.CognitiveServices/accounts
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| NumberOfCalls |API 呼叫總數 |Count |總計 |API 呼叫總數。 |
| NumberOfFailedCalls |失敗的 API 呼叫總數 |Count |總計 |失敗的 API 呼叫總數。 |

## <a name="microsoft.compute/virtualmachines"></a>Microsoft.Compute/virtualMachines
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| 百分比 CPU |百分比 CPU |百分比 |平均值 |目前虛擬機器正在使用中的已配置計算單位百分比 |
| 網路輸入 |網路輸入 |位元組 |總計 |虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目 |
| 網路輸出 |網路輸出 |位元組 |總計 |虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目 |
| 磁碟讀取位元組 |磁碟讀取位元組 |位元組 |總計 |在監視期間從磁碟讀取的位元組總計 |
| 磁碟寫入位元組 |磁碟寫入位元組 |位元組 |總計 |在監視期間寫入磁碟的位元組總計 |
| 磁碟讀取作業數/秒 |磁碟讀取作業數/秒 |每秒計數 |平均值 |磁碟讀取 IOPS |
| 磁碟寫入作業數/秒 |磁碟寫入作業數/秒 |每秒計數 |平均值 |磁碟寫入 IOPS |

## <a name="microsoft.compute/virtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| 百分比 CPU |百分比 CPU |百分比 |平均值 |目前虛擬機器正在使用中的已配置計算單位百分比 |
| 網路輸入 |網路輸入 |位元組 |總計 |虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目 |
| 網路輸出 |網路輸出 |位元組 |總計 |虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目 |
| 磁碟讀取位元組 |磁碟讀取位元組 |位元組 |總計 |在監視期間從磁碟讀取的位元組總計 |
| 磁碟寫入位元組 |磁碟寫入位元組 |位元組 |總計 |在監視期間寫入磁碟的位元組總計 |
| 磁碟讀取作業數/秒 |磁碟讀取作業數/秒 |每秒計數 |平均值 |磁碟讀取 IOPS |
| 磁碟寫入作業數/秒 |磁碟寫入作業數/秒 |每秒計數 |平均值 |磁碟寫入 IOPS |

## <a name="microsoft.compute/virtualmachinescalesets/virtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| 百分比 CPU |百分比 CPU |百分比 |平均值 |目前虛擬機器正在使用中的已配置計算單位百分比 |
| 網路輸入 |網路輸入 |位元組 |總計 |虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目 |
| 網路輸出 |網路輸出 |位元組 |總計 |虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目 |
| 磁碟讀取位元組 |磁碟讀取位元組 |位元組 |總計 |在監視期間從磁碟讀取的位元組總計 |
| 磁碟寫入位元組 |磁碟寫入位元組 |位元組 |總計 |在監視期間寫入磁碟的位元組總計 |
| 磁碟讀取作業數/秒 |磁碟讀取作業數/秒 |每秒計數 |平均值 |磁碟讀取 IOPS |
| 磁碟寫入作業數/秒 |磁碟寫入作業數/秒 |每秒計數 |平均值 |磁碟寫入 IOPS |

## <a name="microsoft.devices/iothubs"></a>Microsoft.Devices/IotHubs
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| d2c.telemetry.ingress.allProtocol |遙測訊息傳送嘗試 |Count |總計 |要嘗試傳送至您 IoT 中樞的裝置到雲端遙測訊息數目 |
| d2c.telemetry.ingress.success |已傳送的遙測訊息 |Count |總計 |成功傳送至您 IoT 中樞的裝置到雲端遙測訊息數目 |
| c2d.commands.egress.complete.success |已完成的命令 |Count |總計 |裝置已順利完成的雲端到裝置命令數目 |
| c2d.commands.egress.abandon.success |放棄的命令 |Count |總計 |裝置所放棄的雲端到裝置命令數目 |
| c2d.commands.egress.reject.success |拒絕的命令 |Count |總計 |裝置所拒絕的雲端到裝置命令數目 |
| devices.totalDevices |裝置總計 |Count |總計 |向 IoT 中樞註冊的裝置數目 |
| devices.connectedDevices.allProtocol |連接的裝置 |Count |總計 |連接至 IoT 中樞的裝置數目 |

## <a name="microsoft.eventhub/namespaces"></a>Microsoft.EventHub/namespaces
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| INREQS |傳入的要求 |Count |總計 |命名空間的事件中樞內送訊息輸送量 |
| SUCCREQ |成功的要求 |Count |總計 |命名空間的成功要求總數 |
| FAILREQ |失敗的要求 |Count |總計 |命名空間的失敗要求總數 |
| SVRBSY |伺服器忙線錯誤 |Count |總計 |命名空間的伺服器忙線錯誤總數 |
| INTERR |內部伺服器錯誤 |Count |總計 |命名空間的內部伺服器錯誤總數 |
| MISCERR |其他錯誤 |Count |總計 |命名空間的失敗要求總數 |
| INMSGS |傳入訊息 |Count |總計 |命名空間的內送訊息總數 |
| OUTMSGS |外送訊息 |Count |總計 |命名空間的外送訊息總數 |
| EHINMBS |每秒傳入位元組 |每秒位元組 |總計 |命名空間的事件中樞內送訊息輸送量 |
| EHOUTMBS |每秒傳出位元組 |每秒位元組 |總計 |命名空間的外送訊息總數 |
| EHABL |封存待處理項目訊息 |Count |總計 |命名空間的事件中樞封存於待處理項目中訊息 |
| EHAMSGS |封存訊息 |Count |總計 |命名空間中的事件中樞已封存訊息 |
| EHAMBS |封存訊息輸送量 |每秒位元組 |總計 |命名空間中的事件中樞已封存訊息輸送量 |

## <a name="microsoft.logic/workflows"></a>Microsoft.Logic/workflows
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| RunsStarted |執行已啟動 |Count |總計 |已啟動的工作流程執行數目。 |
| RunsCompleted |完成的執行 |Count |總計 |已完成的工作流程執行數目。 |
| RunsSucceeded |已成功的執行 |Count |總計 |已成功的工作流程執行數目。 |
| RunsFailed |失敗的執行 |Count |總計 |已失敗的工作流程執行數目。 |
| RunsCancelled |取消的執行 |Count |總計 |已取消的工作流程執行數目。 |
| RunLatency |執行延遲 |秒 |平均值 |已完成的工作流程執行延遲。 |
| RunSuccessLatency |執行成功的延遲 |秒 |平均值 |已成功的工作流程執行延遲。 |
| RunThrottledEvents |執行節流的事件 |Count |總計 |工作流程動作或觸發節流的事件數目。 |
| RunFailurePercentage |執行失敗百分比 |百分比 |總計 |已失敗的工作流程執行百分比。 |
| ActionsStarted |啟動的動作 |Count |總計 |已啟動的工作流程動作數目。 |
| ActionsCompleted |完成的動作 |Count |總計 |已完成的工作流程動作數目。 |
| ActionsSucceeded |成功的動作 |Count |總計 |已成功的工作流程動作數目。 |
| ActionsFailed |動作的失敗 |Count |總計 |已失敗的工作流程動作數目。 |
| ActionsSkipped |略過的動作 |Count |總計 |已略過的工作流程動作數目。 |
| ActionLatency |動作延遲 |秒 |平均值 |已完成的工作流程動作延遲。 |
| ActionSuccessLatency |動作成功延遲 |秒 |平均值 |已成功的工作流程動作延遲。 |
| ActionThrottledEvents |動作節流的事件 |Count |總計 |工作流程動作已節流的事件數目。 |
| TriggersStarted |啟動的觸發程序 |Count |總計 |已啟動的工作流程觸發程序數目。 |
| TriggersCompleted |完成的觸發程序 |Count |總計 |已完成的工作流程觸發程序數目。 |
| TriggersSucceeded |成功的觸發程序 |Count |總計 |已成功的工作流程觸發程序數目。 |
| TriggersFailed |失敗的觸發程序 |Count |總計 |已失敗的工作流程觸發程序數目。 |
| TriggersSkipped |略過的觸發程序 |Count |總計 |已略過的工作流程觸發程序數目。 |
| TriggersFired |引發的觸發程序 |Count |總計 |已引發的工作流程觸發程序數目。 |
| TriggerLatency |觸發程序延遲 |秒 |平均值 |已完成的工作流程觸發程序延遲。 |
| TriggerFireLatency |觸發程序引發延遲 |秒 |平均值 |已引發的工作流程觸發程序延遲。 |
| TriggerSuccessLatency |觸發程序成功延遲 |秒 |平均值 |已成功的工作流程觸發程序延遲。 |
| TriggerThrottledEvents |觸發程序節流的事件 |Count |總計 |工作流程觸發程序節流的事件數目。 |
| BillableActionExecutions |可計費的動作執行 |Count |總計 |計費的工作流程動作執行數目。 |
| BillableTriggerExecutions |可計費的觸發程序執行 |Count |總計 |計費的工作流程觸發程序執行數目。 |
| TotalBillableExecutions |可計費的總執行次數 |Count |總計 |計費的工作流程執行數目。 |

## <a name="microsoft.network/applicationgateways"></a>Microsoft.Network/applicationGateways
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| 輸送量 |輸送量 |每秒位元組 |平均值 | |

## <a name="microsoft.search/searchservices"></a>Microsoft.Search/searchServices
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| SearchLatency |搜尋延遲 |秒 |平均值 |搜尋服務的平均搜尋延遲 |
| SearchQueriesPerSecond |每秒搜尋查詢 |每秒計數 |平均值 |搜尋服務的每秒搜尋查詢 |
| ThrottledSearchQueriesPercentage |節流的搜尋查詢百分比 |百分比 |平均值 |已針對搜尋查詢進行節流的搜尋服務百分比 |

## <a name="microsoft.servicebus/namespaces"></a>Microsoft.ServiceBus/namespaces
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| CPUXNS |每個命名空間的 CPU 使用量 |百分比 |最大值 |服務匯流排進階命名空間 CPU 使用量度量 |
| WSXNS |每個命名空間的記憶體大小使用量 |百分比 |最大值 |服務匯流排進階命名空間記憶體使用量度量 |

## <a name="microsoft.sql/servers/databases"></a>Microsoft.Sql/servers/databases
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| cpu_percent |CPU 百分比 |百分比 |平均值 |CPU 百分比 |
| physical_data_read_percent |資料 IO 百分比 |百分比 |平均值 |資料 IO 百分比 |
| log_write_percent |記錄 IO 百分比 |百分比 |平均值 |記錄 IO 百分比 |
| dtu_consumption_percent |DTU 百分比 |百分比 |平均值 |DTU 百分比 |
| 儲存體 |資料庫大小總計 |位元組 |最大值 |資料庫大小總計 |
| connection_successful |成功的連線 |Count |總計 |成功的連線 |
| connection_failed |失敗的連線 |Count |總計 |失敗的連線 |
| blocked_by_firewall |遭到防火牆封鎖 |Count |總計 |遭到防火牆封鎖 |
| 死結 |死結 |Count |總計 |死結 |
| storage_percent |資料庫大小百分比 |百分比 |最大值 |資料庫大小百分比 |
| xtp_storage_percent |記憶體中 OLTP 儲存體百分比 (預覽) |百分比 |平均值 |記憶體中 OLTP 儲存體百分比 (預覽) |
| workers_percent |背景工作角色百分比 |百分比 |平均值 |背景工作角色百分比 |
| sessions_percent |工作階段百分比 |百分比 |平均值 |工作階段百分比 |
| dtu_limit |DTU 限制 |Count |平均值 |DTU 限制 |
| dtu_used |已使用 DTU |Count |平均值 |已使用 DTU |
| service_level_objective |資料庫的服務等級目標 |Count |總計 |資料庫的服務等級目標 |
| dwu_limit |dwu 限制 |Count |最大值 |dwu 限制 |
| dwu_consumption_percent |DWU 百分比 |百分比 |平均值 |DWU 百分比 |
| dwu_used |已使用 DWU |Count |平均值 |已使用 DWU |

## <a name="microsoft.sql/servers/elasticpools"></a>Microsoft.Sql/servers/elasticPools
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| cpu_percent |CPU 百分比 |百分比 |平均值 |CPU 百分比 |
| physical_data_read_percent |資料 IO 百分比 |百分比 |平均值 |資料 IO 百分比 |
| log_write_percent |記錄 IO 百分比 |百分比 |平均值 |記錄 IO 百分比 |
| dtu_consumption_percent |DTU 百分比 |百分比 |平均值 |DTU 百分比 |
| storage_percent |儲存體百分比 |百分比 |平均值 |儲存體百分比 |
| workers_percent |背景工作角色百分比 |百分比 |平均值 |背景工作角色百分比 |
| sessions_percent |工作階段百分比 |百分比 |平均值 |工作階段百分比 |
| eDTU_limit |eDTU 限制 |Count |平均值 |eDTU 限制 |
| storage_limit |儲存體限制 |位元組 |平均值 |儲存體限制 |
| eDTU_used |已使用 eDTU |Count |平均值 |已使用 eDTU |
| storage_used |已使用儲存體 |位元組 |平均值 |已使用儲存體 |

## <a name="microsoft.streamanalytics/streamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| ResourceUtilization |SU % 使用率 |百分比 |最大值 |SU % 使用率 |
| InputEvents |輸入事件 |Count |總計 |輸入事件 |
| InputEventBytes |輸入事件位元組 |位元組 |總計 |輸入事件位元組 |
| LateInputEvents |延遲輸入事件 |Count |總計 |延遲輸入事件 |
| OutputEvents |輸出事件 |Count |總計 |輸出事件 |
| ConversionErrors |資料轉換錯誤 |Count |總計 |資料轉換錯誤 |
| 錯誤數 |執行階段錯誤 |Count |總計 |執行階段錯誤 |
| DroppedOrAdjustedEvents |次序錯誤事件 |Count |總計 |次序錯誤事件 |
| AMLCalloutRequests |函式要求 |Count |總計 |函式要求 |
| AMLCalloutFailedRequests |失敗的函式要求 |Count |總計 |失敗的函式要求 |
| AMLCalloutInputEvents |函式事件 |Count |總計 |函式事件 |

## <a name="microsoft.web/serverfarms"></a>Microsoft.Web/serverfarms
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| CpuPercentage |CPU 百分比 |百分比 |平均值 |CPU 百分比 |
| MemoryPercentage |記憶體百分比 |百分比 |平均值 |記憶體百分比 |
| DiskQueueLength |磁碟佇列長度 |Count |總計 |磁碟佇列長度 |
| HttpQueueLength |Http 佇列長度 |Count |總計 |Http 佇列長度 |
| BytesReceived |資料輸入 |位元組 |總計 |資料輸入 |
| BytesSent |資料輸出 |位元組 |總計 |資料輸出 |

## <a name="microsoft.web/sites"></a>Microsoft.Web/sites
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| CPU 時間 |CPU 時間 |秒 |總計 |CPU 時間 |
| 要求 |要求 |Count |總計 |要求 |
| BytesReceived |資料輸入 |位元組 |總計 |資料輸入 |
| BytesSent |資料輸出 |位元組 |總計 |資料輸出 |
| Http2xx |Http 2xx |Count |總計 |Http 2xx |
| Http3xx |Http 3xx |Count |總計 |Http 3xx |
| Http401 |Http 401 |Count |總計 |Http 401 |
| Http403 |Http 403 |Count |總計 |Http 403 |
| Http404 |Http 404 |Count |總計 |Http 404 |
| Http406 |Http 406 |Count |總計 |Http 406 |
| Http4xx |Http 4xx |Count |總計 |Http 4xx |
| Http5xx |Http 伺服器錯誤 |Count |總計 |Http 伺服器錯誤 |
| MemoryWorkingSet |記憶體工作集 |位元組 |總計 |記憶體工作集 |
| AverageMemoryWorkingSet |平均記憶體工作集 |位元組 |平均值 |平均記憶體工作集 |
| AverageResponseTime |平均回應時間 |秒 |平均值 |平均回應時間 |

## <a name="microsoft.web/sites/slots"></a>Microsoft.Web/sites/slots
| 度量 | 度量顯示名稱 | 單位 | 彙總類型 | 說明 |
| --- | --- | --- | --- | --- |
| CPU 時間 |CPU 時間 |秒 |總計 |CPU 時間 |
| 要求 |要求 |Count |總計 |要求 |
| BytesReceived |資料輸入 |位元組 |總計 |資料輸入 |
| BytesSent |資料輸出 |位元組 |總計 |資料輸出 |
| Http2xx |Http 2xx |Count |總計 |Http 2xx |
| Http3xx |Http 3xx |Count |總計 |Http 3xx |
| Http401 |Http 401 |Count |總計 |Http 401 |
| Http403 |Http 403 |Count |總計 |Http 403 |
| Http404 |Http 404 |Count |總計 |Http 404 |
| Http406 |Http 406 |Count |總計 |Http 406 |
| Http4xx |Http 4xx |Count |總計 |Http 4xx |
| Http5xx |Http 伺服器錯誤 |Count |總計 |Http 伺服器錯誤 |
| MemoryWorkingSet |記憶體工作集 |位元組 |總計 |記憶體工作集 |
| AverageMemoryWorkingSet |平均記憶體工作集 |位元組 |平均值 |平均記憶體工作集 |
| AverageResponseTime |平均回應時間 |秒 |平均值 |平均回應時間 |

## <a name="next-steps"></a>後續步驟
* [了解 Azure 監視器中的度量](monitoring-overview.md#monitoring-sources)
* [建立度量警示](../azure-portal/insights-receive-alert-notifications.md)
* [將度量匯出至儲存體、事件中樞或 Log Analytics](monitoring-overview-of-diagnostic-logs.md)

<!--HONumber=Oct16_HO2-->


