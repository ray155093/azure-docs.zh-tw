<properties
	pageTitle="將磁碟附加至 Linux VM | Microsoft Azure"
	description="了解如何將資料磁碟連接至執行 Linux 的 Azure 虛擬機器並初始化磁碟，以便開始使用。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="iainfou"/>

# 如何將資料磁碟連接至 Linux 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Resource Manager 模型。參閱如何[使用 Resource Manager 部署模型連接資料磁碟](virtual-machines-linux-add-disk.md)。

您可以將空的磁碟和含有資料的磁碟連接到 Azure VM。這兩種類型的磁碟都是位於 Azure 儲存體帳戶中的 .vhd 檔案。就像將任何磁碟新增到 Linux 機器一樣，連接之後您需要將它初始化和格式化才可供使用。本文將會詳細說明連接空的磁碟和連接含有資料的磁碟到 VM，以及初始化和格式化新磁碟的方法。

> [AZURE.NOTE] 最好使用一或多個不同的磁碟來儲存虛擬機器的資料。當您建立 Azure 虛擬機器時，它會有作業系統磁碟和暫存磁碟。**請勿使用暫存磁碟來儲存持續資料。** 顧名思義，它只提供暫存儲存空間。它並不提供備援或備份，因為它不在 Azure 儲存體內。暫存磁碟通常是由 Azure Linux 代理程式管理，並自動掛接到 **/mnt/resource** (或 Ubuntu 映像中的**/mnt**)。換句話說，Linux 核心可能會將資料磁碟命名為 `/dev/sdc`，且您必須分割、格式化及掛接此資源。如需詳細資訊，請參閱 [Azure Linux 代理程式使用者指南][Agent]。

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## 在 Linux 中初始化新的資料磁碟

1. SSH 連線到您的 VM。如需詳細資訊，請參閱[如何登入執行 Linux 的虛擬機器][Logon]。

2. 接下來您需要尋找資料磁碟的裝置識別碼以進行初始化。作法有二：

	a) Grep SCSI 裝置中的記錄檔，如以下命令︰

			$sudo grep SCSI /var/log/messages

	針對最新的 Ubuntu 散發套件，您可能需要使用 `sudo grep SCSI /var/log/syslog`，因為預設可能會停用記錄到 `/var/log/messages` 的功能。

	在出現的訊息中，您可以找到最後一個新增之資料磁碟的識別碼。

	![取得磁碟訊息](./media/virtual-machines-linux-classic-attach-disk/scsidisklog.png)

	或

	b) 使用 `lsscsi` 命令來找出裝置識別碼。您可透過 `yum install lsscsi` (Red Hat 式散發) 或 `apt-get install lsscsi`(Debian 式散發) 來安裝 `lsscsi`。您可以透過磁碟的 _LUN_ (亦稱為**邏輯單元編號**) 找到您所需的磁碟。例如，您可從 `azure vm disk list <virtual-machine>` 輕易看到所附加磁碟的 _LUN_，如下所示：

			~$ azure vm disk list TestVM
			info:    Executing command vm disk list
			+ Fetching disk images
			+ Getting virtual machines
			+ Getting VM disks
			data:    Lun  Size(GB)  Blob-Name                         OS
			data:    ---  --------  --------------------------------  -----
			data:         30        TestVM-2645b8030676c8f8.vhd  Linux
			data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
			info:    vm disk list command OK

	針對相同範例的虛擬機器，與 `lsscsi` 的輸出進行比較：

			ops@TestVM:~$ lsscsi
			[1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
			[2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
			[3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
			[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc

	在每個資料列的 Tuple 中，最後一個數字即為 _LUN_。如需詳細資訊，請參閱 `man lsscsi`。

3. 在出現提示時輸入下列命令，以建立新裝置：

		$sudo fdisk /dev/sdc


4. 出現提示時，輸入 **n** 建立新的磁碟分割。


	![建立新的裝置](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)

5. 出現提示時，輸入 **p** 將磁碟分割設為主要磁碟分割，輸入 **1** 設為第一個磁碟分割，然後按 Enter 鍵接受預設的磁柱值。在某些系統上，它可能會顯示第一個和最後一個磁區的預設值，而不是圓柱圖。您可以選擇接受這些預設值。


	![建立磁碟分割](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)



6. 輸入 **p** 查看目前分割之磁碟的詳細資料。


	![列出磁碟資訊](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)



7. 輸入 **w** 寫入磁碟的設定。


	![寫入磁碟變更](./media/virtual-machines-linux-classic-attach-disk/fdiskwritedisk.png)

8. 您現在可以在新的磁碟分割上建立檔案系統。在裝置識別碼後加上磁碟分割編號 (在以下範例中為 `/dev/sdc1`)。以下範例會在 /dev/sdc1 上建立 ext4 磁碟分割：

		# sudo mkfs -t ext4 /dev/sdc1

	![建立檔案系統](./media/virtual-machines-linux-classic-attach-disk/mkfsext4.png)

	>[AZURE.NOTE] 請注意，SUSE Linux Enterprise 11 系統只支援對 ext4 檔案系統的唯讀存取。針對這些系統，建議您將新檔案系統格式化為 ext3，而非 ext4。


9. 建立目錄以掛接新的檔案系統，如下所示：

		# sudo mkdir /datadrive


10. 最後您可以掛接磁碟機，如下所示︰

		# sudo mount /dev/sdc1 /datadrive

	資料磁碟現在可以當做 **/datadrive** 來使用。
	
	![建立目錄和掛接磁碟](./media/virtual-machines-linux-classic-attach-disk/mkdirandmount.png)


11. 將新的磁碟機新增至 /etc/fstab：

	為了確保重新開機之後自動重新掛接磁碟機，必須將磁碟機新增至 /etc/fstab 檔案。此外，強烈建議在 /et/fstab 中使用全域唯一識別碼 (Universally Unique IDentifier, UUID) 來參考磁碟機，而不只是裝置名稱 (例如，/dev/sdc1)。若要尋找新磁碟機的 UUID，您可以使用 **blkid** 公用程式：

		# sudo -i blkid

	輸出類似如下範例：

		/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
		/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
		/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


	>[AZURE.NOTE] 不當編輯 **/etc/fstab** 檔案會導致系統無法開機。如果不確定，請參閱散發套件的文件，以取得如何適當編輯此檔案的相關資訊。在編輯之前，也建議先備份 /etc/fstab 檔案。

	接下來，在文字編輯器中開啟 **/etc/fstab** 檔案：

		# sudo vi /etc/fstab

	在此範例中，我們使用先前步驟所建立之新的 **/dev/sdc1** 裝置的 UUID 值，並使用掛接點 **/datadrive**。在 **/etc/fstab** 檔案的結尾加入以下程式碼：

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2

	或者，在基於 SUSE Linux 的系統上，您可能需要使用稍微不同的格式：

		/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults   1   2

	現在，您可以測試檔案系統是否適當掛接，方法是取消掛接檔案系統，再重新掛接，例如使用先前步驟中建立的範例掛接點 `/datadrive`：

		# sudo umount /datadrive
		# sudo mount /datadrive

	如果 `mount` 命令發生錯誤，請檢查 /etc/fstab 檔案的語法是否正確。如果還有建立其他資料磁碟機或磁碟分割，同樣也需要分別在 /etc/fstab 中輸入。

	您必須使用此命令將磁碟機設為可寫入：

		# sudo chmod go+w /datadrive

>[AZURE.NOTE] 後續移除資料磁碟而不編輯 fstab，可能會造成 VM 無法開機。如果這是常見情況，那麼多數散發套件會提供 `nofail` 和/或 `nobootwait` fstab 選項，即使磁碟在開機時無法掛接，也能讓系統開機。請查閱散發套件的文件，以取得這些參數的相關資訊。

## 其他資源
[如何登入執行 Linux 的虛擬機器][Logon]

[如何從 Linux 虛擬機器卸離磁碟](virtual-machines-linux-classic-detach-disk.md)

[搭配服務管理 API 使用 Azure CLI](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-classic-log-on.md

<!---HONumber=AcomDC_0518_2016-->