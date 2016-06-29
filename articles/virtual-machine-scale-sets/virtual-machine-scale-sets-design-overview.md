<properties
	pageTitle="設計虛擬機器擴展集進行調整 | Microsoft Azure"
	description="深入了解如何設計虛擬機器擴展集進行調整"
	keywords="linux 虛擬機器, 虛擬機器擴展集" 
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/01/2016"
	ms.author="gatneil"/>

# 設計 VM 擴展集以進行調整

本主題會討論虛擬機器擴展集的設計考量。如需虛擬機器擴充集的資訊，請參閱 [Virtual Machine Scale Sets Overview](virtual-machine-scale-sets-overview.md) (虛擬機器擴展集概觀)。


## 儲存體

擴展集使用儲存體帳戶儲存集合中的 VM OS 磁碟。建議比例為每個儲存體帳戶 20 部 VM 或更少。也建議您分散儲存體帳戶名稱開頭字元的英文字母。這有助於將負載分散到不同的內部系統。例如，在下列範本中，我們使用 uniqueString ARM 範本函數產生前置詞雜湊，加在儲存體帳戶名稱的前面︰[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)。


## 過度佈建

從 2016-03-30 API 版本開始，VM 擴展集預設會「過度佈建」VM。這表示擴展集實際備妥的 VM 數目會超過您要求的數目，然後再刪除不必要的 VM。這可改善佈建的成功率，因為即使一部 VM 未成功佈建，Azure Resource Manager 都會將整個部署視為「失敗」。這些額外的 VM 不會向您收費，也不計入配額限制。

雖然這的確改善了佈建的成功率，但也可能導致不是設計來處理 VM 的應用程式，出現令人困惑的無預警消失行為。若要關閉過度佈建，請確定範本中有下列字串："overprovision": false。

如果關閉過度佈建，可以避過每個儲存體帳戶較大的 VM 比例，但數量逾 40 即不建議這麼做。


## 限制
建置在自訂映像 (一個由您所建立) 上的擴展集，必須在一個儲存體帳戶內建立所有的 OS 磁碟 VHD。因此，在以自訂映像為建置基礎的擴展集中建議 VM 數上限為 20。如果關閉過度佈建，數目上限為 40。

以平台映像為建置基礎的擴展集受限於 100 部 VM (這種規模建議 5 個儲存體帳戶)。

如果超出這些限制允許的 VM，您必須部署多個擴展集。[如需如何執行這項操作的範例，請參閱這個範本。](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)

<!---HONumber=AcomDC_0615_2016-->