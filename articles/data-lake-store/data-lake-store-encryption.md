---
title: "Azure Data Lake Store 中的加密 | Microsoft Docs"
description: "了解 Azure Data Lake Store 中的加密和金鑰輪替方式"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 9bd9996a4a22b2f57510b6f3b6625e22b3183b1c
ms.contentlocale: zh-tw
ms.lasthandoff: 04/29/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Azure Data Lake Store 中的資料加密

## <a name="overview-of-encryption-in-azure-data-lake-store"></a>Azure Data Lake Store 中的加密概觀

Azure Data Lake Store 中的加密 (ADLS) 讓您能夠保護您的資料，實作企業安全性原則，並符合合規性需求。 本文提供設計概觀，並討論 Data Lake Store 如何實作資料加密的技術層面。

ADLS 支援預設開啟的透明待用資料加密。 以下是這些詞彙的更詳細說明︰

* 預設開啟︰建立新的 Azure Data Lake Store 帳戶時，預設設定會啟用加密。 因此，Data Lake Store 中儲存的資料一律會在儲存於持續性媒體之前進行加密。 這是所有資料的行為，建立帳戶之後便無法變更。
* 透明：ADLS 會在保存資料前自動加密，以及在擷取之前將資料解密。 系統管理員會在 Data Lake Store 層級設定和管理加密。 資料存取 API 不會變更，因此因為加密而與 Data Lake Store 互動的應用程式和服務也不需要變更。

傳輸中的資料 (也稱為移動中的資料) 也一律會在 Data Lake Store 中加密。 除了在儲存至持續性媒體之前加密資料，也一律會使用 HTTPS (透過安全通訊端層的 HTTP) 保護傳輸中或移動中的資料。 HTTPS 是 Data Lake Store REST 介面支援的唯一通訊協定。

![圖 1](./media/data-lake-store-encryption/fig1.png)


## <a name="setting-up-encryption-with-azure-data-lake-store"></a>使用 Azure Data Lake Store 設定加密

在帳戶建立期間會設定 Azure Data Lake Store 的加密，預設永遠啟用。 客戶可以選擇管理金鑰或允許 Azure Data Lake Store (預設值) 為他們管理金鑰。

若要了解如何使用 Azure Data Lake Store 設定加密，請參閱 – [快速入門](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="under-the-hood--how-encryption-works-in-azure-data-lake-store"></a>背後原理 - 了解 Azure Data Lake Store 中的加密方式

### <a name="master-encryption-keys"></a>主要加密金鑰

Azure Data Lake Store 提供兩種管理主要加密金鑰 (MEK) 的模式。 底下會更詳細地進一步說明主要加密金鑰的使用方式。 現在，假設主要加密金鑰是最上層金鑰。 需要有主要加密金鑰的存取權，才能將任何儲存在 Data Lake Store 中的資料解密。

管理主要加密金鑰的兩種模式如下︰

1.    服務管理的金鑰
2.    客戶管理的金鑰

這兩種模式都會將主要加密金鑰儲存在 Azure Key Vault 中保護。 Azure Key Vault 是 Azure 上受到完整管理、高度安全的服務，可用來保護加密金鑰。 您可以從[這裡](https://azure.microsoft.com/services/key-vault)深入了解 Azure Key Vault。

以下是兩種管理 MEK 模式所提供的簡潔功能比較。

|  | 服務管理的金鑰 | 客戶管理的金鑰 |
| --- | --- | --- |
|儲存資料的方式|一律在儲存之前加密|一律在儲存之前加密|
|主要加密金鑰的儲存位置|Azure Key Vault|Azure Key Vault|
|任何加密金鑰是否會完全儲存在 Azure Key Vault 外部？|否|否|
|是否可以在 Azure Key Vault 中擷取 MEK？|否。 一旦儲存在 Key Vault 中，就只能用來加密和解密。|不會。 一旦儲存在 Key Vault 中，就只能用來加密和解密。|
|誰擁有 Azure Key Vault 和 MEK？|Azure Data Lake Store 服務。|客戶擁有 Azure Key Vault，其屬於自己的 Azure 訂用帳戶。 Key Vault 中的 MEK 可以由軟體或硬體 (HSM) 管理。|
|客戶是否可以撤銷 Azure Data Lake Store 服務對於 MEK 的存取權？|否|是。 他們可以管理 Azure Key Vault 的存取控制清單，並移除 Azure Data Lake Store 服務之服務識別的存取控制項目。|
|客戶是否可以永久刪除 MEK？|否|是。 如果客戶從 Azure Key Vault 中刪除 MEK，任何人 (包括 Azure Data Lake Store 服務) 都無法解密 ADLS 帳戶中的資料。 <br><br> 如果從 Azure Key Vault 中刪除 MEK 之前，客戶明確進行備份，而後可以將它還原，進而復原資料。 如果從 Azure Key Vault 中刪除 MEK 之前，客戶明確進行備份，即可將它還原，然後復原資料。|


除了誰負責管理 MEK 以及其所在 Key Vault 的最上層差異，這兩種模式的其餘設計都相同。

選擇主要加密金鑰的模式時，請記住幾個重要的層面。

1.    您可以在佈建 ADLS 帳戶時，選擇使用客戶管理的金鑰或 ADLS 管理的金鑰。
2.    一旦佈建 ADLS 帳戶，就無法變更模式

### <a name="encryption-and-decryption-of-data"></a>資料的加密和解密

Azure Data Lake 的資料加密設計會使用三種金鑰類型。 下表摘要說明這些類型的定位：

| Key                   | 縮寫 | 相關聯的項目 | 儲存位置                             | 類型       | 注意事項                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| 主要加密金鑰 | MEK          | ADLS 帳戶 | Azure Key Vault                              | 非對稱 | 它可以由 ADLS 管理或客戶管理                                                              |
| 資料加密金鑰   | DEK          | ADLS 帳戶 | 永續性儲存體 – 由 ADLS 服務管理 | 對稱  | DEK 是由 MEK 加密，而加密的 DEK 會由服務儲存在持續性媒體上 |
| 區塊加密金鑰  | BEK          | 資料區塊 | None                                         | 對稱  | BEK 衍生自 DEK 和資料區塊                                                      |

下圖說明這些概念：

![圖 2](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>將檔案解密時的虛擬演算法︰
1.    檢查 ADLS 帳戶的 DEK 是否已快取並可供使用。
    * 若非如此，則從永續性儲存體讀取加密的 DEK，並將它傳送至 Azure Key Vault 進行解密。 快取記憶體中已解密的 DEK，且現在可供使用。
2.    對於檔案中的每個資料區塊
    * 從永續性儲存體讀取已加密的資料區塊
    * 從 DEK 和已加密的資料區塊產生 BEK
    * 使用 BEK 將資料解密
#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>將資料區塊加密時的虛擬演算法︰
1.    檢查 ADLS 帳戶的 DEK 是否已快取並可供使用。
    * 若非如此，則從永續性儲存體讀取加密的 DEK，並將它傳送至 Azure Key Vault 進行解密。 快取記憶體中已解密的 DEK，且現在可供使用。
2.    從 DEK 產生資料區塊的唯一 BEK。
3.    經由使用 AES-256 加密的 BEK，將資料區塊加密。
4.    在永續性儲存體上儲存已加密的資料區塊

> [!NOTE] 
> 基於效能考量，資料加密金鑰 (DEK) 會短暫地在記憶體中快取一小段時間，而在經過這段期間之後立即遭到清除。 儲存於持續性媒體時，一律會由主要加密金鑰 (MEK) 加密。

## <a name="key-rotation"></a>金鑰輪替

使用客戶管理的金鑰時，Azure Data Lake Store 允許輪替主要加密金鑰 (MEK)。 若要了解如何使用客戶管理的金鑰來設定 ADLS 帳戶，請參閱[開始使用](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)頁面。

### <a name="pre-requisites"></a>必要條件

設定 Azure Data Lake 時，客戶已選擇使用自己的金鑰。 建立帳戶之後，就無法變更此選項。 如果您使用預設選項進行加密，一律會使用 Azure Data Lake 管理的金鑰將您的資料加密，使用此選項時，客戶無法輪替金鑰，因為金鑰是由 Azure Data Lake。 下列步驟假設您使用客戶管理的金鑰 (從 Key Vault 中選擇自己的金鑰)。

### <a name="how-to-rotate-the-key-mek-in-azure-data-lake-store"></a>如何在 Azure Data Lake Store 中輪替金鑰 (MEK)

1. 登入新的 [Azure 入口網站](https://portal.azure.com/)
2. 瀏覽至儲存與您的 Azure Data Lake Store 帳戶相關聯之金鑰的 Key Vault 並選取 [金鑰]。

    ![之間的信任](./media/data-lake-store-encryption/keyvault.png)

3.    選取與您的 Azure Data Lake Store 帳戶相關聯的金鑰，並建立此金鑰的新版本。
  
   此時，Azure Data Lake 僅支援新版金鑰的金鑰輪替，我們不支援輪替成不同的金鑰

   ![newversion](./media/data-lake-store-encryption/keynewversion.png)

4.    瀏覽至 Azure Data Lake 儲存體帳戶，然後選取 [加密]

    ![newversion](./media/data-lake-store-encryption/select-encryption.png)

5.    您會看到一則注意事項，告知新版的金鑰可供使用，以及一個可將金鑰輪替為新版本的按鈕。 按一下 [輪替金鑰]，將金鑰更新為新的版本。

    ![完成](./media/data-lake-store-encryption/rotatekey.png)

6. 這項作業應在 2 分鐘內完成，且預計不會因為金鑰輪替而停機。 作業完成後，就會使用新版的金鑰。

