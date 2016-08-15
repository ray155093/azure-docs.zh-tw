<properties
   pageTitle="管理 Azure Marketplace 上的虛擬機器映像 | Microsoft Azure"
   description="如何在初始發佈後管理 Azure Marketplace 上的虛擬機器映像的詳細指南。"
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="hascipio;"/>

# 關於 Azure Marketplace 中的虛擬機器優惠的後期製作指南

本文說明如何更新 Azure Marketplace 中已上線的虛擬機器優惠。同時引導您將一或多個新的 SKU 加入至現有的優惠，以及從 Azure Marketplace 中移除上線的虛擬機器優惠或 SKU。

一旦供應項目/SKU於 [Azure 入口網站](http://portal.azure.com)預備完成，您就無法變更以下欄位：

- **供應項目識別碼︰**[發佈入口網站 -> 虛擬機器 -> 選取您的供應項目 -> VM 映像索引標籤 -> 供應項目識別碼]
- **SKU 識別碼︰**[發佈入口網站 -> 虛擬機器 -> 選取您的供應項目 -> SKU 索引標籤 -> 新增 SKU]
- **發行者命名空間︰** [發佈入口網站 -> 虛擬機器 -> 逐步解說索引標籤 -> 告知我們您的公司 (可見於「步驟 2：註冊您的公司」) -> 發行者命名空間 -> 命名空間]

一旦供應項目/SKU 列於 [Azure Marketplace](http://azure.microsoft.com/marketplace)，您就無法變更以下欄位：

- **供應項目識別碼︰**[發佈入口網站 -> 虛擬機器 -> 選取您的供應項目 -> VM 映像索引標籤 -> 供應項目識別碼]
- **SKU 識別碼︰**[發佈入口網站 -> 虛擬機器 -> 選取您的供應項目 -> SKU 索引標籤 -> 新增 SKU]
- **發行者命名空間︰**[發佈入口網站 -> 虛擬機器 -> 逐步解說索引標籤 -> 告知我們您的公司 (可見於「步驟 2：註冊」) -> 發行者命名空間 -> 命名空間]
- **連接埠** [發佈入口網站 -> 虛擬機器 -> 選取您的供應項目 -> VM 映像索引標籤 -> 開啟連接埠]
- **已列出 SKU 的價格變更**
- **已列出 SKU 的計費模式變更**
- **移除已列出 SKU 的計費區域**
- **變更已列出 SKU 的資料磁碟計數**



## 1\.如何更新 SKU 的技術詳細資料

您可以遵循下列步驟來新增版本到已列出的 SKU，然後重新發佈供應項目︰

1. 登入[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [VM 映像] 索引標籤。
4. 從 [VM 映像] 索引標籤的 [SKU] 區段中，找出您想要更新的 SKU。
5. 之後加入新的 SKU 版本號碼，然後按一下 **"+"** 按鈕。新版本應該是 X.Y.Z 格式，其中 X、Y、Z 是整數。只能以累加方式變更版本。
6. 在 [OS VHD URL] 方塊中，新增針對作業系統 VHD 所建立的共用存取簽章 URI，然後儲存變更。

    >[AZURE.IMPORTANT] 您不能增加/減少已列出的 SKU 的資料磁碟計數。在此案例中，您需要建立新的 SKU。如需詳細請指引，請參閱第 3 小節[如何在已列出的供應項目下新增 SKU](#3-how-to-add-a-new-sku-under-a-live-offer)。

7. 完成變更之後，瀏覽至 [發佈] 索引標籤，然後按一下 [推送至預備環境] 按鈕。如需在預備環境中測試供應項目的詳細指引，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
8. 在預備環境中測試供應項目之後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## 2\.如何更新優惠或 SKU 的非技術性詳細資料

您可以更新 Azure Marketplace 中已上線的優惠或 SKU 的非技術性 (行銷、法律、支援、類別) 詳細資料。

### 2\.1 更新優惠描述和標誌

您可以遵循下列步驟來更新優惠詳細資料，然後重新發佈優惠︰

1. 登入[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [行銷] 索引標籤。
4. 按一下 [英文 (美國)] 按鈕。
5. 從左側功能表，按一下 [詳細資料] 索引標籤。在 [詳細資料] 索引標籤的 [描述] 區段下，您可以更新供應項目標題、供應項目摘要、供應項目完整摘要，然後儲存變更。

    >[AZURE.NOTE] 更新 SKU 詳細資料時，請注意下列事項。**請不要在供應項目描述和 SKU 描述下方輸入重複文字。請不要在 SKU 標題和優惠完整摘要下方輸入重複文字。請不要在 SKU 標題和優惠摘要下方輸入重複文字。**

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)

6. 在 [詳細資料] 索引標籤的 [標誌] 區段下，您可以更新標誌。不過，請確定標誌遵循 [Azure Marketplace 指導方針](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (請參閱「步驟 1︰提供 Marketplace 行銷內容 -> 詳細資料 -> Azure Marketplace 標誌指導方針」一節)。

    >[AZURE.NOTE] 主圖圖示是選擇性。您可以選擇不上傳主圖圖示。然而，主圖圖示一旦上傳，就無法從發佈入口網站中刪除。在此情況下，您必須遵循[主圖圖示指導方針](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (請參閱「步驟 1︰提供 Marketplace 行銷內容 -> 詳細資料 -> 主圖標誌橫幅的其他指導方針」一節)。

7. 完成變更之後，瀏覽至 [發佈] 索引標籤，然後按一下 [推送至預備環境] 按鈕。如需在預備環境中測試供應項目的詳細指引，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)。
8. 在預備環境中測試供應項目之後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### 2\.2.更新 SKU 描述

您可以遵循下列步驟來更新 SKU 詳細資料，然後重新發佈優惠︰

1. 登入[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [行銷] 索引標籤。
4. 按一下 [英文 (美國)] 按鈕。
5. 從左側功能表，按一下 [方案] 索引標籤。在 [方案] 索引標籤的 [SKU] 區段下，您可以更新 SKU 標題、SKU 摘要和 SKU 描述詳細資料，然後儲存變更。

    >[AZURE.NOTE] 更新 SKU 詳細資料時，請注意下列事項。**請不要在供應項目描述和 SKU 描述下方輸入重複文字。請不要在 SKU 標題和優惠完整摘要下方輸入重複文字。請不要在 SKU 標題和優惠摘要下方輸入重複文字。**

6. 完成變更之後，瀏覽至 [發佈] 索引標籤，然後按一下 [推送至預備環境] 按鈕。如需在預備環境中測試優惠的詳細指引，請參閱此連結
7. 在預備環境中測試供應項目之後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### 2\.3 變更現有連結或加入新連結

您可以遵循下列步驟來變更現有連結或加入新連結，然後重新發佈優惠︰

1. 登入[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [行銷] 索引標籤。
4. 按一下 [英文 (美國)] 按鈕。
5. 從左側功能表，按一下 [連結] 索引標籤。
6. 如果您想要新增連結，請在 [連結] 區段下按一下 [加入連結] 按鈕。[加入連結] 對話方塊隨即開啟。在此對話方塊中，您可以新增連結標題和 URL 欄位，然後儲存變更。您可以輸入任何包含資訊來協助客戶的連結。
7. 如果您想要更新或刪除現有的連結，請選取適當的連結，然後依需要按一下編輯按鈕或刪除按鈕。

    >[AZURE.NOTE] 請確定您在此區段中輸入的連結可正常運作，因為在生產環境要求處理期間會驗證這些連結。

8. 完成變更之後，瀏覽至 [發佈] 索引標籤，然後按一下 [推送至預備環境] 按鈕。如需在預備環境中測試供應項目的詳細指引，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)。
9. 在預備環境中測試供應項目之後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### 2\.4 變更現有範例影像或加入新範例影像

您可以遵循下列步驟來變更現有範例影像或加入新範例影像，然後重新發佈優惠︰

>[AZURE.NOTE] 只有一個範例影像會顯示在 [https://portal.azure.com](https://portal.azure.com)。

1. 登入[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [行銷] 索引標籤。
4. 按一下 [英文 (美國)] 按鈕。
5. 從左側功能表，按一下 [範例影像] 索引標籤。
6. 如果您想要新增範例影像，請在 [範例影像] 區段下按一下 [上傳新影像] 按鈕，然後儲存變更。

    >[AZURE.NOTE] 加入範例影像是選擇性步驟。

7. 如果您想要更新或刪除現有範例影像，請找出適當的範例影像，然後依需要按一下 [取代影像] 按鈕或刪除按鈕。

8. 完成變更之後，瀏覽至 [發佈] 索引標籤，然後按一下 [推送至預備環境] 按鈕。如需在預備環境中測試供應項目的詳細指引，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)。
9. 在預備環境中測試供應項目之後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### 2\.5 更新法律聲明內容

您可以遵循下列步驟來更新法律聲明內容，然後重新發佈優惠︰

1. 登入[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [行銷] 索引標籤。
4. 按一下 [英文 (美國)] 按鈕。
5. 從左側功能表，按一下 [法律聲明] 索引標籤。在 [法律聲明] 索引標籤下，您可以更新使用原則/條款。在 [使用條款] 文字方塊中輸入或貼上原則/條款，然後儲存變更。
6. 法律使用條款的字元限制為 1,000,000 個字元。
7. 完成變更之後，瀏覽至 [發佈] 索引標籤，然後按一下 [推送至預備環境] 按鈕。如需在預備環境中測試供應項目的詳細指引，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
8. 在預備環境中測試供應項目之後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### 2\.6 更新支援資訊

您可以遵循下列步驟來更新支援資訊，然後重新發佈優惠︰

1. 登入[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [支援] 索引標籤。
4. 在 [支援] 索引標籤的 [工程連絡人] 區段下，您可以更新連絡人詳細資料。這些詳細資料僅用於合作夥伴與 Microsoft 之間的內部通訊。
5. 在 [支援] 索引標籤的 [客戶支援] 區段下，您可以更新支援連絡人詳細資料，例如 [名稱]、[電子郵件]、[電話] 和 [支援 URL]。這些詳細資料僅用於合作夥伴與 Microsoft 之間的內部通訊。

    >[AZURE.NOTE] 如果您只想要提供電子郵件支援，請在 [客戶支援] 區段下提供虛設的電話號碼。在此情況下，將改為使用您提供的電子郵件。

6. 完成變更之後，瀏覽至 [發佈] 索引標籤，然後按一下 [推送至預備環境] 按鈕。如需在預備環境中測試供應項目的詳細指引，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
7. 在預備環境中測試供應項目之後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### 2\.7 更新類別

您可以遵循下列步驟來更新優惠的類別區段，然後重新發佈優惠︰

1. 登入[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [類別] 索引標籤。
4. 在 [類別] 區段下，您可以更新供應項目的類別，然後儲存變更。您最多可以為 Azure Marketplace 資源庫選取五個類別。
5. 完成變更之後，瀏覽至 [發佈] 索引標籤，然後按一下 [推送至預備環境] 按鈕。如需在預備環境中測試供應項目的詳細指引，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
6. 在預備環境中測試供應項目之後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## 3\.如何在已列出的供應項目下新增 SKU

您可以遵循下列步驟在上線優惠下新增 SKU︰

1. 登入[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [SKU] 索引標籤。然後按一下 [加入 SKU] 按鈕。新的對話方塊隨即開啟。輸入小寫的 SKU 識別碼。如果您想要以 BYOL 計費模式發佈新的 SKU，請核取自備計費模式 (BYOL) 核取方塊。否則，請取消核取 BYOL 核取方塊。然後，按一下對話方塊中的勾號以建立新的 SKU。如果您沒有為新的 SKU 選擇 BYOL 計費模式，則新 SKU 的計費模式會自動設為「每小時」。如果您想要啟用「每小時」計費模式 30 天免費試用，請按一下 [有免費試用可用嗎?] 的 [一個月] 選項。否則，請選取 [無試用]。[附註：僅當您在建立新 SKU 時「沒有」在對話方塊中選取 BYOL，[有免費試用可用嗎?] 選項才會出現。]

    >[AZURE.IMPORTANT] 「只有」當您獲准在 Azure Marketplace 中發佈方案範本優惠時，[從 Marketplace 隱藏此 SKU，因為應該永遠透過方案範本購買它] 選項才應該標示為 [是]。否則，這個選項應該永遠標示為 [否]。

4. 現在，從左側功能表按一下 [VM 映像] 索引標籤，找出您已建立的新 SKU。
5. 若要設定新的 SKU，請參閱此[連結](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image)的「步驟 5」，以取得指導方針。
6. 若要加入新 SKU 的行銷資料，請參閱此[連結](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)中的步驟 1︰提供 Marketplace 行銷內容 -> 詳細資料 -> 第 2 到 第 5 點。
7. 若要加入新的 SKU 的價格資訊，請參閱 2.1 節。設定此[連結](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)的 VM 價格
8. 完成變更之後，瀏覽至 [發佈] 索引標籤，然後按一下 [推送至預備環境] 按鈕。如需在預備環境中測試供應項目的詳細指引，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
9. 在預備環境中測試供應項目之後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## 4\.如何變更已列出 SKU 的資料磁碟計數

您不能增加/減少已列出的 SKU 的資料磁碟計數。在此案例中，您需要建立新的 SKU。如需詳細請指引，請參閱第 3 小節[如何在上線供應項目下新增 SKU](#3-how-to-add-a-new-sku-under-a-live-offer)。

## 5\.如何從 Azure Marketplace 刪除已列出的供應項目

要求移除上線優惠時，有幾方面需要注意。請遵循下列步驟來取得支援小組的指引，以從 Azure Marketplace 移除已列出的供應項目︰

1.	使用此[連結](https://support.microsoft.com/zh-TW/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=zh-TW&supportregion=zh-TW&pesid=15635&ccsid=635993707583706681)提出支援票證。
2.	選取 [管理供應項目] 作為 [問題類型]，選取 [修改已在生產中的供應項目和/或 SKU] 作為 [類別]。
3.	提交要求

支援小組會引導您完成優惠/SKU 刪除程序。

>[AZURE.NOTE] 對於處於 [草稿] 狀態的供應項目 (亦即不是 [預備] 或 [生產])，您永遠可以按一下 [歷程記錄] 索引標籤下的 [捨棄草稿] 按鈕來刪除它。

## 6\.如何從 Azure Marketplace 刪除已列出的 SKU

您可以遵循下列步驟來從 Azure Marketplace 刪除已列出的 SKU︰

1. 登入[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側窗格，按一下 [SKU] 索引標籤。
4. 選取您要刪除的 SKU，然後針對該 SKU 按一下 [刪除] 按鈕。
5. 完成後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。
6. 在 Azure Marketplace 重新發佈供應項目之後，會從 Azure Marketplace 和 Azure 入口網站刪除 SKU。

## 7\.如何從 Azure Marketplace 刪除已列出的 SKU 目前的版本

您可以遵循下列步驟來從 Azure Marketplace 刪除已列出的 SKU 目前的版本︰此程序完成之後，SKU 將會回復為先前的版本。

1. 登入[發佈入口網站](https://publish.windowsazure.com)。
2.	瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3.	從左側窗格，按一下 [VM 映像] 索引標籤。
4.	選取您要刪除其目前版本的 SKU，然後針對該版本按一下 [刪除] 按鈕。
5.	完成後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。
6.	在 Azure Marketplace 重新發佈供應項目之後，會從 Azure Marketplace 和 Azure 入口網站刪除已列出的 SKU 的目前版本。SKU 將會回復為先前的版本。

## 8\.如何將列出價格還原成生產環境值
我已變更已列出的 SKU 的價格 (或已移除已列出的 SKU 的計費區域)。由於它在 Azure Marketplace 中不受支援，我想將我的變更還原成生產環境值。我該怎麼做？

請依照下列步驟執行：

1. 登入[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [價格] 索引標籤。
4. 在 [價格] 索引標籤下，選取您想要重設其價格的區域。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)

5. 對於計費模式是每小時的 SKU，請重設所有核心的價格，因為它們處於所選區域的生產環境。對於計費模式是 BYOL 的 SKU，請在 [外部授權的 (BYOL) SKU 可用性] 區段下方核取該 SKU 的核取方塊，以在該區域公開發行 SKU (請參閱以下螢幕擷取畫面)。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)

6. 現在，按一下 [依據美國價格自動訂定其他市場價格] 按鈕。

    >[AZURE.NOTE] 視選取的區域而定，按鈕的標籤可能會不同。由於我們在建立這份文件時選取美國，因此以下螢幕擷取畫面中的按鈕標示為 [依據美國價格自動訂定其他市場價格]。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)

7. 自動定價精靈隨即開啟。第 1 頁會顯示基本市場的選項。選擇您的區域，然後按一下 **"->"** 按鈕移至下一頁。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)

8. 選取核心和方案的選項將顯示在第 2 頁。選取所需的方案和核心，然後按一下 [->] 按鈕。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)

9. 第 3 頁會顯示市場/區域。按一下 [全部切換] 按鈕以選取所有區域，或手動核取區域的方塊。按一下 [->] 按鈕移至下一頁。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)

10. 第 4 頁會顯示匯率。按一下 [完成] 按鈕以完成步驟。精靈會根據您的選擇重設價格。

11. 現在，瀏覽至 [價格] 索引標籤，然後按一下 [檢視摘要和變更] 按鈕。在 [檢視版本] 區段中選取 [草稿]，在 [比較對象] 區段中選取 [生產環境] (請參閱以下螢幕擷取畫面)。如果看不出價格差異，即表示價格已順利還原成生產環境值。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)

12. 完成變更之後，瀏覽至 [發佈] 索引標籤，然後按一下 [推送至預備環境] 按鈕。如需在預備環境中測試供應項目的詳細指引，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
13. 在預備環境中測試供應項目之後，瀏覽至發佈入口網站的 [發佈] 索引標籤，按一下 [要求核准以推送至生產環境] 按鈕，將供應項目重新發佈到 Azure Marketplace。

## 9\.如何將計費模式還原成生產環境值
我已變更已列出的 SKU 的計費模式。由於它在 Azure Marketplace 中不受支援，我想將我的變更還原成生產環境值。我該怎麼做？

請依照下列步驟執行：

1. 登入[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [SKU] 索引標籤。
4. 按一下 [編輯] 按鈕以還原計費模式。隨即開啟一個視窗。視情況核取或取消核取 [計費與授權於 Azure 外部進行 (亦即自備授權)] 核取方塊。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)

5. 完成後，請參閱本文件的問題 8 的答案來回復價格。
6. 接著瀏覽至發佈入口網站中的 [發佈] 索引標籤，將供應項目推送至預備環境進行測試。完成供應項目的測試後，按一下 [要求核准以推送至生產環境] 按鈕以在 Azure Marketplace 重新發佈您的供應項目。

## 10\.如何將已列出的 SKU 的可見性設定還原成生產環境值

請依照下列步驟執行：

1. 登入[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [虛擬機器] 索引標籤並選取您的供應項目。
3. 從左側功能表，按一下 [SKU] 索引標籤。
4. 選取您的 SKU，將 SKU 的可見性設定還原成生產環境值。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)

5. 在變更完成後，按一下 [要求核准以推送至生產環境] 按鈕以在 Azure Marketplace 重新發佈您的供應項目。

## 另請參閱
- [使用者入門：如何將供應項目發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)
- [了解賣方 Insights 報告](marketplace-publishing-report-seller-insights.md)
- [了解付款報告](marketplace-publishing-report-payout.md)
- [如何變更雲端解決方案提供者轉售商獎勵](marketplace-publishing-csp-incentive.md)
- [疑難排解 Marketplace 中常見的發佈問題](marketplace-publishing-support-common-issues.md)
- [以發佈者身分取得支援](marketplace-publishing-get-publisher-support.md)
- [建立內部部署 VM 映像](marketplace-publishing-vm-image-creation-on-premise.md)
- [在 Azure Preview 入口網站中建立執行 Windows 的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

<!---HONumber=AcomDC_0803_2016-->