---
title: "適用於 IntelliJ 的 Azure 工具組登入指示 | Microsoft Docs"
description: "了解如何使用適用於 IntelliJ 的 Azure 工具組登入 Microsoft Azure。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d1be0432a54ed300bc135345b660b90f3efb3b39
ms.lasthandoff: 04/22/2017


---

# <a name="azure-sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>適用於 IntelliJ 的 Azure 工具組的 Azure 登入指示

適用於 IntelliJ 的 Azure 工具組提供兩種登入您 Azure 帳戶的方法︰

  * **互動式** - 當您使用這個方法時，每次登入 Azure 帳戶都要輸入 Azure 認證。
  * **自動化** - 當您使用這個方法時，您所建立的認證檔案會包含您的服務主體資料，此後您可以使用認證檔案自動登入 Azure 帳戶。

下列各節中的步驟將說明如何使用每個方法。

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="signing-into-your-azure-account-interactively"></a>以互動方式登入您的 Azure 帳戶

下列步驟將說明如何以手動方式輸入您的 Azure 認證來登入 Azure。

1. 使用 IntelliJ IDEA 開啟您的專案。

1. 依序按一下 [工具]、[Azure] 以及 [Azure 登入]。

   ![Azure 登入的 IntelliJ 功能表][I01]

1. 當 [Azure 登入] 對話方塊出現時，請選取 [互動式]，然後按一下 [登入]。

   ![[登入] 對話方塊][I02]

1. 當 [Azure 登入] 對話方塊出現時，請輸入您的 Azure 認證，然後按一下 [登入]。

   ![[Azure 登入] 對話方塊][I03]

1. 當 [選取訂用帳戶]對話方塊出現時，請選取您要使用的訂用帳戶，然後按一下 [確定]。

   ![[選取訂用帳戶] 對話方塊][I04]

## <a name="signing-out-of-your-azure-account-when-you-signed-in-interactively"></a>當您以互動方式登入時，登出您的 Azure 帳戶

在上一節中設定步驟之後，每次您重新啟動 IntelliJ IDEA 時，都會自動將您登出 Azure 帳戶。 不過，如果您想要登出您的 Azure 帳戶而不重新啟動 IntelliJ IDEA，請使用下列步驟。

1. 在 IntelliJ IDEA 中，依序按一下 [工具]、[Azure] 以及 [Azure 登出]。

   ![Azure 登出的 IntelliJ 功能表][L01]

1. 當 [Azure 登出] 對話方塊出現時，按一下 [是]。

   ![[登出] 對話方塊][L02]

## <a name="signing-into-your-azure-account-automatically-and-creating-a-credentials-file-to-use-in-the-future"></a>自動登入 Azure 帳戶，並建立要在未來使用的認證檔案

下列步驟將逐步引導您建立的認證檔案會包含您的服務主體資料。 一旦完成這些步驟後，Eclipse 會自動使用認證檔案，讓您每次開啟專案時都會自動登入 Azure。

1. 使用 IntelliJ IDEA 開啟您的專案。

1. 依序按一下 [工具]、[Azure] 以及 [Azure 登入]。

   ![Azure 登入的 IntelliJ 功能表][A01]

1. 當 [Azure 登入] 對話方塊出現時，請選取 [自動化]，然後按一下 [新增]。

   ![[登入] 對話方塊][A02]

1. 當 [Azure 登入] 對話方塊出現時，請輸入您的 Azure 認證，然後按一下 [登入]。

   ![[Azure 登入] 對話方塊][A03]

1. 當 [建立驗證檔案] 對話方塊出現時，請選取您想要使用的訂用帳戶，選擇您的目的地目錄，然後按一下 [啟動]。

   ![[Azure 登入] 對話方塊][A04]

1. [服務主體建立狀態]對話方塊隨即顯示，並在您的檔案成功建立之後，按一下 [確定]。

   ![[服務主體建立狀態] 對話方塊][A05]

1. 當 [Azure 登入]對話方塊出現時，按一下 [登入]。

   ![[Azure 登入] 對話方塊][A06]

1. 當 [選取訂用帳戶]對話方塊出現時，請選取您要使用的訂用帳戶，然後按一下 [確定]。

   ![[選取訂用帳戶] 對話方塊][A07]

## <a name="signing-out-of-your-azure-account-when-you-signed-in-automatically"></a>當您以自動化方式登入時，登出您的 Azure 帳戶

在上一節中設定步驟之後，每次您重新啟動 IntelliJ IDEA 時，Azure 工具組都會自動將您登入 Azure 帳戶。 不過，若要登出您的 Azure 帳戶，並防止 Azure 工具組自動將您登入，請使用下列步驟。

1. 在 IntelliJ IDEA 中，依序按一下 [工具]、[Azure] 以及 [Azure 登出]。

   ![Azure 登出的 IntelliJ 功能表][L01]

1. 當 [Azure 登出] 對話方塊出現時，按一下 [是]。

   ![[登出] 對話方塊][L03]

## <a name="signing-into-your-azure-account-automatically-using-a-credentials-file-which-you-have-already-created"></a>使用已建立的認證檔案來自動登入 Azure 帳戶

如果您在使用 IntelliJ IDEA 時登出 Azure，必須重新設定適用於 Eclipse 的 Azure 工具組來使用已建立的認證檔案之後，才可以自動登入 Azure 帳戶。 下列步驟將引導您將 Azure 工具組設定為使用現有的認證檔案。

1. 使用 IntelliJ IDEA 開啟您的專案。

1. 依序按一下 [工具]、[Azure] 以及 [Azure 登入]。

   ![Azure 登入的 IntelliJ 功能表][A01]

1. 當 [Azure 登入] 對話方塊出現時，請選取 [自動化]，然後按一下 [瀏覽]。

   ![[登入] 對話方塊][A02]

1. 當 [選取驗證檔案] 對話方塊出現時，請選取您稍早建立的認證檔案，然後按一下 [選取]。

   ![[登入] 對話方塊][A08]

1. 當 [Azure 登入]對話方塊出現時，按一下 [登入]。

   ![[Azure 登入] 對話方塊][A06]

1. 當 [選取訂用帳戶]對話方塊出現時，請選取您要使用的訂用帳戶，然後按一下 [確定]。

   ![[選取訂用帳戶] 對話方塊][A07]

## <a name="see-also"></a>另請參閱
如需適用於 Java IDE 的 Azure 套件組的詳細資訊，請參閱下列連結：

* [適用於 Eclipse 的 Azure 工具組]
  * [適用於 Eclipse 的 Azure 工具組的新功能]
  * [安裝 Azure Toolkit for Eclipse]
  * [適用於 Eclipse 的 Azure 工具組登入指示]
  * [Create a Hello World Web App for Azure in Eclipse (在 Eclipse 中建立 Azure Hello World Web 應用程式)]
* [Azure Toolkit for IntelliJ]
  * [適用於 IntelliJ 的 Azure 工具組新增功能]
  * [安裝 Azure Toolkit for IntelliJ]
  * *適用於 IntelliJ 的 Azure 工具組登入指示 (本文)*
  * [在 IntelliJ 中建立 Azure Hello World Web 應用程式]

如需有關使用 Azure 搭配 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心]和[適用於 Visual Studio Team Services 的 Java 工具]。

<!-- URL List -->

[適用於 Eclipse 的 Azure 工具組]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse (在 Eclipse 中建立 Azure Hello World Web 應用程式)]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[在 IntelliJ 中建立 Azure Hello World Web 應用程式]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[安裝 Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[安裝 Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[適用於 Eclipse 的 Azure 工具組登入指示]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Sign In Instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[適用於 Eclipse 的 Azure 工具組的新功能]: ./azure-toolkit-for-eclipse-whats-new.md
[適用於 IntelliJ 的 Azure 工具組新增功能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 開發人員中心]: https://azure.microsoft.com/develop/java/
[適用於 Visual Studio Team Services 的 Java 工具]: https://java.visualstudio.com/

<!-- IMG List -->

[I01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I01.png
[I02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I02.png
[I03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I03.png
[I04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I04.png

[A01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A01.png
[A02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A02.png
[A03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A03.png
[A04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A04.png
[A05]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A05.png
[A06]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A06.png
[A07]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A07.png
[A08]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A08.png

[L01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L01.png
[L02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L02.png
[L03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L03.png

