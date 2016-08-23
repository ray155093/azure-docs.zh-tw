<properties
	pageTitle="使用 Azure CLI 管理角色型存取控制 (RBAC) | Microsoft Azure"
	description="了解如何使用 Azure 命令列介面，藉由列出角色和角色動作，以及藉由將角色指派給訂用帳戶和應用程式範圍，來管理「角色型存取控制」(RBAC)。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="07/22/2016"
	ms.author="kgremban"/>

# 使用 Azure 命令列介面管理角色型存取控制

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

您可以使用 Azure 入口網站及 Azure Resource Manager API 中的「角色型存取控制」(RBAC) 來精確管理對您訂用帳戶和資源的存取。透過這項功能，您可以為 Active Directory 使用者、群組或是服務主體指派特定範圍的一些角色，藉此賦予其存取權限。

您必須具備下列條件，才能使用 Azure 命令列介面 (CLI) 來管理 RBAC：

- Azure CLI 0.8.8 版或更新版本。若要安裝最新版本，並將它與 Azure 訂用帳戶建立關聯，請參閱[安裝和設定 Azure CLI](../xplat-cli-install.md)。
- Azure CLI 中的 Azure Resource Manager。如需詳細資訊，請瀏覽[搭配使用 Azure CLI 和 Resource Manager](../xplat-cli-azure-resource-manager.md)。

## 列出角色

###	列出所有可用的角色
若要列出所有可用的角色，請使用：

		azure role list

下列範例顯示*所有可用角色*的清單。

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![RBAC Azure 命令列 - azure 角色清單 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	列出角色的動作
若要列出角色的動作，請使用：

    azure role show "<role name>"

下列範例顯示 *Contributor* 和 *Virtual Machine Contributor* 角色的動作。

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![RBAC Azure 命令列 - azure 角色顯示 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	列出存取權
###	列出資源群組上有效的角色指派
若要列出資源群組中存在的角色指派，請使用︰

    azure role assignment list --resource-group <resource group name>

下列範例顯示 pharma-sales-projecforcast 群組中的角色指派。

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![RBAC Azure 命令列 - 依群組顯示的 azure 角色指派清單 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	列出使用者的角色指派
若要列出特定使用者的角色指派，以及指派給使用者群組的指派，請使用︰

	azure role assignment list --signInName <user email>

您也可以透過修改命令，來查看繼承自群組的角色指派︰

	azure role assignment list --expandPrincipalGroups --signInName <user email>

下列範例顯示授與 *sameert@aaddemo.com* 使用者的角色指派。這包括直接指派給使用者的角色，以及繼承自群組的角色。

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![RBAC Azure 命令列 - 依使用者顯示的 azure 角色指派清單 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	授與存取權
在您已識別所要指派的角色之後，若要授與存取權，請使用：

    azure role assignment create

###	將角色指派給訂用帳戶範圍中的群組
若要將角色指派給訂用帳戶範圍中的群組，請使用：

	azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

下列範例會將*讀者*角色指派給*訂用帳戶*範圍中的 *Christine Koch 小組*。


![RBAC Azure 命令列 - 群組所建立的 azure 角色指派 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	將角色指派給訂用帳戶範圍中的應用程式
若要將角色指派給訂用帳戶範圍中的應用程式，請使用：

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

下列範例會將「參與者」角色授與所選取之訂用帳戶上的「Azure AD」應用程式。

 ![RBAC Azure 命令列 - 應用程式所建立的 azure 角色指派](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	將角色指派給資源群組範圍中的使用者
若要將角色指派給資源群組範圍中的使用者，請使用：

	azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

下列範例會將 *Virtual Machine Contributor* 角色授與 *Pharma-Sales-ProjectForcast* 資源群組範圍中的 *samert@aaddemo.com* 使用者。

![RBAC Azure 命令列 - 使用者所建立的 azure 角色指派 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	將角色指派給資源範圍中的群組
若要將角色指派給資源範圍中的群組，請使用：

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

下列範例會將 *Virtual Machine Contributor* 角色授與「子網路」上的 *Azure AD* 群組。

![RBAC Azure 命令列 - 群組所建立的 azure 角色指派 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	移除存取
若要移除角色指派，請使用：

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

下列範例會從 *Pharma-Sales-ProjectForcast* 資源群組上的 *sammert@aaddemo.com* 使用者移除 *Virtual Machine Contributor* 角色指派。此範例會接著從訂用帳戶上的群組移除角色指派。

![RBAC Azure 命令列 - azure 角色指派刪除 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## 建立自訂角色
若要建立自訂角色，請使用：

	azure role create --inputfile <file path>

下列範例會建立一個名為 *Virtual Machine Operator* 的自訂角色。自訂角色會授與對 *Microsoft.Compute*、*Microsoft.Storage* 及 *Microsoft.Network* 資源提供者之所有讀取作業的存取權，以及授與對啟動、重新啟動和監視虛擬機器的存取權。自訂角色可用於兩個訂用帳戶中。這個範例使用 JSON 檔案做為輸入。

![JSON - 自訂角色定義 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![RBAC Azure 命令列 - azure 角色建立 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## 修改自訂角色

若要修改自訂角色，請先使用 `azure role show` 命令來擷取角色定義。接著，對角色定義檔案進行想要的變更。最後，使用 `azure role set` 儲存已修改的角色定義。

	azure role set --inputfile <file path>

下列範例會將 *Microsoft.Insights/diagnosticSettings/* 作業新增到 **Actions** 中，並將 Azure 訂用帳戶新增到 Virtual Machine Operator 自訂角色的 **AssignableScopes** 中。

![JSON - 修改自訂角色定義 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![RBAC Azure 命令列 - azure 角色設定 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## 刪除自訂角色

若要刪除自訂角色，請先使用 `azure role show` 命令來判斷角色的 **ID**。接著，使用 `azure role delete` 命令，藉由指定 **ID** 來刪除角色。

下列範例會移除 *Virtual Machine Operator* 自訂角色

![RBAC Azure 命令列 - azure 角色刪除 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## 列出自訂角色

若要列出範圍中可供指派的角色，請使用 `azure role list` 命令。

下列範例會列出選取的訂用帳戶中可供指派的所有角色。

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![RBAC Azure 命令列 - azure 角色清單 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

在下列範例中，*Virtual Machine Operator* 自訂角色無法在 *Production4* 訂用帳戶中使用，因為該訂用帳戶並沒有在角色的 **AssignableScopes** 中。

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![RBAC Azure 命令列 - 自訂角色的 azure 角色清單 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## RBAC 主題
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0810_2016------>