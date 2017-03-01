---
title: "使用 Visual Studio 連接到 Azure Government | Microsoft Docs"
description: "透過 Visual Studio 連接，在 Azure Government 中管理訂用帳戶的相關資訊"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: faf269aa-e879-4b0e-a5ba-d4110684616a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: d49b2c6afc48cda01045cddb3844c5234e72de37
ms.lasthandoff: 02/21/2017


---


# <a name="connecting-via-visual-studio"></a>透過 Visual Studio 連線
Visual Studio 可供開發人員在建置解決方案時輕鬆地管理其 Azure 訂用帳戶。  Visual Studio 目前不允許您在使用者介面中設定連往 Azure Government 的連線。  

### <a name="updating-visual-studio-for-azure-government"></a>更新 Azure Government 的 Visual Studio
若要讓 Visual Studio 能夠連線至 Azure Government，您需要更新登錄。

1. 關閉 Visual Studio
2. 建立名為 **VisualStudioForAzureGov.reg** 的文字檔
3. 複製下列文字並貼到 **VisualStudioForAzureGov.reg**：
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. 儲存檔案，然後按兩下以執行檔案。  系統會提示您將檔案合併到您的登錄。
5. 啟動 Visual Studio，並開始使用[雲端總管](../vs-azure-tools-resources-managing-with-cloud-explorer.md)

> [!NOTE]
> 設定此登錄機碼之後，就只能存取 Azure Government 訂用帳戶。  您仍然可以看到之前設定的訂用帳戶，但它們無法運作，因為 Visual Studio 現在是連線到 Azure Government 而不是 Azure 公用。  請參閱下一節，以取得用來還原變更的步驟。
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>還原 Visual Studio 對 Azure Government 的連線
若要讓 Visual Studio 能夠連線到 Azure 公用，您需要移除啟用 Azure Government 連線的登錄設定。

1. 關閉 Visual Studio
2. 建立名為 **VisualStudioForAzureGov_Remove.reg** 的文字檔
3. 複製下列文字並貼到 **VisualStudioForAzureGov_Remove.reg**：
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. 儲存檔案，然後按兩下以執行檔案。  系統會提示您將檔案合併到您的登錄。
5. 啟動 Visual Studio

> [!NOTE]
> 將此登錄機碼還原之後，Azure Government 訂用帳戶會顯示，但無法加以存取。  您可以放心地移除它們。
> 
> 

