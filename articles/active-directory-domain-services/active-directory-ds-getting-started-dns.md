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
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: abb27292d4b5533fe6f3d66d6921fea8c82f18dd
ms.lasthandoff: 04/12/2017


---
# <a name="update-dns-settings-for-the-azure-virtual-network"></a>更新 Azure 虛擬網路的 DNS 設定
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>工作 4 - 更新 Azure 虛擬網路的 DNS 設定
在先前的組態工作中，您已成功為目錄啟用 Azure Active Directory Domain Services。 下一個工作是確保虛擬網路內的電腦可以連線並取用這些服務。 在本文中，您可以更新虛擬網路的 DNS 伺服器設定，以指向虛擬網路上可以使用 Azure Active Directory Domain Services 的兩個 IP 位址。

> [!NOTE]
> 在您已啟用目錄的 Azure Active Directory Domain Services 之後，請記下在您目錄的 [設定] 索引標籤上顯示之 Azure Active Directory Domain Services 的 IP 位址。
>
>

若要為已啟用 Azure Active Directory Domain Services 的虛擬網路更新 DNS 伺服器設定，請執行下列作業︰

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
> 在更新虛擬網路的 DNS 伺服器設定之後，可能需要一段時間，網路上的虛擬機器才能取得已更新的 DNS 組態。 如果虛擬機器無法連線到網域，您可以排清虛擬機器上的 DNS 快取 ('ipconfig /flushdns')。 此命令會強制重新整理虛擬機器上的 DNS 設定。
>
>

## <a name="next-steps"></a>後續步驟
工作 5：[啟用 Azure Active Directory Domain Services 的密碼同步處理](active-directory-ds-getting-started-password-sync.md)

