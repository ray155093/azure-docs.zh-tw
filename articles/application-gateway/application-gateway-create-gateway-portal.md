---
title: "使用入口網站建立應用程式閘道 | Microsoft Docs"
description: "了解如何使用入口網站來建立「應用程式閘道」"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 2195eb4ce0e22c8c7c72ac0638ab927f13c4d454
ms.contentlocale: zh-tw
ms.lasthandoff: 05/02/2017


---
# <a name="create-an-application-gateway-by-using-the-portal"></a>使用入口網站建立應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 傳統 PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager 範本](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

了解如何使用 SSL 卸載建立應用程式閘道。

![案例範例][scenario]

應用程式閘道是專用的虛擬設備，會以服務形式提供應用程式傳遞控制器 (ADC)，為您的應用程式提供各種第 7 層負載平衡功能。

此案例將會：

1. [建立中型應用程式閘道](#create-an-application-gateway)，閘道會使用 SSL 卸載，且其子網路中含有兩個執行個體。
1. [將伺服器新增到後端集區](#add-servers-to-backend-pools)
1. [刪除所有資源](#delete-all-resources)。 當您在佈建時，您在這個練習中建立的某些資源會產生費用。 若要將費用降至最低，完成練習之後，務必完成此區段中的步驟以刪除您建立的資源。



> [!IMPORTANT]
> 其他應用程式閘道組態 (包括自訂健康狀況探查、後端集區位址及其他規則) 會在設定應用程式閘道設定之後才進行設定，而不會在初始部署期間設定。

## <a name="create-an-application-gateway"></a>建立應用程式閘道

若要建立應用程式閘道，您需完成下列步驟。 應用程式閘道需要有自己的子網路。 建立虛擬網路時，請確定您保留足夠的位址空間，以便擁有多個子網路。 將應用程式閘道部署到子網路之後，就只能將額外的應用程式閘道新增到該子網路。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 如果您沒有帳戶，可以註冊[免費試用一個月](https://azure.microsoft.com/free)
1. 在入口網站的 [我的最愛] 窗格中，按一下 [新增]
1. 在 [新增] 刀鋒視窗中，按一下 [網路]。 在 [網路] 刀鋒視窗中，按一下 [應用程式閘道]，如下圖中所示：

    ![建立應用程式閘道][1]

1. 在顯示的 [基本] 刀鋒視窗中，輸入下列值，然後按一下 [確定]：

   | **設定** | **值** | **詳細資料**
   |---|---|---|
   |**名稱**|AdatumAppGateway|應用程式閘道的名稱|
   |**層級**|標準|可用的值為「標準」或 WAF。 若要深入了解 WAF，請瀏覽 [Web 應用程式防火牆](application-gateway-web-application-firewall-overview.md)。|
   |**SKU 大小**|中型|當選擇「標準」層級時，選項為 [小型]、[中型] 和 [大型]。 當選擇 WAF 層級時，選項只有 [中型] 和 [大型]。|
   |**執行個體計數**|2|高可用性的應用程式閘道執行個體數目。 執行個體計數 1 應僅用於測試目的。|
   |**訂用帳戶**|[您的訂用帳戶]|選取要在其中建立應用程式閘道的訂用帳戶。|
   |**資源群組**|**建立新項目：**AdatumAppGatewayRG|建立資源群組。 資源群組名稱在您選取的訂用帳戶中必須是唯一的。 若要深入了解資源群組，請閱讀 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) 概觀。|
   |**位置**|美國西部||

1. 在 [虛擬網路] 底下顯示的 [設定] 刀鋒視窗中，按一下 [選擇虛擬網路]。 這會開啟進入 [選擇虛擬網路] 刀鋒視窗。  按一下 [建立新項目] 以開啟 [建立虛擬網路] 刀鋒視窗。

 ![選擇虛擬網路][2]

1. 在 [建立虛擬網路] 刀鋒視窗中輸入下列值，然後按一下 [確定]。 這將會關閉 [建立虛擬網路] 和 [選擇虛擬網路] 刀鋒視窗。 這也會使用所選的子網路，填入 [設定] 刀鋒視窗的 [子網路] 欄位。

   |**設定** | **值** | **詳細資料** |
   |---|---|---|
   |**名稱**|AdatumAppGatewayVNET|應用程式閘道的名稱|
   |**位址空間**|10.0.0.0/16| 這是虛擬網路的位址空間|
   |**子網路名稱**|AppGatewaySubnet|應用程式閘道的子網路名稱|
   |**子網路位址範圍**|10.0.0.0/28| 這個子網路允許在虛擬網路中有更多其他的子網路，以供後端集區成員使用|

1. 在 [前端 IP 設定] 底下的 [設定] 刀鋒視窗中，選擇 [公用] 做為 [IP 位址類型]

1. 在 [公用 IP 位址] 底下的 [設定] 刀鋒視窗中，按一下 [選擇公用 IP 位址]。如此會開啟 [選擇公用 IP 位址] 刀鋒視窗，然後按一下 [建立新項目]。

 ![選擇公用 IP][3]

1. 在 [建立公用 IP 位址] 刀鋒視窗中，接受預設值，並按一下 [確定]。 這將會關閉 [選擇公用 IP 位址] 刀鋒視窗、[建立公用 IP 位址] 刀鋒視窗，並使用所選的公用 IP 位址填入 [公用 IP 位址]。

1. 在 [接聽程式設定] 底下的 [設定] 刀鋒視窗中，按一下 [通訊協定] 底下的 [HTTPS]。 這麼做會新增額外欄位。 按一下 [上傳 PFX 憑證] 欄位的資料夾圖示，然後選擇適當的 .pfx 憑證。 在額外的 [接聽程式設定] 欄位中輸入下列資訊：

   |**設定** | **值** | **詳細資料** |
   |---|---|---|
   |名稱|憑證的名稱|這個值是用於參考憑證的易記名稱|
   |密碼|.pfx 的密碼| 這是用於私密金鑰的密碼|

1. 按一下 [設定] 刀鋒視窗上的 [確定] 以繼續。

1. 檢閱 [摘要] 刀鋒視窗上的設定，然後按一下 [確定] 以開始建立應用程式閘道。 建立應用程式閘道是漫長的工作，而且需要一段時間才能完成。

## <a name="add-servers-to-backend-pools"></a>將伺服器新增到後端集區

建立應用程式閘道後，仍需將裝載要進行負載平衡之應用程式的系統新增至應用程式閘道。 這些伺服器的 IP 位址、FQDN 或 NIC 會新增至後端位址集區。

### <a name="ip-address-or-fqdn"></a>IP 位址或 FQDN

1. 建立應用程式閘道之後，在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 按一下 [所有資源] 刀鋒視窗中的 [AdatumAppGateway] 應用程式閘道。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選] 方塊中輸入 **AdatumAppGateway** 輕鬆地存取應用程式閘道。

1. 系統會顯示您建立的應用程式閘道。 按一下 [後端集區]，然後選取目前的後端集區 [appGatewayBackendPool]。如此會開啟 [appGatewayBackendPool] 刀鋒視窗。

   ![應用程式閘道後端集區][4]

1. 按一下 [新增目標] 以新增 FQDN 值的 IP 位址。 選擇 [IP 位址或 FQDN] 當作 [類型]，並在欄位中輸入您的 IP 位址或 FQDN。 針對其他的後端集區成員重複這個步驟。 完成時，按一下 [儲存]。

### <a name="virtual-machine-and-nic"></a>虛擬機器和 NIC

您也可以將虛擬機器 NIC 新增為後端集區成員。 只有與應用程式閘道相同的虛擬網路內的虛擬機器可透過下拉式清單來取得。

1. 建立應用程式閘道之後，在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 按一下 [所有資源] 刀鋒視窗中的 [AdatumAppGateway] 應用程式閘道。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選] 方塊中輸入 **AdatumAppGateway** 輕鬆地存取應用程式閘道。

1. 系統會顯示您建立的應用程式閘道。 按一下 [後端集區]，然後選取目前的後端集區 [appGatewayBackendPool]。如此會開啟 [appGatewayBackendPool] 刀鋒視窗。

   ![應用程式閘道後端集區][5]

1. 按一下 [新增目標] 以新增 FQDN 值的 IP 位址。 選擇 [虛擬機器] 當作 [類型]，並選取要使用的虛擬機器和 NIC。 完成時，按一下 [儲存]

   > [!NOTE]
   > 下拉式方塊中只提供位於應用程式閘道相同虛擬網路內的虛擬機器。

## <a name="delete-all-resources"></a>刪除所有資源

若要刪除這篇文章中建立的所有資源，請完成下列步驟︰

1. 在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 在 [所有資源] 刀鋒視窗中，按一下 [AdatumAppGatewayRG] 資源群組。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選] 方塊中輸入 **AdatumAppGatewayRG** 輕鬆地存取資源群組。
1. 在 [AdatumAppGatewayRG] 刀鋒視窗中，按一下 [刪除] 按鈕。
1. 入口網站會要求您輸入資源群組的名稱，以確認您想要刪除它。 按一下 [刪除]，輸入 AdatumAppGateway 做為資源群組名稱，然後按一下 [刪除]。 刪除資源群組會刪除資源群組內的所有資源，所以務必確認資源群組的內容，然後再刪除它。 入口網站會刪除資源群組內包含的所有資源，然後刪除資源群組本身。 這個程序需要幾分鐘的時間。

## <a name="next-steps"></a>後續步驟

此案例會建立預設應用程式閘道。 後續步驟用於設定應用程式閘道，方法是修改設定，以及調整閘道中的規則。 瀏覽下列文章即可找到這些步驟：

參閱 [建立自訂健康狀態探查](application-gateway-create-probe-portal.md)

參閱 [設定 SSL 卸載](application-gateway-ssl-portal.md)

了解如何使用應用程式閘道的功能 - [Web 應用程式防火牆](application-gateway-webapplicationfirewall-overview.md)來保護您的應用程式。

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

