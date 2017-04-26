---
title: "搭配使用 ACR 與 Azure DC/OS 叢集 | Microsoft Docs"
description: "在 Azure Container Service 中搭配使用 Azure Container Registry 與 DC/OS 叢集"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: "Docker, Containers, Micro-services, Mesos, Azure, FileShare, cifs, 容器, 微服務"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a8a3716f8d03b596285026426c7514b7b642cb25
ms.lasthandoff: 03/31/2017


---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>搭配使用 ACR 與 Azure DC/OS 叢集以部署應用程式

本文將探討如何搭配使用私人容器登錄 (例如 ACR (Azure Container Registry)) 與 DC/OS 叢集。 使用 ACR 可讓您私人儲存映像並對它進行控制，例如版本和 (或) 更新。

在練習此範例之前，您需要︰ 
* 已在 Azure Container Service 中設定的 DC/OS 叢集。 請參閱[部署 Azure Container Service 叢集](container-service-deployment.md)。
* 部署 Azure Container Service。 請參閱[使用 Azure 入口網站建立私人 Docker 容器登錄](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)或[使用 Azure CLI 2.0 建立私人 Docker 容器登錄](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli)
* 已在 DC/OS 叢集內設定檔案共用。 請參閱[建立檔案共用並將它掛接到 DC/OS 叢集](container-service-dcos-fileshare.md)
* 了解如何使用 [Web UI](container-service-mesos-marathon-ui.md) 或 [REST API](container-service-mesos-marathon-rest.md) 在 DC/OS 叢集中部署 Docker 映像

## <a name="manage-the-authentication-inside-your-cluster"></a>管理叢集內的驗證

從私人登錄推送和提取映像的傳統方式是先在其上進行驗證。 若要這麼做，您必須在需要使用您私人登錄的任何 Docker 用戶端處理序上使用 `docker login` 命令列。
而在生產環境中 (本例使用 DC/OS)，您會想要確定您可以從任何節點提取映像。 這表示您想要自動進行驗證程序，而不要在每個電腦上執行命令列，因為您可以想像得到，根據您的叢集大小而定，這會是繁重而惱人的作業。 

假設您已經[在 DC/OS 內設定檔案共用](container-service-dcos-fileshare.md)，我們會透過下列方式來利用它︰

### <a name="from-any-client-machine-recommended-method"></a>從任何用戶端電腦 [建議方法]

下列命令可在任何環境 (Windows/Mac/Linux) 上執行︰

1. 確定您符合下列必要條件︰
  * TAR 工具
    * [Windows](http://gnuwin32.sourceforge.net/packages/gtar.htm)
  * Docker 
    * [Windows](https://www.docker.com/docker-windows)
    * [MAC](https://www.docker.com/docker-mac)
    * [Ubuntu](https://www.docker.com/docker-ubuntu)
    * [其他](https://www.docker.com/get-docker)
  * 檔案共用已[透過下列方法](container-service-dcos-fileshare.md)掛接在您的叢集內

2. 在您偏好使用的終端機上使用下列命令來起始 ACR 服務的驗證︰`sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`。 您必須以您在 Azure 入口網站上提供的值取代 `USERNAME`、`PASSWORD` 和 `ACR-REGISTRY-NAME` 變數

3. 有趣的是，當您在進行 `docker login` 作業時，這些值會儲存在電腦本機上的主資料夾底下 (在 Mac 和 Linux 上是 `cd ~/.docker`，在 Windows 上是 `cd %HOMEPATH%`)。 我們會使用 `tar czf` 命令來壓縮此資料夾的內容。

4. 最後一個步驟是將我們剛才建立的 tar 檔案，複製到[您建立作為必要條件](container-service-dcos-fileshare.md)的檔案共用內。 您可以搭配使用 Azure-CLI 與下列命令 `az storage file upload -s <shareName> --account-name <storageAccountName> --account-key <storageAccountKey> -source <pathToTheTarFile>` 來執行這個步驟

總結來說，使用下列設定的範例 (使用 Windows 環境) 如下︰
* ACR 名稱：**`demodcos`**
* 使用者名稱：**`demodcos`**
* 密碼：**`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* 儲存體帳戶名稱︰**`anystorageaccountname`**
* 儲存體帳戶金鑰︰**`aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg==`**
* 在儲存體帳戶內建立的共用名稱︰**`share`**
* 要上傳之 tar 封存檔的路徑︰**`%HOMEPATH%/.docker/docker.tar.gz`**

```bash
# Changing directory to the home folder of the default user
cd %HOMEPATH%

# Authentication into my ACR
docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
tar czf docker.tar.gz .docker

# Upload the tar archive in the fileshare
az storage file upload -s share --account-name anystorageaccountname --account-key aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg== --source %HOMEPATH%/docker.tar.gz
```

### <a name="from-the-master-not-recommended-method"></a>從主機 [不建議的方法]

我們不建議您從主機執行作業，這樣才能避免錯誤及影響整個環境。

1. 首先，以 SSH 方式連線到 DC/OS 型叢集的主機 (或主要主機)。 例如，`ssh userName@masterFQDN –A –p 22`，其中 masterFQDN 是主機 VM 的完整網域名稱。 [按一下這裡來了解詳細資訊](https://docs.microsoft.com/azure/container-service/container-service-connect#connect-to-a-dcos-or-swarm-cluster)

2. 使用下列命令來起始 ACR 服務的驗證︰`sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`。 您必須以您在 Azure 入口網站上提供的值取代 `USERNAME`、`PASSWORD` 和 `ACR-REGISTRY-NAME` 變數

3. 有趣的是，當您在進行 `docker login` 作業時，這些值會儲存在電腦本機上的主資料夾 `~/.docker` 底下。 我們會使用 `tar czf` 命令來壓縮此資料夾的內容。

4. 最後一個步驟是將我們剛才建立的 tar 檔案複製到檔案共用內。 這項作業可讓叢集內的所有虛擬機器使用此認證，並在 Azure Container Registry 上進行驗證。

總結來說，使用下列設定的範例如下︰
* ACR 名稱：**`demodcos`**
* 使用者名稱：**`demodcos`**
* 密碼：**`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* 叢集內的掛接點︰**`/mnt/share`**

```bash
# Changing directory to the home folder of the default user
cd ~

# Authentication into my ACR
sudo docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
sudo tar czf docker.tar.gz .docker

# Copy of the tar file in the file share of my cluster
sudo cp docker.tar.gz /mnt/share
```


## <a name="deploy-an-image-from-acr-with-marathon"></a>使用 Marathon 從 ACR 部署映像

您應該已經將您想要部署的映像推送至容器登錄內。 請參閱[使用 Docker CLI 將您的第一個映像推送至私人 Docker 容器登錄](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)

假設我們想要從裝載在 Azure 上的私人登錄 (ACR) 部署 **simple-web** 映像 (具有 **2.1** 標籤)，我們會使用下列設定︰

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "demodcos.azurecr.io/simple-web:2.1",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  },
  "uris":  [
       "file:///mnt/share/docker.tar.gz"
   ]
}
```

> [!NOTE] 
> 如您所見，我們使用 **uris** 選項來指定認證的儲存位置。
>

## <a name="next-steps"></a>後續步驟
* 深入了解如何[管理 DC/OS 容器](container-service-mesos-marathon-ui.md)。
* 透過 [Marathon REST API](container-service-mesos-marathon-rest.md) 進行 DC/OS 容器管理。
