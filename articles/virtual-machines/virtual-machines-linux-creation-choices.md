<properties
	pageTitle="建立 Linux VM 的不同方式 | Microsoft Azure"
	description="列出在 Azure 建立 Linux 虛擬機器的不同方式，並提供進一步指示的連結"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# 使用 Resource Manager 建立 Linux 虛擬機器的不同方式

Azure 提供使用 Resource Manager 部署模型建立 VM 的不同方式，以供不同的使用者和用途使用。本文章將摘要說明這些差異，以及您建立 Linux 虛擬機器 (VM) 時可做的選擇。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

## 工具選項

### 命令殼層︰Azure CLI 

從 CLI，使用 Azure 命令列介面。您可以深入了解如何透過 npm、Docker 容器或安裝指令碼[安裝 Azure CLI](../xplat-cli-install.md)。下列教學課程提供有關使用 Azure CLI 的範例︰

* [從 Azure CLI 建立用於開發和測試的 Linux VM](virtual-machines-linux-quick-create-cli.md) 

* [使用 Azure 範本建立受保護的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

* [使用 Azure CLI 從頭開始建立 Linux VM](virtual-machines-linux-create-cli-complete.md)

### GUI：Azure 入口網站

[Azure 入口網站](https://portal.azure.com)的圖形化使用者介面是試用 VM 的簡單方法，特別是在您剛開始使用 Azure 時，因為您的系統上沒有要安裝的項目。使用 Azure 入口網站來建立 VM：

* [使用 Azure 入口網站建立執行 Linux 的虛擬機器](virtual-machines-linux-portal-create.md) 

## 作業系統和映像選項

利用這兩種方法，根據您想要執行的作業系統來選擇映像。Azure 與其合作夥伴提供許多映像，其中有些包括已預先安裝的應用程式和工具。或者，您可以上傳您自己的其中一個映像。

### Azure 映像

在上述的所有文章中，您可以輕鬆地使用現有的 Azure 映像來建立 VM，並針對網路、負載平衡及更多功能來自訂。入口網站有提供 Azure Marketplace，其中網羅了 Azure 提供的映像。您可以使用命令列取得類似的清單。例如，在 Azure CLI 中執行 `azure vm image list` 來取得所有可用映像的清單 (根據位置和發行者提供)。如需有關瀏覽和使用可用映像的範例，請參閱[使用 Azure CLI 巡覽和選取 Azure 虛擬機器映像](virtual-machines-linux-cli-ps-findimage.md)。

### 使用您自己的映像

如果您需要特定自訂項目，可以「擷取」現有的 Azure VM，使用以該 VM 為基礎的映像，或者上傳您自己的映像 (儲存於虛擬硬碟 (VHD) 中)。如需有關支援的散發版本以及如何使用自己的映像的詳細資訊，請參閱下列文件：

* [Azure 背書的散發套件](virtual-machines-linux-endorsed-distros.md)

* [非背書散發套件的資訊](virtual-machines-linux-create-upload-generic.md)

* [如何擷取 Linux 虛擬機器以做為 Resource Manager 範本](virtual-machines-linux-capture-image.md)。

## 後續步驟

* 嘗試其中一個教學課程，以透過[入口網站](virtual-machines-linux-portal-create.md)、[CLI](virtual-machines-linux-quick-create-cli.md) 或 Azure Resource Manager [範本](virtual-machines-linux-cli-deploy-templates.md)建立 Linux VM。

* 在建立 Linux VM 後，您可以輕鬆地[新增資料磁碟](virtual-machines-linux-add-disk.md)。

* [重設密碼或 SSH 金鑰及管理使用者](virtual-machines-linux-using-vmaccess-extension.md)的快速步驟

<!---HONumber=AcomDC_0518_2016-->