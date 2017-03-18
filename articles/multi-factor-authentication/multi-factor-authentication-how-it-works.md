---
title: "Azure Multi-Factor Authentication -它的作用"
description: "Azure Multi-Factor Authentication 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 它藉由要求第二種形式的驗證提供額外的安全性，並透過一系列簡單的驗證選項提供增強式驗證。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e028130569a1e55834f68bde87f21f6f7505d41f
ms.openlocfilehash: 9132e9bde7403e2868b92cb1149904d926ff5d8e
ms.lasthandoff: 02/23/2017


---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication 的作用
多因素驗證的安全性仰賴其分層方法。 使用多重驗證因素會為攻擊者帶來相當程度的挑戰。 即使攻擊者試圖打探使用者的密碼，在不持有信任裝置的情況下便沒有任何意義。 如果使用者遺失裝置，拾獲該裝置的人仍然無法使用此裝置，除非他或她剛好知道使用者的密碼。

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)

Azure Multi-Factor Authentication 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。  它藉由要求第二種形式的驗證提供額外的安全性，並透過一系列簡單的驗證選項提供增強式驗證。

如需其運作方式的詳細資訊，請觀看以下影片：

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]

## <a name="methods-available-for-two-step-verification"></a>雙步驟驗證適用的方法
當使用者登入時，系統會將額外的驗證傳送給使用者。  以下是適用於這個第二次驗證的方法清單。

| 驗證方法 | 說明 |
| --- | --- |
| 撥打電話 |撥打使用者已註冊的手機，要求他們按 # 符號或輸入 PIN 碼來驗證登入。 |
| 簡訊 |傳送含六位數代碼的簡訊到使用者手機。  輸入此代碼可完成驗證程序。 |
| 行動應用程式通知 |將驗證要求傳送到使用者的智慧型手機，要求他們在行動應用程式中選取 [驗證] 來完成驗證。 如果您將應用程式通知選為主要驗證方法，這種情況便會發生。  如果它們在未登入時收到通知，他們可以選擇提報詐騙。 |
| 行動應用程式驗證碼 |在使用者智慧型手機上執行的行動應用程式，會顯示 6 位數驗證碼，每隔 30 秒會變更。 使用者尋找最新的程式碼，並在登入頁面上輸入以完成驗證程序。 如果您將驗證碼選為主要驗證方法，這種情況便會發生。 |
| 協力廠商 OATH 權杖 | Azure Multi-Factor Authentication 可以設定為接受第 3 方驗證方法。 |

Azure Multi-Factor Authentication 為雲端與伺服器提供可選取的驗證方法。 這表示您可以選擇可供使用者使用的方法：撥打電話、簡訊、應用程式通知或應用程式程式碼。 如需詳細資訊，請參閱 [可選取的驗證方法](multi-factor-authentication-whats-next.md#selectable-verification-methods)。

## <a name="next-steps"></a>後續步驟

- 請參閱不同的 [Azure Multi-Factor Authentication 版本和耗用方法](multi-factor-authentication-versions-plans.md)

- 選擇要[在雲端還是內部部署](multi-factor-authentication-get-started.md)部署 Azure MFA
