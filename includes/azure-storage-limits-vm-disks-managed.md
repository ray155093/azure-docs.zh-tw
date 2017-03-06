**標準受管理的虛擬機器磁碟**

| 標準磁碟類型 | S4 | S6 | S10 | S20 | S30 |
| --- | --- |--- | --- | --- | --- |
| 磁碟大小 | 30 GB | 64 GB | 128 GB | 512 GB | 1024 GB (1 TB)|
| 每一磁碟的 IOPS | 500 |500 |500 |500 |500 |
| 每一磁碟的輸送量 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 

**進階受管理的虛擬機器磁碟：每一磁碟限制**

| 進階儲存體磁碟類型 | P10 | P20 | P30 |
| --- | --- | --- | --- |
| 磁碟大小 |128 GB |512 GB |1024 GB (1 TB) |
| 每一磁碟的 IOPS 上限 |500 |2300 |5000 |
| 每一磁碟的輸送量上限 |100 MB/秒 |150 MB/秒 |200 MB/秒 |

**進階受管理的虛擬機器磁碟：每個 VM 限制**

| 資源 | 預設限制 |
| --- | --- |
| 每個 VM 的最大 IOPS |80,000 IOPS (使用 GS5 VM<sup>1</sup>) |
| 每一 VM 輸送量上限 |2,000 MB/秒 (使用 GS5 VM<sup>1</sup>) |

<sup>1</sup>如需其他 VM 大小的限制，請參考 [VM 大小](../articles/virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 
