---
title: "在 Linux 上建立 Azure Service Fabric Reliable Actors Java 應用程式 | Microsoft Docs"
description: "了解如何在五分鐘內建立和部署 Java Service Fabric Reliable Actors 應用程式。"
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 254f38a600ea4026120bc411368eeb01310e56b2
ms.contentlocale: zh-tw
ms.lasthandoff: 07/15/2017

---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>在 Linux 上建立第一個 Java Service Fabric Reliable Actors 應用程式

本快速入門可協助您在短短幾分鐘內在 Linux 開發環境中建立第一個 Azure Service Fabric Java 應用程式。  當您完成時，您會有一個在本機開發叢集上執行的簡單 Java 單一服務應用程式。  

## <a name="prerequisites"></a>必要條件
開始之前，請在 [Linux 開發環境](service-fabric-get-started-linux.md)中安裝 Service Fabric SDK、Azure CLI，以及設定開發叢集。 如果您使用 Mac OS X，您可以[使用 Vagrant 在虛擬機器中設定 Linux 開發環境](service-fabric-get-started-mac.md)。

您也需要為部署應用程式設定 [Azure CLI 2.0](service-fabric-azure-cli-2-0.md) (建議) 或 [XPlat CLI](service-fabric-azure-cli.md)。

## <a name="create-the-application"></a>建立應用程式
Service Fabric 應用程式包含一或多個服務，而每個服務在提供應用程式的功能時都有特定角色。 適用於 Linux 的 Service Fabric SDK 包含 [Yeoman](http://yeoman.io/) 產生器，可讓您輕鬆建立第一個服務且稍後新增更多服務。  您也可以使用適用於 Eclipse 的外掛程式，建立、建置及部署 Service Fabric Java 應用程式。 請參閱[使用 Eclipse 建立和部署第一個 Java 應用程式](service-fabric-get-started-eclipse.md)。 在此快速入門中，使用 Yeoman 建立具有單一服務的應用程式，該服務可儲存和取得計數器值。

1. 在終端機中，輸入 ``yo azuresfjava``。
2. 為您的應用程式命名。 
3. 選擇第一個服務的類型並加以命名。 在本教學課程中，選擇 Reliable Actor 服務。 如需其他服務類型的詳細資訊，請參閱 [Service Fabric 程式設計模型概觀](service-fabric-choose-framework.md)。
   ![適用於 Java 的 Service Fabric Yeoman 產生器][sf-yeoman]

## <a name="build-the-application"></a>建置應用程式
Service Fabric Yeoman 範本包含 [Gradle](https://gradle.org/) 的建置指令碼，可用來從終端機建置應用程式。 若要建置和封裝應用程式，請執行下列指令碼：

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>部署應用程式
建置應用程式後，可以將它部署到本機叢集。

### <a name="using-xplat-cli"></a>使用 XPlat CLI

1. 連接到本機 Service Fabric 叢集。

    ```bash
    azure servicefabric cluster connect
    ```

2. 執行範本中所提供的安裝指令碼，將應用程式套件複製到叢集的映像存放區、註冊應用程式類型，以及建立應用程式的執行個體。

    ```bash
    ./install.sh
    ```

### <a name="using-azure-cli-20"></a>使用 Azure CLI 2.0

部署建置的應用程式與部署其他任何 Service Fabric 應用程式相同。 請參閱[使用 Azure CLI 管理 Service Fabric 應用程式](service-fabric-application-lifecycle-azure-cli-2-0.md)文件以取得詳細指示。

這些命令的參數可以在應用程式套件內產生的資訊清單中找到。

部署應用程式後，開啟瀏覽器並瀏覽至 [http://localhost:19080/Explorer](http://localhost:19080/Explorer) 的 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)。
接著展開 [應用程式] 節點，請注意，您的應用程式類型現在有一個項目，而另一個項目則在該類型的第一個執行個體。

## <a name="start-the-test-client-and-perform-a-failover"></a>啟動測試用戶端並執行容錯移轉
動作項目不會自行執行任何動作，它們需要其他服務或用戶端傳送訊息給它們。 動作項目範本包含簡單的測試指令碼，您可以用來與動作項目服務互動。

1. 使用監看式公用程式執行指令碼，以查看動作項目服務的輸出。  測試指令碼會在動作項目上呼叫 `setCountAsync()` 方法來遞增計數器，在動作項目上呼叫 `getCountAsync()` 方法以取得新的計數器值，並對主控台顯示該值。

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. 在 Service Fabric Explorer 中，找出裝載動作項目服務主要複本的節點。 在以下的螢幕擷取畫面中是節點 3。 主要服務複本會處理讀取和寫入作業。  服務狀態變更會接著複寫至次要複本 (在底下螢幕擷取畫面中的節點 0 和 1 上執行)。

    ![在 Service Fabric Explorer 中尋找主要複本][sfx-primary]

3. 在 [節點] 中，按一下您在上一個步驟中找到的節點，然後從 [動作] 功能表選取 [停用 (重新啟動)]。 此動作會重新啟動執行主要服務複本的節點，並強制容錯移轉至在另一個節點上執行的其中一個次要複本。  該次要複本會提升為主要，不同節點上會建立另一個次要複本，而且主要複本會開始採取讀取/寫入作業。 當節點重新啟動時，請留意測試用戶端的輸出，並注意儘管是容錯移轉，計數器仍會繼續增加。

## <a name="remove-the-application"></a>移除應用程式
使用範本中提供的解除安裝指令碼來刪除應用程式執行個體、取消註冊應用程式套件，並從叢集的映像存放區中移除此應用程式套件。

```bash
./uninstall.sh
```

在 Service Fabric 總管中，您可看到不再出現於 [應用程式] 節點中的應用程式和應用程式類型。

## <a name="next-steps"></a>後續步驟
* [使用 Eclipse 在 Linux 上建立第一個 Service Fabric Java 應用程式](service-fabric-get-started-eclipse.md)
* [深入了解 Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [使用 Azure CLI 與 Service Fabric 叢集互動](service-fabric-azure-cli.md)
* [針對部署進行疑難排解](service-fabric-azure-cli.md#troubleshooting)
* 了解 [Service Fabric 支援選項](service-fabric-support.md)

## <a name="related-articles"></a>相關文章

* [開始使用 Service Fabric 和 Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [開始使用 Service Fabric XPlat CLI](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

