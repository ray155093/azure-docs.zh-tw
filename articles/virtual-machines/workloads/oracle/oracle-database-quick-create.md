---
title: "在 Azure VM 上建立 Oracle 12c Database | Microsoft Docs"
description: "快速在您的 Azure 環境中啟動並執行 Oracle 12c 資料庫。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tonyguid
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/17/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 3cf6625407afe5b4fb53a945f4a505338122aaec
ms.lasthandoff: 04/13/2017

---

# <a name="create-an-oracle-12c-database-on-azure-vm"></a>在 Azure VM 上建立 Oracle 12c Database

此指令碼用 Azure CLI 建立 Oracle 12c Database。

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本指南詳述使用 Azure CLI 從 Marketplace 資源庫映像部署 Oracle 12c Database。

開始之前，請確定已安裝 Azure CLI。 如需詳細資訊，請參閱 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

## <a name="log-in-to-azure"></a>登入 Azure 

使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 `westus` 位置建立名為 `myResourceGroup` 的資源群組。

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-virtual-machine"></a>Create virtual machine

使用 [az vm create](/cli/azure/vm#create) 命令來建立 VM。 

下列範例會建立名為 `myVM` 的 VM，並建立 SSH 金鑰 (如果它們不存在於預設金鑰位置)。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image Oracle:Oracle-Database-Ee:12.1.0.2:latest --data-disk-sizes-gb 20 --size Standard_DS2_v2  --generate-ssh-keys
```

建立 VM 後，Azure CLI 會顯示類似下列範例的資訊。 記下 `publicIpAddress`。 此位址用來存取 VM。

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

## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

使用下列命令，建立與虛擬機器的 SSH 工作階段。 以虛擬機器的 `publicIpAddress` 取代 IP 位址。

```bash 
ssh <publicIpAddress>
```

## <a name="create-database"></a>建立 Database

Oracle 軟體已安裝於 Marketplace 映像上，因此下一個步驟是安裝資料庫。 第一個步驟是以「oracle」超級使用者執行並初始化要記錄的接聽程式：

```bash
su oracle
Password: <enter initial oracle password: xxxxxxxx >
[oracle@myVM /]$ lsnrctl start
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

下一個步驟是建立資料庫：

```bash
[oracle@myVM /]$ dbca -silent -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 -sid cdb1 -responseFile NO_VALUE \
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

## <a name="set-up-connectivity"></a>設定連線 
本機連線測試

建立資料庫後，我們即需要設定 ORACLE_HOME 和 ORACLE_SID 環境變數。

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME

ORACLE_SID=cdb1; export ORACLE_SID
```

現在我們可以使用 sqlplus 進行連線︰

```bash
sqlplus / as sysdba

SQL*Plus: Release 12.1.0.2.0 Production on Fri Apr 7 13:16:30 2017

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

```

最後一件事是設定外部端點。 由於需要設定保護 VM 的 Azure 網路安全性群組，因此我們應在 VM 上結束 SSH 工作階段。 我們使用 Azure CLI 執行此命令︰

```bash
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVmNSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

結果看起來應該會像下面的回應這樣：

```
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

若要測試的外部連線，請在遠端電腦上執行 sqlplus。 連線前，請先建立該 PC 上建立「tnsnames.ora」檔案︰

```
azure_pdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<vm-name>.cloudapp.net)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=pdb1)
    )
  )
```


## <a name="delete-virtual-machine"></a>刪除虛擬機器

若不再需要，您可以使用下列命令來移除資源群組、VM 和所有相關資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

[建立高可用性的虛擬機器教學課程](../../linux/create-cli-complete.md)

[瀏覽 VM 部署 CLI 範例](../../linux/cli-samples.md)

