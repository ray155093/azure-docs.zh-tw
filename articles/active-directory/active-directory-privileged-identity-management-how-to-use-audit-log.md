<properties
   pageTitle="如何使用稽核記錄檔 | Microsoft Azure"
   description="了解如何在 Azure 特殊權限身分識別管理擴充功能中使用稽核記錄。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management：如何使用稽核記錄檔

您可以使用 Privileged Identity Management (PIM) 稽核記錄，來查看指定期間內的所有使用者指派與啟用。

## 瀏覽至稽核記錄檔
從 [Azure 入口網站](https://portal.azure.com)儀表板選取 [Azure AD Priviliged Identity Management] 應用程式。在這裡按一下 PIM 儀表板中的 [稽核記錄]，來存取稽核記錄檔。

## 稽核記錄圖表
您可以使用稽核記錄，在折線圖中檢視啟用總數、每天最大啟用數目，以及每天平均啟用數目。如果稽核記錄中有一個以上的角色，您也可以依角色篩選資料。

使用 [時間]、[動作] 和 [角色] 按鈕來排序記錄。

## 稽核記錄清單
稽核記錄清單中的欄如下：

- **要求者** - 要求啟用或變更角色的使用者。如果值為「Azure 系統」，請檢查 Azure 稽核記錄檔以取得詳細資訊。
- **使用者** - 啟用角色或指派給角色的使用者。
- **角色** - 使用者指派或啟用的角色。
- **動作** - 要求者採取的動作。這可包括指派、解除指派、啟用或停用。
- **時間** - 動作發生的時間。
- **推理** - 如果在啟用過程中於 [原因] 欄位輸入任何文字，則它將會顯示於此處。
- **到期** - 只與角色啟用相關。

## 篩選稽核記錄檔

您可以按一下 [篩選] 按鈕，來篩選稽核記錄檔顯示的資訊。隨即會出現 [更新圖表參數] 刀鋒視窗。

設定篩選器之後，請按一下 [更新] 篩選記錄檔中的資料。如果資料並未立即出現，請重新整理頁面。


### 變更日期範圍
使用 [今天]、[過去一週]、[過去一個月] 或 [自訂] 按鈕，變更稽核記錄檔的時間範圍。

當您選擇 [自訂] 按鈕時，將會看見指定記錄檔日期範圍的 [開始] 日期欄位和 [結束] 日期欄位。您可以使用 MM/DD/YYYY 格式輸入日期，或者按一下 [月曆] 圖示並從月曆中選擇日期。

### 變更記錄中包含的角色

選取或取消選取每個角色旁邊的 [角色] 核取方塊，將其納入記錄檔或排除在記錄檔外。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->