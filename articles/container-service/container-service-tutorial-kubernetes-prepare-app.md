---
title: "Azure Container Service 教學課程 - 準備應用程式 | Microsoft Docs"
description: "Azure Container Service 教學課程 - 準備應用程式"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服務, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 49d727de69c700af0ae6402ad54b5880010ebb3b
ms.contentlocale: zh-tw
ms.lasthandoff: 06/28/2017

---

# <a name="create-container-images-to-be-used-with-azure-container-service"></a>建立要搭配 Azure Container Service 使用的容器映像

在本教學課程中，已針對 Kubernetes 備妥一個應用程式。 完成的步驟包括：  

> [!div class="checklist"]
> * 從 GitHub 複製應用程式來源  
> * 從應用程式來源建立容器映像
> * 在本機 Docker 環境中測試映像

在後續的教學課程中，這些容器映像會上傳至 Azure Container Registry，然後在 Azure 裝載的 Kubernetes 叢集中執行。

## <a name="before-you-begin"></a>開始之前

本教學課程假設使用者對核心 Docker 概念有基本認識，例如容器、容器映像和基本 Docker 命令。 如有需要，請參閱[開始使用 Docker]( https://docs.docker.com/get-started/)以取得容器基本概念入門。 

若要完成本教學課程，您需要 Docker 開發環境。 Docker 提供可輕鬆在 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系統上設定 Docker 的套件。

## <a name="get-application-code"></a>取得應用程式程式碼

本教學課程中使用的範例應用程式是基本投票應用程式。 應用程式是由前端 Web 元件和後端資料庫所組成。 

使用 git 將應用程式的複本下載至您的開發環境。

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

在應用程式目錄中，可以找到預先建立的 Dockerfiles 和 Kubernetes 資訊清單檔案。 這些檔案可用來建立整套教學課程的資產。 

## <a name="create-container-images"></a>建立容器映像

若要建立前端應用程式的容器映像，請使用 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令。

```bash
docker build ./azure-voting-app/azure-vote -t azure-vote-front
```

重複執行命令，此時針對後端容器映像。

```bash
docker build ./azure-voting-app/azure-vote-mysql -t azure-vote-back
```

完成時，使用 `docker images` 命令來查看所建立的映像。 

```bash
docker images
```

輸出：

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

## <a name="test-application"></a>測試應用程式

現已建立兩個容器映像，請在本機開發環境中測試這些映像。 

首先，建立 Docker 網路。 此網路用於容器之間的通訊。  

```bash
docker network create azure-vote
```

使用 `docker run` 命令執行後端容器映像的執行個體。

在此範例中，mysql 資料庫檔案會儲存在容器內。 一旦此應用程式移至 Kubernete 叢集，外部資料磁碟區會用來儲存資料庫檔案。 此外，環境變數正用於設定 MySQL 認證。

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

執行前端容器映像的執行個體。

環境變數正用於設定資料庫連線資訊。

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front
```

完成時，執行 `docker ps` 以查看執行中的容器。  

```bash
docker ps
```

輸出：

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
3aa02e8ae965        azure-vote-front     "/usr/bin/supervisord"   59 seconds ago      Up 57 seconds       443/tcp, 0.0.0.0:8080->80/tcp   flaskmysqlvote_azure-vote-front_1
5ae60b3ba181        azure-vote-backend   "docker-entrypoint..."   59 seconds ago      Up 58 seconds       0.0.0.0:3306->3306/tcp          azure-vote-back
```

瀏覽至 `http://localhost:8080` 以查看執行中的應用程式。 此應用程式需要幾秒鐘的時間初始化。 如果發生錯誤，請再試一次。

![azure 上 Kubernetes 叢集的影像](media/container-service-kubernetes-tutorials/vote-app.png)

## <a name="clean-up-resources"></a>清除資源

現已驗證應用程式功能，可以停止並移除執行中的容器。 請勿刪除容器映像。 這些映像會在下一個教學課程中上傳至 Azure Container Registry 執行個體。

使用 [docker rm](https://docs.docker.com/engine/reference/commandline/rm/) 命令來停止並刪除前端容器。 

```bash
docker rm -f azure-vote-front
```

使用 [docker rm](https://docs.docker.com/engine/reference/commandline/rm/) 命令來停止並刪除後端容器。 

```bash
docker rm -f azure-vote-back
```

使用 [docker network rm](https://docs.docker.com/engine/reference/commandline/network_rm/) 命令來刪除網路。

```bash
docker network rm azure-vote
```

完成時，您有組成 Azure Vote 應用程式的兩個容器映像。

## <a name="next-steps"></a>後續步驟

在本教學課程中，應用程式已經過測試並已建立應用程式的容器映像。 已完成下列步驟：

> [!div class="checklist"]
> * 從 GitHub 複製應用程式來源  
> * 從應用程式來源建立容器映像
> * 在本機 Docker 環境中測試映像

前往下一個教學課程，了解如何在 Azure Container Registry 中儲存容器映像。

> [!div class="nextstepaction"]
> [將映像推送至 Azure Container Registry](./container-service-tutorial-kubernetes-prepare-acr.md)
