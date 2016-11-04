---
title: 如何設定雲端服務 (入口網站) | Microsoft Docs
description: 了解如何在 Azure 中設定雲端服務。了解更新雲端服務組態和設定角色執行個體的遠端存取。這些範例使用 Azure 入口網站。
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2016
ms.author: adegeo

---
# 如何設定雲端服務
> [!div class="op_single_selector"]
> * [Azure 入口網站](cloud-services-how-to-configure-portal.md)
> * [Azure 傳統入口網站](cloud-services-how-to-configure.md)
> 
> 

您可以在 Azure 入口網站中設定雲端服務的最常用設定。或者，如果您想要直接更新組態檔，可以下載要更新的服務組態檔、上傳更新過的檔案，然後將雲端服務更新為使用這些組態變更。使用上述任一種方式，都會將組態更新推送到所有角色執行個體。

您也可以管理雲端服務角色的執行個體，或從遠端桌面存取它們。

每個角色至少必須有兩個角色執行個體，Azure 才能確保服務在組態更新期間有 99.95% 的可用性。如此才能讓一個虛擬機器在受到更新時，還有另一個虛擬機器可以處理用戶端要求。如需詳細資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)。

## 變更雲端服務
開啟 [Azure 入口網站](https://portal.azure.com/)之後，瀏覽至您的雲端服務。您可以從這裡管理許多層面。

![設定頁面](./media/cloud-services-how-to-configure-portal/cloud-service.png)

[設定] 或 [所有設定] 連結可開啟 [設定] 刀鋒視窗，讓您變更 [屬性]、變更 [組態]、管理 [憑證]、設定 [警示規則]，以及管理可存取此雲端服務的 [使用者]。

![Azure 雲端服務設定刀鋒視窗](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

> [!NOTE]
> 您無法使用 **Azure 入口網站**來變更用於雲端服務的作業系統，只能透過 [Azure 傳統入口網站](http://manage.windowsazure.com/)變更此設定。[這裡](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file)有詳細說明。
> 
> 

## 監控
您可以將警示新增至雲端服務。按一下 [設定] > [警示規則] > [加入警示]。

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

您可以從這裡設定警示。您可以使用 [計量] 下拉式方塊設定下列資料類型的警示。

* 磁碟讀取
* 磁碟寫入
* 網路輸入
* 網路輸出
* CPU 百分比

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### 從計量圖格設定監視
除了使用 [設定] > [警示規則]，您也可以在 [雲端服務] 刀鋒視窗的 [監視] 區段中，按一下其中一個計量圖格。

![雲端服務監視](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

您可以從這裡自訂與圖格一起使用的圖表，或新增警示規則。

## 重新啟動、重新安裝映像或遠端桌面
目前，您無法使用 **Azure 入口網站**來設定遠端桌面。不過，您可以透過 [Azure 傳統入口網站](cloud-services-role-enable-remote-desktop.md)、[PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) 或 [Visual Studio](../vs-azure-tools-remote-desktop-roles.md) 來設定它。

首先，請按一下雲端服務執行個體。

![雲端服務執行個體](./media/cloud-services-how-to-configure-portal/cs-instance.png)

從開啟的刀鋒視窗中，您可以起始遠端桌面連線、從遠端重新啟動執行個體，或從遠端重新安裝執行個體的映像 (開始使用全新的映像)。

![雲端服務執行個體按鈕](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## 重新設定 .cscfg
您可能需要透過[服務組態 (cscfg)](cloud-services-model-and-package.md#cscfg) 檔案來重新設定雲端服務。首先，您需要下載 .cscfg 檔案，進行修改，然後上傳。

1. 按一下 [設定] 圖示或 [所有設定] 連結，以開啟 [設定] 刀鋒視窗。
   
    ![設定頁面](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. 按一下 [**組態**] 項目。
   
    ![設定刀鋒視窗](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. 按一下 [**下載**] 按鈕。
   
    ![下載](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. 在您更新服務組態檔之後，請上傳和套用組態更新：
   
    ![上傳](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. 選取 .cscfg 檔案，然後按一下 [**確定**]。

## 後續步驟
* 了解如何[部署雲端服務](cloud-services-how-to-create-deploy-portal.md)。
* 設定[自訂網域名稱](cloud-services-custom-domain-name-portal.md)。
* [管理您的雲端服務](cloud-services-how-to-manage-portal.md)。
* 設定 [SSL 憑證](cloud-services-configure-ssl-certificate-portal.md)。

<!---HONumber=AcomDC_0803_2016-->