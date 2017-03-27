---
title: "使用點對站將電腦連接至 Azure 虛擬網路︰入口網站 | Microsoft Docs"
description: "使用 Resource Manager 和 Azure 入口網站建立點對站 VPN 閘道連線，安全地連線到您的 Azure 虛擬網路。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: aafcfb9800a0d3ca9cf259617645da5ab6539c5b
ms.lasthandoff: 03/21/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>使用 Azure 入口網站設定 VNet 的點對站連線
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [傳統 - Azure 入口網站](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

點對站 (P2S) 設定可讓您建立從個別的用戶端電腦到虛擬網路的安全連線。 P2S 是透過 SSTP (安全通訊端通道通訊協定) 的 VPN 連線。 當您想要從遠端位置 (例如從住家或會議) 連線至 VNet 時，或只有幾個需要連線至虛擬網路的用戶端時，點對站連線是很實用的解決方案。 P2S 連線不需要 VPN 裝置或公眾對應 IP 位址即可運作。 您可從用戶端電腦建立 VPN 連線。

本文將逐步引導您使用 Azure 入口網站，建立具有點對站連線的 VNet。 如需有關點對站連線的詳細資訊，請參閱本文結尾的[點對站常見問題集](#faq)。

### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S 連線的部署模型和方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

下表顯示 P2S 組態的兩種部署模型和可用的部署方法。 當包含設定的文章推出時，我們會直接從此資料表連結至該文章。

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>基本工作流程
![Point-to-Site-diagram](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

### <a name="example"></a>範例值
* **名稱：VNet1**
* **位址空間：192.168.0.0/16**<br>在此範例中，我們只使用一個位址空間。 您可以針對 VNet 使用一個以上的位址空間。
* **子網路名稱：FrontEnd**
* **子網路位址範圍：192.168.1.0/24**
* **訂用帳戶：**如果您有一個以上的訂用帳戶，請確認您使用正確的訂用帳戶。
* **資源群組：TestRG**
* **位置：美國東部**
* **GatewaySubnet：192.168.200.0/24**
* **虛擬網路閘道名稱：VNet1GW**
* **閘道類型：VPN**
* **VPN 類型：路由式**
* **公用 IP 位址：VNet1GWpip**
* **連線類型：點對站**
* **用戶端位址集區：172.16.201.0/24**<br>使用這個點對站連線來連線到 VNet 的 VPN 用戶端，會收到來自用戶端位址集區的 IP 位址。

## <a name="createvnet"></a>第 1 部分 - 建立虛擬網路
在開始之前，請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。 如果您要練習建立此組態，您可以參考[範例值](#example)。

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="address"></a>第 2 部分 - 指定位址空間和子網路
您可以將其他位址空間和子網路新增至已建立的 Vnet。

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>第 3 部分 - 新增閘道子網路

將虛擬網路連接到閘道之前，您必須先建立虛擬網路要連接的閘道子網路。 閘道服務會使用閘道子網路中指定的 IP 位址。 如果可能，請使用 /28 或 /27 的 CIDR 區塊來建立閘道子網路，以提供足夠的 IP 位址來因應未來額外的組態需求。

本節中的螢幕擷取畫面提供作為參考範例。 請務必使用與您的組態所需值對應的 GatewaySubnet 位址範圍。

###<a name="to-create-a-gateway-subnet"></a>建立閘道子網路

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>第 4 部分 - 指定 DNS 伺服器 (選擇性)
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>第 5 部分 - 建立虛擬網路閘道
點對站連線需要下列設定：

* 閘道類型：VPN
* VPN 類型：路由式

### <a name="to-create-a-virtual-network-gateway"></a>建立虛擬網路閘道
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>第 6 部分 - 產生憑證

憑證是 Azure 用於點對站 VPN 的 VPN 用戶端驗證。 建立根憑證之後，您要將公開憑證資料 (不是私密金鑰)，匯出為 Base-64 編碼的 X.509.cer 檔案。 接著將來自根憑證的公開憑證資料上傳至 Azure。

每個使用點對站連線至 VNet 的用戶端電腦都必須安裝用戶端憑證。 用戶端憑證是從根憑證產生，並安裝在每部用戶端電腦上。 如果未安裝有效的用戶端憑證，且用戶端嘗試連線至 VNet，驗證將會失敗。

### <a name="getcer"></a>步驟 1 - 取得根憑證的 .cer 檔案

####<a name="enterprise-certificate"></a>企業憑證
 
如果您是使用企業解決方案，則可以使用現有的憑證鏈結。 取得您想要使用的根憑證 .cer 檔案。

####<a name="self-signed-root-certificate"></a>自我簽署根憑證

如果您未使用企業憑證解決方案，則必須建立自我簽署的根憑證。 若要建立包含 P2S 驗證的必要欄位之自我簽署根憑證，您可以使用 PowerShell。 [使用 PowerShell 建立點對站連線的自我簽署根憑證](vpn-gateway-certificates-point-to-site.md)將逐步引導您完成建立自我簽署根憑證的步驟。

> [!NOTE]
> 先前，makecert 是建立自我簽署根憑證，並產生用於點對站連線之用戶端憑證的建議方法。 您現在可以使用 PowerShell 來建立這些憑證。 使用 PowerShell 的一個優點是能夠建立 SHA-2 憑證。 請參閱[使用 PowerShell 建立點對站連線的自我簽署根憑證](vpn-gateway-certificates-point-to-site.md)的必要值。
>
>


#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>若要匯出自我簽署根憑證的公開金鑰

點對站連線需要公開金鑰 (.cer) 上傳至 Azure。 下列步驟可協助您匯出自我簽署根憑證的 .cer 檔案。

1. 若要取得憑證的 .cer 檔案，請開啟 [certmgr.msc] 。 找出自我簽署的根憑證，通常位於 '[憑證 - 目前的使用者]\[個人]\[憑證]' 中，然後按一下滑鼠右鍵。 按一下 [所有工作]，然後按一下 [匯出]。 這會開啟 [憑證匯出精靈] 。
2. 在精靈中，按 [下一步]。 選取 [否，不要匯出私密金鑰]，然後按 [下一步]。
3. 在 [匯出檔案格式] 頁面上，選取 [Base-64 編碼 X.509 (.CER)]，然後按 [下一步]。 
4. 在 [要匯出的檔案] 中，[瀏覽] 到您要匯出憑證的位置。 針對 [檔案名稱] ，請為憑證檔案命名。 然後按 [下一步] 。
5. 按一下 [完成]  以匯出憑證。 您將會看到 [匯出成功]。 按一下 [確定] 以關閉精靈。

### <a name="generateclientcert"></a>步驟 2 - 產生用戶端憑證
您可以為每個會連接到虛擬網路的用戶端產生一個唯一的憑證，或是在多個用戶端上使用相同的憑證。 產生唯一的用戶端憑證的優點是能夠視需要撤銷單一憑證。 否則，如果每個人都使用相同的用戶端憑證，而您發現需要撤銷某個用戶端的憑證時，將必須為所有使用該憑證進行驗證的用戶端產生並安裝新的憑證。

####<a name="enterprise-certificate"></a>企業憑證
- 如果您使用企業憑證解決方案，請以一般的名稱值格式 'name@yourdomain.com' 產生用戶端憑證，而不要使用 'domain name\username' 格式。
- 請確定您簽發的用戶端憑證所根據的憑證範本，是以「用戶端驗證」(而不是「智慧卡登入」等) 作為使用清單中第一個項目的「使用者」憑證範本。您可以按兩下用戶端憑證，然後檢視 [詳細資料] > [增強金鑰使用方法]，來檢查憑證。

####<a name="self-signed-root-certificate"></a>自我簽署根憑證 
如果您使用自我簽署根憑證，請參閱[使用 PowerShell 建立點對站連線的自我簽署憑證](vpn-gateway-certificates-point-to-site.md#clientcert)，以了解產生與點對站連線相容之用戶端憑證的步驟。


### <a name="exportclientcert"></a>步驟 3 - 匯出用戶端憑證
如果您使用 [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) 指示從自我簽署根憑證產生用戶端憑證，它會自動安裝在您用來產生它的電腦上。 如果您想要在另一部用戶端電腦上安裝用戶端憑證，您需要將它匯出。

1. 若要匯出用戶端憑證，請開啟 **certmgr.msc**。 以滑鼠右鍵按一下要匯出的用戶端憑證，然後依序按一下 [所有工作] 和 [匯出]。 這會開啟 [憑證匯出精靈] 。
2. 在精靈中，按一下 [下一步]，接著選取 [是，匯出私密金鑰]，然後按 [下一步]。
3. 在 [匯出檔案格式] 頁面上，保留選取預設值。 務必選取 [如果可能的話，包含憑證路徑中的所有憑證]。 然後按 [下一步] 。 
4. 在 [安全性]  頁面上，您必須保護私密金鑰。 如果您選取要使用密碼，請務必記錄或牢記您為此憑證設定的密碼。 然後按 [下一步] 。
5. 在 [要匯出的檔案] 中，[瀏覽] 到您要匯出憑證的位置。 針對 [檔案名稱] ，請為憑證檔案命名。 然後按 [下一步] 。
6. 按一下 [完成]  以匯出憑證。   

## <a name="addresspool"></a>第 7 部分 - 新增用戶端位址集區
1. 一旦建立虛擬網路閘道，請瀏覽至虛擬網路閘道刀鋒視窗的 [設定] 區段。 在 [設定] 區段中，按一下 [點對站組態] 以開啟 [組態] 刀鋒視窗。
   
    ![點對站刀鋒視窗](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. **位址集區**是連線的用戶端將接收 IP 位址的 IP 位址集區。 新增位址集區，然後按一下 [儲存]。
   
    ![用戶端位址集區](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>第 8 部分 - 上傳根憑證 .cer 檔案
建立閘道之後，您可以將信任的根憑證的 .cer 檔案上傳至 Azure。 您最多可上傳 20 個根憑證。 您並未將根憑證的私密金鑰上傳至 Azure。 一旦上傳 .cer 檔案，Azure 會使用它來驗證連接至虛擬網路的用戶端。

1. 新增憑證時，是在 [點對站組態] 刀鋒視窗的 [根憑證] 區段中新增。  
2. 請確定您以 Base-64 編碼 X.509 (.cer) 檔案形式匯出根憑證。 您需要以這種格式匯出它，以便可以使用文字編輯器開啟憑證。
3. 使用文字編輯器 (例如「記事本」) 開啟憑證。 請只以連續一行的形式複製下列區段：
   
    ![憑證資料](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)

    > [!NOTE]
    > 複製憑證資料時，請確定您是以連續一行的形式複製文字，而不含歸位字元或換行字元。 您可能必須將文字編輯器中的檢視修改成 [顯示符號] 或 [顯示所有字元]，才能看到歸位字元和換行字元。                                                                                                                                                                            
    >
    >

4. 將憑證資料貼到 [公開憑證資料] 欄位中。 提供憑證「名稱」，然後按一下 [儲存]。 您最多可新增 20 個受信任的根憑證。
   
    ![憑證上傳](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="clientconfig"></a>第 9 部分 - 下載和安裝 VPN 用戶端組態套件
使用 P2S 連接到 Azure 的用戶端必須安裝用戶端憑證和 VPN 用戶端組態封裝。 VPN 用戶端組態封裝可供 Windows 用戶端使用。 

VPN 用戶端套件包含的資訊可設定 Windows 內建的 VPN 用戶端軟體。 此組態專屬於您要連線的 VPN。 此套件不會安裝其他軟體。

您可以在每個用戶端電腦上使用相同的 VPN 用戶端組態封裝，前提是版本符合用戶端的架構。

### <a name="step-1---download-the-client-configuration-package"></a>步驟 1 - 下載用戶端組態套件

1. 在 [點對站組態] 刀鋒視窗上，按一下 [下載 VPN 用戶端] 來開啟 [下載 VPN 用戶端] 刀鋒視窗。 需要幾分鐘的時間來產生套件。
   
    ![VPN 用戶端下載 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. 為您的用戶端選取正確的封裝，然後按一下 [下載]。 儲存組態封裝檔案。 您會在將連線至虛擬網路的每個用戶端電腦上安裝它。

    ![VPN 用戶端下載 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpnclient.png)

### <a name="step-2---install-the-client-configuration-package"></a>步驟 2 - 安裝用戶端組態套件

1. 在本機將組態檔複製到您要連線至虛擬網路的電腦上。 
2. 在用戶端電腦上按兩下 .exe 檔案以安裝套件。 因為是您建立組態套件，所以它尚未簽署。 這表示您可能會看到警告。 如果您看到 Windows SmartScreen 快顯視窗，請按一下 [更多資訊] (左側)，然後按一下 [仍要執行] 來安裝套件。
3. 在用戶端電腦上，瀏覽至 [網路設定]，然後按一下 [VPN]。 您會看到列出的連線。 它會顯示將要連接的虛擬網路名稱，如下所示：
3. 在用戶端電腦上安裝封裝。 如果您看到 Windows SmartScreen 快顯視窗，請按一下 [更多資訊] (左側)，然後按一下 [仍要執行] 來安裝套件。
4. 在用戶端電腦上，瀏覽至 [網路設定]，然後按一下 [VPN]。 您會看到列出的連線。 它會顯示將要連接的虛擬網路名稱，如以下範例所示︰ 
   
    ![VPN 用戶端](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png)


## <a name="installclientcert"></a>第 10 部分 - 安裝匯出的用戶端憑證

如果您想要從不同於用來產生用戶端憑證的用戶端電腦建立 P2S 連線，您需要安裝用戶端憑證。 安裝用戶端憑證時，您需要匯出用戶端憑證時所建立的密碼。 

1. 找出 *.pfx* 檔案並複製到用戶端電腦。 在用戶端電腦上，按兩下 *.pfx* 檔案以安裝。 [存放區位置] 保留 [目前使用者]，然後按 [下一步]。
2. 在 [要匯入的檔案]  頁面上，請勿進行任何變更。 按 [下一步] 。
3. 在 [私密金鑰保護] 頁面上，如果您已使用密碼，請輸入憑證的密碼，或確認安裝憑證的安全性主體是否正確，然後按 [下一步]。
4. 在 [憑證存放區] 頁面上，保留預設的位置，然後按 [下一步]。
5. 按一下 [完成] 。 在憑證安裝的 [安全性警告] 上，按一下 [是]。 您可以安心地按一下 [是]，因為您已經產生憑證。 現在已成功匯入憑證。

## <a name="connect"></a>第 11 部分 - 連接至 Azure
1. 若要連接至您的 VNet，在用戶端電腦上瀏覽到 VPN 連線，然後找出所建立的 VPN 連線。 其名稱會與虛擬網路相同。 按一下 [ **連接**]。 可能會出現與使用憑證有關的快顯訊息。 如果出現，按一下 [繼續]  以使用較高的權限。 
2. 在 [連線] 狀態頁面上，按一下 [連線] 以便開始連線。 如果出現 [選取憑證]  畫面，請確認顯示的用戶端憑證是要用來連接的憑證。 如果沒有，請使用下拉箭頭來選取正確的憑證，然後按一下 [確定] 。
   
    ![正要連接到 Azure 的 VPN 用戶端](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)

    
3. 現在應該已建立您的連接。
   
    ![VPN 用戶端已連接至 Azure Stack](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)
                                                                                                                                                                           

> [!NOTE]
> 如果您使用的是藉由企業 CA 解決方案簽發的憑證，而在驗證時發生問題，請檢查用戶端憑證上的驗證順序。 您可以按兩下用戶端憑證，然後移至 [詳細資料] > [增強金鑰使用方法]，來檢查驗證清單順序。 請確定清單所顯示的第一個項目是「用戶端驗證」。 如果不是，您就必須根據以「用戶端驗證」作為清單中第一個項目的「使用者」範本來簽發用戶端憑證。 
>
>

## <a name="verify"></a>第 12 部分 - 驗證您的連接
1. 若要驗證您的 VPN 連線為作用中狀態，請開啟提升權限的命令提示字元，並執行 *ipconfig/all*。
2. 檢視結果。 請注意，您接收到的 IP 位址是您在組態中指定的點對站 VPN 用戶端位址集區中的其中一個位址。 結果應該類似下面的內容：
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="add"></a>新增或移除受信任的根憑證
您可以從 Azure 新增和移除受信任的根憑證。 當您移除受信任的憑證時，從根憑證所產生的用戶端憑證將不再能夠透過點對站連線到 Azure。 若您希望用可以戶端連線，他們需要安裝在 Azure 中受信任的憑證所產生的新用戶端憑證。

### <a name="to-add-a-trusted-root-certificate"></a>若要新增受信任的根憑證

您最多可新增 20 個受信任的根憑證 .cer 檔案至 Azure。 如需指示，請參閱這篇文章的[上傳受信任的根憑證](#uploadfile)一節。

### <a name="to-remove-a-trusted-root-certificate"></a>移除受信任的根憑證

1. 若要移除受信任的根憑證，瀏覽至虛擬網路閘道的 [點對站組態] 刀鋒視窗。
2. 在刀鋒視窗的 [根憑證] 區段中，找出您想要移除的憑證。
3. 按一下憑證旁邊的省略符號，然後按一下 [移除]。

## <a name="revokeclient"></a>撤銷用戶端憑證
您可以撤銷用戶端憑證。 憑證撤銷清單可讓您選擇性地拒絕以個別的用戶端憑證為基礎的點對站連線。 這不同於移除受信任的根憑證。 若您從 Azure 移除受信任的根憑證 .cer，就會撤銷所有由撤銷的根憑證所產生/簽署的用戶端憑證之存取權。 撤銷用戶端憑證，而不是根憑證，可以讓從根憑證產生的憑證繼續用於點對站連線的驗證。

常見的做法是使用根憑證管理小組或組織層級的存取權，然後使用撤銷的用戶端憑證針對個別使用者進行細部的存取控制。

### <a name="to-revoke-a-client-certificate"></a>若要撤銷用戶端憑證

您可以藉由將指紋新增至撤銷清單來撤銷用戶端憑證。

1. 擷取用戶端憑證指紋。 如需詳細資訊，請參閱[做法：擷取憑證的指紋](https://msdn.microsoft.com/library/ms734695.aspx)。
2. 將資訊複製到文字編輯器，並移除所有的空格，讓它是連續字串。
3. 瀏覽至虛擬網路閘道 [點對站組態] 刀鋒視窗。 這是您用來[上傳受信任的根憑證](#uploadfile)的刀鋒視窗。
4. 在 [撤銷憑證] 區段中，輸入憑證的易記名稱 (它不一定是憑證 CN)。
5. 將指紋字串複製並貼上到 [指紋]欄位。
6. 指紋會進行驗證，並且自動新增至撤銷清單。 您會在畫面上看到一則訊息，指出清單正在更新。 
7. 更新完成之後，憑證無法再用於連線。 嘗試使用此憑證進行連線的用戶端會收到訊息，指出憑證不再有效。

## <a name="faq"></a>點對站常見問題集

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>後續步驟
一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。 若要了解網路與虛擬機器的詳細資訊，請參閱 [Azure 與 Linux VM 網路概觀](../virtual-machines/virtual-machines-linux-azure-vm-network-overview.md)。



