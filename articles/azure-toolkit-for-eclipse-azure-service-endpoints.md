---
title: "Azure 服務端點"
description: "說明適用於 Eclipse 的 Azure 工具組中 Azure 服務端點的設定。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9c6125ec-7278-461e-b69c-ed56e844f742
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c6e67ba61732beba96831e6c3b0fd89bb8097972


---
# <a name="azure-service-endpoints"></a>Azure 服務端點
Azure 服務端點會判斷您的應用程式是否已部署至全域 Azure 平台並由該平台管理，Azure 由位於中國的 21Vianet 或私人 Azure 平台運作。 [ **服務端點** ] 對話方塊可讓您指定要使用的服務端點。 若要在 Eclipse 中開啟 [服務端點] 對話方塊，請依序按一下 [視窗]、[喜好設定]，展開 [Azure]，然後按一下 [服務端點]。 設定 [ **作用中設定** ] 欄位，可決定您目前工作區中的 Azure 專案將使用的 Azure 服務端點。

[ **服務端點** ] 對話方塊如下顯示。

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>設定服務端點
在 [ **服務端點** ] 對話方塊中，執行下列其中一個動作：

* 如果想使用全域 Azure 平台，請從 [作用中設定] 下拉式清單中，選取 [windowsazure.com]，並按一下 [確定]。
* 如果想使用由位於中國的 21Vianet 所運作的 Azure，請從 [作用中設定] 下拉式清單中，選取 [windowsazure.cn (中國)]，並按一下 [確定]。
* 如果想使用私人 Azure 平台：
  1. 按一下 [ **編輯**]。
  2. 隨即會開啟一個對話方塊，告知您 [ **服務端點** ] 對話方塊將關閉，喜好設定檔案將開啟。 按一下 [確定] 。
  3. 在 preferencesets.xml 檔案中，建立新的 `preferenceset` 元素。 針對此新元素，建立 `name`、`blob`、`management`、`portalURL` 和 `publishsettings` 屬性，再為其新增對應至私人 Azure 平台的值。 您可使用為現有 `preferenceset` 元素所提供的值作為範本。 **注意**：用於 `blob` 屬性的值必須在 URL 中包含 "blob" 文字。
  4. 儲存並關閉 preferencesets.xml。
  5. 重新開啟 [ **服務端點** ] 對話方塊。
  6. 從 [作用中設定] 下拉式清單中，選取您建立的作用中設定，然後按一下 [確定]。
  7. 建立私人 Azure 平台 `preferenceset` 元素後，您可以按一下 [服務端點] 對話方塊中的 [編輯] 按鈕，變更指派給該元素的值。 如有需要，也可以建立多個私人 Azure 平台 `preferenceset` 元素。

## <a name="see-also"></a>另請參閱
[適用於 Eclipse 的 Azure 工具組][適用於 Eclipse 的 Azure 工具組]

[安裝 Azure Toolkit for Eclipse][安裝 Azure Toolkit for Eclipse] 

[在 Eclipse 中為 Azure 建立 Hello World 應用程式][在 Eclipse 中為 Azure 建立 Hello World 應用程式]

如需如何搭配使用 Azure 與 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心][Azure Java 開發人員中心]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[適用於 Eclipse 的 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[在 Eclipse 中為 Azure 建立 Hello World 應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[安裝 Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->



<!--HONumber=Nov16_HO3-->


