---
title: "使用點對站將電腦連線至 Azure 虛擬網路︰傳統入口網站 | Microsoft Docs"
description: "使用傳統入口網站建立點對站 VPN 閘道連線，以安全地連線至您的傳統 Azure 虛擬網路。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 4f5668a5-9b3d-4d60-88bb-5d16524068e0
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ffd5690853a7344c7122940ad9676d1903ba2d4a
ms.lasthandoff: 03/09/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal-classic"></a>使用傳統入口網站設定 VNet 的點對站連線 (傳統)
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [傳統 - Azure 入口網站](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> * [傳統 - 傳統入口網站](vpn-gateway-point-to-site-create.md)
> 
> 

點對站 (P2S) 設定可讓您建立從個別的用戶端電腦到虛擬網路的安全連線。 P2S 是透過 SSTP (安全通訊端通道通訊協定) 的 VPN 連線。 當您想要從遠端位置 (例如從住家或會議) 連線至 VNet 時，或只有幾個需要連線至虛擬網路的用戶端時，點對站連線是很實用的解決方案。 P2S 連線不需要 VPN 裝置或公眾對應 IP 位址即可運作。 您可從用戶端電腦建立 VPN 連線。

本文將逐步引導您使用傳統入口網站，以傳統部署模型建立具有點對站連線的 VNet。 如需有關點對站連線的詳細資訊，請參閱本文結尾的[點對站常見問題集](#faq)。



### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S 連線的部署模型和方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

下表顯示 P2S 組態的兩種部署模型和可用的部署方法。 當包含設定的文章推出時，我們會直接從此資料表連結至該文章。

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>基本工作流程
![點對站圖表](./media/vpn-gateway-point-to-site-create/p2sclassic.png "點對站")

以下步驟將逐步引導您建立與虛擬網路的安全點對站連線。 點對站連線的設定分為四個區段。 您設定每個區段的順序很重要。 請勿略過步驟或往前跳。

* **區段 1：** 建立虛擬網路和 VPN 閘道。
* **區段 2：** 建立用來驗證的憑證並將其上傳。
* **區段 3：** 匯出並安裝用戶端憑證。
* **區段 4：** 設定 VPN 用戶端。



## <a name="vnetvpn"></a>區段 1 - 建立虛擬網路和 VPN 閘道

在開始之前，請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

### <a name="part-1-create-a-virtual-network"></a>第 1 部份 - 建立虛擬網路
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。 這些步驟使用傳統入口網站，而非 Azure 入口網站。 目前您無法使用 Azure 入口網站建立 P2S 連線。
2. 按一下螢幕左下角的 [新增]。 在導覽窗格中依序按一下 [網絡服務] 和 [虛擬網路]。 按一下 [Custom Create]  開始組態精靈。
3. 在 [虛擬網路詳細資料]  頁面上，輸入下列資訊，然後按一下右下角的下一步箭頭。
   
   * **名稱**：為虛擬網路命名。 例如，'VNet1'。 這是當您將 Vm 部署到此 VNet 時要參照的名稱。
   * **位置**：位置會與您要存放資源 (VM) 的實體位置 (區域) 直接相關。 例如，如果您要讓部署到此虛擬網路的 VM 實際上位於 East US 中，請選取該位置。 建立關聯之後，您就無法變更與虛擬網路相關聯的區域。
4. 在 [DNS Servers and VPN Connectivity]  頁面上，輸入下列資訊，然後按一下右下角的下一步箭頭。
   
   * **DNS 伺服器**：輸入 DNS 伺服器名稱和 IP 位址，或從捷徑功能表中選取先前註冊的 DNS 伺服器。 此設定不會建立 DNS 伺服器。 它可讓您指定要用於此虛擬網路之名稱解析的 DNS 服務。 如果您想要使用 Azure 預設的名稱解析服務，請讓此區段保留空白。
   * **設定點對站 VPN**：勾選此核取方塊。
5. 在 [點對站連線能力]  頁面中，指定 VPN 用戶端在連線時將接收 IP 位址的 IP 位址範圍。 關於可指定的位址範圍，您必須遵守幾項規則。 請務必確認您指定的範圍未與內部部署網路中的任何範圍重疊。
6. 輸入下列資訊，然後按 [下一步] 箭頭。
   
   * **位址空間**：包含起始 IP 和 CIDR (位址計數)。
   * **新增位址空間**：唯有網路設計需要時才需新增位址空間。
7. 在 [虛擬網路位址空間] 頁面上，指定您想要用於虛擬網路的位址範圍。 這些是將指派給 VM 的動態 IP 位址 (DIP)，以及指派給您部署至此虛擬網路之其他角色執行個體的動態 IP 位址 (DIP)。<br><br>特別重要的是，您選取的範圍不得與用於內部部署網路的任何範圍重疊。 您必須與網路管理員協調，後者可能需要從內部部署網路位址空間中切割出 IP 位址範圍，以供您用於虛擬網路。
8. 輸入下列資訊，然後按一下勾號，開始建立您的虛擬網路。
   
   * **位址空間**：新增要用於此虛擬網路的內部 IP 位址範圍，包括起始 IP 和計數。 重要的是，選取的範圍不得與用於內部部署網路的任何範圍重疊。 
   * **新增子網路**：不需要其他子網路，但您可能希望為將擁有靜態 DIP 的 VM 建立個別子網路。 或者，您想要讓 VM 與其他角色執行個體位於不同的子網路。
   * **新增閘道子網路**：點對站 VPN 需要閘道子網路。 按一下以新增閘道子網路。 閘道子網路僅用於虛擬網路閘道。
9. 一旦建立虛擬網路之後，您將在 Azure 傳統入口網站的網路頁面上看見 [狀態] 下列出 [已建立]。 待虛擬網路建立後，您便可建立動態路由閘道。

### <a name="part-2-create-a-dynamic-routing-gateway"></a>第 2 部份 - 建立動態路由閘道器
閘道器類型必須設定為動態。 靜態路由閘道器無法與此功能搭配運作。

1. 在 Azure 傳統入口網站的 [網路] 頁面上，按一下您建立的虛擬網路，然後瀏覽到 [儀表板] 頁面。
2. 在 [儀表板] 頁面底部，按一下 [建立閘道]。 畫面上將會出現一則訊息，詢問**您是否想要為虛擬網路 "VNet1" 建立閘道**。 按一下 [是] 即可開始建立閘道。 閘道建立作業可能最多需要花費 45 分鐘的時間。

## <a name="generate"></a>區段 2 - 產生並上傳憑證
憑證是 Azure 用於點對站 VPN 的 VPN 用戶端驗證。 建立根憑證之後，您要將公開憑證資料 (不是私密金鑰)，匯出為 Base-64 編碼的 X.509.cer 檔案。 接著將來自根憑證的公開憑證資料上傳至 Azure。

每個使用點對站連線至 VNet 的用戶端電腦都必須安裝用戶端憑證。 用戶端憑證是從根憑證產生，並安裝在每部用戶端電腦上。 如果未安裝有效的用戶端憑證，且用戶端嘗試連線至 VNet，驗證將會失敗。

您將在本區段中進行下列工作：

* 取得根憑證的 .cer 檔案。 這可以是自我簽署根憑證，或者您也可以使用企業憑證系統。
* 將 .cer 檔案上傳至 Azure。
* 產生用戶端憑證。

### <a name="root"></a>第 1 部分︰取得根憑證的 .cer 檔案


如果您是使用企業解決方案，則可以使用現有的憑證鏈結。 取得您想要使用的根憑證 .cer 檔案。


如果您未使用企業憑證解決方案，則必須建立自我簽署的根憑證。 若要建立包含 P2S 驗證的必要欄位之自我簽署根憑證，您可以使用 PowerShell。 [使用 PowerShell 建立點對站連線的自我簽署根憑證](vpn-gateway-certificates-point-to-site.md)將逐步引導您完成建立自我簽署根憑證的步驟。

> [!NOTE]
> 先前，makecert 是建立自我簽署根憑證，並產生用於點對站連線之用戶端憑證的建議方法。 您現在可以使用 PowerShell 來建立這些憑證。 使用 PowerShell 的一個優點是能夠建立 SHA-2 憑證。 請參閱[使用 PowerShell 建立點對站連線的自我簽署根憑證](vpn-gateway-certificates-point-to-site.md)的必要值。
>
>


#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>若要匯出自我簽署根憑證的公開金鑰。

點對站連線需要公開金鑰 (.cer) 上傳至 Azure。 下列步驟可協助您匯出自我簽署根憑證的 .cer 檔案。

1. 若要取得憑證的 .cer 檔案，請開啟 [certmgr.msc] 。 找出自我簽署的根憑證，通常位於 '[憑證 - 目前的使用者]\[個人]\[憑證]' 中，然後按一下滑鼠右鍵。 按一下 [所有工作]，然後按一下 [匯出]。 這會開啟 [憑證匯出精靈] 。
2. 在精靈中，按 [下一步]。 選取 [否，不要匯出私密金鑰]，然後按 [下一步]。
3. 在 [匯出檔案格式] 頁面上，選取 [Base-64 編碼 X.509 (.CER)]，然後按 [下一步]。 
4. 在 [要匯出的檔案] 中，[瀏覽] 到您要匯出憑證的位置。 針對 [檔案名稱] ，請為憑證檔案命名。 然後按 [下一步] 。
5. 按一下 [完成]  以匯出憑證。 您將會看到 [匯出成功]。 按一下 [確定] 以關閉精靈。

### <a name="upload"></a>第 2 部份︰將根憑證 .cer 檔案上傳至 Azure 傳統入口網站

將受信任的憑證新增至 Azure。 當您將 Base64 編碼 X.509 (.cer) 檔案新增至 Azure 時，便是告訴 Azure 信任該檔案所代表的根憑證。 您最多可上傳 20 個根憑證。 您並未將根憑證的私密金鑰上傳至 Azure。 一旦上傳 .cer 檔案，Azure 會使用它來驗證連接至虛擬網路的用戶端。

1. 在 Azure 傳統入口網站中，於虛擬網路的 [憑證] 頁面上，按一下 [上傳根憑證]。
2. 在 [上傳憑證]  頁面上，瀏覽到 .cer 根憑證，然後按一下勾號。

### <a name="createclientcert"></a>第 3 部份︰產生用戶端憑證
您可以為每個會進行連線的用戶端產生唯一的憑證，您也可以在多個用戶端上使用相同的憑證。 產生唯一的用戶端憑證的優點是能夠視需要撤銷單一憑證。 否則，如果每個人都使用相同的用戶端憑證，而您發現需要撤銷某一個用戶端的憑證時，則必須為所有使用該憑證進行驗證的用戶端產生並安裝新的憑證。

####<a name="enterprise-certificate"></a>企業憑證
- 如果您使用企業憑證解決方案，請以一般的名稱值格式 'name@yourdomain.com' 產生用戶端憑證，而不要使用 'domain name\username' 格式。
- 請確定您簽發的用戶端憑證所根據的憑證範本，是以「用戶端驗證」(而不是「智慧卡登入」等) 作為使用清單中第一個項目的「使用者」憑證範本。您可以按兩下用戶端憑證，然後檢視 [詳細資料] > [增強金鑰使用方法]，來檢查憑證。

####<a name="self-signed-root-root-certificate"></a>自我簽署根憑證 
如果您使用自我簽署根憑證，請參閱[使用 PowerShell 建立點對站連線的自我簽署憑證](vpn-gateway-certificates-point-to-site.md#clientcert)，以了解產生與點對站連線相容之用戶端憑證的步驟。


## <a name="installclientcert"></a>第 3 部分 - 匯出及安裝用戶端憑證

產生用戶端憑證之後，您必須將憑證匯出為 .pfx 檔，並再將它安裝在用戶端電腦上。 每個用戶端電腦都必須具備用戶端憑證才能進行驗證。 您可以自動安裝用戶端憑證，也可以手動安裝憑證。 下列步驟將逐步引導您手動匯出並安裝用戶端憑證。

### <a name="export-the-client-certificate"></a>匯出用戶端憑證

1. 若要匯出用戶端憑證，請開啟 **certmgr.msc**。 以滑鼠右鍵按一下要匯出的用戶端憑證，然後依序按一下 [所有工作] 和 [匯出]。 這會開啟 [憑證匯出精靈] 。
2. 在精靈中，按一下 [下一步]，接著選取 [是，匯出私密金鑰]，然後按 [下一步]。
3. 在 [匯出檔案格式]  頁面上，您可以保留選取預設值。 然後按 [下一步] 。 
4. 在 [安全性]  頁面上，您必須保護私密金鑰。 如果您選取要使用密碼，請務必記錄或牢記您為此憑證設定的密碼。 然後按 [下一步] 。
5. 在 [要匯出的檔案] 中，[瀏覽] 到您要匯出憑證的位置。 針對 [檔案名稱] ，請為憑證檔案命名。 然後按 [下一步] 。
6. 按一下 [完成]  以匯出憑證。

### <a name="install-the-client-certificate"></a>安裝用戶端憑證

安裝用戶端憑證時，您需要匯出用戶端憑證時所建立的密碼。

1. 找出 *.pfx* 檔案並複製到用戶端電腦。 在用戶端電腦上，按兩下 *.pfx* 檔案以安裝。 [存放區位置] 保留 [目前使用者]，然後按 [下一步]。
2. 在 [要匯入的檔案]  頁面上，請勿進行任何變更。 按 [下一步] 。
3. 在 [私密金鑰保護] 頁面上，如果您已使用密碼，請輸入憑證的密碼，或確認安裝憑證的安全性主體是否正確，然後按 [下一步]。
4. 在 [憑證存放區] 頁面上，保留預設的位置，然後按 [下一步]。
5. 按一下 [完成] 。 在憑證安裝的 [安全性警告] 上，按一下 [是]。 您可以安心地按一下 [是]，因為您已經產生憑證。 現在已成功匯入憑證。

## <a name="vpnclientconfig"></a>第 4 部分 - 設定 VPN 用戶端
若要連接到虛擬網路，您還需要設定 VPN 用戶端。 用戶端需要具備用戶端憑證及適當的 VPN 用戶端組態套件，才能順利連線。 若要設定 VPN 用戶端，請依序執行下列步驟：

### <a name="part-1-create-the-vpn-client-configuration-package"></a>第 1 部份 - 建立 VPN 用戶端組態套件
1. 在 Azure 傳統入口網站中，虛擬網路的**儀表板**頁面上，瀏覽至右上角的 [快速瀏覽] 功能表。 VPN 用戶端套件包含的組態資訊可設定 Windows 內建的 VPN 用戶端軟體。 此套件不會安裝其他軟體。 這些是您所要連接之虛擬網路的專屬設定。 如需支援的用戶端作業系統清單，請參閱本文結尾的[點對站連線常見問題集](#faq)。<br><br>選取對應至即將安裝之目標用戶端作業系統的下載封裝：
   
   * 如果是 32 位元用戶端，請選取 [下載 32 位元用戶端 VPN 套件] 。
   * 如果是 64 位元用戶端，請選取 [下載 64 位元用戶端 VPN 套件] 。
2. 建立用戶端套件需要幾分鐘的時間。 完成套件後，您就可以下載檔案。 您下載的 *.exe* 檔案可以安全地儲存在本機電腦上。
3. 從 Azure 傳統入口網站產生及下載 VPN 用戶端套件之後，您便可將用戶端套件安裝至要連線到虛擬網路的用戶端電腦上。 如果您打算將 VPN 用戶端套件安裝到多部用戶端電腦，請確定它們也已安裝用戶端憑證。

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>第 2 部份：在用戶端上安裝 VPN 組態套件
1. 在本機將設定檔複製到您要連接到虛擬網路的電腦上，然後按兩下 .exe 檔案。 
2. 套件安裝完成後，您就可以啟動 VPN 連線。 設定套件並非由 Microsoft 所簽署。 建議您使用組織的簽署服務來簽署套件，或是自行使用 [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327) 來簽署。 即使沒有簽署，還是可以使用套件。 不過，如果套件未簽署，當您安裝套件時將會出現警告。
3. 在用戶端電腦上，瀏覽至 [網路設定]，然後按一下 [VPN]。 您會看到列出的連線。 它會顯示將要連接的虛擬網路名稱，如下所示︰ 
   
    ![VPN 用戶端](./media/vpn-gateway-point-to-site-create/vpn.png "VPN 用戶端")

### <a name="part-3-connect-to-azure"></a>第 3 部分：連接到 Azure
1. 若要連接至您的 VNet，在用戶端電腦上瀏覽到 VPN 連線，然後找出所建立的 VPN 連線。 其名稱會與虛擬網路相同。 按一下 [ **連接**]。 可能會出現與使用憑證有關的快顯訊息。 如果出現，按一下 [繼續]  以使用較高的權限。 
2. 在 [連線] 狀態頁面上，按一下 [連線] 以便開始連線。 如果出現 [選取憑證]  畫面，請確認顯示的用戶端憑證是要用來連接的憑證。 如果沒有，請使用下拉箭頭來選取正確的憑證，然後按一下 [確定] 。
   
    ![VPN 用戶端 2](./media/vpn-gateway-point-to-site-create/clientconnect.png "VPN 用戶端連接")
3. 現在應該已建立您的連接。
   
    ![VPN 用戶端 3](./media/vpn-gateway-point-to-site-create/connected.png "VPN 用戶端連接 2")

> [!NOTE]
> 如果您使用的是藉由企業 CA 解決方案簽發的憑證，而在驗證時發生問題，請檢查用戶端憑證上的驗證順序。 您可以按兩下用戶端憑證，然後移至 [詳細資料] > [增強金鑰使用方法]，來檢查驗證清單順序。 請確定清單所顯示的第一個項目是「用戶端驗證」。 如果不是，您就必須根據以「用戶端驗證」作為清單中第一個項目的「使用者」範本來簽發用戶端憑證。 
>
>

### <a name="part-4-verify-the-vpn-connection"></a>第 4 部份︰驗證 VPN 連線
1. 若要驗證您的 VPN 連線為作用中狀態，請開啟提升權限的命令提示字元，並執行 *ipconfig/all*。
2. 檢視結果。 請注意，您接收到的 IP 位址是建立 VNet 時所指定之點對站連線位址範圍內的其中一個位址。 結果應該類似下面的內容：

範例：

    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="faq"></a>點對站常見問題集

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>後續步驟

一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。 若要了解網路與虛擬機器的詳細資訊，請參閱 [Azure 與 Linux VM 網路概觀](../virtual-machines/virtual-machines-linux-azure-vm-network-overview.md)。


