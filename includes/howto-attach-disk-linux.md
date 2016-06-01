
如需有關磁碟的詳細資訊，請參閱[關於虛擬機器的磁碟和 VHD](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md)。

<a id="attachempty"></a>
## 連接空的磁碟

1.  開啟 Azure CLI，並[連接到您的 Azure 訂用帳戶](../articles/xplat-cli-connect.md)。確定處於 Azure 服務管理模式 (`azure config mode asm`)。

2.  輸入 `azure vm disk attach-new` 建立並連接新的磁碟，如下所示。將 _TestVM_ 取代為 Linux 虛擬機器的名稱，並指定磁碟大小 (GB) (在此範例中為 100 GB)：

        azure vm disk attach-new TestVM 100

3.	資料磁碟在建立並連接之後，就會列在 `azure vm disk list <virtual-machine-name>` 的輸出中，如下所示：

        $ azure vm disk list TestVM
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        TestVM-2645b8030676c8f8.vhd  Linux
        data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## 連接現有磁碟

連接現有磁碟要求您在儲存體帳戶中需要有可用的 .vhd。

1. 	開啟 Azure CLI，並[連接到您的 Azure 訂用帳戶](../articles/xplat-cli-connect.md)。確定處於 Azure 服務管理模式 (`azure config mode asm`)。

2.	檢查您想要附加的 VHD 是否已上傳至 Azure 訂用帳戶：

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
    	data:    TestVM-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

3.  如果找不到您想要使用的磁碟，則可以使用 `azure vm disk create` 或 `azure vm disk upload`，將本機 VHD 上傳至您的訂用帳戶。`disk create` 範例如下：

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://mystorageaccount.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	您也可以使用 `azure vm disk upload`，將 VHD 上傳至特定的儲存體帳戶。在[這裡](virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks)閱讀管理 Azure 虛擬機器資料磁碟之命令的詳細資訊。

4.  現在，您可以將所需的 VHD 連接到您的虛擬機器︰

		$azure vm disk attach TestVM myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	請務必將 _TestVM_ 取代為您的虛擬機器名稱，並將 _myTestVhd_ 取代為您所需的 VHD。

5.	您可以使用 `azure vm disk list <virtual-machine-name>`，確認磁碟是否已連接到虛擬機器：

		$azure vm disk list TestVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        TestVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK


> [AZURE.NOTE]
新增資料磁碟之後，您必須登入虛擬機器並初始化磁碟，這樣虛擬機器才能使用該磁碟來進行儲存 (如需做法的詳細資訊，請參閱下列步驟)。
