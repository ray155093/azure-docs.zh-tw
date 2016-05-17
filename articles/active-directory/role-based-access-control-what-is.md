<properties
	pageTitle="Azure Active Directory 角色型存取控制 | Microsoft Azure"
	description="在 Azure 入口網站中使用 Azure 角色型存取控制開始進行存取管理。使用角色指派在您的目錄中指派權限。"
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
	ms.date="05/03/2016"
	ms.author="kgremban"/>

# 開始在 Azure 入口網站中使用存取管理

Azure 角色型存取控制 (RBAC) 可以對 Azure 進行更細緻的存取權管理。您可以使用 RBAC 來區隔開發小組的職責，僅授與使用者作業所需的存取權。本文將介紹存取管理的基本概念，然後協助您在 Azure 入口網站中啟動並執行 RBAC。

## Azure 存取權管理的基礎
每個 Azure 訂用帳戶都會與一個 Azure Active Directory (AD) 目錄相關聯。該目錄中的使用者、群組和應用程式可以管理 Azure 訂用帳戶中的資源。這些存取權限是透過 Azure 入口網站、Azure 命令列工具或 Azure 管理 API 授與。

在特定範圍將適當的 RBAC 角色指派給使用者、群組和應用程式，即可授與存取權。角色指派的範圍可以是訂用帳戶、資源群組或單一資源。在父範圍指派的角色也會授與其內含子系的存取權。例如，具有資源群組存取權的使用者可以管理其內含的所有資源，例如網站、虛擬機器和子網路。

![Azure Active Directory 元素之間的關聯性 - 圖表](./media/role-based-access-control-what-is/rbac_aad.png)

您指派的 RBAC 角色，指定使用者、群組或應用程式可以在該範圍內管理的資源。

## 內建角色
Azure RBAC 有適用於所有資源類型的三個基本角色：

- **擁有者**具有所有資源的完整存取權，包括將存取權委派給其他人的權限。
- **參與者**可以建立和管理所有類型的 Azure 資源，但是不能將存取權授與其他人。
- **讀者**可以檢視現有的 Azure 資源。

Azure 中其餘的 RBAC 角色可以管理特定 Azure 資源。例如，「虛擬機器參與者」角色可讓使用者建立和管理虛擬機器。但不會授予他們存取虛擬機器所連接的虛擬網路或子網路的存取權。

[RBAC 內建角色](role-based-access-built-in-roles.md)會列出 Azure 中可用的角色。它會指定每個內建角色授與使用者的作業和範圍。如果您想要定義自己的角色，獲得更進一步控制，請參閱如何建立 [Azure RBAC 中的自訂角色](role-based-access-control-custom-roles.md)。

## 資源階層和存取繼承
- Azure 中的每個**訂用帳戶**只屬於一個目錄。
- 每個**資源群組**只屬於一個訂用帳戶。
- 每個**資源**只屬於一個資源群組。

您在父範圍授與的存取權會在子範圍繼承。例如：

- 您可將讀者角色指派給訂用帳戶範圍內的 Azure AD 群組。該群組的成員可以檢視訂用帳戶中的每個資源群組和資源。
- 您可將參與者角色指派給資源群組範圍內的應用程式。它可以管理該資源群組中所有類型的資源，但是無法管理訂用帳戶中的其他資源群組。

## Azure RBAC 與傳統訂用帳戶系統管理員
傳統訂用帳戶系統管理員和共同管理員具有 Azure 訂用帳戶的完整存取權。他們可以使用 [Azure 入口網站](https://portal.azure.com)和 Azure Resource Manager API 或是 [Azure 傳統入口網站](https://manage.windowsazure.com)和 Azure 服務管理 API 來管理資源。在 RBAC 模型中，傳統系統管理員會獲指派訂用帳戶範圍的擁有者角色。

只有 Azure 入口網站和新的 Azure Resource Manager API 支援 Azure RBAC。獲指派 RBAC 角色的使用者和應用程式無法使用傳統管理入口網站和 Azure 服務管理 API。

## 管理與資料作業的授權
Azure RBAC 僅支援在 Azure 入口網站和 Azure Resource Manager API 中的 Azure 資源管理作業。並非所有 Azure 資源的資料作業都可以透過 RBAC 授權。例如，可以使用 RBAC 管理儲存體帳戶，但無法管理儲存體帳戶內的 Blob 或資料表。同樣地，可以管理 SQL Database，但無法管理其中的資料表。

## 後續步驟
- 在 Azure 入口網站中開始使用 [Azure 角色型存取控制](role-based-access-control-configure.md)。
- 請參閱 [RBAC 內建角色](role-based-access-built-in-roles.md)
- 為自己定義 [Azure RBAC 中的自訂角色](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0504_2016-->