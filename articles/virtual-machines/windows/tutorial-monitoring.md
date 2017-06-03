---
title: "Azure 監視器和 Windows 虛擬機器 | Microsoft Docs"
description: "教學課程 - 使用 Azure PowerShell 監視 Windows 虛擬機器"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 2bf6e3cf2f039a9c92617203a3d4cf2aac8901ce
ms.contentlocale: zh-tw
ms.lasthandoff: 05/09/2017

---

# <a name="monitor-a-windows-virtual-machine-with-azure-powershell"></a>使用 Azure PowerShell 監視 Windows 虛擬機器

Azure 監視器使用代理程式從 Azure VM 收集開機和效能資料，將此資料儲存在 Azure 儲存體，並讓資料可透過入口網站、Azure PowerShell 模組和 Azure CLI 存取。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 啟用 VM 上的開機診斷
> * 檢視開機診斷
> * 檢視 VM 主機計量
> * 安裝診斷擴充功能
> * 檢視 VM 計量
> * 建立警示
> * 設定進階監視

本教學課程需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 ` Get-Module -ListAvailable AzureRM` 找出版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

若要完成本教學課程中的範例，您目前必須具有虛擬機器。 如有需要，這個[指令碼範例](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)可以為您建立一部虛擬機器。 逐步完成教學課程之後，請視需要取代資源群組、VM 名稱、位置。

## <a name="view-boot-diagnostics"></a>檢視開機診斷

Windows 虛擬機器開機時，開機診斷代理程式會擷取可用於疑難排解的螢幕輸出。 此功能預設為啟用狀態。 擷取的螢幕畫面會儲存在 Azure 儲存體帳戶，這也是預設會建立的帳戶。 

您可以使用 [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) 命令取得開機診斷資料。 在下列範例中，開機診斷會下載到 * c:\* 磁碟機的根目錄。 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>檢視主機計量

在 Azure 中有 Windows VM 專用的主機 VM 與它互動。 系統會自動收集主機的計量，而且可以在 Azure 入口網站中檢視這些計量。

1. 在 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroup]，然後選取資源清單中的 [myVM]。
2. 按一下 VM 刀鋒視窗上的 [計量]，然後選取 [可用的計量] 下的任何 [主機] 計量以查看主機 VM 的執行狀況。

    ![檢視主機計量](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>安裝診斷擴充功能

系統提供基本的主機計量，但若要查看更細微或 VM 特定的計量，則需要在 VM 上安裝 Azure 診斷的擴充功能。 Azure 診斷擴充功能可額外提供從 VM 擷取的監視和診斷資料。 您可以檢視這些效能計量，並依據 VM 的執行狀況建立警示。 診斷擴充功能可透過 Azure 入口網站安裝，如下所示︰

1. 在 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroup]，然後選取資源清單中的 [myVM]。
2. 按一下 [診斷設定]。 清單會顯示在上一節已啟用開機診斷。 按一下 [基本計量] 的核取方塊。
3. 按一下 [啟用來賓層級監視] 按鈕。

    ![檢視診斷計量](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>檢視 VM 計量

您可以檢視 VM 計量，和您檢視主機 VM 計量資訊的方式相同︰

1. 在 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroup]，然後選取資源清單中的 [myVM]。
2. 若要查看 VM 的執行狀況，按一下 VM 刀鋒視窗上的 [計量]，然後選取 [可用的計量] 下的任何診斷計量。

    ![檢視 VM 計量](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>建立警示

您可以根據特定效能計量來建立警示。 舉例來說，可使用警示來通知您平均 CPU 使用量超過特定臨界值、或是可用磁碟空間降到低於某個量。 警示會顯示在 Azure 入口網站，或是可以透過電子郵件傳送。 您也可以觸發 Azure 自動化 Runbook 或 Azure Logic Apps 以回應產生的警示。

以下範例會建立平均 CPU 使用量的警示。

1. 在 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroup]，然後選取資源清單中的 [myVM]。
2. 按一下VM 刀鋒視窗中的 [警示規則]，按一下橫跨在警示刀鋒視窗上方的 [新增計量警示]。
4. 提供警示的 [名稱]，例如 myAlertRule。
5. 若要在 CPU 百分比連續五分鐘超過 1.0 時觸發警示，保留所有其他已選取的預設值。
6. (選擇性) 選取 [電子郵件的擁有者、參與者及讀者] 核取方塊以傳送電子郵件通知。 預設動作是在入口網站中顯示通知。
7. 按一下 [確定] 按鈕。

## <a name="advanced-monitoring"></a>進階監視 

您可以使用 [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) (OMS) 對您的 VM 進行更進階的監視。 如果您尚未這麼做，可以註冊 Operations Management Suite 的[免費試用版](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial)。

當您可以存取 OMS 入口網站時，可以在 [設定] 刀鋒視窗中找到工作區金鑰和工作區識別碼。 使用 [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) 命令將 OMS 擴充功能新增到 VM。 更新以下範例中的變數值，以反映您的 OMS 工作區金鑰和工作區識別碼。  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

幾分鐘後，您應該會在 OMS 工作區中看到新的 VM。 

![OMS 刀鋒視窗](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>後續步驟
在本教學課程中，您利用 Azure 資訊安全中心設定並檢閱 VM。 您已了解如何︰

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立資源群組和 VM 
> * 啟用 VM 上的開機診斷
> * 檢視開機診斷
> * 檢視主機計量
> * 安裝診斷擴充功能
> * 檢視 VM 計量
> * 建立警示
> * 設定進階監視

請前進到下一個教學課程，了解 Azure 資訊安全中心。

> [!div class="nextstepaction"]
> [管理 VM 安全性](./tutorial-azure-security.md)
