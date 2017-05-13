---
title: "建立 Azure 應用程式閘道 - Azure CLI 2.0 | Microsoft Docs"
description: "了解如何使用 Resource Manager 中的 Azure CLI 2.0 建立應用程式閘道"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 14dab2197ff7c1eaff012066e321ef1b99f05bb3
ms.contentlocale: zh-tw
ms.lasthandoff: 05/02/2017


---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 建立應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 傳統 PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager 範本](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

Azure 應用程式閘道是第 7 層負載平衡器。 不論是在雲端或內部部署中，此閘道均提供在不同伺服器之間進行容錯移轉及效能路由傳送 HTTP 要求。 應用程式閘道具有下列應用程式傳遞功能：HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性、「安全通訊端層」(SSL) 卸載、自訂健康狀態探查，以及多站台支援。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本

您可以使用下列其中一個 CLI 版本來完成工作︰

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md) - 適用於傳統和資源管理部署模型的 CLI。
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md) - 適用於資源管理部署模型的新一代 CLI

## <a name="prerequisite-install-the-azure-cli-20"></a>必要條件：安裝 Azure CLI 2.0

若要執行本文的步驟，您需要[安裝適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)。

> [!NOTE]
> 如果您沒有 Azure 帳戶，就需要申請一個。 請 [在此處註冊免費試用](../active-directory/sign-up-organization.md)。

## <a name="scenario"></a>案例

在此案例中，您將了解如何使用 Azure 入口網站來建立應用程式閘道。

此案例將會：

* 建立含有兩個執行個體的中型應用程式閘道。
* 建立名為 AdatumAppGatewayVNET 且含有 10.0.0.0/16 保留 CIDR 區塊的虛擬網路。
* 建立名為 Appgatewaysubnet 且使用 10.0.0.0/28 做為其 CIDR 區塊的子網路。
* 為 SSL 卸載設定憑證。

![案例範例][scenario]

> [!NOTE]
> 其他應用程式閘道組態 (包括自訂健康狀況探查、後端集區位址及其他規則) 會在設定應用程式閘道設定之後才進行設定，而不會在初始部署期間設定。

## <a name="before-you-begin"></a>開始之前

「Azure 應用程式閘道」需要有自己的子網路。 建立虛擬網路時，請確定您保留足夠的位址空間，以便擁有多個子網路。 將應用程式閘道部署到子網路之後，就只能將額外的應用程式閘道新增到該子網路。

## <a name="log-in-to-azure"></a>登入 Azure

開啟 [Microsoft Azure 命令提示字元] 並登入。 

```azurecli
az login -u "username"
```

> [!NOTE]
> 您也可以使用 `az login` 而不搭配會要求在 aka.ms/devicelogin 輸入代碼的裝置登入參數。

輸入上述範例後會提供程式碼。 在瀏覽器中瀏覽至 https://aka.ms/devicelogin 以繼續登入程序。

![顯示裝置登入的 cmd][1]

在瀏覽器中，輸入收到的程式碼。 系統會將您重新導向至 [登入] 頁面。

![要輸入程式碼的瀏覽器][2]

輸入程式碼後您已登入，請關閉瀏覽器以繼續進行案例。

![已順利登入][3]

## <a name="create-the-resource-group"></a>建立資源群組

建立應用程式閘道之前，會建立資源群組以包含應用程式閘道。 以下顯示命令。

```azurecli
az resource group create --name myresourcegroup --location "West US"
```

## <a name="create-a-virtual-network-and-subnet"></a>建立虛擬網路和子網路

建立資源群組後，就會為應用程式閘道建立虛擬網路。  在下列範例中，已為虛擬網路定義 10.0.0.0/16 的位址空間，並為子網路定義 10.0.0.0/28 的位址空間，如前一個案例的附註所示。

```azurecli
az network vnet create \
--name AdatumAppGatewayVNET \
--address-prefix 10.0.0.0/16 \
--subnet-name Appgatewaysubnet \
--subnet-prefix 10.0.0.0/28 \
--resource-group AdatumAppGateway \
--location eastus
```

## <a name="create-the-application-gateway"></a>建立應用程式閘道

建立虛擬網路和子網路後，便已完成應用程式閘道的先決條件。 此外下列步驟也需要先前匯出的 .pfx 憑證和憑證密碼︰用於後端的 IP 位址是後端伺服器的 IP 位址。 這些值可以是虛擬網路中的私人 IP、公用 IP 或後端伺服器的完整網域名稱。

```azurecli
az network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group AdatumAppGatewayRG \
--vnet-name AdatumAppGatewayVNET \
--vnet-address-prefix 10.0.0.0/16 \
--subnet Appgatewaysubnet \
--subnet-address-prefix 10.0.0.0/28 \
--servers 10.0.0.4 10.0.0.5 \
--cert-file /mnt/c/Users/username/Desktop/application-gateway/fabrikam.pfx \
--cert-password P@ssw0rd \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 443 \
--routing-rule-type Basic \
--http-settings-port 80

```

> [!NOTE]
> 如需可在建立期間提供的參數清單，請執行下列命令︰**az network application-gateway create --help**。

此範例會建立一個具有接聽程式、後端集區、後端 http 設定及規則之預設設定的基本應用程式閘道。 也會設定 SSL 卸載。 佈建成功之後，您可以依據您的部署需求修改這些設定。
如果在先前步驟中已經以後端集區定義 Web 應用程式，一旦建立之後，負載平衡即開始。

## <a name="delete-all-resources"></a>刪除所有資源

若要刪除這篇文章中建立的所有資源，請完成下列步驟︰

```azurecli
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>後續步驟

參閱 [建立自訂健康狀態探查](application-gateway-create-probe-portal.md)

參閱 [設定 SSL 卸載](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

