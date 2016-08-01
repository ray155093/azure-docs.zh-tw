<properties
 pageTitle="Linux VM 的計算基準測試分數 | Microsoft Azure"
 description="比較執行 Linux 之 Azure VM 的 CoreMark 計算基準測試分數"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="07/18/2016"
 ms.author="danlep"/>

# Linux VM 的計算基準測試分數

下列 CoreMark 基準測試分數顯示執行 Ubuntu 的 Azure 高效能 VM 產品陣容的計算效能。也有 [Windows VM](virtual-machines-windows-compute-benchmark-scores.md) 的計算基準測試分數。




## A 系列 - 計算密集型


大小 | vCPU | NUMA 節點 | CPU | 執行 | 反覆運算/秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 179 | 110,294 | 554
Standard\_A9 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 189 | 210,816| 2,126
Standard\_A10 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 188 | 110,025 | 1,045
Standard\_A11 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 188 | 210,727| 2,073

## Dv2 系列


大小 | vCPU | NUMA 節點 | CPU | 執行 | 反覆運算/秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 140 | 14,852 | 780
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 133 | 29,467 | 1,863
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 139 | 56,205 | 1,167
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 126 | 108,543 | 3,446
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 126 | 205,332 | 9,998
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 125 | 28,598 | 1,510
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 131 | 55,673 | 1,418
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 140 | 107,986 | 3,089
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 140 | 208,186 | 8,839
Standard\_D15\_v2 | 20 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz |28 | 268,560 | 4,667

## F 系列

大小 | vCPU | NUMA 節點 | CPU | 執行 | 反覆運算/秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_F1 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 154 | 15,602 | 787
Standard\_F2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 126 | 29,519 | 1,233
Standard\_F4 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 147 | 58,709 | 1,227
Standard\_F8 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 224 | 112,772 | 3,006
Standard\_F16 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 42 | 218,571 | 5,113



## G 系列


大小 | vCPU | NUMA 節點 | CPU | 執行 | 反覆運算/秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1 | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 31,310 | 2,891
Standard\_G2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 60,112 | 3,537
Standard\_G3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 107,522 | 4,537
Standard\_G4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 195,116 | 5,024
Standard\_G5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 360,329 | 14,212

## GS 系列


大小 | vCPU | NUMA 節點 | CPU | 執行 | 反覆運算/秒 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 28,613 | 1,884
Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 54,348 | 3,474
Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 104,564 | 1,834
Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 194,111 | 4,735
Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 357,396 | 16,228


## 關於 CoreMark

Linux 數字是藉由在 Ubuntu 上執行 [CoreMark](http://www.eembc.org/coremark/faq.php) 來計算出。在 CoreMark 的設定中，執行緒的數目是設定為虛擬 CPU 的數目，而並行處理則是設定為 PThreads。目標反覆運算次數已根據預期的效能進行調整，以提供一個至少 20 秒 (通常會更長) 的執行階段，而最終分數代表的是已完成的反覆運算次數除以執行測試所花費秒數後得出的值。每個測試在每個 VM 上至少執行了七次。測試執行時間是在 2015 年 10 月，於測試執行當天支援 VM 的每個 Azure 公用區域中的多個 VM 上執行。
## 後續步驟



* 如需儲存體容量、磁碟詳細資料及其他選擇 VM 大小的考量，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

* 若要在 Linux VM 上執行 CoreMark 指令碼，請下載 [CoreMark 指令碼套件](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip)。

<!---HONumber=AcomDC_0720_2016-->