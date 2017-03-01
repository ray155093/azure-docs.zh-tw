---
title: "管理 Azure Analysis Services 中的使用者 | Microsoft Docs"
description: "了解如何以 Azure 管理 Analysis Services 伺服器上的使用者。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 194910a3e4cb655b39a64d2540994d90d34a68e4
ms.openlocfilehash: 039ed6f4be9f3e0f6b92e5a9f11e12392912df9d
ms.lasthandoff: 02/16/2017


---
# <a name="manage-users-in-azure-analysis-services"></a>管理 Azure Analysis Services 中的使用者
在 Azure Analysis Services 中，有兩種類型的使用者：伺服器管理員和資料庫使用者。 

## <a name="server-administrators"></a>伺服器管理員
在 Azure 入口網站，或 SSMS 中的伺服器屬性中，您可以使用您伺服器之控制項刀鋒視窗中的 **Analysis Services 管理員**來管理伺服器管理員。 Analysis Services 管理員是資料庫伺服器管理員，擁有一般資料庫管理工作的權利，例如新增與移除資料庫，以及管理使用者。 根據預設，系統會將在 Azure 入口網站中建立伺服器的使用者自動新增成為 Analysis Services 管理員。

![Azure 入口網站的伺服器管理員](./media/analysis-services-manage-users/aas-manage-users-admins.png)

您也應該知道︰

* Windows Live ID 不是 Azure Analysis Services 支援的身分識別類型。  
* Analysis Services 管理員必須是有效的 Azure Active Directory 使用者。
* 如果透過 Azure Resource Manager 範本建立 Azure Analysis Services 伺服器，Analysis Services 管理員會取得應新增為管理員的使用者 JSON 陣列。

Analysis Services 管理員與 Azure 資源管理員不同，後者可以管理 Azure 訂用帳戶的資源。 這種做法能夠保持與 Analysis Services 中現有 XMLA 和 TMSL 管理行為的相容性，並可讓您將 Azure 資源管理與 Analysis Services 資料庫管理之間的職責劃分隔離。 若要檢視 Azure Analysis Services 資源的所有角色與存取類型，請使用控制項刀鋒視窗上的存取控制 (IAM)。

### <a name="to-add-administrators-using-azure-portal"></a>若要使用 Azure 入口網站新增系統管理員
1. 在您的伺服器的控制刀鋒視窗中，按一下 [Analysis Services 管理員]。
2. 在 [\<servername> - Analysis Services 管理員]刀鋒視窗中，按一下 [新增]。
3. 在 [新增伺服器管理員] 刀鋒視窗中，選取要新增的使用者帳戶。

## <a name="database-users"></a>資料庫使用者
資料庫使用者必須新增到資料庫角色。 角色會定義具有相同資料庫權限的使用者和群組。 根據預設，表格式模型資料庫擁有具備讀取權限的預設使用者角色。 若要深入了解，請參閱[表格式模型中的角色](https://msdn.microsoft.com/library/hh213165.aspx)。

Azure Analysis Services 模型資料庫使用者*必須在您的 Azure Active Directory 中*。 指定的使用者名稱必須是組織的電子郵件地址或 UPN。 這與內部部署表格式模型資料庫不同，這些資料庫藉由 Windows 網域使用者名稱來支援使用者。 

您可以在 SQL Server Data Tools (SSDT) 或在 SQL Server Management Studio (SSMS) 中建立資料庫角色、新增使用者和群組到角色，以及設定資料列層級安全性。 您也可以使用 [Analysis Services PowerShell Cmdlet](https://msdn.microsoft.com/library/hh758425.aspx) 或使用[表格式模型指令碼語言 (英文)](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) 來新增使用者到角色或從中移除使用者。

**TMSL 指令碼範例**

在此範例中，使用者和群組會新增至 SalesBI 資料庫的使用者角色。

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)

訂用帳戶管理員可以在控制刀鋒視窗中，使用 [存取控制] 來設定角色。 這與上述在伺服器或資料庫層級設定的伺服器管理員或資料庫使用者不同。 

![Azure 入口網站中的存取控制](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

角色會套用到使用者或帳戶，讓他們可在入口網站或使用 Azure Resource Manager 範本完成需要執行的工作。 若要深入了解，請參閱[角色型存取控制](../active-directory/role-based-access-control-what-is.md)。

## <a name="next-steps"></a>後續步驟
如果您尚未將表格式模型部署到伺服器，現在正是時候。 若要深入了解，請參閱 [Deploy to Azure Analysis Services](analysis-services-deploy.md) (部署至 Azure Analysis Services)。

如果您已將模型部署至您的伺服器，您便可以透過用戶端或瀏覽器與伺服器連線。 若要深入了解，請參閱 [Get data from Azure Analysis Services server](analysis-services-connect.md) (從 Azure Analysis Services 伺服器取得資料)。


