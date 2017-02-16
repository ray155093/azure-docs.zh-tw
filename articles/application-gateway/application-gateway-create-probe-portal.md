---
title: "使用入口網站建立應用程式閘道的自訂探查 | Microsoft Docs"
description: "了解如何使用入口網站建立應用程式閘道的自訂探查"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: aaf13418331f29287399621cb911e4b9f5b33dc0
ms.openlocfilehash: 0fafaff220dfb5d224bc5b57b630f8b2912155f7


---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>使用入口網站建立應用程式閘道的自訂探查
> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 傳統 PowerShell](application-gateway-create-probe-classic-ps.md)

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>案例

下列案例會完成在現有應用程式閘道中建立自訂健康狀態探查的程序。
此案例假設您已經依照步驟 [建立應用程式閘道](application-gateway-create-gateway-portal.md)。

## <a name="a-namecreateprobeacreate-the-probe"></a><a name="createprobe"></a>建立探查

您可以透過入口網站中兩個步驟的程序來設定探查。 第一個步驟是建立探查。 接下來將探查新增至應用程式閘道的後端 http 設定。

### <a name="step-1"></a>步驟 1

瀏覽到 [Azure 入口網站](http://portal.azure.com)，然後選取現有的應用程式閘道。

![應用程式閘道概觀][1]

### <a name="step-2"></a>步驟 2

按一下 [探查]，然後按一下 [加入] 按鈕來新增探查。

![已填入資訊的 [新增探查] 刀鋒視窗][2]

### <a name="step-3"></a>步驟 3

填入必要的探查資訊，完成時，按一下 [確定] 。

* **名稱** - 此值是可在入口網站中存取的易記探查名稱。
* **主機** - 此值是用於探查的主機名稱。 只有當應用程式閘道上設定多站台時適用，否則請使用 '127.0.0.1'。 此值與 VM 主機名稱不同。
* **路徑** - 完整自訂探查 URL 的其餘部分。 有效路徑的開頭為 '/'
* **間隔 (秒)** - 執行探查以檢查健康狀態的頻率。 建議您不要設定低於 30 秒。
* **逾時 (秒)** - 探查逾時前所等待的時間。 逾時間隔需要高到足以進行 http 呼叫，以確保可使用後端的健康狀態頁面。
* **狀況不良閾值** - 視為狀況不良的失敗嘗試次數。 臨界值為 0 表示，如果健康狀態檢查失敗，後端會被立即斷定為狀況不良。

> [!IMPORTANT]
> 主機名稱與伺服器名稱不同。 此值是在應用程式伺服器上執行的虛擬主機名稱。 探查會傳送到 http://(host name):(port from httpsetting)/urlPath

![探查組態設定][3]

## <a name="add-probe-to-the-gateway"></a>將探查新增到閘道

既然已經建立探查，此時即可將它新增到閘道。 探查設定是在應用程式閘道的後端 http 設定上設定。

### <a name="step-1"></a>步驟 1

按一下應用程式閘道的 [HTTP 設定]來開啟組態刀鋒視窗，然後按一下視窗中目前的後端 http 設定。

![[https 設定] 視窗][4]

### <a name="step-2"></a>步驟 2

在 [appGatewayBackEndHttp] 設定刀鋒視窗中，按一下 [使用自訂探查]，然後選擇在[建立探查](#createprobe)一節中建立的探查。
完成時，按一下 [確定]  即可套用設定。

![[appgatewaybackend 設定] 刀鋒視窗][5]

預設探查會檢查 Web 應用程式的預設存取權。 既然已經建立自訂探查，應用程式閘道便可使用已定義的自訂路徑來監視所選後端的健康狀態。 應用程式閘道會根據已定義的準則檢查探查中指定的檔案。 如果對 host:Port/path 的呼叫沒有傳回 Http 200 OK 狀態回應，當達到狀況不良閾值時，系統就會將該伺服器從輪替的行列中移除。 探查會繼續在狀況不良的執行個體上執行，以判斷它何時再次變成狀況良好。 系統將執行個體重新加回至狀況良好的伺服器集區之後，流量就會再次開始流向它，而對執行個體的探查則會如常依據使用者指定的間隔繼續執行。

## <a name="next-steps"></a>後續步驟

若要了解如何設定與「Azure 應用程式閘道」搭配運作的「SSL 卸載」，請參閱 [設定 SSL 卸載](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png



<!--HONumber=Dec16_HO3-->


