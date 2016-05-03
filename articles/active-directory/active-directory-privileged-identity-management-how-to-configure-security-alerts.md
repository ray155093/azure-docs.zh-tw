<properties
   pageTitle="如何設定安全性警示 | Microsoft Azure"
   description="了解如何為 Azure Privileged Identity Management 擴充功能設定安全性警示。"
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

# Azure AD Privileged Identity Management：如何設定安全性警示

## 安全性警示
Azure Privileged Identity Management (PIM) 會產生下列警示，您可在 PIM 儀表板的 [警示] 區段中檢視它們。

| 警示 | 觸發程序 | 建議 |
| ----- | ------- | -------------- |
| **永久啟用** | 在 PIM 外部，系統管理員已永久指派給某個角色。 | 檢閱新的角色指派，如有需要請將它變更為暫時性。 |
| **特殊權限角色的可疑啟用更新** | 在設定所允許的時間內，重複啟用相同角色的次數過多。 | 連絡使用者確定其可否成功啟用角色。 |
| **針對角色啟用設定弱式驗證** | 設定中有一些不含 MFA 的角色。 | 考慮要求 MFA 以啟用所有角色。 |
| **備援系統管理員** | 有暫時性的系統管理員最近尚未啟用其角色。 | 移除不再需要的角色指派。 |
| **全域管理員太多** | 全域管理員的數目超過建議的數目。 | 移除不再需要的角色指派，或讓某些變成暫時性的。 |

## 設定安全性警示設定

### 「特殊權限角色的可疑啟用更新」警示

設定 [啟用更新時間範圍] 和 [Number of activation renewals] (啟用更新次數) 設定，控制觸發這個警示的時間。

1. 從儀表板的 [活動] 區段，選取 [安全性警示]。隨即會出現 [作用中的安全性警示] 刀鋒視窗。
2. 按一下 [設定]。
3. 調整滑桿或在文字欄位中輸入分鐘數，來設定 [啟用更新時間範圍]。最大值為 100。
4. 在啟用更新時間範圍內，藉由調整滑桿或在文字欄位中輸入更新數目，來設定 [啟用更新數目]。最大值為 100。
5. 按一下 [儲存]。

### 「備援系統管理員」警示
1. 從儀表板的 [活動] 區段，選取 [安全性警示]。隨即會出現 [作用中的安全性警示] 刀鋒視窗。
2. 按一下 [設定]。
3. 調整滑桿或在文字欄位中輸入天數，來選取允許未啟用角色的天數。
4. 按一下 [儲存]。

### 「全域管理員太多」警示

有兩項設定會觸發這個警示︰
- 如果全域管理員的數目超過允許的系統管理員數目，則 [全域管理員的數目下限] 會觸發警示。
- 如果全域管理員的系統管理員百分比超過允許的設定，則 [全域管理員百分比] 會觸發警示。

1. 從儀表板的 [活動] 區段，選取 [安全性警示]。隨即會出現 [作用中的安全性警示] 刀鋒視窗。
2. 按一下 [設定]。
3. 調整滑桿或在文字欄位中輸入數目，來設定 [全域管理員數目下限]。
4. 調整滑桿或在文字欄位中輸入百分比，來設定 [全域管理員的百分比]。
5. 按一下 [儲存]。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->