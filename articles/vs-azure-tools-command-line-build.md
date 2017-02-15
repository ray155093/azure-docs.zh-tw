---
title: "Azure 的命令列建置 | Microsoft Docs"
description: "Azure 的命令列建置"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e189bf8241266443bee4dbbb5faf1cffa983bdb4


---
# <a name="command-line-build-for-azure"></a>Azure 的命令列建置
## <a name="overview"></a>Overview
您可以藉由在命令提示字元中執行 MSBuild 來建立 Azure 部署的封裝。 除了自動化部份的組建程序，您還可以設定及定義進行偵錯、預備及實際執行的組建。

## <a name="microsoft-build-engine-msbuild"></a>Microsoft Build Engine (MSBuild)
藉由使用 Microsoft Build Engine (MSBuild)，您可以在未安裝 Visual Studio 的組建實驗室環境中建置產品。 MSBuild 的專案檔案會採用可擴充且 Microsoft 完全支援的 XML 格式。 在這種檔案格式中，您可以說明針對一或多個平台和組態所必須建置的項目。

您也可以在命令提示字元中執行 MSBuild，本主題將說明這個方法。 藉由在命令提示字元中設定屬性，您便可以建置專案的特定組態。 同樣地，您也可以定義 MSBuild 命令將會建置的目標。 如需命令列參數及 MSBuild 的詳細資訊，請參閱 [MSBuild 命令列參考](https://msdn.microsoft.com/library/ms164311.aspx)。

## <a name="installation"></a>安裝
如下列程序所述，您必須先在組建伺服器上安裝軟體與工具，才能使用 MSBuild 建立 Azure 封裝。

1. 安裝 .NET Framework 4 或更新版本 (包含 MSBuild)。
2. 安裝 [Azure 製作工具](http://go.microsoft.com/fwlink/?LinkId=394615) (搜尋 MicrosoftAzureAuthoringTools-x64.msi 或 MicrosoftAzureAuthoringTools-x86.msi)。
3. 安裝 [Azure Libraries for .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (搜尋 MicrosoftAzureLibsForNet-x64.msi 或 MicrosoftAzureLibs-x86.msi)。
4. 複製另一部電腦上 Visual Studio 安裝的 Microsoft.WebApplication.targets 檔案。
   
    此檔案會位於目錄 C:\Program Files (x86) \MSBuild\Microsoft\Visual Studio\v12.0\WebApplications (v11.0 for Visual Studio 2012) 中，您應該將它複製到組建伺服器上的相同目錄。
5. 安裝 [Azure Tools for Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616)。
   
    搜尋 WindowsAzureTools.vs120.exe 來建置 Visual Studio 2013 專案。

## <a name="msbuild-parameters"></a>MSBuild 參數
若要建立封裝，最簡單的方法是使用 `/t:Publish` 選項來執行 MSBuild。 根據預設，此命令會建立與專案的根資料夾相關的目錄，例如 ProjectDir\bin\Configuration\app.publish 根資料夾的目錄\. 當您建置 Azure 專案時，會產生兩個檔案，即套件檔本身及伴隨的組態檔：

* Project.cspkg
* ServiceConfiguration.TargetProfile.cscfg

依預設，每個 Azure 專案都會包含一個供本機 (偵錯) 組建使用的服務組態檔，和另一個供雲端 (預備或生產) 組建使用的服務組態檔，但是您可以視需要新增或移除服務組態檔。 在 Visual Studio 內建置封裝時，系統將會詢問您要在封裝中加入哪個服務組態檔。 使用 MSBuild 來建置封裝時，依預設會加入本機服務組態檔。 若要加入不同的服務組態檔，請設定 `TargetProfile`MSBuild 命令的屬性 (`MSBuild /t:Publish /p:TargetProfile=ProfileName`)。

如果您想要為儲存的封裝和組態檔使用替代目錄，請使用 `/p:PublishDir=Directory\` 選項來設定路徑 (包括結尾的反斜線分隔符號)。

## <a name="deployment"></a>部署
建立封裝之後，您可以將它部署至 Azure。 如需示範該程序的教學課程，請參閱 Azure 網站。 如需如何自動化該程序的相關資訊，請參閱 [Azure 中雲端服務的連續傳遞](cloud-services/cloud-services-dotnet-continuous-delivery.md)。




<!--HONumber=Nov16_HO3-->


