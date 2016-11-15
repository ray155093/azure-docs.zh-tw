---
title: "URL 型內容路由概觀 | Microsoft Docs"
description: "此頁面提供應用程式閘道 URL 型內容路由、UrlPathMap 組態和 PathBasedRouting 規則的概觀。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 4409159b-e22d-4c9a-a103-f5d32465d163
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ea30057b62bdf926d6e39c170c4ad32afb202aef


---
# <a name="url-path-based-routing-overview"></a>URL 路徑型路由概觀
URL 路徑型路由可讓您根據要求的 URL 路徑，將流量路由傳送至後端伺服器集區。 有一個案例是將對於不同內容類型的要求路由傳送至不同的後端伺服器集區。
在下列範例中，應用程式閘道會針對 contoso.com 從三個後端伺服器集區提供流量，例如：VideoServerPool、ImageServerPool 和 DefaultServerPool。

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

對 http://contoso.com/video* 的要求會路由傳送至 VideoServerPool，而 http://contoso.com/images* 則會路由傳送至 ImageServerPool。 如果沒有任何路徑模式相符，則會選取 DefaultServerPool。

## <a name="urlpathmap-configuration-element"></a>UrlPathMap 組態元素
UrlPathMap 元素是用來指定與後端伺服器集區對應的路徑模式。 下列程式碼範例是來自範本檔案的 urlPathMap 元素程式碼片段。

```json
"urlPathMaps": [
{
"name": "<urlPathMapName>",
"id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>",
"properties": {
    "defaultBackendAddressPool": {
        "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName>"
    },
    "defaultBackendHttpSettings": {
        "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpSettingsList/<settingsName>"
    },
    "pathRules": [
        {
            "paths": [
                <pathPattern>
            ],
            "backendAddressPool": {
                "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName2>"
            },
            "backendHttpsettings": {
                "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpsettingsList/<settingsName2>"
            },

        },

    ],

}
}
]
```

> [!NOTE]
> PathPattern：此設定是要比對的路徑模式清單。 每個都必須以 / 開始，而且唯一允許出現 "*" 的地方是緊接在 "/" 之後的結尾處。 傳送給路徑比對器的字串未在第一個 ? 或 # 之後包含任何文字，而這些字元在此處是不允許的。 
> 
> 

如需詳細資訊，您可以查看 [使用 URL 型路由的 Resource Manager 範本](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) 。

## <a name="pathbasedrouting-rule"></a>PathBasedRouting 規則
類型 PathBasedRouting 的 RequestRoutingRule 可用來將接聽程式繫結至 urlPathMap。 針對此接聽程式接收到的所有要求都會根據 urlPathMap 中指定的原則進行路由傳送。
PathBasedRouting 規則的程式碼片段：

```json
"requestRoutingRules": [
    {

"name": "<ruleName>",
"id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/requestRoutingRules/<ruleName>",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>"
    },

}
    }
]
```

## <a name="next-steps"></a>後續步驟
了解 URL 型內容路由之後，請移至 [使用 URL 型路由建立應用程式閘道](application-gateway-create-url-route-portal.md) ，利用 URL 路由規則來建立應用程式閘道。




<!--HONumber=Nov16_HO2-->


