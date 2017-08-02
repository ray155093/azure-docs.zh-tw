---
title: "傳統 Windows VM 的技術文章 | Microsoft Azure"
description: "傳統部署模型中 Windows 虛擬機器的 Microsoft Azure 說明文件文章完整清單"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-service-management
editor: 
ms.assetid: 7f9a508b-34ec-4bdb-92d1-8844480369d5
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 5c971ed2e09bd275dc34da3ea94b3ff4a6338c9a
ms.contentlocale: zh-tw
ms.lasthandoff: 07/12/2017

---
# <a name="technical-articles-for-windows-vms-in-the-classic-deployment-model"></a>傳統部署模型中的 Windows VM 的技術文章
尋找在傳統部署模型中建立和管理以 Windows 為基礎的 Azure 虛擬機器所需的所有說明文件。

> [!IMPORTANT] 
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。

## <a name="overview"></a>概觀
[關於虛擬機器](windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[關於以傳統部署模型建立之 Azure 虛擬機器的常見問題集](windows/classic/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[比較 VM、網站及雲端服務](../app-service-web/choose-web-site-cloud-service-vm.md)

[Azure 中的虛擬機器和容器](windows/containers.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="environment-setup"></a>環境設定
[免費帳戶](https://azure.microsoft.com/free/)

[安裝 Azure PowerShell](/powershell/azure/overview)

[安裝 Azure CLI](../cli-install-nodejs.md)

## <a name="get-started"></a>開始使用
[Windows VM 的學習路徑](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)

[在 Azure 傳統入口網站中建立 Windows 虛擬機器](windows/classic/tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何登入執行 Windows Server 的傳統虛擬機器](windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="plan"></a>規劃
[關於傳統虛擬機器的映像](windows/classic/about-images.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[虛擬機器的大小](windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[高效能運算 VM 大小](windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Azure 虛擬機器的計劃性維護](windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Azure 基礎結構服務實作指導方針](windows/infrastructure-subscription-accounts-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[建立虛擬機器的可用性設定組](windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="deploy"></a>部署
[建立一個執行 Windows 的自訂虛擬機器](windows/classic/createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[擷取以傳統部署模型建立的 Windows 虛擬機器](windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[使用 PowerShell 建立並上傳傳統 Windows Server VHD](windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[使用 Chef 自動化 Azure 虛擬機器部署](windows/chef-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[在 Azure PowerShell 中建立及設定傳統 Windows 虛擬機器](windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[將自訂資料插入 Azure 虛擬機器](windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="manage"></a>管理
[使用 Azure PowerShell 管理您的虛擬機器](windows/classic/manage-psh.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[將傳統 VNET 連接至新的 VNET](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)

[有關虛擬機器代理程式和擴充功能](windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[管理虛擬機器擴充功能](windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[傳統 Windows 虛擬機器的自訂指令碼擴充功能](windows/classic/extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[平台支援的從傳統移轉至 Azure Resource Manager](windows/migration-classic-resource-manager-deep-dive.md)

## <a name="configure"></a>設定
[如何重設 Windows VM 的密碼或遠端桌面服務](windows/reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[有關虛擬機器擴充功能和功能](windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[如何在 Windows VM 上安裝和設定 Symantec Endpoint Protection](windows/classic/install-symantec.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何在 Windows VM 上安裝和設定 Trend Micro Deep Security as a Service](windows/classic/install-trend.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何以傳統部署模型設定虛擬機器的可用性設定組](windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何在傳統的 Azure 虛擬機器上設定端點](windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="storage"></a>儲存體
[有關 Azure 虛擬機器的磁碟和 VHD](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[如何將資料磁碟連接至傳統 Windows 虛擬機器](windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何從傳統 Windows 虛擬機器卸離資料磁碟](windows/classic/detach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[使用 D 磁碟機做為 Windows VM 上的資料磁碟機](windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="networking"></a>網路
[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)

[連接藉由虛擬網路或雲端服務，以傳統部署模型建立的虛擬機器](windows/classic/connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[使用 Azure PowerShell 管理 NSG](../virtual-network/virtual-networks-create-nsg-classic-ps.md)

[建立負載平衡器](../load-balancer/load-balancer-get-started-internet-classic-portal.md)

## <a name="develop"></a>開發
[在 Visual Studio 中建立和管理 Azure 虛擬機器](windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[使用 Visual Studio 建立 Web 應用程式的虛擬機器](windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何在虛擬機器上以 Java 執行大量運算工作](windows/classic/java-run-compute-intensive-task.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Windows Server VM 上的 Django Hello World Web 應用程式](windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="workloads"></a>工作負載
[HPC Pack](windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[MongoDB](windows/classic/install-mongodb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[MySQL](windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Oracle](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)

[SAP](windows/classic/sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[SQL Server](./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[Tomcat](windows/classic/java-run-tomcat-app-server.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="reference"></a>參考
[服務管理模式中的 Azure CLI 命令](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)

[服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[服務管理 .NET API](https://msdn.microsoft.com/library/azure/mt420161.aspx)

[Azure 服務管理 PowerShell Cmdlet 參考文件](/powershell/azure/overview?view=azuresmps-3.7.0)

## <a name="troubleshooting"></a>疑難排解
[疑難排解執行 Windows 之 Azure 虛擬機器的遠端桌面連線](windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[疑難排解在 Azure 虛擬機器上執行的應用程式存取](windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[在 Azure 中建立、重新啟動或調整 VM 大小時，對配置失敗進行疑難排解](windows/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[針對在 Azure 中建立新 Windows 虛擬機器的傳統部署問題進行疑難排解](windows/classic/troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[針對在 Azure 中重新啟動或調整現有 Windows 虛擬機器大小的傳統部署問題進行疑難排解](windows/classic/virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)

