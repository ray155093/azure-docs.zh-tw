---
title: "使用適用於 IntelliJ 的 Azure 工具組來管理虛擬機器 | Microsoft Docs"
description: "了解如何使用適用於 IntelliJ 的 Azure 工具組來管理 Azure 虛擬機器。"
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
ms.openlocfilehash: 0452267fa11bb09ccebd0bc9032ac269d79451d5
ms.lasthandoff: 04/22/2017


---

# <a name="managing-virtual-machines-using-the-azure-explorer-for-intellij"></a>使用適用於 IntelliJ 的 Azure 工具組來管理虛擬機器

Azure Explorer 是適用於 IntelliJ 的 Azure 工具組一部分，可為 Java 開發人員提供易於使用的解決方案，從 IntelliJ IDE 內管理其 Azure 帳戶中的虛擬機器。

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="creating-a-virtual-machine-in-intellij"></a>在 IntelliJ 中建立虛擬機器

下列步驟將逐步引導您使用 Azure Explorer 建立虛擬機器的步驟。

1. 使用[適用於 IntelliJ 的 Azure 工具組登入指示]文章中的步驟來登入您的 Azure 帳戶。

1. 在 [Azure Explorer] 工具視窗中，展開 **Azure** 節點，以滑鼠右鍵按一下 [虛擬機器]，然後按一下 [建立 VM]。
   ![建立 VM 功能表][CR01]

1. 當 [建立新的虛擬機器] 精靈出現時，請選擇您的訂用帳戶，然後按 [下一步]。
   ![建立新的虛擬機器精靈][CR02]

1. 在精靈的下一個畫面中，指定下列選項，然後按 [下一步]：![建立新的虛擬機器精靈][CR03]

   a. **位置**︰指定要建立虛擬機器的位置；例如「美國西部」。

   b. **建議的映像**︰指定您會從常用映像的簡短清單中選擇映像。

   c. **自訂映像**︰指定您會選擇自訂映像，您需要針對這些自訂映像指定下列選項︰

      * **發行者**︰指定建立了您會用來建立虛擬機器之映像的發行者；例如「Microsoft」。

      * **供應項目**︰指定虛擬機器將從選取之發行者使用的供應項目；例如「JDK」。

      * **SKU**︰指定要從選取的供應項目使用之 Stockkeeping 單元 (SKU)，例如「JDK_8」。

      * **版本 #**︰指定要從選取的 SKU 使用的版本。

1. 在精靈的下一個畫面中，指定下列選項，然後按 [下一步]：![建立新的虛擬機器精靈][CR04]

   a. **虛擬機器名稱**：指定您新虛擬機器的名稱，其必須以字母開頭，且只能包含字母、數字及連字號。

   b.這是另一個 C# 主控台應用程式。 **大小**︰指定要配置給虛擬機器的核心和記憶體數目。

   c. **使用者名稱**︰指定要管理您的虛擬機器所建立的系統管理員帳戶。

   d. **密碼**和**確認**︰指定您系統管理員帳戶的密碼。

1. 在精靈的最後一個畫面中，指定下列選項，然後按 [完成]：![建立新的虛擬機器精靈][CR07]

   a. **資源群組**︰指定虛擬機器的資源群組；您必須選擇下列選項之一︰
      * **新建**：指定您想要建立新的資源群組。
      * **使用現有**︰指定您將從與您 Azure 帳戶相關聯的資源群組清單中進行選擇。

   b.這是另一個 C# 主控台應用程式。 **儲存體帳戶**︰指定要用來儲存您虛擬機器的儲存體帳戶；您可以選擇現有的儲存體帳戶或建立新的帳戶。 如果您選擇 [新建] **&lt;&lt;&gt;&gt;**，將會顯示下列對話方塊︰

      ![新建儲存體帳戶對話方塊][CR05]

   c. **虛擬網路**和**子網路**︰指定虛擬機器要連線的虛擬網路和子網路；您可以選擇要用於您虛擬機器的現有網路和子網路，也可以建立新的網路和子網路。 如果您選擇 [新建] **&lt;&lt;&gt;&gt;**，將會顯示下列對話方塊︰

      ![新建虛擬網路對話方塊][CR06]

   d. **公用 IP 位址**︰指定虛擬機器的外部對應 IP 位址；您可以選擇建立新的 IP 位址，或如果您的虛擬機器不會有公用 IP 位址，則選擇 **(無)**。

   e. **網路安全性群組**︰指定您的虛擬機器將會使用的選用網路防火牆；您可以選擇現有的防火牆，或如果您的虛擬機器不會使用網路防火牆，則選擇 **(無)**。

   f. **可用性設定組**︰指定您的虛擬機器可能所屬的選用可用性設定；您可以選擇現有的可用性設定組或建立新的可用性設定組，或者如果您的虛擬機器不會屬於可用性設定組，則選擇 **(無)**。

1. 完成上述步驟後，新的虛擬機器即會顯示在 Azure Explorer 工具視窗中。
   ![新的虛擬機器][CR08]

## <a name="restarting-a-virtual-machine-in-intellij"></a>在 IntelliJ 中重新啟動虛擬機器

若要在 IntelliJ 中使用 Azure Explorer 重新啟動虛擬機器，請使用下列步驟︰

1. 在 **Azure Explorer** 工具視窗中，以滑鼠右鍵按一下虛擬機器，然後選擇 [重新啟動]。
   ![重新啟動虛擬機器][RE01]

1. 出現提示時，按一下 [是]，重新啟動虛擬機器。
   ![重新啟動虛擬機器][RE02]

## <a name="shutting-down-a-virtual-machine-in-intellij"></a>在 IntelliJ 中將虛擬機器關機

若要在 IntelliJ 中使用 Azure Explorer 將執行中的虛擬機器關機，請使用下列步驟︰

1. 在 **Azure Explorer** 工具視窗中，以滑鼠右鍵按一下虛擬機器，然後選擇 [關機]。
   ![將虛擬機器關機][SH01]

1. 出現提示時，按一下 [是]，將虛擬機器關機。
   ![將虛擬機器關機][SH02]

## <a name="deleting-a-virtual-machine-in-intellij"></a>在 IntelliJ 中刪除虛擬機器

若要在 IntelliJ 中使用 Azure Explorer 刪除虛擬機器，請使用下列步驟︰

1. 在 **Azure Explorer** 工具視窗中，以滑鼠右鍵按一下虛擬機器，然後選擇 [刪除]。
   ![刪除虛擬機器][DE01]

1. 出現提示時，按一下 [是]，刪除虛擬機器。
   ![刪除虛擬機器][DE02]

## <a name="see-also"></a>另請參閱
如需 Azure 虛擬機器大小與定價的詳細資訊，請參閱下列連結︰

* Azure 虛擬機器大小
   * [Azure 中的 Windows 虛擬機器大小]
   * [Azure 中的 Linux 虛擬機器大小]
* Azure 虛擬機器定價
   * [Windows 虛擬機器定價]
   * [Linux 虛擬機器定價]

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

[Azure 中的 Windows 虛擬機器大小]: /azure/virtual-machines/virtual-machines-windows-sizes
[Azure 中的 Linux 虛擬機器大小]: /azure/virtual-machines/virtual-machines-linux-sizes
[Windows 虛擬機器定價]: /pricing/details/virtual-machines/windows/
[Linux 虛擬機器定價]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[RE01]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/RE01.png
[RE02]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/RE02.png

[SH01]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/SH01.png
[SH02]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/SH02.png

[DE01]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/DE01.png
[DE02]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/DE02.png

[CR01]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR01.png
[CR02]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR02.png
[CR03]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR03.png
[CR04]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR04.png
[CR05]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR05.png
[CR06]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR06.png
[CR07]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR07.png
[CR08]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR08.png

