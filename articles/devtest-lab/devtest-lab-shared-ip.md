---
title: "了解 Azure DevTest Labs 中的共用 IP 位址 | Microsoft Docs"
description: "了解 Azure DevTest Labs 如何使用共用 IP 位址，將需要存取您的實驗室 VM 的公用 IP 位址減到最少。"
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 905357b9e2262b86cde31874287cc0b89eef4815
ms.lasthandoff: 03/17/2017

---

# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>了解 Azure DevTest Labs 中的共用 IP 位址

Azure DevTest Labs 使用共用 IP 位址，將需要存取您的個人實驗室 VM 的公用 IP 位址數目減到最少。  本文說明共用 IP 的運作方式和其相關的組態選項。

## <a name="shared-ip-setting"></a>共用 IP 設定

當您建立實驗室時，它會位於虛擬網路的子網路。  根據預設，建立這個子網路時會將 [啟用共用公用 IP]設為 [是]。  此設定會為整個子網路建立一個公用 IP 位址。  如需設定虛擬網路和子網路的詳細資訊，請參閱[設定 Azure DevTest Labs 中的虛擬網路](devtest-lab-configure-vnet.md)。

![新的實驗室子網路](media/devtest-lab-shared-ip/lab-subnet.png)

此實驗室中建立任何 VM 會預設為使用共用 IP。  建立 VM 時，此設定會出現在 [進階設定] 刀鋒視窗的 [IP 位址組態] 之下。

![新的 VM](media/devtest-lab-shared-ip/new-vm.png)

每當已啟用共用 IP 的 VM 新增至子網路時，就會在公用 IP 位址上指派 TCP 通訊埠，以轉送至 VM 上的 RDP 連接埠。  

## <a name="using-the-shared-ip"></a>使用共用 IP

在 RDP 用戶端中，您可以使用 IP 位址或完整網域名稱，後面緊接冒號，再接著連接埠，以連線至 VM 上的遠端桌面。  例如，在下圖中，連線至 VM 的 RDP 位址是 `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`。  或者，從 Azure 入口網站中，選取 [連線] 按鈕，下載預先設定的 RDP 檔案。

![VM 範例](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>後續步驟

* [在 Azure DevTest Labs 中定義實驗室原則](devtest-lab-set-lab-policy.md)
* [在 Azure DevTest Labs 中設定虛擬網路](devtest-lab-configure-vnet.md)






