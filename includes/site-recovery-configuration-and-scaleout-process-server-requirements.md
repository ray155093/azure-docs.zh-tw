| **硬體** | |
| --- |---|
| CPU 核心數目| 8 |
| RAM| 12 GB|
| 磁碟數量 | 3 <br><br> - 作業系統磁碟<br> - 處理序伺服器快取磁碟<br> - 保留磁碟機 (適用於容錯回復)|
| - 磁碟可用空間 (處理序伺服器快取) | 600 GB
| 磁碟可用空間 (保留磁碟) | 600 GB|
| **軟體** | |
| 作業系統版本 | Windows Server 2012 R2 |
| 作業系統地區設定 | 英文 (en-us)|
| VMware vSphere PowerCLI 版本 | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server 角色 | 請勿啟用下列角色： <br> - Active Directory Domain Services <br>- 網際網路資訊服務 <br> - Hyper-V |
| **網路** | |
| 網路介面卡類型 | VMXNET3 |
| IP 位址類型 | 靜態 |
| 網際網路存取 | 伺服器應該能夠存取下列 URL (直接或透過 Proxy 伺服器)： <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (向外延展處理伺服器不需要此項) <br> - time.nist.gov <br> - time.windows.com |
| 連接埠 | 443 (控制通道協調流程)<br>9443 (資料傳輸)|
