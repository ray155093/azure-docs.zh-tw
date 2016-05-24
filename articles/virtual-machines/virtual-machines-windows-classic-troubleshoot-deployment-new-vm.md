<properties
   pageTitle="針對 Windows VM 部署-傳統進行疑難排解 | Microsoft Azure"
   description="針對在 Azure 中建立新 Windows 虛擬機器的傳統部署問題進行疑難排解"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="jiangchen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
  ms.service="virtual-machines-windows"
  ms.workload="na"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="05/06/2016"
  ms.author="cjiang"/>

# 針對在 Azure 中建立新 Windows 虛擬機器的傳統部署問題進行疑難排解

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Resource Manager 模型。

[AZURE.INCLUDE [支援免責聲明](../../includes/support-disclaimer.md)]

## 收集稽核記錄檔

若要開始進行排解疑難，請收集稽核記錄，識別與問題相關的錯誤。

在 Azure 入口網站中，依序按一下 [瀏覽] > [虛擬機器] > [您的 Windows 虛擬機器] > [設定] > [稽核記錄]。

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y：**如果作業系統是一般化的 Windows，且帶著一般化設定被上傳及/或擷取，則不會有任何錯誤。同樣的，如果作業系統是特殊化的 Windows，且帶著特殊化設定被上傳及/或擷取，則不會有任何錯誤。

**上傳錯誤：**

**N<sup>1</sup>：**如果作業系統是一般化的 Windows，但是以特殊化被上傳，就會發生佈建逾時錯誤，VM 會卡在 OOBE 畫面。

**N<sup>2</sup>：**如果作業系統是特殊化的 Windows，但是以一般化被上傳，就會發生佈建失敗錯誤，VM 會卡在 OOBE 畫面，因為新 VM 是以原始的電腦名稱、使用者名稱和密碼執行。

**解決方案：**

若要解決這兩個錯誤，請上傳原始 VHD、可用的內部部署、以及與該 OS (一般化/特殊化) 相同的設定。若要以一般化上傳，請務必先執行 sysprep。如需詳細資訊，請參閱[建立 Windows Server VHD 並上傳至 Azure](virtual-machines-windows-classic-createupload-vhd.md)。

**擷取錯誤：**

**N<sup>3</sup>：**如果作業系統是一般化的 Windows，但是以特殊化被擷取，就會發生佈建逾時錯誤，因為 VM 被標示為一般化而無法加以使用。

**N<sup>4</sup>：**如果作業系統是特殊化的 Windows，但是以一般化被擷取，就會發生佈建失敗錯誤，因為新 VM 是以原始的電腦名稱、使用者名稱和密碼執行。此外，原始 VM 會被標示為特殊化而無法加以使用。

**解決方案：**

若要解決這兩個錯誤，請刪除入口網站中目前的映像，[從目前的 VHD 重新擷取映像](virtual-machines-windows-classic-capture-image.md)，此映像將帶有與該 OS (一般化/特殊化) 相同的設定。

## 問題︰自訂/資源庫/Marketplace 映像；配置失敗
當新的 VM 要求被傳送到沒有可用空間可處理要求、或不支援所要求的 VM 大小的叢集，便會發生此錯誤。在相同的雲端服務中不可混合不同系列的 VM。因此，如果您想要建立和您的雲端服務可支援大小不同的新 VM，計算要求將會失敗。

您可能會遇到因兩種情況造成的錯誤，取決於您用於建立新 VM 的雲端服務的條件約束。

**原因 1：**雲端服務已釘選到特定叢集，或是連結到同質群組，因而釘選到所設計的特定叢集。所以該同質群組中新的計算資源要求，將於裝載現有資源的相同叢集中嘗試提出。不過，同一叢集可能不支援要求的 VM 大小，或者可用空間不足，導致配置錯誤。無論新的資源是透過新的雲端服務，或是透過現有的雲端服務來建立，都是如此。

**解決方式 1：**

- 建立新的雲端服務，並將它和區域或以區域為基礎的虛擬網路相關聯。
- 在新的雲端服務中建立新 VM。如果您在嘗試建立新的雲端服務時收到錯誤，請稍後再試一次，或變更雲端服務的區域。

> [AZURE.IMPORTANT] 如果您嘗試在現有的雲端服務中建立新的 VM，但無法建立，而您又必須為新的 VM 建立新的雲端服務，則可以選擇合併相同雲端服務中的所有 VM。若要這樣做，請刪除現有雲端服務中的 VM，然後從它們位於新雲端服務中的磁碟重新擷取它們。然而，請務必記得新的雲端服務將會有新的名稱和 VIP，因此您需要為所有目前將此資訊用於現有雲端服務的相依性更新該資訊。

**原因 2：**雲端服務已經與連結到同質群組的虛擬網路關聯，因而釘選到所設計的特定叢集。因此，該同質群組中所有新的計算資源要求，將於裝載現有資源的相同叢集中嘗試提出。不過，同一叢集可能不支援要求的 VM 大小，或者可用空間不足，導致配置錯誤。無論新的資源是透過新的雲端服務，或是透過現有的雲端服務來建立，都是如此。

**解決方式 2：**

- 建立新的區域虛擬網路。
- 在新的虛擬網路中建立新 VM。
- [連接您現有的虛擬網路](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/)到新的虛擬網路。深入了解[區域虛擬網路](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)。此外，也可以[將同質群組式虛擬網路移轉至區域虛擬網路](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)，然後建立新 VM。

<!---HONumber=AcomDC_0511_2016-->