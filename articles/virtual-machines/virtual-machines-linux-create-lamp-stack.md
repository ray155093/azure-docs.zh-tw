---
title: "在 Azure 的 Linux 虛擬機器上部署 LAMP | Microsoft Docs"
description: "了解如何在 Azure 的 Linux VM 上安裝 LAMP 堆疊"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jluk
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: NA
ms.topic: article
ms.date: 2/21/2017
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: 61e8824fe1a63efb215c7be25d3e6d7c1649d8a5
ms.openlocfilehash: 3709add7fd8d97138fd858e2b260c23300dad378
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-lamp-stack-on-azure"></a>在 Azure 上部署 LAMP 堆疊
本文會逐步引導您在 Azure 上部署 Apache Web 伺服器、MySQL 和 PHP (LAMP 堆疊)。 您需要 Azure 帳戶 ([取得免費試用](https://azure.microsoft.com/pricing/free-trial/)) 和 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)。 您也可以使用 [Azure CLI 1.0](virtual-machines-linux-create-lamp-stack-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 來執行這些步驟。

## <a name="quick-command-summary"></a>快速命令摘要

1. 將 [azuredeploy.parameters.json 檔案](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json)儲存並編輯到本機電腦上的喜好設定。
2. 執行下列兩個命令來建立資源群組，然後部署範本︰

```azurecli
az group create -l westus -n myResourceGroup
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

### <a name="deploy-lamp-on-existing-vm"></a>在現有 VM 上部署 LAMP
下列命令會更新套件，然後安裝 Apache、MySQL 和 PHP︰

```bash
sudo apt-get update
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>逐步解說在新的 VM 上部署 LAMP

1. 使用 [az group create](/cli/azure/group#create) 來建立資源群組以包含新的 VM：

```azurecli
az group create -l westus -n myResourceGroup
```
若要建立 VM 本身，您可以使用已撰寫的 Azure Resource Manager 範本 (位於 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app))。

2. 將 [azuredeploy.parameters.json 檔案](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json)儲存到本機電腦上。
3. 將 **azuredeploy.parameters.json** 檔案編輯到慣用的輸入中。
4. 使用參考所下載 json 檔案的 [az group deployment create] 來部署範本︰

```azurecli
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

輸出類似於下列範例：

```json
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Resources/deployments/azuredeploy",
"name": "azuredeploy",
"properties": {
    "correlationId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "debugSetting": null,
}
...
"provisioningState": "Succeeded",
"template": null,
"templateLink": {
    "contentVersion": "1.0.0.0",
    "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json"
    },
    "timestamp": "2017-02-22T00:05:51.860411+00:00"
},
"resourceGroup": "myResourceGroup"
}
```

您現在已建立安裝了 LAMP 的 Linux VM。 如果您希望，您可以向下跳到[確認 LAMP 安裝成功](#verify-lamp-successfully-installed)來確認安裝。

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>逐步解說在現有 VM 上部署 LAMP
如果您需要建立 Linux VM 的說明，您可以前往[這裡以了解如何建立 Linux VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-cli)。 接下來，您需要透過 SSH 登入 Linux VM。 如果您需要建立 SSH 金鑰的說明，您可以前往[這裡以了解如何在 Linux/Mac 上建立 SSH 金鑰](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
如果您已經有 SSH 金鑰，請繼續進行，並從您的命令列透過 SSH 使用 `ssh azureuser@mypublicdns.westus.cloudapp.azure.com` 登入 Linux VM。

您目前正在 Linux VM 內運作，我們可逐步引導您在以 Debian 為基礎的散發版本上安裝 LAMP 堆疊。 其他 Linux 散發版本的確切命令可能會有所不同。

#### <a name="installing-on-debianubuntu"></a>安裝在 Debian/Ubuntu 上
您需要安裝下列封裝：`apache2`、`mysql-server`、`php5` 和 `php5-mysql`。 直接擷取這些封裝或使用 Tasksel，即可安裝這些封裝。
安裝之前，您必須下載並更新封裝清單。

```bash
sudo apt-get update
```

##### <a name="individual-packages"></a>個別封裝
使用 apt-get：

```bash
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

##### <a name="using-tasksel"></a>使用 Tasksel
或者，您也可以下載 Tasksel，這是一個 Debian/Ubuntu 工具，以協調工作的方式安裝多個相關套件到您的系統上。

```bash
sudo apt-get install tasksel
sudo tasksel install lamp-server
```

執行上述任一選項之後，隨即會提示您安裝這些封裝和其他各種相依性。 若要設定 MySQL 的管理密碼，請按 [y] 然後按 Enter 鍵以繼續進行，並遵循所有其他提示。 此程序會安裝使用 PHP 搭配 MySQL 時所需的基本必要 PHP 擴充功能。 

![][1]

請執行下列命令，以查看可以封裝形式提供的其他 PHP 擴充功能：

```bash
apt-cache search php5
```

#### <a name="create-infophp-document"></a>建立 info.php 文件
您現在應可透過命令列輸入 `apache2 -v`、`mysql -v` 或 `php -v`，檢查您有哪個版本的 Apache、MySQL 和 PHP。

如果您想要進一步測試，您可以建立快速 PHP 資訊頁面，以在瀏覽器中檢視。 透過以下命令，使用 Nano 文字編輯器建立檔案：

```bash
sudo nano /var/www/html/info.php
```

在 GNU Nano 文字編輯器中，新增下列幾行：

```php
<?php
phpinfo();
?>
```

然後，儲存並結束文字編輯器。

使用這個命令重新啟動 Apache，讓所有新的安裝生效。

```bash
sudo service apache2 restart
```

## <a name="verify-lamp-successfully-installed"></a>確認 LAMP 安裝成功
現在您可以開啟瀏覽器並移至 http://youruniqueDNS/info.php，來檢查您建立的 PHP 資訊頁面。 該頁面看起來應該類似下圖。

![][2]

前往 http://youruniqueDNS/ 檢視 Apache2 Ubuntu 預設頁面，即可檢查您的 Apache 安裝。 輸出類似於下列範例：

![][3]

恭喜，您剛已在 Azure VM 上安裝 LAMP 堆疊！

## <a name="next-steps"></a>後續步驟
請查看 LAMP 堆疊上的 Ubuntu 文件︰

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png

