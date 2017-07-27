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
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 39f6acd0def9fa5c2de470268cda6666aa572e5d
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017

---

# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>在 Azure Linux 虛擬機器上設定 Oracle ASM  

請透過本文了解如何在 Azure 中的 Oracle Linux 虛擬機器 (VM) 上安裝和設定 Oracle Automatic Storage Management (Oracle ASM)。

在開始之前，請確定您已安裝 Azure CLI。 如需詳細資訊，請參閱 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="prepare-the-environment"></a>準備環境

### <a name="sign-in-to-azure"></a>登入 Azure 

若要在 Azure CLI 中登入您的 Azure 訂用帳戶，請使用 [az login](/cli/azure/#login) 命令。 然後，遵循螢幕上的指示來進行。

```azurecli
az login
```

### <a name="create-a-resource-group"></a>建立資源群組

若要建立資源群組，請使用 [az group create](/cli/azure/group#create) 命令。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 westus 位置建立名為 myResourceGroup 的資源群組。

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-a-vm"></a>建立 VM

若要建立 VM 來與 Oracle ASM 搭配使用，請完成下列步驟︰

#### <a name="1--to-create-a-virtual-machine-use-the-az-vm-createcliazurevmcreate-command"></a>1.若要建立虛擬機器，請使用 [az vm create](/cli/azure/vm#create) 命令。 

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

#### <a name="2--add-disks-to-use-for-your-oracle-asm-configuration"></a>2.新增要供 Oracle ASM 組態使用的磁碟︰

  ```azurecli
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk4 --new --size-gb 50
  ```

### <a name="connect-to-the-vm"></a>連接至 VM

若要對 VM 建立 SSH 工作階段，請使用下列命令。 以 VM 的 `publicIpAddress` 值取代 IP 位址。

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>安裝 Oracle ASM

若要安裝 Oracle ASM，請完成下列步驟。 

如需關於安裝 Oracle ASM 的詳細資訊，請參閱 [Oracle ASMLib Downloads for Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)。  

### <a name="1-run-yum-list"></a>1.執行 `yum list`：

  ```bash
  $ sudo su -
  # yum list
  ```
  當您第一次執行 `yum list` 時，系統可能需要幾分鐘的時間才能載入此清單。

### <a name="2--run-these-additional-commands"></a>2.執行下列額外命令︰

  ```bash
  # yum list | grep oracleasm
  # yum -y install kmod-oracleasm.x86_64
  # yum -y install oracleasm-support.x86_64
  # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
  ```

### <a name="3--verify-that-oracle-asm-is-installed"></a>3.確認您已安裝 Oracle ASM：

  ```bash
  # rpm -qa |grep oracleasm
  oracleasm-support-2.1.10-4.el6.x86_64
  kmod-oracleasm-2.0.8-15.el6_9.x86_64
  oracleasmlib-2.0.12-1.el6.x86_64
  ```

### <a name="4--add-users-and-groups"></a>4.新增使用者和群組：

  ```bash
  # groupadd -g 54345 asmadmin
  # groupadd -g 54346 asmdba
  # groupadd -g 54347 asmoper
  # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
  # usermod -g oinstall -G dba,asmdba,asmadmin oracle
  ```

### <a name="5--verify-users-and-groups"></a>5.確認使用者和群組：

  ```bash
  # id grid
  uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
  ```

### <a name="6--create-a-folder-and-change-owner"></a>6.建立資料夾，並變更擁有者：

  ```bash
  # mkdir /u01/app/grid
  # chown grid:oinstall /u01/app/grid
  ```

## <a name="set-up-oracle-asm"></a>設定 Oracle ASM

在此教學課程中，預設使用者是「grid」，預設群組是「asmadmin」。 確定「oracle」使用者屬於 asmadmin 群組。 若要設定 Oracle ASM 的安裝，請完成下列步驟︰

### <a name="1--set-the-oracle-asm-library-driver"></a>1.設定 Oracle ASM 程式庫驅動程式︰

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

### <a name="2--view-the-disk-configuration"></a>2.檢視磁碟組態︰
  ```bash
  # cat /proc/partitions
  ```

### <a name="3--format-the-disk"></a>3.將磁碟格式化︰

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

### <a name="4--repeat-the-preceding-step-for-devsdd-devsde-and-devsdf"></a>4.針對 /dev/sdd、/dev/sde 和 /dev/sdf 重複執行上一個步驟。

### <a name="5--check-the-disk-configuration"></a>5.檢查磁碟組態︰

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

### <a name="6--check-the-oracle-asm-service-status"></a>6.檢查 Oracle ASM 服務狀態︰

```bash
# service oracleasm status
Checking if ASM is loaded: no
Checking if /dev/oracleasm is mounted: no
```

### <a name="7--start-the-oracle-asm-service"></a>7.啟動 Oracle ASM 服務︰

```bash
# service oracleasm start
Initializing the Oracle ASMLib driver:                     [  OK  ]
Scanning the system for Oracle ASMLib disks:               [  OK  ]
```

### <a name="8--create-oracle-asm-disks"></a>8.建立 Oracle ASM 磁碟︰

```bash
# service oracleasm createdisk ASMSP /dev/sdc1
Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

# service oracleasm createdisk DATA /dev/sdd1
Marking disk "DATA" as an ASM disk:                        [  OK  ]

# service oracleasm createdisk DATA1 /dev/sde1
Marking disk "DATA1" as an ASM disk:                       [  OK  ]

# service oracleasm createdisk FRA /dev/sdf1
Marking disk "FRA" as an ASM disk:                         [  OK  ]
```

### <a name="9--list-oracle-asm-disks"></a>9.列出 Oracle ASM 磁碟︰

```bash
# service oracleasm listdisks
ASMSP
DATA
DATA1
FRA
```

### <a name="10-change-the-passwords-for-the-root-oracle-and-grid-users-you-use-the-passwords-later-during-installation"></a>10.變更 root、oracle 和 grid 使用者的密碼 (您之後會在安裝期間使用這些密碼)︰

```bash
# passwd oracle
# passwd grid
# passwd root
```

### <a name="11-change-the-folder-permission"></a>11.變更資料夾權限︰

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

### <a name="1--download-oracle-grid-infrastructure-from-the-oracle-asm-download-pagehttpwwworaclecomtechnetworkdatabaseenterprise-editiondownloadsdatabase12c-linux-download-2240591html"></a>1.從 [Oracle ASM 下載頁面](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html)下載 Oracle Grid Infrastructure。 

  在 **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64** 下載標題底下，應該有兩個可供下載的 .zip 檔案。

### <a name="2--after-you-download-the-zip-files-to-your-client-computer-you-can-use-secure-copy-protocol-scp-to-copy-the-files-to-your-vm"></a>2.將 .zip 檔下載到用戶端電腦之後，您可以使用「安全複製通訊協定 (SCP)」將這些檔案複製到 VM。

```bash
scp *.zip <publicIpAddress>:<folder>
```

### <a name="3--move-the-zip-files-to-the-opt-folder-then-change-the-owner-of-the-files"></a>3.將 .zip 檔移到 /opt 資料夾。 接著，變更檔案的擁有者︰

```bash
# mv <folder>/*.zip /opt
# cd /opt
# chown grid:oinstall linuxamd64_12102_grid_1of2.zip
# chown grid:oinstall linuxamd64_12102_grid_2of2.zip
```
### <a name="4--unzip-the-files-install-the-linux-unzip-utility-if-its-not-already-installed"></a>4.將檔案解壓縮 (如果您尚未安裝 Linux 解壓縮公用程式，請加以安裝)︰
```bash
# yum install unzip
# unzip linuxamd64_12102_grid_1of2.zip
# unzip linuxamd64_12102_grid_2of2.zip
```
### <a name="5--change-permission"></a>5.變更權限：
```bash
# chown -R grid:oinstall /opt/grid
```
### <a name="6--turn-off-the-firewall"></a>6.關閉防火牆︰
```bash
# service iptables status
# service iptables stop
```

### <a name="7--check-available-swap-space-you-need-at-lease-68-gb-of-swap-space-to-install-grid-by-default-linux-azure-vms-will-not-have-swap-enabled-and-configured-by-default"></a>7.檢查可用的交換空間。 您需要至少 6.8 GB 的交換空間才能安裝 Grid，Linux Azure VM 預設不會啟用交換，並且以預設值設定。

強烈建議您使用 waagent 來設定交換空間，讓它一律在戰時磁碟 (暫存磁碟) 內建立，如需步驟的詳細資訊，請參閱下列連結： 

* [如何在 Linux Azure 虛擬機器中新增分頁檔](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>讓用戶端和 VM 準備好執行 x11 (僅適用於 Windows 用戶端)
這是選擇性步驟，如果您使用 Linux 用戶端或是已設定 x11，可以跳過此步驟。

### <a name="1--download-putty-and-xming-to-your-windows-computer"></a>1.將 PuTTY 和 Xming 下載到 Windows 電腦︰

  * [下載 PuTTY](http://www.putty.org/)
  * [下載 Xming](https://xming.en.softonic.com/)

### <a name="2--after-you-install-putty-in-the-putty-folder-for-example-cprogram-filesputty-run-puttygenexe-putty-key-generator"></a>2.在 PuTTY 安裝好之後，請在 PuTTY 資料夾 (例如 C:\Program Files\PuTTY) 執行 puttygen.exe (PuTTY 金鑰產生器)。

### <a name="3--in-putty-key-generator"></a>3.在 PuTTY 金鑰產生器中︰

- 若要產生金鑰，請選取 [產生] 按鈕。
- 複製金鑰的內容 (Ctrl+C)。
- 選取 [儲存私密金鑰] 按鈕。
- 略過隨之出現的警告，然後選取 [確定]。

  ![PuTTY 金鑰產生器頁面的螢幕擷取畫面](./media/oracle-asm/puttykeygen.png)

### <a name="4--in-your-vm-run-these-commands"></a>4.在您的 VM 中，執行下列命令︰

```bash
# sudo su - grid
$ mkdir .ssh (if not already created)
$ cd .ssh
```

### <a name="5--create-a-file-named-authorizedkeys-paste-the-contents-of-the-key-in-this-file-and-then-save-the-file"></a>5.建立名為 authorized_keys 的檔案。 在此檔案中貼上金鑰的內容，然後儲存檔案。

> [!NOTE]
> 金鑰中必須包含字串 `ssh-rsa`。 此外，金鑰的內容必須是單行文字。
>  

### <a name="6--start-putty-in-the-category-pane-go-to-connection--ssh--auth-in-the-private-key-file-for-authentication-box-browse-to-the-key-that-you-generated-earlier"></a>6.啟動 PuTTY。 在 [類別] 窗格中，移至 [連線] > [SSH] > [驗證]。 在 [用於驗證的私密金鑰檔] 方塊中，瀏覽至您稍早產生的金鑰。

  ![[設定私密金鑰] 頁面上的螢幕擷取畫面](./media/oracle-asm/setprivatekey.png)

### <a name="7--in-the-category-pane-go-to-connection--ssh--x11-select-the-enable-x11-forwarding-box"></a>7.在 [類別] 窗格中，移至 [連線] > [SSH] > [X11]。 選取 [啟用 X11 轉送] 方塊。

  ![[啟用 X11] 頁面的螢幕擷取畫面](./media/oracle-asm/enablex11.png)

### <a name="8-in-the-category-pane-go-to-session-enter-the-host-information-and-then-select-open"></a>8.在 [類別] 窗格中，移至 [工作階段]。 輸入主機資訊，然後選取 [開啟]。

  ![[工作階段] 頁面的螢幕擷取畫面](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>安裝 Oracle Grid Infrastructure

若要安裝 Oracle Grid Infrastructure，請完成下列步驟：

### <a name="1-sign-in-as-grid-you-should-be-able-to-sign-in-without-being-prompted-for-a-password"></a>1.以 grid 的身分登入  (您應該能夠順利登入，而不會收到需要輸入密碼的提示)。 

> [!NOTE]
> 請確定 Xming 已在執行，然後才開始安裝。

```bash
$ cd /opt/grid
$ ./runInstaller
```

  Oracle Grid Infrastructure 12c Release 1 安裝程式會隨即開啟  (安裝程式可能需要幾分鐘的時間才會啟動)。

### <a name="2-on-the-select-installation-option-page-select-install-and-configure-oracle-grid-infrastructure-for-a-standalone-server"></a>2.在 [選取安裝選項] 頁面上，選取 [為獨立伺服器安裝和設定 Oracle Grid Infrastructure]。

  ![安裝程式之 [選取安裝選項] 頁面的螢幕擷取畫面](./media/oracle-asm/install01.png)

### <a name="3-on-the-select-product-languages-page-select-english-or-the-language-that-you-want"></a>3.在 [選取產品語言] 頁面上，選取 [英文] 或您想要使用的語言。

  ![安裝程式之 [選取產品語言] 頁面的螢幕擷取畫面](./media/oracle-asm/install02.png)

### <a name="4-on-the-create-asm-disk-group-page"></a>4.在 [建立 ASM 磁碟群組] 頁面上︰
- 輸入磁碟群組的名稱。
- 在 [備援] 底下選取 [外部]。
- 在 [配置單位大小] 底下選取 [4]。
- 在 [新增磁碟] 底下選取 [ORCLASMSP]。

  ![安裝程式之 [建立 ASM 磁碟群組] 頁面的螢幕擷取畫面](./media/oracle-asm/install03.png)

### <a name="5-on-the-specify-asm-password-page-select-the-use-same-passwords-for-these-accounts-option-and-enter-a-password"></a>5.在 [指定 ASM 密碼] 頁面上選取 [對這些帳戶使用相同密碼] 選項，然後輸入密碼。

  ![安裝程式之 [指定 ASM 密碼] 頁面的螢幕擷取畫面](./media/oracle-asm/install04.png)

### <a name="6-optional-on-the-specify-management-options-page-select-the-register-with-enterprise-manager-em-cloud-control-box"></a>6.(選擇性) 在 [指定管理選項] 頁面上，選取 [向 Enterprise Manager (EM) Cloud Control 進行註冊] 方塊。

  ![安裝程式之 [指定管理選項] 頁面的螢幕擷取畫面](./media/oracle-asm/install05.png)

### <a name="7-on-the-privileged-operating-system-groups-page-use-the-default-settings"></a>7.在 [特殊權限作業系統群組] 頁面上使用預設設定。

  ![安裝程式之 [特殊權限作業系統群組] 頁面的螢幕擷取畫面](./media/oracle-asm/install06.png)

### <a name="8-on-the-specify-installation-location-page-use-default-settings"></a>8.在 [指定安裝位置] 分頁上，使用預設設定。

  ![安裝程式之 [指定安裝位置] 頁面的螢幕擷取畫面](./media/oracle-asm/install07.png)

### <a name="9-on-the-create-inventory-page-enter-or-browse-to-the-folder-location"></a>9.在 [建立清查] 頁面上，輸入或瀏覽至資料夾位置。

  ![安裝程式之 [建立清查] 頁面的螢幕擷取畫面](./media/oracle-asm/install08.png)

### <a name="10-on-the-root-script-execution-configuration-page-select-the-automatically-run-configuration-scripts-box-then-select-the-use-root-user-credential-option-and-enter-the-root-user-password"></a>10.在 [Root 指令碼執行組態] 頁面上，選取 [自動執行組態指令碼] 方塊。 接著，選取 [使用 root 使用者認證] 選項，然後輸入 root 使用者密碼。

  ![安裝程式之 Root 指令碼執行組態頁面的螢幕擷取畫面](./media/oracle-asm/install09.png)

### <a name="11-on-the-perform-prerequisite-checks-page-select-fix--check-again"></a>11.在 [執行先決條件檢查] 頁面上，選取 [修正並重新檢查]。

  ![安裝程式之 [執行先決條件檢查] 頁面的螢幕擷取畫面](./media/oracle-asm/install10.png)

### <a name="12-on-the-fixup-script-page-select-ok"></a>12.在 [修復指令碼] 頁面上，選取 [確定]。

  ![安裝程式之 [修復指令碼] 頁面的螢幕擷取畫面](./media/oracle-asm/install11.png)

### <a name="13-on-the-summary-page-review-your-settings-selections-and-then-select-install"></a>13.在 [摘要] 頁面上，檢閱您選取的設定，然後選取 [安裝]。

  ![安裝程式之 [摘要] 頁面的螢幕擷取畫面](./media/oracle-asm/install12.png)

### <a name="14-a-warning-dialog-box-appears-select-yes-to-continue"></a>14.此時會出現警告對話方塊。 選取 [是] 以繼續進行。

  ![警告對話方塊的螢幕擷取畫面](./media/oracle-asm/install14.png)

### <a name="15-on-the-finish-page-select-close-to-finish-the-installation"></a>15.在 [完成] 頁面上，選取 [關閉] 來完成安裝。

  ![安裝程式之 [完成] 頁面的螢幕擷取畫面](./media/oracle-asm/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>設定 Oracle ASM 安裝

若要設定 Oracle ASM 的安裝，請完成下列步驟︰

### <a name="1--sign-in-as-grid-from-your-x11-session"></a>1.從 X11 工作階段以 grid 的身分登入：

  ```bash
  $ cd /u01/app/grid/product/12.1.0/grid/bin
  $ ./asmca
  ```

  Oracle ASM Configuration Assistant 隨即開啟。

### <a name="2--on-the-configure-asm-disk-groups-page-select-the-create-button-and-then-select-show-advanced-options"></a>2.在 [設定 ASM: 磁碟群組] 頁面上，選取 [建立] 按鈕，然後再選取 [顯示進階選項]。

  ![[設定 ASM: 磁碟群組] 頁面的螢幕擷取畫面](./media/oracle-asm/asm01.png)

### <a name="3--on-the-create-disk-group-page"></a>3.在 [建立磁碟群組] 頁面上︰

- 輸入磁碟群組名稱。
- 在 [選取成員磁碟] 底下選取 [ORCL_DATA] 和 [ORCL_DATA1]。
- 在 [配置單位大小] 底下選取 [4]。

  ![[建立磁碟群組] 頁面的螢幕擷取畫面](./media/oracle-asm/asm02.png)

### <a name="4--on-the-configure-asm-disk-groups-page-select-the-create-button-and-then-select-show-advanced-options"></a>4.在 [設定 ASM: 磁碟群組] 頁面上，選取 [建立] 按鈕，然後再選取 [顯示進階選項]。

  ![[設定 ASM: 磁碟群組] 頁面的螢幕擷取畫面](./media/oracle-asm/asm03.png)

### <a name="5--on-the-create-disk-group-page"></a>5.在 [建立磁碟群組] 頁面上︰

- 輸入磁碟群組名稱。
- 在 [備援] 底下選取 [外部]。
- 在 [選取成員磁碟] 底下選取 [ORCL_FRA]。
- 在 [配置單位大小] 底下選取 [4]。

  ![[建立磁碟群組] 頁面的螢幕擷取畫面](./media/oracle-asm/asm04.png)

### <a name="6--select-exit-to-close-asm-configuration-assistant"></a>6.選取 [結束] 以關閉 ASM Configuration Assistant。

  ![[設定 ASM: 磁碟群組] 頁面與 [結束] 按鈕的螢幕擷取畫面](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>建立資料庫

Azure Marketplace 映像上已安裝 Oracle 軟體。 若要安裝資料庫，請完成下列步驟：

### <a name="1--switch-users-to-the-oracle-superuser-and-then-initialize-the-listener-for-logging"></a>1.將使用者切換為 oracle 超級使用者，然後將接聽程式初始化以啟用記錄功能：

  ```bash
  $ su - oracle
  Password:
  $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
  $ ./dbca
   ```
   Database Configuration Assistant 隨即開啟。

### <a name="2--on-the-database-operation-page-select-create-database"></a>2.在 [資料庫作業] 頁面上選取 [建立資料庫]。

  ![[資料庫作業] 頁面的螢幕擷取畫面](./media/oracle-asm/createdb01.png)
  
### <a name="3-on-the-creation-mode-page"></a>3.在 [建立模式] 頁面上︰

- 輸入資料庫名稱。
- 在 [儲存體類型] 中選取 [自動儲存體管理 (ASM)]。
- 在 [資料庫檔案位置] 中，瀏覽至您想要使用的資料夾。
- 在 [快速復原區] 中，瀏覽至您想要使用的資料夾。

  ![[建立模式] 頁面的螢幕擷取畫面](./media/oracle-asm/createdb02.png)

### <a name="4--on-the-summary-page-review-your-settings-selections-and-then-select-finish-to-create-the-database"></a>4.在 [摘要] 頁面上，檢閱您選取的設定，然後選取 [完成] 以建立資料庫。

  ![[摘要] 頁面的螢幕擷取畫面](./media/oracle-asm/createdb03.png)

### <a name="5---optional-on-the-finish-page-to-change-the-passwords-select-password-management"></a>5. (選擇性) 在 [完成] 頁面上，若要變更密碼，請選取 [密碼管理]。

  ![[完成] 頁面的螢幕擷取畫面](./media/oracle-asm/createdb04.png)


## <a name="delete-the-vm"></a>刪除 VM

若您不再需要此 VM，您可以使用下列命令來移除資源群組、VM 和所有相關資源：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

[教學課程：建立高可用性的虛擬機器](../../linux/create-cli-complete.md)

[瀏覽 VM 部署 Azure CLI 範例](../../linux/cli-samples.md)

