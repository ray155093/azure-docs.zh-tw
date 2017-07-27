---
title: "使用 Azure CLI 2.0 來管理 Service Fabric 應用程式"
description: "描述使用 Azure CLI 2.0 在 Service Fabric 叢集部署或移除應用程式的程序"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a99819f6a1c0ef31e14c95b6bd47138feb05053f
ms.contentlocale: zh-tw
ms.lasthandoff: 07/01/2017

---
# <a name="manage-service-fabric-application-using-azure-cli-20"></a>使用 Azure CLI 2.0 來管理 Service Fabric 應用程式

請依照本文件來建立和刪除在 Service Fabric 叢集中執行的應用程式。

## <a name="prerequisites"></a>必要條件

請務必安裝 Azure CLI 2.0 並選取您的 Service Fabric 叢集。 如需詳細資訊，請參閱[開始使用 Azure CLI 2.0 文件](service-fabric-azure-cli-2-0.md)。

您還應該備妥 Service Fabric 應用程式套件以供部署。 如需有關如何撰寫及封裝應用程式的詳細資訊，請參閱[應用程式模型文件](service-fabric-application-model.md)。

## <a name="overview"></a>概觀

部署新應用程式是由四個步驟所組成：

1. 將應用程式套件上傳到 Service Fabric 映像存放區
1. 佈建應用程式類型
1. 指定和建立應用程式
1. 指定和建立服務

移除現有的應用程式需要三個步驟：

1. 刪除應用程式
1. 將已關聯的應用程式類型解除佈建
1. 刪除映像存放區內容

## <a name="deploy-a-new-application"></a>部署新應用程式

若要部署新應用程式，請依照下列步驟操作

### <a name="upload-a-new-application-package-to-the-image-store"></a>將新應用程式套件上傳到映像存放區

建立應用程式之前，必須先將應用程式套件上傳到 Service Fabric 映像存放區。
假設之後您的應用程式套件存在於 `app_package_dir` 目錄中。 請使用下列命令來上傳該目錄：

```azurecli
az sf application upload --path ~/app_package_dir
```

針對大型應用程式套件，您可以指定 `--show-progress` 選項以顯示上傳進度。

### <a name="provision-application-type"></a>佈建應用程式類型

上傳完成之後，必須佈建應用程式。 若要佈建應用程式，請使用下列命令

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

`application-type-build-path` 與包含先前上傳之應用程式套件的目錄名稱相同

### <a name="create-application-from-application-type"></a>從應用程式類型建立應用程式

佈建應用程式之後，您可以使用下列命令來為應用程式命名並建立應用程式：

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

這裡的 `app-name` 是您要賦予應用程式執行個體的名稱。 其他參數則可以從先前佈建的應用程式資訊清單中找到。

應用程式名稱應該以 `fabric:/` 前置詞作為開頭。

### <a name="create-services-for-the-new-application"></a>為新的應用程式建立服務

建立應用程式之後，您可以從該應用程式建立服務。 針對此範例，我們會從應用程式建立新的無狀態服務。 先前佈建之應用程式套件內的服務資訊清單中會定義您可以從應用程式建立的服務。

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-creation-and-health"></a>確認應用程式的建立和健康情況

若要確認應用程式和服務是否部署成功，您可以使用下列命令來檢查是否有列出該應用程式和服務：

```azurecli
az sf application list
az sf service list --application-list TestApp
```

若要確認服務是否狀況良好，請使用類似的命令來擷取服務和應用程式的健康情況

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

狀況良好的服務和應用程式的 `HealthState` 值應該是 `Ok`。

## <a name="remove-an-existing-application"></a>移除現有的應用程式

若要移除應用程式，請依照下列步驟操作

### <a name="delete-the-application"></a>刪除應用程式

執行下列命令來刪除應用程式

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>將應用程式類型解除佈建

刪除應用程式之後，如果已不再需要其應用程式類型，可以將應用程式類型解除佈建。 使用下列命令將應用程式類型解除佈建

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

這裡的類型名稱和類型版本應該與先前佈建之應用程式資訊清單中的名稱和版本相符

### <a name="delete-application-package"></a>刪除應用程式套件

將應用程式類型解除佈建之後，如果已不再需要其應用程式套件，可以將應用程式套件從映像存放區中刪除。 刪除應用程式套件有助於回收磁碟空間。 使用下列命令將應用程式套件從映像存放區中刪除：

```azurecli
az sf application package-delete --content-path app_package_dir
```

這裡的 `content-path` 應該與建立應用程式時一開始上傳的目錄名稱相同

## <a name="related-articles"></a>相關文章

* [開始使用 Service Fabric 和 Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [開始使用 Service Fabric XPlat CLI](service-fabric-azure-cli.md)

