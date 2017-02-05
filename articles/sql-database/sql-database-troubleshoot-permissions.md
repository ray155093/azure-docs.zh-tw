---
title: "如何執行管理工作，例如重設管理密碼 |Microsoft Docs"
description: "描述如何在 SQL Database 中執行一般管理工作。 例如，重設管理密碼，授與和移除存取權。"
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "重設管理密碼"
ms.assetid: 9803fdcf-6501-4ac4-8cd0-f80071e052e1
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 5b717afe388d9ad6d4d37fa07d351e55ac8dbbb8


---
# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>如何在 Azure SQL Database 中執行重設管理密碼等一般管理工作
使用本主題中的步驟即可快速授與或移除對 Azure SQL Database 的存取權。 如需更完整的資訊，請參閱：

* [管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)
* [保護您的 SQL Database](sql-database-security-overview.md)
* [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>重設邏輯伺服器的系統管理員密碼
* 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Server]、從清單中選取伺服器，然後按一下 [重設密碼]。

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>為協助確保只有授權的 IP 位址可以存取伺服器
* 請參閱 [如何：在 SQL Database 上進行防火牆設定](sql-database-configure-firewall-settings.md)。

## <a name="to-create-contained-database-users-in-the-user-database"></a>若要在使用者資料庫中建立自主的資料庫使用者
* 使用 [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) 陳述式，並參閱[自主的資料庫使用者 - 使資料庫可攜](https://msdn.microsoft.com/library/ff929188.aspx)。

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>若要使用 Azure Active Directory 驗證自主的資料庫使用者
* 請參閱 [使用 Azure Active Directory 驗證連線到 SQL Database](sql-database-aad-authentication.md)。

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>若要在虛擬 master 資料庫中建立高權限使用者的其他登入
* 使用 [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) 陳述式，並參閱[管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)的＜管理登入＞一節以獲得詳細資訊。




<!--HONumber=Dec16_HO4-->


