---
title: "將群組指派給 Azure AD 應用程式 | Microsoft Docs&quot;"
description: "如何為 Azure 應用程式實作群組指派。"
services: active-directory
documentationcenter: 
author: IHenkel
manager: femila
editor: 
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: inhenk
translationtype: Human Translation
ms.sourcegitcommit: e66b606433f8924a509f2d04dae67ff00ded6dca
ms.openlocfilehash: 81b9b66bb16e183b8c3c206a4a49b2f4845bc201


---
# <a name="azure-ad-and-applications-assign-groups-to-an-application"></a>Azure AD 和應用程式：將群組指派給應用程式
在將使用者和群組指派給應用程式之前，您必須先要求使用者指派。 若要了解如何要求使用者指派，請參閱 [Azure AD 和應用程式：要求使用者指派](active-directory-applications-guiding-developers-requiring-user-assignment.md) 一文。

本文假設您已經在針對此應用程式使用的作用中目錄內建立了群組。

## <a name="assigning-groups-to-an-application"></a>將群組指派給應用程式
1. 使用系統管理員帳戶登入 Azure 入口網站。
2. 在主功能表中按一下 [ **所有項目** ] 項目。
3. 選擇您要為應用程式使用的目錄。
4. 按一下 [ **應用程式** ] 索引標籤。
5. 從與此目錄相關聯的應用程式清單中選取應用程式。
6. 按一下 [ **使用者和群組** ] 索引標籤。
7. 透過 [ **群組** ] 下拉式清單，篩選作用中目錄內的群組清單。
8. 選取群組。
9. 按一下 [ **指派**]。
10. 出現提示時，按一下 [ **是** ]。

## <a name="next-steps"></a>後續步驟
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]



<!--HONumber=Feb17_HO1-->


