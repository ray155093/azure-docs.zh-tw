---
title: "IoT 中樞 - IP 篩選器 | Microsoft Docs"
description: "本教學課程說明如何將 Azure IoT 中樞的特定 IP 位址加入封鎖清單或允許清單。"
services: iot-hub
documentationcenter: 
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: f833eac3-5b5f-46a7-a47b-f4f6fc927f3f
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: 457d0d97601e18de2a19b83be100954d01f9bbf8
ms.openlocfilehash: 86622918a136da4c478c8d47a677a67e46eb093b


---

# <a name="ip-filter"></a>IP 篩選器

安全性是任何以 Azure IoT 中樞為基礎之 IoT 解決方案的重要一環。 在設定安全性的過程中，有時候您需要將特定 IP 位址加入封鎖清單或允許清單。 IP 篩選器功能可讓您設定規則，以拒絕或接受來自特定 IPv4 位址的流量。

## <a name="when-to-use"></a>使用時機

有兩個特定使用案例適合封鎖特定 IP 位址的 IoT 中樞端點︰

- 當 IoT 中樞只應接收來自指定 IP 位址範圍的流量，並拒絕其他所有流量時。 例如，當您搭配使用 IoT 中樞與 [Azure Express Route] 來建立 IoT 中樞與內部部署基礎結構之間的私人連線時。
- 當您需要拒絕 IoT 中樞系統管理員認為可疑的 IP 位址所傳來的流量時。

## <a name="how-filter-rules-are-applied"></a>篩選器規則的套用方式

IP 篩選器規則會套用在 IoT 中樞服務層級。 因此，IP 篩選器規則會套用到裝置和後端應用程式使用任何受支援通訊協定所建立的所有連線上。

嘗試建立連線的 IP 位址若符合 IoT 中樞內的拒絕 IP 規則，將會收到未授權 401 狀態碼和描述。 回應訊息則不涉及 IP 規則。

## <a name="default-setting"></a>預設設定
根據預設，IoT 中樞之入口網站中的 **IP 篩選器**方格是空的。 這項預設設定表示您的中樞可接受來自任何 IP 位址的連線。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。

![][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>新增或編輯 IP 篩選器規則

當您新增 IP 篩選器規則時，系統會提示您輸入下列值︰

- [IP 篩選器規則名稱] 必須是唯一、不區分大小寫的英數字元字串，最長可為 128 個字元。 所能接受的字元只有 ASCII 7 位元英數字元以及 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。
- 選取 [拒絕] 或 [接受] 做為 IP 篩選器規則的 [動作]。
- 提供單一 IPv4 位址或以 CIDR 表示法表示的 IP 位址區塊。 例如，在 CIDR 表示法中，192.168.100.0/22 表示從 192.168.100.0 到 192.168.103.255 的 1024 個 IPv4 位址。

![][img-ip-filter-add-rule]

儲存規則之後，您會看到通知您正在進行更新的警示。

![][img-ip-filter-save-new-rule]

當您達到十個 IP 篩選器規則的上限後，[新增] 選項便會停用。

您可以按兩下包含規則的資料列，以編輯現有規則。

## <a name="delete-an-ip-filter-rule"></a>刪除 IP 篩選器規則

若要刪除 IP 篩選器規則，請選取方格中的一個或多個規則，然後按一下 [刪除]。

![][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>IP 篩選器規則評估

IP 篩選器規則會依序套用，第一個符合 IP 位址的規則會決定接受或拒絕動作。

例如，如果您想要接受 192.168.100.0/22 範圍中的位址，並拒絕其他所有位址，則方格中的第一個規則應接受位址範圍 192.168.100.0/22。 下一個規則應使用 0.0.0.0/0 範圍拒絕所有位址。 如果您新增的最後一個規則是拒絕 0.0.0.0/0 範圍，則會將預設行為變更為加入允許清單。

按一下資料列前端呈垂直方向的三個點並使用拖放功能，即可變更方格中的 IP 篩選器規則順序。

若要儲存新的 IP 篩選器規則順序，請按一下 [儲存]。

![][img-ip-filter-rule-order]

## <a name="next-steps"></a>後續步驟

若要進一步探索 IoT 中樞的功能，請參閱︰

* [作業監視][lnk-monitor]
* [IoT 中樞度量][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT 中心開發人員指南]: iot-hub-devguide.md
[Azure Express Route]:  https://azure.microsoft.com/en-us/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md


<!--HONumber=Nov16_HO3-->


