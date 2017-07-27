<!-- A-series - compute-intensive instances, H-series -->

A8-A11 和 H 系列大小也稱為 *計算密集型執行個體*。 執行這些大小的硬體是針對計算密集型和網路密集型應用程式 (包括高效能運算 (HPC) 叢集應用程式)、模型化及模擬而設計及最佳化的。 A8-A11 系列使用 Intel Xeon E5-2670 @ 2.6 GHZ，而 H 系列使用 Intel Xeon E5-2667 v3 @ 3.2 GHz。 

Azure H 系列虛擬機器是下一代高效能運算 VM，以高端運算需求為目標，例如分子建模以及運算流體力學。 這些 8 與 16 核心 VM 是以 Intel Haswell E5-2667 V3 處理器技術，搭載 DDR4 記憶體與本機 SSD 型儲存體為基礎建置。 

除了大量的 CPU 能力，H 系列使用 FDR InfiniBand 與數個記憶體組態，針對低延遲 RDMA 網路提供不同的選項，以支援記憶體大量運算需求。



## <a name="h-series"></a>H 系列

ACU：290 - 300

| 大小 | CPU 核心 | 記憶體：GiB | 本機 SSD: GiB | 最大資料磁碟 | 最大磁碟輸送量︰IOPS | 最大 NIC |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |16 |16 x 500 |2  |
| Standard_H16 |16 |112 |2000 |32 |32 x 500 |4 |
| Standard_H8m |8 |112 |1000 |16 |16 x 500 |2  |
| Standard_H16m |16 |224 |2000 |32 |32 x 500 |4  |
| Standard_H16r* |16 |112 |2000 |32 |32 x 500 |4  |
| Standard_H16mr* |16 |224 |2000 |32 |32 x 500 |4 |

*對於 MPI 應用程式，FDR InfiniBand 網路會啟用專用 RDMA 後端網路，以提供超低延遲和高頻寬。

<br>



## <a name="a-series---compute-intensive-instances"></a>A 系列 - 大量計算執行個體

ACU：225

| 大小 | CPU 核心 | 記憶體：GiB | 本機 HDD: GiB | 最大資料磁碟 | 最大資料磁碟輸送量︰IOPS | 最大 NIC|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16x500 |2 |
| Standard_A9* |16 |112 |382 |16 |16x500 |4 |
| Standard_A10 |8 |56 |382 |16 |16x500 |2  |
| Standard_A11 |16 |112 |382 |16 |16x500 |4 |

*對於 MPI 應用程式，FDR InfiniBand 網路會啟用專用 RDMA 後端網路，以提供超低延遲和高頻寬。

<br>



