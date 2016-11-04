---
title: 從 PowerApps Enterprise 中的 API 建立 Swagger 2.0 API 定義 | Microsoft Docs
description: 了解如何透過使用現有 API 建立的 Swagger 2.0 API 定義註冊 API
services: ''
suite: powerapps
documentationcenter: ''
author: MandiOhlinger
manager: dwrede
editor: ''

ms.service: powerapps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2016
ms.author: guayan

---
# 從 Swagger 2.0 API 定義註冊 API
> [!IMPORTANT]
> 本主題已經封存，並且很快就會移除。請到全新的 [PowerApps](https://powerapps.microsoft.com) 來看看我們在忙些什麼。
> 
> * 若要深入了解 PowerApps 並開始使用，請移至 [PowerApps](https://powerapps.microsoft.com)。  
> * 若要深入了解 PowerApps 中可用的連線，請瀏覽 [List of available connections (可用連線清單)](https://powerapps.microsoft.com/tutorials/connections-list/)。  
> * 若要深入了解 PowerApps 中的自訂 API，請前往 [What are Custom APIs (什麼是自訂 API)](https://powerapps.microsoft.com/tutorials/register-custom-api/)。 
> 
> 

<!--Archived
Many organizations already have some existing APIs that users can use and consume within their apps. To use these APIs in your apps, you must "register" the APIs in the Azure portal using a managed API, existing APIs in your app service environment, or creating an API using Swagger. 

> [AZURE.SELECTOR]
- [Managed APIs](../articles/power-apps/powerapps-register-from-available-apis.md)
- [APIs in your ASE](../articles/power-apps/powerapps-register-api-hosted-in-app-service.md)
- [Swagger APIs](../articles/power-apps/powerapps-register-existing-api-from-api-definition.md)

In this topic, we focus on the third option - **registering one of your own APIs using Swagger 2.0 API definition** that you created from an existing API. 

#### Prerequisites to get started

- Sign up for [PowerApps Enterprise](powerapps-get-started-azure-portal.md)
- Create an [app service environment](powerapps-get-started-azure-portal.md)

## Register an existing API using Swagger 2.0 API definition

It's very easy to register these existing APIs. Steps include:

1. Create the [Swagger 2.0](http://swagger.io) API definition for your existing API. PowerApps uses Swagger 2.0 as the API definition format. You can use the tools referenced on the [Swagger 2.0 website](http://swagger.io) to help you easily author a Swagger 2.0 API definition. A few things to note:  

    - The ``host`` property should point to the actual endpoint of your existing API. Do you not use scheme or any sub-paths. For example, enter ``api.contoso.com``.  <br/><br/>
    - The ``basePath`` property should list the sub paths of your existing API endpoint, if there is any. Start with a forward slash ``/``. For example,  enter ``/purchaseorderapi``.

2. Make sure your existing API is accessible by your app service environment securely:  

    1. If you are comfortable with making your API accessible using the internet, you can set up HTTP basic access authentication between your app service environment and your existing API. [Update an existing API](powerapps-configure-apis.md) to see how.  <br/><br/>
    2. If you want to keep your API within your organization's network, you can set up a virtual network on the app service environment to access your organization's network securely. Learn more about [app service environments](../app-service-web/app-service-app-service-environment-intro.md).

3. In the [Azure portal](https://portal.azure.com/), select **PowerApps**, and then select **Manage APIs**:  
![][11]
4. In Manage APIs, select **Add**:  
![][12]
5. In **Add API**, enter the API properties:  

    - In **Name**, enter a name for your API. Notice that the name you enter is included in the runtime URL of the API. Make the name meaningful and unique within your organization.
    - In **Source**, select **Import from Swagger 2.0**.

6. In **API definition (Swagger 2.0)**, upload your Swagger 2.0 API definition file:  
 ![][13]
7. Select **ADD** to complete these steps.

> [AZURE.TIP] When you register an API, you're registering the API to your app  service environment. Once in the app service environment, it can be used by other apps within the same app service environment.

## Summary and next steps

In this topic, you've seen how to register an API from Swagger 2.0 API definition. Here are some related topics and resources for learning more about PowerApps:  

- [Configure the API properties](powerapps-configure-apis.md)
- [Give users access to the APIs](powerapps-manage-api-connection-user-access.md)
- [Start creating your apps in PowerApps](https://powerapps.microsoft.com/tutorials/)
-->


<!--References-->
[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png

<!---HONumber=AcomDC_0504_2016-->