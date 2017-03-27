---
title: "開始使用適用於 Azure Service Fabric 的 Eclipse 外掛程式 | Microsoft Docs"
description: "開始使用適用於 Azure Service Fabric 的 Eclipse 外掛程式。"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b612259b97bf238bac28cb77bf26f684128dd882
ms.lasthandoff: 03/21/2017


---

# <a name="getting-started-with-eclipse-plugin-for-service-fabric-java-application-development"></a>開始使用 Eclipse 外掛程式來開發 Service Fabric Java 應用程式
Eclipse 是 Java 開發人員最常使用的其中一種整合式開發環境 (IDE)。 在本文中，我們將討論如何設定 Eclipse 開發環境來使用 Service Fabric。 本文會協助您安裝外掛程式、建立 Service fabric 應用程式，以及將 Service Fabric 應用程式部署到本機或遠端 Service Fabric 叢集。

## <a name="install-or-update-service-fabric-plugin-on-eclipse-neon"></a>在 Eclipse Neon 安裝或更新 Service Fabric 外掛程式
Service Fabric 為**適用於 Java 開發人員的 Eclipse 整合式開發環境 (IDE)** 提供了外掛程式，可簡化 Java 服務的建置和部署程序。

1. 確定已安裝最新版 Eclipse **Neon** 和最新的 Buildship 版本 (1.0.17 或更新版本)。 您可以選擇 [說明] => [安裝詳細資料]，檢查已安裝的元件版本。 您可以使用[這裡][buildship-update]的指示更新 Buildship。 若要檢查 Eclipse Neon 是否為最新版本並加以更新，您可以移至 [說明] => [檢查更新]。

2. 若要安裝 Service Fabric 外掛程式，請選擇 [說明] => [安裝新軟體...]。
  1. 在 [使用] 文字方塊中，輸入︰``http://dl.windowsazure.com/eclipse/servicefabric``。
  2. 按一下 [新增]。

  ![Service Fabric 的 Eclipse Neon 外掛程式][sf-eclipse-plugin-install]

  3. 選擇 Service Fabric 外掛程式，然後按 [下一步]。
  4. 繼續進行安裝並接受使用者授權合約。

如果您已安裝 Service Fabric Eclipse 外掛程式，請確定您使用的是最新版本。 您可以遵循 [說明] => [安裝詳細資料]****，檢查它是否可以再更新。 然後，在已安裝的外掛程式清單中搜尋 Service Fabric，並按一下 [更新]。 如果有任何擱置中的更新，則會加以擷取並安裝。

> [!NOTE]
> 如果在 Eclipse 上安裝或更新 Service Fabric Eclipse 外掛程式時需要很久的時間，這是因為 Eclipse 每次都會嘗試擷取對 Eclipse 執行個體註冊之所有更新網站所發生的所有新變更的中繼資料。 因此，若要加快程序，您可以使用這個小秘訣 - 您可以移至 [可用軟體網站]，並取消核取指向 Service Fabric 外掛程式位置 `http://dl.windowsazure.com/eclipse/servicefabric` 之項目以外的所有項目。
>

## <a name="create-service-fabric-application-using-eclipse"></a>使用 Eclipse 建立 Service Fabric 應用程式

1. 移至 [檔案] => [新增] => [其他]。 選取 [Service Fabric 專案]。 按 [下一步] 。

    ![Service Fabric 新專案第 1 頁][create-application/p1]

2. 為專案命名。 按 [下一步] 。

    ![Service Fabric 新專案第 2 頁][create-application/p2]

3. 從範本 (執行者、無狀態、容器或來賓可執行檔) 的可用集合中選取服務範本。 按 [下一步] 。

    ![Service Fabric 新專案第 3 頁][create-application/p3]

4. 在此頁面上輸入服務名稱和/或相關的服務詳細資料，然後按一下 [完成]。

    ![Service Fabric 新專案第 4 頁][create-application/p4]

5. 當您建立第一個 Service Fabric 專案時，系統會詢問您是否要設定 Service Fabric 檢視方塊，請選取 [是] 以繼續。

    ![Service Fabric 新專案第 5 頁][create-application/p5]

6. 在成功建立後，專案外觀如下 -

    ![Service Fabric 新專案第 6 頁][create-application/p6]

## <a name="build-and-deploy-the-service-fabric-application-using-eclipse"></a>使用 Eclipse 建置和部署 Service Fabric 應用程式

* 以滑鼠右鍵按一下您剛才在上面建立的 Service Fabric 應用程式。 在內容功能表中選取選項 [Service Fabric]。 這會帶出有多個選項的子功能表。 看起來像這樣 -

    ![Service Fabric 快顯功能表][publish/RightClick]

  在用來建置、重建和清除的選項上按一下，便會執行預定的動作。
  - [建置應用程式] 會建置應用程式而不進行清除
  - [重新建置應用程式] 會對應用程式執行清除建置
  - [清除應用程式] 會清除所建置構件的應用程式


* 您也可以從這個功能表選擇部署、解除部署和發佈應用程式。
  - [部署應用程式] 會部署到您的本機叢集
  - [發佈應用程式...] 會詢問您要選取 ``Local.json`` 還是 ``Cloud.json`` 發佈設定檔。 這些 JSON 檔案會用來儲存連線到本機或雲端 (Azure) 叢集所需的資訊 (例如連線端點和安全性資訊)。

  ![Service Fabric 快顯功能表][publish/Publish]

* 另一種方式是使用 Eclipse 執行組態來部署 Service Fabric 應用程式。

  1. 選擇 [執行] => [執行組態]。 選取 [等級專案] 底下的 **ServiceFabricDeployer** 執行組態。
  2. 在右窗格的 [引數] 索引標籤底下，指定 [本機] 或 [雲端] 來做為 **publishProfile**。 預設設定是 [本機]。 若是部署至遠端/雲端叢集，請選取 [雲端]。
  3. 視情況編輯 `Local.json` 或 `Cloud.json`，以確保發佈設定檔中已填入適當的資訊，以及端點詳細資訊和安全性認證 (如果有的話)。
  4. 確定右窗格上位於 [等級專案] 底下的 [工作目錄] 指向您要部署的應用程式。 如果不是，請按一下 [工作區...] 按鈕，然後選取您要的應用程式。
  5. 依序按一下 [套用] 及 [執行]。

幾分鐘內即可建置和部署您的應用程式。 您可以從 Service Fabric Explorer 監視其狀態。  

## <a name="add-new-service-fabric-service-to-your-service-fabric-application"></a>將新的 Service Fabric 服務新增至 Service Fabric 應用程式

您可以使用下列步驟將新的 Service Fabric 服務新增至現有 Service Fabric 應用程式︰

1. 以滑鼠右鍵按一下要新增服務的專案，以開啟內容功能表，然後選取 [Service Fabric] 選項。 這會帶出有多個選項的子功能表。

    ![Service Fabric 新增服務第 1 頁][add-service/p1]

2. 選取 [新增 ServiceFabric 服務] 選項，讓它引導您完成下一組步驟，以將服務新增至專案。
3. 選取要新增至專案的服務範本，然後按 [下一步]。

    ![Service Fabric 新增服務第 2 頁][add-service/p2]

4. 輸入服務名稱 (和其他必要的詳細資料 (如有必要))，然後按下面的 [新增服務] 按鈕。  

    ![Service Fabric 新增服務第 3 頁][add-service/p3]

5. 成功新增服務後，整個專案結構現在看起來會像下圖 -

    ![Service Fabric 新增服務第 4 頁][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>升級 Service Fabric Java 應用程式

假設您已使用 Service Fabric Eclipse 外掛程式建立並部署 ``App1`` 專案，以建立名為 ``fabric:/App1Application``、應用程式類型為 ``App1AppicationType`` 且應用程式版本為 1.0 的應用程式。 現在您想要升級應用程式，但不要讓它停止運作。

變更您的應用程式，並重建已修改的服務。  以服務 (和程式碼或組態或資料，視情況而定) 已更新的版本，更新已修改之服務的資訊清單檔案 (``ServiceManifest.xml``)。 同時，也以應用程式已更新的版本號碼及已修改的服務，修改應用程式的資訊清單 (``ApplicationManifest.xml``)。  

若要使用 Eclipse 升級應用程式，您可以使用下列步驟建立重複的執行組態，並視需要用它升級應用程式 -
1. 選擇 [執行] => [執行組態]。 按一下左窗格中 [等級專案] 左邊的小箭號。
2. 以滑鼠右鍵按一下 **ServiceFabricDeployer**，然後選取 [重複]。 為此組態指定新名稱，例如 **ServiceFabricUpgrader**。
3. 在右窗格的 [引數] 索引標籤底下，將 **-Pconfig='deploy'** 變更為 **-Pconfig=upgrade**，然後按一下 [套用]。
4. 現在，您已建立並儲存用來升級應用程式的執行組態，您可以在想要時 [執行]。 這會處理從應用程式資訊清單檔中取得最新版更新應用程式類型版本的程序。

您現在可以使用 Service Fabric Explorer 來監視應用程式升級。 應用程式在幾分鐘內會完成更新。

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

