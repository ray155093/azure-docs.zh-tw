<properties
   pageTitle="使用虛擬機器擴充功能將應用程式部署自動化 | Microsoft Azure"
   description="Azure 虛擬機器 DotNet 核心教學課程"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# 使用 Azure Resource Manager 範本進行應用程式部署

識別出所有 Azure 基礎結構需求並轉譯成部署範本之後，就必須處理實際的應用程式部署需求。這裡的應用程式部署係指將實際的應用程式二進位檔安裝到 Azure 資源上。以「音樂市集」範例來說，必須在每一部虛擬機器上安裝並設定 .Net 核心、NGINX 及監督員。必須將「音樂市集」二進位檔安裝到虛擬機器上，並且必須預先建立「音樂市集」資料庫。

本文件詳細說明「虛擬機器」擴充功能如何將 Azure 虛擬機器的應用程式部署和設定自動化。所有相依項目和獨特的設定都會以醒目提示的方式標示。為了獲得最佳體驗，請將一個解決方案執行個體預先部署到您的 Azure 訂用帳戶，然後與 Azure Resource Manager 範本搭配運作。您可以在下列連結找到完整的範本 – [Ubuntu 上的音樂市集部署](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)。

## 組態指令碼

「虛擬機器」擴充功能是針對虛擬機器執行以提供組態自動化的特製化程式。針對許多特定用途 (防毒、記錄組態及 Docker 組態) 都有擴充功能可供使用。自訂指令碼擴充功能可以用來對虛擬機器執行任何指令碼。在「音樂市集」範例中，需仰賴自訂指令碼擴充功能來設定 Ubuntu 虛擬機器及安裝「音樂市集」應用程式。

在詳細說明如何在 Azure Resource Manager 範本中宣告虛擬機器擴充功能之前，請先檢查所執行的指令碼。此指令碼會設定 Ubuntu 虛擬機器以裝載「音樂市集」應用程式。在執行時，指令碼會安裝所有必要的軟體、從原始檔控制安裝「音樂市集」應用程式，以及準備資料庫。

若要深入了解如何在 Linux 上裝載 .Net 核心應用程式，請參閱 [Publish to a Linux Production Environment (發佈至 Linux 生產環境)](https://docs.asp.net/en/latest/publishing/linuxproduction.html)。


```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## VM 指令碼擴充功能

透過將擴充功能資源包含在 Azure Resource Manager 範本中，即可在建置階段對虛擬機器執行「VM 指令碼擴充功能」。您可以使用 Visual Studio 的「加入新資源」精靈或在範本中插入有效的 JSON，來新增擴充功能。「指令碼擴充功能」資源內嵌在「虛擬機器」資源的巢狀結構中；在下列範例中即可看到此情況。

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [VM 指令碼擴充功能](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359)。

請注意，在下列 JSON 中，指令碼是儲存在 GitHub。此指令碼也可以儲存在 Azure Blob 儲存體中。此外，Azure Resource Manager 範本也允許建構指令碼執行字串，讓範本參數值可被用來當作指令碼執行參數。在此案例中，是在部署範本時提供資料，接著在執行指令碼時，就可以使用這些值。

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

如需有關使用自訂指令碼擴充功能的詳細資訊，請參閱[使用 Resource Manager 範本來自訂指令碼擴充功能](./virtual-machines-linux-extensions-customscript.md)。

## 後續步驟

<hr>

[探索其他 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates)

<!---HONumber=AcomDC_0928_2016-->