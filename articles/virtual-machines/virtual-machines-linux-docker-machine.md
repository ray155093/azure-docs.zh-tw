---
title: 用 Docker 電腦在 Azure 中建立 Docker 主機 | Microsoft Docs
description: 說明如何使用 Docker 電腦在 Azure 中建立 Docker 主機。
services: virtual-machines-linux
documentationcenter: ''
author: squillace
manager: timlt
editor: tysonn

ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2016
ms.author: rasquill

---
# 使用 Docker 電腦搭配 Azure 驅動程式
[Docker](https://www.docker.com/) 是最常用的虛擬化方式之一，它不使用虛擬機器，而是使用 Linux 容器，作為在共用資源上隔離應用程式資料和執行計算的方法。本主題說明何時及如何使用 [Docker 電腦](https://docs.docker.com/machine/) (`docker-machine` 命令) 在 Azure 中建立新的 Linux VM，啟用為 Linux 容器的 Docker 主機。

## 使用 Docker 電腦建立 VM
使用驅動程式選項 (`-d`) 的 `azure` 驅動程式引數和任何其他引數，以 `docker-machine create` 命令在 Azure 中建立 Docker 主機 VM。

下例依賴預設值，但它確實會開啟連接網際網路的 VM 連接埠 80 以使用 nginx 容器來進行測試、讓 `ops` 成為 SSH 的登入使用者，以及呼叫新的 VM `machine`。

輸入 `docker-machine create --driver azure` 查看選項及其預設值，您也可以參閱 [Docker Azure 驅動程式文件](https://docs.docker.com/machine/drivers/azure/)。(請注意，如果啟用了雙因素驗證，系統會提示您使用第二個因素驗證)。

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

輸出應該類似下面這樣，端視您的帳戶中是否設定了雙因素驗證。

```
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## 設定您的 Docker 介面
現在請輸入 `docker-machine env <VM name>`，看看設定介面需要做些什麼。

```bash
docker-machine env machine
```

這會列印環境資訊，看起來像這樣。請注意，IP 位址已指派，如此您需要測試 VM。

```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

您可以執行建議的組態命令，或自行設定環境變數。

## 執行容器
現在，您可以執行簡單的 Web 伺服器，測試所有項目是否都運作正常。我們在此使用標準的 nginx 映像，指定它應接聽連接埠 80，而且若 VM 重新啟動，則容器也應該重新啟動 (`--restart=always`)。

```bash
docker run -d -p 80:80 --restart=always nginx
```

輸出應該類似下面這樣：

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## 測試容器
使用 `docker ps` 檢查執行中的容器：

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

查看執行中的容器，輸入 `docker-machine ip <VM name>` 以尋找 IP 位址 (如果忘記請從 `env` 命令)：

![執行 ngnix 容器](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## 後續步驟
如果有興趣，您可以試用 Azure [Docker VM 擴充功能](virtual-machines-linux-dockerextension.md)，用 Azure CLI 或 Azure Resource Manager 範本執行相同的作業。

如需更多使用 Docker 的範例，請參閱來自 [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [示範](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)的 [Working with Docker (使用 Docker)](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker)。如需來自 HealthClinic.biz 示範的更多快速入門，請參閱 [Azure Developer Tools Quickstarts (Azure 開發人員工具快速入門)](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)。

<!---HONumber=AcomDC_0727_2016-->