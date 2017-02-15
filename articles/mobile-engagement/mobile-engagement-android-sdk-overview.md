---
title: "Azure Mobile Engagement 的 Android SDK 整合"
description: "描述如何整合 Azure Mobile Engagement SDK 至 Android 應用程式"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2b89a13230439698854656f0bb5367f1ed35ce1c


---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Azure Mobile Engagement 的 Android SDK 整合
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

此文件說明適用於 Azure Mobile Engagement Android SDK 的所有整合及組態選項。

## <a name="prerequisites"></a>必要條件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>進階功能
### <a name="reporting-features"></a>報告功能
您可以新增這些功能：

1. [進階報告選項](mobile-engagement-android-advanced-reporting.md)
2. [位置報告選項](mobile-engagement-android-location-reporting.md)
3. [進階組態選項](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>通知:
[如何將 Reach (通知) 整合在 Android 應用程式中](mobile-engagement-android-integrate-engagement-reach.md)

1. Google 雲端通訊 (GCM)： [如何整合 GCM 與 Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. Amazon 裝置傳訊 (ADM)： [如何整合 ADM 與 Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>標記計畫實作：
[如何在 Android 應用程式中使用進階 Mobile Engagement 標記 API](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>版本資訊
### <a name="423-08102016"></a>4.2.3 (08/10/2016)
* 不會再鎖定 WIFI。
* 修正在 init 之前呼叫 getDeviceId 時的死結問題 (4.2.0 中引進的錯誤)。

如需所有版本，請參閱 [完整版本資訊](mobile-engagement-android-release-notes.md)。

## <a name="upgrade-procedures"></a>升級程序
如果您已經將較舊版的 SDK 整合到應用程式中，請參閱 [升級程序](mobile-engagement-android-upgrade-procedure.md)。




<!--HONumber=Nov16_HO3-->


