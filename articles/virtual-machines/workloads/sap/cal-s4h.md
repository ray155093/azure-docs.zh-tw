---
title: "在 Azure VM 上部署 SAP S/4HANA 或 BW/4HANA | Microsoft Docs"
description: "在 Azure VM 上部署 SAP S/4HANA 或 BW/4HANA"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 2a645e6ab50b7b764a56bb7a79ad5cf295881214
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-sap-s4hana-or-bw4hana-on-microsoft-azure"></a>在 Microsoft Azure 上部署 SAP S/4HANA 或 BW/4HANA
本文說明如何使用 SAP Cloud Appliance Library (SAP CAL) 3.0 在 Microsoft Azure 上部署 S/4HANA。 從程序觀點來看，部署其他 SAP HANA 型解決方案 (例如 BW/4 HANA) 也是以相同的方式運作。 您只需選取不同的解決方案即可。

> [!NOTE]
如需有關 SAP Cloud Appliance Library 的詳細資訊，請參閱[它們的網站首頁](https://cal.sap.com/)。 SAP 也有提供一個關於 [SAP Cloud Appliance Library 3.0 (英文)](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience) 的部落格。

## <a name="step-by-step-process-to-deploy-the-solution"></a>部署解決方案的逐步程序

下列螢幕擷取畫面顯示如何在 Azure 上部署 S/4HANA。 對於 BW/4 HANA 等其他解決方案，此程序的運作方式相同。

第一個螢幕擷取畫面顯示 Azure 上可用的所有 SAP CAL HANA 型解決方案。 請注意位於底部的「SAP S/4HANA 內部部署版本」。

![SAP Cloud Appliance Library [Solutions] (解決方案) 視窗的螢幕擷取畫面](./media/cal-s4h/s4h-pic-1b.jpg)

首先，請建立新的 SAP CAL 帳戶。 在 [Accounts] (帳戶) 中，您會看到兩個 Azure 選項：Microsoft Azure 和由 21Vianet 操作的 Azure 選項。 針對此範例，請選擇 [Microsoft Azure]。

![SAP Cloud Appliance Library [Accounts] (帳戶) 視窗的螢幕擷取畫面](./media/cal-s4h/s4h-pic-2.jpg)

接著，輸入可在 Azure 入口網站上找到的 Azure 訂用帳戶 ID。 然後，下載 Azure 管理憑證。

![SAP Cloud Appliance Library [Accounts] (帳戶) 視窗的螢幕擷取畫面](./media/cal-s4h/s4h-pic3b.jpg)

> [!NOTE]
若要尋找您的 Azure 訂用帳戶 ID，您應該使用 Azure 傳統入口網站，而不是較新的 Azure 入口網站。 這是因為 SAP CAL 尚未針對新模型進行調整，而仍然需要傳統入口網站才能使用管理憑證。

以下螢幕擷取畫面顯示傳統入口網站。 從 [設定]，選取 [訂用帳戶] 索引標籤，以尋找要在 SAP CAL [Accounts] (帳戶) 視窗中輸入的訂用帳戶 ID。

![Azure 傳統入口網站的螢幕擷取畫面](./media/cal-s4h/s4h-pic4b.jpg)

從 [設定]，切換到 [管理憑證] 索引標籤。 上傳管理憑證可賦予 SAP CAL 權限，讓它能夠在客戶訂用帳戶內建立虛擬機器。 (您要上傳的是先前從 SAP CAL 下載的管理憑證)。

![Azure 傳統入口網站中 [管理憑證] 索引標籤的螢幕擷取畫面](./media/cal-s4h/s4h-pic5.jpg)

此時會跳出一個對話方塊，供您選取已下載的憑證檔案。

![[上傳管理憑證] 對話方塊的螢幕擷取畫面](./media/cal-s4h/s4h-pic8.jpg)

上傳憑證之後，即可在 SAP CAL 內測試 SAP CAL 與 Azure 訂用帳戶之間的連線。 應該會跳出一則指出此連線有效的訊息。

![SAP Cloud Appliance Library [Accounts] (帳戶) 視窗的螢幕擷取畫面](./media/cal-s4h/s4h-pic9.jpg)

接著，選取應該部署的解決方案，然後建立執行個體。
輸入執行個體名稱、選擇 Azure 區域，然後定義解決方案的主要密碼。

![SAP Cloud Appliance Library [Solutions] (解決方案) 視窗的螢幕擷取畫面](./media/cal-s4h/s4h-pic10.jpg)

在一段時間之後，視解決方案的大小和複雜性 (SAP CAL 會提供預估) 而定，解決方案將會顯示為作用中並可供使用。

![SAP Cloud Appliance Library [Instances] (執行個體) 視窗的螢幕擷取畫面](./media/cal-s4h/s4h-pic11.jpg)

您可以查看解決方案的一些詳細資料，例如已部署的 VM 類型。 在此案例中，已建立三個不同大小和用途的 Azure VM。

![SAP Cloud Appliance Library [Instances] (執行個體) 視窗的螢幕擷取畫面](./media/cal-s4h/s4h-pic12.jpg)

在 Azure 傳統入口網站中，可以找到這些虛擬機器，其開頭與 SAP CAL 中所提供的執行個體名稱相同。

![顯示 Azure 傳統入口網站中三部虛擬機器的螢幕擷取畫面](./media/cal-s4h/s4h-pic13.jpg)

現在可以使用 SAP CAL 入口網站中的 [Connect] (連接) 按鈕來連接到解決方案。 對話方塊會包含一個使用者指南連結，其中說明與該解決方案搭配運作的所有預設認證。

![[Connect to the Instance] (連接到執行個體) 對話方塊的螢幕擷取畫面](./media/cal-s4h/s4h-pic14b.jpg)

另一個選項是登入用戶端 Windows VM，然後啟動預先設定的 SAP GUI (舉例來說)。

![預先設定之 SAP GUI 的螢幕擷取畫面](./media/cal-s4h/s4h-pic15.jpg)

