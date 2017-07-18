---
title: "安裝和設定 Terraform 以在 Azure 中佈建 VM 和的其他基礎結構 | Microsoft Docs"
description: "了解如何安裝和設定 Terraform 以便建立 Azure 資源"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: aa82dda778af927db0a204eb88bae445c086e309
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>安裝和設定 Terraform 以在 Azure 中佈建 VM 和的其他基礎結構 
本文詳細說明安裝和設定 Terraform 以在 Azure 中佈建資源 (例如虛擬機器) 的必要步驟。 您將了解如何建立及使用 Azure 認證來啟用 Terraform，進而以安全的方式佈建雲端資源。

HashiCorp Terraform 使用稱為 HCL 的自訂範本化語言，提供簡單的方法來定義及部署雲端基礎結構。 此自訂語言[容易撰寫且易於了解](terraform-create-complete-vm.md)。 此外，透過 "terraform plan" 命令，Terraform 可讓您在認可對基礎結構所做的變更之前將其視覺化。 請遵循下列步驟，開始使用 Terraform 搭配 Azure。

## <a name="installing-terraform"></a>安裝 Terraform
若要安裝 Terraform，請適合您 OS 的套件[下載](https://www.terraform.io/downloads.html)到個別的安裝目錄中。 此下載包含單一可執行檔，您也應該為其定義全域 PATH。 在[本頁](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)可以找到在 Linux 和 Mac 上設定 PATH 的指示，然而[本頁](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)也包含在 Windows 上設定 PATH 的指示。 執行 "terraform" 命令來驗證您的安裝 - 您應會看到可用的 Terraform 選項清單作為輸出。

接著，您必須允許 Terraform 存取您 Azure 訂用帳戶以執行基礎結構佈建。

## <a name="setting-up-terraform-access-to-azure"></a>設定 Terraform 對 Azure 的 存取權
若要讓 Terraform 能夠將資源佈建至 Azure，您必須在 Azure Active Directory (AAD) 中建立兩個實體 - AAD 應用程式和 AAD 服務主體。 然後，您可以在 Terraform 指令碼中使用這些實體的識別碼。 服務主體是全域 AAD 應用程式的本機執行個體。 擁有服務主體，即可進行全域資源的細微本機存取控制。

有數種方式可建立 AAD 應用程式和 AAD 服務主體。 現今最簡單又最快的方式是使用 Azure CLI 2.0，[您可以在 Windows/Linux/Mac 上進行下載和安裝](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)。 您也可以使用 Powershell 或 Azure CLI 1.0 來建立必要的安全性基礎結構。 下列指示說明如何設定 Terraform 以便 Azure 使用這些方法。

### <a name="windowslinuxmac-users-using-azure-cli-20"></a>使用 Azure CLI 2.0 的 Windows/Linux/Mac 使用者
下載並安裝 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) 之後，請發出下列命令來登入以管理您的 Azure 訂用帳戶。

```
az login
```

>[!NOTE]
>如果您使用 China、German 或 Government Azure Cloud，您需要先設定 Azure CLI 才能使用該雲端。 您可以執行下列命令來達成此目的：

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

如果您有多個 Azure 訂用帳戶，`az login` 命令會傳回其詳細資料。 設定 `SUBSCRIPTION_ID` 環境變數，以保存從您要使用之訂用帳戶傳回的 `id` 欄位值。 

為此區段設定您要使用的訂用帳戶。

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

查詢帳戶，以取得訂用帳戶識別碼和租用戶識別碼值。

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

接下來，為 Terraform 建立不同的認證。

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

這會輸出 client_id、client_secret (密碼)、sp_name 和 tenant。 請記下 **client_id** 和 **client_secret**。

開啟新的殼層及執行下列命令，並以傳回的值替代 **sp_name**、**client_secret** 和 **tenant**，即可確認您的認證 (服務主體)：

```
az login --service-principal -u SP_NAME -p CLIENT_SECRET --tenant TENANT
az vm list-sizes --location westus
```

### <a name="windows-users-using-powershell"></a>使用 PowerShell 的 Windows 使用者
如果您使用 Windows 電腦來撰寫和執行您的 Terraform 指令碼，且偏好使用 Powershell 進行設定工作，您需要使用適當的 Powershell 工具來設定您的電腦。 若要這樣做，請 (1)[安裝 Azure PowerShell 工具](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)和 (2) 從 Powershell 主控台下載並執行 [azure-setup.ps1 指令碼](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1)。 若要執行 azure-setup.ps1 指令碼，請加以下載，從 Powershell 主控台執行 "./azure-setup.ps1 setup" 命令，並以系統管理權限登入您的 Azure 訂用帳戶。 然後，在系統提示時提供應用程式名稱 (任意字串，必要) 且 (選擇性) 提供強式密碼。 如果您未提供密碼，則會使用 .Net 安全性程式庫為您產生強式密碼。

### <a name="linuxmac-users-using-azure-cli-10"></a>使用 Azure CLI 1.0 的 Linux/Mac 使用者
若要在採用 Azure CLI 1.0 的 Linux 機器或 Mac 上開始使用 Terraform，您必須確保在您的電腦上安裝適當的程式庫。 若要這樣做，請 (1) [安裝 Azure xPlat CLI 工具](https://docs.microsoft.com/cli/azure/install-azure-cli)、(2) [下載並安裝 jq ](https://stedolan.github.io/jq/download/) JSON 處理器，以及 (3) 從主控台下載並執行 [azure-setup.sh 指令碼](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) bash 指令碼。 若要執行 azure-setup.sh 指令碼，請加以下載，從主控台執行 "./azure-setup setup" 命令，並以系統管理權限登入您的 Azure 訂用帳戶。 然後，在系統提示時提供應用程式名稱 (任意字串，必要)，並在系統提示時 (選擇性) 提供強式密碼。 如果您未提供密碼，則會使用 .Net 安全性程式庫為您產生強式密碼。

上述所有指令碼會建立 AAD 應用程式和服務主體，並為服務主體提供訂用帳戶的參與者或擁有者層級存取權。 因為授與高階存取權，您應該一律保護這些指令碼所產生的安全性資訊。 記下這些指令碼所提供之安全性資訊的四大部分：client_id、client_secret、subscription_id 和 tenant_id。 

## <a name="setting-environment-variables"></a>設定環境變數
建立並設定 AAD 服務主體後，您需要讓 Terraform 知道所要使用的租用戶識別碼、訂用帳戶識別碼、用戶端識別碼和用戶端祕密。 在 Terraform 指令碼中內嵌這些值，即可執行此作業 (如[下一節](terraform-create-complete-vm.md)所述)。 或者，您可以也設定下列環境變數 (並因而避免不小心簽入/共用您的認證)：

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

以下是可用來設定這些變數的範例殼層指令碼：

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_client_id
export ARM_CLIENT_SECRET=your_client_secret
export ARM_TENANT_ID=your_tenant_id
```

此外，如果您正在使用 Terraform 搭配 Azure Government、Azure Germany 或 Azure China，您需要適當地設定環境變數。

## <a name="next-steps"></a>後續步驟
您現在已安裝 Terraform 並設定 Azure 認證，可以開始將基礎結構部署到您的 Azure 訂用帳戶中。 接下來，深入了解如何[使用 Terraform 建立基礎結構](terraform-create-complete-vm.md)。
