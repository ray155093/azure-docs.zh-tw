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


## 卸離磁碟

在您找到磁碟的 LUN 編號之後，就可以開始卸離磁碟：

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

<!---HONumber=AcomDC_0608_2016-->