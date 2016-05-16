<properties
	pageTitle="建立多部虛擬機器 | Microsoft Azure"
	description="在 Windows 上建立多部虛擬機器的選項"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="guybo"/>

# 建立多部 Azure 虛擬機器

需要建立大量類似虛擬機器的案例不勝枚舉，例如高效能運算 (HPC)、大規模資料分析、可調整且通常是無狀態的中介層或後端伺服器 (如 Web 伺服器)，以及分散式資料庫。

本文章討論在 Azure 中建立多部 VM 的可用選項，這些選項超越手動建立一系列 VM 的簡單案例，能避免只要建立稍多 VM 就應付不暇的窘境。

將建立許多類似 VM 這件工作最佳化的其中一個方法，就是使用 Azure Resource Manager 資源迴圈建構。

## 資源迴圈

資源迴圈是 Azure Resource Manager 範本中的速記語法，它能您在迴圈中建立一組設定相似的資源。您可以使用資源迴圈來建立多個儲存體帳戶、網路介面、虛擬機器等。如需資源迴圈的詳細資訊，請參考[使用資源迴圈在可用性設定組中建立 VM](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/)。

## 規模的挑戰

儘管資源迴圈讓建置大規模雲端基礎結構變得更簡單，而且也允許使用更精確的範本，不過仍有一些難題有待克服。例如，如果要使用資源迴圈建立 100 部虛擬機器，在定義 VM 時，您需要建立 NIC 與 VM 和儲存體帳戶之間的關聯。由於 VM 的數目和儲存體帳戶的數目可能會不同，因此會造就不同大小的資源迴圈。這些是可解決的問題，不過會讓複雜度隨著規模擴大而大幅增長。

另一項挑戰發生於當您需要彈性調整的基礎結構時 (如自動調整，亦即自動因應工作負載而增加或減少 VM 數目)。VM 不提供改變數目 (相應放大和相應縮小) 的整合式機制。如果您希望能透過移除 VM 來達到相應縮小的目的，藉由確保 VM 在更新和容錯網域之間保持平衡來保有高可用性是另一項艱難的工作。

最後，雖然資源迴圈是速記語法，不過只要您使用一個，就會有許多以基礎網狀架構為目標的建立資源訴求。一旦有許多建立類似資源的訴求出現，對 Azure 來說，這就是改善設計和實施最佳化，提升部署可靠性和效能的潛在機會。這正是_虛擬機器擴展集_問世的契機。

## 虛擬機器擴展集

虛擬機器擴展集是用來部署及管理一組相同 VM 的 Azure 計算資源。由於所有 VM 的設定均相同，所以只要變更集合中的 VM 數目就能輕鬆地相應縮小和增加 VM 擴展集；這樣一來，只要經過設定，它就能根據工作負載的需求自動調整。

對於需要相應放大計算資源的應用程式，調整作業會隱含地平衡分散到容錯網域和更新網域。

與其建立 NIC 和 VM 等多種資源之間的關聯，擴展集擁有網路、儲存體、虛擬機器和擴充功能等供您集中設定的屬性。

如 VM 擴展集的簡介，請參閱[產品頁面](https://azure.microsoft.com/services/virtual-machine-scale-sets/)；如需詳細資訊，請造訪[文件](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)。

<!---HONumber=AcomDC_0504_2016-->