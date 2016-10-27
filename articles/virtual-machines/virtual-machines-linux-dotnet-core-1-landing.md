<properties
   pageTitle="Azure 虛擬機器 DotNet 核心教學課程 1 | Microsoft Azure"
   description="Azure 虛擬機器 DotNet 核心教學課程"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>


# <a name="automating-application-deployments-to-azure-virtual-machines"></a>將應用程式自動部署到 Azure 虛擬機器

這個系列包含四個部分，詳細說明了如何使用 Azure Resource Manager 範本來部署及設定 Azure 資源和應用程式。 在這個系列中，會部署一個範例範本，並檢查部署範本。 這個系列的目標是要教導 Azure 資源之間的關聯性，並提供部署完全整合之 Azure Resource Manager 範本的實際操作體驗。 本文件假設您對 Azure Resource Manager 有基本程度的認識，開始進行本教學課程之前，請讓自己熟悉 Azure Resource Manager 的基本概念。

## <a name="music-store-application"></a>音樂市集應用程式

這個系列中所使用的範例是模擬「音樂市集」購物體驗的 .Net 核心應用程式。 此應用程式可以部署到 Linux 或 Windows 虛擬系統上，針對這兩者都已建立範例部署。 此應用程式包含一個 Web 應用程式和一個 SQL Database。 開始閱讀此系列的文章之前，請先使用在此頁面上找到 [部署] 按鈕來部署應用程式。 已完整部署時，應用程式/Azure 架構看起來會像下圖。 

您可以在[音樂市集 Linux 範本]( https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db)這裡找到音樂市集 Resource Manager 範本

![音樂市集應用程式](./media/virtual-machines-linux-dotnet-core/music-store.png)

在下列四篇文章中將會檢查這當中的每個元件 (包括關聯的 JSON 範本)。

- [**應用程式架構**](./virtual-machines-linux-dotnet-core-2-architecture.md) – 應用程式元件 (例如網站和資料庫) 必須裝載在 Azure 電腦資源 (例如虛擬機器和 Azure SQL Database) 上。 此文件將逐步解說如何將計算需求與 Azure 資源對應，以及如何使用 Azure Resource Manager 範本來部署這些資源。 

- [**存取和安全性**](./virtual-machines-linux-dotnet-core-3-access-security.md) – 將應用程式裝載在 Azure 中時，必須考量如何存取應用程式，以及不同的應用程式元件如何存取彼此。 此文件詳細說明如何提供和保護對應用程式的網際網路存取，以及應用程式元件之間的存取。

- [**可用性和規模**](./virtual-machines-linux-dotnet-core-4-avalibility-scale.md) – 可用性和規模係指可在基礎結構停機時保持執行的能力，以及可調整計算資源來滿足應用程式需求的能力。 此文件詳細說明部署負載平衡且高可用性應用程式所需的元件。

- [**應用程式部署**](./virtual-machines-linux-dotnet-core-5-app-deployment.md) - 將應用程式部署到「Azure 虛擬機器」時，必須考量將應用程式二進位檔安裝在「虛擬機器」上的方法。 此文件詳細說明如何使用「Azure 虛擬機器自訂指令碼擴充功能」來自動安裝應用程式。

開發 Azure Resource Manager 範本時，目標是要自動部署 Azure 基礎結構，以及自動安裝及設定裝載在此 Azure 基礎結構上的任何應用程式。 仔細閱讀這些文章將可獲得這項體驗的範例解說。

## <a name="deploy-the-music-store-application"></a>部署音樂市集應用程式

您可以使用此按鈕來部署「音樂市集」應用程式。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-linux%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Azure Resource Manager 範本需要下列參數值。

|參數名稱 |說明   |
|---|---|
|SSHKEYDATA   | 用來保護對「虛擬機器」之存取的 SSH 金鑰資料。 如需有關建立 SSH 金鑰組的資訊，請參閱[在 Azure 中為 Linux VM 建立 SSH 金鑰](virtual-machines-linux-mac-create-ssh-keys.md)。  |
|ADMINUSERNAME   | 在虛擬機器和 Azure SQL Database 上使用的系統管理員使用者名稱。  |
|SQLADMINPASSWORD | 在 Azure SQL Database 上使用的密碼。  |
|NUMBEROFINSTANCES | 要建立的虛擬機器數目。 這當中的每一部虛擬機器都會裝載「音樂市集」Web 應用程式，而且系統會平衡它們之間的所有流量負載。 |
|PUBLICIPADDRESSDNSNAME | 與公用 IP 位址關聯的全域唯一 DNS 名稱。 |

範本部署完成時，請使用任何網際網路瀏覽器來瀏覽至公用 IP 位址。 將會顯示 .Net Core 音樂網站。

## <a name="next-steps"></a>後續步驟

<hr>

[步驟 1 - 使用 Azure Resource Manager 範本的應用程式架構](./virtual-machines-linux-dotnet-core-2-architecture.md)

[步驟 2 - Azure Resource Manager 範本中的存取和安全性](./virtual-machines-linux-dotnet-core-3-access-security.md)

[步驟 3 - Azure Resource Manager 範本中的可用性和規模](./virtual-machines-linux-dotnet-core-4-avalibility-scale.md)

[步驟 4 - 使用 Azure Resource Manager 範本進行應用程式部署](./virtual-machines-linux-dotnet-core-5-app-deployment.md)





<!--HONumber=Oct16_HO2-->


