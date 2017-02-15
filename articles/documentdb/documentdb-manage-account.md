---
title: "透過 Azure 入口網站管理 DocumentDB 帳戶 | Microsoft Docs"
description: "了解如何透過 Azure 入口網站管理 DocumentDB 帳戶。 尋找使用 Azure 入口網站來檢視、複製、刪除和存取帳戶的指南。"
keywords: "Azure 入口網站, documentdb, azure, Microsoft azure"
services: documentdb
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f38b5d6127897ba2b083e0f3bb41562650214ae9


---
# <a name="how-to-manage-a-documentdb-account"></a>如何管理 DocumentDB 帳戶
了解如何在 Azure 入口網站中設定全域一致性、使用金鑰，以及刪除 DocumentDB 帳戶。

## <a name="a-idconsistencyamanage-documentdb-consistency-settings"></a><a id="consistency"></a>管理 DocumentDB 一致性設定
根據您應用程式的語意選取正確的一致性層級。 您應該閱讀[使用一致性層級來充分發揮 DocumentDB 中的可用性和效能][consistency]，來熟悉 DocumentDB 中可用的一致性層級。 DocumentDB 會在您資料庫帳戶可用的每個一致性層級提供一致性、可用性和效能保證。 使用強式一致性層級設定您的資料庫帳戶，需要將您的資料限制為單一 Azure 區域且無法全域使用。 另一方面，寬鬆的一致性層級 (限定過期、工作階段或最終) 讓您能夠將任意數目的 Azure 區域與您的資料庫帳戶產生關聯。 下列簡單步驟示範如何針對您的資料庫帳戶選取預設一致性層級。 

### <a name="to-specify-the-default-consistency-for-a-documentdb-account"></a>指定 DocumentDB 帳戶的預設一致性
1. 在 [Azure 入口網站](https://portal.azure.com/)中，存取 DocumentDB 帳戶。
2. 在帳戶刀鋒視窗中，按一下 [預設一致性] 。
3. 在 [預設一致性] 刀鋒視窗中，選取新的一致性層級，然後按一下 [儲存]。
    ![預設一致性工作階段][5]

## <a name="a-idkeysaview-copy-and-regenerate-access-keys"></a><a id="keys"></a>檢視、複製和重新產生存取金鑰
當您建立 DocumentDB 帳戶時，服務會產生兩個主要存取金鑰，用於存取 DocumentDB 帳戶時的驗證。 透過提供這兩個存取金鑰，DocumentDB 讓您可以重新產生金鑰，同時又不需中斷 DocumentDB 帳戶。 

在 [Azure 入口網站](https://portal.azure.com/)中，從 [DocumentDB 帳戶] 刀鋒視窗上的資源功能表存取 [金鑰] 刀鋒視窗，以檢視、複製及重新產生用來存取 DocumentDB 帳戶的存取金鑰。

![Azure 入口網站 [金鑰] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-manage-account/keys.png)

> [!NOTE]
> [金鑰] 刀鋒視窗也包含主要和次要連接字串，可用來從[資料移轉工具](documentdb-import-data.md)連接到您的帳戶。
> 
> 

此刀鋒視窗上也有唯讀金鑰。 讀取和查詢為唯讀作業，建立、刪除和取代則並非唯讀作業。

### <a name="copy-an-access-key-in-the-azure-portal"></a>在 Azure 入口網站中複製存取金鑰
在 [金鑰] 刀鋒視窗上，按一下要複製之金鑰旁的 [複製] 按鈕。

![在 Azure 入口網站 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/documentdb-manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>重新產生存取金鑰
您應定期變更 DocumentDB 帳戶的存取金鑰，讓連線更加安全。 指派的兩個存取金鑰可讓您在重新產生一個存取金鑰的同時，使用另一個存取金鑰維持 DocumentDB 帳戶連線。

> [!WARNING]
> 重新產生存取金鑰會影響任何相依於目前金鑰的應用程式。 所有使用存取金鑰來存取 DocumentDB 帳戶的用戶端，都必須更新為使用新的金鑰。
> 
> 

如果您有應用程式或雲端服務正在使用 DocumentDB 帳戶，除非您變換金鑰，否則會在重新產生金鑰後失去連線。 下列步驟概述變換金鑰所牽涉的程序。

1. 更新應用程式程式碼中的存取金鑰，以參考 DocumentDB 帳戶的次要存取金鑰。
2. 重新產生 DocumentDB 帳戶的主要存取金鑰。 在 [Azure 入口網站](https://portal.azure.com/)中，存取 DocumentDB 帳戶。
3. 在 [DocumentDB 帳戶] 刀鋒視窗中，按一下 [金鑰]。
4. 在 [金鑰] 刀鋒視窗中，按一下 [重新產生] 按鈕，然後按一下 [確定] 以確認要產生新的金鑰。
    ![重新產生存取金鑰](./media/documentdb-manage-account/regenerate-keys.png)
5. 一旦確認新的金鑰可供使用後 (重新產生後約 5 分鐘)，更新應用程式程式碼中的存取金鑰，以參考新的主要存取金鑰。
6. 重新產生次要存取金鑰。
   
    ![重新產生存取金鑰](./media/documentdb-manage-account/regenerate-secondary-key.png)

> [!NOTE]
> 新產生的金鑰可能需要經過幾分鐘之後才能用來存取 DocumentDB 帳戶。
> 
> 

## <a name="get-the--connection-string"></a>取得連接字串
若要擷取連接字串，請執行下列操作： 

1. 在 [Azure 入口網站](https://portal.azure.com)中，存取 DocumentDB 帳戶。
2. 在資源功能表中，按一下 [金鑰]。
3. 按一下 [主要連接字串] 或 [次要連接字串] 方塊旁邊的 [複製] 按鈕。 

如果您要在 [DocumentDB 資料庫移轉工具](documentdb-import-data.md)中使用連接字串，請在連接字串結尾加上資料庫名稱。 `AccountEndpoint=< >;AccountKey=< >;Database=< >`。

## <a name="a-iddeletea-delete-a-documentdb-account"></a><a id="delete"></a> 刪除 DocumentDB 帳戶
若要從「Azure 入口網站」中移除您已不再使用的 DocumentDB 帳戶，請使用 [DocumentDB 帳戶] 刀鋒視窗上的 [刪除帳戶] 命令。

![如何在 Azure 入口網站中刪除 DocumentDB 帳戶](./media/documentdb-manage-account/deleteaccount.png)

1. 在 [Azure 入口網站](https://portal.azure.com/)中，存取要刪除的 DocumentDB 帳戶。
2. 在 [DocumentDB 帳戶] 刀鋒視窗上，按一下 [更多]，然後按一下 [刪除帳戶]。 或者，以滑鼠右鍵按一下資料庫的名稱，然後按一下 [刪除帳戶] 。
3. 在後續的確認刀鋒視窗中輸入 DocumentDB 帳戶名稱，以確認您想要刪除該帳戶。
4. 按一下 [刪除]  按鈕。

![如何在 Azure 入口網站中刪除 DocumentDB 帳戶](./media/documentdb-manage-account/delete-account-confirm.png)

## <a name="a-idnextanext-steps"></a><a id="next"></a>後續步驟
了解如何 [開始使用 DocumentDB 帳戶](http://go.microsoft.com/fwlink/p/?LinkId=402364)。

若要深入了解 DocumentDB，請參閱 [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409)上的 Azure DocumentDB 文件。

<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/



<!--HONumber=Nov16_HO3-->


