---
title: "Azure Active Directory 中的具名位置 | Microsoft Docs"
description: "透過設定具名位置，您可以避免組織所擁有的 IP 位址針對不可能到達非典型位置的移動風險事件類型，產生誤判。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 9637c56cf88ccf4c54fda789d4e7e3ca3e0a4fed
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="named-locations-in-azure-active-directory"></a>Azure Active Directory 中的具名位置

具名位置是 Azure Active Directory 的一個功能，可讓您標記組織中信任的 IP 位址範圍。 在您的環境中，可以在[風險事件](active-directory-reporting-risk-events.md)的偵測內容中使用具名位置，以減少「不可能到達非典型位置的移動」風險事件類型的回報誤判次數。 




## <a name="configuration"></a>組態

**設定具名位置：**

1. 以全域系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左窗格中，按一下 [Azure Active Directory]。

    ![具名位置](./media/active-directory-named-locations/01.png)

3. 在 [Azure Active Directory] 刀鋒視窗的 [安全性] 區段中，按一下 [條件式存取]。

    ![具名位置](./media/active-directory-named-locations/05.png)


4. 在 [條件式存取] 刀鋒視窗的 [管理] 區段中，按一下 [具名位置]。

    ![具名位置](./media/active-directory-named-locations/06.png)


5. 在 [具名位置] 刀鋒視窗頂端的功能表中，按一下 [新增位置]。

    ![具名位置](./media/active-directory-named-locations/07.png)


6. 在 [新增] 刀鋒視窗中，執行下列步驟︰

    ![具名位置](./media/active-directory-named-locations/08.png)

    a. 在 [名稱]  文字方塊中，輸入具名位置的名稱。

    b. 在 [IP 範圍] 文字方塊中，輸入 IP 範圍。 IP 範圍的格式必須為「無類別網域間路由選擇」(CIDR)。  

    c. 按一下 [建立] 。



## <a name="what-you-should-know"></a>您應該知道的事情

**大量更新** - 建立或更新具名位置時，您可以上傳或下載包含 IP 範圍的 CSV 檔案來進行大量更新。 透過上傳，會將檔案中的 IP 位址新增到清單中，而不是覆寫清單。

![具名位置](./media/active-directory-named-locations/09.png)


**限制** - 您最多可以定義 60 個具名位置，每個位置皆指派一個 IP 範圍。 如果您只設定一個具名位置，則最多可以為該位置定義 500 個 IP 範圍。


## <a name="next-steps"></a>後續步驟

若要深入了解：

- 風險事件，請參閱 [Azure Active Directory 風險事件](active-directory-reporting-risk-events.md)


