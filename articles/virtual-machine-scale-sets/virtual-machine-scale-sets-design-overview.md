---
title: 設計虛擬機器擴展集進行調整 | Microsoft Docs
description: 深入了解如何設計虛擬機器擴展集進行調整
keywords: linux 虛擬機器, 虛擬機器擴展集
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: gatneil

---
# 設計 VM 擴展集以進行調整
本主題會討論虛擬機器擴展集的設計考量。如需虛擬機器擴展集的相關資訊，請參閱[虛擬機器擴展集概觀](virtual-machine-scale-sets-overview.md)。

## 儲存體
擴展集使用儲存體帳戶儲存集合中的 VM OS 磁碟。建議比例為每個儲存體帳戶 20 部 VM 或更少。也建議您分散儲存體帳戶名稱開頭字元的英文字母。這樣做有助於將負載分散到不同的內部系統。例如，在下列範本中，我們使用 uniqueString Resource Manager 範本函數來產生前置詞雜湊，這些雜湊會加在儲存體帳戶名稱的前面：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)。

## 過度佈建
從 "2016-03-30" API 版本開始，VM 擴展集預設會「過度佈建」VM。藉由開啟過度佈建，擴展集實際上所啟動的 VM 數目會比您要求的還多，最後再刪除額外啟動的 VM。過度佈建可改善佈建的成功率。這些額外的 VM 不會向您收費，也不計入配額限制。

雖然過度佈建的確改善了佈建的成功率，但也可能導致不是設計來處理 VM 無預警消失的應用程式出現令人困惑的行為。若要將過度佈建關閉，請確定範本中有下列字串："overprovision": "false"。您可以在 [VM 擴展集 REST API 文件](https://msdn.microsoft.com/library/azure/mt589035.aspx)中找到更多詳細資料。

如果關閉過度佈建，可以避過每個儲存體帳戶較大的 VM 比例，但數量逾 40 即不建議這麼做。

## 限制
擴展集如果是以自訂映像 (一個由您所建立的映像) 為建置基礎，就必須在一個儲存體帳戶內建立所有的 OS 磁碟 VHD。因此，在以自訂映像為建置基礎的擴展集中建議 VM 數上限為 20。如果關閉過度佈建，數目上限為 40。

以平台映像為建置基礎的擴展集目前限制為 100 部 VM (針對這種規模，建議使用 5 個儲存體帳戶)。

如果超出這些限制允許的 VM，您必須部署多個擴展集，如[這個範本](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)中所示。

<!---HONumber=AcomDC_0817_2016-->