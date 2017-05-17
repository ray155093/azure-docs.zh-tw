---
title: "使用 Azure 入口網站建立和管理適用於 MySQL 的 Azure 資料庫伺服器 | Microsoft Docs"
description: "本文說明如何使用 Azure 入口網站，快速建立新的「適用於 MySQL 的 Azure 資料庫」伺服器和管理伺服器。"
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: cbf1bcd90c53a7221773c7dc7731b37cf762e953
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>使用 Azure 入口網站建立和管理適用於 MySQL 的 Azure 資料庫伺服器
本文說明如何使用 Azure 入口網站，快速建立新的「適用於 MySQL 的 Azure 資料庫」伺服器和管理伺服器。 伺服器管理包括檢視伺服器詳細資料和資料庫、重設密碼及刪除伺服器。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站
登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-an-azure-database-for-mysql-server"></a>建立適用於 MySQL 的 Azure 資料庫伺服器
請遵循下列步驟建立名為 "mysqlserver4demo" 的「適用於 MySQL 的 Azure 資料庫」伺服器

1- 按一下 Azure 入口網站左上角的 [新增] 按鈕。

2- 從 [新增] 頁面中選取 [資料庫]，然後從 [資料庫] 頁面中選取 [適用於 MySQL 的 Azure 資料庫]。

> 建立的「適用於 MySQL 的 Azure 資料庫」伺服器會有一組已定義的[計算和儲存體](./concepts-compute-unit-and-storage.md)資源。 建立的資料庫位於 Azure 資源群組和「適用於 MySQL 的 Azure 資料庫」伺服器中。

![建立新的伺服器](./media/howto-create-manage-server-portal/create-new-server.png)

3- 在「適用於 MySQL 的 Azure 資料庫」表單中填寫下列資訊︰

| **表單欄位** | **欄位描述** |
|----------------|-----------------------|
| *伺服器名稱* | azure-mysql (伺服器名稱是全域唯一的) |
| *訂用帳戶* | MySQLaaS (從下拉式清單選取) |
| *資源群組* | myresource (建立新的資源群組，或使用現有的資源群組) |
| 伺服器管理員登入 | myadmin (設定管理帳戶名稱) |
| *密碼* | 設定管理帳戶密碼 |
| *確認密碼* | 確認管理帳戶密碼 |
| *位置* | 北歐 (選取北歐或美國西部) |
| *版本* | 5.6 (選擇適用於 MySQL 的 Azure 資料庫伺服器版本) |

4- 按一下 [定價層] 指定新資料庫的服務層和效能等級。 在基本層，計算單位可以設定在 50 到 100 之間，而在標準層，則可以設定在 100 到 200 之間，還可根據加入的數量來新增儲存體。 在本作法指南中，我們選擇 50 計算單位和 50GB。 按一下 [確定]  以儲存您的選取項目。
![建立伺服器定價層](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5- 按一下 [建立] 佈建伺服器。 佈建需要幾分鐘的時間。

> 勾選 [釘選到儀表板] 選項以輕鬆追蹤部署。
> [!NOTE]
> 雖然基本層支援的儲存體最多為 1000GB，而標準層最多為 10000GB，但在公開預覽中，最大儲存體仍暫時限於 1000GB。 
</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>更新適用於 MySQL 的 Azure 資料庫伺服器
佈建新的伺服器之後，使用者有 2 個選項可編輯現有的伺服器︰重設系統管理員密碼，或變更計算單位來相應增加/減少伺服器。

### <a name="change-the-administrator-user-password"></a>變更系統管理員使用者密碼
1- 在伺服器 [概觀] 刀鋒視窗中，按一下 [重設密碼] 填入密碼輸入和確認視窗。

2- 在視窗中輸入新密碼並確認密碼，如下所示︰![重設密碼](./media/howto-create-manage-server-portal/reset-password.png)

3- 按一下 [確定] 儲存新密碼。

### <a name="scale-updown-by-changing-compute-units"></a>變更計算單位以相應增加/減少

1- 在 [伺服器] 刀鋒視窗的 [設定] 下，按一下 [定價層]，開啟「適用於 MySQL 的 Azure 資料庫」伺服器的 [定價層] 刀鋒視窗。

2- 依照**建立適用於 MySQL 的 Azure 資料庫伺服器**中的步驟 4，在相同的定價層變更計算單位。

## <a name="delete-an-azure-database-for-mysql-server"></a>刪除適用於 MySQL 的 Azure 資料庫伺服器

1- 在伺服器 [概觀] 刀鋒視窗上，按一下 [刪除] 命令按鈕，開啟 [刪除確認] 刀鋒視窗。

2- 在刀鋒視窗的輸入方塊中，輸入正確的伺服器名稱以再次確認。

3- 再按一次 [刪除] 按鈕，確認刪除動作，並等候通知列上出現「刪除成功」快顯訊息。

## <a name="list-the-azure-database-for-mysql-databases"></a>列出「適用於 MySQL 的 Azure 資料庫」資料庫
在伺服器 [概觀] 刀鋒視窗上，向下捲動直到在底部看到資料庫圖格。 資料表中會列出所有資料庫。 按一下 [刪除] 命令按鈕，開啟 [刪除確認] 刀鋒視窗。

![顯示資料庫](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>顯示「適用於 MySQL 的 Azure 資料庫」伺服器的詳細資料
在伺服器刀鋒視窗的 [設定] 下，按一下 [屬性]，將會開啟 [屬性] 刀鋒視窗。 然後，您就可以檢視伺服器的所有詳細資訊。

## <a name="next-steps"></a>後續步驟

[快速入門：使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫伺服器](./quickstart-create-mysql-server-database-using-azure-portal.md)

