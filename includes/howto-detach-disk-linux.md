當您不再需要某個連結至虛擬機器 (VM) 的資料磁碟時，可以輕鬆將它中斷連結。這會將磁碟與 VM 中斷連結，但這不會將它從儲存體中移除。如果您想要再次使用磁碟上現有的資料，您可以將磁碟重新連結至相同 VM 或其他 VM。

> [AZURE.NOTE] Azure 中的 VM 使用不同類型的磁碟 - 作業系統磁碟、本機暫存磁碟，以及選擇性的資料磁碟。如需詳細資訊，請參閱[有關虛擬機器的磁碟和 VHD](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md)。您無法將作業系統磁碟中斷連結，除非您同時刪除 VM。


## 尋找磁碟

您必須先找出 LUN 編號 (要中斷連結之磁碟的識別碼)，才能將磁碟與 VM 中斷連結。若要這樣做，請遵循下列步驟：

1. 	開啟 Azure CLI 並[連接至您的 Azure 訂用帳戶](../articles/xplat-cli-connect.md)。確定處於 Azure 服務管理模式中 (`azure config mode asm`)。

2. 	使用 `azure vm disk list
	<virtual-machine-name>` 來找出哪些磁碟已連結至您的 VM：

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

3. 	請記下您要卸離之磁碟的 LUN 或**邏輯單元編號**。

## 移除磁碟的作業系統參考

在卸離 Linux 客體中的磁碟之前，您應該確認並未使用磁碟上任何一個分割區，並確保作業系統不會在重新開機後嘗試重新裝載。下列步驟可復原您在[附加](../articles/virtual-machines-linux-classic-attach-disk.md)磁碟時可能建立的組態。

1. 使用 `lsscsi` 命令來找出磁碟識別碼。您可透過 `yum install lsscsi` (Red Hat 式散發) 或 `apt-get install lsscsi`(Debian 式散發) 來安裝 `lsscsi`。您可以使用上述的 LUN 編號找到要尋找的磁碟識別碼。在每個資料列的 Tuple 中，最後一個數字即為 LUN。在以下範例中，LUN 0 將對應至 _/dev/sdc_

			ops@TestVM:~$ lsscsi
			[1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
			[2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
			[3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
			[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
			[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd

2. 使用 `fdisk -l <disk>` 探索與要卸離之磁碟相關聯的分割區。
3. 
			$ sudo fdisk -l /dev/sdc
			Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
			Units = sectors of 1 * 512 = 512 bytes
			Sector size (logical/physical): 512 bytes / 512 bytes
			I/O size (minimum/optimal): 512 bytes / 512 bytes
			Disk label type: dos
			Disk identifier: 0x5a1d2a1a

			   Device Boot      Start         End      Blocks   Id  System
			/dev/sdc1            2048  2145386495  1072692224   83  Linux

3. 取消掛接每個列出的磁碟分割區。在此範例中是：`$ sudo umount /dev/sdc1`
4. 使用 `blkid` 命令來探索所有分割區的 UUID

			$ sudo blkid
			/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
			/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
			/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
			/dev/sdd1: UUID="44444444-4b4b-4c4c-4d4d-4e4e4e4e4e4e" TYPE="ext4
			
5. 移除 **/etc/fstab** 檔案 (與裝置路徑或要卸離之磁碟的所有分割區的 UUID 相關聯) 中的項目。此範例中的項目可能是︰

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
或

		/dev/sdc1   /datadrive   ext4   defaults   1   2


## 卸離磁碟

找出磁碟的 LUN 編號並移除作業系統參考之後，您可以開始卸離磁碟︰

1. 	執行命令 `azure vm disk detach
 	<virtual-machine-name> <LUN>`，從虛擬機器卸離選取的磁碟：

		$azure vm disk detach UbuntuVM 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	您可以執行此命令，檢查是否已卸離磁碟：

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

卸離的磁碟仍留在儲存體中，但不再連接至虛擬機器。

<!---HONumber=AcomDC_0706_2016-->