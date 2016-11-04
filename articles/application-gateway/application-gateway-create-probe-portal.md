---
title: 使用入口網站建立應用程式閘道的自訂探查 | Microsoft Docs
description: 了解如何使用入口網站建立應用程式閘道的自訂探查
services: application-gateway
documentationcenter: na
author: georgewallace
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2016
ms.author: gwallace

---
# 使用入口網站建立應用程式閘道的自訂探查
> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 傳統 PowerShell](application-gateway-create-probe-classic-ps.md)
> 
> 

<BR>

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## 案例
下列案例會完成在現有應用程式閘道中建立自訂健康狀態探查的程序。此案例假設您已經依照步驟[建立應用程式閘道](application-gateway-create-gateway-portal.md)。

## <a name="createprobe"></a>建立探查
您可以透過入口網站中兩個步驟的程序來設定探查。第一個步驟是建立探查，接著是將探查新增到應用程式閘道的後端 http 設定。

### 步驟 1
瀏覽至 http://portal.azure.com，然後選取現有的應用程式閘道。

![應用程式閘道概觀][1]

### 步驟 2
按一下 [探查]，然後按一下 [加入] 按鈕來加入新的探查。

![已填入資訊的 [新增探查] 刀鋒視窗][2]

### 步驟 3
填入必要的探查資訊，完成時，按一下 [確定]。

* **名稱** - 這是可在入口網站中存取的易記探查名稱。
* **主機** - 這是用於探查的主機名稱。
* **路徑** - 完整自訂探查 URL 的其餘部分。
* **間隔 (秒)** - 執行探查以檢查健康狀態的頻率。
* **逾時 (秒)** - 探查逾時前所等待的時間。
* **狀況不良閾值** - 視為狀況不良的失敗嘗試次數。

> [!IMPORTANT]
> 主機名稱不是伺服器名稱。這是在應用程式伺服器上執行的虛擬主機名稱。探查會傳送到 http://(host 名稱):(來自 httpsetting 的連接埠)/urlPath
> 
> 

![探查組態設定][3]

## 將探查新增到閘道
既然已經建立探查，此時即可將它新增到閘道。探查設定是在應用程式閘道的後端 http 設定上設定。

### 步驟 1
按一下應用程式閘道的 [HTTP 設定]，然後按一下視窗中目前的後端 http 設定來開啟組態刀鋒視窗。

![[https 設定] 視窗][4]

### 步驟 2
在 [appGatewayBackEndHttp] 設定刀鋒視窗中，按一下 [使用自訂探查]，然後選擇在[建立探查](#createprobe)一節中建立的探查。完成時，按一下 [確定] 即可套用設定。

![[appgatewaybackend 設定] 刀鋒視窗][5]

預設探查會檢查 Web 應用程式的預設存取權。既然已經建立自訂探查，應用程式閘道便可使用已定義的自訂路徑來監視所選後端的健康狀態。應用程式閘道會根據已定義的準則檢查探查中指定的檔案。如果對 host:Port/path 的呼叫沒有傳回 Http 200 OK 狀態回應，當達到狀況不良閾值時，系統就會將該伺服器從輪替的行列中移除。探查會繼續在狀況不良的執行個體上執行，以判斷它何時再次變成狀況良好。系統將執行個體重新加回至狀況良好的伺服器集區之後，流量就會再次開始流向它，而對執行個體的探查則會如常依據使用者指定的間隔繼續執行。

## 後續步驟
若要了解如何設定與「Azure 應用程式閘道」搭配運作的「SSL 卸載」，請參閱[設定 SSL 卸載](application-gateway-ssl-portal.md)。

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png

<!---HONumber=AcomDC_0810_2016------>