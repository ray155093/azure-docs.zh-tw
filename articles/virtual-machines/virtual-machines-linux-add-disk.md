---
title: "在 Linux VM 中新增磁碟 | Microsoft Docs"
description: "了解如何在 Linux VM 中新增永續性磁碟"
keywords: "linux 虛擬機器,新增資源磁碟"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 3005a066-7a84-4dc5-bdaa-574c75e6e411
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.date: 09/06/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 17ddda372f3a232be62e565b700bb1be967fb8e3
ms.openlocfilehash: 5e9fb48fdf0da9a1c75f4d08ab7d97976859340c


---
# <a name="add-a-disk-to-a-linux-vm"></a>在 Linux VM 中新增磁碟
本文說明如何將持續性磁碟連接到您的 VM，以便您保留資料 - 即使您的 VM 會由於維護或調整大小而重新佈建。 若要新增磁碟，您需要在 Resource Manager 模式中設定的 [Azure CLI](../xplat-cli-install.md) (`azure config mode arm`)。  

## <a name="quick-commands"></a>快速命令
下列範例會將 `50`GB 磁碟附加至名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 VM：

```azurecli
azure vm disk attach-new myResourceGroup myVM 50
```

## <a name="attach-a-disk"></a>連接磁碟
連接新磁碟很快。 輸入 `azure vm disk attach-new myResourceGroup myVM sizeInGB` ，就能為 VM 建立並連接新的 GB 磁碟。 如果您未明確識別儲存體帳戶，則您所建立的任何磁碟都會放在作業系統磁碟所在的相同儲存體帳戶中。 下列範例會將 `50`GB 磁碟附加至名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 VM：

```azurecli
azure vm disk attach-new myResourceGroup myVM 50
```

輸出

```azurecli
info:    Executing command vm disk attach-new
+ Looking up the VM "myVM"
info:    New data disk location: https://mystorageaccount.blob.core.windows.net/vhds/myVM-20150526-043.vhd
+ Updating VM "myVM"
info:    vm disk attach-new command OK
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>連接到 Linux VM 以掛接新磁碟
> [!NOTE]
> 本主題會利用使用者名稱和密碼連線到 VM。 若要使用公開和私密金鑰組來與您的 VM 通訊，請參閱 [如何搭配使用 SSH 與 Azure 上的 Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 您可以修改利用 `azure vm quick-create` 命令建立之 VM 的**SSH** 連線能力，方法為使用 `azure vm reset-access` 命令來完全地重設 **SSH** 存取、新增或移除使用者，或新增公開金鑰檔案來保護存取安全。
> 
> 

您必須使用 SSH 登入 Azure VM 來分割、格式化和掛接新磁碟，以供 Linux VM 使用。 如果您不熟悉使用 **ssh** 進行連接，此命令會採用 `ssh <username>@<FQDNofAzureVM> -p <the ssh port>` 形式，如下所示：

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com -p 22
```

輸出

```bash
The authenticity of host 'mypublicdns.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@mypublicdns.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myVM:~$
```

現在已連線到 VM，您已準備好連接磁碟。  請先使用 `dmesg | grep SCSI` (您用來探索新磁碟的方法可能有所不同) 尋找該磁碟。 在此案例中，它看起來如下：

```bash
dmesg | grep SCSI
```

輸出

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

而且，在本主題的案例中， `sdc` 磁碟是我們想要的磁碟。 現在使用 `sudo fdisk /dev/sdc` 來分割磁碟 -- 假設在您的案例中，磁碟為 `sdc`，使它成為磁碟分割 1 上的主要磁碟，並接受其他預設值。

```bash
sudo fdisk /dev/sdc
```

輸出

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

在命令提示字元中，輸入 `p` 來建立磁碟分割：

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

同時，使用 **mkfs** 命令來指定檔案系統類型和裝置名稱，將檔案系統寫入磁碟分割。 在本主題中，我們將使用先前內容中的 `ext4` 和 `/dev/sdc1`：

```bash
sudo mkfs -t ext4 /dev/sdc1
```

輸出

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

現在我們會使用 `mkdir`，建立目錄來掛接檔案系統：

```bash
sudo mkdir /datadrive
```

而您可以使用 `mount`來掛接目錄：

```bash
sudo mount /dev/sdc1 /datadrive
```

資料磁碟現在可以當做 `/datadrive`來使用。

```bash
ls
```

輸出

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

為了確保重新開機之後自動重新掛接磁碟機，必須將磁碟機新增至 /etc/fstab 檔案。 此外，強烈建議在 /et/fstab 中使用全域唯一識別碼 (Universally Unique IDentifier, UUID) 來參考磁碟機，而不只是裝置名稱 (例如， `/dev/sdc1`)。 如果作業系統在開機期間偵測到磁碟錯誤，使用 UUID 可避免將不正確的磁碟掛接到指定的位置。 其餘的資料磁碟則會被指派這些相同的裝置識別碼。 若要尋找新磁碟機的 UUID，請使用 **blkid** 公用程式：

```bash
sudo -i blkid
```

輸出大致如下：

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> 不當編輯 **/etc/fstab** 檔案會導致系統無法開機。 如果不確定，請參閱散發套件的文件，以取得如何適當編輯此檔案的相關資訊。 在編輯之前，也建議先備份 /etc/fstab 檔案。
> 
> 

接下來，在文字編輯器中開啟 **/etc/fstab** 檔案：

```bash
sudo vi /etc/fstab
```

在此範例中，我們會使用先前步驟所建立之新的 **/dev/sdc1** 裝置的 UUID 值，並使用掛接點 **/datadrive**。 在 **/etc/fstab** 檔案的結尾加入以下程式碼：

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> 稍後移除資料磁碟而不編輯 fstab，可能會造成 VM 無法開機。 大多數的散發套件會提供 `nofail` 和/或 `nobootwait` fstab 選項。 即使磁碟在開機時無法掛接，這些選項也能讓系統開機。 請查閱散發套件的文件，以取得這些參數的相關資訊。
> 
> **Nofail** 選項可確保即使檔案系統已損毀或磁碟在開機時並不存在，仍然會啟動 VM。 若不使用此選項，您可能會遇到[因為 FSTAB 錯誤所以無法 SSH 到 Linux VM](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) 中所述的行為

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure 中 Linux 的 TRIM/UNMAP 支援
有些 Linux 核心會支援 TRIM/UNMAP 作業以捨棄磁碟上未使用的區塊。 這主要是在標準儲存體中相當實用，可用來通知 Azure 已刪除的頁面已不再有效而可予以捨棄。 如果您建立大型檔案，然後再將它們刪除，這便可節省成本。

有兩種方式可在 Linux VM 中啟用 TRIM 支援。 像往常一樣，請參閱您的散發套件以了解建議的方法︰

* 在 `/etc/fstab` 中使用 `discard` 掛接選項，例如：

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* 在某些情況下，`discard` 選項可能會影響效能。 或者，您也可以從命令列手動執行 `fstrim` 命令，或將它新增到 crontab 來定期執行︰
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>疑難排解
[!INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>後續步驟
* 請記住，如果將新磁碟重新開機，除非您將該資訊寫入 [/etc/fstab](http://en.wikipedia.org/wiki/Fstab) 檔案，否則該磁碟無法供 VM 使用。
* 若要確保您的 Linux VM 已正確設定，請檢閱 [最佳化您的 Linux 機器效能](virtual-machines-linux-optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 建議。
* 新增其他磁碟以擴充儲存體容量，並 [設定 RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 以提升效能。




<!--HONumber=Dec16_HO1-->


