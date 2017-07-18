---
title: "在 Azure Linux 虛擬機器上備份及復原 Oracle Database 12c 資料庫 | Microsoft Docs"
description: "了解如何在您的 Azure 環境中備份及復原 Oracle Database 12c 資料庫。"
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
ms.date: 5/17/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7a53ca814170a6651fc499c41395fe088a6f869a
ms.contentlocale: zh-tw
ms.lasthandoff: 06/15/2017

---

# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>在 Azure Linux 虛擬機器上備份及復原 Oracle Database 12c 資料庫

您可以使用 Azure CLI 在命令提示字元或使用指令碼建立和管理 Azure 資源。 在本文中，我們會使用 Azure CLI 指令碼，從 Azure Marketplace 資源庫映像部署 Oracle Database 12c 資料庫。

在開始之前，請確定您已安裝 Azure CLI。 如需詳細資訊，請參閱 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="prepare-the-environment"></a>準備環境
### <a name="step-1-assumptions"></a>步驟 1：假設

*   若要執行備份和復原程序，您必須使用 Oracle Database 12c 的已安裝執行個體來建立 Linux VM。 您用來建立 VM 的 Marketplace 映像名為 Oracle:Oracle-Database-Ee:12.1.0.2:latest。

    如需有關如何建立 Oracle 資料庫的指示，請參閱 [Oracle 資料庫快速建立指南](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create)。


### <a name="step-2-connect-to-the-vm"></a>步驟 2：連線至 VM

*   若要使用 VM 建立安全殼層 (SSH) 工作階段，請使用下列命令：將 IP 位址和主機名稱的組合取代為 VM 的 `publicIpAddress` 值。

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>步驟 3︰準備資料庫

1.  這個步驟假設您具有在虛擬機器上執行的 Oracle 執行個體 (cdb1)，稱為 myVM。

    執行 oracle 超級使用者根目錄，然後將接聽程式初始化：

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  請確定資料庫處於封存記錄模式 (選擇性步驟)：

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  建立資料表來測試認可 (選擇性步驟)：

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  確認或變更備份檔案位置和大小：

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. 使用 Oracle 復原管理員 (RMAN) 來備份資料庫：

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>步驟 4：Linux VM 的應用程式一致備份

這是 Azure 備份中的一項新功能，可讓使用者指定 VM 快照集前後要執行的指令碼 (前置和後置)。

#### <a name="1-download-vmsnapshotscriptpluginconfigjson-from-httpsgithubcommicrosoftazurebackupvmsnapshotpluginconfig-content-should-be-similar-to-following"></a>1.請從 https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig 下載 VMSnapshotScriptPluginConfig.json。 內容應該類似下列範例。

```azurecli
{
    "pluginName" : "ScriptRunner",
    "preScriptLocation" : "",
    "postScriptLocation" : "",
    "preScriptParams" : ["", ""],
    "postScriptParams" : ["", ""],
    "preScriptNoOfRetries" : 0,
    "postScriptNoOfRetries" : 0,
    "timeoutInSeconds" : 30,
    "continueBackupOnFailure" : true,
    "fsFreezeEnabled" : true
}
```

#### <a name="2-create-etcazure-folder-on-vm"></a>2.在 VM 上建立 /etc/azure 資料夾

```bash
$ sudo su -
# mkdir /etc/azure
# cd /etc/azure
```

#### <a name="3-copy-vmsnapshotscriptpluginconfigjson-file-to-folder-etcazure"></a>3.將 VMSnapshotScriptPluginConfig.json 檔案複製到資料夾 /etc/azure。

#### <a name="4-edit-the-vmsnapshotscriptpluginconfigjson-file-to-included-the-prescriptlocation-and-postscriptlocation-parameters-for-example"></a>4.編輯 VMSnapshotScriptPluginConfig.json 檔案以包含 PreScriptLocation 和 PostScriptlocation 參數。 例如：
```azurecli
{
    "pluginName" : "ScriptRunner",
    "preScriptLocation" : "/etc/azure/pre_script.sh",
    "postScriptLocation" : "/etc/azure/post_script.sh",
    "preScriptParams" : ["", ""],
    "postScriptParams" : ["", ""],
    "preScriptNoOfRetries" : 0,
    "postScriptNoOfRetries" : 0,
    "timeoutInSeconds" : 30,
    "continueBackupOnFailure" : true,
    "fsFreezeEnabled" : true
}
```
#### <a name="5-create-the-pre-and-post-script-files"></a>5.建立前置和後置指令碼檔案

以下是冷備份前置和後置指令碼 (關機及重新啟動) 的範例

如 /etc/azure/pre_script.sh

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
```

如 /etc/azure/post_script.sh

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
```

以下是熱備份前置和後置指令碼的範例

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
```

如 /etc/azure/post_script.sh

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
```

如 /etc/azure/pre_script.sql，您必須依需求修改檔案的內容

```bash
alter tablespace SYSTEM begin backup;
...
...
alter system switch logfile;
alter system archive log stop;
```

如 /etc/azure/post_script.sql，您必須依需求修改檔案的內容

```bash
alter tablespace SYSTEM end backup;
...
...
alter system archive log start;
```

#### <a name="6-change-file-permissions"></a>6.變更檔案權限

```bash
# chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
# chmod 700 /etc/azure/pre_script.sh
# chmod 700 /etc/azure/post_script.sh
```

#### <a name="7-test-the-scripts-sign-in-as-root-make-sure-no-errors"></a>7.測試指令碼 (以根目錄身分登入)，請確定沒有錯誤

```bash
# /etc/azure/pre_script.sh
# /etc/azure/post_script.sh
```

如需詳細資訊，請參閱[適用於 Linux VM 的應用程式一致備份](https://azure.microsoft.com/en-us/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/)。


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>步驟 5：使用 Azure 復原服務保存庫來備份 VM

1.  登入 Azure 入口網站，然後搜尋復原服務保存庫執行個體。
![復原服務保存庫頁面](./media/oracle-backup-recovery/recovery_service_01.png)

2.  按一下 [新增] 按鈕即可新增保存庫。
![復原服務保存庫新增頁面](./media/oracle-backup-recovery/recovery_service_02.png)

3.  若要繼續，請按一下 [myVault]。 [詳細資料] 頁面隨即出現。
![復原服務保存庫詳細資料頁面](./media/oracle-backup-recovery/recovery_service_03.png)

4.  按一下 [備份] 按鈕。 接著，您要新增備份目標、原則和備份項目。
![復原服務保存庫備份頁面](./media/oracle-backup-recovery/recovery_service_04.png)

5.  如為**備份目標**，請使用預設 **Azure** 和**虛擬機器**值。 若要繼續，請按一下 [確定]。
![復原服務保存庫詳細資料頁面](./media/oracle-backup-recovery/recovery_service_05.png)

6.  如為**備份原則**，請使用 **DefaultPolicy** 或**建立新的原則**。 若要繼續，請按一下 [確定]。
![復原服務保存庫備份原則詳細資料頁面](./media/oracle-backup-recovery/recovery_service_06.png)

7.  選取 **myVM1** 核取方塊，按一下 [確定]，然後按一下 [啟用備份]。
![要備份詳細資料頁面的復原服務保存庫項目](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > 按一下 [啟用備份] 之後，備份程序要到排定的時間過期後才會啟動。 若要設定即時備份，請完成下一個步驟。

8.  按一下 [備份項目]，然後在 [備份項目計數] 下，按一下備份項目計數。

    ![復原服務保存庫 myVault 詳細資料頁面](./media/oracle-backup-recovery/recovery_service_08.png)

9.  在頁面的右側，按一下省略符號 (**...**) 按鈕，然後再按一下 [立即備份]。

    ![復原服務保存庫立即備份命令](./media/oracle-backup-recovery/recovery_service_09.png)

10. 按一下 [備份] 按鈕，等候備份程序完成，然後移至「步驟 5：將資料庫檔案移除。」
若要檢視此備份作業的狀態，請按一下 [作業]。
![復原服務保存庫作業頁面](./media/oracle-backup-recovery/recovery_service_10.png)

    備份作業的狀態會顯示在下一個映像中。

    ![包含狀態的復原服務保存庫作業頁面](./media/oracle-backup-recovery/recovery_service_11.png)

11. 適用於應用程式一致備份。 可在 /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0 記錄檔中解決任何錯誤。

### <a name="step-6-remove-the-database-files"></a>步驟 6：將資料庫檔案移除 
本文稍後您會了解如何測試復原程序。 您在測試復原程序之前，必須先將資料庫檔案移除。

1.  將資料表空間和備份檔案移除：

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  關閉 Oracle 執行個體 (選擇性步驟)：

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-recovery-services-vaults"></a>從復原服務保存庫還原刪除的檔案
若要還原已刪除的檔案，請完成下列程序：

1. 登入 Azure 入口網站，然後搜尋 myVault 復原服務保存庫項目。 在右上角的**備份項目**下，按一下項目數目。
![復原服務保存庫 myVault 備份項目](./media/oracle-backup-recovery/recovery_service_12.png)

2. 在**備份項目計數**下，按一下項目數目。
![復原服務保存庫 Azure 虛擬機器備份項目計數](./media/oracle-backup-recovery/recovery_service_13.png)

3. 按一下 [檔案復原 (預覽)]。
![復原服務保存庫檔案復原頁面的螢幕擷取畫面](./media/oracle-backup-recovery/recovery_service_14.png)

4. 按一下 [下載指令碼]，然後在用戶端電腦上，將下載檔案 (.sh) 儲存到資料夾。
![下載指令碼檔案會儲存選項](./media/oracle-backup-recovery/recovery_service_15.png)

5. 將 .sh 檔案複製到 VM。

    下一個範例會示範如何使用安全複製 (scp) 命令將檔案移至 VM。 您也可以將內容複製到剪貼簿，然後再將內容貼到在 VM 上設定的新檔案。

    > [!IMPORTANT]
    > 在下一個範例中，請務必更新 IP 位址和資料夾的值。 值必須對應至儲存檔案的資料夾。

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. 變更檔案，讓它屬於根目錄。

    在下一個範例中，您會變更檔案，讓它屬於根目錄，然後您要變更權限。

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    下一個範例會示範您在執行上述指令碼之後應該看到的內容。 當系統提示您繼續時，請輸入 **Y**。

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. 確認存取已掛接的磁碟區。

    若要結束，請輸入 **q**，然後搜尋掛接的磁碟區。 若要建立已新增磁碟區的清單，請在命令提示字元中輸入 **df -k**。

    ![df -k 命令](./media/oracle-backup-recovery/recovery_service_16.png)

8. 使用下列指令碼，將遺失的檔案複製回到資料夾。

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. 在下列指令碼中，使用 RMAN 來復原資料庫：

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. 將磁碟取消掛接。

    在 Azure 入口網站中，按一下 [取消掛接磁碟]。

    ![取消掛接磁碟命令](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>還原整個 VM

您並非從復原服務保存庫將刪除的檔案還原，而是可以還原整個 VM。

### <a name="step-1-drop-the-myvm"></a>步驟 1：卸除 myVM

*   登入 Azure 入口網站、移至 [myVM] 保存庫，然後選取 [刪除]。

    ![刪除命令](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>步驟 2：將 VM 復原

1.  移至 [復原服務保存庫]，然後選取 [myVault]。
![出現在 UI 中的 myVault 項目](./media/oracle-backup-recovery/recover_vm_02.png)

2.  在右上角的**備份項目**下，按一下項目數目。
![myVault 備份項目](./media/oracle-backup-recovery/recover_vm_03.png)

3.  在**備份項目計數**下，按一下項目數目。
![復原 VM 頁面](./media/oracle-backup-recovery/recover_vm_04.png)

4.  在頁面的右側，按一下省略符號 (**...**) 按鈕，然後再按一下 [還原 VM]。
![還原 VM 命令](./media/oracle-backup-recovery/recover_vm_05.png)

5.  選取您想要還原的項目，然後按一下 [確定]。
![選取還原點](./media/oracle-backup-recovery/recover_vm_06.png) 如果您已啟用應用程式一致性備份，應該會看到「藍色」直條。

6.  選取 [虛擬機器名稱]、選取 [資源群組]，然後按一下 [確定]。
![還原設定值](./media/oracle-backup-recovery/recover_vm_07.png)

7.  若要還原 VM，請按一下 UI 左下角 (請參閱先前的映像) 的 [還原]。

8.  若要檢視還原程序的狀態，請按一下 [作業]。
![備份作業狀態命令](./media/oracle-backup-recovery/recover_vm_08.png)

    下一個映像會顯示還原程序的狀態。

    ![還原程序的狀態](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>步驟 3：設定公用 IP 位址
還原 VM 之後，您要設定公用 IP 位址。

1.  使用搜尋方塊來尋找**公用 IP 位址**。

    ![公用 IP 位址的清單](./media/oracle-backup-recovery/create_ip_00.png)

2.  按一下左上角的 [新增]。 如為**名稱**，請選取公用 IP 名稱，如為**資源群組**，請選取 [使用現有]。 按一下 [建立] 。

    ![建立 IP 位址](./media/oracle-backup-recovery/create_ip_01.png)

3.  若要將公用 IP 位址與 VM 的 NIC 介面產生關聯，請搜尋並選取 [myVMip]，然後按一下 [關聯]。

    ![將 IP 位址產生關聯](./media/oracle-backup-recovery/create_ip_02.png)

4.  如為**資源類型**，選取**網路介面**、選取 myVM 執行個體所使用的 NIC，然後按一下 [確定]。

    ![選取資源類型和 NIC 值](./media/oracle-backup-recovery/create_ip_03.png)

5.  搜尋並開啟從入口網站移植的 myVM 執行個體。 與 VM 相關聯的 IP 位址會顯示在右上角。

    ![IP 位址值](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>步驟 4：連線至 VM

*   使用下列指令碼連線︰

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>步驟 5：測試是否可存取資料庫
*   您可以使用下列指令碼來測試協助工具：

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

> [!IMPORTANT]
> 如果資料庫 **startup** 命令產生錯誤，若要復原資料庫，請參閱「步驟 6：使用 RMAN 來復原資料庫。」

### <a name="step-6-use-rman-to-recover-the-database-optional-step"></a>步驟 6：使用 RMAN 復原資料庫 (選擇性步驟)
*   使用下列指令碼來復原資料庫：

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

現在已完成備份及復原 Azure Linux 虛擬機器上的 Oracle 12c 資料庫。
## <a name="delete-the-vm"></a>刪除 VM

若您不再需要此 VM，可以使用下列命令將資源群組、VM 和所有相關資源移除：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

[教學課程︰建立高可用性 VM](../../linux/create-cli-complete.md)

[瀏覽 VM 部署 Azure CLI 範例](../../linux/cli-samples.md)

