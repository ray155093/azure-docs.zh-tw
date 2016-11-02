<properties
    pageTitle="在傳統入口網站中建立執行 Windows 的 VM | Microsoft Azure"
    description="在 Azure 傳統入口網站中建立 Windows 虛擬機器。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>在 Azure 傳統入口網站中建立執行 Windows 的虛擬機器

> [AZURE.SELECTOR]
- [Azure 傳統入口網站](virtual-machines-windows-classic-tutorial.md)
- [PowerShell：傳統部署](virtual-machines-windows-classic-create-powershell.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 了解如何 [使用 Resource Manager 部署模型執行這些步驟](virtual-machines-windows-hero-tutorial.md)。 如果您想要使用新的 Azure 入口網站，請參閱 [在 Azure 入口網站中建立第一個 Windows 虛擬機器](virtual-machines-windows-hero-tutorial.md)。

本教學課程示範在 Azure 傳統入口網站中建立執行 Windows 的 Azure 虛擬機器 (VM) 有多麼容易。 我們將使用 Windows Server 映像做為範例，這只是 Azure 提供眾多映像中的一種。 請注意，您可以選擇何種映像取決於您的訂用帳戶。 例如，Windows 桌面映像可能可供 MSDN 訂閱者使用。

本節說明如何使用 Azure 傳統入口網站中的 [從組件庫]  選項建立虛擬機器。 此選項提供的組態選擇比 [快速建立]  選項還多。 例如，如果您要將虛擬機器加入虛擬網路中，您必須使用 [從組件庫]  選項。

您也可以使用 [您自己的映像](virtual-machines-windows-classic-createupload-vhd.md)來建立 VM。 若要了解上述方法與其他方法，請參閱 [建立 Windows 虛擬機器的其他方式](virtual-machines-windows-creation-choices.md)。



## <a name="video-walkthrough"></a>影片逐步解說

以下是本教學課程的逐步解說。

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a name="<a-id="createvirtualmachine">-</a>create-the-virtual-machine"></a><a id="createvirtualmachine"> </a>建立虛擬機器

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>後續步驟

- 登入虛擬機器。 如需相關指示，請參閱 [登入執行 Windows Server 的虛擬機器](virtual-machines-windows-classic-connect-logon.md)。

- 附加磁碟來儲存資料。 您可以附加空的磁碟和含有資料的磁碟。 如需相關指示，請參閱 [將資料磁碟連接至以傳統部署模型建立的 Windows 虛擬機器](virtual-machines-windows-classic-attach-disk.md)。



<!--HONumber=Oct16_HO2-->


