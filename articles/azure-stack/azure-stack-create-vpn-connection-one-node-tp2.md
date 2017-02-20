---
title: "在不同 Azure Stack PoC 環境中的兩個虛擬網路之間建立站對站 VPN 連線 |Microsoft Docs"
description: "可讓雲端系統管理員在 TP2 中的兩個單節點 POC 環境之間建立站對站 VPN 連線的逐步程序。"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: scottnap
translationtype: Human Translation
ms.sourcegitcommit: 5104c7996de9dc0597e65be31c28a9aaa1243a90
ms.openlocfilehash: d324290caf5b5a085a2daf67e541c295dffda732


---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-poc-environments"></a>在不同 Azure Stack PoC 環境中的兩個虛擬網路之間建立站對站 VPN 連線
## <a name="overview"></a>概觀
本文說明如何在兩個不同 Azure Stack 概念證明 (POC) 環境中的兩個虛擬網路之間建立站對站 VPN 連線。 當您設定連線時，您將了解 VPN 閘道在 Azure Stack 中的運作方式。

> [!NOTE]
> 本文件特別適用於 Azure Stack TP2 POC。
> 
> 

### <a name="connection-diagram"></a>連接圖表
下圖顯示當您完成時的設定情況︰

![](media/azure-stack-create-vpn-connection-one-node-tp2/image1.png)

### <a name="before-you-begin"></a>開始之前
若要完成此設定﹐請確定在開始前具備下列項目：

* 符合 [Azure Stack 部署必要條件](azure-stack-deploy.md)所定義之 Azure Stack POC 硬體需求以及該文件所定義之其他必要條件的兩部伺服器。
* Azure Stack Technical 預覽 2 部署套件。

## <a name="deploy-the-poc-environments"></a>部署 POC 環境
您將部署兩個 Azure Stack POC 環境，以完成此設定。

* 對於您所部署的每個 POC，您可以依照[部署 Azure Stack POC](azure-stack-run-powershell-script.md)一文中的部署指示執行。
  本文件中的每個 POC 環境通常是指 *POC1* 和 *POC2*。

## <a name="configure-quotas-for-compute-network-and-storage"></a>設定計算、網路和儲存體的配額
首先，您必須設定計算、網路和儲存體的*配額*。 這些服務可與「方案」建立關聯，然後與租用戶可以訂閱的「優惠」建立關聯。

> [!NOTE]
> 您需要針對每個 Azure Stack POC 環境執行下列步驟。
> 
> 

針對已從 TP1 變更的服務建立配額。 如需在 TP2 中建立配額的步驟，請參閱 <http://aka.ms/mas-create-quotas>。 您可以接受此練習中所有配額設定的預設值。

## <a name="create-a-plan-and-offer"></a>建立方案和優惠
[方案](azure-stack-key-features.md)結合一或多項服務。 身為提供者的您可以為租用戶製作方案。 租用戶接著即可訂閱各項優惠，使用其內含的方案與服務。

> [!NOTE]
> 您需要針對每個 Azure Stack POC 環境執行下列步驟。
> 
> 

1. 首先建立方案。 若要這麼做，您可以依照[建立方案](azure-stack-create-plan.md)線上文章中的步驟執行。
2. 依照[在 Azure Stack 中建立優惠](azure-stack-create-offer.md)中所述的步驟建立優惠。
3. 以租用戶系統管理員的身分登入入口網站﹐然後[訂閱您所建立的優惠](azure-stack-subscribe-plan-provision-vm.md)。

## <a name="create-the-network-resources-in-poc-1"></a>在 POC 1 中建立網路資源
您現在要實際建立您設定組態所需的資源。 下列步驟說明您將要進行的動作。 這些指示說明如何使用入口網站建立資源，但也可使用 PowerShell 完成相同的動作。

![](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="log-in-as-a-tenant"></a>以租用戶身分登入
服務系統管理員可以租用戶身分登入，藉此測試其租用戶可能會使用的方案、優惠和訂用帳戶。 如果您還沒有租用戶的話，請在登入前先 [建立租用戶帳戶](azure-stack-add-new-user-aad.md) 。

### <a name="create-the-virtual-network--vm-subnet"></a>建立虛擬網路和 VM 子網路
1. 使用租用戶帳戶登入。
2. 在 Azure 入口網站中，按一下 [新增]。
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)
3. 從 Marketplace 功能表中選取 [網路]。
4. 按一下功能表上的 [虛擬網路] 項目。
5. 按一下資源描述刀鋒視窗底部附近的 [建立]。 根據此表格﹐在適當的欄位中輸入下列值。
   
   | **欄位** | **值** |
   | --- | --- |
   | 名稱 |vnet-01 |
   | 位址空間 |10.0.10.0/23 |
   | 子網路名稱 |subnet-01 |
   | 子網路位址範圍 |10.0.10.0/24 |
6. 您應會看到稍早建立的訂用帳戶填入 [訂用帳戶] 欄位中。
7. 對於資源群組﹐您可以建立新的資源群組，或選取 [使用現有的] (如果您已經有資源群組)。
8. 驗證預設位置。
9. 按一下 [建立] 。

### <a name="create-the-gateway-subnet"></a>建立閘道子網路
1. 從儀表板開啟您剛建立的虛擬網路資源 (Vnet-01)。
2. 在 [設定] 刀鋒視窗上選取 [子網路]。
3. 按一下 [閘道子網路]﹐將閘道子網路新增至虛擬網路。
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)
4. 子網路名稱預設為 **GatewaySubnet**。
   閘道子網路很特別﹐必須具有此特定名稱，才能正常運作。
5. 在 [位址範圍] 欄位中，輸入 **10.0.11.0/24**。
6. 按一下 [建立] 以建立閘道子網路。

### <a name="create-the-virtual-network-gateway"></a>建立虛擬網路閘道
1. 在 Azure 入口網站中，按一下 [新增]。
   
2. 從 Marketplace 功能表中選取 [網路]。
3. 從網路資源清單中選取 [虛擬網路閘道]。
4. 檢閱描述，然後按一下 [建立]。
5. 在 [名稱] 欄位中輸入 **GW1**。
6. 按一下 [虛擬網路] 項目以選擇虛擬網路。
   從清單中選取 **Vnet-01**。
7. 按一下 [公用 IP 位址] 功能表項目。 當 [選擇公用 IP 位址] 刀鋒視窗開啟時﹐按一下 [新建]。
8. 在 [名稱] 欄位中，輸入 **GW1-PiP**﹐然後按一下 [確定]。
9. [閘道類型] 預設應選取 [VPN]。 保留此設定。
10. [VPN 類型] 預設應選取 [路由式]。
    保留此設定。
11. 確認 [訂用帳戶] 和 [位置] 均正確無誤。 您可以視需要將資源釘選到儀表板。 按一下 [建立] 。

### <a name="create-the-local-network-gateway"></a>建立區域網路閘道
在此 Azure Stack 評估部署與實際 Azure 部署中實作*區域網路閘道*的方式有點不同。

就像在 Azure 中，您有區域網路閘道的概念。 不過，在 Azure 部署中﹐區域網路閘道代表您用來連接至 Azure 中虛擬網路閘道的內部部署 (位於租用戶) 實體裝置。 但在此 Azure Stack 評估部署中，連線的兩端都是虛擬網路閘道！

更廣泛來說﹐區域網路閘道資源一律指連線另一端的遠端閘道。 因為 POC 的設計方式，您需要提供其他 POC 的 NAT VM 上的外部網路卡 IP 位址做為區域網路閘道的公用 IP 位址。 您接著會在 NAT VM 上建立 NAT 對應﹐確保兩端都已正確連接。


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>取得 NAT VM 的外部介面卡 IP 位址
1. 登入 POC2 的 Azure Stack 實體機器。
2. 按住 [Windows 鍵] + R，以開啟 [執行] 功能表﹐鍵入 **mstsc** 並且按 Enter。
3. 在 [電腦] 欄位中輸入名稱 **MAS-BGPNAT01**﹐然後按一下 [連接] ****。
4. 按一下 [開始] 功能表並以滑鼠右鍵按一下 [Windows PowerShell]，然後選取 [以系統管理員身分執行]。
5. 輸入 **ipconfig /all**。
6. 尋找已連接到內部部署網路的乙太網路卡，並記下繫結至該介面卡的 IPv4 位址。 在範例環境中，該位址是 **10.16.167.195**，但您的位址將會不同。
7. 請記下此位址。 您會將它做為您在 POC1 中建立之區域網路閘道資源的公用 IP 位址。

### <a name="create-the-local-network-gateway-resource"></a>建立區域網路閘道資源
1. 登入 POC1 的 Azure Stack 實體機器。
2. 在 [電腦] 欄位中輸入名稱 **MAS-CON01**﹐然後按一下 [連接]。
3. 在 Azure 入口網站中，按一下 [新增]。
   
4. 從 Marketplace 功能表中選取 [網路]。
5. 從資源清單中選取 [區域網路閘道]。
6. 在 [名稱] 欄位中輸入 **POC2-GW**。
7. 您還不知道其他閘道的 IP 位址，但沒有關係﹐因為您可以稍後回到這裡進行變更。 現在在 [IP 位址] 欄位中輸入 **10.16.167.195**。
8. 在 [位址空間] 欄位中輸入您在 POC2 中建立的 Vnet 位址空間。 這將會是 **10.0.20.0/23**﹐所以輸入該值。
9. 確認您的 [訂用帳戶]、[資源群組] 和 [位置]均正確無誤，然後按一下 [建立]。

### <a name="create-the-connection"></a>建立連線
1. 在 Azure 入口網站中，按一下 [新增]。
   
2. 從 Marketplace 功能表中選取 [網路]。
3. 從資源清單中選取 [連線]。
4. 在 [基本] 設定刀鋒視窗中，選擇 [站對站 (IPSec)] 做為 [連線類型]。
5. 選取 [訂用帳戶]、[資源群組] 和 [位置]﹐然後按一下 [確定]。
6. 在 [設定] 刀鋒視窗中，選擇您先前建立的 [虛擬網路閘道] (**GW1**)。
7. 選擇您先前建立的 [區域網路閘道] (**POC2-GW**)。
8. 在 [連線名稱] 欄位中，輸入 **POC1-POC2**。
9. 在 [共用金鑰 (PSK)] 欄位中輸入 **12345**﹐然後按一下 [確定]。

### <a name="create-a-vm"></a>建立 VM
若要驗證透過 VPN 連線傳輸的資料，您需要 VM 在每個 POC 中傳送和接收資料。 立即在 POC1 中建立 VM，並將它放在您的虛擬網路的 VM 子網路上。

1. 在 Azure 入口網站中，按一下 [新增]。
   
2. 從 Marketplace 功能表選取 [虛擬機器]。
3. 在虛擬機器映像清單中，選取 [Windows Server 2012 R2 Datacenter] 映像。
4. 在 [基本] 刀鋒視窗的 [名稱] 欄位中輸入 **VM01**。
5. 輸入有效的使用者名稱和密碼。 建立 VM 之後﹐您將使用此帳戶來登入 VM。
6. 提供 [訂用帳戶]、[資源群組] 和 [位置]﹐然後按一下 [確定]。
7. 在 [大小] 刀鋒視窗上，選擇此執行個體的 VM 大小，然後按一下 [選取]。
8. 在 [設定] 刀鋒視窗上，您可以接受預設值；只要確定選取的虛擬網路是 **VNET-01** 且子網路已設為 **10.0.10.0/24** 即可。 按一下 [確定] 。
9. 檢閱 [摘要] 刀鋒視窗上的設定，然後按一下 [確定]。

## <a name="create-the-network-resources-in-poc-2"></a>在 POC 2 中建立網路資源
### <a name="log-in-as-a-tenant"></a>以租用戶身分登入
服務系統管理員可以租用戶身分登入，藉此測試其租用戶可能會使用的方案、優惠和訂用帳戶。 如果您還沒有租用戶的話，請在登入前先 [建立租用戶帳戶](azure-stack-add-new-user-aad.md) 。

### <a name="create-the-virtual-network-and-vm-subnet"></a>建立虛擬網路和 VM 子網路
1. 使用租用戶帳戶登入。
2. 在 Azure 入口網站中，按一下 [新增]。
   
3. 從 Marketplace 功能表中選取 [網路]。
4. 按一下功能表上的 [虛擬網路]。
5. 按一下資源描述刀鋒視窗底部附近的 [建立]。 在下表所列的適當欄位中輸入下列值。
   
   | **欄位** | **值** |
   | --- | --- |
   | 名稱 |vnet-02 |
   | 位址空間 |10.0.20.0/23 |
   | 子網路名稱 |subnet-02 |
   | 子網路位址範圍 |10.0.20.0/24 |
6. 您應會看到先前建立的訂用帳戶填入 [訂用帳戶] 欄位中。
7. 對於資源群組﹐您可以建立新的資源群組，或選取 [使用現有的] (如果您已經有資源群組)。
8. 驗證預設**位置**。 如果想要，您可以將虛擬網路釘選到儀表板，以便存取。
9. 按一下 [建立] 。

### <a name="create-the-gateway-subnet"></a>建立閘道子網路
1. 從儀表板開啟您建立的虛擬網路資源 (**Vnet-02**)。
2. 在 [設定] 刀鋒視窗上選取 [子網路]。
3. 按一下 [閘道子網路]﹐將閘道子網路新增至虛擬網路。
   
4. 子網路名稱預設為 **GatewaySubnet**。
   閘道子網路很特別﹐必須具有此特定名稱，才能正常運作。
5. 在 [位址範圍] 欄位中，輸入 **10.0.20.0/24**。
6. 按一下 [建立] 以建立閘道子網路。

### <a name="create-the-virtual-network-gateway"></a>建立虛擬網路閘道
1. 在 Azure 入口網站中，按一下 [新增]。
   
2. 從 Marketplace 功能表中選取 [網路]。
3. 從網路資源清單中選取 [虛擬網路閘道]。
4. 檢閱描述，然後按一下 [建立]。
5. 在 [名稱] 欄位中輸入 **GW2**。
6. 按一下 [虛擬網路] 以選擇虛擬網路。
   從清單中選取 **Vnet-02**。
7. 按一下 [公用 IP 位址]。 當 [選擇公用 IP 位址] 刀鋒視窗開啟時﹐按一下 [新建]。
8. 在 [名稱] 欄位中，輸入 **GW2-PiP**﹐然後按一下 [確定]。
9. [閘道類型] 預設應選取 [VPN]。 保留此設定。
10. [VPN 類型] 預設應選取 [路由式]。
    保留此設定。
11. 確認 [訂用帳戶] 和 [位置] 均正確無誤。 您可以視需要將資源釘選到儀表板。 按一下 [建立] 。

### <a name="create-the-local-network-gateway"></a>建立區域網路閘道
#### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>取得 NAT VM 的外部介面卡 IP 位址
1. 登入 POC1 的 Azure Stack 實體機器。
2. 按住 [Windows 鍵] + R，以開啟 [執行] 功能表﹐鍵入 **mstsc** 並且按 Enter。
3. 在 [電腦] 欄位中輸入名稱 **MAS-BGPNAT01**﹐然後按一下 [連接] ****。
4. 按一下 [開始] 功能表﹐以滑鼠右鍵按一下 [Windows PowerShell]，然後選取 [以系統管理員身分執行]。
5. 輸入 **ipconfig /all**。
6. 尋找已連接到內部部署網路的乙太網路卡，並記下繫結至該介面卡的 IPv4 位址。 在範例環境中，該位址是 **10.16.169.131**，但您的位址將會不同。
7. 請記下此位址。 您稍後會將它做為您在 POC1 中建立之區域網路閘道資源的公用 IP 位址。

#### <a name="create-the-local-network-gateway-resource"></a>建立區域網路閘道資源
1. 登入 POC2 的 Azure Stack 實體機器。
2. 在 [電腦] 欄位中輸入名稱 **MAS-CON01**﹐然後按一下 [連接]。
3. 在 Azure 入口網站中，按一下 [新增]。
   
4. 從 Marketplace 功能表中選取 [網路]。
5. 從資源清單中選取 [區域網路閘道]。
6. 在 [名稱] 欄位中輸入 **POC1-GW**。
7. 您現在需要您針對 POC1 中的虛擬網路閘道所錄製的公用 IP 位址。 在 [IP 位址] 欄位中輸入 **10.16.169.131**。
8. 在 [位址空間] 欄位中輸入 POC1 - **10.0.0.0/16** 中 **Vnet-01** 的位址空間。
9. 確認您的 [訂用帳戶]、[資源群組] 和 [位置]均正確無誤，然後按一下 [建立]。

## <a name="create-the-connection"></a>建立連線
1. 在 Azure 入口網站中，按一下 [新增]。
   
2. 從 Marketplace 功能表中選取 [網路]。
3. 從資源清單中選取 [連線]。
4. 在 [基本] 設定刀鋒視窗中，選擇 [站對站 (IPSec)] 做為 [連線類型]。
5. 選取 [訂用帳戶]、[資源群組] 和 [位置]﹐然後按一下 [確定]。
6. 在 [設定] 刀鋒視窗中，選擇您先前建立的 [虛擬網路閘道] (**GW1**)。
7. 選擇您先前建立的 [區域網路閘道] (**POC1-GW**)。
8. 在 [連線名稱] 欄位中，輸入 **POC2-POC1**。
9. 在 [共用金鑰 (PSK)] 欄位中輸入 **12345**。 如果您選擇不同的值，請記住該值必須符合您在 POC1 上建立的共用金鑰。 按一下 [確定] 。

## <a name="create-a-vm"></a>建立 VM
立即在 POC1 中建立 VM，並將它放在您的虛擬網路的 VM 子網路上。

1. 在 Azure 入口網站中，按一下 [新增]。
   
2. 從 Marketplace 功能表選取 [虛擬機器]。
3. 在虛擬機器映像清單中，選取 [Windows Server 2012 R2 Datacenter] 映像。
4. 在 [基本] 刀鋒視窗的 [名稱] 欄位中輸入 **VM02**。
5. 輸入有效的使用者名稱和密碼。 建立 VM 之後﹐您將使用此帳戶來登入 VM。
6. 提供 [訂用帳戶]、[資源群組] 和 [位置]﹐然後按一下 [確定]。
7. 在 [大小] 刀鋒視窗上，選擇此執行個體的 VM 大小，然後按一下 [選取]。
8. 在 [設定] 刀鋒視窗上，您可以接受預設值；只要確定選取的虛擬網路是 **VNET-02** 且子網路已設為 **20.0.0.0/24** 即可。 按一下 [確定] 。
9. 檢閱 [摘要] 刀鋒視窗上的設定，然後按一下 [確定]。

## <a name="configure-the-nat-vm-in-each-poc-for-gateway-traversal"></a>在每個 POC 中設定 NAT VM 以便閘道周遊
因為 POC 已設計為自封並與部署實體主機的網路隔離，所以閘道連接至的「外部」VIP 網路實際上不在外部，但卻隱藏在執行網路位址轉譯 (NAT) 的路由器之後。 路由器實際上是在 POC 基礎結構中執行路由及遠端存取服務 (RRAS) 角色的 Windows Server VM (**MAS-BGPNAT01**)。 您必須在 MA-BGPNAT01 VM 上設定 NAT﹐才能允許站對站 VPN 連線連接兩端。 若要這麼做，您必須建立靜態 NAT 對應﹐以將 BGPNAT VM 上的外部介面對應至 VPN 連線所需連接埠的邊界閘道集區 VIP。

> [!NOTE]
> 只有 POC 環境需要此組態。
> 
> 

### <a name="configure-nat"></a>設定 NAT
您必須在 BOTH POC 環境中遵循下列步驟。

1. 登入 POC1 的 Azure Stack 實體機器。
2. 按住 [Windows 鍵] + R，以開啟 [執行] 功能表﹐鍵入 **mstsc** 並且按 **Enter**。
3. 在 [電腦] 欄位中輸入名稱 **MAS-BGPNAT01**﹐然後按一下 [連接]。
4. 按一下 [開始] 功能表並以滑鼠右鍵按一下 [Windows PowerShell]，然後選取 [以系統管理員身分執行]。
5. 輸入 **ipconfig /all**。
6. 尋找已連接到內部部署網路的乙太網路卡，並記下繫結至該介面卡的 IPv4 位址。 在範例環境中，該位址是 **10.16.169.131** (下方的紅色圈起處)，但您的位址將會不同。
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image16.png)
7. 輸入下列 PowerShell 命令﹐以指定 IKE 驗證連接埠的外部 NAT 位址。 請記得將 IP 位址變更為符合您環境的 IP 位址。
   
       Add-NetNatExternalAddress -NatName BGPNAT -IPAddress 10.16.169.131 -PortStart 499 -PortEnd 501
8. 接下來，您可建立靜態 NAT 對應﹐以將外部位址對應至閘道公用 IP 位址，進而對應第 1 階段 IPSEC 通道的 ISAKMP 連接埠 500。
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 500 -InternalPort 500
> [!NOTE] 
> 此處的 `-InternalAddress` 參數是您稍早建立的虛擬網路閘道公用 IP 位址。  若要尋找這個 IP 位址，請查看 [虛擬網路閘道] 刀鋒視窗的屬性，以及尋找公用 IP 位址的值。       

9. 最後，您必須設定 NAT 周遊﹐其透過 NAT 裝置使用連接埠 4500 成功建立完整的 IPEC 通道。
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 4500 -InternalPort 4500
> [!NOTE] 
> 此處的 `-InternalAddress` 參數是您稍早建立的虛擬網路閘道公用 IP 位址。  若要尋找這個 IP 位址，請查看 [虛擬網路閘道] 刀鋒視窗的屬性，以及尋找公用 IP 位址的值。       

10. 重複 POC2 中的步驟 1 至 9。

## <a name="test-the-connection"></a>測試連線
既然已建立站對站連線﹐您應該確認可以透過它取得流量。 這項工作很簡單，因為只牽涉到登入您在任一 POC 環境中建立的其中一個 VM 以及 ping 您在其他環境中建立的 VM。 若要確保您會透過站對站連線傳送流量，請務必在遠端子網路上 ping VM 的直接 IP (DIP) 位址 (而非 VIP)。 若要這麼做，您需要尋找並記下在連線另一端的位址。

### <a name="log-in-to-the-tenant-vm-in-poc1"></a>在 POC1 中登入租用戶 VM
1. 登入 POC1 的 Azure Stack 實體機器﹐然後使用租用戶帳戶登入入口網站。
2. 按一下左側導覽中的 [虛擬機器]。
3. 在 VM 清單中尋找您先前建立的 **VM01**，然後按一下它。
4. 在虛擬機器的刀鋒視窗上按一下 [連接]。
   
     ![](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. 從 VM 內部開啟命令提示字元並輸入 **ipconfig /all**。
6. 在輸出中尋找 **IPv4 位址** 並記下它。 這是您將從 POC2 ping 的位址。 在範例環境中，位址是 **10.0.10.4**，但在您的環境中可能會不同。 不過﹐它應該落在先前建立的 **10.0.10.0/24** 子網路內。

### <a name="log-in-to-the-tenant-vm-in-poc2"></a>在 POC2 中登入租用戶 VM
1. 登入 POC2 的 Azure Stack 實體機器﹐然後使用租用戶帳戶登入入口網站。
2. 按一下左側導覽中的 [虛擬機器]。
3. 在 VM 清單中尋找您先前建立的 **VM02**，然後按一下它。
4. 在虛擬機器的刀鋒視窗上按一下 [連接]。
   
5. 從 VM 內部開啟命令提示字元並輸入 **ipconfig /all**。
6. 您應該會看到落在 10.0.20.0/24 內的 IPv4 位址。 在範例環境中，位址是 10.0.20.4，但您的位址可能會不同。
7. 現在從 POC2 中的 VM﹐您可透過通道 ping POC1 中的 VM。 若要這麼做﹐您可以 ping 從 VM01 記錄的 DIP。
   在範例環境中，這是 10.0.10.4，但務必 ping 您在實驗室中記下的位址。 您應該會看到如下所示的結果：
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
8. 遠端 VM 的回覆表示測試成功！ 您可以關閉 [VM 連接] 視窗。 或者，您可以嘗試進行一些其他資料傳輸 (如檔案複製) 以測試您的連線。

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>檢視透過閘道連線的資料傳輸統計資料
如果您想要知道多少資料通過您的站對站連線，可在 [連線] 刀鋒視窗中取得此資訊。 這項測試也是確認您剛傳送的 ping 真的通過 VPN 連線的另一種好方法。

1. 仍已登入 POC2 中租用戶 VM 的情況下，使用您的租用戶帳戶登入 [Microsoft Azure Stack POC 入口網站]。
2. 按一下 [瀏覽] 功能表項目，然後選取 [連線]。
3. 按一下清單中的 **POC2-POC1** 連線。
4. 在 [連線] 刀鋒視窗上，您可以看到 [資料輸入] 和 [資料輸出] 的統計資料。 在下列螢幕擷取畫面中﹐您可看到一個 ping 會建立的一些較大數字。 這是因為也會傳輸一些其他檔案。 您應該會看到一些非零值。
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)




<!--HONumber=Feb17_HO2-->


