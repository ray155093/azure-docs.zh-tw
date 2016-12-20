---
title: "在 StorSimple 裝置上安裝 Update 2.2 | Microsoft Docs"
description: "說明如何在您的 StorSimple 8000 系列裝置上安裝 StorSimple 8000 系列 Update 2.2。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 047c7a4b-73d0-45ea-8d51-c54d71871392
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c60b4de88d60f0373d69fe2f3cee5ccf888b8d8c


---
# <a name="install-update-22-on-your-storsimple-device"></a>在 StorSimple 裝置上安裝 Update 2.2
## <a name="overview"></a>概觀
本教學課程說明如何透過 Azure 傳統入口網站及使用 Hotfix 方法，在執行舊版軟體的 StorSimple 裝置上安裝 Update 2.2。 當閘道器是設定於 StorSimple 裝置之 DATA 0 以外的網路介面上，且您正嘗試從 Update 1 以前的軟體版本更新時，就會使用 Hotfix 方法。

Update 2.2 包括裝置軟體、WMI 及 iSCSI 更新。 如果從 2.1 版更新，只需要套用裝置軟體更新。 如果是從 Update 2 以前的版本更新，您也將需要套用 LSI 驅動程式、Spaceport、Storport 及磁碟韌體更新。 裝置軟體、WMI、iSCSI、LSI 驅動程式、Spaceport 及 Storport 修正程式為非干擾性更新，且可透過 Azure 傳統入口網站套用。 磁碟韌體更新為干擾性更新，且只能透過裝置的 Windows PowerShell 介面套用。 

> [!IMPORTANT]
> * 安裝前會執行一組手動和自動預先檢查，以根據硬體狀態和網路連線來判斷裝置健全狀況。 這些預先檢查只會在您從 Azure 傳統入口網站套用更新時執行。
> * 建議您透過 Azure 傳統入口網站安裝軟體和驅動程式更新。 如果入口網站中的更新前閘道器檢查失敗，請移至裝置的 Windows PowerShell 介面安裝更新 (勿透過其他方式)。 視您從哪一個版本更新而定，可能需要 1.5-2.5 小時來安裝更新。 維護模式更新必須透過裝置的 Windows PowerShell 介面安裝。 由於維護模式更新是干擾性更新，它們將會導致裝置的停機時間。
> * 如果執行的是選擇性的 StorSimple Snapshot Manager，更新裝置之前，請確定您已將 Snapshot Manager 版本升級至 Update 2.2。
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-22-via-the-azure-classic-portal"></a>透過 Azure 傳統入口網站安裝 Update 2.2
請執行下列步驟來將您的裝置更新至 [Update 2.2](storsimple-update21-release-notes.md)。

> [!NOTE]
> 如果您是要套用 Update 2 或更新版本 (包括 Update 2.1)，Microsoft 將可以提取裝置的其他診斷資訊。 因此，當我們的作業小組識別有問題的裝置時，我們更有能力從裝置收集資訊並診斷問題。 接受 Update 2 或更新版本，表示您允許我們提供此主動支援。
> 
> 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

1. 確認您裝置執行的是 **StorSimple 8000 系列 2.2 (6.3.9600.17708)**。 [ **上次更新日期** ] 應該也已修改。 
   
   如果您是從 Update 2 之前的版本更新，您也會看到有可用的維護模式更新 (此訊息可能會在您安裝更新之後繼續顯示長達 24 小時)。
   
   維護模式更新為干擾性更新，會導致裝置產生停機時間，且只能透過您裝置的 Windows PowerShell 介面加以套用。 在某些情況下，當您執行 Update 1.2 時，磁碟韌體可能已經是最新狀態，這種情況下，您不需要安裝任何維護模式更新。
   
   如果您是從 Update 2 更新，您的裝置現在應該是最新狀態。 您可以略過剩餘步驟。
2. 下載維護模式更新，方法是使用 [下載 Hotfix](#to-download-hotfixes) 中列出的步驟來搜尋和下載 KB3121899，它會安裝磁碟韌體更新 (此時應該已經安裝其他更新)。
3. 請遵循 [安裝及驗證維護模式 Hotfix](#to-install-and-verify-maintenance-mode-hotfixes) 中列出的步驟安裝維護模式更新。 

## <a name="install-update-22-as-a-hotfix"></a>以 Hotfix 的方式安裝 Update 2.2
請在嘗試透過 Azure 傳統入口網站安裝更新，而無法通過閘道器檢查時執行此程序。 當您指派閘道器給非 DATA 0 網路介面且您的裝置正在執行早於 Update 1 的軟體版本時，檢查才會失敗。

可以使用 Hotfix 方法升級的軟體版本為：

* Update 0.1、0.2、0.3
* Update 1、1.1、1.2
* Update 2、2.1 

> [!IMPORTANT]
> * 如果您的裝置正在執行 Release (GA) 版本，請連絡 [Microsoft 支援服務](storsimple-contact-microsoft-support.md) 以協助您進行更新。
> 
> 

Hotfix 方法涉及下列三個步驟：

* 從 Microsoft Update Catalog 下載 Hotfix。
* 安裝及驗證定期模式 Hotfix。
* 安裝並確認維護模式 Hotfix (僅限於從 Update 2 之前的軟體更新時)。

#### <a name="download-updates-for-a-device-running-update-21-software"></a>下載適用於執行 Update 2.1 軟體之裝置的更新
**如果您裝置執行的是 Update 2.1**，您必須只下載 KB3179904 裝置軟體更新。 請只安裝開頭為 'all-hcsmdssoftwareudpate' 的二進位檔。 不要安裝開頭為 `all-cismdsagentupdatebundle`的 Cis 和 MDS 代理程式更新。 若沒有這麼做，可能會導致發生錯誤。 這是非干擾性更新，IO 不會中斷，裝置也不會有任何停機時間。

#### <a name="download-updates-for-a-device-running-update-2-software"></a>下載適用於執行 Update 2 軟體的裝置的更新
**如果您裝置執行的是 Update 2**，您就必須以指定的順序下載並安裝下列 Hotfix：

| 順序 | KB | 說明 | 更新類型 | 安裝時間 |
| --- | --- | --- | --- | --- |
| 1. |KB3179904 |軟體更新 &#42; |定期  <br></br>非干擾性 |~ 45 分鐘 |
| 2. |KB3146621 |iSCSI 封裝 |定期  <br></br>非干擾性 |~ 20 分鐘 |
| 3. |KB3103616 |WMI 封裝 |定期  <br></br>非干擾性 |~ 12 分鐘 |

 &#42; *請注意，軟體更新是由兩個二進位檔組成︰開頭為 `all-hcsmdssoftwareupdate` 的裝置軟體更新，以及開頭為 `all-cismdsagentupdatebundle` 的 Cis 和 Mds 代理程式。必須先安裝裝置軟體更新，再安裝 Cis 和 Mds 代理程式。您還必須在套用 Cis 和 MDS 代理程式更新之後，先透過 `Restart-HcsController` Cmdlet 重新啟動作用中的控制器，然後才套用剩餘的更新。* 

#### <a name="download-updates-for-a-device-running-pre-update-2-software"></a>下載適用於執行 Update 2 之前版本軟體的裝置的更新
**如果您裝置執行的版本是 0.2、0.3、1.0 及 1.1**，則除了軟體、iSCSI 及 WMI 更新之外，您還必須下載並安裝 LSI 驅動程式和韌體更新。 如果您是執行 Update 1.2 或 2，則已經安裝此更新。 

| 順序 | KB | 說明 | 更新類型 | 安裝時間 |
| --- | --- | --- | --- | --- |
| 4. |KB3121900 |LSI 驅動程式與韌體 |定期  <br></br>非干擾性 |~ 20 分鐘 |

<br></br>
**如果您裝置執行的版本是 0.2、0.3、1.0、1.1 及 1.2**，您就必須下載並安裝 Spaceport 和 Storport 修正程式。 如果您是執行 Update 2，則已經安裝這些修正程式。

| 順序 | KB | 說明 | 更新類型 | 安裝時間 |
| --- | --- | --- | --- | --- |
| 5. |KB3090322 |Spaceport 修正程式  </br>  Windows Server 2012 R2 |定期  <br></br>非干擾性 |~ 20 分鐘 |
| 6. |KB3080728 |Storport 修正程式  </br>  Windows Server 2012 R2 |定期  <br></br>非干擾性 |~ 20 分鐘 |

<br></br>
您可能也需要安裝磁碟韌體更新。 您可以執行 `Get-HcsFirmwareVersion` Cmdlet 來確認是否需要進行磁碟韌體更新。 如果您執行的是這些韌體版本：`XMGG`、`XGEG`、`KZ50`、`F6C2`、`VR08`，您就不需要安裝這些更新。

| 順序 | KB | 說明 | 更新類型 | 安裝時間 |
| --- | --- | --- | --- | --- |
| 7. |KB3121899 |磁碟韌體 |維護  <br></br>干擾性 |~ 30 分鐘 |

<br></br>

> [!IMPORTANT]
> * 此程序僅需要執行一次，即可套用 Update 2.2。 您可以使用 Azure 傳統入口網站套用後續的更新。
> * 如果您是從 Update 2 更新，總安裝時間會接近 1.5 小時。
> * 使用此程序套用更新之前，請確定兩個裝置控制器都在線上，而且所有硬體元件的狀況良好。
> 
> 

請執行以下步驟來下載及安裝 Hotfix。

[!INCLUDE [storsimple-install-update21-hotfix](../../includes/storsimple-install-update21-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>後續步驟
深入了解 [Update 2.1 版](storsimple-update21-release-notes.md)。




<!--HONumber=Nov16_HO3-->


