<properties
	pageTitle="為 Azure Resource Manager 中的虛擬機器設定金鑰保存庫 | Microsoft Azure"
	description="如何設定要與 Azure Resource Manager 虛擬機器搭配使用的金鑰保存庫"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# 為 Azure Resource Manager 中的虛擬機器設定金鑰保存庫

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型

在 Azure Resource Manager 堆疊中，密碼/憑證會被塑造成「金鑰保存庫資源提供者」所提供的資源。若要深入了解「金鑰保存庫」，請參閱[什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-whatis.md)

## 設定
為了讓「金鑰保存庫」能與 Azure Resource Manager「虛擬機器」搭配使用，必須將「金鑰保存庫」上的 *EnabledForDeployment* 屬性設定為 true。您可以在各種用戶端中進行這項操作，如以下所示。

## CLI
如需了解如何使用 CLI 來建立「金鑰保存庫」，請參閱[使用 CLI 管理金鑰保存庫](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault)

針對 CLI，您必須先建立「金鑰保存庫」，然後啟用部署原則。您可以使用下列命令來進行這項操作

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## 範本
使用範本時，您必須將「金鑰保存庫」資源的 `enabledForDeployment` 屬性設定為 `true`。

	{
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

如需透過範本建立「金鑰保存庫」時您可以設定的其他選項，請參閱[這裡](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

<!---HONumber=AcomDC_0608_2016-->