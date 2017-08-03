---
title: "Marketplace 中 Azure 受管理的應用程式 | Microsoft Docs"
description: "描述可透過 Marketplace 取得的 Azure 受管理的應用程式。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: bd7880bdbca8cbf1abcab2cbade050876e26aea1
ms.contentlocale: zh-tw
ms.lasthandoff: 07/13/2017

---

# <a name="azure-managed-applications-in-the-marketplace"></a>Marketplace 中 Azure 受管理的應用程式

如[受管理的應用程式概觀](managed-application-overview.md)一文中所討論，在 Microsoft Azure Marketplace 中受管理的應用程式讓 MSP、ISV 和系統整合業者 (SI) 能提供解決方案給所有 Azure 客戶。 這類解決方案能減少客戶的維護與服務額外負荷。 發行者可以透過 Marketplace 銷售基礎結構和軟體，並將服務和操作支援附加至它們。 

這篇文章說明 MSP、ISV 或 SI 如何將應用程式發行至 Marketplace，並將它廣泛提供給客戶。  

## <a name="pre-requisites-for-publishing-a-managed-application"></a>發行受管理應用程式的必要條件

列於 Marketplace 的必要條件

* 技術需求

    *  如需 Resource Manager 範本之結構和語法的詳細資訊，請參閱 [製作 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
    *  若要檢視完整範本解決方案，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/en-us/documentation/templates/)或[快速入門範本儲存機制](https://github.com/azure/azure-quickstart-templates)。
    *  如需建立介面，讓客戶透過 Marketplace 部署應用程式的詳細資訊，請參閱[建立使用者介面定義檔](managed-application-createuidefinition-overview.md)。

* 非技術性需求 (商務需求)

    *   您的公司 (或其子公司) 必須位於 Marketplace 支援的銷售來源國家/地區。
    *   您的產品授權，必須與 Marketplace 支援的計費模式相容。
    *   您必須以合乎商業行為的方式，負責為客戶提供技術支援。 支援可以為免費、付費，或透過社群支援。
    *   您必須負責為您的軟體和任何第三方廠商相依性進行授權。
    *   為了使您的供應項目可列於 Azure Marketplace 和 Azure 入口網站中，您提供的內容必須符合標準
    *   您必須同意 Azure Marketplace 參與原則和發行者合約中的條款。
    *   您必須同意遵守使用條款、Microsoft 隱私權聲明以及 Microsoft Azure 認證方案合約。

## <a name="how-to-create-a-new-azure-application-offer"></a>如何建立新的 Azure 應用程式供應項目

符合先決條件之後，您就準備好開始撰寫您的受管理應用程式供應項目。 讓我們先快速概觀供應項目和 SKU。

### <a name="offer"></a>提供項目

受管理應用程式的供應項目對應至發行者提供的產品分類供應項目。 如果您有新類型的解決方案/應用程式，您想要在 Marketplace 中提供，可以將它設定為新的供應項目。 優惠 SKU 的集合。 每個供應項目會以個別實體出現在 Marketplace。

### <a name="sku"></a>SKU

SKU 是優惠的最小購買單位。 在同一個產品類別 (供應項目) 內，SKU 讓您可以區分不同的支援功能、供應項目是否受管理，以及和支援的計費模式。

SKU 會顯示在 Marketplace 中的父供應項目底下。 它在 Azure 入口網站中顯示成自己的可購買實體。

### <a name="set-up-offer"></a>設定供應項目

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2.  從左側導覽列，按一下 [新增供應項目] 並選取 [Azure 應用程式]。

    ![新增供應項目](./media/managed-application-author-marketplace/newOffer.png)

3.  接著會開啟新供應項目的 [編輯器] 檢視，您便準備好開始進行撰寫。

    ![供應項目設定](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

4.  在左側 [編輯器]  檢視中，可以看到需要填寫的表單。 每個表單都包含需要填寫的欄位。 必填欄位會標示紅色星號 (*)。

 **有四個主要的形式來撰寫受管理的應用程式**

    *   優惠設定
    *   SKU
    *   Marketplace
    *   支援

這些表單將於下列各節中詳細說明。

## <a name="offer-settings-form"></a>供應項目設定表單

優惠設定表單是指定優惠設定的基本表單。 下列說明各個不同欄位：

* 供應項目 ID - 此欄位是在發行者設定檔內供應項目的唯一識別碼。 此識別碼會顯示在產品的 URL，Resource Manager 範本和計費報告中。 此識別碼只能包含小寫英數字元或連字號 (-)。 此識別碼不能以連字號結尾，且不能超過 50 個字元。 供應項目上架後，此欄位便會鎖住。
* 發行者 ID - 此欄位下拉式表單，可讓您選擇您想要在什麼發行者設定檔之下發佈此供應項目。 供應項目上架後，此欄位便會鎖住。
* 名稱 - 此欄位是您供應項目的顯示名稱。 它是顯示在 Marketplace 和入口網站中的名稱。 它最多不能超過 50 個字元。 建議在產品中包含明顯的品牌名稱。 請勿包含您的公司名稱，除非這是您的行銷方式。 如果您在自己的網站行銷這項優惠，請確認名稱與您網站的名稱相同。

選取 [儲存] 儲存您的進度。 下一個步驟是為您的供應項目新增 SKU。

## <a name="skus-form"></a>SKU 表單

選取 [SKU] 表單。 您可以在這裡看到 [新 SKU] 的選項。 選取此選項可讓您輸入 [SKU 識別碼]。

![選取新的 SKU](./media/managed-application-author-marketplace/newOffer_skus.png)

[SKU 識別碼] 是在供應項目內 SKU 的唯一識別碼。 此識別碼會顯示在產品的 URL，Resource Manager 範本和計費報告中。 此識別碼只能包含小寫英數字元或連字號 (-)。 此識別碼不能以連字號結尾，且不能超過 50 個字元。 供應項目上架後，此欄位便會鎖住。 您可以在一個優惠內建立多個 SKU。 您需要針對您預計發佈的每個映像，提供 SKU。

選取 [新 SKU] 之後，需要填入下列表單：

![提供新 SKU](./media/managed-application-author-marketplace/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>如何填滿 SKU 詳細資料區段

* 標題 - 提供此 SKU 的標題。 這是此項目顯示在組件庫的內容。
* 摘要 - 提供此 SKU 的簡短摘要。 此文字會顯示在標題底下。
* 描述 - 提供有關 SKU 的詳細描述。
* SKU 類型 - 允許的值包括 [受管理的應用程式] 和 [解決方案範本]。 此案例中，選取 [受管理的應用程式]。

### <a name="how-to-fill-package-details-section"></a>如何填寫套件詳細資料區段

套件區段具有下列需要填寫的欄位

![套件](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

**目前的版本** - 提供您上傳的套件版本。 其格式應該如下 - `{number}.{number}.{number}{number}`

**套件檔案** - 此套件包含壓縮成 .zip 檔案的下列檔案。

* **applianceMainTemplate.json** - 用來部署解決方案/應用程式的部署範本檔案。 您可以在這裡找到關於如何撰寫部署範本檔案的更多詳細資料 - [建立第一個 Azure Resource Manager 範本](resource-manager-create-first-template.md)
* **appliancecreateUIDefinition.json** - Azure 入口網站會使用這個檔案來產生使用者介面，以便佈建此解決方案/應用程式。 如需詳細資訊，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
* **mainTemplate.json** - 只包含 Microsoft.Solution/appliances 資源的範本檔案。 關於這個資源，要注意的主要屬性如下：

mainTemplate 包含下列屬性：

*  kind - 針對 Marketplace 中受管理的應用程式，使用 **Marketplace**
*  ManagedResourceGroupId - 客戶訂用帳戶中的資源群組，當中會部署 applianceMainTemplate.json 中所定義的所有資源。
*  PublisherPackageId - 唯一識別套件的字串。 請以 `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}` 格式提供值。
  publisherId 和 OfferId 可以從發行入口網站取得。

  ![供應項目識別碼](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
  您可以取得 SKU 識別碼，如下圖所示：

  ![SKU 識別碼](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
  您可以取得套件版本，如下圖所示：

  ![套件版本](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  因此，使用前述範例，**PublisherPackageId** 的值是 `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`

  範例 mainTemplate.json：

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountNamePrefix": {
        "type": "string",
        "metadata": {
          "description": "Specify the name of the storage account"
        }
      },
      "storageAccountType": {
        "type": "string"
      }
    },
    "variables": {
      "managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
    },
    "resources": [{
      "type": "Microsoft.Solutions/appliances",
      "apiVersion": "2016-09-01-preview",
      "name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
      "location": "[resourceGroup().location]",
      "kind": "marketplace",
      "properties": {
        "managedResourceGroupId": "[variables('managedResourceGroup')]",
        "PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0",
        "parameters": {
          "storageAccountName": {
            "value": "[parameters('storageAccountNamePrefix')]"
          },
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }],
    "outputs": {

    }
  }
  ```

此套件應該包含要成功佈建此應用程式所需的任何其他巢狀範本或指令碼。 mainTemplate.json、applianceMainTemplate.json 和 applianceCreateUIDefinition.json 必須存在於根資料夾。

**Authorizations** - 這個屬性會定義誰可以存取客戶訂用帳戶中資源的什麼存取層級。 它可讓發行者代表客戶管理應用程式。

* PrincipalId - 這個屬性是客戶訂用帳戶中的資源上，使用者、使用者群組或被授與特定權限 (如角色定義所述) 之應用程式的 Azure Active Directory 識別碼。
* 角色定義 - 這個屬性是由 Azure AD 所提供的所有內建 RBAC 角色的清單。 您可以選取最適合且可讓您代表客戶管理資源的角色。

可以新增多個授權。 不過，建議要建立 Active Directory 使用者群組，並在 **PrincipalId** 指定其識別碼。 如此可新增更多使用者到使用者群組，並且不需要更新 SKU。

如需 RBAC 的詳細資訊，請參閱[在 Azure 入口網站中開始使用角色型存取控制](../active-directory/role-based-access-control-what-is.md)。

## <a name="marketplace-form"></a>Marketplace 表單

Marketplace 表單會要求 [Azure Marketplace](https://azuremarketplace.microsoft.com) 以及 [Azure 入口網站](https://portal.azure.com/)上顯示的欄位。

### <a name="preview-subscription-ids"></a>預覽訂用帳戶識別碼

在此輸出 Azure 訂用帳戶識別碼清單，這些訂用帳戶可以在供應項目發佈後存取供應項目。 這些經允許的訂用帳戶，可以在供應項目上架前，讓您測試預覽供應項目。 合作夥伴入口網站可讓您新增最多 100 組允許的訂用帳戶。

### <a name="suggested-categories"></a>建議的類別

從提供的清單中，選擇最能與您的供應項目建立關聯的五種類別。 選取的類別係用來將您提供的供應項目對應至 [Marketplace](https://azuremarketplace.microsoft.com) 和[入口網站](https://portal.azure.com/)中提供的產品類別。

#### <a name="azure-marketplace"></a>Azure Marketplace

受管理應用程式的摘要中，會顯示下列欄位：

![Marketplace 摘要](./media/managed-application-author-marketplace/publishvm10.png)

在受管理應用程式的概觀中，會顯示下列欄位：

![Marketplace 概觀](./media/managed-application-author-marketplace/publishvm11.png)

在受管理應用程式的方案和定價中，會顯示下列欄位：

![Marketplace 方案](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Azure 入口網站

受管理應用程式的摘要中，會顯示下列欄位：

![入口網站摘要](./media/managed-application-author-marketplace/publishvm12.png)

在受管理應用程式的概觀中，會顯示下列欄位：

![入口網站概觀](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>標誌指導方針

Cloud Partner 網站中所上傳的所有標誌都應該遵循下列指導方針︰

*   Azure 設計具有簡單的調色盤。 請保持標誌上具有最少的主要和次要色彩數目。
*   入口網站的佈景主題色彩是白色與黑色。 因此請避免使用這些色彩做為您標誌的背景色彩。 請使用會讓您的標誌在入口網站顯得突出的某些色彩。 建議您使用簡單的主要色彩。 **如果您使用透明背景，請確定標誌/文字不是白色、黑色或藍色。**
*   請不要在標誌上使用漸層背景。
*   避免在標誌上放置文字 (甚至是公司或品牌名稱)。 您標誌的外觀與風格應該是「一般」，而且應該避免漸層。
*   請確定標誌不會自動縮放。

#### <a name="hero-logo"></a>主圖標誌

主圖標誌為選用項。 發行者可以選擇不上傳主圖標誌。 但主圖圖示一旦上傳，即無法刪除。 屆時合作夥伴必須遵循主圖圖示的 Marketplace 指導方針。

##### <a name="guidelines-for-the-hero-logo-icon"></a>主圖標誌圖示的指導方針

*   發行者顯示名稱、方案標題和供應項目完整摘要會以白色字型色彩顯示。 因此，您應避免在主圖圖示的背景使用淺色系。 主圖圖示不允許使用黑色、白色和透明背景。
*   供應項目列出之後，會以程式設計方式將發行者顯示名稱、方案標題、供應項目完整摘要和 [建立] 按鈕內嵌在主圖標誌內。 因此您在設計主圖標誌時不應輸入任何文字。 因為文字 (也就是發行者顯示名稱、方案標題、供應項目完整摘要) 是以程式設計方式包含，所以我們只有在右邊留空。 文字右側的空白空間應為 415 x 100 (從左邊開始位移 370px)。

![publishvm14](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>支援表單

下一步要填寫的表單是支援表單。 此表格需要您填寫您公司中的支援連絡資訊，例如工程連絡資訊及客戶支援連絡資訊。

## <a name="how-to-publish-an-offer"></a>如何發佈優惠

完成所有區段之後，請選取 [發行] 開始讓客戶可以使用您供應項目的程序。

## <a name="next-steps"></a>後續步驟

* 如需受管理應用程式的簡介，請參閱 [Azure 受管理的應用程式概觀](managed-application-overview.md)。
* 如需從 Marketplace 使用受管理應用程式的詳細資訊，請參閱[在 Marketplace 中使用 Azure 受管理的應用程式](managed-application-consume-marketplace.md)。
* 如需發行 Service Catalog 受管理應用程式的資訊，請參閱[建立和發行 Service Catalog 受管理的應用程式](managed-application-publishing.md)。
* 如需使用 Service Catalog 受管理應用程式的詳細資訊，請參閱[使用 Service Catalog 受管理的應用程式](managed-application-consumption.md)。

