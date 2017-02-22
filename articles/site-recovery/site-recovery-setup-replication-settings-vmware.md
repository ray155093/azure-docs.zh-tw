---
title: "設定 Azure Site Recovery 的複寫設定 | Microsoft Docs"
description: "說明如何部署 Site Recovery，以協調將 VMM 雲端中的 Hyper-V VM 複寫、容錯移轉和復原至 Azure。"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/19/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 74d51269cdd55e39eaa2963fec2b409decb47d0a
ms.openlocfilehash: a279fdc35c62c76e6f0858d52c64240032669f15


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>管理 VMware 至 Azure 的複寫原則


## <a name="create-a-new-replication-policy"></a>建立新的複寫原則

1. 在左側功能表按一下 [管理 -> Site Recovery 基礎結構]。 
2. 在 [VMware 和實體機器] 區段下選取 [複寫原則]。
3. 按一下頂端的 [+複寫原則]。

    ![建立複寫原則](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. 輸入原則名稱。

5. 在 [RPO 臨界值] 中，指定 RPO 限制。 連續複寫超過此限制時，會產生警示。
6. 在 [復原點保留] 中，針對每個復原點指定保留週期的長度 (以小時為單位)。 受保護的機器可以復原到週期內的任意點。 

    > [!NOTE] 
    > 複寫至進階儲存體的機器最多支援保留 24 小時，複寫至標準儲存體的機器最多支援保留 72 小時。
    
    > [!NOTE] 
    > 將會自動建立容錯回復的複寫原則。

7. 在 [應用程式一致性快照集頻率] 中，指定每隔多久建立包含應用程式一致性快照集的復原點 (以分鐘為單位)。

8. 按一下 [確定]。 應該會在大約 30 秒至 1 分鐘內建立原則。

![建立複寫原則](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-configuration-server-with-replication-policy"></a>使設定伺服器與複寫原則產生關聯
1. 按一下要與設定伺服器產生關聯的複寫原則。
2. 按一下頂端的 [關聯]。
![建立複寫原則](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. 從伺服器清單中選取 [設定伺服器]。
4. 按一下 [確定]。 應該會在大約 1 至 2 分鐘內與設定伺服器產生關聯。

![建立複寫原則](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-replication-policy"></a>編輯複寫原則
1. 按一下您要編輯複寫設定的複寫原則。
![建立複寫原則](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. 按一下頂端的 [編輯設定]。
![建立複寫原則](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. 根據需要來變更設定。
4. 按一下頂端的 [儲存]。 根據多少 VM 使用該複寫原則而定，應該會在大約 2 至 5 分鐘內儲存原則。

![建立複寫原則](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-configuration-server-from-replication-policy"></a>使設定伺服器與複寫原則中斷關聯
1. 按一下要與設定伺服器產生關聯的複寫原則。
2. 按一下頂端的 [中斷關聯]。
3. 從伺服器清單中選取 [設定伺服器]。
4. 按一下 [確定]。 應該會在大約 1 至 2 分鐘內與設定伺服器中斷關聯。
    
    > [!NOTE] 
    > 如果至少有一個複寫項目使用該原則，您無法與設定伺服器中斷關聯。 與設定伺服器中斷關聯之前，請確定沒有複寫項目使用該原則。

## <a name="delete-replication-policy"></a>刪除複寫原則 

1. 按一下您想要刪除的複寫原則。
2. 按一下 [刪除]。 應該會在大約 30 秒至 1 分鐘內刪除原則。

    > [!NOTE] 
    > 如果複寫原則至少有 1 個相關聯的設定伺服器，您無法刪除該複寫原則。 請確定沒有複寫項目使用該原則，然後刪除所有相關聯的設定伺服器，再刪除原則。


<!--HONumber=Jan17_HO4-->


