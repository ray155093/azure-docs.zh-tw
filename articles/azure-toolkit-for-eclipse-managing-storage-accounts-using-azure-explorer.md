---
title: "使用適用於 Eclipse 的 Azure 工具組來管理儲存體帳戶 | Microsoft Docs"
description: "了解如何使用適用於 Eclipse 的 Azure 工具組來管理 Azure 儲存體帳戶。"
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
ms.openlocfilehash: 3c95ddde2909cd8dee055724ba01f22647627bd3
ms.lasthandoff: 04/22/2017


---

# <a name="managing-storage-accounts-using-the-azure-explorer-for-eclipse"></a>使用適用於 Eclipse 的 Azure 工具組來管理儲存體帳戶

Azure Explorer 是適用於 Eclipse 的 Azure 工具組一部分，可為 Java 開發人員提供易於使用的解決方案，從 Eclipse IDE 內管理其 Azure 帳戶中的儲存體帳戶。

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="creating-a-storage-account-in-eclipse"></a>在 Eclipse 中建立儲存體帳戶

下列步驟將逐步引導您使用 Azure Explorer 建立儲存體帳戶的步驟。

1. 使用[適用於 Eclipse 的 Azure 工具組登入指示]文章中的步驟來登入您的 Azure 帳戶。

1. 在 **Azure Explorer** 檢視中，展開 **Azure** 節點，以滑鼠右鍵按一下 [儲存體帳戶]，然後按一下 [建立儲存體帳戶]。
   ![建立儲存體帳戶功能表][CS01]

1. 當 [建立儲存體帳戶] 對話方塊出現時，請指定下列選項︰![建立新的儲存體帳戶對話方塊][CS02]

   a. **名稱**：指定新儲存體帳戶的名稱。

   b.這是另一個 C# 主控台應用程式。 **訂用帳戶**：指定您要用於新儲存體帳戶的 Azure 訂用帳戶。

   c. **資源群組**︰指定虛擬機器的資源群組；您必須選擇下列選項之一︰
      * **新建**：指定您想要建立新的資源群組。
      * **使用現有**︰指定您將從與您 Azure 帳戶相關聯的資源群組清單中進行選擇。

   d. **區域**︰指定要建立儲存體帳戶的位置，例如「美國西部」。

   e. **帳戶種類**︰指定要建立的儲存體帳戶類型，例如「Blob 儲存體」。 (如需詳細資訊，請參閱[關於 Azure 儲存體帳戶]。)

   f. **效能**︰指定儲存體帳戶將從選取之發行者使用的供應項目，例如「進階」。 (如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標]。)

   g. **複寫**︰指定儲存體帳戶的複寫，例如「區域備援」。 (如需詳細資訊，請參閱 [Azure 儲存體複寫]。)

1. 當您指定了上述所有選項時，按一下 [建立]。

## <a name="creating-a-storage-container-in-eclipse"></a>在 Eclipse 中建立儲存體容器

下列步驟將逐步引導您使用 Azure Explorer 建立儲存體容器的步驟。

1. 在 Azure Explorer 中，以滑鼠右鍵按一下您要在其中建立容器的儲存體帳戶，然後按一下 [建立 blob 容器]。
   ![建立儲存體容器功能表][CC01]

1. 當 [建立 Blob 容器] 對話方塊出現時，請指定您容器的名稱，然後按一下 [確定]。 (如需有關命名儲存體容器的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料]。)![建立儲存體容器對話方塊][CC02]

## <a name="deleting-a-storage-container-in-eclipse"></a>在 Eclipse 中刪除儲存體容器

若要使用 Azure Explorer 刪除儲存體容器，請使用下列步驟︰

1. 在 Azure Explorer 中，以滑鼠右鍵按一下儲存體容器，然後按一下 [刪除]。
   ![刪除儲存體容器功能表][DC01]

1. 出現提示時，按一下 [確定] 以刪除儲存體容器。
   ![刪除儲存體容器對話方塊][DC02]

## <a name="deleting-a-storage-account-in-eclipse"></a>在 Eclipse 中刪除儲存體帳戶

若要使用 Azure Explorer 刪除儲存體帳戶，請使用下列步驟︰

1. 在 **Azure Explorer** 檢視中，以滑鼠右鍵按一下儲存體帳戶，然後選擇 [刪除]。
   ![刪除儲存體帳戶功能表][DS01]

1. 出現提示時，按一下 [確定] 以刪除儲存體帳戶。
   ![刪除儲存體帳戶對話方塊][DS02]

## <a name="see-also"></a>另請參閱
如需 Azure 儲存體帳戶、大小與定價的詳細資訊，請參閱下列連結︰

* [Microsoft Azure 儲存體簡介]
* [關於 Azure 儲存體帳戶]
* Azure 儲存體帳戶大小
  * [Azure 中的 Windows 儲存體帳戶大小]
  * [Azure 中的 Linux 儲存體帳戶大小]
* Azure 儲存體帳戶定價
  * [Windows 儲存體帳戶定價]
  * [Linux 儲存體帳戶定價]

如需適用於 Java IDE 的 Azure 套件組的詳細資訊，請參閱下列連結：

* [適用於 Eclipse 的 Azure 工具組]
  * [適用於 Eclipse 的 Azure 工具組的新功能]
  * [安裝 Azure Toolkit for Eclipse]
  * [適用於 Eclipse 的 Azure 工具組登入指示]
  * [Create a Hello World Web App for Azure in Eclipse (在 Eclipse 中建立 Azure Hello World Web 應用程式)]
* [Azure Toolkit for IntelliJ]
  * [適用於 IntelliJ 的 Azure 工具組新增功能]
  * [安裝 Azure Toolkit for IntelliJ]
  * [適用於 IntelliJ 的 Azure 工具組登入指示]
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
[適用於 IntelliJ 的 Azure 工具組登入指示]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[適用於 Eclipse 的 Azure 工具組的新功能]: ./azure-toolkit-for-eclipse-whats-new.md
[適用於 IntelliJ 的 Azure 工具組新增功能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 開發人員中心]: https://azure.microsoft.com/develop/java/
[適用於 Visual Studio Team Services 的 Java 工具]: https://java.visualstudio.com/

[Microsoft Azure 儲存體簡介]: /azure/storage/storage-introduction
[關於 Azure 儲存體帳戶]: /azure/storage/storage-create-storage-account
[Azure 儲存體複寫]: /azure/storage/storage-redundancy
[Azure 儲存體延展性和效能目標]: /azure/storage/storage-scalability-targets
[命名和參考容器、Blob 及中繼資料]: http://go.microsoft.com/fwlink/?LinkId=255555

[Azure 中的 Windows 儲存體帳戶大小]: /azure/virtual-machines/virtual-machines-windows-sizes
[Azure 中的 Linux 儲存體帳戶大小]: /azure/virtual-machines/virtual-machines-linux-sizes
[Windows 儲存體帳戶定價]: /pricing/details/virtual-machines/windows/
[Linux 儲存體帳戶定價]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC02.png

