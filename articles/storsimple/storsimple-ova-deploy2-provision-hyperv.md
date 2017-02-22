---
title: "部署 StorSimple Virtual Array：在 Hyper-V 中佈建"
description: "部署 StorSimple Virtual Array 的第二個教學課程，內容為如何在 Hyper-V 中佈建虛擬裝置。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 38c440e7-81e9-4689-9ec3-c231a499c43e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/11/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 2fddc72747ce97e8985e9f1c84ffdce52cd05e2e


---
# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-hyper-v"></a>部署 StorSimple Virtual Array：在 Hyper-V 中佈建虛擬陣列
![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>概觀
本佈建教學課程適用於執行 2016 年 3 月公開上市 (GA) 版的 Microsoft Azure StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置)。 本教學課程說明如何在於 Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 執行 Hyper-V 的主機系統上佈建 StorSimple Virtual Array。 本文適用於 StorSimple Virtual Arrays 在 Azure 傳統入口網站的部署，以及 Microsoft Azure 政府服務雲端。

您需要系統管理員權限，才能佈建及設定虛擬裝置。 佈建及初始安裝程序可能需要大約 10 分鐘的時間才能完成。

## <a name="provisioning-prerequisites"></a>佈建的必要條件
此處提供在於 Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 執行 Hyper-V 的主機系統上佈建虛擬裝置的必要條件。

### <a name="for-the-storsimple-manager-service"></a>對於 StorSimple Manager 服務
在您開始前，請確定：

* 您已完成 [準備入口網站以使用 StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md)中的所有步驟。
* 您已經從 Azure 入口網站下載適用於 Hyper-V 的虛擬裝置映像。 如需詳細資訊，請參閱〈 [步驟 3：下載虛擬裝置映像](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image)〉。
  
  > [!IMPORTANT]
  > StorSimple Virtual Array 上執行的軟體只能搭配 Storsimple Manager 服務。
  > 
  > 

### <a name="for-the-storsimple-virtual-device"></a>對於 StorSimple 虛擬裝置
在您部署虛擬裝置之前，請確定：

* 您可以存取可用來佈建裝置、在 Windows Server 2008 R2 或更新版本上執行 Hyper-V 的主機系統。
* 主機系統能夠把下列資源專門用來佈建虛擬裝置：
  
  * 至少 4 顆核心。
  * 至少 8 GB 的 RAM。
  * 一個網路介面。
  * 供系統資料使用的 500 GB 虛擬磁碟。

### <a name="for-the-network-in-the-datacenter"></a>針對資料中心內的網路
在您開始之前，請檢閱部署 StorSimple 虛擬裝置的網路需求，並適當地設定資料中心網路。 如需詳細資訊，請參閱 [StorSimple Virtual Array 網路需求](storsimple-ova-system-requirements.md#networking-requirements)。

## <a name="step-by-step-provisioning"></a>佈建的逐步指示
若要佈建及連接到虛擬裝置，您必須執行下列步驟：

1. 確認主機系統有足夠的資源來符合最低的虛擬裝置需求。
2. 在 Hypervisor 中佈建虛擬裝置。
3. 啟動虛擬裝置，並取得 IP 位址。

我們會在下列各節解釋這些步驟。

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements"></a>步驟 1：確認主機系統符合最低的虛擬裝置需求
若要建立虛擬裝置，您將需要：

* 安裝在 Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 SP1 上的 Hyper-V 角色。
* 在已連線至主機的 Microsoft Windows 用戶端上的 Microsoft Hyper-V 管理員。

您必須確定要用來建立虛擬裝置的基本硬體 (主機系統)，能夠把下列資源專門提供給虛擬裝置來使用：

* 至少 4 顆核心。
* 至少 8 GB 的 RAM。
* 一個網路介面。
* 供系統資料使用的 500 GB 虛擬磁碟。

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>步驟 2：在 Hypervisor 中佈建虛擬裝置
請執行下列步驟，以便在您的 Hypervisor 中佈建裝置。

#### <a name="to-provision-a-virtual-device"></a>佈建虛擬裝置
1. 在 Windows Server 主機上，將虛擬裝置映像複製到本機磁碟機中。 這是您透過 Azure 入口網站下載的映像 (VHD 或 VHDX)。 請記下您複製映像的位置，因為稍後將會在程序中使用該資訊。
2. 開啟 [伺服器管理員] 。 按一下右上角的 [工具]，然後選取 [Hyper-V 管理員]。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)
   
   如果您執行的是 Windows Server 2008 R2，請開啟「Hyper-V 管理員」。 在 [伺服器管理員] 中，按一下 [角色] > [Hyper-V] > [Hyper-V 管理員]。
3. 在 [Hyper-V 管理員] 的範圍窗格中，於您的系統節點上按一下滑鼠右鍵以開啟操作功能表，然後按一下 [新增]  >  [虛擬機器]。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)
4. 在「新增虛擬機器精靈」的「開始之前」頁面上，按 [下一步]。
5. 在「指定名稱和位置」頁面上，提供您虛擬裝置的「名稱」。 按 [下一步] 。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)
6. 在「指定世代」頁面上，選擇裝置映像類型，然後按 [下一步]。 如果您使用的是 Windows Server 2008 R2，則不會出現此頁面。
   
   * 如果您已下載適用於 Windows Server 2012 或更新版本的 .vhdx 映像，請選擇 [第 2 代]  。
   * 如果您已下載適用於 Windows Server 2008 R2 或更新版本的 .vhd 映像，請選擇 [第 1 代]  。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)
7. 在「指派記憶體」頁面上，至少指定 **8192 MB** 的「啟動記憶體」，請勿啟用動態記憶體，然後按 [下一步]。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)
8. 在「設定網路功能」頁面上，指定連線到網際網路的虛擬交換器，然後按 [下一步]。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)
9. 在「連接虛擬硬碟」 頁面上，選擇 [使用現有的虛擬硬碟]，指定虛擬裝置映像 (.vhdx 或.vhd) 的位置，然後按 [下一步]。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)
10. 檢閱「摘要」，然後按一下 [結束] 來建立虛擬機器。 但還不要跳到下一步，您還需要新增一些 CPU 核心和第二個磁碟機。 
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)
11. 您需要 4 顆核心才能符合最低需求。 若要新增虛擬處理器，請在 [Hyper-V 管理員] 視窗中選取您的主機系統，然後在右窗格中的 [虛擬機器] 清單下，找出您剛建立的虛擬機器。 選取虛擬機器的名稱並按一下滑鼠右鍵，然後選取 [設定] 。
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)
12. 在「設定」頁面上，按一下左窗格中的 [處理器]。 在右窗格中，將 [虛擬處理器數目]  設定為 4 (或更多)。 按一下 [套用] 。
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)
13. 您也必須新增 500 GB 的虛擬資料磁碟，才能符合最低需求。 在「設定」  頁面中：
    
    1. 在左窗格中，選取 [SCSI 控制器] 。
    2. 在右窗格中，選取 [硬碟]，然後按一下 [新增]。
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)
14. 在「硬碟」頁面上，選取 [虛擬硬碟] 選項，然後按一下 [新增]。 這會啟動「新增虛擬硬碟精靈」 。
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)
15. 在「新增虛擬硬碟精靈」的「開始之前」頁面上，按 [下一步]。
16. 在「選擇磁碟格式」頁面上，接受 [VHDX] 格式預設選項。 按 [下一步] 。 如果您執行的是 Windows Server 2012 R2 或 Windows Server 2008 R2，則不會看到這個畫面。
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)
17. 在「選擇磁碟類型」頁面上，將虛擬硬碟類型設定為 [動態擴充] (建議選項)。 您也可以選擇 [固定大小]  磁碟，但可能需要等待很長一段時間。 建議您不要使用 [差異]  選項。 按 [下一步] 。 請注意，在 Windows Server 2012 R2 和 Windows Server 2012 中，預設值是 [動態擴充]  。 在 Windows Server 2008 R2 中，預設值是 [固定大小] 。
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)
18. 在「指定名稱和位置」頁面上，提供資料磁碟的「名稱」和「位置」(您可以瀏覽至該位置)。 按 [下一步] 。
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)
19. 在「設定磁碟」頁面上，選取 [建立新的空白虛擬硬碟] 選項，然後將大小指定為 **500 GB** (或更多)。 500 GB 是最低需求，您永遠可以佈建更大的磁碟。 請注意，佈建之後您無法擴充或縮小磁碟。 如需有關要佈建磁碟大小的詳細資訊，請檢閱[最佳作法](storsimple-ova-best-practices.md#configuration-best-practices)文件中的「大小」區段。 按 [下一步] 。
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)
20. 在「摘要」頁面上，檢閱虛擬資料磁碟的詳細資料，如果您對這些資料感到滿意，請按一下 [完成] 來建立磁碟。 精靈會隨即關閉，而您的電腦會新增一個虛擬硬碟。
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)
21. 您將會返回「設定」  頁面。 按一下 [確定] 來關閉「設定」頁面並返回 [Hyper-V 管理員] 視窗。
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>步驟 3：啟動虛擬裝置，並取得 IP 位址
請執行下列步驟來啟動您的虛擬裝置，並連線到該虛擬裝置。

#### <a name="to-start-the-virtual-device"></a>啟動虛擬裝置
1. 啟動虛擬裝置。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)
2. 當裝置開始執行之後，選取裝置並按一下滑鼠右鍵，然後選取 [連接] 。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)
3. 您可能需要等待 5 至 10 分鐘，裝置才會準備就緒。 主控台會顯示狀態訊息以表明進度。 裝置就緒之後，請前往 [動作] 。 按 `Ctrl + Alt + Delete` 來登入虛擬裝置。 預設使用者為 *StorSimpleAdmin*，預設密碼為 *Password1*。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)
4. 基於安全上的理由，裝置系統管理員密碼會在您第一次登入時過期。 系統將會提示您變更密碼。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)
   
   請輸入至少包含 8 個字元的密碼。 密碼必須至少符合下列 4 個需求中的 3 個：大寫、小寫、數字和特殊字元。 請重新輸入密碼來加以確認。 系統將會通知您密碼已經變更。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)
5. 密碼變更成功之後，裝置可能會重新啟動。 請等待裝置啟動。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)
   
    畫面將會出現裝置的 Windows PowerShell 主控台及進度列。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)
6. 步驟 6 至 8 僅適用於在非 DHCP 環境中開機的情況。 如果您是在 DHCP 環境中，請略過這些步驟並前往步驟 9。 如果您是在非 DHCP 環境中讓裝置開機，您會看到下列畫面。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)
   
    您現在必須設定網路。
7. 使用 `Get-HcsIpAddress` 命令來列出虛擬裝置上已啟用的網路介面。 如果您的裝置有已啟用的單一網路介面，系統指派給該介面的預設名稱會是 `Ethernet`。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)
8. 使用 `Set-HcsIpAddress` Cmdlet 來設定網路。 範例如下所示：
   
    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`
   
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)
9. 初始安裝程序完成，且裝置已開機之後，您將會看到裝置橫幅文字。 請記下 IP 位址及橫幅文字中的 URL，以便管理裝置。 您將使用此 IP 位址連線到虛擬裝置的 Web UI，以及完成本機安裝和註冊程序。
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)
10. (選擇性) 只有當您要在政府服務雲端部署裝置時，才執行這個步驟。 您現在將在您的裝置上啟用美國聯邦資訊處理標準 (FIPS) 模式。 FIPS 140 標準定義核准美國聯邦政府電腦系統所使用的密碼編譯演算法來保護機密資料。
    
    1. 若要啟用 FIPS 模式，請執行下列 Cmdlet：
       
        `Enter-HcsFIPSMode`
    2. 在您啟用 FIPS 模式之後，重新啟動您的裝置，讓密碼編譯驗證生效。
       
       > [!NOTE]
       > 您可以在您的裝置上啟用或停用 FIPS 模式。 不支援在 FIPS 和非 FIPS 模式之間替換裝置。
       > 
       > 

如果裝置不符合最低設定需求，橫幅文字中會出現錯誤訊息 (如下所示)。 您必須修改裝置設定，讓裝置有足夠的資源來符合最低需求。 然後您就可以將裝置重新啟動，並連線到該裝置。 請參閱 [步驟 1：確認主機系統符合最低的虛擬裝置需求](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements)中的最低組態需求。

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

如果您在使用本機 Web UI 進行初始設定作業時碰到其他任何錯誤，請參閱 [使用本機 Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)中的下列工作流程。

* 執行診斷測試來 [疑難排解 Web UI 安裝程式錯誤](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)。
* [產生記錄檔封裝及檢視記錄檔](storsimple-ova-web-ui-admin.md#generate-a-log-package)。

![影片圖示](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png) **提供的影片**

請觀賞說明如何在 Hyper-V 中佈建 StorSimple 虛擬陣列的影片。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Create-a-StorSimple-Virtual-Array/player]
> 
> 

## <a name="next-steps"></a>後續步驟
* [Set up your StorSimple Virtual Array as a file server (將 StorSimple 虛擬陣列設定為檔案伺服器)](storsimple-ova-deploy3-fs-setup.md)
* [Set up your StorSimple Virtual Array as an iSCSI server (將 StorSimple 虛擬陣列設定為 iSCSI 伺服器)](storsimple-ova-deploy3-iscsi-setup.md)




<!--HONumber=Jan17_HO5-->


