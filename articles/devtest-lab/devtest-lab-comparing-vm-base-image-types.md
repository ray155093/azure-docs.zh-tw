<properties
	pageTitle="比較 DevTest Labs 中的自訂映像和公式 | Microsoft Azure"
	description="了解自訂映像與作為 VM 基礎的公式之間的差異，以決定哪一個最適合您的環境。"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2016"
	ms.author="tarcher"/>

# 比較研發/測試實驗室中的自訂映像和公式

## Overview
[自訂映像](./devtest-lab-create-template.md)和[公式](./devtest-lab-manage-formulas.md)可以當作[建立新 VM](./devtest-lab-add-vm-with-artifacts.md) 的基礎。不過，自訂映像與公式的主要差別在於自訂映像只是根據 VHD 的影像，而公式是根據 VHD 的影像並且具有預先設定的設定 (例如 VM 大小、虛擬網路和子網路、構件等)。這些預先設定的設定是使用預設值所設定，並且可以在建立 VM 時予以覆寫。本文說明使用自訂映像與使用公式的一些優缺點。
 
## 自訂映像的優缺點
自訂映像提供靜態、不可變的方式，從所需的環境中建立 VM。

**優點**
- 從自訂映像進行的 VM 佈建，與從映像啟動 VM 後未進行任何變更一樣地快速。換句話說，因為自訂映像就是沒有設定的映像，所以不需要套用任何設定。
- 從單一自訂映像建立的 VM 都相同。

**缺點**
- 如果您需要更新自訂映像的某些部分，就必須重新建立映像。

## 公式的優缺點
公式提供動態方式，透過所需的組態/設定來建立 VM。

**優點**
- 透過構件可以即時擷取環境中的變更。例如，如果您想要已安裝發行管線的最新位元的 VM，或登錄儲存機制中的最新程式碼，只需要指定構件，這個構件部署最新位元，或登錄與目標基本映像一起的公式中的最新程式碼。只要使用此公式建立 VM，就會將最新位元/程式碼部署/登錄至 VM。
- 公式可以定義自訂映像無法提供的預設設定 (例如 VM 大小和虛擬網路設定)。
- 公式中所儲存的設定會顯示為預設值，但是可以在建立 VM 時進行修改。

**缺點**
- 透過公式建立 VM 所需的時間多於透過自訂映像建立 VM。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 相關部落格文章

- [自訂映像或公式？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

<!---HONumber=AcomDC_0831_2016-->