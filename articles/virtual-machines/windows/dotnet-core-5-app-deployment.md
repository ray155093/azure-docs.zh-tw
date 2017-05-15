---
title: "使用虛擬機器擴充功能將應用程式部署自動化 | Microsoft Docs"
description: "Azure 虛擬機器 DotNet 核心教學課程"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 79c91304-6c1b-4354-a185-fecc129b139d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 2d1860a16405646452f6448c26bf995473ac04b3
ms.contentlocale: zh-tw
ms.lasthandoff: 03/31/2017


---
# <a name="application-deployment-with-azure-resource-manager-templates-for-windows-vms"></a>使用適用於 Windows VM 的 Azure Resource Manager 範本進行應用程式部署

識別出所有 Azure 基礎結構需求並轉譯成部署範本之後，就必須處理實際的應用程式部署需求。 這裡的應用程式部署係指將實際的應用程式二進位檔安裝到 Azure 資源上。 以「音樂市集」範例來說，必須在每一部虛擬機器上安裝並設定 .Net 核心和 IIS。 必須將「音樂市集」二進位檔安裝到虛擬機器上，並且必須預先建立「音樂市集」資料庫。

本文件詳細說明「虛擬機器」擴充功能如何將 Azure 虛擬機器的應用程式部署和設定自動化。 所有相依項目和獨特的設定都會以醒目提示的方式標示。 為了獲得最佳體驗，請將一個解決方案執行個體預先部署到您的 Azure 訂用帳戶，然後與 Azure Resource Manager 範本搭配運作。 您可以在下列連結找到完整的範本 – [Windows 上的音樂市集部署](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)。

## <a name="configuration-script"></a>組態指令碼
「虛擬機器」擴充功能是針對虛擬機器執行以提供組態自動化的特製化程式。 針對許多特定用途 (防毒、記錄組態及 Docker 組態) 都有擴充功能可供使用。 自訂指令碼擴充功能可以用來對虛擬機器執行任何指令碼。 在「音樂市集」範例中，需仰賴自訂指令碼擴充功能來設定 Windows 虛擬機器及安裝「音樂市集」應用程式。

在詳細說明如何在 Azure Resource Manager 範本中宣告虛擬機器擴充功能之前，請先檢查所執行的指令碼。 此指令碼會設定 Windows 虛擬機器以裝載「音樂市集」應用程式。 在執行時，指令碼會安裝所有必要的軟體、從原始檔控制安裝「音樂市集」應用程式，以及準備資料庫。 

> 此範例僅供示範之用。

```PowerShell
<#
    .SYNOPSIS
        Downloads and configures .Net Core Music Store application sample across IIS and Azure SQL DB.
#>

Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# Firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# Folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# Install iis
Install-WindowsFeature web-server -IncludeManagementTools

# Install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# Download music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music

# Azure SQL connection sting in environment variable
[Environment]::SetEnvironmentVariable("Data:DefaultConnection:ConnectionString", "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30",[EnvironmentVariableTarget]::Machine)

# Pre-create database
$env:Data:DefaultConnection:ConnectionString = "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30"
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# Configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>VM 指令碼擴充功能
透過將擴充功能資源包含在 Azure Resource Manager 範本中，即可在建置階段對虛擬機器執行「VM 指令碼擴充功能」。 您可以使用 Visual Studio 的「加入新資源」精靈或在範本中插入有效的 JSON，來新增擴充功能。 「指令碼擴充功能」資源內嵌在「虛擬機器」資源的巢狀結構中；在下列範例中即可看到此情況。

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [VM 指令碼擴充功能](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339)。 

請注意，在下列 JSON 中，指令碼是儲存在 GitHub。 此指令碼也可以儲存在 Azure Blob 儲存體中。 此外，Azure Resource Manager 範本也允許建構指令碼執行字串，讓範本參數值可被用來當作指令碼執行參數。 在此案例中，是在部署範本時提供資料，接著在執行指令碼時，就可以使用這些值。

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

如上所述，它也能夠將自訂指令碼儲存於 Azure Blob 儲存體中。 用來將指令碼資源儲存於 Blob 儲存體中的選項有兩種：一種是讓容器/指令碼公開，然後遵循與上述相同的方式，另一種則是您也可以將它保留於私人 Blob 儲存體中，這會要求您提供 storageAccountName 和 storageAccountKey 給 CustomScriptExtension 資源定義。

在下列範例中，我們已向前邁進一步。 雖然您可以在部署期間提供儲存體帳戶名稱和金鑰做為參數或變數，但是，Resource Manager 範本會提供 `listKeys` 函式，可在部署階段以程式設計方式取得儲存體帳戶金鑰，並將它插入範本。

在下列 CustomScriptExtension 資源定義範例中，已經將我們的自訂指令碼上傳到稱為 `mystorageaccount9999` 的 Azure 儲存體帳戶中，此帳戶存在於另一個稱為 `mysa999rgname` 的資源群組中。 當我們部署包含此資源的範本時，`listKeys` 函式會以程式設計方式取得資源群組 `mysa999rgname` 中儲存體帳戶 `mystorageaccount9999` 的儲存體帳戶金鑰，並將它插入範本。

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://mystorageaccount9999.blob.core.windows.net/container/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]",
      "storageAccountName": "mystorageaccount9999",
      "storageAccountKey": "[listKeys(resourceId('mysa999rgname','Microsoft.Storage/storageAccounts', mystorageaccount9999), '2015-06-15').key1]"
    }
  }
}
```

這種方法的主要優點是，它不需要您在發生儲存體帳戶金鑰變更時，變更您的範本或部署參數。

如需有關使用自訂指令碼擴充功能的詳細資訊，請參閱 [使用 Resource Manager 範本來自訂指令碼擴充功能](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="next-step"></a>後續步驟
<hr>

[探索其他 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates)


