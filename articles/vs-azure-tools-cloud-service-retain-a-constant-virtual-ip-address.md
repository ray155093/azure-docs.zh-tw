---
title: "如何保持 Azure 雲端服務的固定虛擬 IP 位址 | Microsoft Docs"
description: "了解如何確保 Azure 雲端服務的虛擬 IP 位址 (VIP) 不會變更。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 12d73e2f490acc92bb4a2e1014bb24f7f672f713
ms.lasthandoff: 03/22/2017


---
# <a name="how-to-retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>如何保持 Azure 雲端服務的固定虛擬 IP 位址
當您更新裝載於 Azure 中的雲端服務時，您可能需要確保服務的虛擬 IP 位址 (VIP) 不會變更。 許多網域管理服務都使用網域名稱系統 (DNS) 註冊網域名稱。 DNS 只有在 VIP 保持不變時才能運作。 您可以使用 Azure 工具中的 [發佈精靈]  確保雲端服務的 VIP 不會在您更新它時變更。 如需有關如何使用雲端服務的 DNS 網域管理的詳細資訊，請參閱 [設定 Azure 雲端服務的自訂網域名稱](cloud-services/cloud-services-custom-domain-name.md)。

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>發佈雲端服務而不變更其 VIP
當您第一次將雲端服務的 VIP 部署至特定環境 (例如生產環境) 的 Azure 中，會配置雲端服務的 VIP。 只有當您明確刪除部署或由部署更新程序隱含地刪除部署時，VIP 才會變更。 若要保留 VIP，您不能刪除您的部署，且您必須要確定 Visual Studio 不會自動刪除您的部署。 您可以藉由在 [發佈精靈] 中指定部署設定來控制行為，該精靈可支援多種部署選項。 您可以指定全新部署或更新部署，可以是累加或同時的，且這兩種更新部署都會保留 VIP。 如需這些不同類型部署的定義，請參閱 [發佈 Azure 應用程式精靈](vs-azure-tools-publish-azure-application-wizard.md)。  此外，您可以在發生錯誤時，控制是否要刪除先前的雲端服務部署。 如果您沒有正確設定該選項，VIP 可能會意外變更。

## <a name="updating-a-cloud-service-without-changing-its-vip"></a>更新雲端服務而不變更它的 VIP
1. 在 Visual Studio 中建立或開啟 Azure 雲端服務專案。 

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後從操作功能表中選取 [發佈]。

    ![Publish menu](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

1. 在 [發行 Azure 應用程式] 對話方塊中，選取您要部署到其中的 Azure 訂用帳戶、視需要登入，然後選取 [下一步]。

    ![發佈 Azure 應用程式：登入](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

1. 在 [一般設定] 索引標籤上，確認您要部署的目標雲端服務名稱、**環境**、**組建組態**和**服務組態**全都正確無誤。

    ![發佈 Azure 應用程式 - 一般設定](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

1. 在 [進階設定] 索引標籤上，確認儲存體帳戶和部署標籤都正確無誤、已清除 [失敗時刪除部署] 核取方塊，並已選取 [部署更新] 核取方塊。 藉由選取 [部署更新] 核取方塊，您可以確定不會刪除您的部署，而且在重新發佈應用程式時不會遺失 VIP。 藉由清除 [失敗時刪除部署] 核取方塊，您可以確定在部署期間發生錯誤時，不會遺失 VIP。

    ![發佈 Azure 應用程式 - 進階設定](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

1. (選擇性) 若要進一步指定您希望更新角色的方式，請選取 [部署更新] 旁的 [設定]。 選取 [累加式更新] 或 [同時更新]。 如果您選擇 [累加式更新]，就會逐一更新應用程式的每個執行個體，讓應用程式隨時可供使用。 如果您選擇 [同時更新]，即會同時更新應用程式的所有執行個體。 同時更新較為快速，但是您的服務可能無法在更新過程中使用。 完成時，選取 [下一步]。

    ![發佈 Azure 應用程式 - 部署更新設定](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

1. 返回 [發行 Azure 應用程式] 對話方塊時，選取 [下一步]，直到 [摘要] 頁面顯示為止。 確認您的設定，然後選取 [發佈]。
   
    ![發佈 Azure 應用程式 - 摘要](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>後續步驟
- [使用 Visual Studio 發佈 Azure 應用程式精靈](vs-azure-tools-publish-azure-application-wizard.md)


