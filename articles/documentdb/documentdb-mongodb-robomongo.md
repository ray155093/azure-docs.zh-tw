---
title: "使用 Robomongo 和具有 MongoDB 通訊協定支援的 Azure DocumentDB 帳戶 | Microsoft Docs"
description: "了解如何搭配使用 Robomongo 與 MongoDB 的通訊協定支援的 DocumentDB 帳戶，現在可供預覽。"
keywords: robomongo
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a9d78597f708020eaec3fc03d8c8fc2652bddb50
ms.openlocfilehash: 6a7fb7052700bdeed5a3ca43da85304bba4230d8


---
# <a name="use-robomongo-with-a-documentdb-account-with-protocol-support-for-mongodb"></a>使用 Robomongo 和具有 MongoDB 通訊協定支援的 DocumentDB 帳戶
若要使用 Robomongo 連接至具有 MongoDB 的通訊協定支援的 Azure DocumentDB 帳戶，您必須：

* 下載並安裝 [Robomongo](https://robomongo.org/)
* 擁有具有 MongoDB 通訊協定支援的 DocumentDB 帳戶的 [連接字串](documentdb-connect-mongodb-account.md) 資訊。

## <a name="connect-using-robomongo"></a>使用 Robomongo 來連接
若要將具有 MongoDB 通訊協定支援的 DocumentDB 帳戶新增至 Robomongo MongoDB 連接，請執行下列步驟。

1. 使用 [這裡](documentdb-connect-mongodb-account.md)的資訊，擷取具有 MongoDB 連接資訊通訊協定支援的 DocumentDB。

    ![[連接字串] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-mongodb-robomongo/connectionstringblade.png)
2. 執行 *Robomongo.exe*

3. 按一下 [檔案] 下的 [連接] 按鈕來管理您的連接。 然後，按一下 [MongoDB 連接] 視窗中的 [建立]，將會開啟 [連接設定] 視窗。

4. 在 [連接設定] 視窗中，選擇名稱。 然後，從步驟 1 的連接資訊中，找出 [主機] 和 [連接埠]，分別輸入至 [位址] 和 [連接埠]。

    ![Robomongo 管理連線的螢幕擷取畫面](./media/documentdb-mongodb-robomongo/manageconnections.png)
5. 在 [驗證] 索引標籤上，按一下 [執行驗證]。 然後，輸入您的 [資料庫] (預設值是 *Admin*)、[使用者名稱] 和 [密碼]。
在步驟 1 的連接資訊中，可以找到 [使用者名稱] 和 [密碼]。

    ![Robomongo 驗證索引標籤的螢幕擷取畫面](./media/documentdb-mongodb-robomongo/authentication.png)
6. 在 [SSL] 索引標籤上，勾選 [使用 SSL 通訊協定]，然後將 [驗證方法] 變更為 [自我簽署憑證]。

    ![Robomongo SSL 索引標籤的螢幕擷取畫面](./media/documentdb-mongodb-robomongo/SSL.png)
7. 最後，按一下 [測試] 以確認您能夠連接，然後按一下 [儲存]。

## <a name="next-steps"></a>後續步驟
* 探索具有 MongoDB 通訊協定支援的 DocumentDB 的 [範例](documentdb-mongodb-samples.md)。



<!--HONumber=Dec16_HO1-->


