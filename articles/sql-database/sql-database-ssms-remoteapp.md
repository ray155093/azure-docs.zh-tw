---
title: "在 Azure RemoteApp 中使用 SQL Server Management Studio 連接到 SQL Database | Microsoft 文件"
description: "使用本教學課程來了解如何在連線到 SQL Database 時，在 Azure RemoteApp 中使用 SQL Server Management Studio 以維護安全性和效能"
services: sql-database
documentationcenter: 
author: adhurwit
manager: jhubbard
ms.assetid: 1052c83c-e7f5-4736-922f-216194d8874b
ms.service: sql-database
ms.custom: overview
ms.workload: data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: adhurwit
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 92cad42344fb6f77b3088f74ea0e37a542bd0378
ms.lasthandoff: 04/03/2017


---
# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>在 Azure RemoteApp 中使用 SQL Server Management Studio 來連接到 SQL Database

> [!IMPORTANT]
> Azure RemoteApp 即將中止。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
>

## <a name="introduction"></a>簡介
本教學課程會示範如何在 Azure RemoteApp 中使用 SQL Server Management Studio (SSMS) 連接到 SQL Database。 它將逐步說明在 Azure RemoteApp 中設定 SQL Server Management Studio 的程序、說明優點，並示範您可以在 Azure Active Directory 中使用的安全性功能。

**預估完成時間：** 45 分鐘

## <a name="ssms-in-azure-remoteapp"></a>Azure RemoteApp 中的 SSMS
Azure RemoteApp 是在 Azure 中的一項 RDS 服務，可提供應用程式。 您可以在此處深入了解： [什麼是 RemoteApp？](../remoteapp/remoteapp-whatis.md)

在 Azure RemoteApp 中執行的 SSMS 可提供您與在本機執行 SSMS 相同的體驗。

![螢幕擷取畫面顯示正在 Azure RemoteApp 中執行的 SSMS][1]

## <a name="benefits"></a>優點
在 Azure RemoteApp 中使用 SSMS 有許多好處，包括：

* Azure SQL Server 上的連接埠 1433 不需要對外 (Azure 之外) 公開。
* 不需要在 Azure SQL Server 防火牆中不斷新增和移除 IP 位址。
* 所有 Azure RemoteApp 連線都會使用加密的遠端桌面通訊協定，透過 HTTPS 在連接埠 443 上進行
* 這是多使用者功能，且可以調整。
* 在與 SQL Database 相同的區域中擁有 SSMS 可增進效能。
* 您可以利用擁有使用者活動報告的 Azure Active Directory Premium Edition 來稽核 Azure RemoteApp 的使用狀況。
* 您可以啟用 Multi-Factor Authentication (MFA)。
* 使用任一種支援的 Azure RemoteApp 用戶端 (包括 iOS、Android、Mac、Windows Phone，和 Windows 電腦) 時，即可隨處存取 SSMS。

## <a name="create-the-azure-remoteapp-collection"></a>建立 Azure RemoteApp 集合
以下是使用 SSMS 建立 Azure RemoteApp 集合的步驟：

### <a name="1-create-a-new-windows-vm-from-image"></a>1.從映像建立新的 Windows VM
從資源庫中使用「Windows Server 遠端桌面工作階段主機 Windows Server 2012 R2」映像來建立您的新 VM。

### <a name="2-install-ssms-from-sql-express"></a>2.從 SQL Express 安裝 SSMS
移至新的 VM 並瀏覽到此下載頁面：[Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/download/details.aspx?id=42299)

有一個僅下載 SSMS 的選項。 下載之後，移至安裝目錄，並執行安裝程式以安裝 SSMS。

您同時需要安裝 SQL Server 2014 Service Pack 1。 您可以在這裡下載：[Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/download/details.aspx?id=46694)

SQL Server 2014 Service Pack 1 包含和 Azure SQL Database 搭配使用的基本功能。

### <a name="3-run-validate-script-and-sysprep"></a>3.執行「驗證」指令碼和 SysPrep
VM 桌面上的是稱為「驗證」的 PowerShell 指令碼。 按兩下以執行此指令碼。 它會確認 VM 已準備好用於遠端裝載應用程式。 驗證完成時，它會要求執行 Sysprep - 請選擇執行它。

Sysprep 完成時，它會關閉 VM。

若要深入了解建立 Azure RemoteApp 映像，請參閱： [How to create a RemoteApp template image in Azure (如何在 Azure 中建立 RemoteApp 範本映像)](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)

### <a name="4-capture-image"></a>4.擷取映像
當 VM 已經停止執行時，請在目前的入口網站尋找並擷取它。

若要深入了解擷取映像，[請參閱擷取以傳統部署模型建立之 Azure Windows 虛擬機器的映像](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="5-add-to-azure-remoteapp-template-images"></a>5.新增至 Azure RemoteApp 範本映像
在目前入口網站的 [Azure RemoteApp] 區段中，移至 [範本映像] 索引標籤然後按一下 [新增]。 在快顯方塊中，選取 [從您的虛擬機器映像庫匯入映像] 然後選擇您剛才建立的映像。

### <a name="6-create-cloud-collection"></a>6.建立雲端集合
在目前的入口網站中，建立新的 Azure RemoteApp 雲端集合。 選擇您剛才匯入之已安裝 SSMS 的範本映像。

![建立新的雲端集合][2]

### <a name="7-publish-ssms"></a>7.發佈 SSMS
在新雲端集合的 [發佈] 索引標籤上，從 [開始] 功能表選取 [發佈應用程式]，然後從清單中選擇 SSMS。

![發佈應用程式][5]

### <a name="8-add-users"></a>8.新增使用者
您可以在 [使用者存取] 索引標籤上，選取將能存取這個只包含 SSMS 之 Azure RemoteApp 集合的使用者。

![新增使用者][6]

### <a name="9-install-the-azure-remoteapp-client-application"></a>9.安裝 Azure RemoteApp 用戶端應用程式
您可以在這裡下載並安裝 Azure RemoteApp 用戶端： [下載 | Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx)

## <a name="configure-azure-sql-server"></a>設定 Azure SQL Server
所需的唯一設定是確保已針對防火牆啟用 Azure 服務。 如果您使用此方案，就不需要新增任何的 IP 位址來開啟防火牆。 允許進入 SQL Server 的網路流量來自於其他 Azure 服務。

![Azure 允許][4]

## <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)
可特別為此應用程式啟用 MFA。 移至 Azure Active Directory 的 [應用程式] 索引標籤。 您會發現 Microsoft Azure RemoteApp 的項目。 如果您按一下該應用程式然後設定，您將會看到以下頁面，您可以在頁面中為此應用程式啟用 MFA。

![啟用 MFA][3]

## <a name="audit-user-activity-with-azure-active-directory-premium"></a>使用 Azure Active Directory Premium 稽核使用者活動
如果您沒有 Azure AD Premium，那麼您必須在您目錄的 [授權] 區段中將它開啟。 啟用 Premium 之後，您可以將使用者指派到 Premium 層級。

當您移至 Azure Active Directory 中的使用者時，您接著可以移至 [活動] 索引標籤來查看 Azure RemoteApp 的登入資訊。

## <a name="next-steps"></a>後續步驟
完成上述的所有步驟之後，您將可以執行 Azure RemoteApp 用戶端，並使用已指派的使用者登入。 您將會看到 SSMS 顯示為您的其中一個應用程式，而您可以將它當作就像是安裝在您電腦上而能夠存取 Azure SQL Server 一樣來執行它。

如需有關如何連接到 SQL Database 的詳細資訊，請參閱 [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)。

以上為目前的所有內容。 盡情享受！

<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
