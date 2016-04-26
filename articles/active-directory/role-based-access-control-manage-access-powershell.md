<properties
	pageTitle="使用 Azure PowerShell 管理角色型存取控制 (RBAC) | Microsoft Azure"
	description="如何使用 Azure PowerShell 管理 RBAC，包括列出角色、指派角色，以及刪除角色指派。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="04/12/2016"
	ms.author="kgremban"/>

# 使用 Azure PowerShell 管理角色型存取控制

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)


在 Azure 入口網站和 Azure 資源管理 API 中的角色型存取控制 (RBAC) 可讓您精確地管理訂用帳戶的存取。透過這項功能，您可以為 Active Directory 使用者、群組或是服務主體指派特定範圍的一些角色，藉此賦予其存取權限。

在使用 PowerShell 來管理 RBAC 之前，您必須具備下列項目：

- Azure PowerShell 0.8.8 或更新版本。若要安裝最新版本，並將它與 Azure 訂用帳戶建立關聯，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

- Azure Resource Manager Cmdlet。在 PowerShell 中，安裝 [Azure Resource Manager Cmdlet](https://msdn.microsoft.com/library/mt125356.aspx)。

## 列出角色

### 列出所有可用的角色
若要列出可以指派的 RBAC 角色，以及若要檢查它們獲得存取權的作業，請使用：

		Get-AzureRmRoleDefinition

![RBAC PowerShell - Get-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### 列出角色的動作
若要列出特定角色的動作，請使用：

    Get-AzureRmRoleDefinition <role name>

![RBAC PowerShell - 特定角色的 Get-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## 查看誰具有存取權
若要列出 RBAC 存取權指派，請使用：

    Get-AzureRmRoleAssignment

###	列出特定範圍的角色指派
您可以查看指定訂用帳戶、資源群組或資源的所有存取權指派。例如，若要查看資源群組的所有使用中指派，請使用︰

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![RBAC PowerShell - 資源群組的 Get-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### 列出指派給使用者的角色
若要列出指派給指定使用者的所有角色，包括指派給其所屬群組的角色，請使用︰

    Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups

![RBAC PowerShell - 使用者的 Get-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### 列出傳統服務管理員和共同管理員角色指派
若要列出傳統訂用帳戶管理員和共同管理員的存取權指派，請使用：

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## 授與存取權
### 搜尋物件識別碼
若要指派角色，您必須識別物件 (使用者、群組或應用程式) 和範圍。

如果您不知道訂用帳戶 ID，可以在 Azure 入口網站的 [訂用帳戶] 刀鋒視窗中找到。或者，您也可以在 MSDN 中了解如何使用 [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) 加以查詢。

若要取得 Azure AD 群組的物件識別碼，請使用：

    Get-AzureRmADGroup -SearchString <group name in quotes>

若要取得 Azure AD 服務主體或應用程式的物件 ID，請使用：

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### 將角色指派給訂用帳戶範圍中的應用程式
若要將存取權授與訂用帳戶範圍中的應用程式，請使用：

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name in quotes> -Scope <subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### 將角色指派給資源群組範圍中的使用者
若要將存取權授與資源群組範圍中的使用者，請使用：

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### 將角色指派給資源範圍中的群組
若要將存取權授與資源範圍中的群組，請使用：

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## 移除存取
若要移除使用者、群組和應用程式的存取權，請使用：

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## 建立自訂角色
若要建立自訂角色，請使用 `New-AzureRmRoleDefinition` 命令。

下列範例會建立名為 *Virtual Machine Operator* 的自訂角色，該角色會授與 *Microsoft.Compute*、*Microsoft.Storage*，和 *Microsoft.Network* 資源提供者對所有讀取作業的存取權限，以及授與啟動、重新啟動，和監視虛擬機器的存取權限。自訂角色可用於兩個訂用帳戶中。

![RBAC PowerShell - Get-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## 修改自訂角色
若要先修改自訂角色，請使用 `Get-AzureRmRoleDefinition` 命令擷取角色定義。接著，對角色定義進行想要的變更。最後，使用 `Set-AzureRmRoleDefinition` 命令儲存已修改的角色定義。

下列範例會將 `Microsoft.Insights/diagnosticSettings/*` 作業加入到 *Virtual Machine Operator* 自訂角色。

![RBAC PowerShell - Set-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

下列範例會將 Azure 訂用帳戶加入到 Virtual Machine Operator 自訂角色的可指派範圍。

![RBAC PowerShell - Set-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## 刪除自訂角色

若要刪除自訂角色，請使用 `Remove-AzureRmRoleDefinition` 命令。

下列範例會移除 *Virtual Machine Operator* 自訂角色

![RBAC PowerShell - Remove-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## 列出自訂角色
若要列出範圍中可供指派的角色，請使用 `Get-AzureRmRoleDefinition` 命令。

下列範例會列出選取的訂用帳戶中所有可供指派的角色。

![RBAC PowerShell - Get-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

在下列範例中，*Virtual Machine Operator* 自訂角色無法在 *Production4* 訂用帳戶中使用，因為該訂用帳戶並沒有在角色的 **AssignableScopes** 中。

![RBAC PowerShell - Get-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## 另請參閱
- [搭配使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0413_2016-->