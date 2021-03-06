---
title: "Azure 服務匯流排驗證和授權 | Microsoft Docs"
description: "使用共用存取簽章 (SAS) 驗證向服務匯流排驗證應用程式。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 5ed7558cfff9991734e909e06e8bac9181131381
ms.contentlocale: zh-tw
ms.lasthandoff: 06/28/2017


---
# <a name="service-bus-authentication-and-authorization"></a>服務匯流排驗證和授權

應用程式可以使用共用存取簽章 (SAS) 驗證，或透過 Azure Active Directory 存取控制 (也稱為存取控制服務或 ACS) 向 Azure 服務匯流排進行驗證。 共用存取簽章 (SAS) 驗證可讓應用程式使用在命名空間或在與特定權限相關聯的實體上設定的存取金鑰，向服務匯流排進行驗證。 您可以接著使用此金鑰來產生共用存取簽章權杖，以便用戶端用來向服務匯流排進行驗證。

> [!IMPORTANT]
> 建議您透過 ACS 使用 SAS，因為它提供服務匯流排簡單、有彈性且容易使用的驗證配置。 在應用程式不需要管理已授權「使用者」概念的情況下，可以使用 SAS。 

## <a name="shared-access-signature-authentication"></a>共用存取簽章驗證
[SAS 驗證](service-bus-sas.md) 可讓您授與使用者具有特定權限之服務匯流排資源的存取權。 服務匯流排中的 SAS 驗證牽涉到在服務匯流排資源上設定具有相關權限的密碼編譯金鑰。 接著用戶端可以藉由提出 SAS 權杖 (其中包含正在存取的資源 URI 及利用設定金鑰簽署的到期日) 存取該資源。

您可以在服務匯流排命名空間上設定 SAS 的金鑰。 金鑰套用至該命名空間中的所有訊息實體。 您也可以在服務匯流排佇列和主題上設定金鑰。 [Azure 轉送](../service-bus-relay/relay-authentication-and-authorization.md)上也支援 SAS。

若要使用 SAS，您可以在命名空間、佇列或主題上設定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 物件。 此規則由下列元素組成：

* *KeyName* 。
* *PrimaryKey* 是用來簽署/驗證 SAS 權杖的密碼編譯金鑰。
* *SecondaryKey* 是用來簽署/驗證 SAS 權杖的密碼編譯金鑰。
* *權限* 表示接聽、傳送或管理授與權限的集合。

在命名空間層級設定的授權規則可以授與命名空間中所有實體的存取權給具備使用對應金鑰簽署之權杖的用戶端。 在服務匯流排命名空間、佇列或主題上最多可以設定 12 條這類授權規則。 根據預設，具備所有權限的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 會在第一次佈建時為每個命名空間設定。

若要存取實體，用戶端需要使用特定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)產生的 SAS 權杖。 SAS 權杖乃是使用資源字串的 HMAC-SHA256 所產生，該字串由宣告存取的資源 URI 以及具備與授權規則相關聯之密碼編譯金鑰的過期所組成。

服務匯流排的 SAS 驗證支援包含在 Azure .NET SDK 2.0 版或更新版本中。 SAS 包括 [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)的支援。 所有接受連接字串做為參數的 API 包括 SAS 連接字串的支援。

## <a name="acs-authentication"></a>ACS 驗證
透過 ACS 的服務匯流排驗證可透過隨附的「-sb」ACS 命名空間管理。 如果您想要建立服務匯流排命名空間的隨附 ACS 命名空間，您無法使用 Azure 傳統入口網站建立您的服務匯流排命名空間。您必須使用 [New-AzureSBNamespace](/powershell/module/azure/new-azuresbnamespace?view=azuresmps-3.7.0) PowerShell Cmdlet 建立命名空間。 例如：

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

若要避免建立 ACS 命名空間，請發出下列命令：

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

例如，如果您建立一個名為 **contoso.servicebus.windows.net** 的「服務匯流排」命名空間，系統就會自動佈建一個名為 **contoso-sb.accesscontrol.windows.net** 的隨附 ACS 命名空間。 對於 2014 年 8 月之前建立的所有命名空間，也會建立隨附的 ACS 命名空間。

根據預設，此隨附 ACS 命名空間中會佈建具備所有權限的預設服務身分識別 "owner"。 您可以藉由設定適當的信任關係來透過 ACS 取得任何服務匯流排實體的細微控制。 您可以設定其他服務身分識別以管理服務匯流排實體的存取權。

若要存取實體，用戶端會藉由出示其認證，利用適當宣告向 ACS 要求 SWT 權杖。 SWT 權杖必須做為要求的一部分傳送至服務匯流排，以啟用用於存取實體的用戶端授權。

服務匯流排的 ACS 驗證支援包含在 Azure .NET SDK 2.0 版或更新版本中。 此驗證包括 [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider)的支援。 所有接受連接字串做為參數的 API 包括 ACS 連接字串的支援。

## <a name="next-steps"></a>後續步驟

如需 SAS 的詳細資料，請繼續閱讀[使用共用存取簽章的服務匯流排驗證](service-bus-sas.md)。

如需 Azure 轉送驗證和授權的相關對應資訊，請參閱 [Azure 轉送驗證和授權](../service-bus-relay/relay-authentication-and-authorization.md)。 


