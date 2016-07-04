<properties
	pageTitle="Azure 儀表板的共用和存取控制 | Microsoft Azure"
	description="了解如何管理 Azure 儀表板的共用和存取控制。"
	services="azure-resource-manager,azure-portal"
    keywords="入口網站,共用,存取"
	documentationCenter=""
	authors="adamabdelhamed"
	manager="dend"
	editor="dend"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="adamab"/>
    
# Azure 儀表板的共用和存取控制

入口網站中大多數圖格所顯示資訊的存取權是由 Azure [角色型存取控制](./active-directory/role-based-access-control-configure.md)所控管。為了將儀表板完美地整合到生態系統中，所有已發佈的儀表板會當作 Azure 資源實作。從存取控制觀點來看，儀表板與虛擬機器或儲存體帳戶並無不同。

範例如下。假設您有 Azure 訂用帳戶，而且已為您小組的各種成員指派訂用帳戶的**擁有者**、**參與者**或**讀取者**角色。身為擁有者或參與者的使用者將能夠列出、檢視、建立、修改或刪除訂用帳戶內的儀表板。身為讀者的使用者將能夠列出和檢視儀表板，但無法進行修改或刪除。具有讀者存取權的使用者將能夠對已發佈的儀表板進行本機編輯 (例如在針對問題進行疑難排解時)，但不會提供將這些變更發佈回到伺服器的選項。他們會有為自己製作儀表板的私人複本的選項。

請注意，儀表板上的個別圖格將會根據其顯示資料的資源，強制執行自己的存取控制需求。這表示您可以設計一個更廣泛共用而同時仍會保護個別圖格上資料的儀表板。

<!---HONumber=AcomDC_0622_2016-->