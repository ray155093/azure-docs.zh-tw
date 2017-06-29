---
title: "使用 Azure VM 代理程式與 Jenkins 持續整合。"
description: "Azure VM 代理程式作為 Jenkins 從屬。"
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: zh-tw
ms.lasthandoff: 06/21/2017

---
# <a name="use-azure-vm-agents-for-continuous-integration-with-jenkins"></a>使用 Azure VM 代理程式與 Jenkins 持續整合。

本快速入門示範如何在 Azure 中使用 Jenkins Azure VM 代理程式外掛程式來建立隨選 Linux (Ubuntu) 代理程式。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門：

* 如果您還沒有 Jenkins Master，可以開始使用[解決方案範本](install-jenkins-solution-template.md) 
* 如果您還沒有 Azure 服務主體，請參閱[使用 Azure CLI 2.0 建立 Azure 服務主體](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)。

## <a name="install-azure-vm-agents-plugin"></a>安裝 Azure VM 代理程式外掛程式

如果您是從[解決方案範本](install-jenkins-solution-template.md)開始，Azure VM 代理程式會安裝在 Jenkins Master 中。

否則，從 Jenkins 儀表板內安裝 **Azure VM 代理程式**外掛程式。

## <a name="configure-the-plugin"></a>設定外掛程式

* 在 Jenkins 儀表板中，按一下 [管理 Jenkins] -> [設定系統] ->。 捲動到頁面底部，並尋找包含 [新增雲端] 下拉式清單的區段。 從功能表中，選取 **Microsoft Azure VM 代理程式**
* 從 Azure 認證下拉式清單中選取現有的帳戶。  若要新增新的 **Microsoft Azure 服務主體，**請輸入下列值：訂用帳戶識別碼、用戶端識別碼、用戶端密碼和 OAuth 2.0 權杖端點。

![Azure 認證](./media/jenkins-azure-vm-agents/service-principal.png)

* 按一下 [確認設定]，確定設定檔設定是否正確。
* 儲存設定，然後繼續下一個步驟。

## <a name="template-configuration"></a>範本設定

### <a name="general-configuration"></a>一般設定
接著，設定用來定義 Azure VM 代理程式的範本。 

* 按一下 [新增]可新增範本。 
* 提供新範本的名稱。 
* 針對標籤，請輸入 "ubuntu"。 會在作業設定期間使用此標籤。
* 從下拉式方塊中選取所需的區域。
* 選取所需的 VM 大小。
* 指定 Azure 儲存體帳戶名稱或保持空白，就會使用預設名稱 "jenkinsarmst"。
* 指定保留時間 (以分鐘為單位)。 此設定會定義自動刪除閒置的代理程式之前，Jenkins 可以等待的分鐘數。 如果您不想要自動刪除閒置的代理程式，可以指定 0。

![一般設定](./media/jenkins-azure-vm-agents/general-config.png)

### <a name="image-configuration"></a>映像設定

若要建立 Linux (Ubuntu) 代理程式，請選取 [映像參考]，並使用下列設定作為範例。 請參閱 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) 以取得最新版的 Azure 支援映像。

* 映像發佈者︰Canonical
* 映像提供：UbuntuServer
* 映像 Sku：14.04.5-LTS
* 映像版本：最新
* OS 類型：Linux
* 啟動方法：SSH
* 提供管理員認證
* 針對 VM 初始化指令碼，請輸入：
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![映像設定](./media/jenkins-azure-vm-agents/image-config.png)

* 按一下 [驗證範本] 以確認設定。
* 按一下 [儲存] 。

## <a name="create-a-job-in-jenkins"></a>在 Jenkins 中建立作業

* 在 Jenkins 儀表板中，按一下 [新增項目] 。 
* 輸入名稱並選取 [Freestyle 專案]，然後按一下 [確定]。
* 在 [一般] 索引標籤中，選取 [限制可執行專案的位置] 並在標籤運算式中輸入 "ubuntu"。 現在，您會在下拉式清單中看到 "ubuntu"。
* 按一下 [儲存] 。

![設定作業](./media/jenkins-azure-vm-agents/job-config.png)

## <a name="build-your-new-project"></a>建置新專案

* 返回 Jenkins 儀表板。
* 以滑鼠右鍵按一下您所建立的新作業，然後按一下 [立即建置]。 隨即開始建置。 
* 當建置完成之後，請移至 [主控台輸出]。 您會看到在 Azure 上看到建置在遠端執行。

![主控台輸出](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="reference"></a>參考

* Azure 星期五視訊：[使用 Azure VM 代理程式與 Jenkins 持續整合](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents)
* 支援資訊和設定選項：[Azure VM 代理程式 Jenkins 外掛程式 Wiki](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) 


