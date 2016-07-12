<properties
	pageTitle="資源群組指導方針 | Microsoft Azure"
	description="了解適合用來在 Azure 基礎結構服務中部署資源群組的關鍵設計和實作指導方針。"
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="iainfou"/>

# Azure 資源群組指導方針

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

本文著重於了解在資源群組中邏輯性建置環境並將所有元件分組在一起的方式。


## 資源群組的實作指導方針

决策：

- 您要以核心基礎結構元件建置資源群組，還是以完整的應用程式部署？
- 您是否需要使用角色型存取控制來限制資源群組的存取？

工作：

- 定義您將需要的核心基礎結構元件，以及您是否會使用專屬的資源群組。
- 檢閱如何實作 Resource Manager 範本以取得一致且可重現的部署。
- 定義針對控制資源群組存取所需的使用者存取角色。
- 使用您的命名慣例來建立資源群組組合。您可以使用 Azure CLI 或入口網站。


## 資源群組

在 Azure 中，您可以邏輯性地將相關資源 (例如儲存體帳戶、虛擬網路及虛擬機器 (VM)) 分組在一起，以將它們做為單一實體進行部署、管理及維護。從管理的角度來看，這可讓您在將所有相關資源保持在一起的情況下更輕鬆地部署應用程式，或是授與其他人存取該資源群組。如需針對資源群組取得更完整的了解，請參閱 [Azure Resource Manager 概觀](../resource-group-overview.md)。

資源群組的其中一個關鍵功能，是使用宣告儲存體、網路及計算資源的 JSON 檔案，以及任何要套用的相關自訂指令碼或設定來建置環境的能力。透過使用這些 JSON 範本，您可以為應用程式建立一致且可重現的部署。這讓您可以輕鬆建置開發環境，然後使用相同的範本建立生產環境部署，反之亦然。如需深入了解範本的使用方式，您可以參閱[範本逐步解說](../resource-manager-template-walkthrough.md)，它將能引導您完成建置 JSON 範本的每個步驟。

您在使用資源群組設計環境時，有兩種不同的執行方式：

- 針對每個應用程式部署使用包含儲存體帳戶、虛擬網路和子網路、VM、負載平衡器等等的資源群組。
- 使用包含核心虛擬網路和子網路或儲存體帳戶的集中式資源群組，並讓您的應用程式位於屬於它們的資源群組中 (這些資源群組僅包含 VM、負載平衡器、網路介面等等)。

隨著您相應放大，為虛擬網路和子網路建立集中式資源群組將能讓您更輕鬆地為混合式連線能力選項建立跨單位網路連線，而不用讓每個應用程式擁有需要個別設定和維護的虛擬網路。[角色型存取控制](../active-directory/role-based-access-control-what-is.md)提供更細微的資源群組存取控制方法。針對實際執行應用程式，您可以控制可以存取那些資源的使用者，或是針對核心基礎結構資源限制只有基礎結構工程師可以使用它們。您的應用程式使用者將只能存取他們資源群組內的應用程式元件，而非環境的核心 Azure 基礎結構。當您在設計環境時，請考慮需要存取資源的使用者，並據以設計您的資源群組。


## 後續步驟

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0629_2016-->