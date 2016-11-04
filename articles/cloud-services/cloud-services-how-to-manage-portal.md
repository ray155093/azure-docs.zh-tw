---
title: 常見的雲端服務管理工作 | Microsoft Docs
description: 了解如何在 Azure 入口網站中管理雲端服務。這些範例使用 Azure 入口網站。
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
ms.date: 08/02/2016
ms.author: adegeo

---
# 如何管理雲端服務
> [!div class="op_single_selector"]
> * [Azure 入口網站](cloud-services-how-to-manage-portal.md)
> * [Azure 傳統入口網站](cloud-services-how-to-manage.md)
> 
> 

您可以在 Azure 入口網站的 [雲端服務 (傳統)] 區域中管理雲端服務。本文章說明您在管理雲端服務時，可以採取的一些常見動作。其中包括更新、刪除、調整以及將預備部署升級為生產部署。

如需如何調整雲端服務的詳細資訊，可在[這裡](cloud-services-how-to-scale-portal.md)取得。

## 作法：更新雲端服務角色或部署
如果您需要更新雲端服務的應用程式程式碼，請使用雲端服務刀鋒視窗上的 [**更新**]。您可以更新單一角色或所有角色。您可以上傳新的服務封裝或服務組態檔來更新。

1. 在 [Azure 入口網站][Azure 入口網站]，選取您想要更新的雲端服務。這會開啟雲端服務執行個體刀鋒視窗。
2. 在刀鋒視窗中，按一下 [更新] 按鈕。
   
    ![更新按鈕](./media/cloud-services-how-to-manage-portal/update-button.png)
3. 使用新的服務封裝檔 (.cspkg) 和服務組態檔 (.cscfg) 更新部署。
   
    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)
4. **選擇性**更新部署標籤和儲存體帳戶。
5. 如果有任何角色只有一個角色執行個體，請選取 [即使一個或多個角色包含單一執行個體，也要部署]，讓升級能夠繼續。
   
    要讓 Azure 保證服務在雲端服務更新期間有 99.95% 的可用性，每個角色都至少必須有兩個角色執行個體 (虛擬機器)。有兩個角色執行個體，其中一部虛擬機器將會在另一部虛擬機器更新時處理用戶端要求。
6. 在封裝上傳完成之後，選取 [開始部署] 以套用更新。
7. 按一下 [確定] 開始更新服務。

## 作法：交換部署以使預備部署升格為生產部署
當您決定部署新版的雲端服務時，可以在您的雲端服務預備環境中，預備並測試新版本。使用 [交換] 將這兩個部署的 URL 位址互換，並將新版本升級為生產部署。

您可以在 [雲端服務] 頁面或儀表板交換部署。

1. 在 [Azure 入口網站][Azure 入口網站]，選取您想要更新的雲端服務。這會開啟雲端服務執行個體刀鋒視窗。
2. 在刀鋒視窗中，按一下 [**交換**] 按鈕。
   
    ![Cloud Services Swap](./media/cloud-services-how-to-manage-portal/swap-button.png)
3. 隨即開啟下列確認提示。
   
    ![Cloud Services Swap](./media/cloud-services-how-to-manage-portal/swap-prompt.png)
4. 確認部署資訊之後，按一下 [**是**] 交換部署。
   
    交換部署的速度很快，因為唯一變更的是部署的虛擬 IP 位址 (VIP)。
   
    為了節省運算成本，您可以在確認生產部署如預期運作之後刪除預備部署。

## 作法：將資源連結到雲端服務
Azure 入口網站不會像目前 Azure 傳統入口網站一樣將資源連結一起。相反地，會在雲端服務所使用的相同資源群組中部署額外的資源。

## 作法：刪除部署和雲端服務
刪除雲端服務之前，您必須先刪除每個現有的部署。

為了節省運算成本，您可以在確認生產部署如預期運作之後刪除預備部署。您需要為停止的已部署角色執行個體支付運算成本。

使用下列程序，刪除部署或雲端服務。

1. 在 [Azure 入口網站][Azure 入口網站]，選取您想要刪除的雲端服務。這會開啟雲端服務執行個體刀鋒視窗。
2. 在刀鋒視窗中，按一下 [**刪除**] 按鈕。
   
    ![Cloud Services Swap](./media/cloud-services-how-to-manage-portal/delete-button.png)
3. 您可以核取 [**雲端服務及其部署**]，或是選擇 [**生產部署**] 或 [**預備部署**]，即可刪除整個雲端服務。
   
    ![Cloud Services Swap](./media/cloud-services-how-to-manage-portal/delete-blade.png)
4. 按一下底部的 [**刪除**] 按鈕。
5. 若要刪除雲端服務，請按一下 [刪除雲端服務]。然後，在確認提示處按一下 [是]。

> [!NOTE]
> 當刪除雲端服務並且已設定詳細監視時，您必須從儲存體帳戶中手動刪除資料。如需何處可找到這些度量表的相關資訊，請參閱[這篇](cloud-services-how-to-monitor.md)文章。
> 
> 

[Azure 入口網站]: https://portal.azure.com

## 後續步驟
* [雲端服務的一般設定](cloud-services-how-to-configure-portal.md)。
* 了解如何[部署雲端服務](cloud-services-how-to-create-deploy-portal.md)。
* 設定[自訂網域名稱](cloud-services-custom-domain-name-portal.md)。
* 設定 [SSL 憑證](cloud-services-configure-ssl-certificate-portal.md)。

<!---HONumber=AcomDC_0810_2016------>