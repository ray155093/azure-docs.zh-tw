---
title: "停用及刪除 StorSimple Virtual Array | Microsoft Docs"
description: "描述如何停用然後刪除 StorSimple 裝置，將其從服務中移除。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: bf5ddb32-da4b-446f-ab91-215e9020e1c8
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d2008fa396405de99c527a84831a6544cdc0fc1d
ms.openlocfilehash: d62b484150a6786f0a6abbff1ca2c172e1ad43db
ms.lasthandoff: 02/28/2017


---
# <a name="deactivate-and-delete-a-storsimple-virtual-array-via-storsimple-manager"></a>透過 StorSimple Manager 停用及刪除 StorSimple Virtual Array
## <a name="overview"></a>概觀
當您停用 StorSimple Virtual Array 時，會切斷裝置與相對應 StorSimple Manager 服務之間的連接。 停用是永久性的作業，而且無法復原。 停用的裝置無法再次向 StorSimple Manager 服務登錄。

您可能需要在下列情況停用及刪除 StorSimple 虛擬裝置：

* 您的裝置已上線，而且您打算容錯移轉這個裝置。 如果您打算升級至較大的裝置，您可能需要執行這項操作。 裝置資料傳輸且容錯移轉完成之後，您就可以刪除裝置。
* 您的裝置已離線，而且您打算容錯移轉這個裝置。 這可能會在有災害時發生，災害的原因是資料中心中斷，您的主要裝置已關閉。 您計劃將裝置容錯移轉至次要裝置。 裝置資料傳輸且容錯移轉完成之後，您可以刪除裝置。
* 您想要解除委任裝置，然後再刪除它。 

當您停用裝置時，將無法再存取儲存在本機的任何資料。 只有儲存在雲端的資料可以復原。 如果您計劃在停用之後保留裝置資料，則您應該在停用裝置之前擷取所有資料的雲端快照集。 這樣可讓您在稍後的階段中復原所有的資料。

本教學課程說明如何：

* 停用裝置 
* 刪除或停用裝置

## <a name="deactivate-a-device"></a>停用裝置
請執行下列步驟，以停用您的裝置。

#### <a name="to-deactivate-the-device"></a>停用裝置
1. 移至 [裝置]  頁面。 選取您想要停用的裝置。
   
    ![選取要停用的裝置](./media/storsimple-ova-deactivate-and-delete-device/deactivate1m.png)
2. 按一下頁面底部的 [停用] 。
   
    ![按一下停用](./media/storsimple-ova-deactivate-and-delete-device/deactivate2m.png)
3. 確認訊息隨即出現。 按一下 [是]  以繼續。 
   
    ![確認停用](./media/storsimple-ova-deactivate-and-delete-device/deactivate3m.png)
   
    停用程序將會啟動，並需要幾分鐘才能完成。
   
    ![停用進行中](./media/storsimple-ova-deactivate-and-delete-device/deactivate4m.png)
4. 停用之後，會重新整理裝置清單。 
   
    ![停用完成](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)
   
    您現在可以刪除此裝置。 

## <a name="delete-the-device"></a>刪除裝置
裝置必須先停用才能刪除。 刪除裝置會將其從與服務連接的裝置清單移除。 服務將不再管理已刪除的裝置。 但是，與裝置相關聯的資料仍然會在雲端中。 請注意，此資料還是會產生費用。 

請完成下列步驟來刪除裝置：

#### <a name="to-delete-the-device"></a>刪除裝置
1. 在 StorSimple Manager 服務上 **裝置** 頁面上，選取您想要刪除的已停用裝置。
   
   ![選取要刪除的裝置](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)
2. 按一下頁面底部的 [ **刪除**]。
   
   ![按一下刪除](./media/storsimple-ova-deactivate-and-delete-device/deactivate6m.png)
3. 系統將提示您進行確認。 輸入裝置名稱，以確認裝置刪除。 請注意，刪除裝置並不會刪除與裝置相關聯的雲端資料。 按一下核取圖示以繼續。
   
   ![Confirm delete](./media/storsimple-ova-deactivate-and-delete-device/deactivate7m.png) 
4. 刪除裝置可能需要數分鐘的時間。 
   
   ![刪除進行中](./media/storsimple-ova-deactivate-and-delete-device/deactivate8m.png)
   
    刪除裝置之後，會重新整理裝置清單。
   
   ![刪除完成](./media/storsimple-ova-deactivate-and-delete-device/deactivate9m.png)

## <a name="next-steps"></a>後續步驟
* 若要了解如何使用 StorSimple Manager 服務，請移至 [使用 StorSimple Manager 服務管理 StorSimple Virtual Array](storsimple-ova-manager-service-administration.md)。 


