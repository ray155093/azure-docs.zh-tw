---
title: "VMware VM 複寫至 Azure (CSP 計劃) 的多租用戶支援 | Microsoft Docs"
description: "描述如何使用 Azure 入口網站在多租用戶環境中部署 Azure Site Recovery，以透過 CSP 計畫協調內部部署 VMware 虛擬機器至 Azure 的複寫、容錯移轉和復原"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: manayar
ms.translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: ed484afc59bbf48490e3ff4389e8e28c71a5e471
ms.contentlocale: zh-tw
ms.lasthandoff: 02/17/2017


---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-the-csp-program"></a>Azure Site Recovery 中的多租用戶支援，適用於透過 CSP 計畫將 VMware 虛擬機器複寫到 Azure

Azure Site Recovery 支援租用戶訂用帳戶的多租用戶環境。 透過 CSP 計畫建立及管理的租用戶訂用帳戶也支援多租用戶。 本文中詳述實作及管理多租用戶 VMware 到 Azure 案例的指南。 同時也詳述透過 CSP 建立及管理租用戶訂用帳戶。

請注意，本指南與複寫 VMware 虛擬機器到 Azure 的現有文件密切相關。 本指南應和該[文件](site-recovery-vmware-to-azure.md)搭配使用。

## <a name="multi-tenant-environments"></a>多租用戶環境
多租用戶模型主要有三種：

1.  **共用主機服務提供者 (HSP)** – 合作夥伴擁有實體基礎結構，而使用者則共用資源 (如 vCenter、資料中心、實體儲存空間等) 以將多租用戶的 VM 裝載在同一個基礎結構。 DR 管理可由合作夥伴使用受管理的服務來提供，或是由租用戶擁有，如同自助 DR 解決方案一樣。
2.  **專用主機服務提供者** – 合作夥伴擁有實體基礎結構，但使用專用資源 (如多個 vCenters、實體資料存放區等) 在個別的基礎結構上裝載每個租用戶的 VM。 DR管理同樣可由合作夥伴管理，或是由租用戶自助。
3.  **受管理的服務提供者 (MSP)** – 客戶擁有裝載 VM 的實體基礎結構，而合作夥伴則提供 DR 支援及管理。

## <a name="shared-hosting-multi-tenant-guidance"></a>共用主機多租用戶指南
本指南詳述共用主機案例。 另外兩個案例是共用主機案例的子集，且使用相同的原則。 共用主機指南最後面會詳述其中的差異。

多租用戶案例中的基本需求是將不同的租用戶隔離，使租用戶無法得知其他租用戶裝載的內容。 這個需求在自助管理環境中，比在完全由合作夥伴管理的環境中來得重要，甚至有重大影響。 本指南假設租用戶隔離是必要條件。

架構如下圖所示︰

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)

**圖 1：含一個 vCenter 的共用主機案例**

如上圖可見，每個客戶將擁有個別的「管理伺服器」。 這樣做是為了將租用戶限制於只能存取租用戶特定 VM，以達到租用戶隔離的目的。 VMware 虛擬機器複寫案例會使用組態伺服器來管理帳戶以探索 VM 及安裝代理程式。 我們會針對多租用戶環境遵循相同的原則，但是另外透過 vCenter 存取控制來限制 VM 探索。

資料隔離的需求會迫使租用戶的基礎結構機密資訊 (如存取認證) 保持公開。 因此，我們建議管理伺服器的所有元件 (組態伺服器 (CS)、處理伺服器 (PS) 和主要目標伺服器 (MT)) 都保持由合作夥伴全權控制。 其中包含相應放大 PS。

### <a name="every-cs-under-the-multi-tenant-scenario-uses-two-accounts"></a>多租用戶案例下的每個 CS 都使用兩個帳戶：

- **vCenter 存取帳戶**：此帳戶是用來探索租用戶，而且會指派 vCenter 存取權限給這個帳戶 (在下節中描述)。 建議合作夥伴自行在設定工具上輸入這些認證，以免意外洩漏這些資訊。
- **虛擬機器存取帳戶**：此帳戶是用來在租用戶上，透過自動推送安裝行動代理程式。 這通常是租用戶可能會提供給合作夥伴，或可能由合作夥伴直接管理的網域帳戶。 如果租用戶不希望直接與合作夥伴分享詳細資料，可以允許租用戶透過限時的 CS 存取權來輸入認證，或者租用戶可以在合作夥伴的協助下手動安裝行動代理程式。

### <a name="requirements-for-vcenter-access-account"></a>vCenter 存取帳戶的需求

如先前章節中所述，必須使用已獲指派特殊角色的帳戶來設定 CS。 請務必注意，必須針對每個 vCenter 物件將此角色指派給 vCenter 存取帳戶，而且不要傳播到子物件。 這也是為了確保租用戶隔離，因為存取傳播可能會導致意外地也存取其他物件

![permissions-without-propagation](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

也可以選擇將使用者帳戶和角色指派到資料中心物件，並傳播到子物件，在此之後必須指派「無存取權」角色給帳戶，使其無法存取特定租用戶不應存取的每一個物件 (例如其他租用戶的 VM)。 這樣不但繁瑣，也可能意外公開存取控制，因為會對每個新建立的子物件自動授與從父物件繼承的存取權。 因此建議採用第一種方法。

vCenter 帳戶存取程序如下：

1.  藉由複製預先定義的「唯讀」角色來建立新的角色，並以易記名稱 (例如此範例中使用的 Azure_Site_Recovery) 命名。
2.  將下列權限指派給這個角色：
 *  資料存放區 -> 配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案
 *  網路 -> 網路指派
 *  資源 -> 指派 VM 至資源集區、移轉已關閉電源的 VM、移轉已開啟電源的 VM
 *  工作 -> 建立工作、更新工作
 *  虛擬機器 -> 組態
 *  虛擬機器 -> 互動 -> 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝
 *  虛擬機器 -> 清查 -> 建立、註冊、取消註冊
 *  虛擬機器 -> 佈建 -> 允許虛擬機器下載、允許虛擬機器檔案上傳
 *  虛擬機器 -> 快照 -> 移除快照。

    ![role-permissions](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3.  針對不同物件，指派存取層級給 vCenter 帳戶 (在 CS 中使用)：

| **Object** | **角色** | **備註** |
| --- | --- | --- |
| vCenter | 唯讀 | 這只是為了允許 vCenter 存取以管理其他物件。 如果帳戶不會提供給租用戶，或用於任何 vCenter 上的管理作業，則可以移除此權限。 |
| 資料中心 | Azure_Site_Recovery |  |
| 主機和主機叢集 | Azure_Site_Recovery | 再次確認存取權是在物件層級，以確認只能存取那些在容錯移轉前後將會有租用戶 VM 的主機。 |
| 資料存放區和資料存放區叢集 | Azure_Site_Recovery | 同上 |
| 網路 | Azure_Site_Recovery |  |
| 管理伺服器 | Azure_Site_Recovery | 如果有任何元件在 CS 機器以外，這就會包括所有元件 (CS、PS 及 MT) 的存取權。 |
| 租用戶 VM | Azure_Site_Recovery | 確保特定租用戶的任何新租用戶 VM 也會有此存取權，否則將無法透過 Azure 入口網站探索它們。 |

現在已經完成 vCenter 帳戶存取權。 這可以滿足完成容錯回復作業的最小權限需求。 請注意，這些存取權限也可以和您現有的原則搭配使用。 只要將現有的權限集修改成包含上面第 2 點詳述的角色權限即可。

若要限制 DR 作業僅進行到容錯移轉狀態，而沒有容錯回復的功能，請遵循上述程序，但是不要指派「Azure_Site_Recovery」給 vCenter 存取帳戶，而是改為只指派「唯讀」角色。 這個權限集合會允許 VM 複寫和容錯移轉，而不允許容錯回復。 請注意，以上程序的其餘部分都保持原狀。 所有權限仍僅指派到物件層級而未傳播到子物件，以確保租用戶隔離及限制 VM 探索。

## <a name="other-multi-tenant-environments"></a>其他多租用戶環境

上述指南詳細描述如何針對共用主機解決方案設定多租用戶環境。 另外兩個主要解決方案是專用主機和受管理的服務。 它們的架構如下：

### <a name="dedicated-hosting-solution"></a>專用主機解決方案

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)

**圖 2：含多個 vCenters 的專用主機案例**

此處的架構差異是每個租用戶的基礎結構都是只針對該租用戶所佈建的。 主機提供者仍需要按照針對共用主機詳述的 CSP 步驟來執行，但不需要擔心租用戶隔離問題，因為租用戶已經透過個別的 vCenter 來隔離。 CSP 佈建保持不變。

### <a name="managed-service-solution"></a>受管理的服務解決方案

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)

**圖 3：含多個 vCenters 的受管理的服務案例**

此處的架構差異是每個租用戶的基礎結構也都是在實體上和其他租用戶分開。 此案例通常是在租用戶擁有基礎結構時存在，而且只需要解決方案提供者來管理 DR。 合作夥伴仍需要按照針對共用主機詳述的 CSP 步驟來執行，但不需要擔心租用戶隔離問題，因為租用戶已經在實體上透過不同的基礎結構來隔離。 CSP 佈建保持不變。


## <a name="csp-program-overview"></a>CSP 計畫概觀
Microsoft 的雲端解決方案提供者 (CSP) [計畫](https://partner.microsoft.com/en-US/cloud-solution-provider)致力於和合作夥伴合作提供所有 Microsoft 雲端服務 (包括 O365、EMS 和 Microsoft Azure) 來達到雙贏。 該計畫能使我們的合作夥伴全面掌握與客戶的端對端關係，並且成為主要的關係連絡窗口。 透過 CSP，合作夥伴可以為客戶部署 Azure 訂用帳戶，並將這些訂用帳戶與他們自己的加值自訂優惠結合。

在 Azure Site Recovery 的情況下，合作夥伴可以直接透過 CSP 為客戶管理整個「災害復原」解決方案，或者使用 CSP 來設定 Azure Site Recovery 環境，並且讓客戶以自助方式管理自己的 DR 需求。 在這兩種情況下，合作夥伴是 Azure Site Recovery 和最終客戶之間的連絡窗口，且合作夥伴負責維護客戶關係，以及向客戶收取 Azure Site Recovery 使用量的費用。

## <a name="creating-and-managing-tenant-accounts"></a>建立及管理租用戶帳戶

### <a name="step-0-prerequisite-check"></a>步驟 0：必要條件檢查

VM 的必要條件和 Azure Site Recovery [文件](site-recovery-vmware-to-azure.md)中所述相同。 除了這些必要條件之外，也應該要先設定好上述存取控制，才能透過 CSP 進行租用戶管理。 針對每個租用戶建立可以和租用戶 VM 及合作夥伴的 vCenter 通訊的個別「管理伺服器」。 只有合作夥伴有此伺服器的存取權限。

### <a name="step-1-create-tenant-account"></a>步驟 1︰建立租用戶帳戶

1.  透過[合作夥伴中心](https://partnercenter.microsoft.com/)登入您的 CSP 帳戶。 在左側的 [儀表板] 功能表上，選取 [客戶] 選項。

    ![csp-dashboard](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

2.  在開啟的頁面上，按一下 [新增客戶] 按鈕。

    ![add-customer](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

3.  在 [新客戶] 頁面上，填入租用戶的所有帳戶資訊詳細資訊，然後按一下 [下一步: 訂閱]。

    ![fill-details](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

4.  在選取訂閱的頁面上，向下捲動以新增 [Microsoft Azure] 訂用帳戶。 現在可以新增其他訂用帳戶，或在未來任何時候新增。

    ![add-subscription](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

5.  繼續並在下一頁檢閱為租用戶輸入的所有詳細資料，然後按一下 [送出] 按鈕。

    ![customer-summary](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)

6.  建立客戶之後，您會看到確認頁面，其中包含預設帳戶的詳細資料，以及該訂用帳戶的密碼。 儲存資訊並在稍後視需要透過 Azure 入口網站登入來變更密碼。 您可以和租用戶直接共用這項資訊，或者也可以視需要建立個別帳戶並共用。

### <a name="step-2-access-tenant-account"></a>步驟 2︰存取租用戶帳戶

1.  您可以透過儀表板從 [客戶] 頁面存取租用戶的訂用帳戶，如步驟 1 中所述。 在此頁面瀏覽，然後按一下剛才建立的租用戶帳戶的名稱。
2.  這會開啟租用戶帳戶的 [訂用帳戶] 區段，您可以在此監視該帳戶現有的訂用帳戶，並且視需要新增更多訂用帳戶。 若要管理租用戶的 DR 作業，請選取頁面右側的 [所有資源] \(Azure 入口網站) 選項。

    ![all-resources](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)

3.  按一下 [所有資源] 按鈕會授與您對該租用戶之 Azure 訂用帳戶的存取權，您可以檢查 Azure 入口網站右上角顯示的 AAD 來驗證相同事項。

    ![aad-admin](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

您現在可以透過 Azure 入口網站為租用戶執行所有 Site Recovery 作業，以及管理 DR 作業。 每次透過 CSP 存取租用戶訂用帳戶來管理 DR 時都必須遵循上面詳述的程序。

### <a name="step-3-deploy-resources-to-tenant-subscription"></a>步驟 3：將資源部署到租用戶訂用帳戶
1.  在 Azure 入口網站上，建立「資源群組」，並於每個一般程序部署「復原服務」保存庫。 下載保存庫註冊金鑰。
2.  使用保存庫註冊金鑰為租用戶註冊 CS。
3.  輸入兩個存取帳戶 (vCenter 存取帳戶和 VM 存取帳戶) 的認證。

    ![config-accounts](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-recovery-services-vault"></a>步驟 4：向復原服務保存庫註冊站台復原基礎結構
1.  開啟 Azure 入口網站並在之前建立的保存庫上，向在上一步中註冊的 CS 註冊 vCenter 伺服器。 將 vCenter 存取帳戶用於此目的。
2.  針對每個一般程序的 Site Recovery 完成「準備基礎結構」程序。
3.  現在可以開始複寫 VM。 確認在 [複寫] 選項下的 VM 選取刀鋒視窗上，只有顯示該租用戶的 VM。

    ![tenant-vms](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-subscription"></a>步驟 5︰將對訂用帳戶的存取權指派給租用戶

在自助 DR 的情況下，必須向租用戶提供在步驟 1 之項目 6 中所提的帳戶詳細資料。 此動作應在合作夥伴設定 DR 基礎結構之後完成。 不論是何種 DR 類型 (受管理或自助)，都會要求合作夥伴只能透過 CSP 入口網站來存取租用戶訂用帳戶，並設定保存庫，以及向租用戶訂用帳戶註冊合作夥伴所擁有的基礎結構。

合作夥伴也可以透過 CSP 入口網站將新的使用者加入租用戶訂用帳戶，方法如下：

1.  移至特定租用戶的 CSP 訂用帳戶頁面，並選取 [使用者與授權] 選項。

    ![user-licenses](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    您現在可以輸入相關詳細資料及選取權限以建立新的使用者，或選擇透過 CSV 檔案上傳使用者清單。
2.  建立使用者之後，返回 Azure 入口網站並在 [訂用帳戶] 刀鋒視窗下選取相關訂用帳戶。
3.  在開啟的新刀鋒視窗上選取存取控制 (IAM)，然後按一下 [+新增] 以使用相關的存取層級新增使用者。 透過 CSP 入口網站建立的使用者會自動顯示在按一下存取層級之後所開啟的刀鋒視窗中。

    ![user-subscription](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    「參與者」角色就足以執行多數的管理作業。 除了變更存取層級 (必須具有「擁有者」存取層級) 之外，具有此存取層級的使用者可以在訂用帳戶上執行任何作業。 您也可以視需要微調存取層級。

