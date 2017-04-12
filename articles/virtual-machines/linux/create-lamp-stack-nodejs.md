---
title: "使用 Azure CLI 1.0 在 Linux 虛擬機器上部署 LAMP | Microsoft Docs"
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
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: feba2fb20d1831e92358ff5d1b4c9589d63d28dc
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-lamp-stack-with-the-azure-cli-10"></a>使用 Azure CLI 1.0 部署 LAMP 堆疊
本文會逐步引導您在 Azure 上部署 Apache Web 伺服器、MySQL 和 PHP (LAMP 堆疊)。 您需要 Azure 帳戶 ([取得免費試用](https://azure.microsoft.com/pricing/free-trial/)) 和[連接到您的 Azure 帳戶](../../xplat-cli-connect.md)的 [Azure CLI](../../cli-install-nodejs.md)。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0] – 適用於傳統和資源管理部署模型的 CLI (本文)
- [Azure CLI 2.0](create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 適用於資源管理部署模型的新一代 CLI

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

* 在現有 VM 上部署 LAMP

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>逐步解說在新的 VM 上部署 LAMP
首先，您可以建立將包含新 VM 的[資源群組](../../azure-resource-manager/resource-group-overview.md)：

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

若要建立 VM 本身，您可以使用已撰寫的 Azure Resource Manager 範本 (位於 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app))。

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

您應會看到提示更多輸入的回應︰

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

您現在已建立安裝了 LAMP 的 Linux VM。 如果您希望，您可以向下跳到[確認 LAMP 安裝成功](#verify-lamp-successfully-installed)來確認安裝。

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>逐步解說在現有 VM 上部署 LAMP
如果您需要建立 Linux VM 的說明，您可以前往[這裡以了解如何建立 Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 接下來，您需要透過 SSH 登入 Linux VM。 如果您需要建立 SSH 金鑰的說明，您可以前往[這裡以了解如何在 Linux/Mac 上建立 SSH 金鑰](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
如果您已經有 SSH 金鑰，請繼續進行，並從您的命令列透過 SSH 使用 `ssh exampleUsername@exampleDNS` 登入 Linux VM。

您目前正在 Linux VM 內運作，我們可逐步引導您在以 Debian 為基礎的散發版本上安裝 LAMP 堆疊。 其他 Linux 散發版本的確切命令可能會有所不同。

#### <a name="installing-on-debianubuntu"></a>安裝在 Debian/Ubuntu 上
您需要安裝下列封裝：`apache2`、`mysql-server`、`php5` 和 `php5-mysql`。 直接擷取這些封裝或使用 Tasksel，即可安裝這些封裝。 這兩個選項的指示如下所列。
安裝之前，您必須下載並更新封裝清單。

    user@ubuntu$ sudo apt-get update

##### <a name="individual-packages"></a>個別封裝
使用 apt-get：

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>使用 Tasksel
或者，您也可以下載 Tasksel，這是一個 Debian/Ubuntu 工具，以協調工作的方式安裝多個相關套件到您的系統上。

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

執行上述任一選項之後，隨即會提示您安裝這些封裝和其他各種相依性。 按 'y' 然後按 'Enter' 鍵以繼續進行，並遵循所有其他提示，即可設定 MySQL 的管理密碼。 這會安裝使用 PHP 搭配 MySQL 時所需的基本必要 PHP 擴充功能。 

![][1]

請執行下列命令，以查看可以封裝形式提供的其他 PHP 擴充功能：

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>建立 info.php 文件
您現在應可透過命令列輸入 `apache2 -v`、`mysql -v` 或 `php -v`，檢查您有哪個版本的 Apache、MySQL 和 PHP。

如果您想要進一步測試，您可以建立快速 PHP 資訊頁面，以在瀏覽器中檢視。 透過以下命令，使用 Nano 文字編輯器建立檔案：

    user@ubuntu$ sudo nano /var/www/html/info.php

在 GNU Nano 文字編輯器中，新增下列幾行：

    <?php
    phpinfo();
    ?>

然後，儲存並結束文字編輯器。

使用這個命令重新啟動 Apache，讓所有新的安裝生效。

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>確認 LAMP 安裝成功
現在您可以開啟瀏覽器並移至 http://youruniqueDNS/info.php，來檢查您建立的 PHP 資訊頁面。 該頁面看起來應該類似下圖。

![][2]

前往 http://youruniqueDNS/ 檢視 Apache2 Ubuntu 預設頁面，即可檢查您的 Apache 安裝。 您應該會看到類似下圖的內容。

![][3]

恭喜，您剛已在 Azure VM 上安裝 LAMP 堆疊！

## <a name="next-steps"></a>後續步驟
請查看 LAMP 堆疊上的 Ubuntu 文件︰

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/deploy-lamp-stack/apachesuccesspage.png
