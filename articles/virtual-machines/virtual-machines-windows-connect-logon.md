<properties
	pageTitle="連接到 Windows Server VM | Microsoft Azure"
	description="了解如何使用 Azure 入口網站和資源管理員部署模型來連接和登入 Windows Server VM。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/28/2016"
	ms.author="cynthn"/>

# 如何連接和登入執行 Windows Server 的 Azure 虛擬機器 


您會使用 Azure 入口網站中的 [連線] 按鈕啟動遠端桌面 (RDP) 工作階段。首先您要連接至虛擬機器，然後登入。

## 連接至虛擬機器

1. 如果您尚未登入 [Azure 入口網站](https://portal.azure.com/)，請先登入。

2.	在 [中樞] 功能表上，按一下 [虛擬機器]。

3.	然後從清單中選取虛擬機器。

4. 在虛擬機器的刀鋒視窗中，按一下 [**連線**]。

	![顯示如何連接至 VM 的 Azure 入口網站螢幕擷取畫面。](./media/virtual-machines-windows-connect-logon/connect.png)

## 登入虛擬機器

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]


## 後續步驟

如果嘗試連線時遇到問題，請參閱[針對執行 Windows 之 Azure 虛擬機器的遠端桌面連線進行疑難排解](virtual-machines-windows-troubleshoot-rdp-connection.md)。本文會逐步帶領您診斷及解決常見的問題。

<!---HONumber=AcomDC_0504_2016-->