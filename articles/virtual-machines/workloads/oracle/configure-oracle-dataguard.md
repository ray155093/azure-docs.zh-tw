---
title: "在 Azure Linux VM 上實作 Oracle Data Guard | Microsoft Docs"
description: "快速在您的 Azure 環境中啟動並執行 Oracle Data Guard。"
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
ms.date: 05/10/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: e87485fc2e5adce1ed8d85dc424ef95102c849c3
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017

---

# <a name="implement-oracle-data-guard-on-azure-linux-vm"></a>在 Azure Linux VM 上實作 Oracle Data Guard 

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本指南詳述使用 Azure CLI 從 Marketplace 資源庫映像部署 Oracle 12c Database。 在 Oracle 資料庫建立好之後，本文件會逐步說明如何在 Azure VM 上安裝和設定 Data Guard。

開始之前，請確定已安裝 Azure CLI。 如需詳細資訊，請參閱 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="prepare-the-environment"></a>準備環境
### <a name="assumptions"></a>假設

若要執行 Oracle Data Guard 的安裝，您需要在相同的可用性設定組建立兩個 Azure VM。 您用來建立 VM 的 Marketplace 映像是「Oracle:Oracle-Database-Ee:12.1.0.2:latest」。

主要 VM (myVM1) 具有已執行的 Oracle 執行個體。

待命 VM (myVM2) 只安裝了 Oracle 軟體。

### <a name="log-in-to-azure"></a>登入 Azure 

使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```azurecli
az login
```

### <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 `westus` 位置建立名為 `myResourceGroup` 的資源群組。

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-availability-set"></a>建立可用性設定組

此步驟為選用步驟，但建議執行。 如需詳細資訊，請參閱 [Azure 可用性設定組指南](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)。

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-virtual-machine"></a>Create virtual machine

使用 [az vm create](/cli/azure/vm#create) 命令來建立 VM。 

下列範例會建立 2 個 VM，分別名為 `myVM1` 和 `myVM2`。 如果預設的金鑰位置還沒有 SSH 金鑰，請建立這些金鑰。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。

建立 myVM1 (主要)
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

在 VM 建立好之後，Azure CLI 會顯示類似下列範例的資訊：請記下 `publicIpAddress`。 此位址用來存取 VM。

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

建立 myVM2 (待命)
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

此 VM 建立好之後，也請記下 `publicIpAddress`。

### <a name="open-the-tcp-port-for-connectivity"></a>開啟用於連線的 TCP 通訊埠

此步驟是為了設定外部端點，以便讓 VM 能夠遠端存取 Oracle DB，因此請執行下列命令。

開啟 myVM1 的連接埠

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVmN1SG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

結果看起來應該會像下面的回應這樣：

```bash
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

開啟 myVM2 的連接埠

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2N1SG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-virtual-machine"></a>連線至虛擬機器

使用下列命令，建立與虛擬機器的 SSH 工作階段。 以虛擬機器的 `publicIpAddress` 取代 IP 位址。

```bash 
ssh azureuser@<publicIpAddress>
```

### <a name="create-database-on-myvm1-primary"></a>在 myVM1 (主要) 上建立資料庫

Oracle 軟體已安裝於 Marketplace 映像上，因此下一個步驟是安裝資料庫。 第一個步驟會以「oracle」超級使用者的身分來執行。

```bash
sudo su - oracle
```

建立資料庫：

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
輸出結果看起來應該會像下面的回應這樣：

```bash
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

設定 ORACLE_SID 和 ORACLE_HOME 變數

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

您可以視需要將 ORACLE_HOME 和 ORACLE_SID 新增到 .bashrc 檔案中，如此可將這些設定儲存，以供日後登入使用。

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="data-guard-configurations"></a>Data Guard 組態

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>在 myVM1 (主要) 上啟用封存記錄模式

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
```
啟用強制記錄功能，並確定至少有一個記錄檔存在。

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

建立待命重做記錄

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 50M;
```

開啟 Flashback (可復原到很早以前的狀態) 並將 STANDBY_FILE_MANAGEMENT 設定為 auto

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
```

### <a name="service-setup-on-myvm1-primary"></a>myVM1 (主要) 上的服務設定

編輯或建立 tnsnames.ora 檔案，其位於 $ORACLE_HOME\network\admin 資料夾

新增下列項目

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

編輯或建立 listener.ora 檔案，其位於 $ORACLE_HOME\network\admin 資料夾

新增下列項目

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

啟動接聽程式

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="service-setup-on-myvm2-standby"></a>myVM2 (待命) 上的服務設定

編輯或建立 tnsnames.ora 檔案，其位於 $ORACLE_HOME\network\admin 資料夾

新增下列項目

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

編輯或建立 listener.ora 檔案，其位於 $ORACLE_HOME\network\admin 資料夾

新增下列項目

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

啟動接聽程式

```bash
$ lsnrctl stop
$ lsnrctl start
```

啟用 Data Guard Broker
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="restore-database-to-myvm2-standby"></a>將資料庫還原到 myVM2 (待命)

以下列內容建立參數檔案「/tmp/initcdb1_stby.ora」
```bash
*.db_name='cdb1'
```

建立資料夾

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

建立密碼檔案

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0.2/db_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```
在 myVM2 上啟動資料庫

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

使用 RMAN 公用程式來還原資料庫

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

在 RMAN 中執行下列命令
```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

啟用 Data Guard Broker
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>在 myVM1 (主要) 上設定 Data Guard Broker

啟動 Data Guard Manager，並使用 SYS 和密碼進行登入 (請勿使用作業系統驗證)。 執行下列操作

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> CREATE CONFIGURATION my_dg_config AS PRIMARY DATABASE IS cdb1 CONNECT IDENTIFIER IS cdb1;
Configuration "my_dg_config" created with primary database "cdb1"
DGMGRL> ADD DATABASE cdb1_stby AS CONNECT IDENTIFIER IS cdb1_stby MAINTAINED AS PHYSICAL;
Database "cdb1_stby" added
DGMGRL> ENABLE CONFIGURATION;
Enabled.
```

檢閱組態
```bash
DGMGRL> SHOW CONFIGURATION;

Configuration - my_dg_config

  Protection Mode: MaxPerformance
  Members:
  cdb1      - Primary database
    cdb1_stby - Physical standby database

Fast-Start Failover: DISABLED

Configuration Status:
SUCCESS   (status updated 26 seconds ago)
```

這樣便已完成 Oracle Data Guard 設定。 下一節會說明如何測試連線並進行切換

### <a name="connect-database-from-client-machine"></a>從用戶端機器連線到資料庫

在用戶端機器上更新或建立 tnsnames.ora 檔案，此檔案通常位於 $ORACLE_HOME\network\admin。

使用 myVM1 和 myVM2 的 `publicIpAddress` 來取代 IP

```bash
cdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM1 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1)
    )
  )

cdb1_stby=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM2 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1_stby)
    )
  )
```

啟動 sqlplus

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```
## <a name="test-data-guard-configuration"></a>測試 Data Guard 組態

### <a name="database-switchover-on-myvm1-primary"></a>myVM1 (主要) 上的資料庫轉換

從主要切換至待命 (cdb1 至 cdb1_stby)

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1_stby;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1_stby"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1_stby" is opening...
Operation requires start up of instance "cdb1" on database "cdb1"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1_stby"
DGMGRL>
```

您現在應該能夠連線到待命資料庫了

啟動 sqlplus

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="database-switch-back-on-myvm2-standby"></a>myVM2 (待命) 上的資料庫反向切換

若要反向切換，請在 myVM2 上執行下列操作
```bash
$ dgmgrl sys/OraPasswd1@cdb1_stby
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1" is opening...
Operation requires start up of instance "cdb1" on database "cdb1_stby"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1"
```

同樣地，您現在應該能夠連線到主要資料庫了

啟動 sqlplus

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

這樣便已完成 Oracle Linux 上的 Data Guard 安裝和設定。


## <a name="delete-virtual-machine"></a>刪除虛擬機器

若不再需要，您可以使用下列命令來移除資源群組、VM 和所有相關資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

[建立高可用性的虛擬機器教學課程](../../linux/create-cli-complete.md)

[瀏覽 VM 部署 CLI 範例](../../linux/cli-samples.md)

