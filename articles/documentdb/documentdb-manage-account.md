<properties
	pageTitle="透過 Azure 入口網站管理 DocumentDB 帳戶 | Microsoft Azure"
	description="了解如何透過 Azure 入口網站管理 DocumentDB 帳戶。尋找使用 Azure 入口網站來檢視、複製、刪除和存取帳戶的指南。"
	keywords="Azure 入口網站, documentdb, azure, Microsoft azure"
	services="documentdb"
	documentationCenter=""
	authors="AndrewHoh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="anhoh"/>

# 如何管理 DocumentDB 帳戶

了解如何設定全域一致性及管理多個區域，以取得資料的全球可用性。此外，還能了解如何使用金鑰，以及如何在 Azure 入口網站中刪除帳戶。

## <a id="consistency"></a>管理 DocumentDB 一致性設定

根據您應用程式的語意選取正確的一致性層級。您應該已熟悉 DocumentDB 中可用的一致性層級︰[使用一致性層級以最大化 DocumentDB 中的可用性和效能][consistency]。DocumentDB 在您資料庫帳戶可用的每一個一致性層級提供一致性、可用性和效能保證。使用強式一致性層級設定您的資料庫帳戶，需要將您的資料限制為單一 Azure 區域且無法全域使用。另一方面，寬鬆的一致性層級 (限定過期、工作階段或最終) 讓您能夠將任意數目的 Azure 區域與您的資料庫帳戶產生關聯。下列簡單步驟示範如何針對您的資料庫帳戶選取預設一致性層級。

### 指定 DocumentDB 帳戶的預設一致性

1. 在 [Azure 入口網站](https://portal.azure.com/)中，存取 DocumentDB 帳戶。
2. 在 [帳戶] 刀鋒視窗中，如果 [設定] 刀鋒視窗尚未開啟，按一下 [所有設定]。![預設一致性工作階段][5]

3. 在 [所有設定] 刀鋒視窗中，按一下 [功能] 下的 [預設一致性] 項目。![預設一致性工作階段][6]

4. 在 [預設一致性] 刀鋒視窗中，選取新的一致性層級，然後按一下 [儲存]。
5. 透過 Azure 入口網站通知中樞可監視作業的進度。

> [AZURE.NOTE] 整個 DocumentDB 帳戶的預設一致性設定變更可能需要幾分鐘的時間才會生效。

## <a id="addregion"></a>新增區域

DocumentDB 可在大部分的 [Azure 區域][azureregions]中使用。選取資料庫帳戶的預設一致性層級之後，您可以關聯一或多個區域 (取決於您對於預設一致性層級和全球發佈需求的選擇)。

> [AZURE.NOTE] 目前可將新區域新增至在 2016 年 6 月 13 日或之後建立的新 DocumentDB 帳戶。在 Marketplace 中選取 [Azure DocumentDB - 多個區域資料庫帳戶] 來建立多個區域帳戶。在 6 月 13 日之前建立的帳戶，可在不久的將來針對全球可用性加以啟用。

1. 在 [Azure 入口網站](https://portal.azure.com/)的動態工具列，按一下 [DocumentDB 帳戶]。
2. 在 [DocumentDB 帳戶] 刀鋒視窗中，選取要修改的資料庫帳戶。
3. 在 [帳戶] 刀鋒視窗中，如果 [所有設定] 刀鋒視窗尚未開啟，按一下 [所有設定]。
4. 在 [所有設定] 刀鋒視窗中，按一下 [新增/移除區域]。![在 [DocumentDB 帳戶] > [設定] > [新增或移除區域] 下方新增區域][1]
5. 在 [新增/移除區域] 刀鋒視窗中，選取要新增或移除的區域，然後按一下 [確定]。新增區域需要費用，如需詳細資訊，請參閱定價頁面。

    ![按一下對應中的區域以新增或移除它們][2]

### 選取區域

設定兩個以上的區域時，建議根據[業務持續性和災害復原 (BCDR)：Azure 配對的區域][bcdr]文章中所述的區域配對來選取區域。

具體來說，在設定多個區域時，請務必從每個配對的區域資料行中選取相同數目的區域 (+/-1 代表奇數/偶數)。比方說，如果您想要部署到 4 個美國地區，可從左側資料行選取 2 個美國地區，然後從右側選取 2 個。因此，以下是一個適當的組合︰美國西部、美國東部、美國中北部和美國中南部。

如果只針對災害復原案例設定 2 個區域，請務必遵循本指南。針對 2 個以上的區域，遵循本指南是一個很好的做法，但只要部分選取的區域會遵守此配對，就不是那麼重要。

## <a id="selectwriteregion"></a>選取寫入區域

與您 DocumentDB 資料庫帳戶相關聯的所有區域可以提供讀取 (兩者，單一項目以及多個項目的編頁讀取) 和查詢，只有一個區域可以主動收到寫入 (插入、更新插入、取代、刪除) 要求。若要設定使用中的寫入區域，請執行下列動作


1. 在 [DocumentDB 帳戶] 刀鋒視窗中，選取要修改的資料庫帳戶。
2. 在 [帳戶] 刀鋒視窗中，如果 [所有設定] 刀鋒視窗尚未開啟，按一下 [所有設定]。
3. 在 [所有設定] 刀鋒視窗中，按一下 [寫入區域優先順序]。![在 [DocumentDB 帳戶] > [設定] > [新增或移除區域] 下方變更寫入區域][3]
4. 按一下並拖曳區域來排列區域清單的順序。區域清單中的第一個區域是使用中的寫入區域。![在 [DocumentDB 帳戶] > [設定] > [變更寫入區域] 下方重新排列區域清單，藉以變更寫入區域][4]

## <a id="keys"></a>檢視、複製和重新產生存取金鑰
當您建立 DocumentDB 帳戶時，服務會產生兩個主要存取金鑰，用於存取 DocumentDB 帳戶時的驗證。透過提供這兩個存取金鑰，DocumentDB 讓您可以重新產生金鑰，同時又不需中斷 DocumentDB 帳戶。

在 [Microsoft Azure 入口網站](https://portal.azure.com/)中，從 [DocumentDB 帳戶] 刀鋒視窗存取 [基本功能] 列中的 [金鑰] 刀鋒視窗，即可檢視、複製和重新產生用來存取 DocumentDB 帳戶的存取金鑰。

![Azure 入口網站 [金鑰] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-manage-account/keys.png)

另一個選項是從 [所有設定] 刀鋒視窗存取 [金鑰] 項目。

![[所有設定]、[金鑰] 刀鋒視窗](./media/documentdb-manage-account/allsettingskeys.png)

> [AZURE.NOTE] [金鑰] 刀鋒視窗也包含主要和次要連接字串，可用來從[資料移轉工具](documentdb-import-data.md)連接到您的帳戶。

它也包括唯讀金鑰，讓使用者有 DocumentDB 的唯讀存取權。讀取和查詢為唯讀作業，建立、刪除和取代則並非唯讀作業。

### 在 Azure 入口網站中檢視並複製存取金鑰

1. 在 [Azure 入口網站](https://portal.azure.com/)中，存取 DocumentDB 帳戶。
2. 在 [基本功能] 列中，從 [DocumentDB 帳戶] 刀鋒視窗按一下 [金鑰]。
3. 在 [金鑰] 刀鋒視窗中，按一下要複製的金鑰右邊的 [複製] 按鈕。![在 Azure 入口網站 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/documentdb-manage-account/copykeys.png)

### 重新產生存取金鑰

您應定期變更 DocumentDB 帳戶的存取金鑰，讓連線更加安全。指派的兩個存取金鑰可讓您在重新產生一個存取金鑰的同時，使用另一個存取金鑰維持 DocumentDB 帳戶連線。

> [AZURE.WARNING] 重新產生存取金鑰會影響任何相依於目前金鑰的應用程式。所有使用存取金鑰來存取 DocumentDB 帳戶的用戶端，都必須更新為使用新的金鑰。

如果您有應用程式或雲端服務正在使用 DocumentDB 帳戶，除非您變換金鑰，否則會在重新產生金鑰後失去連線。下列步驟概述變換金鑰所牽涉的程序。

1. 更新應用程式程式碼中的存取金鑰，以參考 DocumentDB 帳戶的次要存取金鑰。

2. 重新產生 DocumentDB 帳戶的主要存取金鑰。在 [Azure 入口網站](https://portal.azure.com/)中，存取 DocumentDB 帳戶。

3. 在 [基本功能] 列中，從 [DocumentDB 帳戶] 刀鋒視窗按一下 [金鑰]。

4. 在 [金鑰] 刀鋒視窗中按一下 [重新產生主要金鑰] 命令，然後按一下 [確定] 以確認要產生新的金鑰。

5. 一旦確認新的金鑰可供使用後 (重新產生後約 5 分鐘)，更新應用程式程式碼中的存取金鑰，以參考新的主要存取金鑰。

6. 重新產生次要存取金鑰。


> [AZURE.NOTE] 新產生的金鑰可能需要經過幾分鐘之後才能用來存取 DocumentDB 帳戶。

## <a id="delete"></a>刪除 DocumentDB 帳戶
若要移除 Azure 入口網站中不再使用的 DocumentDB 帳戶，請使用 [DocumentDB 帳戶] 刀鋒視窗的 [刪除] 命令。

![如何在 Azure 入口網站中刪除 DocumentDB 帳戶](./media/documentdb-manage-account/deleteaccountconfirmation.png)


1. 在 [Azure 入口網站](https://portal.azure.com/)中，存取要刪除的 DocumentDB 帳戶。
2. 在 [DocumentDB 帳戶]刀鋒視窗中，按一下 [刪除帳戶]。
3. 在後續的確認刀鋒視窗中輸入 DocumentDB 帳戶名稱，確認您想要刪除該帳戶。
4. 在確認刀鋒視窗中按一下 [**刪除**] 按鈕。

## <a id="next"></a>接續步驟

了解如何[開始使用 DocumentDB 帳戶](http://go.microsoft.com/fwlink/p/?LinkId=402364)。

若要深入了解 DocumentDB，請參閱 [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409) 上的 Azure DocumentDB 文件。


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
[azureregions]: https://azure.microsoft.com/zh-TW/regions/#services
[offers]: https://azure.microsoft.com/zh-TW/pricing/details/documentdb/

<!---HONumber=AcomDC_0629_2016-->