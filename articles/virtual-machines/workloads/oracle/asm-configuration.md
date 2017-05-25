---
title: "在 Azure Linux 虛擬機器上設定 Oracle ASM | Microsoft Docs"
description: "快速在您的 Azure 環境中啟動並執行 Oracle ASM。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/2/2017
ms.author: v-shiuma
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: e3c98a3d4d7012f6cb6c7e1a1b8263e6ecdee57b
ms.contentlocale: zh-tw
ms.lasthandoff: 05/17/2017

---

# <a name="set-up-oracle-asm-on-an-azue-linux-virtual-machine"></a>在 Azure Linux 虛擬機器上設定 Oracle ASM 

請透過本文了解如何在 Azure 中的 Oracle Linux 虛擬機器 (VM) 上安裝和設定 Oracle Automatic Storage Management (Oracle ASM)。

在開始之前，請確定您已安裝 Azure CLI。 如需詳細資訊，請參閱 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

## <a name="sign-in-to-azure"></a>登入 Azure 

若要在 Azure CLI 中登入您的 Azure 訂用帳戶，請使用 [az login](/cli/azure/#login) 命令。 然後，遵循螢幕上的指示來進行。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

若要建立資源群組，請使用 [az group create](/cli/azure/group#create) 命令。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 westus 位置建立名為 myResourceGroup 的資源群組。

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-vm"></a>建立 VM

若要建立 VM 來與 Oracle ASM 搭配使用，請完成下列步驟︰

1.  若要建立虛擬機器，請使用 [az vm create](/cli/azure/vm#create) 命令。 

  下列範例會建立名為 myVM 的 VM。 如果預設的金鑰位置還沒有 SSH 金鑰的話，此範例也會建立這些金鑰。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。  

  ```azurecli
  az vm create --resource-group myResourceGroup --name myVM --image Oracle:Oracle-Database-Ee:12.1.0.2:latest --size Standard_DS2_v2 --generate-ssh-keys
  ```

  在您建立 VM 後，Azure CLI 會顯示類似下列範例的資訊。 請記下 `publicIpAddress` 的值。 您必須使用此位址來存取 VM。

  ```azurecli
  {
    "fqdns": "",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
    "location": "westus",
    "macAddress": "00-0D-3A-36-2F-56",
    "powerState": "VM running",
    "privateIpAddress": "10.0.0.4",
    "publicIpAddress": "13.64.104.241",
    "resourceGroup": "myResourceGroup"
  }
  ```

2.  新增要供 Oracle ASM 組態使用的磁碟︰

  ```azurecli
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk4 --new --size-gb 50
  ```

## <a name="connect-to-the-vm"></a>連接至 VM

若要對 VM 建立 SSH 工作階段，請使用下列命令。 以 VM 的 `publicIpAddress` 值取代 IP 位址。

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>安裝 Oracle ASM

若要安裝 Oracle ASM，請完成下列步驟。 

如需關於安裝 Oracle ASM 的詳細資訊，請參閱 [Oracle ASMLib Downloads for Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)。  

1. 執行 `yum list`：

  ```bash
  $ sudo su -
  # yum list
  ```
  當您第一次執行 `yum list` 時，系統可能需要幾分鐘的時間才能載入此清單。

2.  執行下列額外命令︰

  ```bash
  # yum list | grep oracleasm
  # yum -y install kmod-oracleasm.x86_64
  # yum -y install oracleasm-support.x86_64
  # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
  ```

3.  確認您已安裝 Oracle ASM：

  ```bash
  # rpm -qa |grep oracleasm
  oracleasm-support-2.1.10-4.el6.x86_64
  kmod-oracleasm-2.0.8-15.el6_9.x86_64
  oracleasmlib-2.0.12-1.el6.x86_64
  ```

4.  新增使用者和群組：

  ```bash
  # groupadd -g 54345 asmadmin
  # groupadd -g 54346 asmdba
  # groupadd -g 54347 asmoper
  # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
  # usermod -g oinstall -G dba,asmdba,asmadmin oracle
  ```

5.  確認使用者和群組：

  ```bash
  # id grid
  uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
  ```

6.  建立資料夾，並變更擁有者：

  ```bash
  # mkdir /u01/app/grid
  # chown grid:oinstall /u01/app/grid
  ```

## <a name="set-up-oracle-asm"></a>設定 Oracle ASM

在此教學課程中，預設使用者是「grid」，預設群組是「asmadmin」。 確定「oracle」使用者屬於 asmadmin 群組。 若要設定 Oracle ASM 的安裝，請完成下列步驟︰

1.  設定 Oracle ASM 程式庫驅動程式︰

  ```bash
  # /usr/sbin/oracleasm configure -i

  Configuring the Oracle ASM library driver.

  This will configure the on-boot properties of the Oracle ASM library
  driver. The following questions will determine whether the driver is
  loaded on boot and what permissions it will have. The current values
  will be shown in brackets ('[]'). Hitting <ENTER> without typing an
  answer will keep that current value. Ctrl-C will abort.

  Default user to own the driver interface []: grid
  Default group to own the driver interface []: asmadmin
  Start Oracle ASM library driver on boot (y/n) [n]: y
  Scan for Oracle ASM disks on boot (y/n) [y]: y
  Writing Oracle ASM library driver configuration: done
  ```

2.  檢視磁碟組態︰
  ```bash
  # cat /proc/partitions
  ```

3.  將磁碟格式化︰

  ```bash
  # fdisk /dev/sdc
  Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
  Building a new DOS disklabel with disk identifier 0xf865c6ca.
  Changes will remain in memory only, until you decide to write them.
  After that, of course, the previous content won't be recoverable.

  Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

  The device presents a logical sector size that is smaller than
  the physical sector size. Aligning to a physical sector (or optimal
  I/O) size boundary is recommended, or performance may be impacted.

  WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
          switch off the mode (command 'c') and change display units to
          sectors (command 'u').

  Command (m for help): n
  Command action
    e   extended
    p   primary partition (1-4)
  p
  Partition number (1-4): 1
  First cylinder (1-6527, default 1):
  Using default value 1
  Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
  Using default value 6527

  Command (m for help): w
  The partition table has been altered!

  Calling ioctl() to re-read partition table.
  Syncing disks.
  ```

4.  針對 /dev/sdd、/dev/sde 和 /dev/sdf 重複執行上一個步驟。

5.  檢查磁碟組態︰

  ```bash
  # cat /proc/partitions
  major minor  #blocks  name

    8       16   14680064 sdb
    8       17   14678976 sdb1
    8       32   52428800 sdc
    8       33   52428096 sdc1
    8       48   52428800 sdd
    8       49   52428096 sdd1
    8       64   52428800 sde
    8       65   52428096 sde1
    8       80   52428800 sdf
    8       81   52428096 sdf1
    8        0   52428800 sda
    8        1     512000 sda1
    8        2   51915776 sda2
    11        0    1048575 sr0
  ```

6.  檢查 Oracle ASM 服務狀態︰

  ```bash
  # service oracleasm status
  Checking if ASM is loaded: no
  Checking if /dev/oracleasm is mounted: no
  ```

7.  啟動 Oracle ASM 服務︰

  ```bash
  # service oracleasm start
  Initializing the Oracle ASMLib driver:                     [  OK  ]
  Scanning the system for Oracle ASMLib disks:               [  OK  ]
  ```

8.  建立 Oracle ASM 磁碟︰

  ```bash
  # service oracleasm createdisk ASMSP /dev/sdc1
  Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

  # service oracleasm createdisk DATA /dev/sdd1
  Marking disk "DATA" as an ASM disk:                        [  OK  ]

  # service oracleasm createdisk DATA1 /dev/sde1
  Marking disk "DATA1" as an ASM disk:                       [  OK  ]

  [root@myVM ~]# service oracleasm createdisk FRA /dev/sdf1
  Marking disk "FRA" as an ASM disk:                         [  OK  ]
  ```

9.  列出 Oracle ASM 磁碟︰

  ```bash
  # service oracleasm listdisks
  ASMSP
  DATA
  DATA1
  FRA
  ```

10. 變更 root、oracle 和 grid 使用者的密碼 (您之後會在安裝期間使用這些密碼)︰

  ```bash
  # passwd oracle
  # passwd grid
  # passwd root
  ```

11. 變更資料夾權限︰

  ```bash
  # chmod -R 775 /opt
  # chown grid:oinstall /opt
  # chown oracle:oinstall /dev/sdc1
  # chown oracle:oinstall /dev/sdd1
  # chown oracle:oinstall /dev/sde1
  # chown oracle:oinstall /dev/sdf1
  # chmod 600 /dev/sdc1
  # chmod 600 /dev/sdd1
  # chmod 600 /dev/sde1
  # chmod 600 /dev/sdf1
  ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>下載並準備 Oracle Grid Infrastructure

若要下載並準備 Oracle Grid Infrastructure 軟體，請完成下列步驟︰

1.  從 [Oracle ASM 下載頁面](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html)下載 Oracle Grid Infrastructure。 

  在 **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64** 下載標題底下，應該有兩個可供下載的 .zip 檔案。

2.  將 .zip 檔下載到用戶端電腦之後，您可以使用「安全複製通訊協定 (SCP)」將這些檔案複製到 VM。

    ```bash
    scp *.zip <publicIpAddress>:<folder>
    ```

3.  將 .zip 檔移到 /opt 資料夾。 接著，變更檔案的擁有者︰

    ```bash
    # mv <folder>/*.zip /opt
    # cd /opt
    # chown grid:oinstall linuxamd64_12102_grid_1of2.zip
    # chown grid:oinstall linuxamd64_12102_grid_2of2.zip
    ```

4.  將檔案解壓縮 (如果您尚未安裝 Linux 解壓縮公用程式，請加以安裝)︰

    ```bash
    # yum install unzip
    # unzip linuxamd64_12102_grid_1of2.zip
    # unzip linuxamd64_12102_grid_2of2.zip
    ```

5.  變更權限：

    ```bash
    # chown -R grid:oinstall /opt/grid
    ```

6.  關閉防火牆︰

    ```bash
    # service iptables status
    # service iptables stop
    ```

7.  檢查可用的交換空間。 您必須至少有 6 GB 的交換空間以便安裝 Oracle Grid Infrastructure︰

    ```bash
    # swapon -s
    ```

    如果您的交換空間不足 6 GB，您可以執行下列命令來新增更多交換空間︰

    ```bash
    # dd if=/dev/zero of=/extraswap bs=1M count=6144
    6144+0 records in
    6144+0 records out
    6442450944 bytes (6.4 GB) copied, 141.245 s, 45.6 MB/s

    # mkswap /mnt/resource/extraswap
    mkswap: /mnt/resource/extraswap: warning: don't erase bootbits sectors
            on whole disk. Use -f to force.
    Setting up swapspace version 1, size = 6291452 KiB
    no label, UUID=80bd7816-b3a2-4eec-a824-733209644fc5
    # swapon /mnt/resource/extraswap

    ```

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>讓用戶端和 VM 準備好執行 X11 (僅適用於 Windows 用戶端)

若要讓用戶端和 VM 準備好執行 X11，請完成下列步驟︰ 

1.  以 root 身分登入，然後編輯 /etc/ssh/ssh_config 檔案。 將 Forwardx11 的設定變更為 `yes`：

  ```
  #   ForwardX11 no
  ForwardX11 yes

  ```

2.  將 PuTTY 和 Xming 下載到 Windows 電腦︰

  * [下載 PuTTY](http://www.putty.org/)
  * [下載 Xming](https://xming.en.softonic.com/)

3.  在 PuTTY 安裝好之後，請在 PuTTY 資料夾 (例如 C:\Program Files\PuTTY) 執行 puttygen.exe (PuTTY 金鑰產生器)。

4.  在 PuTTY 金鑰產生器中︰

  1.  若要產生金鑰，請選取 [產生] 按鈕。 
  2.  複製金鑰的內容 (Ctrl+C)。
  3.  選取 [儲存私密金鑰] 按鈕。 
  4.  略過隨之出現的警告，然後選取 [確定]。

  ![PuTTY 金鑰產生器頁面的螢幕擷取畫面](./media/asm-configuration/puttykeygen.png)

5.  在您的 VM 中，執行下列命令︰

  ```bash
  # sudo su - grid
  $ mkdir .ssh (if not already created)
  $ cd .ssh
  ```

6.  建立名為 authorized_keys 的檔案。 在此檔案中貼上金鑰的內容，然後儲存檔案。

  > [!NOTE]
  > 金鑰中必須包含字串 `ssh-rsa`。 此外，金鑰的內容必須是單行文字。
  >  

7.  啟動 PuTTY。 在 [類別] 窗格中，移至 [連線] > [SSH] > [驗證]。 在 [用於驗證的私密金鑰檔] 方塊中，瀏覽至您稍早產生的金鑰。

  ![[設定私密金鑰] 頁面上的螢幕擷取畫面](./media/asm-configuration/setprivatekey.png)

9.  在 [類別] 窗格中，移至 [連線] > [SSH] > [X11]。 選取 [啟用 X11 轉送] 方塊。

  ![[啟用 X11] 頁面的螢幕擷取畫面](./media/asm-configuration/enablex11.png)

10. 在 [類別] 窗格中，移至 [工作階段]。 輸入主機資訊，然後選取 [開啟]。

  ![[工作階段] 頁面的螢幕擷取畫面](./media/asm-configuration/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>安裝 Oracle Grid Infrastructure

若要安裝 Oracle Grid Infrastructure，請完成下列步驟：

1. 以 grid 的身分登入  (您應該能夠順利登入，而不會收到需要輸入密碼的提示)。 

  > [!NOTE]
  > 請確定 Xming 已在執行，然後才開始安裝。

    ```bash
    $ cd /opt/grid
    $ ./runInstaller
    ```

  Oracle Grid Infrastructure 12c Release 1 安裝程式會隨即開啟  (安裝程式可能需要幾分鐘的時間才會啟動)。

2. 在 [選取安裝選項] 頁面上，選取 [為獨立伺服器安裝和設定 Oracle Grid Infrastructure]。

  ![安裝程式之 [選取安裝選項] 頁面的螢幕擷取畫面](./media/asm-configuration/install01.png)

3. 在 [選取產品語言] 頁面上，選取 [英文] 或您想要使用的語言。

  ![安裝程式之 [選取產品語言] 頁面的螢幕擷取畫面](./media/asm-configuration/install02.png)

4. 在 [建立 ASM 磁碟群組] 頁面上︰
  1.  輸入磁碟群組的名稱。
  2.  在 [備援] 底下選取 [外部]。
  3.  在 [配置單位大小] 底下選取 [4]。
  4.  在 [新增磁碟] 底下選取 [ORCLASMSP]。

  ![安裝程式之 [建立 ASM 磁碟群組] 頁面的螢幕擷取畫面](./media/asm-configuration/install03.png)

5. 在 [指定 ASM 密碼] 頁面上選取 [對這些帳戶使用相同密碼] 選項，然後輸入密碼。

  ![安裝程式之 [指定 ASM 密碼] 頁面的螢幕擷取畫面](./media/asm-configuration/install04.png)

6. (選擇性) 在 [指定管理選項] 頁面上，選取 [向 Enterprise Manager (EM) Cloud Control 進行註冊] 方塊。

  ![安裝程式之 [指定管理選項] 頁面的螢幕擷取畫面](./media/asm-configuration/install05.png)

7. 在 [特殊權限作業系統群組] 頁面上使用預設設定。

  ![安裝程式之 [特殊權限作業系統群組] 頁面的螢幕擷取畫面](./media/asm-configuration/install06.png)

8. 在 [指定安裝位置] 頁面上，使用預設設定。

  ![安裝程式之 [指定安裝位置] 頁面的螢幕擷取畫面](./media/asm-configuration/install07.png)

9. 在 [建立清查] 頁面上，輸入或瀏覽至資料夾位置。

  ![安裝程式之 [建立清查] 頁面的螢幕擷取畫面](./media/asm-configuration/install08.png)

10. 在 [Root 指令碼執行組態] 頁面上，選取 [自動執行組態指令碼] 方塊。 接著，選取 [使用 root 使用者認證] 選項，然後輸入 root 使用者密碼。

  ![安裝程式之 Root 指令碼執行組態頁面的螢幕擷取畫面](./media/asm-configuration/install09.png)

11. 在 [執行先決條件檢查] 頁面上，選取 [修正並重新檢查]。

  ![安裝程式之 [執行先決條件檢查] 頁面的螢幕擷取畫面](./media/asm-configuration/install10.png)

12. 在 [修復指令碼] 頁面上，選取 [確定]。

  ![安裝程式之 [修復指令碼] 頁面的螢幕擷取畫面](./media/asm-configuration/install11.png)

13. 在 [摘要] 頁面上，檢閱您選取的設定，然後選取 [安裝]。

  ![安裝程式之 [摘要] 頁面的螢幕擷取畫面](./media/asm-configuration/install12.png)

14. 此時會出現警告對話方塊。 選取 [是] 以繼續進行。

  ![警告對話方塊的螢幕擷取畫面](./media/asm-configuration/install14.png)

15. 在 [完成] 頁面上，選取 [關閉] 來完成安裝。

  ![安裝程式之 [完成] 頁面的螢幕擷取畫面](./media/asm-configuration/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>設定 Oracle ASM 安裝

若要設定 Oracle ASM 的安裝，請完成下列步驟︰

1.  從 X11 工作階段以 grid 的身分登入：

  ```bash
  $ cd /u01/app/grid/product/12.1.0/grid/bin
  $ ./asmca
  ```

  Oracle ASM Configuration Assistant 隨即開啟。

2.  在 [設定 ASM: 磁碟群組] 頁面上，選取 [建立] 按鈕，然後再選取 [顯示進階選項]。

  ![[設定 ASM: 磁碟群組] 頁面的螢幕擷取畫面](./media/asm-configuration/asm01.png)

3.  在 [建立磁碟群組] 頁面上︰

  1.  輸入磁碟群組名稱。
  2.  在 [選取成員磁碟] 底下選取 [ORCL_DATA] 和 [ORCL_DATA1]。
  3.  在 [配置單位大小] 底下選取 [4]。 

  ![[建立磁碟群組] 頁面的螢幕擷取畫面](./media/asm-configuration/asm02.png)

4.  在 [設定 ASM: 磁碟群組] 頁面上，選取 [建立] 按鈕，然後再選取 [顯示進階選項]。

  ![[設定 ASM: 磁碟群組] 頁面的螢幕擷取畫面](./media/asm-configuration/asm03.png)

5.  在 [建立磁碟群組] 頁面上︰

  1.  輸入磁碟群組名稱。
  2.  在 [備援] 底下選取 [外部]。
  3.  在 [選取成員磁碟] 底下選取 [ORCL_FRA]。
  4.  在 [配置單位大小] 底下選取 [4]。

  ![[建立磁碟群組] 頁面的螢幕擷取畫面](./media/asm-configuration/asm04.png)

6.  選取 [結束] 以關閉 ASM Configuration Assistant。

  ![[設定 ASM: 磁碟群組] 頁面與 [結束] 按鈕的螢幕擷取畫面](./media/asm-configuration/asm05.png)

## <a name="create-the-database"></a>建立資料庫

Azure Marketplace 映像上已安裝 Oracle 軟體。 若要安裝資料庫，請完成下列步驟：

1.  將使用者切換為 oracle 超級使用者，然後將接聽程式初始化以啟用記錄功能：

  ```bash
  $ su - oracle
  Password:
  $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
  $ ./dbca
   ```
   Database Configuration Assistant 隨即開啟。

2.  在 [資料庫作業] 頁面上選取 [建立資料庫]。

  ![[資料庫作業] 頁面的螢幕擷取畫面](./media/asm-configuration/createdb01.png)
  
3. 在 [建立模式] 頁面上︰

    1.  輸入資料庫名稱。 
    2.  在 [儲存體類型] 中選取 [自動儲存體管理 (ASM)]。
    3.  在 [資料庫檔案位置] 中，瀏覽至您想要使用的資料夾。
    4.  在 [快速復原區] 中，瀏覽至您想要使用的資料夾。

  ![[建立模式] 頁面的螢幕擷取畫面](./media/asm-configuration/createdb02.png)

4.  在 [摘要] 頁面上，檢閱您選取的設定，然後選取 [完成] 以建立資料庫。

  ![[摘要] 頁面的螢幕擷取畫面](./media/asm-configuration/createdb03.png)

5.   (選擇性) 在 [完成] 頁面上，若要變更密碼，請選取 [密碼管理]。

  ![[完成] 頁面的螢幕擷取畫面](./media/asm-configuration/createdb04.png)


## <a name="delete-the-vm"></a>刪除 VM

若您不再需要此 VM，您可以使用下列命令來移除資源群組、VM 和所有相關資源：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

[教學課程：建立高可用性的虛擬機器](../../linux/create-cli-complete.md)

[瀏覽 VM 部署 Azure CLI 範例](../../linux/cli-samples.md)

