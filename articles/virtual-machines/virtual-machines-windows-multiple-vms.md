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

需要建立大量類似虛擬機器 (VM) 的案例不勝枚舉，部分範例包括高效能運算 (HPC)、大規模資料分析、可調整且通常是無狀態的中介層或後端伺服器 (例如 Web 伺服器)，以及分散式資料庫。

本文章討論在 Azure 中建立多部 VM 的可用選項，這些選項超越手動建立一系列 VM 的簡單案例。畢竟，在建立數量較多的 VM 時，如果按照平常使用的程序進行將無法妥善調整。

其中一個可建立許多類似 VM 的方法，就是使用 Azure Resource Manager 資源迴圈建構。

## 資源迴圈

資源迴圈是 Azure Resource Manager 範本中的速記語法，它能您在迴圈中建立一組設定相似的資源。您可以使用資源迴圈來建立多個儲存體帳戶、網路介面或虛擬機器。如需資源迴圈的詳細資訊，請參考[使用資源迴圈在可用性設定組中建立 VM](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/)。

## 規模的挑戰

儘管資源迴圈讓建置大規模雲端基礎結構變得更簡單，也可以產生更精確的範本，不過仍有一些難題有待克服。例如，如果要使用資源迴圈建立 100 部虛擬機器，您需要建立網路介面控制器 (NIC) 與對應 VM 和儲存體帳戶之間的關聯。由於 VM 的數目和儲存體帳戶的數目可能會不同，因此會造就不同大小的資源迴圈。這些是可解決的問題，不過會讓複雜度隨著規模擴大而大幅增長。

另一項挑戰發生於當您需要彈性調整的基礎結構時，例如您可能想自動調整基礎結構，以自動因應工作負載而增加或減少 VM 數目。VM 不提供改變數目 (相應放大和相應縮小) 的整合式機制。假設您想透過移除 VM 來達到相應縮小的目的，但即使 VM 在更新和容錯網域之間保持平衡，依然很難保證高可用性。

最後，當您使用一個資源迴圈時，就會產生許多涉及基礎網狀架構的資源建立訴求。一旦有許多建立類似資源的訴求出現，Azure 就有潛在機會可以改善設計和實施最佳化，藉此提升部署可靠性和效能。這正是我們引進「虛擬機器擴展集」的契機。

## 虛擬機器擴展集

虛擬機器擴展集是一種 Azure 雲端服務資源，可用來部署及管理一組相同的 VM。由於所有 VM 的設定均相同，所以能輕鬆相應縮小和增加 VM 擴展集。您只要變更集合中的 VM 數目即可。您也可以根據工作負載的需求自動調整 VM 擴展集。

對於需要相應放大計算資源的應用程式，調整作業會隱含地平衡分散到容錯網域和更新網域。

VM 擴展集擁有網路、儲存體、虛擬機器和擴充功能等供您集中設定的屬性，而不需建立 NIC 和 VM 等多種資源之間的關聯。

如需 VM 擴展集的簡介，請參閱[虛擬機器擴展集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)的產品頁面。如需詳細資訊，請參閱[虛擬機器擴展集](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)的文件。

<!---HONumber=AcomDC_0518_2016-->