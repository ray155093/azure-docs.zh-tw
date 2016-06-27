<properties
   pageTitle="虛擬機器上的 Docker 和 Compose | Microsoft Azure"
   description="在 Azure 中的 Linux 虛擬機器上使用 Compose 和 Docker 的快速簡介"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/10/2016"
   ms.author="danlep"/>

# 在 Azure 虛擬機器上開始使用 Docker 和 Compose 定義並執行多容器應用程式

開始使用 Docker 和 [Compose](http://github.com/docker/compose)，在 Azure 中的 Linux 虛擬機器上定義並執行複雜的應用程式。藉由 Compose (*Fig* 的後續版本)，您可使用簡單的文字檔，定義多個 Docker 容器所組成的應用程式。然後您可以透過可進行所有操作以便在 VM 上執行的單一命令，啟動您的應用程式。

例如，本文將說明如何藉由 Ubuntu VM 上的後端 MariaDB SQL 資料庫快速設定 WordPress 部落格，但您也可以使用 Compose 來設定更複雜的應用程式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


如果您是初次使用 Docker 和容器，請參閱 [Docker 高階白板](https://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/)。

## 步驟 1：設定 Linux VM 做為 Docker 主機

您可以使用 Azure Markeplace 中的各種 Azure 程序和可用的映像或 Resource Manager 範本來建立 Linux VM，並將其設定為 Docker 主機。例如，請參閱[使用 Docker VM 延伸模組來部署您的環境](virtual-machines-linux-dockerextension.md)來取得快速程序，以使用[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)建立具有 Docker VM 擴充功能的 Ubuntu VM。當您使用 Docker VM 延伸模組時，您的 VM 會自動設為 Docker 主機，並已安裝 Compose。該文章中的範例會示範如何使用 Resource Manager 模式中[適用於 Mac、Linux 和 Windows 的 Azure 命令列介面](../xplat-cli-install.md) (Azure CLI) 建立 VM。

## 步驟 2︰確認已安裝 Compose

Linux VM 和 Docker 執行之後，請使用 SSH 從用戶端電腦連線到此 VM。

若要在 VM 上測試 Compose 的安裝，請執行下列命令。

```
$ docker-compose --version
```

您將看到類似 `docker-compose 1.6.2, build 4d72027` 的輸出內容。

>[AZURE.TIP] 如果您使用另一種方法來建立 Docker 主機，而且需要自行安裝 Compose，請參閱 [Compose 文件](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)。


## 步驟 3：建立 docker-compose.yml 組態檔

接下來，請建立 `docker-compose.yml` 檔案，這只是一個文字組態檔，用來定義要在此 VM 上執行的 Docker 容器。此檔案會指定要在每個容器上執行的映像 (或可能是來自 Dockerfile 的組建)、所需的環境變數和相依性、連接埠，容器之間的連結等等。如需有關 yml 檔案語法的詳細資訊，請參閱 [Compose 檔案參考](http://docs.docker.com/compose/yml/)。

在 VM 上建立工作目錄，並使用您慣用的文字編輯器建立 `docker-compose.yml`。若要嘗試簡單範例來進行概念驗證，請將下列文字複製到檔案。此組態會使用來自 [DockerHub 登錄](https://registry.hub.docker.com/_/wordpress/)的映像，安裝 WordPress (開放原始碼部落格和內容管理系統) 和連結的後端 MariaDB SQL 資料庫。

 ```
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

## 步驟 4：以 Compose 啟動容器

在您 VM 的工作目錄中，執行以下命令。(視您的環境而定，您可能需要使用 `sudo` 執行 `docker-compose`。)

```
$ docker-compose up -d

```

如此會啟動 `docker-compose.yml` 中指定的 Docker 容器。您會看到類似以下的輸出：

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE] 請務必在啟動時使用 **-d** 選項，讓容器在背景持續執行。

若要確認容器是否已啟動，請輸入 `docker-compose ps`。您應該會看到如下的內容：

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

您現在可以在 VM 的連接埠 80 上直接連線到 WordPress。如果您使用 Resource Manager 範本建立 VM，請嘗試連線到 `http://<dnsname>.<region>.cloudapp.azure.com`，而如果您使用傳統部署模型建立 VM，請嘗試連線到 `http://<cloudservicename>.cloudapp.net`。您現在應該會看到 WordPress 起始畫面，您可以在其中完成安裝，然後開始使用此應用程式。

![WordPress 起始畫面][wordpress_start]


## 後續步驟

* 移至 [Docker VM 延伸模組使用者指南](https://github.com/Azure/azure-docker-extension/blob/master/README.md)，以了解在 Docker VM 內設定 Docker 和 Compose 的更多選項。例如，其中一個選項是將 Compose yml 檔案 (轉換為 JSON) 直接置於 Docker VM 延伸模組的組態中。
* 如需建置和部署多容器應用程式的其他範例，請參閱 [Compose 命令列參考](http://docs.docker.com/compose/reference/)和[使用者指南](http://docs.docker.com/compose/)。
* 使用 Azure Resource Manager 範本 (您自己的範本或[社群](https://azure.microsoft.com/documentation/templates/)提供的範本) 部署包含 Docker 的 Azure VM，以及使用 Compose 設定的應用程式。例如，[以 Docker 部署 WordPress 部落格](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql)範本使用 Docker 和 Compose，藉由 Ubuntu VM 上的 MySQL 後端快速部署 WordPress。
* 嘗試整合 Docker Compose 與 [Docker Swarm](virtual-machines-linux-docker-swarm.md) 叢集。如需案例，請參閱[使用 Compose 與 Swarm](https://docs.docker.com/compose/swarm/)。

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_0615_2016-->