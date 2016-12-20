---
title: "MyDriving Azure IoT 範例：快速入門 | Microsoft Docs"
description: "應用程式快速入門，以完整示範如何使用 Microsoft Azure 建立 IoT 系統的架構，包括串流分析、機器學習服務和事件中樞。"
services: 
documentationcenter: .net
suite: 
author: harikmenon
manager: douge
ms.assetid: f40ea71b-5721-4a6b-a886-53c2e9dffe8f
ms.service: multiple
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2016
ms.author: harikm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c6366d42b91748963ec14c258f92746e5d2fde92


---
# <a name="mydriving-iot-system-quick-start"></a>MyDriving IoT 系統：快速入門
MyDriving 是示範典型 [物聯網](iot-suite-overview.md) (IoT) 解決方案設計與實作的系統，可收集來自裝置的遙測、在雲端處理該資料，以及套用機器學習來提供調適型回應。 該示範會記錄您的行車車程相關資料，方法是使用您的行動電話資料和配接器來收集您汽車控制系統的資訊。 它會使用此資料來提供您與其他使用者之駕駛習慣比較的相關回饋。

MyDriving 的真正用途是讓您開始建立您自己的 IoT 解決方案。 但在那之前，讓我們先以我們的測試使用者團隊成員身分，引導您了解 MyDriving 應用程式本身。 這可以先讓您體驗應用程式和應用程式背後的系統，然後再研究架構。 這也會向您介紹 HockeyApp，這是管理向測試使用者發送您 Alpha 和 Beta 版應用程式的酷炫方式。

## <a name="use-the-mobile-experience"></a>使用行動體驗
如果您有 Android、iOS 或 Windows 10 裝置，您可以使用 MyDriving 應用程式。

### <a name="android-and-windows-10-mobile-installation"></a>Android 和 Windows 10 Mobile 安裝
在您的裝置上：

1. 允許部署應用程式：
   
   * Android：在 [設定] > [安全性] 中，允許來自 [未知來源] 的應用程式。
   * Windows 10：在 [設定] > [更新] > [適用於開發人員] 中，設定 [開發人員模式]。
2. 使用 [HockeyApp](https://rink.hockeyapp.net)登入，或登入其中，以加入我們的 beta 版測試小組。 HockeyApp 可讓您輕鬆地將您應用程式的早期版本散發給測試使用者。
   
   如果您是使用 Windows 10，請使用 Edge 瀏覽器。
   
   如果您是 Build 2016 出席者，請使用其中一個 Microsoft 按鈕，並使用您為會議註冊的相同 Microsoft 帳戶電子郵件登入。 您已經向 HockeyApp 註冊。
   
   ![HockeyApp 登入畫面](./media/iot-solution-get-started/image1.png)
3. 從此處下載並安裝應用程式：
   
   * [Android](http://rink.io/spMyDrivingAndroid)
   * [Windows 10](http://rink.io/spMyDrivingUWP)
   
   有兩個項目。 安裝 **信任的人員**中的憑證。 然後安裝應用程式。

*在 Windows 10 Mobile 上啟動應用程式是否有任何問題？* 可能是因為您的手機是上一次或上上一次更新的版本。 請確定您已經取得最新更新，或安裝：

* [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 
* [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx) 
* [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx)

### <a name="ios-installation"></a>iOS 安裝
如果您參與 Build 2016，請以我們 HockeyApp 測試團隊成員的身分下載應用程式：

1. 在您的 iOS 裝置上，登入 [HockeyApp](https://rink.hockeyapp.net)。
   使用其中一個 Microsoft 登入按鈕，並使用您為會議註冊的相同 Microsoft 帳戶電子郵件登入。 (請勿使用電子郵件和密碼欄位。)
   
   ![HockeyApp 登入畫面](./media/iot-solution-get-started/image1.png)
2. 在 HockeyApp 儀表板中，選取並下載 MyDriving。
3. 從 HockeyApp 授權 Beta 版：
   
   a. 移至 [設定] > 一般] > [設定檔和裝置管理]。
   
   b.這是另一個 C# 主控台應用程式。 信任 [Bit Stadium GmbH] 憑證。

若未參與  Build 2016，您可以自行建置及部署應用程式：

1. [從 GitHub]下載程式碼。
2. [使用 Xamarin]建置及部署。

在 [MyDriving Reference Guide (MyDriving 參考指南)](http://aka.ms/mydrivingdocs)中尋找更多詳細資料。

## <a name="get-an-obd-adapter-optional"></a>取得 OBD 配接器 (選擇性)
這是讓這個系統成為真正物聯網系統的組件！ 您可以在沒有配接器的情況下使用應用程式，但搭配實物使用會更有趣，況且它們的價格也不貴。

車載診斷系統 (OBD) 是您汽車的一項功能，車行可以用它來調整您的汽車及診斷奇怪的雜音和警告燈訊號。 除非您的汽車是年代久遠的古董車，否則您都可以隔間的某處找到一個插槽，通常在儀表板底下的襟翼後。 透過正確的連接器，您就可以取得引擎效能的計量並進行特定調整。 一般的車用品店就可以買到很便宜的 OBD 連接器。 它是使用藍牙或 WiFi 連線至您手機上的應用程式。

在這個案例中，我們將把您的車連線至雲端。 直接連線方式是從 OBD 連線至您的手機；但我們的應用程式是以轉送器的方式運作。 您汽車的遙測會直接傳送至 MyDriving IoT 中樞，並在中樞中處理遙測以記錄您的行車路線並評估您的駕駛習慣。

要連線 OBD 裝置：

1. 檢查您的車子是否有 OBD 插座。
2. 取得 OBD 配接器：
   
   * 若是使用 Android 或 Windows 手機，您需要支援藍牙的 OBD II 配接器。 我們使用 [BAFX Products 34t5 Bluetooth OBDII Scan Tool (BAFX 產品 34t5 藍牙 OBDII 掃描工具)]。
   * 若是使用 iOS 手機，您需要支援 WiFi 的 OBD 配接器。 我們使用 [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner (ScanTool OBDLink MX Wi-Fi: OBD 配接器/診斷掃描器)]。
3. 請依照 OBD 配接器隨附的指示將它連線至您的手機。 請記住下列要點：
   
   * 藍牙配接器必須在 [設定]  頁面中和手機配對。
   * Wi-Fi 配接器必須擁有在 192.168.xxx.xxx 範圍內的位址。
4. 如果您擁有數部車，您可以為每部車取得個別的配接器 (最多三個)。

如果您沒有 OBD 配接器，應用程式仍會從手機的 GPS 接收器傳送位置和速度資料給後端，並且將會詢問您是否要模擬 OBD。

您可以在 [MyDriving Reference Guide (MyDriving 參考指南)](http://aka.ms/mydrivingdocs)的 2.1 節＜IoT Devices (IoT 裝置)＞中找到更多關於應用程式如何使用來自 OBD 配接器，以及關於用於建立您自己之 OBD 裝置的選項的資訊。

## <a name="use-the-app"></a>使用應用程式
啟動應用程式。 有一個初級的快速入門可引導您逐步了解其運作方式。

### <a name="track-your-trips"></a>追蹤您的行程
點選記錄按鈕 (位於螢幕底部的紅色大圓圈) 來開始行程，再點選一次即可結束。

![適用於車程追蹤的記錄按鈕圖例](./media/iot-solution-get-started/image2.png)

您每次開始行程時，如果沒有 OBD 裝置，就會詢問您是否要使用模擬器。

在行程結束時，點選停止按鈕，您就可以取得摘要。

![車程摘要的範例](./media/iot-solution-get-started/image3.png)

### <a name="review-your-trips"></a>檢閱您的行程
![上一趟車程的範例](./media/iot-solution-get-started/image4.png)

### <a name="review-your-profile"></a>檢閱您的設定檔
![駕駛風格設定檔的範例](./media/iot-solution-get-started/image5.png)

## <a name="send-us-your-test-feedback"></a>將您的測試意見反應傳給我們
由於我們建立 MyDriving 的目的，是為了協助您快速開始建立自己的 IoT 系統，因此當然想聽到您對其運作方式的意見反應！ 若發生下列情況，請告訴我們：

* 您遇到問題或挑戰。
* 沒有擴充點可更適合您的案例。
* 您發現更有效率的方式來完成特定需求。
* 您有改善 MyDriving 或這份文件的任何其他建議。

在 MyDriving 應用程式內部，您可以使用內建的 HockeyApp 意見反應機制：在 iOS 和 Android 上只要搖晃一下您的手機，或使用 [意見反應]  功能表命令。 這將會自動附加螢幕擷取畫面，這樣我們就能知道您在說什麼。 而且如果不幸地發生當機，HockeyApp 會收集當機記錄以告知我們發生當機。 您也可以透過 [HockeyApp portal (HockeyApp 入口網站)]提供意見反應。

您也可以[在 GitHub 上提出問題]，或在底下留下意見 (英文版)。

我們期待聽到您的意見反應！

## <a name="next-steps"></a>後續步驟
* 探索 [MyDriving Reference Guide (MyDriving 參考指南)](http://aka.ms/mydrivingdocs) 以了解我們如何設計及建置整個 MyDriving 系統。
* [Create and deploy a system of your own (建立及部署您自己的系統) (建立及部署您自己的系統)](iot-solution-build-system.md) 。 [MyDriving Reference Guide (MyDriving 參考指南)](http://aka.ms/mydrivingdocs) 也會引導您了解您將進行大部分自訂作業的區域。

[從 GitHub]: https://github.com/Azure-Samples/MyDriving
[使用 Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
[BAFX Products 34t5 Bluetooth OBDII Scan Tool (BAFX 產品 34t5 藍牙 OBDII 掃描工具)]: http://www.amazon.com/gp/product/B005NLQAHS
[ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner (ScanTool OBDLink MX Wi-Fi: OBD 配接器/診斷掃描器)]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
[HockeyApp portal (HockeyApp 入口網站)]: https://rink.hockeyapp.org
[在 GitHub 上提出問題]: https://github.com/Azure-Samples/MyDriving/issues



<!--HONumber=Nov16_HO3-->


