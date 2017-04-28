---
title: "使用 Azure 入口網站建立 IoT 中樞 | Microsoft Docs"
description: "如何透過 Azure 入口網站建立、管理和刪除 Azure IoT 中樞。 其中包括定價層、調整、安全性和傳訊組態的相關資訊。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: d88c6e8d4c0c5aecbdbcf6262da8d919ad3b325d
ms.lasthandoff: 01/31/2017


---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>使用 Azure 入口網站建立 IoT 中樞
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介
本文說明如何在 Azure 入口網站中尋找 IoT 中樞服務，以及如何建立和管理 IoT 中樞。

## <a name="where-to-find-iot-hubs"></a>哪裡可以找到 IoT 中樞
您可以在幾個不同的地方找到 IoT 中樞。

1. **+ 新增**：**Azure IoT 中樞**是 IoT 服務，且可以在 [+ 新增] 底下的 [物聯網] 類別中找到，類似於其他服務。
2. IoT 中樞也可以透過 Marketplace 存取為 [物聯網] 底下的主服務。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞
您可以使用下列方法建立 IoT 中樞：

* 透過 [+ 新增]  選項建立 IoT 中樞會導致刀鋒視窗在下一個螢幕擷取畫面中顯示。 透過這個方法以及透過 Marketplace 建立 IoT 中樞的步驟完全相同。
* 透過 Marketplace 建立 IoT 中樞：按一下 [建立] 可開啟刀鋒視窗，和先前 [+ 新增] 體驗的刀鋒視窗相同。 接下來的幾節列出建立 IoT 中樞包含的幾個步驟。

### <a name="choose-the-name-of-the-iot-hub"></a>選擇 IoT 中樞的名稱
若要建立 IoT 中樞，您必須替 IoT 中樞命名。 此名稱必須是 IoT 中樞內唯一的名稱。 解決方案後端不允許中樞重複，所以建議此中樞的名稱盡可能是唯一的。

### <a name="choose-the-pricing-tier"></a>選擇定價層。
您可以從 4 個層級中選擇：**免費**、**標準 1**、**標準 2** 和**標準 S3**。 免費層只允許 500 個裝置連接至 IoT 中樞，每天最多 8,000 封訊息。

**標準 S1**：IoT 中樞 S1 版本的設計對象為具有大量裝置，但每個裝置產生的資料量卻非常少的 IoT 解決方案。 S1 版本的每個單位可讓您跨所有連接的裝置，每天傳輸高達 400,000 封訊息。

**標準 S2**：IoT 中樞 S2 版本的設計對象是裝置會在其中產生大量資料的 IoT 解決方案。 S2 版本的每個單位可讓您跨所有連接的裝置，每天傳輸高達 6 百萬封訊息。

**標準 S3**：IoT 中樞 S3 版本是針對產生大量資料的 IoT 解決方案所設計。 S3 版本的每個單位可讓您跨所有連接的裝置，每天傳輸高達 3 億封訊息。

![][4]

> [!NOTE]
> IoT 中樞只允許每個 Azure 訂用帳戶有一個免費中樞。
> 
> 

### <a name="iot-hub-units"></a>IoT 中樞單位
每天每單位允許的訊息數目取決於您的中樞定價層。 例如，如果您想要 IoT 中樞支援 700,000 封訊息的輸入，您可以選擇 2 個 S1 層單位。

### <a name="device-to-cloud-partitions-and-resource-group"></a>裝置到雲端分割及資源群組
您可以變更 IoT 中樞的分割數目。 預設的分割設定為 4 個。不過，您可以從下拉式清單選擇不同的分割數目。

對資源群組而言，您不需要明確建立空的資源群組。 您可以在建立資源時，選擇建立新的資源群組，或使用現有的資源群組。

![][5]

### <a name="choose-subscriptions"></a>選擇訂用帳戶
Azure IoT 中樞會自動顯示使用者帳戶所連結的 Azure 訂用帳戶清單。 您可以在此選擇其中一個選項，將 IoT 中樞與該 Azure 訂用帳戶產生關聯。

### <a name="choose-the-location"></a>選擇位置
位置選項提供可在其中使用 IoT 中樞的區域清單。 IoT 中樞可在下列地區部署︰澳大利亞東部、澳大利亞東南部、東亞、東南亞、北歐、西歐、日本東部、日本西部、美國東部和美國西部。

### <a name="create-the-iot-hub"></a>建立 IoT 中樞
完成上述的所有步驟之後，現在已經準備好要建立 IoT 中樞。 按一下 [建立]  會啟動利用特定選項建立此 IoT 中樞的後端程序，並將其部署到指定的位置。

建立 IoT 中樞需要幾分鐘的時間，因為要在適當的位置伺服器中進行後端部署需要時間。

## <a name="change-the-settings-of-the-iot-hub"></a>變更 IoT 中樞的設定
從 IoT 中樞刀鋒視窗建立 IoT 中樞後，您可以變更此現有 IoT 中樞的設定。

![][8]

**共用存取原則**：這些原則定義了裝置與服務連接至 IoT 中樞的權限。 您可以按一下 [一般] 之下的 [共用存取原則] 來存取這些原則。 在這個刀鋒視窗中，您可以修改現有的原則或新增原則。

### <a name="create-a-policy"></a>建立原則
* 按一下 [新增] 開啟刀鋒視窗。 您可以在此輸入新的原則名稱以及您想要與此原則產生關聯的權限，如在下一個圖中所示：
  
    有許多權限可與這些共用原則產生關聯。 前兩個原則，**登錄讀取**和**登錄寫入**，適用於授與讀取和寫入存取權給裝置身分識別存放區或身分識別登錄。 選擇寫入選項就會自動選擇讀取選項。
  
     **服務連接** 原則會授與存取雲端端點 (例如連接至 IoT 中樞之服務的用戶群組) 的權限。 **裝置連接** 原則會授與權限，用於傳送和接收 IoT 中樞的裝置端端點上的訊息。
* 按一下 [建立]  將此新建立的原則新增至現有的清單。

![][10]

## <a name="endpoints"></a>端點
按一下 [端點] 以顯示您正在修改之 IoT 中樞的端點清單。 端點有兩個類型︰IoT 中樞內建的端點，以及您在 IoT 中樞建立後新增的端點。

![][11]

### <a name="built-in-endpoints"></a>內建端點
有兩種內建端點︰**雲端到裝置回饋**和**事件**。

* **雲端到裝置回饋**設定：此設定有 2 個子設定：訊息的**雲端到裝置 TTL** (存留時間) 和**保留時間** (以小時為單位)。 當您第一次建立 IoT 中樞時，這兩個設定的預設值都是一個小時。 若要調整這些設定，可使用滑桿或輸入值。
* **事件**設定：這個設定有數個子設定，其中有些是唯讀。 下列清單說明這些設定：

    * **分割**：當 IoT 中樞建立後會設定預設值。 您可以透過此設定變更分割數目。

    * **事件中樞相容的名稱和端點**：建立 IoT 中樞時，事件中樞會在內部建立，而您在某些情況下可能需要其存取權。 您無法自訂事件中樞的相容名稱和端點值，但可以按一下 [複製] 來複製它們。

    * **保留時間**：預設為一天，但可以使用下拉式清單變更。 這是裝置到雲端設定以天為單位的值。

    * **取用者群組**：取用者群組是類似於其他傳訊系統的設定，可以利用特定方式提取資料以將其他應用程式或服務連接到 IoT 中樞。 每個 IoT 中樞都是使用預設取用者群組建立的。 不過，您可以使用這個設定新增或刪除 IoT 中樞的取用者群組。

    > [!NOTE]
    > 預設的取用者群組無法編輯或刪除。
    > 
    > 

### <a name="custom-endpoints"></a>自訂端點
您可以使用入口網站在 IoT 中樞上新增自訂端點。 從 [端點] 刀鋒視窗，按一下頂端的 [新增] 以開啟 [新增端點] 刀鋒視窗。 輸入必要資訊，然後按一下 [確定]。 您的自訂端點現在會列在主要 [端點] 刀鋒視窗中。

![][13]

您可以在[參考 - IoT 中樞端點][lnk-devguide-endpoints]中深入了解自訂端點。

## <a name="routes"></a>路由
按一下 [路由] 以管理 IoT 中樞分派您的裝置到雲端訊息的方式。

![][14]

您可以新增路由到 IoT 中樞，方法是按一下 [路由]*刀鋒視窗頂端的 [新增]，輸入必要資訊然後按一下 [確定]**。您的路由接著會列在主要 [路由]** 刀鋒視窗中。您可以在路由清單中按一下路由來編輯它。若要啟用路由，按一下清單中的路由並將 [已啟用]** 切換為 [關閉]**。按一下刀鋒視窗底部的 [確定]** 來儲存變更。

![][15]

## <a name="pricing-and-scale"></a>價格和調整
現有 IoT 中樞的價格可透過 [價格]  設定變更，但是有下列例外狀況：

* 在目前的實作中，具有免費 SKU 的 IoT 中樞無法變更為付費 SKU 層，反之亦然。
* 在 Azure 訂用帳戶中只能有一個免費層 IoT 中樞。

![][12]

只有在當天傳送的訊息數目不會衝突時，才允許從較高的層 (S2 或 S3) 移至較低層 (S1 或 S2)。 例如，如果每天的訊息數目都超過 400,000，則 IoT 中樞的層會變更。 不過，如果您變更為 S1 層，IoT 中樞當天會進行節流。

## <a name="delete-the-iot-hub"></a>刪除 IoT 中樞
您可以藉由按一下 [瀏覽] ，然後選擇要刪除的適當中樞，即可瀏覽至您想要刪除的 IoT 中樞。 按一下 IoT 中樞名稱下方的 [刪除]  按鈕會刪除 IoT 中樞。

## <a name="next-steps"></a>後續步驟
遵循下列連結以深入了解如何管理 Azure IoT 中樞：

* [大量管理 IoT 裝置][lnk-bulk]
* [IoT 中樞度量][lnk-metrics]
* [作業監視][lnk-monitor]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南][lnk-devguide]
* [使用 IoT 閘道 SDK 來模擬裝置][lnk-gateway]
* [徹底保護您的 IoT 解決方案][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md

