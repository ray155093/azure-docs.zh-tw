---
title: "使用 Azure 備份保護混合式備份的安全性功能 | Microsoft Docs"
description: "了解如何使用 Azure 備份的安全性功能，讓備份更安全"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2016
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 4a846e86fa8cefbee04e1e30078a0d2aabdd834d
ms.openlocfilehash: 1b7df71f103e8284bdcc7bc3a27fe44815b12305


---
# <a name="security-features-for-protecting-hybrid-backups-using-azure-backup"></a>使用 Azure 備份保護混合式備份的安全性功能
越來越多客戶會遇到惡意軟體、勒索軟體、入侵等安全性問題。這些安全性問題會導致資料遺失，並不斷增加每個安全性缺口的成本。 為了防範這類攻擊，Azure 備份現在提供安全性功能來保護混合式備份。 本文討論如何透過 Microsoft Azure 復原服務代理程式和 Microsoft Azure 備份伺服器來啟用和利用這些功能。 這些功能是以三大安全性要件為基礎︰

1. **防護** - 每當執行重要作業 (如變更複雜密碼) 時，就會新增額外的驗證。 這項驗證用來確保只有具備有效 Azure 認證的使用者可以執行這類作業。
2. **警示** - 每當執行重要作業 (如刪除備份資料) 時，就會傳送電子郵件通知給訂用帳戶管理員。 這封電子郵件可確保使用者及時獲知這類動作。
3. **復原** - 已刪除的備份資料會額外保留 14 天 (從刪除日期算起)。 這可確保指定時間週期內的資料復原能力，所以即使發生攻擊，也不會遺失任何資料。 而且會維護更多的復原點，以防範資料損毀。

> [!NOTE]
> 只有當您使用下列項目，才應該啟用安全性功能︰ <br/>
> * **MAB 代理程式** - 至少為代理程式 2.0.9052 版。 一旦啟用這些功能，您就應該升級為此代理程式版本，以執行重要作業，例如變更複雜密碼、停止備份並刪除資料 <br/>
> * **Azure 備份伺服器** - 至少為 MAB 代理程式 2.0.9052 版與 Azure 備份伺服器 Update 1 <br/>
> * **DPM** - 不要對 DPM 啟用這些功能。 這些功能即將在未來的 UR 中提供，所以啟用它們不會對現有功能造成任何影響。 <br/>
> * **IaaS VM 備份** - 不要對 IaaS VM 備份啟用這些功能。 這些功能尚無法用於 IaaS VM 備份，所以啟用它們不會對 IaaS VM 備份造成任何影響。
> * 一旦啟用，您即可取得所有向保存庫註冊之 Azure 復原服務代理程式 (MARS) 電腦和 Azure 備份伺服器的安全性功能。 <br/>
> * 啟用此設定是一次性動作，啟用之後便無法停用這些功能。 <br/>
> * 這項功能僅適用於復原服務保存庫。
>
>

## <a name="enabling-security-features"></a>啟用安全性功能
建立復原服務保存庫的使用者能夠使用所有的安全性功能。 對於現有的復原服務保存庫，應使用下列步驟來啟用這些功能︰

1. 使用 Azure 認證登入 Azure 入口網站
2. 在中樞功能表中輸入 Recovery Services，以瀏覽至復原服務清單。

    ![建立復原服務保存庫的步驟 1](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    復原服務保存庫清單隨即出現。 從此清單中選取保存庫。

    選取的保存庫儀表板隨即開啟。
3. 從保存庫下顯示的項目清單中，按一下 [設定] 之下的 [屬性]。

    ![開啟保存庫屬性](./media/backup-azure-security-feature/vault-list-properties.png)
4. 按一下 [安全性設定] 之下的 [更新]。

    ![開啟安全性設定](./media/backup-azure-security-feature/security-settings-update.png)

    [更新] 連結會開啟 [安全性設定] 刀鋒視窗，以便您啟用這些功能並提供功能的摘要。
5. 從 [是否已設定 Azure Multi-Factor Authentication？] 的下拉式清單中選取一個值，確認您是否已啟用 [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)。 如果已啟用，系統會要求您在登入 Azure 入口網站時從另一個裝置 (例如行動電話) 進行驗證。

   在 Azure 備份中執行重要作業時，您必須輸入 Azure 入口網站上可用的 [安全性 PIN 碼]，這是安全性功能的一部分。 啟用 Azure Multi-Factor Authentication 可多加一層安全性，確保只有獲得授權且具備有效 Azure 認證並從第二個裝置進行驗證的使用者可以存取 Azure 入口網站並執行這類重要作業。
6. 使用切換按鈕來 [啟用] 並按一下頂端的 [儲存] 來儲存安全性設定，如下圖所示。 您只能在從 [是否已設定 Azure Multi-Factor Authentication？] 的下拉式清單中選取值之後， 選取 [啟用]。

    ![啟用安全性設定](./media/backup-azure-security-feature/enable-security-settings.png)

## <a name="recovering-deleted-backup-data"></a>復原已刪除的備份資料
基於安全性考量，如果已執行「停止備份並刪除備份資料」作業，Azure 備份會保留已刪除的備份資料額外 14 天，並不會立即將它刪除。 若要在 14 天的期間內還原此資料，請使用下列步驟︰

若為 **Microsoft 復原服務代理程式 (MARS)** 使用者：

1. 如果發生備份的電腦仍可使用，請使用 MARS 中的[將資料復原到相同電腦](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-the-same-machine)，以便從舊的復原點復原資料。
2. 如果無法使用上述的電腦，請使用[復原到其他電腦](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)，以使用另一部 MARS 電腦來取得此資料。

若為 **Azure 備份伺服器**使用者：

1. 如果發生備份的伺服器仍可使用，請重新保護已刪除的資料來源，並使用復原資料功能來從舊的復原點復原資料。
2. 如果無法使用上述的電腦，請使用[從其他 Azure 備份伺服器復原資料](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server)，以使用另一部 Azure 備份伺服器來取得此資料。

## <a name="preventing-attacks"></a>預防攻擊
這項功能已新增各種檢查，確保只有有效的使用者可以執行各種作業。

### <a name="authentication-to-perform-critical-operations"></a>用來執行重要作業的驗證
針對重要作業新增額外一層驗證時，系統會提示您在執行「停止保護並刪除資料」和「變更複雜密碼」作業時輸入 [安全性 PIN 碼]。

若要接收 [安全性 PIN 碼]，請使用下列步驟：

1. 登入 Azure 入口網站。
2. 瀏覽至 [復原服務保存庫] > [設定] > [屬性]。
3. 按一下 [安全性 PIN 碼] 底下的 [產生]。 [產生] 連結會開啟一個刀鋒視窗，其中包含要在 Azure 復原服務代理程式 UI 中輸入的 [安全性 PIN 碼]。
    此 PIN 碼的有效時間只有 5 分鐘，而且會在該期間後自動產生。

### <a name="maintaining-minimum-retention-range"></a>維護最小的保留範圍
為了確保永遠都有有效的復原點可用，已新增下列檢查︰

1. 若為每日保留，最少應該保留**七**天
2. 若為每週保留，最少應該保留**四**週
3. 若為每月保留，最少應該保留**三**個月
4. 若為每年保留，最少應該保留**一**年

## <a name="notifications-for-critical-operations"></a>重要作業的通知
每當執行一些重要作業時，訂用帳戶管理員會收到一封含有作業相關詳細資訊的電子郵件通知。 如果您想要設定其他電子郵件 ID 來接收電子郵件通知，您可以使用 Azure 入口網站加以設定。

本文所提的安全性功能會提供目標攻擊的防禦機制，以防止攻擊者接觸備份。 更重要的是，這些功能會在發生攻擊時提供復原資料的能力。

## <a name="next-steps"></a>後續步驟
* [開始使用 Azure 復原服務保存庫](backup-azure-vms-first-look-arm.md)以啟用這些功能
* [下載最新的 Azure 復原服務代理程式](http://aka.ms/azurebackup_agent)以保護 Windows 電腦及防護備份資料以免遭受攻擊
* [下載最新的 Azure 備份伺服器](https://aka.ms/latest_azurebackupserver)以保護工作負載及防護備份資料以免遭受攻擊



<!--HONumber=Feb17_HO1-->


