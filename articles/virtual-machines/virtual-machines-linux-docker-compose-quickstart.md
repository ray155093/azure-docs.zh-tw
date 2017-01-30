---
title: "在 Azure 中使用 Docker Compose 連接至 Linux VM | Microsoft Docs"
description: "如何在 Azure 中的 Linux 虛擬機器上使用 Docker 和 Compose"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 3295120664e409440641818b13dd1abab6f2f72f
ms.openlocfilehash: 06ad7f9267f24ee1f2fe417ad4aa0bf1096832d6


---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>在 Azure 虛擬機器上開始使用 Docker 和 Compose 定義並執行多容器應用程式。
藉由 [Compose](http://github.com/docker/compose)，您將可以使用簡單的文字檔來定義由多個 Docker 容器所組成的應用程式。 接著，您可以透過單一命令來啟動應用程式，此命令會執行所需的一切準備工作，以部署您的已定義環境。 舉例來說，本文將說明如何藉由 Ubuntu VM 上的後端 MariaDB SQL 資料庫來快速設定 WordPress 部落格。 您也可以使用 Compose 來設定更複雜的應用程式。

## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>步驟 1：設定 Linux VM 做為 Docker 主機
您可以使用 Azure Marketplace 中的各種 Azure 程序和可用映像或 Resource Manager 範本來建立 Linux VM，並將其設定為 Docker 主機。 例如，請參閱[使用 Docker VM 擴充功能來部署您的環境](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，以使用[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)藉由 Azure Docker VM 擴充功能建立 Ubuntu VM。 

當您使用 Docker VM 擴充功能時，您的 VM 會自動設為 Docker 主機，並已安裝 Compose。 該文章中的範例會示範如何在 Resource Manager 模式中使用 [Azure CLI 1.0](../xplat-cli-install.md) 建立 VM。

先前文件的基本命令會在 `West US` 位置中建立名為 `myResourceGroup` 的資源群組，並使用已安裝的 Azure Docker VM 延伸模組部署 VM︰

```azurecli
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Azure CLI 僅在幾秒鐘後讓您回到提示，但仍會建立並設定您的 Docker 主機。 需要幾分鐘的時間才能完成部署。 您可以使用 `azure vm show` 命令檢視關於 Docker 主機狀態的詳細資訊。 下列範例會在名為 `myResourceGroup` 的資源群組中檢查名為 `myDockerVM` 的 VM 狀態 (範本的預設名稱 - 請勿變更這個名稱)。 輸入您在上一個步驟中建立的資源群組名稱︰+

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

在接近輸出頂端的地方，您會看到 VM 的 `ProvisioningState` 。 當這顯示為 `Succeeded`時，即表示部署已完成，您可以透過 SSH 連線到 VM。

## <a name="step-2-verify-that-compose-is-installed"></a>步驟 2︰確認已安裝 Compose
部署完成之後，請使用您在部署期間提供的 DNS 名稱，透過 SSH 連接到新的 Docker 主機。 您可以使用 `azure vm show -g myDockerResourceGroup -n myDockerVM` 檢視您 VM 的詳細資料，包括 DNS 名稱。

若要檢查 Compose 是否已安裝在 VM 上，請執行下列命令：

```bash
docker-compose --version
```

您會看到類似 `docker-compose 1.6.2, build 4d72027`的輸出。

> [!TIP]
> 如果您使用另一種方法來建立 Docker 主機而需要自行安裝 Compose，請參閱 [Compose 文件](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)。


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>步驟 3：建立 docker-compose.yml 組態檔
接下來，您需建立 `docker-compose.yml` 檔案，這只是一個文字組態檔，用來定義要在 VM 上執行的 Docker 容器。 此檔案會指定要在每個容器上執行的映像 (或可能是來自 Dockerfile 的組建)、必要的環境變數和相依性、連接埠，以及容器之間的連結。 如需有關 yml 檔案語法的詳細資料，請參閱 [Compose file reference (Compose 檔案參考)](http://docs.docker.com/compose/yml/)。

建立 `docker-compose.yml` 檔案，如下所示：

```bash
touch docker-compose.yml
```

使用慣用的文字編輯器將一些資料新增至檔案。 下列範例使用 `vi` 編輯器：

```bash
vi docker-compose.yml
```

將下列範例貼入文字檔案。 此組態會使用來自 [DockerHub 登錄](https://registry.hub.docker.com/_/wordpress/) 的映像，安裝 WordPress (開放原始碼部落格和內容管理系統) 和連結的後端 MariaDB SQL 資料庫。 輸入您自己的 `MYSQL_ROOT_PASSWORD`，如下所示︰

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>步驟 4：以 Compose 啟動容器
在與您 `docker-compose.yml` 檔案相同的目錄中，執行下列命令 (根據您的環境，您可能需要使用 `sudo` 執行 `docker-compose`。)：

```bash
docker-compose up -d
```

這個命令會啟動 `docker-compose.yml`中指定的 Docker 容器。 此步驟需要幾分鐘的時間來完成。 您會看到類似以下範例的輸出：

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> 請務必在啟動時使用 **-d** 選項，讓容器在背景持續執行。


若要確認容器是否已啟動，請輸入 `docker-compose ps`。 您應該會看到如下的內容：

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

您現在可以在 VM 的連接埠 80 上直接連線到 WordPress。 開啟網頁瀏覽器並輸入您 VM 的 DNS 名稱 (例如 `http://myresourcegroup.westus.cloudapp.azure.com`)。 您現在應該會看到 WordPress 起始畫面，您可以在其中完成安裝，然後開始使用此應用程式。

![WordPress 起始畫面][wordpress_start]

## <a name="next-steps"></a>後續步驟
* 如需了解在 Docker VM 中設定 Docker 和 Compose 的更多選項，請移至 [Docker VM 擴充功能使用者指南](https://github.com/Azure/azure-docker-extension/blob/master/README.md) 。 例如，其中一個選項是將 Compose yml 檔案 (轉換為 JSON) 直接置於 Docker VM 延伸模組的組態中。
* 如需更多建置和部署多容器應用程式的範例，請參閱 [Compose command-line reference (Compose 命令列參考)](http://docs.docker.com/compose/reference/) 和[使用者指南](http://docs.docker.com/compose/)。
* 使用 Azure 資源管理員範本 (您自己的範本或 [社群](https://azure.microsoft.com/documentation/templates/)提供的範本) 部署包含 Docker 的 Azure VM，以及使用 Compose 設定的應用程式。 例如， [以 Docker 部署 WordPress 部落格](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) 範本使用 Docker 和 Compose，藉由 Ubuntu VM 上的 MySQL 後端快速部署 WordPress。
* 嘗試整合 Docker Compose 與 [Docker Swarm](virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 叢集。 如需案例，請參閱 [Using Compose with Swarm (搭配使用 Compose 與 Swarm)](https://docs.docker.com/compose/swarm/) 。

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png



<!--HONumber=Dec16_HO3-->


