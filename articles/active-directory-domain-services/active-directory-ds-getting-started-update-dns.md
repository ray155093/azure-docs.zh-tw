---
title: "Azure Active Directory Domain Services：更新 Azure 虛擬網路的 DNS 設定 | Microsoft Docs"
description: "開始使用 Azure Active Directory 網域服務"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/27/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 8bee2a25f196d645b27f30f21305b1550e44e07a
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017


---
<a id="update-dns-settings-for-the-azure-virtual-network" class="xliff"></a>

# 更新 Azure 虛擬網路的 DNS 設定
<a id="task-4-update-dns-settings-for-the-azure-virtual-network" class="xliff"></a>

## 工作 4 - 更新 Azure 虛擬網路的 DNS 設定
在先前的組態工作中，您已成功為目錄啟用 Azure Active Directory Domain Services。 下一個工作是確保虛擬網路內的電腦可以連線並取用這些服務。 在本文中，您可以更新虛擬網路的 DNS 伺服器設定，以指向虛擬網路上可以使用 Azure Active Directory Domain Services 的兩個 IP 位址。

> [!NOTE]
> 在您已啟用目錄的 Azure Active Directory Domain Services 之後，請記下在您目錄的 [設定] 索引標籤上顯示之 Azure Active Directory Domain Services 的 IP 位址。
>
>

若要為已啟用 Azure Active Directory Domain Services 的虛擬網路更新 DNS 伺服器設定，請完成下列步驟︰

1. 前往 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 在左側窗格中，選取 [網路]。  
    隨即開啟 [網路] 視窗。

    ![虛擬網路視窗](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. 在 [虛擬網路] 索引標籤上，選取已啟用 Azure Active Directory Domain Services 的虛擬網路，以檢視其內容。
4. 按一下 [設定]  索引標籤。

    ![虛擬網路視窗](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. 已在 [DNS 伺服器] 區段中，輸入您目錄的 [設定] 索引標籤之 [網域服務] 中所顯示的兩個 IP 位址。
6. 若要儲存此虛擬網路的 DNS 伺服器設定，請在頁面底部之工作窗格上按一下 [儲存]。

   ![更新虛擬網路的 DNS 伺服器設定](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
>  網路中的虛擬機器只會在重新啟動後取得新的 DNS 設定。 如果您希望它們立即取得更新後的 DNS 設定，請透過入口網站、PowerShell 或 CLI 觸發重新啟動。
>
>

<a id="next-steps" class="xliff"></a>

## 後續步驟
工作 5：[啟用 Azure Active Directory Domain Services 的密碼同步處理](active-directory-ds-getting-started-password-sync.md)

