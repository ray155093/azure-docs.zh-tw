---
title: "Azure Active Directory 目錄的特性 | Microsoft Docs"
description: "了解您的目錄為完全獨立的資源以管理 Azure Active Directory 目錄"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 5ec00d5e8380f121dd9302cf08a0708c530aab9b
ms.contentlocale: zh-tw
ms.lasthandoff: 03/01/2017


---
# <a name="understand-how-multiple-azure-active-directory-directories-interact"></a>了解多個 Azure Active Directory 目錄的互動方式
在 Azure Active Directory (Azure AD) 目錄中，每個目錄都是完全獨立的資源：對等、全功能，且在邏輯上獨立於您管理的其他目錄。 目錄之間沒有任何父子關聯性。 目錄之間的這項獨立性包括資源獨立性、系統管理獨立性和同步處理獨立性。

## <a name="resource-independence"></a>資源獨立性
如果您在某個目錄中建立或刪除資源，則不會影響另一個目錄中的任何資源 (但外部使用者有部分例外狀況)，如下所述。 如果您搭配使用自訂網域 'contoso.com' 與某個目錄，則它不能與任何其他目錄搭配使用。

## <a name="administrative-independence"></a>系統管理獨立性
如果是 'Contoso' 目錄的非系統管理使用者，請建立 'Test' 測試目錄，然後：

* 根據預設，建立目錄的使用者會新增為該新目錄的外部使用者，並會獲得該目錄中的全域管理員角色。
* 'Contoso' 目錄的系統管理員沒有 'Test' 目錄的直接系統管理權限，除非 'Test' 的系統管理員特別將這些權限授與他們。 'Contoso' 的系統管理員如果可控制建立 'Test' 的使用者帳戶，即可控制 'Test' 目錄的存取權。
* 如果您變更 (新增或移除) 某個目錄中使用者的系統管理員角色，則變更不會影響另一個目錄中該使用者可能有的任何系統管理員角色。

## <a name="synchronization-independence"></a>同步處理獨立性
您可以單獨設定每個 Azure AD 目錄，以取得從任一項目的單一執行個體同步處理的資料：

* 目錄同步處理 (DirSync) 工具，以與單一 AD 樹系同步處理資料。
* Azure Active Directory Connector for Forefront Identity Manager，以與一或多個內部部署樹系和 (或) 非 Azure AD 資料來源同步處理資料。

## <a name="add-an-azure-ad-directory"></a>新增 Azure AD 目錄
若要在 Azure 傳統入口網站中新增 Azure AD 目錄，請選取左邊的 Azure Active Directory 延伸模組，然後點選 [新增] 。

> [!NOTE]
> 與其他 Azure 資源不同，您的目錄不是 Azure 訂用帳戶的子資源。 如果您取消或允許 Azure 訂用帳戶到期，則還是可以使用 Azure PowerShell、Azure Graph API 或其他介面 (例如 Office 365 系統管理中心) 存取目錄資料。 您也可以關聯另一個訂用帳戶與目錄。
>
>

## <a name="next-steps"></a>後續步驟
如需 Azure AD 授權問題和最佳作法的一般概觀，請參閱 [什麼是 Azure Active Directory 授權？](active-directory-licensing-what-is.md)。

