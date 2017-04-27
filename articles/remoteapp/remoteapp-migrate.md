---
title: "從 Azure RemoteApp 移轉使用者資料 | Microsoft Docs"
description: "了解如何將使用者資料轉入或轉出 Azure RemoteApp。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d7e4fbf1-cb42-4430-94a0-ed6d4676fc86
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 39dd726078c4dcc55063300bdca998ad822feadd
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>如何將資料轉入或轉出 Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp 即將於 2017 年 8 月 31 日停止服務。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
> 
> 

您可以使用許多不同的工具和方法，將 [使用者資料](remoteapp-upd.md) 傳入和傳出 Azure RemoteApp。 以下是幾個方法︰

* 使用剪貼簿共用複製並貼上
* 將檔案和資料複製到檔案伺服器
* 透過瀏覽器，將檔案複製到商務用 OneDrive
* 使用重新導向複製檔案

> [!NOTE]
> 您無法啟用商務用 OneDrive 或消費者同步處理代理程式 - Azure RemoteApp 中 [不支援](remoteapp-onedrive.md) 。
> 
> 

## <a name="use-copy-and-paste-in-file-explorer"></a>在 [檔案總管] 中使用複製及貼上
[依預設](remoteapp-redirection.md)，RemoteApp 部署中已啟用使用剪貼簿複製和貼上。 這可讓使用者在他們的本機電腦和 RemoteApp 應用程式之間複製檔案。 通常，透過在 RemoteApp 中使用應用程式的正常過程，使用者已將檔案儲存到他們的 UPD - 將該資料移出 RemoteApp 很容易：

1. 在 RemoteApp 集合中[將 [檔案總管] 發佈為應用程式](remoteapp-publish.md)。 (請注意，這是系統管理工作。)
2. 將使用者導向至啟動您所發佈的 [檔案總管] 應用程式，並使用它來從其 UPD 複製與貼上檔案。

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>透過使用標準網路檔案複製將檔案與資料上傳至檔案伺服器
通常組織會使用檔案伺服器儲存一般資料。 如果您知道伺服器名稱或位置，您的使用者可以瀏覽伺服器的區域網路，並複製他們的檔案，類似他們上述的作法。 同樣的，您會希望將 [檔案總管] 發佈至 RemoteApp，然後與您的使用者共用。

> [!NOTE]
> 檔案伺服器必須位在部署 RemoteApp 的可路由網路上。
> 
> 

## <a name="copy-files-to-onedrive-for-business"></a>將檔案複製到商務用 OneDrive
雖然您無法在 RemoteApp 中啟用商務用 OneDrive 同步代理程式，您仍然可以透過瀏覽器將檔案從您的 UPD 複製到商務用 OneDrive。 

1. 將 [檔案總管] 發佈至 RemoteApp，然後告知使用者透過該應用程式存取檔案。 
2. 如果檔案已壓縮會更容易傳輸，因此使用者應該建立一個包含所有檔案的 .zip 檔案，以移動至商務用 OneDrive。
3. 要求使用者移至 Office 365 入口網站，然後移至 OneDrive 並上傳該 .zip 檔案。

## <a name="copy-files-by-using-drive-redirection"></a>使用磁碟重新導向複製檔案
如果您已啟用 [磁碟重新導向](remoteapp-redirection.md)，您已為您的使用者建立對應磁碟機。 在此情況下，他們可以在已重新導向的磁碟機上壓縮他們的檔案，然後儲存到他們的本機電腦上。


