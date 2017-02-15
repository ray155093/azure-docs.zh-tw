---
title: "使用 Visual Studio Online 連續傳遞雲端服務 | Microsoft Docs"
description: "了解如何設定連續傳遞 Azure 雲端應用程式，而不需將診斷儲存體金鑰儲存至服務組態檔"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 148b2959-c5db-4e4a-a7e9-fccb252e7e8a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 7e70f92d4d1333ca6cbac5876e5ccbc763bd915c

---
# <a name="securely-save-cloud-services-diagnostics-storage-key-and-setup-continuous-integration-and-deployment-to-azure-using-visual-studio-online"></a>使用 Visual Studio Online 安全地儲存雲端服務診斷儲存體金鑰及設定連續整合和部署至 Azure
 這是現今開放原始碼專案的常見作法。 在組態檔中儲存應用程式密碼不再是安全的作法，因為從公用原始檔控制外洩的密碼會暴露安全性漏洞。 在連續整合管線的檔案中將密碼儲存為純文字並不安全，因為組建伺服器可能是雲端環境上的共用資源。 本文說明如何 Visual Studio 和 Visual Studio Online 降低開發和連續整合程序期間的安全性考量。

## <a name="remove-diagnostics-storage-key-secret-in-project-configuration-file"></a>移除專案組態檔中的診斷儲存體金鑰密碼
雲端服務診斷擴充功能需要 Azure 儲存體來儲存診斷結果。 儲存體連接字串先前指定於雲端服務組態 (.cscfg) 檔中，無法簽入原始檔控制。 在最新的 Azure SDK 版本中，我們將行為變更為只以權杖所取代的金鑰來儲存部分連接字串。 下列步驟說明新雲端服務工具的運作方式︰

### <a name="1-open-the-role-designer"></a>1.開啟角色設計工具
* 在雲端服務角色上連按兩下或按一下滑鼠右鍵，以開啟角色設計工具

![開啟角色設計工具][0]

### <a name="2-under-diagnostics-section-a-new-check-box-dont-remove-storage-key-secret-from-project-is-added"></a>2.[診斷] 區段下會新增 [不要從專案移除儲存體金鑰密碼] 核取方塊
* 如果您使用本機儲存體模擬器，此核取方塊會停用，因為本機連接字串 (UseDevelopmentStorage = true) 沒有要管理的密碼。

![本機儲存體模擬器連接字串並不是密碼][1]

* 如果您要建立新專案，依預設不會選取此核取方塊。 這會導致所選儲存體連接字串的儲存體金鑰部分被權杖取代。 在目前使用者的 AppData Roaming 資料夾下可找到權杖的值，例如︰C:\Users\contosouser\AppData\Roaming\Microsoft\CloudService

> 請注意，user\AppData 資料夾是依使用者登入控制存取權，被視為儲存開發機密的安全位置。
> 
> 

![儲存體金鑰會儲存在使用者設定檔資料夾下][2]

### <a name="3-select-a-diagnostics-storage-account"></a>3.選取診斷儲存體帳戶
* 按一下 [...] 按鈕啟動的對話方塊，選取儲存體帳戶 帳戶。 請注意，產生的儲存體連接字串不會有儲存體帳戶金鑰。
* 例如︰DefaultEndpointsProtocol=https;AccountName=contosostorage;AccountKey=$(*clouddiagstrg.key*)

### <a name="4----debugging-the-project"></a>4.  進行專案偵錯
* F5 可在 Visual Studio 中進行偵錯。 所有項目的運作方式都應該如同以往。
  ![在本機開始偵錯][3]

### <a name="5-publish-project-from-visual-studio"></a>5.從 Visual Studio 發佈專案
* 啟動 [發佈] 對話方塊並繼續執行登入指示，以將應用程式發佈至 Azure。

### <a name="6-additional-information"></a>6.其他資訊
> 注意︰角色設計工具中的 [設定] 面板會保持原狀。 如果您想要使用密碼管理功能進行診斷，請移至 [組態] 索引標籤。
> 
> 

![新增設定][4]

> 附註︰若已啟用，Application Insights 金鑰會儲存為純文字。 此金鑰只用於上傳內容，所以沒有任何敏感性資料會有遭到入侵的風險。
> 
> 

## <a name="build-and-publish-a-cloud-services-project-using-visual-studio-online-task-templates"></a>使用 Visual Studio Online 工作範本來建置及發佈雲端服務專案
* 下列步驟說明如何使用 Visual Studio Online 工作來設定雲端服務專案的持續整合︰
  ### <a name="1----obtain-a-vso-account"></a>1.  取得 VSO 帳戶
* [建立 Visual Studio Online 帳戶][建立 Visual Studio Online 帳戶] (如果您還沒有的話)
* [建立 Team 專案][建立 Team 專案] (在 Visual Studio Online 帳戶中)

### <a name="2----setup-source-control-in-visual-studio"></a>2.  在 Visual Studio 中設定原始檔控制
* 連接到 Team 專案

![連接到 Team 專案][5]

![選取要連接的 Team 專案][6]

* 將專案新增至原始檔控制

![將專案新增至原始檔控制][7]

![將專案對應至原始檔控制資料夾][8]

* 從 Team Explorer 簽入您的專案

![將專案簽入原始檔控制][9]

### <a name="3----configure-build-process"></a>3.  設定建置流程
* 瀏覽至您的 Team 專案並新增建置流程範本

![新增組建][10]

* 選取建置工作

![新增建置工作][11]

![選取 Visual Studio 建置工作範本][12]

* 編輯建置工作輸入。 請根據您的需求自訂組建參數

![設定建置工作][13]

`/t:Publish /p:TargetProfile=$(targetProfile) /p:DebugType=None /p:SkipInvalidConfigurations=true /p:OutputPath=bin\ /p:PublishDir="$(build.artifactstagingdirectory)\\"`

* 設定組建變數

![設定組建變數][14]

* 新增工作以上傳組建置放

![選擇發佈組建置放工作][15]

![設定發佈組建置放工作][16]

* 執行組建

![將新組建排入佇列][17]

![檢視組建摘要][18]

* 如果建置成功，您會看到類似以下的結果

![建置結果][19]

### <a name="4----configure-release-process"></a>4.  設定發行程序
* 建立新版本

![建立新版本][20]

* 選取 Azure 雲端服務部署工作

![選取 Azure 雲端服務部署工作][21]

* 因為儲存體帳戶金鑰並未簽入原始檔控制，所以我們需要指定秘密金鑰以便設定診斷擴充功能。 展開 [建立新服務的進階選項] 區段，然後編輯 [診斷儲存體帳戶金鑰] 參數輸入。 此輸入接受多行的金鑰值組，其格式為 **[RoleName]:$(StorageAccountKey)**

> 注意︰如果您的診斷儲存體帳戶是在您要發佈雲端服務應用程式的相同訂用帳戶之下，您不必在部署工作中輸入金鑰；部署將以程式設計方式從您的訂用帳戶取得儲存體資訊
> 
> 

![設定雲端服務部署工作][22]

* 使用密碼建置變數來儲存儲存體金鑰。 若要變數遮掩為密碼，按一下變數輸入右邊的鎖定圖示

![在密碼建置變數中儲存儲存體金鑰][23]

* 建立版本並將專案部署至 Azure

![建立新版本][24]

## <a name="next-steps"></a>後續步驟
若要深入了解如何為 Azure 雲端服務設定診斷擴充功能，請參閱[使用 PowerShell 在 Azure 雲端服務中啟用診斷][使用 PowerShell 在 Azure 雲端服務中啟用診斷]

[建立 Visual Studio Online 帳戶]:https://www.visualstudio.com/team-services/
[建立 Team 專案]: https://www.visualstudio.com/it-it/docs/setup-admin/team-services/connect-to-visual-studio-team-services
[使用 PowerShell 在 Azure 雲端服務中啟用診斷]:https://azure.microsoft.com/en-us/documentation/articles/cloud-services-diagnostics-powershell/

[0]: ./media/cloud-services-vs-ci/vs-01.png
[1]: ./media/cloud-services-vs-ci/vs-02.png
[2]: ./media/cloud-services-vs-ci/file-01.png
[3]: ./media/cloud-services-vs-ci/vs-03.png
[4]: ./media/cloud-services-vs-ci/vs-04.png
[5]: ./media/cloud-services-vs-ci/vs-05.png
[6]: ./media/cloud-services-vs-ci/vs-06.png
[7]: ./media/cloud-services-vs-ci/vs-07.png
[8]: ./media/cloud-services-vs-ci/vs-08.png
[9]: ./media/cloud-services-vs-ci/vs-09.png
[10]: ./media/cloud-services-vs-ci/vso-01.png
[11]: ./media/cloud-services-vs-ci/vso-02.png
[12]: ./media/cloud-services-vs-ci/vso-03.png
[13]: ./media/cloud-services-vs-ci/vso-04.png
[14]: ./media/cloud-services-vs-ci/vso-05.png
[15]: ./media/cloud-services-vs-ci/vso-06.png
[16]: ./media/cloud-services-vs-ci/vso-07.png
[17]: ./media/cloud-services-vs-ci/vso-08.png
[18]: ./media/cloud-services-vs-ci/vso-09.png
[19]: ./media/cloud-services-vs-ci/vso-10.png
[20]: ./media/cloud-services-vs-ci/vso-11.png
[21]: ./media/cloud-services-vs-ci/vso-12.png
[22]: ./media/cloud-services-vs-ci/vso-13.png
[23]: ./media/cloud-services-vs-ci/vso-14.png
[24]: ./media/cloud-services-vs-ci/vso-15.png



<!--HONumber=Nov16_HO3-->


