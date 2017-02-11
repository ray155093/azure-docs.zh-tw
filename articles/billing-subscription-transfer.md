---
title: "轉移 Azure 訂用帳戶的擁有權 | Microsoft Docs"
description: "如何將 Azure 訂用帳戶轉移到另一位使用者和關於程序的一些常見問題集 (FAQ)"
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: f7b097883ca0340a3af83fc9531acfe571295994
ms.openlocfilehash: c0323a63676a784660146f84a7813cf2cd5de7b0


---
# <a name="transferring-ownership-of-an-azure-subscription"></a>轉移 Azure 訂用帳戶的擁有權

您可以針對隨用隨付、MSDN、行動套件或 BizSpark 訂用帳戶，輕鬆地將您的訂用帳戶移轉給另一位使用者。 您可以在所擁有的任何隨用隨付、MSDN、行動套件或 BizSpark 訂用帳戶上變更帳戶管理員，而不論您是在哪個國家/地區進行操作。 我們也支援移轉這些訂用帳戶類型的 Azure Marketplace 購買。 

在下列情況下，您可能想要轉移 Azure 訂用帳戶的擁有權：

* 需要將 Azure 訂用帳戶的帳單擁有權交給其他人。
* 想要變更用來註冊 Azure 的帳戶。 或許您是使用 Microsoft 帳戶，但原本是想要使用您的公司或學校帳戶。
* 想要將您的 Azure 訂用帳戶移到另一個目錄。
* Azure 和 Office 365 在不同的租用戶中，並想要合併。

若要將您的訂用帳戶變更至不同的優惠，請參閱[切換至不同的 Azure 訂用帳戶優惠](billing-how-to-switch-azure-offer.md)。 

## <a name="how-to-transfer-ownership-of-an-azure-subscription"></a>如何轉移 Azure 訂用帳戶的擁有權
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. 登入 <https://account.windowsazure.com/Subscriptions>。 您必須是帳戶管理員，才能執行擁有權轉移。 如需有關如何找出訂用帳戶管理員的詳細資訊，請參閱 [常見問題集](#faq)。
2. 選取要移轉的訂用帳戶。
3. 按一下 [轉移訂用帳戶]  選項。

   ![Azure 帳戶的訂用帳戶索引標籤](./media/billing-subscription-transfer/image1.png)
4. 依照提示來指定接受者。

   ![移轉訂用帳戶對話方塊](./media/billing-subscription-transfer/image2.PNG)
5. 接受者會自動收到含有接受連結的電子郵件。

   ![給接受者的訂用帳戶移轉電子郵件](./media/billing-subscription-transfer/image3.png)
6. 接受者按一下連結並遵循指示進行，包括輸入他們的付款資訊。

   ![第一個訂用帳戶移轉網頁](./media/billing-subscription-transfer/image4.png)

   ![第二個訂用帳戶移轉網頁](./media/billing-subscription-transfer/image5.png)
7. 成功！ 現在已移轉訂用帳戶。

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>常見問題集 (FAQ)
* <a name="whoisaa"></a>**訂用帳戶的帳戶管理員是誰？**

  帳戶管理員就是註冊或購買 Azure 訂用帳戶的人員。 他們已獲得授權存取[帳戶中心](https://account.windowsazure.com/Home/Index)並可執行各種管理工作，像是建立訂用帳戶、取消訂用帳戶、變更訂用帳戶的計費方式，或變更服務管理員。 如果您不確定誰是訂用帳戶的帳戶管理員，請使用下列步驟來找出帳戶管理員。

  1. 登入 [Azure 入口網站](https://portal.azure.com)。
  2. 在 [中樞] 功能表中，選取 [訂用帳戶] 。
  3. 選取您想要檢查的訂用帳戶，然後查看 [設定]。
  4. 選取 [屬性] 。 該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員]  方塊中。  
* **訂用帳戶移轉會造成服務中斷嗎？**

  不會影響服務。 移轉訂用帳戶會在目前帳戶管理員之下取消訂用帳戶，並在接受者的帳戶之下建立訂用帳戶。 新的訂用帳戶與基礎 Azure 服務相關聯。 訂用帳戶 ID 維持不變。
* **如何使用此程序來變更訂用帳戶的目錄？**   
  Azure 訂用帳戶建立在帳戶管理員所屬的目錄中。 若要變更目錄，請將訂用帳戶轉移到目標目錄中的使用者帳戶。 當使用者完成步驟接受轉移時，訂用帳戶就會自動移至目標目錄。
* **如果我接管另一個組織的訂用帳戶帳單擁有權，他們可以繼續存取我的資源嗎？**

  如果訂用帳戶轉移到另一個租用戶，與先前的租用戶相關聯的使用者會失去訂用帳戶的存取權。 即使使用者不再是服務管理員或共同管理員，他們仍可能透過其他安全性機制來存取訂用帳戶。 其中包含：

  * 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱 [建立和上傳 Azure 的管理憑證](https://msdn.microsoft.com/library/azure/gg551722.aspx)
  * 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](storage/storage-create-storage-account.md)
  * 服務 (例如 Azure 虛擬機器) 的遠端存取認證

  這不是完整的清單。 如果接受者需要限制對其資源的存取權，則應該考慮更新與服務相關聯的任何密碼。 大部分資源可以更新如下：

  1. 移至 [Azure 入口網站](https://portal.azure.com)。
  2. 在 [中樞] 功能表中，選取 [所有資源]。
  3. 選取資源。 
  4. 在資源刀鋒視窗中，按一下 [設定] 。 您可以在這裡檢視並更新現有的密碼。
* **如果我在計費週期中途移轉訂用帳戶，接受者需要支付整個計費週期嗎？**

  傳送者負責支付移轉完成當時所報告的任何使用量。 接受者負責支付移傳以後所報告的使用量。 可能有某些使用量是在移轉之前發生，但在之後才報告。 使用量會包含在接受者的帳單中。
* **接受者可存取使用量及帳單記錄嗎？**

  接受者可用的資訊只有最新帳單的金額，而如果訂用帳戶是在產生第一份帳單之前移轉，則可使用目前餘額。 其餘的使用量及帳單記錄不會隨著訂用帳戶一起移轉。
* **移轉期間可以變更優惠嗎？**

  優惠必須維持不變。 若要變更優惠，您必須 [連絡支援服務](http://go.microsoft.com/fwlink/?LinkID=619338)。
* **我可以將訂用帳戶轉移給另一個國家/地區的使用者帳戶嗎？**

  否，不支援將訂用帳戶轉移給另一個國家/地區的使用者帳戶。 接受者的使用者帳戶必須在相同的國家/地區。
* **接受者可以使用不同的付款方法嗎？**

  是。 但訂用帳戶帳單記錄會拆成兩個帳戶。  

* **Azure 訂用帳戶轉移後，付款方法會受到影響嗎？**

若要接受訂用帳戶轉移，必須提供信用卡或類似的訂用帳戶付款方法。 例如，如果 Bob 將訂用帳戶轉移給 Jane，而 Jane 接受傳移，則 Jane 必須也要提供她用來支付訂用帳戶的付款方法。 完成轉移後，Bob 轉移給 Jane 的訂用帳戶就不會再向 Bob 收費。

* **如何將我的 Azure 訂用帳戶資料與服務移轉至新的訂用帳戶？**

  請參閱[將資源移動到新的資源群組或訂用帳戶](./azure-resource-manager/resource-group-move-resources.md)。

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>接受訂用帳戶擁有權後的後續步驟
1. 您現在是帳戶管理員。 請檢視並更新服務管理員和共同管理員。 移至 [設定]，管理 [Azure 傳統入口網站](https://manage.windowsazure.com) 中的管理員。 [深入了解](http://go.microsoft.com/fwlink/?LinkID=533293)。
2. 您也可以針對訂用帳戶和服務使用角色型存取控制 (RBAC)。 請造訪 [Azure 入口網站](https://portal.azure.com) [深入了解 RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. 更新與此訂用帳戶服務相關聯的認證。 其中包含：
   * 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱 [Create and upload a management certificate for Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
   * 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](storage/storage-create-storage-account.md)
   * 服務 (例如 Azure 虛擬機器) 的遠端存取認證
4. 若要更新此訂用帳戶的計費警示，請至 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)。 [深入了解](http://go.microsoft.com/fwlink/?LinkID=533292)
5. 如果您正與合作夥伴協力作業，請考慮更新此訂用帳戶的合作夥伴 ID。 您可以在 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)更新合作夥伴識別碼。


## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。 





<!--HONumber=Dec16_HO2-->


