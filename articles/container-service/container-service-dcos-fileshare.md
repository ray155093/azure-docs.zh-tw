---
title: "Azure DC/OS 叢集的檔案共用 | Microsoft Docs"
description: "建立檔案共用並將它掛接到 Azure Container Service 中的 DC/OS 叢集"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service
keywords: "Docker, Containers, Micro-services, Mesos, Azure, FileShare, cifs, 容器, 微服務"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 6d40821327a9df47bb85ea12ecd33e4a0f49e39e
ms.lasthandoff: 03/22/2017


---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>建立檔案共用並將它掛接到 DC/OS 叢集
在此文章中，我們會探索在 Azure 上建立檔案共用，並將它掛接到 DC/OS 叢集的每個代理程式和主機的方法。 設定檔案共用能使在整個叢集上共用檔案 (例如設定、存取、記錄等等) 變得更容易。

在練習此範例之前，您需要已在 Azure Container Service 中設定的 DC/OS 叢集。 請參閱[部署 Azure Container Service 叢集](container-service-deployment.md)。

## <a name="create-a-file-share-on-microsoft-azure"></a>在 Microsoft Azure 上建立檔案共用
### <a name="using-the-portal"></a>使用入口網站

1. 登入入口網站。
2. 建立儲存體帳戶。
   
  ![Azure Container Service 建立儲存體帳戶](media/container-service-dcos-fileshare/createSA.png)

3. 建立之後，按一下 [服務] 區段中的 [檔案]。
   
  ![Azure Container Service [檔案] 區段](media/container-service-dcos-fileshare/filesServices.png)

4. 按一下 [+ 檔案共用]，然後輸入新共用的名稱 ([配額] 並非必要項目)。
   
  ![Azure Container Service [+ 檔案共用]](media/container-service-dcos-fileshare/newFileShare.png)  

### <a name="using-azure-cli-20"></a>使用 Azure CLI 2.0

如有必要，請[安裝並設定 Azure CLI](/cli/azure/install-azure-cli.md)。

```azurecli
################# Change these four parameters ##############
DCOS_PERS_STORAGE_ACCOUNT_NAME=anystorageaccountname
DCOS_PERS_RESOURCE_GROUP=AnyResourceGroupName
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=demoshare
#############################################################

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>在叢集中掛接共用

接著，我們需要使用 CIFS 工具/通訊協定，將此共用掛接到叢集內的所有虛擬機器上。 我們使用下列命令來執行此操作：`mount -t cifs`。

以下的範例使用：
* 儲存體帳戶名稱 **`anystorageaccountname`**
* 虛構的帳戶金鑰 **`P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==`** 
* 掛接點 **`/mnt/share/demoshare`**

```bash
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```

我們會在叢集的每個虛擬機器 (主機和代理程式節點) 上執行此命令。 如果您有大量的代理程式，我們建議您建立指令碼以自動化此程序。  

### <a name="set-up-scripts"></a>設定指令碼

1. 首先，以 SSH 方式連線到 DC/OS 型叢集的主機 (或主要主機)。 例如，`ssh userName@masterFQDN –A –p 22`，其中 masterFQDN 是主機 VM 的完整網域名稱。

2. 將您的私密金鑰複製到主機上的工作目錄 (~)。

3. 使用下列命令變更其權限：`chmod 600 yourPrivateKeyFile`。

4. 使用 `ssh-add yourPrivateKeyFile` 命令匯入您的私密金鑰。 如果第一次無法運作，您可能需要執行 `eval ssh-agent -s`。

5. 在主機上，使用您慣用的編輯器 (例如 VI、Nano 或 VIM) 建立兩個檔案： 
  
  * 一個具有要在每個 VM上執行的指令碼，檔名是 **cifsMount.sh** 
  * 另一個會起始呼叫第一個指令碼的所有 SSH 連線，檔名是 **mountShares.sh**


```bash
# cifsMount.sh

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```
  
```bash
# mountShares.sh

# Install jq used for the next command
sudo apt-get install jq

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share on the current vm (master)
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes
  
# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
  do
    ssh `whoami`@$line -o StrictHostKeyChecking=no -i yourPrivateKeyFile < ./cifsMount.sh
    done
```  
> [!IMPORTANT]
> 您必須使用自己的設定 (例如儲存體帳戶的名稱和密碼) 來變更 **'mount'** 命令。
>  

您建立前述指令碼的資料夾，現在應該有 3 個檔案：  

* **cifsMount.sh**
* **mountShares.sh**
* **yourPrivateKeyFile** 

### <a name="run-the-scripts"></a>執行指令碼

使用下列命令執行 **mountShares.sh** 檔案：`sh mountShares.sh`。

您應該會看到結果顯示於終端機中。 指令碼完成之後，即可在叢集中使用檔案共用。

您可以將指令碼最佳化，此範例的指令碼較為直覺，其目的是為了提供指引。

> [!NOTE] 
> 不建議將此方法用於高 IOPS 的案例，但此方法很適合在叢集上共用文件和資訊。
>

## <a name="next-steps"></a>後續步驟
* 深入了解如何[管理 DC/OS 容器](container-service-mesos-marathon-ui.md)。
* 透過 [Marathon REST API](container-service-mesos-marathon-rest.md) 進行 DC/OS 容器管理。
