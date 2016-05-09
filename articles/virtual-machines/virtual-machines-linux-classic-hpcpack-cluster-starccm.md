<properties
 pageTitle="使用 HPC Pack 在 Linux VM 上執行 StarCCM+ | Microsoft Azure"
 description="在 Azure 上部署 Microsoft HPC Pack 叢集，並且跨 RDMA 網路在多個 Linux 計算節點上執行 StarCCM+ 作業。"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="kateh"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="04/13/2016"
 ms.author="xpillons"/>

# 在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 StarCCM+
本文將說明如何在 Azure 上部署 Microsoft HPC Pack 叢集，並在與 Infiniband 相互連接的多個 Linux 計算節點上執行 [CD-Adapco StarCCM+](http://www.cd-adapco.com/products/star-ccm%C2%AE) 作業。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack 提供功能來執行各種大規模 HPC 和平行應用程式，包括 Microsoft Azure 虛擬機器的叢集上的 MPI 應用程式。HPC Pack 也支援在 HPC Pack 叢集中部署的 Linux 計算節點 VM 上，執行 Linux HPC 應用程式。如需搭配 HPC Pack 使用 Linux 計算節點的簡介，請參閱[開始在 Azure 中的 HPC Pack 叢集使用 Linux 計算節點](virtual-machines-linux-classic-hpcpack-cluster.md)。

## 設定 HPC Pack 叢集
從[這裡](https://www.microsoft.com/zh-TW/download/details.aspx?id=44949)下載 HPC Pack IaaS 部署指令碼，並將它們解壓縮到本機。

Azure PowerShell 是必要條件，如果未在本機電腦上設定，請閱讀此文章[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

在撰寫本文時，來自 Azure 資源庫的 Linux 映像，其包含的 Azure Infiniband 驅動程式適用於 SLES 12、CentOS 6.5 和 CentOS 7.1。這篇文章根據 SLES 12 的使用方式。若要擷取資源庫中支援 HPC 的所有 Linux 映像的名稱，您可以執行下列 PowerShell 命令︰

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

輸出會列出提供映像的位置和稍後會在部署範本中使用的 **ImageName**。部署叢集之前，您必須先建置 HPC Pack 部署範本檔案，並且因為我們的目標是小型叢集，前端節點會是網域控制站並裝載本機 SQL 資料庫。下列範本會部署這樣的一個前端節點、建立名稱為 **MyCluster.xml** 的 XML 檔案，並將 **SubscriptionId**、**StorageAccount**、**Location**、**VMName** 和 **ServiceName** 的值取代成您的值。

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

在提高權限的命令視窗執行 PowerShell 命令，開始建立前端節點︰

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

20 到 30 分鐘之後，前端節點應該會就緒。從 Azure 入口網站上按一下虛擬機器的 [連接] 圖示，即可連接到它。

您最終可能必須修正 DNS 轉寄站。若要這樣做，請啟動 [DNS 管理員]。

1.  在 [DNS 管理員] 中以滑鼠右鍵按一下伺服器名稱，選取 [內容]，然後選取 [轉寄站] 索引標籤

2.  按一下 [編輯] 按鈕來移除任何轉寄站，然後按一下 [確定]

3.  確定已勾選 [如果沒有可用的轉寄站，則使用根目錄提示] 核取方塊，然後按一下 [確定]

## 設定 Linux 計算節點
部署 Linux 計算節點是透過上述用來建立前端節點完全相同的部署範本來完成。將檔案 **MyCluster.xml** 從本機電腦的複製到前端節點，並以您想要部署的節點數目 (<= 20) 更新 **NodeCount** 標記。務必在您的 Azure 配額中有足夠的可用核心，因為為每個 A9 執行個體將會使用您的訂用帳戶中的 16 個核心。如果您想要在相同的預算中使用更多的 VM，您可以使用 A8 執行個體 (8 核心) 而不是 A9。

在前端節點上，複製 HPC Pack IaaS 部署指令碼。

在提高權限的命令視窗中開啟 Azure PowerShell：

1.  執行 **Add-AzureAccount** 以連接您的 Azure 訂用帳戶

2.  如果您有多個訂用帳戶，請執行 **Get-AzureSubscription** 來列出這些訂用帳戶

3.  執行命令 **Select-AzureSubscription -SubscriptionName xxxx -Default** 來設定其中一個為預設值

4.  執行 **.\\New-HPCIaaSCluster.ps1-ConfigFile MyCluster.xml** 以開始部署 Linux 計算節點。![前端節點部署實務][hndeploy]

開啟 [HPC Pack 叢集管理員] 工具，幾分鐘後，Linux 計算節點會定期出現在叢集計算節點的清單中。對於傳統部署模式，IaaS VM 是循序建立，因此，如果節點數目很重要，可能需要一段相當長的時間才能部署所有節點。![HPC Pack 叢集管理員中的 Linux 節點][clustermanager]

現在，叢集中的所有節點都已啟動並執行，還有其他可執行的基礎結構設定。

## 為 Windows 和 Linux 節點設定 Azure 檔案共用
為了讓我們更輕鬆操作，我們會使用「Azure 檔案服務」在 Azure Blob 上提供 CIFS 功能，以做為持續性存放區，可儲存指令碼、應用程式封裝和資料檔案。請注意，這不是最具調整性的解決方案，但它是最簡單且不需要專用 VM 的解決方案。

依照文章[在 Windows 上開始使用 Azure 檔案儲存體](..\storage\storage-dotnet-how-to-use-files.md)的指示來建立 Azure 檔案共用

將儲存體帳戶名稱保持為 **saname**，檔案共用名稱 **sharename** 和儲存體帳戶金鑰 **sakey**。

### 在前端節點上掛接 Azure 檔案共用
開啟提升權限的命令提示字元，然後執行下列命令，將認證儲存在本機電腦的保存庫中

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

然後掛接 Azure 檔案共用執行

```
    net use Z: \<saname>.file.core.windows.net<sharename> /persistent:yes
```

### 在 Linux 計算節點上掛接 Azure 檔案共用
HPC Pack 隨附的一個實用工具是 clusrun 公用程式。此命令列可讓您在一組計算節點上同時執行相同的命令。在本例中，它用來掛接 Azure 檔案共用，然後將它保存以避免重新開機。在前端節點上提高權限的命令視窗中，執行下列命令。

建立掛接點目錄。

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

掛接 Azure 檔案共用

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

保存掛接共用

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## 更新 Linux 驅動程式
您可能最終必須更新 Linux 計算節點的 Infiniband 驅動程式。請閱讀這篇文章來了解是否需要以及如何[為 SLES 12 更新 Linux RDMA 驅動程式](virtual-machines-linux-classic-rdma-cluster.md/#update-the-linux-rdma-drivers-for-sles-12)

## 安裝 StarCCM+
Azure VM 執行個體 A8 和 A9 提供 Infiniband 支援與 RDMA 功能。啟用這些功能的核心驅動程式在針對 Windows 2012 R2 和 SUSE 12 映像撰寫時可在 Azure 資源庫中取得，CentOS 7.x 即將推出。Microsoft MPI 和 Intel MPI (5.x 版) 是 Azure 中可支援這些驅動程式的兩個 MPI 程式庫。

CD-Adapco StarCCM+ 發行 11.x 和以上版本隨附 Intel MPI 5.x 版，因此含有 Azure 的 Infiniband 支援。

如果我們在混合精度中使用版本 11.02.010，請從 [CD-Adapco 入口網站](https://steve.cd-adapco.com)取得 Linux64 StarCCM+ 封裝。

在前端節點上，於 **/hpcdata** Azure 檔案共用中，使用下列內容建立名為 **setupstarccm.sh** 的殼層指令碼。此指令碼就會在每個計算節點上啟動，以在本機上設定 StarCCM+。

#### 範例 setupstarcm.sh 指令碼
```
    #!/bin/bash
    # setupstarcm.sh setup StarCCM+ locally

    # create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # copy the starccm package from the file share to the local dir
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # start a silent installation of starccm without flexlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
現在，若要在所有 Linux 計算節點上設定 StarCCM+，請開啟提升權限的命令視窗，並執行下列命令

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

執行命令時，您可以使用叢集管理員的熱圖來監視 CPU 使用量。幾分鐘後，所有節點應該都會正確設定。

## 執行 StarCCM+ 工作
HPC Pack 用於其工作排程器功能，以便執行 StarCCM+ 作業。若要這樣做，我們需要用來開始作業和執行 StarCCM+ 的一些指令碼的支援。為了簡單起見，輸入資料會先保留在 Azure 檔案共用上。下列 PowerShell 指令碼用於將 StarCCM+ 作業放入佇列。它會採用 3 個引數︰

*  模型名稱
*  要使用的節點數目
*  要在每個節點上使用的核心數目

因為 StarCCM+ 可以善用記憶體頻寬，一般來說，最好每個計算節點使用較少的核心，並新增新節點。每個節點的確切核心速率取決於處理器系列和相互連接速度。

會對節點配置專用的作業，而且不能與其他作業共用。如您所見，作業不會直接以 MPI 作業的形式開始，MPI 啟動程式將會從 **runstarccm.sh** 殼層指令碼開始。

輸入模型和 **runstarccm.sh** 預期會儲存在先前掛接的 **/hpcdata** 共用中。

記錄檔會以作業識別碼來命名，並儲存在 **/hpcdata 共用** 以及 StarCCM+ 輸出檔案中。


#### 範例 SubmitStarccmJob.ps1 指令碼
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # create a new job, this will give us the JobId used to identify the name of the uploaded package in azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # submit job 	
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
將 **runner.java** 取代為您偏好的 StarCCM+ java 模型啟動程式與記錄程式碼。

#### 範例 runstarccm.sh 指令碼
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
    	echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
    	let "I=${I}+2"
    	let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run the starccm with hostfile arg
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
    	-power -podkey "<yourkey>" -rsh ssh \
    	-mpi intel -fabric UDAPL -cpubind bandwidth,v \
    	-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
    	-batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

在我們的測試中，我們使用的是 Power-One-Demand 授權權杖，您必須對其將 **$CDLMD\_LICENSE\_FILE** 環境變數設定為 ****1999@flex.cd-adapco.com**，以及在命令列的 **-podkey** 選項中設定索引鍵。

在進行某些初始化之後，指令碼會從 HPC Pack 設定的 **$CCP\_NODES\_CORES** 環境變數中擷取要建置 hostfile 的節點清單，供 MPI 啟動程式使用。此 hostfile 將包含用於作業的計算節點名稱的清單，一行一個名稱。

**$CCP\_NODES\_CORES** 的格式會遵循下列模式：

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

其中

* `<Number of nodes>`：配置給此工作的節點數目。  
* `<Name of node_n_...>`：配置給此工作的各節點名稱。
* `<Cores of node_n_...>`：配置給此工作的節點核心數目。

核心數目 **$NBCORES** 也是根據節點的數目 **$NBNODES** 和提供做為參數的每個節點核心數目 **$NBCORESPERNODE** 來計算。

針對 MPI 選項，在 Azure 上與 Intel MPI 搭配使用的一個選項是︰

*   mpi intel => 指定 IntelMPI
*   -fabric UDAPL => 使用 Azure 的 Infiniband 動詞
*   -cpubind bandwidth,v => 這是要使用 StarCCM+ 最佳化 MPI 的頻寬
*   -mppflags "-ppn $NBCORESPERNODE -genv I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0 -genv I\_MPI\_DAPL\_UD=0 -genv I\_MPI\_DYNAMIC\_CONNECTION=0" => 這些是要與 Azure Infiniband 搭配使用的 Intel MPI 設定，而且它也會設定每個節點所需的核心數目
*   -batch => 在 Batch 模式下 (無使用者介面) 啟動 StarCCM+


最後，若要開始作業，請確定您的節點啟動並執行，並且在叢集管理員中處於線上狀態。然後從 PowerShell 命令視窗執行此作業︰

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## 停止節點
之後當您完成測試時，若要停止及啟動節點，您可以使用下列 HPC Pack PowerShell 命令︰

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## 後續步驟
嘗試執行其他 Linux 的工作負載，例如，請參閱︰

* [在 Azure 中的 Linux 計算節點以 Microsoft HPC Pack 執行 NAMD](virtual-machines-linux-classic-hpcpack-cluster-namd.md)。

* [在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 OpenFOAM](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)。


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png

<!---HONumber=AcomDC_0427_2016-->