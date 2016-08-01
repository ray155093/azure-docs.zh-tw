<properties 
	pageTitle="使用 Azure 入口網站管理 Azure 資源 | Microsoft Azure" 
	description="使用 Azure 入口網站和 Azure Resource Manager 來管理資源。示範如何使用儀表板和圖格來監視資源。" 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2016" 
	ms.author="tomfitz"/>


# 透過入口網站管理 Azure 資源

> [AZURE.SELECTOR]
- [入口網站](azure-portal/resource-group-portal.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [.NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [節點](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

本主題示範如何使用 [Azure 入口網站](https://portal.azure.com)搭配 [Azure Resource Manager](../resource-group-overview.md) 來管理您的 Azure 資源。若要了解如何透過入口網站部署資源，請參閱[使用 Resource Manager 範本與 Azure 入口網站來部署資源](../resource-group-template-deploy-portal.md)。

目前並非所有服務都支援入口網站或資源管理員。針對這些服務，您必須使用[傳統入口網站](https://manage.windowsazure.com)。如需每個服務的狀態，請參閱 [Azure 入口網站可用性圖表](https://azure.microsoft.com/features/azure-portal/availability/)。

<a id="access-control-for-azure-dashboards" />
## 自訂儀表板來監視資源

入口網站提供一個儀表板，讓您可用來監視及管理資源。儀表板是可完全自訂的，而您可以建立多個儀表板，輕鬆地提供不同的訂用帳戶檢視。

![儀表板](./media/resource-group-portal/dashboard.png)

> [AZURE.TIP] 了解如何使用儀表板的最好方式是檢視 [Build Custom Dashboards in the Microsoft Azure Portal (在 Microsoft Azure 入口網站中建置自訂儀表板)](https://channel9.msdn.com/Blogs/trevor-cloud/azure-portal-dashboards) 影片。

### 共用 Azure 儀表板和存取控制
設定儀表板之後，您可以將它發佈，並與組織中的其他使用者共用。Azure [角色型存取控制](../active-directory/role-based-access-control-configure.md)會控管入口網站中圖格所顯示資訊的存取。所有已發佈的儀表板都會實作為 Azure 資源。從存取控制觀點來看，儀表板與虛擬機器或儲存體帳戶並無不同。

範例如下。假設您有 Azure 訂用帳戶，而且已為您小組的各種成員指派訂用帳戶的**擁有者**、**參與者**或**讀取者**角色。身為擁有者或參與者的使用者能夠列出、檢視、建立、修改或刪除訂用帳戶內的儀表板。身為讀取者的使用者能夠列出和檢視儀表板，但無法進行修改或刪除。具有讀取者存取權的使用者能夠對已發佈的儀表板進行本機編輯 (例如在針對問題進行疑難排解時)，但無法將這些變更發佈回伺服器。他們會有為自己製作儀表板的私人複本的選項。

儀表板上的個別圖格會根據其顯示的資源，強制執行自己的存取控制需求。因此，您可以設計一個廣泛共用且同時仍會保護個別圖格上資料的儀表板。

## 管理資源群組

1. 若要查看訂用帳戶中的所有資源群組，請選取 [資源群組]。

    ![瀏覽資源群組](./media/resource-group-portal/browse-groups.png)

2. 選取您想要管理的特定資源群組。您將會看到資源群組刀鋒視窗，其中提供該資源群組的相關資訊，包括群組中所有資源的清單。

    ![資源群組摘要](./media/resource-group-portal/group-summary.png)

    選取任何資源都會顯示關於該資源的詳細資料。

3. 在資源的刀鋒視窗中，您可以選取摘要下方的 [新增區段]，來新增更多的圖形和資料表。

    ![新增區段](./media/resource-group-portal/add-section.png)

4. 您會看到圖格資源庫，以便選取您要包含在刀鋒視窗中的資訊。顯示的圖格類型會依資源類型篩選。選取不同的資源將會變更可用的圖格。

    ![新增區段](./media/resource-group-portal/tile-gallery.png)

5. 將您需要的圖格拖曳至可用的空間。

    ![拖曳圖格](./media/resource-group-portal/drag-tile.png)

6. 選取入口網站頂端的 [完成] 之後，新檢視就是刀鋒視窗的一部分。

    ![顯示圖格](./media/resource-group-portal/show-lens.png)

7. 若要快速存取資源群組，您可以將此刀鋒視窗釘選到您的儀表板。

    ![釘選資源群組](./media/resource-group-portal/pin-group.png)

    或者，您可以選取區段上方的省略符號 (...)，將此刀鋒視窗的區段釘選到您的儀表板。您也可以自訂刀鋒視窗中的區段大小或完全予以移除。下圖顯示如何釘選、自訂或移除 [CPU 和記憶體] 區段。

    ![釘選區段](./media/resource-group-portal/pin-cpu-section.png)

8. 將此區段釘選到儀表板之後，您會在儀表板上看見摘要。

    ![檢視儀表板](./media/resource-group-portal/view-startboard.png)

而且，立即加以選取，您即可看到更多有關資料的詳細資訊。

## 標記資源

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。如需有關透過入口網站使用標記的資訊，請參閱[使用標記來組織您的 Azure 資源](../resource-group-using-tags.md)。

## 檢視訂用帳戶和成本

您可以檢視訂用帳戶的相關資訊和所有資源的彙總成本。選取 [訂用帳戶] 及您想要查看的訂用帳戶。您可能只有一個訂用帳戶可選取。

![訂用帳戶)](./media/resource-group-portal/select-subscription.png)

在訂用帳戶刀鋒視窗內，您會看到完工速率。

![完工速率](./media/resource-group-portal/burn-rate.png)

還有依資源類型的成本分析。

![資源成本](./media/resource-group-portal/cost-by-resource.png)

## 匯出範本

設定資源群組之後，您可以檢視此資源群組的資源管理員範本。匯出此範本有兩個優點︰

1. 因為所有基礎結構都已定義於範本中，所以您可以輕鬆地自動進行解決方案的未來部署。

2. 您可以查看代表您的解決方案的 JavaScript 物件標記法 (JSON)，藉此熟悉範本語法。

如需逐步指引，請參閱[從現有資源匯出 Azure Resource Manager 範本](../resource-manager-export-template.md)。

## 刪除資源群組或資源

刪除資源群組會刪除其內含的所有資源。您也可以刪除資源群組內的個別資源。在刪除資源群組時應多加留意，因為可能會有其他資源群組中的資源連結至該群組。不會刪除連結的資源，但它們可能無法如預期般運作。

![刪除群組](./media/resource-group-portal/delete-group.png)


## 後續步驟

- 若要檢視稽核記錄檔，請參閱[使用 Resource Manager 來稽核作業](../resource-group-audit.md)。
- 若要針對部署錯誤進行疑難排解，請參閱[使用 Azure 入口網站對資源群組部署進行疑難排解](../resource-manager-troubleshoot-deployments-portal.md)。
- 若要透過入口網站部署資源，請參閱 [使用 Resource Manager 範本與 Azure 入口網站來部署資源](../resource-group-template-deploy-portal.md)。

<!---HONumber=AcomDC_0720_2016-->