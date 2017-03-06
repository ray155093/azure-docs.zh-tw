---
title: "DocumentDB 帳戶的 MongoDB 連接字串 | Microsoft Docs"
description: "了解如何使用 MongoDB 連接字串讓 MongoDB 應用程式連線到 Azure DocumentDB 帳戶。"
keywords: "mongodb 連接字串"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 218d41ac352f01a7e03b5b8a1c45e1bf6d5ace28
ms.openlocfilehash: a3b1289d52207448cebc628466492371d1bf6519
ms.lasthandoff: 01/11/2017


---

# <a name="connect-a-mongodb-app-to-a-documentdb-account-using-a-mongodb-connection-string"></a>使用 MongoDB 連接字串將 MongoDB 應用程式連線到 DocumentDB 帳戶
了解如何使用 MongoDB 連接字串讓 MongoDB 應用程式連線到 Azure DocumentDB 帳戶。 讓 MongoDB 應用程式連線到 Azure DocumentDB 資料庫，您就可以使用 DocumentDB 資料庫做為 MongoDB 應用程式的資料存放區。 

本教學課程提供兩種方式來擷取連接字串資訊︰

- [快速啟動方法](#QuickStartConnection)，用來搭配 .NET、Node.js、MongoDB 殼層、Java 和 Python 的驅動程式。
- [自訂連接字串方法](#GetCustomConnection)，用來搭配其他驅動程式。

## <a name="prerequisites"></a>必要條件

- 一個 Azure 帳戶。 如果您沒有 Azure 帳戶，可以立即建立一個[免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 
- 一個 DocumentDB 帳戶。 如需指示，請參閱[建立用於 MongoDB 應用程式的 DocumentDB 帳戶](documentdb-create-mongodb-account.md)。

## <a name="a-idquickstartconnectionaget-the-mongodb-connection-string-using-the-quick-start"></a><a id="QuickStartConnection"></a>使用快速啟動取得 MongoDB 連接字串
1. 在網際網路瀏覽器中，登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [NoSQL (DocumentDB)] 刀鋒視窗中，選取 [具有 MongoDB 通訊協定支援的 DocumentDB 帳戶]。 
3. 在帳戶刀鋒視窗的 [左導覽] 列中，按一下 [快速啟動]。 
4. 選擇您的平台 (*.NET 驅動程式*、*Node.js 驅動程式*、*MongoDB 殼層*、*Java 驅動程式*、*Python 驅動程式*)。 如果您沒有看到您的驅動程式或工具被列出，別擔心，我們會持續加入更多連線程式碼片段。 請在下面加入您想要看到何種內容的意見，並且閱讀[取得帳戶的連接字串資訊](#GetCustomConnection)以了解如何製作您自己的連線。
5. 將程式碼片段複製和貼上您的 MongoDB 應用程式，如此就準備就緒。

    ![快速啟動刀鋒視窗的螢幕擷取畫面](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a name="a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize"></a><a id="GetCustomConnection"></a> 取得 MongoDB 連接字串以自訂
1. 在網際網路瀏覽器中，登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [NoSQL (DocumentDB)] 刀鋒視窗中，選取 [具有 MongoDB 通訊協定支援的 DocumentDB 帳戶]。 
3. 在帳戶刀鋒視窗的 [左導覽] 列中，按一下 [連接字串]。 
4. [連接字串資訊]  刀鋒視窗隨即開啟，並且顯示使用 MongoDB 的驅動程式連接至帳戶所需的所有資訊，包括預先建構的連接字串。

    ![[連接字串] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>連接字串需求
> [!Important]
> DocumentDB 有嚴格的安全性需求和標準。 DocumentDB 帳戶需要驗證和透過 **SSL** 的安全通訊。
>
>

請務必注意，DocumentDB 支援標準 MongoDB 連接字串 URI 格式，有幾個特定需求︰DocumentDB 帳戶需要驗證和透過 SSL 的安全通訊。  因此，連接字串格式為：

    mongodb://username:password@host:port/[database]?ssl=true

[連接字串] 刀鋒視窗中此字串的值可用的位置如上所述。

* 使用者名稱 (必要)
  * DocumentDB 帳戶名稱
* 密碼 (必要)
  * DocumentDB 帳戶密碼
* 主機 (必要)
  * DocumentDB 帳戶的 FQDN
* 連接埠 (必要)
  * 10250
* 資料庫 (選擇性)
  * 連線時所使用的預設資料庫 (如果未提供資料庫，則預設資料庫是 "test")
* ssl=true (必要)

例如，請考慮上面的 [連接字串資訊] 中顯示的帳戶。  有效的連接字串為：

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>後續步驟
* 了解如何 [使用 MongoChef](documentdb-mongodb-mongochef.md) 和具有 MongoDB 通訊協定支援的 DocumentDB 帳戶。
* 探索具有 MongoDB 通訊協定支援的 DocumentDB 的 [範例](documentdb-mongodb-samples.md)。

