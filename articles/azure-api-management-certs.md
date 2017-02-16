---
title: "上傳 Azure 管理 API 憑證 | Microsoft Docs"
description: "了解如何在 Azure 傳統入口網站中上傳管理 API 憑證。"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 783390791f27a9bf8ea400403a8b0f866f8c5cee
ms.openlocfilehash: 0f1ae486340270cac5cfdb5d44485d05cb996388


---
# <a name="upload-an-azure-management-api-management-certificate"></a>上傳 Azure 管理 API 管理憑證
管理憑證可讓您使用 Azure 所提供的服務管理 API 進行驗證。 許多程式和工具 (例如 Visual Studio 或 Azure SDK) 會使用這些憑證，將各種 Azure 服務的設定與部署自動化。 **這只適用於 Azure 傳統入口網站**。

> [!WARNING]
> 請務必小心！ 這些憑證類型允許使用它們進行驗證的任何人管理與他們相關聯的訂用帳戶。
>
>

如果您需要，可以為您 [提供](cloud-services/cloud-services-certs-create.md#what-are-management-certificates) 有關 Azure 憑證 (包括建立自我簽署憑證) 的詳細資訊。

您也可以使用 [Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/) 驗證用於自動化用途的用戶端程式碼。

## <a name="upload-a-management-certificate"></a>上傳管理憑證
一旦建立管理憑證 (僅包含公開金鑰的 .cer 檔案) 之後，您就可以將其上傳到入口網站。 入口網站提供憑證時，具有符合的憑證 (私密金鑰) 的任何人都可以透過管理 API 連線，並存取相關聯的訂用帳戶的資源。

1. 登入 [Azure 傳統入口網站](http://manage.windowsazure.com)。
2. 務必選取您想要用來與憑證產生關聯的正確訂用帳戶。 按入口網站右上方的 **訂用帳戶** 文字。

    ![設定](./media/azure-api-management-certs/subscription.png)
3. 當您選取正確的訂用帳戶之後，按入口網站左側的 [設定]  (您可能需要向下捲動)。

    ![設定](./media/azure-api-management-certs/settings.png)
4. 按 [管理憑證]  索引標籤。

    ![設定](./media/azure-api-management-certs/certificates-tab.png)
5. 按 [上傳]  按鈕。

    ![設定](./media/azure-api-management-certs/upload.png)
6. 填寫對話方塊資訊，然後按完成的 **核取記號**。

    ![設定](./media/azure-api-management-certs/upload-dialog.png)

## <a name="next-steps"></a>後續步驟
既然您已經擁有與訂用帳戶相關聯的管理憑證，您就能 (在本機安裝相符的憑證之後) 以程式設計方式連接至[服務管理 REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx)，並將同樣與該訂用帳戶相關聯的各種 Azure 資源自動化。



<!--HONumber=Dec16_HO1-->


