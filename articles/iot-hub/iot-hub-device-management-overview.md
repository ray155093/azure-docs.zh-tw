---
title: 裝置管理概觀 | Microsoft Docs
description: Azure IoT 中樞裝置管理概觀
services: iot-hub
documentationcenter: ''
author: bzurcher
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2016
ms.author: bzurcher

---
# Azure IoT 中樞裝置管理概觀 (預覽)
## Azure IoT 裝置管理方法
Azure IoT 中樞裝置管理可提供功能和擴充性模型，讓裝置和後端得以利用 IoT 裝置管理，應付 IoT 中各式各樣的裝置和通訊協定。IoT 內的裝置包羅萬象，從非常受限的感應器、單一用途的微控制器，到功能更強大、可啟用其他裝置和通訊協定的閘道器都是其中一分子。由於垂直領域內的操作員各有其獨特的使用案例，因此 IoT 解決方案在這些領域和應用程式的變化也相當大。IoT 解決方案可以利用 IoT 中樞裝置管理的功能、模式和程式碼程式庫，來對裝置和使用者所形成的各種組合實行裝置管理。

## 簡介
建立成功 IoT 解決方案的關鍵在於，針對操作員該如何持續管理其裝置群組提供策略。IoT 操作員需要簡單可靠的工具和應用程式，讓他們能夠專注於作業中更為重要的層面上。Azure IoT 中樞可提供您用於建立 IoT 應用程式的建置組塊，以幫助您建立最重要的裝置管理模式。

裝置若執行可將其安全地連線到雲端的簡單應用程式 (稱為裝置管理代理程式)，就會被視為受到 IoT 中樞管理。該代理程式的程式碼可讓位於應用程式端的操作員，從遠端證明裝置的狀態並執行管理作業，例如套用網路組態變更或部署韌體更新。

## IoT 裝置管理原則
IoT 本身伴隨著一組獨特的管理挑戰，因此解決方案必須考慮到下列 IoT 裝置管理原則︰

![][img-dm_principles]

* **規模和自動化**：IoT 需要簡單的工具，以便能夠自動執行例行工作，並且只需要少數作業人員就可以管理數百萬個裝置。每一天，操作員無不期望能夠從遠端大量處理裝置作業，並且只在發生需要他們直接關注的問題時才接獲通知。
* **開放性和相容性**：IoT 裝置生態系統富含多樣性。管理工具必須經過量身打造，才能配合數量眾多的裝置類別、平台和通訊協定。操作員必須能夠支援所有裝置，不論是最受限的內嵌單一處理晶片，乃至功能完整而強大的電腦。
* **內容感知**：IoT 環境是動態且不斷變化的，因此服務的可靠性非常重要。裝置管理作業必須納入 SLA 維護時段、網路和電源狀態、使用中況況和裝置地理位置等因素，以確保該維護停機時間不會影響重要的商業運作或造成危險情況。
* **服務眾多角色**︰是否能夠支援 IoT 作業角色的獨特工作流程和處理程序非常重要。作業人員也必須能夠平順地配合內部 IT 部門所提出的約束條件，並將相關的裝置作業資訊提交給主管和其他管理角色。

## IoT 裝置的生命週期
雖然 IoT 專案差異極大，但在裝置管理方面仍有一套常見的模式。在 Azure IoT 中，您可於 IoT 裝置的生命週期內發現這些模式，其由五個不同的階段所組成︰

![][img-device_lifecycle]

1. **計劃**︰讓操作員得以建立裝置屬性配置，以便他們可以輕鬆且精確地查詢和鎖定要進行大量管理作業的裝置群組。
   
    相關建置組塊：[開始使用攣生裝置][lnk-twins-getstarted]、[使用攣生屬性的方式][lnk-twin-properties]
2. **佈建**︰安全地向 IoT 中樞驗證新裝置，並讓操作員可以立即探索裝置功能與目前的狀態。
   
    相關建置組塊：[開始使用 IoT 中樞][lnk-hub-getstarted]、[使用攣生屬性的方式][lnk-twin-properties]
3. **設定**︰協助裝置進行大量組態變更和韌體更新，同時維持健康狀態與安全性。
   
    相關建置組塊：[使用攣生屬性的方式][lnk-twin-properties]、[C2D 方法][lnk-c2d-methods]、[排程/廣播作業][lnk-jobs]
4. **監視**︰監視裝置群的整體健康狀態和進行中更新部署的狀態，以就可能需要關注的問題對操作員發出警示。
   
    相關建置組塊：[使用攣生屬性的方式][lnk-twin-properties]
5. **淘汰**︰在故障、升級循環或服務存留期結束後，更換裝置或予以解除委任。
   
    相關建置組塊：

## IoT 中樞裝置管理模式
IoT 中樞可實現下列這套 (初始) 裝置管理模式。如[教學課程][lnk-get-started]所示，您可以延伸這些模式以完全符合您的案例，並根據這些核心模式設計適用於其他案例的新模式。

1. **重新啟動** - 後端應用程式會透過 D2C 方法讓裝置知道已起始重新啟動。裝置會使用攣生裝置報告的屬性來更新裝置的重新啟動狀態。
   
    ![][img-reboot_pattern]
2. **恢復出廠預設值** - 後端應用程式會透過 D2C 方法讓裝置知道已起始恢復出廠預設值。裝置會使用攣生裝置報告的屬性來更新裝置的恢復出廠預設值狀態。
   
    ![][img-facreset_pattern]
3. **組態** - 後端應用程式使用攣生裝置所需的屬性來設定裝置上執行的軟體。裝置會使用攣生裝置報告的屬性來更新裝置的組態狀態。
   
    ![][img-config_pattern]
4. **韌體更新** - 後端應用程式會透過 D2C 方法讓裝置知道已起始韌體更新。裝置會起始多步驟程序，以下載韌體套件、套用韌體套件，並於最後重新連線到 IoT 中樞服務。透過多步驟程序，裝置會使用攣生裝置報告的屬性來更新裝置的進度和狀態。
   
    ![][img-fwupdate_pattern]
5. **報告進度和狀態** - 應用程式後端會橫跨一組裝置來執行攣生裝置查詢，以報告裝置上所執行動作的狀態和進度。
   
    ![][img-report_progress_pattern]

## 後續步驟
藉由使用 Azure IoT 中樞提供的建置組塊，開發人員可以建立 IoT 應用程式，來滿足 IoT 操作員在裝置生命週期內各個階段的獨特需求。

若要繼續了解 Azure IoT 中樞裝置管理功能，請參閱[開始使用 Azure IoT 中樞裝置管理][lnk-get-started]教學課程。

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md

<!---HONumber=AcomDC_1005_2016-->