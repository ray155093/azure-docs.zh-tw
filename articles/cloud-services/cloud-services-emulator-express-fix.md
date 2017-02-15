---
title: "安裝 Emulator Express 在 Visual Studio 中偵錯雲端服務應用程式 | Microsoft Docs"
description: "說明如何安裝 C++ 可轉散發套件在 Visual Studio 中啟用 Emulator Express"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 22b20f7a-23f4-4f7f-b536-3bf1e01adcd1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 05d672dcb1335c617bb8d8cae43947bcd5e9ab3d

---
# <a name="use-emulator-express-to-debug-cloud-services-application-in-vs-2017"></a>在 VS 2017 中使用 Emulator Express 偵錯雲端服務應用程式
這篇文章說明如何在 VS 2017 中使用 Emulator Express 偵錯雲端服務應用程式。

## <a name="background-context"></a>背景資訊
Visual Studio 中依預設會使用 Emulator Express 來偵錯雲端服務 Web 和背景工作角色。 此設定是在雲端服務專案屬性頁中指定。

![開啟專案屬性][0]

![預設已選取 Emulator Express][1]

Emulator Express 需要有適用於 Visual Studio 的 [sual C++ 可轉散發套件][sual C++ 可轉散發套件]。 目前未隨著 Azure 工作負載一起安裝。 按下 F5 來偵錯雲端服務應用程式時，Visual Studio 會提示安裝此元件，然後繼續偵錯。

![提示安裝 C++ 可轉散發套件][2]

按一下 [是] 以安裝 C++ 可轉散發套件。

![安裝 C++ 可轉散發套件][3]

再按一次 F5 以啟動偵錯工作階段。

![開始偵錯][4]

![偵錯成功][5]

> 注意︰Visual C++ 可轉散發套件只需要安裝一次。 如果您是從舊版的 Azure SDK 升級，且已安裝 Emulator Express，則不會遇到此問題。
> 
> 

## <a name="manual-workaround"></a>手動因應措施
您也可以手動安裝 [sual C++ 可轉散發套件][sual C++ 可轉散發套件]，效果同於 Visual Studio 將它安裝在您的系統上。

[vcredist_x86.exe][vcredist_x86.exe]

[vcredist_x64.exe][vcredist_x64.exe]

## <a name="next-steps"></a>後續步驟
深入了解在 Visual Studio 中使用 Azure 計算模擬器來偵錯雲端服務應用程式︰[使用 Emulator Express 在本機電腦上執行及偵錯雲端服務][使用 Emulator Express 在本機電腦上執行及偵錯雲端服務]

[sual C++ 可轉散發套件]:https://www.microsoft.com/en-us/download/details.aspx?id=30679
[vcredist_x86.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x86.exe
[vcredist_x64.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x64.exe
[使用 Emulator Express 在本機電腦上執行及偵錯雲端服務]:https://azure.microsoft.com/en-us/documentation/articles/vs-azure-tools-emulator-express-debug-run/

[0]: ./media/cloud-services-emulator-express-fix/vs-05.png
[1]: ./media/cloud-services-emulator-express-fix/vs-06.png
[2]: ./media/cloud-services-emulator-express-fix/vs-01.png
[3]: ./media/cloud-services-emulator-express-fix/vs-02.png
[4]: ./media/cloud-services-emulator-express-fix/vs-03.png
[5]: ./media/cloud-services-emulator-express-fix/vs-04.png



<!--HONumber=Nov16_HO3-->


