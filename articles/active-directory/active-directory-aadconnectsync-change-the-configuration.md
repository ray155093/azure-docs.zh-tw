<properties
	pageTitle="Azure AD Connect 同步處理：如何變更預設組態 | Microsoft Azure"
	description="逐步解說如何對 Azure AD Connect 同步處理中的組態進行變更。"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="andkjell"/>


# Azure AD Connect 同步處理：如何變更預設組態
本主題的目的在於逐步解說如何對 Azure AD Connect 同步處理中的預設組態進行變更。其中提供一些常見案例的步驟。具備此知識，您應該能夠根據自己的商務規則對自己的組態進行一些簡單的變更。

## 同步處理規則編輯器
同步處理規則編輯器用於查看和變更預設組態。您可以在 [開始] 功能表的 [Azure AD Connect] 群組之下找到它。![內含同步處理規則編輯器的開始功能表](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

當您開啟它時，您會看到預設的現成可用規則。

![同步處理規則編輯器](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### 在編輯器中瀏覽
編輯器頂端的下拉式清單可讓您快速找到特定規則。例如，如果您想要查看已納入屬性 proxyAddresses 的規則，您可將下拉式清單變更如下︰![SRE 篩選](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png) 若要重設篩選並載入全新的組態，請按鍵盤上的 **F5**。

右上方有 [新增規則] 按鈕。此按鈕用於建立您自己的自訂規則。

底部有可供您處理選取的同步處理規則的按鈕。[編輯] 和 [刪除] 會如您預期般執行。[匯出] 會產生 PowerShell 指令碼以便重新建立同步處理規則。此程序可讓您將同步處理規則從一部伺服器移到另一部伺服器。

## 建立您的第一個自訂規則
最常見的變更是屬性流程的變更。來源目錄中的資料可能不會以您想要的方式出現在 Azure AD 中。在本節的範例中，您要確保使用者的名稱一律為**適當的大小寫**。

### 停用排程器
[排程器](active-directory-aadconnectsync-feature-scheduler.md)預設會每隔 30 分鐘執行一次。您要確保在您進行變更時，排程器未啟動，並疑難排解您的新規則。若要暫時停用排程器，請啟動 PowerShell，然後執行 `Set-ADSyncScheduler -SyncCycleEnabled $false`

![停用排程器](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

### 建立規則

1. 按一下 [新增規則]。
2. 在 [說明] 頁面上輸入下列各項：![輸入規則篩選](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)
	- 名稱：賦予規則描述性名稱。
	- 描述︰讓其他人可以了解規則用途的一些說明。
	- 連接的系統︰可在其中找到物件的系統。在此情況下，我們會選取 Active Directory 連接器。
	- 已連線的系統/Metaverse 物件類型︰分別選取 [使用者] 和 [人員]。
	- 連結類型︰將此值變更為 [聯結]。
	- 優先順序︰提供在系統中是唯一的值。較低的數值表示優先順序較高。
	- 標籤︰保留空白。只有 Microsoft 提供的現成可用規則應該在此方塊中填入值。
3. 在 [範圍篩選器] 頁面上，輸入 **givenName ISNOTNULL**。![輸入規則範圍篩選器](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png) 此區段用來定義應套用規則的物件。如果保留空白，規則會套用到所有的使用者物件。但會包括會議室、服務帳戶，以及其他非人員的使用者物件。
4. 在 [聯結規則] 上，將它保留空白。
5. 在 [轉換] 頁面上，將 FlowType 變更為 [運算式]。選取目標屬性 **giveName**，並在 [來源] 中輸入 `PCase([givenName])`。![輸入規則轉換](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png) 同步處理引擎會區分函式名稱和屬性名稱的大小寫。如果您輸入錯誤，您會在新增規則時看到警告。編輯器可讓您儲存並繼續進行，因此您必須重新開啟規則並予以更正。
6. 按一下 [新增] 以儲存規則。

新的自訂規則應與其他同步處理規則一起顯示在系統中。

### 驗證變更
利用這項新變更，您可確保其如預期般運作，而且不會擲回任何錯誤。視您擁有的物件數目而言，有兩種不同的方式可執行此步驟。

1. 在所有物件上執行完整同步處理
2. 在單一物件上執行預覽和完整同步處理

從 [開始] 功能表啟動 [同步處理服務]。本節中的步驟全都在此工具中執行。

1. **在所有物件上執行完整同步處理** 選取位於頂端的 [連接器]。識別您在前一節中進行變更的連接器，在此例中為 Active Directory 網域服務，並加以選取。從 [動作] 中選取 [執行]，然後選取 [完整同步處理] 和 [確定]。![完整同步處理](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)物件現已在 Metaverse 中更新。您現在想要查看 Metaverse 中的物件。

2. **在單一物件上執行預覽和完整同步處理** 選取位於頂端的 [連接器]。識別您在前一節中進行變更的連接器，在此例中為 Active Directory 網域服務，並加以選取。選取 [搜尋連接器空間]。使用範圍來尋找您要用來測試變更的物件。選取物件，然後按一下 [預覽]。在新的畫面中，選取 [認可預覽]。![認可預覽](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png) 變更現已認可至 Metaverse。

**查看 Metaverse 中的物件** 您現在可挑選幾個範例物件，確定這是預期的值並已套用規則。從頂端選取 [Metaverse 搜尋]。新增您需要的任何篩選，以尋找相關的物件。從搜尋結果中，開啟物件。查看屬性值，同時在 [同步處理規則] 資料行中確認已如預期套用規則。![Metaverse 搜尋](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)
### 停用排程器
如果一切如同預期，您可以再次啟用排程器。從 PowerShell，執行 `Set-ADSyncScheduler -SyncCycleEnabled $true`。

## 其他常見屬性流程變更
上一節說明如何變更屬性流程。本節提供了一些其他的範例。如何建立同步處理規則的步驟已縮減，但您可以在上一節中找到完整的步驟。

### 使用預設以外的其他屬性
Fabrikam 中有對名字、姓氏和顯示名稱使用當地字母的樹系。在擴充屬性中可以找到以拉丁字母表示的這些屬性。在 Azure AD 和 Office 365 中建立全域通訊清單時，組織反而想要使用這些屬性。

在使用預設組態時，當地樹系中的物件看起來像這樣：![屬性流程 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

若要建立具有其他屬性流程的規則，請執行下列作業：

- 從 [開始] 功能表啟動 [同步處理規則編輯器]。
- 在左側依然選取 [輸入] 的情況下，按一下 [新增規則] 按鈕。
- 賦予規則名稱和描述。選取內部部署 Active Directory 和相關的物件類型。在 [連結類型] 中，選取 [聯結]。為優先順序挑選一個其他規則還沒使用的數字。現成可用的規則是從 100 開始，因此此範例可以使用 50 這個值。![屬性流程 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- 將範圍留空 (也就是，應該套用到樹系中的所有使用者物件)。
- 將聯結規則留空 (也就是，讓現成可用的規則處理任何聯結)。
- 在 [轉換] 中，建立下列流程：![屬性流程 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- 按一下 [新增] 以儲存規則。
- 移至 [同步處理服務管理員]。在 [連接器] 上，選取我們已在其中新增規則的連接器。依序選取 [執行] 和 [完整同步處理]。完整同步處理會使用目前的規則重新計算所有物件。

這是具有此自訂規則之相同物件的結果：![屬性流程 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### 屬性的長度
字串屬性預設會設定為可索引，且最大長度為 448 個字元。如果您使用的字串屬性可能包含更多字元，則確定在屬性流程中包含下列項目：`attributeName` <- `Left([attributeName],448)`

### 變更 userPrincipalSuffix
由於使用者未必會知道 Active Directory 中的 userPrincipalName 屬性，因此這個屬性可能不適合做為登入識別碼。Azure AD Connect 同步處理安裝精靈允許挑選不同的屬性，例如 mail。但是在某些情況下必須計算屬性。例如，Contoso 公司有兩個 Azure AD 目錄，一個用於生產環境，一個用於測試。他們想讓其測試租用戶中的使用者在登入識別碼中使用其他後置詞。`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

在此運算式中，取出第一個 @ 符號左邊的所有內容 (Word)，然後使用固定的字串串連。

### 將多重值轉換成單一值
Active Directory 中的某些屬性在結構描述中是多重值，但是在 [Active Directory 使用者和電腦] 中看起來是單一值。描述屬性是其中一個範例。`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

在此運算式中，如果屬性有值，我們就取出屬性中的第一個項目 (Item)、移除開頭和結尾的空格 (Trim)，然後保留字串中的前 448 個字元 (Left)。

### 請勿傳送屬性
如需本節案例的背景，請參閱[控制屬性流程程序](#control-the-attribute-flow-process)。

有兩種方式可防止傳送屬性。第一個可在安裝精靈中使用，而且可讓您[移除選取的屬性](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)。如果您先前不曾同步處理該屬性，則適用這個選項。不過，如果您已開始同步處理這個屬性，且稍後利用這個功能來移除它，則同步處理引擎會停止管理屬性，而現有的值會留在 Azure AD 中。

如果您想要移除屬性的值並確定未來不會傳送它，就需要改為建立自訂規則。

在 Fabrikam，我們在同步處理到雲端的屬性中發現了一些不應該存在的屬性。我們想要確定這些屬性會從 Azure AD 中移除。![不正確的擴充屬性](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- 建立新的輸入同步處理規則並填入說明 ![說明](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- 建立類型為 **Expression** 且來源為 **AuthoritativeNull** 的屬性流程。即使優先順序較低的同步處理規則會嘗試填入值，常值 **AuthoritativeNull** 還是會指出 MV 中的值應該是空的。 ![擴充屬性的轉換](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- 儲存同步處理規則。啟動 [同步處理服務]、尋找連接器、選取 [執行] 和 [完整同步處理]。此步驟會重新計算所有屬性流程。
- 藉由搜尋連接器空間，來確認即將匯出所需的變更。![分段刪除](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## 進階概念

### 控制屬性流程程序
將多個輸入同步處理規則設定為貢獻於相同的 Metaverse 屬性時，則會使用優先順序來決定獲得採用的規則。具有最高優先順序 (最小數值) 的同步處理規則將會貢獻值。輸出規則的情況一樣。具有最高優先順序的同步處理規則會獲得採用，並貢獻值給已連接的目錄。

在某些情況下，同步處理規則應該判斷其他規則的行為方式，而不是貢獻值。在此情況下會使用一些特殊的常值。

對於輸入同步處理規則，常值 **NULL** 可用來指出流程沒有要貢獻的值。具有較低優先順序的其他規則可以貢獻一個值。如果沒有規則提供值，則會移除 Metaverse 屬性。對於輸出規則，如果 **NULL** 是處理完所有同步處理規則後的最終值，則會在已連接的目錄中移除此值。

常值 **AuthoritativeNull** 類似 **NULL**，但差別在於沒有較低優先順序的規則可以貢獻值。

屬性流程也可以使用 **IgnoreThisFlow**。意思上類似於 NULL，表示沒有要貢獻的項目。差別在於它不會移除目標中已經存在的值。好像屬性流程未曾出現一樣。

下列是一個範例：

在 Out to AD - User Exchange hybrid 中，可以找到下列流程︰`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)` 此運算式的意思是︰如果使用者信箱位於 Azure AD 中，則將屬性從 Azure AD 傳送至 AD。如果並非如此，請勿將任何項目送回 Active Directory。在此情況下，它會在 AD 中保留現有的值。

### ImportedValue
函式 ImportedValue 與其他所有函式都不同，其屬性名稱必須以引號 (而非方括號) 括住：`ImportedValue("proxyAddresses")`。

通常在同步處理期間，屬性會使用預期的值，即使它尚未匯出或在匯出期間 (「協定塔的頂端」) 收到錯誤。輸入同步處理會假設尚未到達已連接目錄的屬性最後還是會到達。在某些情況下，請務必只同步處理已連接目錄所確認的值 (「全像圖和差異匯入協定塔」)。

在現成可用的同步處理規則 In from AD – User Common from Exchange 中可以找到此函式的範例。在混合式 Exchange 中，由 Exchange Online 新增的值只有在確認已成功匯出該值時，才能同步處理：`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## 後續步驟

深入了解用於屬性流程的[宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)。

深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)組態。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0824_2016-->