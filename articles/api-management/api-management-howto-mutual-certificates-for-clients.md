---
title: "使用 API 管理中的用戶端憑證驗證保護 API - Azure API 管理 | Microsoft Docs"
description: "了解如何使用用戶端憑證來保護對 API 的存取"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: b04ce0fe0db7649cebc7a1eeb2a35f1d53bf9636
ms.openlocfilehash: 1ce9f07c3e91eacdc74ccab3fbb7dc433a25c197

---

# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的用戶端憑證驗證保護 API

API 管理提供以用戶端憑證保護對 API 之存取 (例如，用戶端對 API 管理) 的功能。 目前，您可以檢查用戶端憑證的指紋是否符合想要的值。 您也可以檢查指紋是否符合已上傳到 API 管理的現有憑證。  

如需有關如何使用用戶端憑證保護對 API 後端服務之存取 (例如，API 管理到後端) 的資訊，請參閱[如何使用用戶端憑證驗證來保護後端服務](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-a-thumbprint-against-a-desired-value"></a>檢查指紋是否符合想要的值

您可以設定下面的原則來檢查用戶端憑證的指紋：

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint-to-validate")" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>檢查指紋是否符合已上傳到 API 管理的憑證

下列範例說明如何檢查用戶端憑證的指紋是否符合已上傳到 API 管理的憑證： 

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>後續步驟

*  [如何使用用戶端憑證驗證來保護後端服務](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)
*  [如何上傳憑證](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)




<!--HONumber=Feb17_HO1-->


