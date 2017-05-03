---
title: "使用適用於 Eclipse 的 Azure 工具組來發佈 Docker 容器 | Microsoft Docs"
description: "了解如何使用適用於 Eclipse 的 Azure 工具組，將 Web 應用程式發佈至 Microsoft Azure 作為 Docker 容器。"
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
ms.openlocfilehash: f738647fa1dad757b53611c7d4abe2dd99fe8838
ms.lasthandoff: 04/22/2017


---

# <a name="how-to-publish-a-web-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>如何使用適用於 Eclipse 的 Azure 工具組，將 Web 應用程式發佈作為 Docker 容器

Docker 容器是部署 Web 應用程式的廣泛使用方法，在這些 Web 應用程式中，開發人員可將所有的專案檔和相依性合併至單一套件，以供部署至伺服器。 適用於 Eclipse 的 Azure 工具組可為 Java 開發人員簡化此程序，方法為新增 發佈作為 Docker 容器功能以部署到 Microsoft Azure，本文中的步驟會逐步引導您將應用程式發佈至 Azure 作為 Docker 容器所需的步驟。

> [!NOTE]
>
> Docker 的詳細資訊位於 [Docker 網站]。
>

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>使用 Docker 容器將您的 web 應用程式發佈至 Azure

1. 在 Eclipse 中開啟 web 應用程式專案。

1. 使用下列方法之一來啟動 [發佈作為 Docker 容器] 精靈︰

   * 在**導覽**檢視中以滑鼠右鍵按一下您的專案，然後依序按一下 [Azure][發佈作為 Docker 容器]： ![發佈作為 Docker 容器][PUB01]

   * 在 Eclipse 工具列中按一下 [發佈]，然後按一下 [發佈作為 Docker 容器]： ![發佈作為 Docker 容器][PUB02]

1. 當 [在 Azure 上部署 Docker 容器] 精靈出現時，您會看到類似下圖的對話方塊︰![將 Docker 容器部署在 Azure 精靈上][PUB03]

   a. 在 [Docker 映像名稱] 文字方塊中，將您 Docker 主機的唯一名稱輸入文字方塊中。 (精靈會自動為您建立名稱，但如果您選擇便可予以修改。)

   b. **主機**視窗隨即顯示您已建立的任何 Docker 主機。
      * 如果您尚未建立任何 Docker 主機，則對話方塊的這個區段將為空白。
      * 如果您已經建立 Docker 主機，可以選擇將 web 應用程式部署至現有的主機；否則，請依照下列步驟來建立新的 Docker 主機。

1. 若要建立新的 Docker 主機，請按一下 [新增] 按鈕；這將會啟動 [建立 Docker 主機] 對話方塊。
      ![在 Azure 上部署 Docker 容器精靈][PUB04]

   a. 為您的 Docker 主機指定下列選項。 (精靈會自動產生大部分的選項，但您可以修改任何您想要自訂的選項。)

      * **名稱**︰這是 Docker 主機的唯一名稱。 (這與您稍早指定的 Docker 映像名稱不相同。)

      * **訂用帳戶**︰指定您要用於主機的 Azure 訂用帳戶。
      
      * **區域**︰指定要放置您主機的地理區域。
      
      * 在 [作業系統和大小] 索引標籤上︰
         * **主機作業系統**︰指定將包含您主機之虛擬機器的作業系統。
         * **大小**指定主機的虛擬機器大小。

      * 在 [資源群組] 索引標籤上：
         * **新的資源群組**︰可讓您為主機建立新的資源群組。
         * **現有的資源群組**︰可讓您從 Azure 帳戶指定現有的資源群組。

      * 在 [網路] 索引標籤上︰
         * **新的虛擬網路**︰可讓您為主機建立新的虛擬網路。
         * **現有的虛擬網路**︰可讓您從 Azure 帳戶指定現有的虛擬網路。

      * 在 [儲存體] 索引標籤上：
         * **新的儲存體帳戶**︰可讓您為主機建立新的儲存體帳戶。
         * **現有的儲存體帳戶**︰可讓您從 Azure 帳戶指定現有的儲存體帳戶。

   b. 一旦您指定了上述選項後，按 [下一步]。

   c. 針對 Docker 主機的虛擬機器登入選擇下列其中一個選項︰[建立 Docker 主機]   ![][PUB05]

      * **從 Azure Key Vault 匯入認證**︰可讓您指定先前儲存的一組認證，這些認證是儲存在您的 Azure 訂用帳戶中。

      > [!NOTE]
      > 使用特定帳戶或服務主體建立的 Azure Key Vault，將無法由另一個共用相同訂用帳戶的帳戶或服務主體自動存取。 若要讓另一個帳戶或服務主體使用 Key Vault，您必須使用 Azure 入口網站來新增該帳戶或服務主體。

      * **新的登入認證**︰可讓您建立一組新的登入認證，其會要求您在 [VM 認證] 索引標籤上指定下列選項︰
         * **使用者名稱**︰指定您虛擬機器登入的使用者名稱。
         * **密碼**和**確認**︰指定您虛擬機器登入的密碼。
         * **SSH**︰指定您 Docker 主機的安全殼層 (SSH) 設定；您可以選擇下列選項︰
            * **無**︰指定虛擬機器將不允許 SSH 連線。
            * **自動產生**︰這個選項會自動建立透過 SSH 連線的必要設定。
            * **從目錄匯入**︰可讓您指定包含一組先前儲存之 SSH 設定的目錄。 更具體來說，目錄必須包含下列兩個檔案︰
               * id_rsa︰這個檔案包含使用者的 RSA 識別。
               * id_rsa.pub︰這個檔案包含將用於驗證的 RSA 公開金鑰。
        
      * 在 [Docker 精靈存取] 索引標籤上，指定下列選項︰![建立 Docker 主機][PUB06]

         * **Docker 精靈連接埠**︰指定您 Docker 主機的唯一 TCP 通訊埠。
         * **TLS 安全性**︰指定您 Docker 主機的傳輸層安全性設定；您可以選擇下列選項︰
            * **無**︰指定虛擬機器將不允許 TLS 連線。
            * **自動產生**︰這個選項會自動建立透過 TLS 連線的必要設定。
            * **從目錄匯入**︰可讓您指定包含一組先前儲存之 TLS 設定的目錄。 更具體來說，目錄必須包含下列六個檔案︰
               * ca.pem 和 ca key.pem︰這些檔案包含 TLS 憑證授權單位的憑證和公開金鑰。
               * cert.pem 和 key.pem︰這些檔案包含將用於 TLS 驗證的用戶端憑證和公開金鑰。
               * server.pem 和 server-key.pem︰這些檔案包含主機的伺服器憑證和公開金鑰。

   d. 一旦您輸入了上述所有選項時，按一下 [完成]。

1. 當 [在 Azure 上部署 Docker 容器] 精靈再度出現時，按 [下一步]。
   ![[在 Azure 上部署 Docker 容器] 精靈][PUB07]

1. 在精靈的最後一個頁面上，指定下列選項︰

   * **Docker 容器名稱**︰這是您 Docker 容器的唯一名稱。

   * 選擇下列其中一個 Docker 映像：

      * **預先定義的 Docker 映像**︰可讓從 Azure 您指定既存的 Docker 映像。

      > [!NOTE]
      > 此下拉式選單中的 Docker 映像清單包含數個映像，已設定 Azure 工具組將其修補，以便將您的成品自動部署。

      * **自訂 Dockerfile**︰可讓您從本機電腦指定先前儲存的 Dockerfile。

      > [!NOTE]
      > 這個更進階的功能適用於想要部署專屬 Dockerfile 的開發人員。 不過，這取決於使用此選項來確保已正確建置其 Dockerfile 的開發人員。 Azure 工具組並不會驗證自訂 Dockerfile 中的內容，因此如果 Dockerfile 有問題，則部署可能會失敗。 此外，Azure 工具組預期自訂 Dockerfile 會包含 web 應用程式成品，且會嘗試開啟 HTTP 連線；如果開發人員發佈不同類型的成品，可能會在部署之後收到無關緊要的錯誤。

   * **連接埠設定**︰指定繫結為您 Docker 容器的唯一 TCP 通訊埠。
   ![[在 Azure 上部署 Docker 容器] 精靈][PUB08]

1. 一旦您完成上述所有步驟後，按一下 [完成]。

Azure 工具組就會開始將您的 web 應用程式部署至 Azure 中的 Docker 容器。 

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
  * [適用於 IntelliJ 的 Azure 工具組登入指示]
  * [在 IntelliJ 中建立 Azure Hello World Web 應用程式]

如需有關使用 Azure 搭配 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心]和[適用於 Visual Studio Team Services 的 Java 工具]。

如需 Docker 的其他資源，請參閱官方 [Docker 網站]。

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

[Docker 網站]: https://www.docker.com/

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB08.png

