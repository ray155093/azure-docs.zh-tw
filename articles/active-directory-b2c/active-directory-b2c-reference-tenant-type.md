---
title: "Azure Active Directory B2C︰生產級別租用戶與預覽版 B2C 租用戶之比較 | Microsoft Docs"
description: "有關 Azure Active Directory B2C 租用戶類型的主題"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 8a0644da-b825-4edc-8ce9-541c3c976afb
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 4b13c040a15bef2f04d2cd2126e2270d061898bd
ms.openlocfilehash: a37992cd2bfe346fd171bde15b6180c56527289b


---
# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure Active Directory B2C︰生產級別租用戶與預覽 B2C 租用戶
如果您打算在 Azure Active Directory (Azure AD) B2C 上編寫生產應用程式，您必須確定您有可供其上線運作的正確租用戶「類型」。 若要查看您擁有的類型，請在 Azure 入口網站上遵循下列步驟以[瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)，並查看 [租用戶類型] 底下的內容。

## <a name="summary"></a>摘要
Azure AD B2C 只在北美洲的 [生產級別]  B2C 租用戶上支援生產應用程式。

| 租用戶類型 | 國家/區域 | 已正式推出？ |
| --- | --- | --- |
| **生產級別租用戶** |北美洲國家/區域 |是 |
| **生產級別租用戶** |所有國家/區域 (北美洲除外) |否 |
| **預覽租用戶** |所有國家/區域 |否 |

> [!NOTE]
> 少數已推出 Azure AD 租用戶 (適用於員工) 的國家或區域目前並未推出 Azure AD B2C 租用戶 (適用於消費者)。 如需詳細資訊，請閱讀後面幾節。
> 
> 

## <a name="production-scale-b2c-tenant-in-north-america"></a>北美洲的生產級別 B2C 租用戶
如果您在北美洲[建立了 B2C 租用戶](active-directory-b2c-get-started.md)，亦即，在下列其中一個國家或區域︰美國、加拿大、哥斯大黎加、多明尼加共和國、薩爾瓦多、瓜地馬拉、墨西哥、巴拿馬、波多黎各和千里達及托巴哥，而且您的 B2C 管理 UI 上的 [租用戶類型] 顯示為 [生產級別]，則您的租用戶可以用於生產應用程式。

> [!NOTE]
> 生產級別租用戶可以調整為每個租用戶有數億個消費者身分識別。
> 
> 

![生產級別租用戶的螢幕擷取畫面](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>任何國家/區域中的預覽 B2C 租用戶
如果您已在 Azure AD B2C 的預覽期間建立 B2C 租用戶，您的 [租用戶類型] 可能會顯示為 [預覽租用戶]。 如果情況如此，您只能將租用戶用於開發和測試用途，而不能用於生產應用程式。

> [!IMPORTANT]
> 預覽 B2C 租用戶沒有任何途徑可以移轉到生產級別 B2C 租用戶。 請注意，當您刪除預覽 B2C 租用戶並使用相同的網域名稱重建生產級別 B2C 租用戶時，會發生已知的問題。 您必須使用不同的網域名稱建立生產級別 B2C 租用戶。
> 
> 

![預覽租用戶的螢幕擷取畫面](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>北美洲以外的生產級別 B2C 租用戶
北美洲以外的區域目前並未正式推出 Azure AD B2C。 不過，您可以在下列其中一個國家或區域建立並使用生產級別租用戶，以便進行開發和測試︰阿爾及利亞、奧地利、亞塞拜然、巴林、白俄羅斯、比利時、保加利亞、克羅埃西亞、賽普勒斯、捷克共和國、丹麥、埃及、愛沙尼亞、芬蘭、法國、德國、希臘、匈牙利、冰島、愛爾蘭、以色列、義大利、約旦、哈薩克、肯亞、科威特、拉脫維亞、黎巴嫩、列支敦斯登、立陶宛、盧森堡、馬其頓 (FYRO)、馬爾他、蒙特內哥羅、摩洛哥、荷蘭、奈及利亞、挪威、阿曼、巴基斯坦、波蘭、葡萄牙、卡達、羅馬尼亞、俄羅斯、沙烏地阿拉伯、塞爾維亞、斯洛伐克、斯洛維尼亞、南非、西班牙、瑞典、瑞士、突尼西亞、土耳其、烏克蘭、阿拉伯聯合大公國和英國。

一旦 Azure AD B2C 宣佈在上面的國家或地區公開上市，您即可繼續使用這些生產級別租用戶並且讓您的生產應用程式上線，而不會遺失任何資料。

## <a name="availability-of-b2c-tenants"></a>B2C 租用戶的可用性
下列國家或區域目前並未推出 B2C 租用戶︰阿富汗、阿根廷、澳洲、巴西、智利、哥倫比亞、厄瓜多、香港特別行政區、印度、印尼、伊拉克、日本、韓國、馬來西亞、紐西蘭、巴拉圭、祕魯、菲律賓、新加坡、斯里蘭卡、台灣、泰國、烏拉圭和委內瑞拉。 我們計劃在未來納入這些國家/區域。




<!--HONumber=Dec16_HO5-->


