---
title: "搭配 Azure DocumentDB 使用 mongoimport 和 mongorestore | Microsoft Docs"
description: "了解如何使用 mongoimport 和 mongorestore 來匯入資料至 DocumentDB：適用於 MongoDB 的 API 帳戶"
keywords: mongoimport, mongorestore
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
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 2af8691326550c631e6668890cb0d6b893fa7740
ms.lasthandoff: 03/08/2017


---
# <a name="migrate-data-to-documentdb-by-using-mongoimport-and-mongorestore"></a>使用 mongoimport 和 mongorestore，將資料移轉至 DocumentDB
> [!div class="op_single_selector"]
> * [匯入至 DocumentDB](documentdb-import-data.md)
> * [匯入適用於 MongoDB 的 API](documentdb-mongodb-migrate.md)
>
>

若要將資料移轉到 Azure DocumentDB：適用於 MongoDB 的 API 帳戶，您必須︰

* 從 [MongoDB 下載中心](https://www.mongodb.com/download-center)下載 *mongoimport.exe* 或*mongorestore.exe*。
* 使您的 [DocumentDB 支援 MongoDB 連接字串](documentdb-connect-mongodb-account.md)。

## <a name="before-you-begin"></a>開始之前

* 增加輸送量︰資料移轉的時間長短取決於您為集合設定的輸送量。 針對較大資料移轉，請務必增加輸送量。 完成移轉之後，再降低輸送量以節省成本。 如需在 [Azure 入口網站](https://portal.azure.com)增加輸送量的詳細資訊，請參閱 [DocumentDB 中的效能等級和定價層](documentdb-performance-levels.md)。

* 啟用 SSL：DocumentDB 有嚴格的安全性需求和標準。 與您的帳戶互動時，請務必啟用 SSL。 本文其他部分的程序包括如何針對 *mongoimport*和*mongorestore* 啟用 SSL。

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>找到您的連接字串資訊 (主機、連接埠、使用者名稱、密碼)

1. 在 [Azure 入口網站](https://portal.azure.com)的左窗格中，按一下 [NoSQL (DocumentDB)]。
2. 在 [訂用帳戶] 窗格中，選取您的帳戶名稱。
3. 在 [連接字串] 刀鋒視窗中，按一下 [連接字串]。  
右窗格中有您成功連接到您的帳戶所需的所有資訊。

    ![連接字串刀鋒視窗](./media/documentdb-mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-api-for-mongodb-with-mongoimport"></a>使用 mongoimport 將資料匯入適用於 MongoDB 的 API

若要將資料匯入您的 DocumentDB 帳戶，使用下列範本來執行匯入。 填寫專屬於您的帳戶的 [主機]、[使用者名稱]、[密碼] 值。  

範本：

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

範例：  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-api-for-mongodb-with-mongorestore"></a>使用 mongorestore 將資料匯入適用於 MongoDB 的 API

若要將資料還原至您的 DocumentDB 帳戶，使用下列範本來執行匯入。 填寫專屬於您的帳戶的 [主機]、[使用者名稱]、[密碼] 值。

範本：

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

範例：

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>後續步驟
* 如需詳細資訊，請參閱 [DocumentDB：適用於 MongoDB 的 API 範例](documentdb-mongodb-samples.md)。

