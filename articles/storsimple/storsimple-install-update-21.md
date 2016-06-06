<properties
   pageTitle="在 StorSimple 裝置上安裝 Update 2.1 | Microsoft Azure"
   description="說明如何在您的 StorSimple 8000 系列裝置上安裝 StorSimple 8000 系列 Update 2.1。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/25/2016"
   ms.author="alkohli" />

# 在 StorSimple 裝置上安裝 Update 2.1

## 概觀

本教學課程說明如何透過 Azure 傳統入口網站即使用 Hotfix 方法，在執行舊版軟體的 StorSimple 裝置上安裝 Update 2.1。當閘道器是設定於 StorSimple 裝置之 DATA 0 以外的網路介面上，且您正嘗試從 Update 1 以前的軟體版本更新時，就會使用 Hotfix 方法。

Update 2.1 包括裝置軟體、WMI 及 iSCSI 更新。如果是從 Update 2 以前的版本更新，您也將需要套用 LSI 驅動程式、Spaceport、Storport 及磁碟韌體更新。裝置軟體、WMI、iSCSI、LSI 驅動程式、Spaceport 及 Storport 修正程式為非干擾性更新，且可透過 Azure 傳統入口網站套用。磁碟韌體更新為干擾性更新，且只能透過裝置的 Windows PowerShell 介面套用。

> [AZURE.IMPORTANT]

> -  您可能不會立即看到 Update 2.1，因為我們會分階段推出更新。請在數天內再次掃描更新，因為很快就會提供 Update。
> - 安裝前會執行一組手動和自動預先檢查，以根據硬體狀態和網路連線來判斷裝置健全狀況。這些預先檢查只會在您從 Azure 傳統入口網站套用更新時執行。
> - 建議您透過 Azure 傳統入口網站安裝軟體和驅動程式更新。如果入口網站中的更新前閘道器檢查失敗，請移至裝置的 Windows PowerShell 介面安裝更新 (勿透過其他方式)。視您從哪一個版本更新而定，可能需要 1.5-2.5 小時來安裝更新。維護模式更新必須透過裝置的 Windows PowerShell 介面安裝。由於維護模式更新是干擾性更新，它們將會導致裝置的停機時間。
> - 如果執行選擇性的 StorSimple Snapshot Manager，更新裝置之前，請先將您的 Snapshot Manager 版本升級至 Update 2.1

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## 透過 Azure 傳統入口網站安裝 Update 2.1

請執行下列步驟，以將您的裝置更新至 [Update 2.1](storsimple-update21-release-notes.md)。


> [AZURE.NOTE]
如果您是要套用 Update 2 或更新版本 (包括 Update 2.1)，Microsoft 將可以提取裝置的其他診斷資訊。因此，當我們的作業小組識別有問題的裝置時，我們更有能力從裝置收集資訊並診斷問題。接受 Update 2 或更新版本，表示您允許我們提供此主動支援。

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. 確認您的裝置是否正在執行 **StorSimple 8000 Series Update 2.1 (6.3.9600.17705)**。[**上次更新日期**] 應該也已修改。 

	如果您是從 Update 2 之前的版本更新，您也會看到有可用的維護模式更新 (此訊息可能會在您安裝更新之後繼續顯示長達 24 小時)。

    維護模式更新為干擾性更新，會導致裝置產生停機時間，且只能透過您裝置的 Windows PowerShell 介面加以套用。在某些情況下，當您執行 Update 1.2 時，磁碟韌體可能已經是最新狀態，這種情況下，您不需要安裝任何維護模式更新。

	如果您是從 Update 2 更新，您的裝置現在應該是最新狀態。您可以略過剩餘步驟。

13. 使用[下載 Hotfix](#to-download-hotfixes) 中列出的步驟，下載維護模式更新，以搜尋和下載 KB3121899，它會安裝磁碟韌體更新 (現在其他更新應該已安裝)。

13. 請遵循[安裝及驗證維護模式 Hotfix](#to-install-and-verify-maintenance-mode-hotfixes) 中列出的步驟安裝維護模式更新。

  

## 以 Hotfix 的方式安裝 Update 2.1

請在嘗試透過 Azure 傳統入口網站安裝更新，而無法通過閘道器檢查時執行此程序。當您指派閘道器給非 DATA 0 網路介面且您的裝置正在執行早於 Update 1 的軟體版本時，檢查才會失敗。

可以使用 Hotfix 方法升級的軟體版本為：

- Update 0.1、0.2、0.3
- Update 1、1.1、1.2
- Update 2 

> [AZURE.IMPORTANT]
>
> - 如果您的裝置正在執行 Release (GA) 版本，請連絡 [Microsoft 支援服務](storsimple-contact-microsoft-support.md)以協助您進行更新。

Hotfix 方法涉及下列三個步驟：

- 從 Microsoft Update Catalog 下載 Hotfix。
- 安裝及驗證定期模式 Hotfix。
- 安裝並確認維護模式 Hotfix (僅限於從 Update 2 之前的軟體更新時)。

#### 下載適用於執行 Update 2 軟體的裝置的更新

**如果您的裝置是執行 Update 2**，您必須以指定順序下載並安裝下列 Hotfix：

| 順序 | KB | 說明 | 更新類型 | 安裝時間 |
|--------|-----------|-------------------------|------------- |-------------|
| 1\. | KB3162954 | 軟體更新 &#42; | 定期 | ~ 45 分鐘 |
| 2\. | KB3146621 | iSCSI 封裝 | 定期 | ~ 20 分鐘 |
| 3\. | KB3103616 | WMI 封裝 | 定期 | ~ 12 分鐘 |


 &#42; *請注意，軟體更新是由兩個二進位檔組成︰`all-hcsmdssoftwareupdate_d5db7c7a86fc0fffd7fd7e8a1b58584ca4850936.exe` 和 `all-cismdsagentupdatebundle_a3b6e721045c9229f62ffe3374fb5715bf3699e3.exe`。必須先安裝裝置軟體更新 `all-hcsmdssoftwareupdate_d5db7c7a86fc0fffd7fd7e8a1b58584ca4850936.exe`，再安裝 Cis 和 Mds 代理程式 `all-cismdsagentupdatebundle_a3b6e721045c9229f62ffe3374fb5715bf3699e3.exe`。*

#### 下載適用於執行 Update 2 之前版本軟體的裝置的更新

**如果您的裝置是執行 0.2、0.3、1.0 及 1.1 版**，您必須下載並安裝 LSI 驅動程式與韌體更新。如果您是執行 Update 1.2 或 2，則已經安裝此更新。
 
| 順序 | KB | 說明 | 更新類型 | 安裝時間 |
|--------|-----------|-------------------------|------------- |-------------|
| 4\. | KB3121900 | LSI 驅動程式與韌體 | 定期 | ~ 20 分鐘 |


<br></br> **如果您的裝置是執行 0.2、0.3、1.0、1.1 及 1.2 版**，您必須下載並安裝 Spaceport 和 Storport 修正程式。如果您是執行 Update 2，則已經安裝這些修正程式。

| 順序 | KB | 說明 | 更新類型 | 安裝時間 |
|--------|-----------|-------------------------|------------- |-------------|
| 5\. | KB3090322 | Spaceport 修正程式 </br> Windows Server 2012 R2 | 定期 | ~ 20 分鐘 |
| 6\. | KB3080728 | Storport 修正程式 </br> Windows Server 2012 R2 | 定期 | ~ 20 分鐘 |



<br></br>您也需要安裝磁碟韌體更新。您可以執行 `Get-HcsFirmwareVersion` Cmdlet 來確認是否需要磁碟韌體更新。如果您是執行這些韌體版本：`XMGG`、`XGEG`、`KZ50`、`F6C2`、`VR08`，您就不需要安裝這些更新。


| 順序 | KB | 說明 | 更新類型 | 安裝時間 |
|--------|-----------|-------------------------|------------- |-------------|
| 7\. | KB3121899 | 磁碟韌體 | 維護 | ~ 30 分鐘 |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - 此程序僅需要執行一次，即可套用 Update 2.1。您可以使用 Azure 傳統入口網站套用後續的更新。
> - 如果您是從 Update 2 更新，總安裝時間會接近 1.5 小時。
> - 使用此程序套用更新之前，請確定兩個裝置控制器都在線上，而且所有硬體元件的狀況良好。

請執行以下步驟來下載及安裝 Hotfix。

[AZURE.INCLUDE [storsimple-install-update21-hotfix](../../includes/storsimple-install-update21-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## 後續步驟

深入了解 [Update 2.1 版](storsimple-update21-release-notes.md)。

<!---HONumber=AcomDC_0525_2016-->