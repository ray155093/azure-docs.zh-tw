---
title: "將 Azure Service Fabric 應用程式部署到合作對象叢集 | Microsoft Docs"
description: "了解如何將應用程式部署到合作對象叢集。"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/03/2017
ms.author: mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 1fde062d9673a48698b269d79e009c7b179e5934
ms.contentlocale: zh-tw
ms.lasthandoff: 07/15/2017

---

# <a name="deploy-an-application-to-a-party-cluster-in-azure"></a>將應用程式部署到 Azure 中的合作對象叢集
本教學課程示範如何部署 Azure Service Fabric 應用程式到 Azure 中的合作對象叢集。

在教學課程的這個部分中，您已了解如何：
> [!div class="checklist"]
> * 使用 Visual Studio 將應用程式部署至遠端叢集
> * 使用 Service Fabric Explorer 從叢集移除應用程式

本教學課程分成三篇文章，本文是全部三篇的第二篇。

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前：
- 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [安裝 Visual Studio 2017](https://www.visualstudio.com/) 並安裝 **Azure 開發**以及 **ASP.NET 和 Web 開發**工作負載。
- [安裝 Service Fabric SDK](service-fabric-get-started.md)

## <a name="set-up-a-party-cluster"></a>設定合作對象叢集
合作對象的叢集是免費的限時 Service Fabric 叢集，裝載於 Azure 上，並且由任何人都可以部署應用程式並了解平台的 Service Fabric 小組執行。 免費！

若要存取合作對象叢集，請瀏覽到此網站：http://aka.ms/tryservicefabric，並遵循指示來存取叢集。 您需要存取合作對象叢集，才能存取 Facebook 或 GitHub 帳戶。

> [!NOTE]
> 合作對象叢集不安全，因為其他人都會看見應用程式以及您在其中輸入的任何資料。 請勿部署不想讓其他人看見的任何項目。 務必閱讀我們的使用條款，以便瞭解所有的詳細資料。

## <a name="make-your-application-ready-for-deployment"></a>備妥應用程式進行部署
ASP.NET Core Web API 服務做為此應用程式的前端，並接受外部流量時，我們想要將該服務繫結至固定的已知連接埠。 在服務 **ServiceManifest.xml** 檔案中指定連接埠。

1. 在 [方案總管] 中，開啟 **WebAPIFrontEnd->PackageRoot->ServiceManifest.xml**。
2. 將現有**端點**元素的**連接埠**屬性變更為 **80**，並儲存變更。

## <a name="deploy-the-app-to-the-azure"></a>將應用程式部署至 Azure
應用程式備妥後，即可直接從 Visual Studio 將其部署到合作對象叢集。

1. 以滑鼠右鍵按一下 [方案總管] 中的 **MyApplication**，並選擇 [發佈]。

    ![[發佈] 對話方塊](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

2. 在 [連接端點] 欄位中輸入合作對象叢集的連接端點，並按一下 [發行]。

    發佈完成後，您應該能夠透過瀏覽器將要求傳送到應用程式。

3. 開啟您偏好的瀏覽器，並輸入叢集位址 (不含連接埠資訊的連線端點，例如，win1kw5649s.westus.cloudapp.azure.com)，然後將 `/api/values` 新增到 URL。

    您現在應該會看到與在本機執行應用程式相同的結果。

    ![叢集的 API 回應](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="remove-the-application-from-a-cluster-using-service-fabric-explorer"></a>使用 Service Fabric Explorer 從叢集移除應用程式
Service Fabric Explorer 是圖形化使用者介面，可用來瀏覽和管理 Service Fabric 叢集中的應用程式。

若要移除部署到合作對象叢集的應用程式：

1. 使用合作對象叢集註冊頁面提供的連結，瀏覽到 Service Fabric Explorer。 例如，http://win1kw5649s.westus.cloudapp.azure.com:19080/Explorer/index.html。

2. 在 Service Fabric Explorer 中，瀏覽到左側樹狀檢視中的 **fabric://MyApplication** 節點。

3. 按一下右側 [Essentials] 窗格中的 [動作] 按鈕，並選擇 [刪除應用程式]。 確認刪除應用程式執行個體，這將移除在叢集中執行的應用程式執行個體。

![刪除 Service Fabric Explorer 中的應用程式](./media/service-fabric-tutorial-deploy-app-to-party-cluster/delete-application.png)

應用程式在 Service Fabric 叢集中部署為應用程式類型，以便您對於叢集中執行的應用程式擁有多個執行個體和版本。 移除執行中的應用程式執行個體後，也可以移除類型，以便完成部署的清除作業。

如需 Service Fabric 應用程式模型的詳細資訊，請參閱[在 Service Fabric 中模型化應用程式](service-fabric-application-model.md)。

1. 瀏覽到樹狀檢視中的 **MyApplicationType** 節點。

2. 按一下右側 [Essentials] 窗格中的 [動作] 按鈕，並選擇 [解除佈建類型]。 確認解除佈建應用程式類型。

![在 Service Fabric Explorer 中解除佈建應用程式類型](./media/service-fabric-tutorial-deploy-app-to-party-cluster/unprovision-type.png)

本教學課程到此結束。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 Visual Studio 將應用程式部署至遠端叢集
> * 使用 Service Fabric Explorer 從叢集移除應用程式

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [使用 Visual Studio Team Services 設定持續整合](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
