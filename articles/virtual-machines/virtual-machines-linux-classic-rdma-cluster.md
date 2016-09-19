<properties
 pageTitle="要執行 MPI 應用程式的 Linux RDMA 叢集 | Microsoft Azure"
 description="建立大小為 A8 或 A9 VM 的 Linux 叢集以使用 RDMA 執行 MPI 應用程式"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# 設定 Linux RDMA 叢集以執行 MPI 應用程式

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


了解如何在 Azure 中使用 A8 和 A9 大小的虛擬機器來設定 Linux RDMA 叢集，以便執行「平行訊息傳遞介面」(MPI) 應用程式。當 Azure A8 和 A9 VM 執行支援的 Linux HPC 發行版本和支援的 MPI 實作時，MPI 應用程式可透過以遠端直接記憶體存取 (RDMA) 技術為基礎的低延遲、高輸送量網路，進行有效率的通訊。

>[AZURE.NOTE] 目前，在從 Azure Marketplace 中的 SUSE Linux Enterprise Server 12 HPC 或是 CentOS 型 6.5 或 7.1 HPC 映像建立的 VM 上，必須要有 Intel MPI Library 版本 5，才支援 Azure Linux RDMA。如需詳細資訊和考量，請參閱[關於 A8、A9、A10 和 A11 執行個體](virtual-machines-linux-a8-a9-a10-a11-specs.md)。



## 叢集部署選項

您可以使用下列方法來建立包含或不含工作排程器的 Linux RDMA 叢集。

* **HPC Pack** - 在 Azure 中建立 Microsoft HPC Pack 叢集並新增執行支援之 Linux 發行版本的 A8 或 A9 大小計算節點以存取 RDMA 網路。請參閱[開始在 Azure 中的 HPC Pack 叢集使用 Linux 計算節點](virtual-machines-linux-classic-hpcpack-cluster.md)。

* **Azure CLI 指令碼** - 如本文稍後所示，請使用 [Azure 命令列介面](../xplat-cli-install.md) (CLI) 來處理 A8 或 A9 大小之 Linux VM 叢集的部署指令碼。「服務管理」模式下的 CLI 會以傳統部署模型循序建立叢集節點，因此如果部署許多計算節點，可能需要花費數分鐘的時間。在傳統部署模型中，A8 或 A9 VM 必須部署在相同的雲端服務中，才能透過 RDMA 網路連接。

* **Azure Resource Manager 範本** - 使用 Resource Manager 部署模型來部署 A8 和 A9 VM 叢集，以連線到 RDMA 網路來執行 MPI 工作負載。您可以[建立自己的範本](../resource-group-authoring-templates.md)，或檢查 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)，取得由 Microsoft 或社群貢獻的範本以部署想要的方案。Resource Manager 範本可提供一個既快速又可靠的方式來部署 Linux 叢集。在 Resource Manager 部署模型中，A8 或 A9 VM 必須部署在相同的可用性設定組中，才能透過 RDMA 網路連接。

## 傳統模型中的範例部署

下列步驟示範如何使用 Azure CLI 從 Azure Marketplace 部署 SUSE Linux Enterprise Server (SLES) 12 HPC VM、安裝 Intel MPI Library，以及建立自訂 VM 映像。然後，使用映像來處理 A8 或 A9 VM 叢集的部署指令碼。

>[AZURE.TIP]  請使用類似的步驟，根據 Azure Marketplace 中的 CentOS 型 6.5 或 7.1 HPC 映像部署 A8 或 A9 VM 的叢集。步驟中註明差異。例如，由於 CentOS 型 HPC 映像包括 Intel MPI，您不需要在從這些映像建立的 VM 上個別安裝 Intel MPI。

### 必要條件

*   **用戶端電腦** - 您需要一部 Mac、Linux 或 Windows 型用戶端電腦來與 Azure 進行通訊。這些步驟假設您使用 Linux 用戶端。

*   **Azure 訂用帳戶** - 如果您沒有訂用帳戶，只需要幾分鐘就可以建立[免費帳戶](https://azure.microsoft.com/free/)。針對較大的叢集，請考慮隨用隨付訂用帳戶或其他購買選項。

*   **核心配額** - 您可能需要增加核心配額以部署 A8 或 A9 VM 的叢集。例如，如本文所示，如果您想要部署 8 個 A9 VM，將至少需要 128 個核心。若要增加配額，請[開立線上客戶支援要求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (免費)。

*   **Azure CLI** - [安裝](../xplat-cli-install.md) Azure CLI 並從用戶端電腦[連接到您的 Azure 訂用帳戶](../xplat-cli-connect.md)。

*   **Intel MPI** - 若要為您的叢集自訂 SLES 12 HPC VM 映像，您必須從 [Intel.com 網站](https://software.intel.com/zh-TW/intel-mpi-library/)下載並安裝最新的 Intel MPI Library 5 執行階段。本文稍後會提供詳細說明。若要為安裝做準備，在向 Intel 註冊之後，請複製適當 Intel MPI 版本之 .tgz 檔案的下載連結。這篇文章根據 Intel MPI 5.0.3.048 版。

    >[AZURE.NOTE] 如果您使用 Azure Marketplace 中的 CentOS 6.5 或 CentOS 7.1 HPC 映像來建立叢集節點，則會預先安裝 Intel MPI 5.1.3.181 版。

### 佈建 SLES 12 VM

使用 Azure CLI 登入 Azure 之後，請執行 `azure config list` 來確認輸出顯示服務管理模式。如果不是，請執行此命令來設定模式：


    azure config mode asm


請輸入下列命令來列出已授權您使用的所有訂用帳戶：


    azure account list

目前使用中訂用帳戶的 `Current` 會設定為 `true`。如果此訂用帳戶不是您想要用來建立叢集的訂用帳戶，請將適當的訂用帳戶識別碼設定為使用中的訂用帳戶：

    azure account set <subscription-Id>

若要查看 Azure 中公開可用的 SLES 12 HPC 映像，假設您的 Shell 環境支援 **grep**，請執行類似下列的命令：


    azure vm image list | grep "suse.*hpc"

現在，請透過執行類似以下的命令，以使用 SLES 12 HPC 映像來佈建 A9 大小的 VM：

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708

其中

* 大小 (在此範例中為 A9) 可以是 A8 或 A9

* 外部 SSH 連接埠號碼 (在此範例中為 22，也就是 SSH 的預設值) 是任何有效的連接埠號碼。內部 SSH 連接埠號是設定為 22

* 在 location 所指定的 Azure 區域中會建立一個新雲端服務。請指定一個有 A8 和 A9 執行個體可用的位置。

* SLES 12 映像名稱目前可以是 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` 或適用於 SUSE 優先支援方案的 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` (會另外收費)

    >[AZURE.NOTE]如果您想要使用 CentOS 型 HPC 映像，目前的映像名稱是 `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-65-HPC-20160408` 和 `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-71-HPC-20160408`。

### 自訂 VM

VM 完成佈建之後，使用 VM 的外部 IP 位址 (或 DNS 名稱) 以及您設定和自訂的外部連接埠編號來設定 VM 的 SSH。如需連接詳細資料，請參閱[如何登入執行 Linux 的虛擬機器](virtual-machines-linux-mac-create-ssh-keys.md)。除非需要根目錄存取權才能完成步驟，否則請以您在 VM 上設定的使用者身分來執行命令。

>[AZURE.IMPORTANT]Microsoft Azure 不會提供 Linux VM 的根目錄存取權。若要在以使用者身分連接至 VM 時取得系統管理存取權，請使用 `sudo` 執行命令。

* **更新** - 使用 **zypper** 安裝更新。您也可能會想要安裝 NFS 公用程式。

    >[AZURE.IMPORTANT]如果您部署了 SLES 12 HPC VM，建議您不要套用核心更新，因為這可能會導致 Linux RDMA 驅動程式發生問題。
    >
    >在來自 Marketplace 的 CentOS 型 HPC 映像上，核心更新已在 **yum** 組態檔中停用。由於 Linux RDMA 驅動程式是以 RPM 套件的形式散發，因此如果更新核心，驅動程式更新可能會無法運作。

* **Linux RDMA 驅動程式更新** - 如果您部署了 SLES 12 HPC VM，您將需要更新 RDMA 驅動程式。如需詳細資料，請參閱[關於 A8、A9、A10 和 A11 計算密集型執行個體](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12)。

* **Intel MPI** - 如果您部署 SLES 12 HPC VM，請藉由執行與下列內容相似的命令，從 Intel.com 網站下載並安裝 Intel MPI Library 5 執行階段。如果您部署 CentOS 型 6.5 或 7.1 HPC VM，就不一定需要這個步驟。

        sudo wget <download link for your registration>

        sudo tar xvzf <tar-file>

        cd <mpi-directory>

        sudo ./install.sh

    接受預設設定以在 VM 上安裝 Intel MPI。

* **鎖定記憶體** - 如需使用 MPI 程式碼防止 RDMA 使用記憶體，您需要在 /etc/security/limits.conf 檔案中加入或變更下列設定。(您必須要有根目錄存取權才能編輯此檔案。) 如果您部署 CentOS 型 6.5 或 7.1 HPC VM，設定已新增至這個檔案。

        <User or group name> hard    memlock <memory required for your application in KB>

        <User or group name> soft    memlock <memory required for your application in KB>

    >[AZURE.NOTE]基於測試目的，您也可以將 memlock 設定為無限制。例如：`<使用者或群組名稱> hard memlock unlimited。

* **SLES 12 VM 的 SSH 金鑰** - 產生 SSH 金鑰以在執行 MPI 工作時，於 SLES 12 HPC 叢集的計算節點之中建立使用者帳戶的信任。(如果您部署 CentOS 型 HPC VM，請勿遵循此步驟。請參閱本文稍後的指示，在您擷取映像並部署叢集之後，設定叢集節點間的無密碼 SSH 信任。)

    執行以下命令建立 SSH 金鑰。出現輸入提示時，只要按 Enter 即可在預設位置產生金鑰，而不需要設定複雜密碼。

        ssh-keygen

    將公開金鑰附加至已知公開金鑰的 authorized\_keys 檔案。

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    在 [~/.ssh] 目錄中，編輯或建立 "config" 檔案。請提供您打算在 Azure 中使用的私人網路 IP 位址範圍 (在本範例中為 10.32.0.0/16)：

        host 10.32.0.*
        StrictHostKeyChecking no

    或者，列出叢集中每個 VM 的私人網路 IP 位址，如下所示：

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]未指定特定 IP 位址或範圍時，設定 `StrictHostKeyChecking no` 可能會造成潛在的安全性風險。

* **應用程式** - 在此 VM 上安裝需要的任何應用程式，或在擷取映像之前執行其他自訂項目。

### 擷取映像

若要擷取映像，請先在 Linux VM 中執行下列命令。此命令會將 VM 取消佈建，但會保留您設定的使用者帳戶與 SSH 金鑰。

```
sudo waagent -deprovision
```

然後，從用戶端電腦，執行下列 Azure CLI 命令來擷取映像。如需詳細資訊，請參閱[如何將傳統 Linux 虛擬機器擷取成映像](virtual-machines-linux-classic-capture-image.md)。

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

執行這些命令後，系統會擷取 VM 映像供您使用，接著將會刪除 VM。現在您已準備好使用自訂映像來部署叢集。

### 使用映像部署叢集

針對您的環境，使用適當的值修改下列 Bash 指令碼，並從用戶端電腦執行。因為 Azure 在傳統部署模型中循序部署 VM，系統會花費幾分鐘部署這段指令碼中建議的 8 個 A9 VM。

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Select a region where A8 and A9 VMs are available, such as West US
# See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## CentOS 叢集上的無密碼 SSH 信任

如果您使用 CentOS 型 HPC 映像部署叢集，有兩種方法可建立計算節點之間的信任︰以主機為基礎的驗證和以使用者為基礎的驗證。以主機為基礎的驗證超出本文的範圍，通常必須在部署期間透過擴充功能指令碼來完成。以使用者為基礎的驗證便於在部署之後建立信任，而且需要在叢集中計算節點之間產生與共用 SSH 金鑰。此命令通常稱為無密碼 SSH 登入，執行 MPI 工作時必須使用此命令。

從社群所貢獻的範例指令碼位於 [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) 上，以在 CentOS 型 HPC 叢集上啟用簡單使用者驗證。請使用下列步驟來下載和使用此指令碼。您也可以修改此指令碼，或使用任何其他方法來建立叢集計算節點之間的無密碼 SSH 驗證。

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
若要執行此指令碼，您必須知道子網路 IP 位址的首碼。請在其中一個叢集節點上執行下列命令來取得首碼。您的輸出應該看起來像 10.1.3.5，而首碼是 10.1.3 部分。

    ifconfig eth0 | grep -w inet | awk '{print $2}'

現在請使用三個參數執行指令碼︰計算節點上的一般使用者名稱、該使用者在計算節點上的一般密碼，以及從上一個命令傳回的子網路首碼。


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

此指令碼會執行下列動作︰

* 在名為 .ssh 的主機節點上建立目錄，此為無密碼登入的必要項目。
* 在 [.ssh] 目錄中建立組態檔，以指示無密碼登入允許從任何叢集節點進行登入。
* 建立包含所有叢集節點之節點名稱與節點 IP 位址的檔案。在執行指令碼之後，會留下這些檔案以供日後參考。
* 為每個叢集節點 (包括主機節點) 建立私密和公開金鑰組，並在 authorized\_keys 檔案中建立項目。

>[AZURE.WARNING]執行這個指令碼可能會建立潛在的安全性風險。請確定未散發 ~/.ssh 中的公開金鑰資訊。


## 設定和執行 Intel MPI

若要在 Azure Linux RDMA 上執行 MPI 應用程式，您需要設定 Intel MPI 專用的特定環境變數。以下範例 Bash 指令碼，可供您設定變數並執行應用程式。視 Intel MPI 的安裝需要來變更 mpivars.sh 路徑。

```
#!/bin/bash -x

# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh


export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

主機檔案的格式如下所示。針對叢集中的每個節點加入一行指令碼。從之前定義的 VNet 指定私人 IP 位址，而非 DNS 名稱。例如，對於 IP 位址為 10.32.0.1 和 10.32.0.2 的兩台主機，此檔案包含下列內容：

```
10.32.0.1:16
10.32.0.2:16
```

## Intel MPI 安裝之後驗證兩個基本的節點叢集

如果您尚未這樣做，請先設定 Intel MPI 的環境。

```
# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### 執行簡單的 MPI 命令

在其中一個計算節點執行簡單的 MPI 命令，以顯示 MPI 已正確安裝而且可以在至少兩個計算節點之間通訊。下列 **mpirun** 命令會在兩個節點上執行 **hostname** 命令。

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
您的輸出應該會列出您傳遞做為 `-hosts` 之輸入的所有節點名稱。例如，具有兩個節點的 **mpirun** 命令會傳回類似以下的輸出：

```
cluster11
cluster12
```

### 執行 MPI 基準測試

下列的 Intel MPI 命令會使用 pingpong 基準測試驗證叢集組態和 RDMA 網路連線。

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

您應該會在包含兩個節點的工作中叢集上看到類似下列的輸出。在 Azure RDMA 網路上，大小為 512 個位元組以下的訊息預期會出現 3 百萬分之一秒或以下的延遲。

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## 後續步驟

* 嘗試在 Linux 叢集上部署並執行 Linux MPI 應用程式。

* 如需 Intel MPI 的指引，請參閱＜[Intel MPI Library 文件](https://software.intel.com/zh-TW/articles/intel-mpi-library-documentation/)＞。

* 請嘗試[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos)以使用 CentOS 型 HPC 映像建立 Intel Lustre 叢集。如需詳細資料，請參閱這篇[部落格文章](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/)。

<!----HONumber=AcomDC_0907_2016-->