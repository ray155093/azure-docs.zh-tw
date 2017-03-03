---
title: "下載 Azure SDK for Java | Microsoft Docs"
description: "了解如何下載 Azure SDK for Java，其中包含為 Maven 專案提供的範例程式碼。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 4b8f8fe6-1b26-4bb4-9be9-6ae757a59e66
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 0933eabf99ef94f2c4b425f1435530edfde65e3f
ms.openlocfilehash: 9e55eb52797f1363cbb65050574e549c575ca5d8
ms.lasthandoff: 02/17/2017


---
# <a name="download-the-azure-sdk-for-java"></a>下載 Azure SDK for Java
本文包含適用於下載及安裝 Azure Management Libraries for Java 的指示。

> [!NOTE]
> Azure Management Libraries for Java 的散發是依據 [Apache 授權 2.0 版][license]。
>

## <a name="azure-libraries-for-java---manual-download"></a>Azure Libraries for Java - 手動下載
若要手動安裝 Azure Management Libraries for Java，請按一下 <http://go.microsoft.com/fwlink/?LinkId=690320>，以下載包含所有程式庫及所有相依性的 ZIP 檔案。

當將 zip 檔案下載到您的電腦之後，請將內容解壓縮，再使用下列選項之一，將 JAR 檔案加入您的專案中：

* 在 Eclipse 或 IntelliJ 中將 JAR 檔案匯入到您的 Java 專案中。
* 在 Eclipse 或 IntelliJ 中設定您 Java 專案的組建路徑，以包含 JAR 檔案的路徑。

> [!NOTE]
> 如需授權及其他資訊，請參閱 ZIP 中的 license.txt 和 ThirdPartyNotices.txt 檔案。
>

## <a name="azure-management-libraries-for-java---building-with-maven"></a>Azure Management Libraries for Java - 使用 Maven 進行建置
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>步驟 1 - 設定使用 Maven 建置專案
若要在 Eclipse 中建立使用 Azure Management Libraries for Java 的 Maven 專案，請依照[開始使用 Azure Management Libraries for Java (英文)][maven-getting-started] 一文中的指示操作。

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>步驟 2 - 設定您的 Maven 設定必的相依性
將專案設定為使用 Maven 來建置後，您就可以使用類似下列範例的語法，將必要的相依性新增至 pom.xml 檔案。 請注意，您無須加入下列範例中所列的每項相依性，而只需加入您專案所需的特定相依性即可。

> [!NOTE]
> 在下列範例的每一個 `<version>` 元素中，以有效的版本號碼 (可從 [Maven 上的 Azure 程式庫存放庫]取得) 取代此範例中的 "n.n.n" 預留位置。
>
>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="see-also"></a>另請參閱
如需適用於 Java IDE 的 Azure 套件組的詳細資訊，請參閱下列連結：

* [適用於 Eclipse 的 Azure 工具組]
  * [安裝 Azure Toolkit for Eclipse]
  * [Create a Hello World Web App for Azure in Eclipse (在 Eclipse 中建立 Azure Hello World Web 應用程式)]
  * [適用於 Eclipse 的 Azure 工具組的新功能]
* [Azure Toolkit for IntelliJ]
  * [安裝 Azure Toolkit for IntelliJ]
  * [在 IntelliJ 中建立 Azure Hello World Web 應用程式]
  * [適用於 IntelliJ 的 Azure 工具組新增功能]

如需如何搭配使用 Azure 與 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心]。

> [!NOTE]
> 如需在 Eclipse 中設定組建路徑的詳細資訊，請參閱 Eclipse 網站上的 [Java 組建路徑] 一文。
>

<!-- URL List -->

[適用於 Eclipse 的 Azure 工具組]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse (在 Eclipse 中建立 Azure Hello World Web 應用程式)]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[在 IntelliJ 中建立 Azure Hello World Web 應用程式]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[安裝 Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[安裝 Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[適用於 Eclipse 的 Azure 工具組的新功能]: ./azure-toolkit-for-eclipse-whats-new.md
[適用於 IntelliJ 的 Azure 工具組新增功能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[Maven 上的 Azure 程式庫存放庫]: http://go.microsoft.com/fwlink/?LinkID=286274
[Java 組建路徑]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998

