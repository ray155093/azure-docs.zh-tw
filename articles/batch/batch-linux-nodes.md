<properties
	pageTitle="Azure Batch 集區中的 Linux 節點 | Microsoft Azure"
	description="了解如何在 Azure Batch 中處理您的 Linux 虛擬機器集區的平行計算工作負載。"
	services="batch"
	documentationCenter="python"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="na"
	ms.date="04/19/2016"
	ms.author="marsma" />

# 在 Azure Batch 集區中佈建 Linux 計算節點

Azure Batch 可讓您同時在 Linux 和 Windows 虛擬機器上執行平行計算工作負載。本文將詳細說明如何同時使用 [Batch Python][py_batch_package] 和 [Batch .NET][api_net] 用戶端程式庫，在 Batch 服務中建立 Linux 計算節點的集區。

> [AZURE.NOTE] Batch 中的 Linux 支援目前為預覽狀態。在公開上市之前，此處所討論功能的某些層面可能會變更。[應用程式封裝](batch-application-packages.md)和[多重執行個體工作](batch-mpi.md)目前在Linux 計算節點上**不支援**。

## 虛擬機器組態

在 Batch 中建立計算節點集區時，有兩個選項可選取節點大小和作業系統︰**雲端服務組態**和**虛擬機器組態**。

**雲端服務組態**只提供 Windows 計算節點。可用的計算節點大小列於[雲端服務的大小](../cloud-services/cloud-services-sizes-specs.md)，而可用的作業系統則列於 [Azure 客體 OS 版次與 SDK 相容性矩陣](../cloud-services/cloud-services-guestos-update-matrix.md)。建立包含雲端服務節點的集區時，您僅必須指定可以在這些文章中找到的節點大小以及其「OS 系列」。建立 Windows 計算節點的集區時，最常使用的是雲端服務。

**虛擬機器組態**可同時提供 Linux 和 Windows 映像計算節點。可用的計算節點大小列於 [Azure 中的虛擬機器大小](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) 和 [Azure 中的虛擬機器大小](../virtual-machines/virtual-machines-windows-sizes.md) (Windows)。建立包含虛擬機器組態節點的集區時，您不僅需指定節點的大小，也必須在節點上安裝**虛擬機器映像參考**和 Batch **節點代理程式 SKU**。

### 虛擬機器映像參考

Batch 服務會在幕後使用[虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)來提供 Linux 計算節點，而這些虛擬機器的作業系統映像則是由 [Azure 虛擬機器 Marketplace][vm_marketplace] 提供。設定虛擬機器映像參考時，您會指定 Marketplace 虛擬機器映像的屬性。建立虛擬機器映像參考時，會需要下列屬性︰

| **映像參考屬性** | **範例** |
| ----------------- | ------------------------ |
| 發行者 | Canonical |
| 提供項目 | UbuntuServer |
| SKU | 14\.04.4-LTS |
| 版本 | 最新 |

> [AZURE.TIP] 您可以在[使用 CLI 或 PowerShell 在 Azure 中巡覽並選取 Linux 虛擬機器映像](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md)中深入了解這些屬性，以及如何列出 Marketplace 映像。請注意，並非所有 Marketplace 映像目前都與 Batch 相容，請參閱以下的 [Node 代理程式 SKU](#node-agent-sku)。

### Node 代理程式 SKU

Batch 節點代理程式是一項程式，會在集區中的每個節點上執行，並在節點與 Batch 服務之間提供命令和控制介面。節點代理程式對不同作業系統有不同的實作方式，稱為 SKU。基本上，建立虛擬機器組態時，您必須先指定虛擬機器映像參考，然後指定要在其上安裝映像的代理程式節點。一般而言，每個節點代理程式可與多個虛擬機器映像 SKU 相容。以下是一些節點代理程式的 SKU 的範例︰

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [AZURE.IMPORTANT] 並非 Marketplace 中所有可用的虛擬機器映像都與目前可用的 Batch 節點代理程式相容。您必須使用 Batch SDK 來列出可用的節點代理程式 SKU 和其相容的虛擬機器映像。如需詳細資訊，請參閱以下的[虛擬機器映像的清單](#list-of-virtual-machine-images)。

## 建立 Linux 集區︰Batch Python

下列程式碼片段顯示如何使用 [Python 適用的 Microsoft Azure Batch 用戶端程式庫][py_batch_package]來建立 Ubuntu Server 計算節點的集區。Batch Python 模組的參考文件，可在此找到：[azure.batch package ][py_batch_docs] 閱讀文件。

在此程式碼片段中，我們會明確建立 [ImageReference][py_imagereference]，指定每一個屬性 (發行者、服務、SKU、版本)。不過，我們建議您在實際執行程式碼中使用 [list\_node\_agent\_skus][py_list_skus] 方法來判斷並在執行階段從可用映像和節點代理程式 SKU 組合中選擇。

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

如上所述，我們建議不要明確建立 [ImageReference][py_imagereference]，您可以使用 [list\_node\_agent\_skus][py_list_skus] 方法從目前支援的節點代理程式/Marketplace 映像組合中動態選取。下列 Python 程式碼片段說明這個方法的使用方式。

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## 建立 Linux 集區︰Batch .NET

下列程式碼片段顯示如何使用 [Batch .NET][nuget_batch_net] 用戶端程式庫來建立 Ubuntu Server 計算節點集區。您可以在 MSDN 上找到 [Batch .NET 參考文件][api_net]。

下列程式碼片段使用 [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] 方法來從目前支援的 Marketplace 映像和節點代理程式 SKU 組合清單中選取。這項技術最理想，因為支援的組合清單可能會隨著時間變更 (最常見的是新增支援的組合)。

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

雖然上述程式碼片段使用 [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] 方法，以動態列出並從支援的映像和節點代理程式 SKU 組合 (建議) 中選取，您也可以明確設定 [ImageReference][net_imagereference]︰

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## 虛擬機器映像的清單

下表列出**在撰寫本文時**與可用的 Batch 節點代理程式相容的 Marketplace 虛擬機器映像。請務必注意，此清單並非永久不變，因為可能隨時新增或移除映像和節點代理程式。我們建議您的 Batch 應用程式和服務一律使用 [list\_node\_agent\_skus][py_list_skus] (Python) 和 [ListNodeAgentSkus][net_list_skus] (Batch .NET)，以判斷並從目前可用的 SKU 中選取。

> [AZURE.WARNING] 下列清單可能隨時變更。一律使用 Batch API 中提供的**清單節點代理程式 SKU**方法來列出，並在執行 Batch 作業時，從相容的虛擬機器和節點代理程式的 SKU 選取。

| **發行者** | **提供項目** | **映像 SKU** | **版本** | **節點代理程式 SKU 識別碼** |
| ------- | ------- | ------- | ------- | ------- |
| Canonical | UbuntuServer | 14\.04.0-LTS | 最新 | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.1-LTS | 最新 | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.2-LTS | 最新 | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.3-LTS | 最新 | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.4-LTS | 最新 | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 15\.10 | 最新 | batch.node.debian 8 |
| Credativ | Debian | 8 | 最新 | batch.node.debian 8 |
| OpenLogic | CentOS | 7\.0 | 最新 | batch.node.centos 7 |
| OpenLogic | CentOS | 7\.1 | 最新 | batch.node.centos 7 |
| OpenLogic | CentOS | 7\.2 | 最新 | batch.node.centos 7 |
| Oracle | Oracle-Linux-7 | OL70 | 最新 | batch.node.centos 7 |
| SUSE | SLES | 12 | 最新 | batch.node.opensuse 42.1 |
| SUSE | SLES | 12-SP1 | 最新 | batch.node.opensuse 42.1 |
| SUSE | SLES-HPC | 12 | 最新 | batch.node.opensuse 42.1 |
| SUSE | openSUSE | 13\.2 | 最新 | batch.node.opensuse 13.2 |
| SUSE | openSUSE-Leap | 42\.1 | 最新 | batch.node.opensuse 42.1 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | 最新 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | 最新 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | 最新 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview | 最新 | batch.node.windows amd64 |

## 連接至 Linux 節點

在開發期間或問題進行疑難排解時，您可能會發現需要登入您的集區中的節點。不同於 Windows 計算節點，您無法使用遠端桌面通訊協定 (RDP) 連線到 Linux 節點。相反地，Batch 服務可讓您遠端連線的每個節點上的 SSH 存取。

下列 Python 程式碼片段會在集區中的每個節點上建立使用者 (遠端連線所需)，然後列印每個節點的 SSH 連線資訊。

```python
import getpass

# Specify the username and prompt for a password
username = "linuxuser"
password = getpass.getpass()

# Create the user that will be added to each node
# in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = client.compute_node.get_remote_login_settings(pool_id,
                                                          node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

以下是包含四個 Linux 節點集區的上述程式碼的範例輸出︰

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

請注意，在節點上建立使用者時，不要使用密碼，而是可以指定 SSH 公開金鑰。在 Python SDK 中，這是在 [ComputeNodeUser][py_computenodeuser] 上使用 **ssh\_public\_key** 參數完成，而在 .NET 中，這是使用 [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key] 屬性完成。

## 定價

Azure Batch 採用 Azure 雲端服務和 Azure 虛擬機器技術。Batch 服務本身為免費提供，這表示您僅需支付對 Batch 解決方案所使用的計算資源。選擇**雲端服務組態**時，將會根據[雲端服務定價][cloud_services_pricing]結構向您收費。選擇**虛擬機器組態**時，將會根據[虛擬機器定價][vm_pricing]結構向您收費。

## 後續步驟

### Batch Python 程式碼範例

查看 GitHub 上 [azure-batch-samples][github_samples] 儲存機制中的 [Python 程式碼範例][github_samples_py]，以取得為您示範如何執行一般 Batch 作業 (例如建立集區、作業和工作) 的數個指令碼。Python 範例隨附的[讀我檔案][github_py_readme]具有安裝所需套件的詳細資訊。

### Batch 論壇

MSDN 上的 [Azure Batch 論壇][forum]是一個很棒的地方，可以討論 Batch 和詢問有關服務的問題。請前往查看實用的「便利貼」文章，並在建置 Batch 解決方案時，若發生問題就張貼問題。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/zh-TW/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/

[1]: ./media/batch-application-packages/app_pkg_01.png "應用程式封裝高階圖表"

<!---HONumber=AcomDC_0427_2016-->