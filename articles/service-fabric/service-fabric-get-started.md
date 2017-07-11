---
title: "設定 Azure 微服務的開發環境 | Microsoft Docs"
description: "安裝執行階段、SDK 和工具，並建立本機開發叢集。 完成此設定之後，您就可以開始建置應用程式。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2017
ms.author: ryanwi, mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: f7479260c7c2e10f242b6d8e77170d4abe8634ac
ms.openlocfilehash: 926dfe3de0715f855e6d5b57f10c2366cda8583b
ms.contentlocale: zh-tw
ms.lasthandoff: 06/21/2017


---
<a id="prepare-your-development-environment" class="xliff"></a>

# 準備您的開發環境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 若要在您的開發機器上建置並執行 [Azure Service Fabric 應用程式][1]，請安裝執行階段、SDK 和工具。 您也必須執行 SDK 中包含的 Windows PowerShell 指令碼。

<a id="prerequisites" class="xliff"></a>

## 必要條件
<a id="supported-operating-system-versions" class="xliff"></a>

### 支援的作業系統版本
下列為支援開發的作業系統版本：

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> 根據預設，Windows 7 只包含 Windows PowerShell 2.0。 Service Fabric PowerShell Cmdlet 需要 PowerShell 3.0 或更新版本。 您可以從 Microsoft 下載中心[下載 Windows PowerShell 5.0][powershell5-download]。
> 
> 

<a id="install-the-sdk-and-tools" class="xliff"></a>

## 安裝 SDK 和工具
<a id="to-use-visual-studio-2017" class="xliff"></a>

### 若要使用 Visual Studio 2017
Service Fabric 工具屬於 Visual Studio 2017 中的 Azure 開發和管理工作負載。 啟用此工作負載作為 Visual Studio 安裝的一部分。
此外，您必須使用 Web Platform Installer 來安裝 Microsoft Azure Service Fabric SDK。

* [安裝 Microsoft Azure Service Fabric SDK][core-sdk]

<a id="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later" class="xliff"></a>

### 若要使用 Visual Studio 2015 (需要 Visual Studio 2015 Update 2 或更新版本)
在 Visual Studio 2015 中，使用 Web Platform Installer，Service Fabric 工具會與 SDK 一起安裝︰

* [安裝 Microsoft Azure Service Fabric SDK 和工具][full-bundle-vs2015]

<a id="sdk-installation-only" class="xliff"></a>

### 僅限 SDK 安裝
如果您只需要 SDK，您可以安裝此套件︰
* [安裝 Microsoft Azure Service Fabric SDK][core-sdk]

目前的版本如下︰
* Service Fabric SDK 2.6.220
* Service Fabric 執行階段 5.6.220
* Visual Studio 2015 工具 1.6.50508.2
* Visual Studio 2017 Update 2

目前的預覽版本如下︰
* Service Fabric SDK 255.255.2718.255
* Service Fabric 執行階段 255.255.5718.255
* Visual Studio 2015 工具 1.6.50509.5
* Visual Studio 2017 Update 3 預覽 1

如需支援版本的清單，請參閱[Service Fabric 支援](service-fabric-support.md)

<a id="enable-powershell-script-execution" class="xliff"></a>

## 啟用 PowerShell 指令碼執行
Service Fabric 會使用 Windows PowerShell 指令碼，以便建立本機開發叢集，以及從 Visual Studio 部署應用程式。 根據預設，Windows 會封鎖這些指令碼的執行。 若要啟用其，您必須修改 PowerShell 執行原則。 以系統管理員身分開啟 PowerShell 並輸入下列命令：

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

<a id="next-steps" class="xliff"></a>

## 後續步驟
現在您的開發環境已完成設定，您可以開始建置和執行應用程式。

* [在 Visual Studio 中建立第一個 Service Fabric 應用程式](service-fabric-create-your-first-application-in-visual-studio.md)
* [了解如何在本機叢集上部署和管理應用程式](service-fabric-get-started-with-a-local-cluster.md)
* [深入了解程式設計模型：Reliable Services 和 Reliable Actors](service-fabric-choose-framework.md)
* [請查看 GitHub 上的 Service Fabric 程式碼範例](https://aka.ms/servicefabricsamples)
* [使用 Service Fabric 總管將叢集視覺化](service-fabric-visualizing-your-cluster.md)
* [遵循 Service Fabric 學習路徑來取得廣泛的平台簡介](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* 了解 [Service Fabric 支援選項](service-fabric-support.md)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Service Fabric 活動頁面"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI 連結"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 連結"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI 連結"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395

