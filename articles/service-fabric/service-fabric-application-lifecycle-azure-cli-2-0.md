---
title: "使用 Azure CLI 2.0 來管理 Azure Service Fabric 應用程式"
description: "了解如何使用 Azure CLI 2.0 在 Azure Service Fabric 叢集中部署和移除應用程式。"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 5728339236e3819b301e428f9d7a8add08f02b3e
ms.contentlocale: zh-tw
ms.lasthandoff: 07/14/2017

---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-cli-20"></a>使用 Azure CLI 2.0 來管理 Azure Service Fabric 應用程式

了解如何建立和刪除在 Azure Service Fabric 叢集中執行的應用程式。

## <a name="prerequisites"></a>必要條件

* 安裝 Azure CLI 2.0。 然後選取 Service Fabric 叢集。 如需詳細資訊，請參閱[開始使用 Azure CLI 2.0](service-fabric-azure-cli-2-0.md)。

* 備妥 Service Fabric 應用程式封裝以供部署。 如需有關如何撰寫及封裝應用程式的詳細資訊，請參閱 [Service Fabric 應用程式模型](service-fabric-application-model.md)。

## <a name="overview"></a>概觀

若要部署新應用程式，請完成下列步驟：

1. 將應用程式封裝上傳到 Service Fabric 映像存放區。
2. 佈建應用程式類型。
3. 指定和建立應用程式。
4. 指定和建立服務。

若要移除現有的應用程式，請完成下列步驟：

1. 刪除應用程式。
2. 將已關聯的應用程式類型解除佈建。
3. 刪除映像存放區內容。

## <a name="deploy-a-new-application"></a>部署新應用程式

若要部署新應用程式，請完成下列工作：

### <a name="upload-a-new-application-package-to-the-image-store"></a>將新應用程式套件上傳到映像存放區

建立應用程式之前，將應用程式封裝上傳到 Service Fabric 映像存放區。 

例如，如果您的應用程式封裝在 `app_package_dir` 目錄中，使用下列命令上傳目錄：

```azurecli
az sf application upload --path ~/app_package_dir
```

針對大型應用程式套件，您可以指定 `--show-progress` 選項以顯示上傳進度。

### <a name="provision-the-application-type"></a>佈建應用程式類型

上傳完成時，佈建應用程式。 若要佈建應用程式，請使用下列命令：

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

值 `application-type-build-path` 是您上傳應用程式封裝的目錄名稱。

### <a name="create-an-application-from-an-application-type"></a>從應用程式類型建立應用程式

佈建應用程式之後，使用下列命令為應用程式命名並建立應用程式：

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` 是您想要用於應用程式執行個體的名稱。 您可以從先前佈建的應用程式資訊清單取得額外的參數。

應用程式名稱必須以 `fabric:/` 前置詞作為開頭。

### <a name="create-services-for-the-new-application"></a>為新的應用程式建立服務

建立應用程式之後，從該應用程式建立服務。 在下列範例中，我們會從應用程式建立新的無狀態服務。 先前佈建的應用程式封裝之中的服務資訊清單中會定義您可以從應用程式建立的服務。

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>確認應用程式部署和健康情況

若要確認應用程式和服務是否部署成功，請檢查是否有列出該應用程式和服務：

```azurecli
az sf application list
az sf service list --application-list TestApp
```

若要確認服務是否狀況良好，請使用類似的命令來取出服務和應用程式的健康情況：

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

狀況良好的服務和應用程式的 `Ok` 值是 `HealthState`。

## <a name="remove-an-existing-application"></a>移除現有的應用程式

若要移除應用程式，請完成下列工作：

### <a name="delete-the-application"></a>刪除應用程式

若要刪除應用程式，請使用下列命令：

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>將應用程式類型解除佈建

刪除應用程式之後，如果不再需要應用程式，可以解除佈建應用程式類型。 若要解除佈建應用程式類型，請使用下列命令：

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

類型名稱和類型版本必須與先前佈建的應用程式資訊清單中的名稱和版本相符。

### <a name="delete-the-application-package"></a>刪除應用程式封裝

解除佈建應用程式類型之後，如果不再需要應用程式封裝，可以從映像存放區中刪除應用程式封裝。 刪除應用程式套件有助於回收磁碟空間。 

若要從映像存放區中刪除應用程式封裝，請使用下列命令：

```azurecli
az sf application package-delete --content-path app_package_dir
```

`content-path` 必須是您建立應用程式時上傳的目錄名稱。

## <a name="related-articles"></a>相關文章

* [開始使用 Service Fabric 和 Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [開始使用 Service Fabric XPlat CLI](service-fabric-azure-cli.md)

