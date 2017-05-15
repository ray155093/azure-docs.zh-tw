---
title: "Linux VM 安全性與 Azure 資訊安全中心 | Microsoft Docs"
description: "教學課程 - VM 安全性與 Azure 資訊安全中心"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 4c680ee63e1c2d8e858c725adc42bbcbc49e4045
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017

---
# <a name="monitor-vm-security-with-the-azure-security-center"></a>透過 Azure 資訊安全中心監視 VM 安全性

Azure 資訊安全中心可協助您了解與安全性作法有關的 Azure 資源組態。 並提供整合式安全性監視，可偵測到其他可能沒注意到的威脅。 本教學課程將提供 Azure 資訊安全中心的簡略概觀，並說明如何與 Azure 虛擬機器搭配使用。   

## <a name="security-center-overview"></a>資訊安全中心概觀

Azure 資訊安全中心可協助您找出潛在的 VM 組態問題以及有針對性的安全性威脅。 範例包含找出遺失網路安全性群組的 VM、未加密的磁碟及 RDP 的暴力密碼破解攻擊。 此資訊會以簡易閱讀的圖表形式顯示在 Azure 資訊安全中心儀表板上。

在 Azure 入口網站的左側導覽窗格上按一下 [資訊安全中心]，即可存取 Azure 資訊安全中心儀表板。 儀表板提供資源健康狀態、安全性警示和設定建議的高階檢視。 透過這裡，您可以檢視 Azure 環境的安全性健康狀態、找到目前建議項目的計數，以及檢視威脅警示的目前狀態。 這些高階圖表皆可展開，其中提供所關注區域的詳細資訊。

![ASC 儀表板](./media/tutorial-azure-security/asc-dash.png)

Azure 資訊安全中心除了找到問題外，還會針對偵測到的問題提供建議。 例如，如果已部署的 VM 沒有連結網路安全性群組，資訊中心就會建立包含補救步驟的建議。 這些建議項目也會提供自動化補救作業，且無須離開 Azure 資訊安全中心的環境。  

![建議](./media/tutorial-azure-security/recommendations.png)

## <a name="configure-data-collection"></a>設定資料收集

您必須先設定 Azure 資訊安全中心的資料收集，才可查看 VM 的安全性組態。 此設定包含啟用資料收集和建立 Azure 儲存體帳戶以保存收集的資料。 

1. 從 Azure 資訊安全中心儀表板中，按一下 [安全性原則] 並選取您的訂用帳戶。 
2. 選取 [資料收集] 下的 [開啟]。
3. 按一下 [選擇儲存體帳戶] 並建立新的儲存體帳戶。 完成時選取 [確定]  。
4. 在 [安全性原則] 刀鋒視窗上按一下 [儲存]。 

完成此動作之後，Azure 資訊安全中心的資料收集代理程式就會安裝在所有虛擬機器上，並開始進行資料收集。 

## <a name="configure-security-policy"></a>設定安全性原則

安全性原則會定義收集資料和建立建議項目所依循的安全性原則項目。 依預設，Azure 資源會根據所有原則項目進行評估。 您可以全面性停用所有 Azure 資源上的個別原則項目，或為每個資源群組停用原則項目。 此組態讓您可以將不同的安全性原則套用至不同組 Azure 資源。 若要深入了解 Azure 資訊安全中心的安全性原則，請參閱[在 Azure 資訊安全中心設定安全性原則](../../security-center/security-center-policies.md)。 

為所有 Azure 資源設定安全性原則：

1. 從 Azure 資訊安全中心儀表板中，按一下 [安全性原則] 並選取您的訂用帳戶。 
2. 按一下 [預防原則]。
3. 啟用或停用要套用至所有 Azure 資源的原則項目。
4. 完成時按一下 [確定]。
5. 在 [安全性原則] 刀鋒視窗上按一下 [儲存]。 

若要設定特定資源群組的原則，請遵循相同步驟，但不是選取 [安全性原則] 刀鋒視窗上的訂用帳戶，而是選取資源群組。 設定原則時，請選取 [繼承] 下的 [唯一]。 如果您想停用特定資源群組上的資料收集，也可以在此執行此設定。

在下列範例中，已為名為 myResoureGroup 的資源群組建立唯一原則。 在此原則中，已停用磁碟加密和 Web 應用程式防火牆建議。

![唯一原則](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>檢視 VM 組態健康狀態

一旦啟用資料收集和設定安全性原則後，Azure 資訊安全中心便開始提供警示和建議項目。 部署 VM 後，資料收集代理程式會進行安裝，並將這些新 VM 的資料填入 Azure 資訊安全中心。 若要深入了解 VM 組態的健康狀態，請參閱[保護 Azure 資訊安全中心內的虛擬機器](../../security-center/security-center-virtual-machine-recommendations.md)。 

收集資料後，每個 VM 和相關 Azure 資源的資源健康狀態會進行彙總，並以方便閱讀的圖表呈現資料。 若要檢視資源健康狀態，請返回 Azure 資訊安全中心儀表板。 在 [資源安全性健康狀態] 下按一下 [計算]。 最後，在 [計算] 刀鋒視窗中按一下 [虛擬機器]。 此檢視會提供所有 VM 組態的狀態摘要。

![計算健康狀態](./media/tutorial-azure-security/compute-health.png)

選取每個 VM 會顯示該 VM 的所有建議項目。 在本教學課程的下一節將詳細說明建議項目。

## <a name="remediate-configuration-issues"></a>補救組態問題：

一旦 Azure 資訊安全中心開始填入組態資料時，系統便會根據已設定的安全性原則建立建議項目。 例如，如果已設定的 VM 沒有相關聯的網路安全性群組，系統就會建立一個建議項目。 若要查看所有建議項目的清單︰ 

1. 從 Azure 資訊安全中心儀表板上，按一下 [建議]。
3. 選取特定的建議項目即會開啟刀鋒視窗，其中包含適用該建議項目的所有資源清單。
4. 選取您想要處理的特定資源。
5. 遵循螢幕上補救步驟的指示。 

在許多情況下，Azure 資訊安全中心會提供可採取的步驟來執行建議，且無須離開 Azure 資訊安全中心。 如以下範例，安全中心偵測到輸入規則未限制的 NSG。 在此建議中，您可以選取 [編輯輸入規則] 按鈕，其中會提供您適當的使用者介面來修改規則。 

![建議](./media/tutorial-azure-security/remediation.png)

建議在執行補救後會標示為已解決。 

## <a name="view-detected-threats"></a>檢視偵測到的威脅

除了資源組態建議，Azure 資訊安全中心也提供威脅偵測警示。 安全性警示功能會彙總從每個 VM、Azure 網路記錄和連線合作夥伴解決方案收集的資料，以偵測不利於 Azure 資源的安全性威脅。 若要深入了解 Azure 資訊安全中心的威脅偵測功能，請參閱 [Azure 資訊安全中心的偵測功能](../../security-center/security-center-detection-capabilities.md)。

若要使用安全性警示功能，須將 Azure 資訊安全中心的定價層從「免費」提升為「標準」。 當您這麼做時，會有 30 天**免費試用版**可用。 若要變更定價層：  

1. 從 Azure 資訊安全中心儀表板中，按一下 [安全性原則] 並選取您的訂用帳戶。
2. 按一下 [定價層] 。
3. 選取新的階層，然後按一下 [選取]。
5. 在 [安全性原則] 刀鋒視窗上按一下 [儲存]。 

一旦啟用後，當偵測到安全性威脅時，安全性警示圖形會開始填入。

![安全性警示](./media/tutorial-azure-security/security-alerts.png)

選取警示以檢視威脅說明、偵測時間、威脅嘗試和建議補救方式等資訊。 在此範例中，安全中心偵測到 294 次嘗試失敗的 RDP 暴力密碼破解攻擊，並提供建議的解決方案。

![RDP 攻擊](./media/tutorial-azure-security/rdp-attack.png)
