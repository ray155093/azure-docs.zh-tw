---
ms.assetid: 
title: "Azure Key Vault 安全世界 | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6477e8d91b71361ef91763418b22596deb216f02
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault 安全世界和地理界限

Azure Key Vault 是多租用戶服務，並且在每個 Azure 位置使用硬體安全模組 (HSM) 的集區。 

同一個地理區域之 Azure 位置上的所有 HSM 都共用相同的密碼編譯界限 (Thales 安全世界)。 例如，美國東部和美國西部共用相同的安全世界，因為它們隸屬於美國地理位置。 同樣地，日本的所有 Azure 位置共用相同的安全世界，而位於澳洲、印度等國的所有 Azure 位置以此類推。 

在一個 Azure 位置的 Key Vault 建立的金鑰備份可還原至另一個 Azure 位置的 Key Vault，前提是這兩個條件都成立︰

- 兩個 Azure 位置都隸屬於相同的地理位置
- 兩個 Key Vault 都隸屬於相同的 Azure 訂用帳戶

例如，以指定的訂用帳戶在印度西部的 Key Vault 建立的金鑰備份，只能還原至同一個訂用帳戶和地理位置 (印度西部、印度中部或印度南部) 的另一個 Key Vault。 



