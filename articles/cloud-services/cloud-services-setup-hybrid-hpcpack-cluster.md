---
title: "使用 Microsoft HPC Pack 設定混合式 HPC 叢集 | Microsoft Docs"
description: "了解如何使用 Microsoft HPC Pack 和 Azure 設定一個小型的混合式高效能運算 (HPC) 叢集"
services: cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: b11f3e1b-b9e1-4b0d-8455-6607b88814e9
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: a0e80acad4e8db2177e699f90f7296c64b38e718
ms.openlocfilehash: da7102882b9dcf7db1285c0ec004ce1ad3859853
ms.lasthandoff: 02/07/2017


---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>使用 Microsoft HPC Pack 和隨選 Azure 計算節點設定混合式高效能計算 (HPC) 叢集
使用 Microsoft HPC Pack 2012 R2 和 Azure 設定小型的混合式高效能運算 (HPC) 叢集。 此叢集將包含一個內部部署的前端節點 (一部執行 Windows Server 作業系統和 HPC Pack 的電腦)，和一些您視需要部署在 Azure 雲端服務中作為背景工作角色執行個體的計算節點。 然後，您便可以在混合式叢集上執行計算作業。

![Hybrid HPC cluster][Overview] 

本教學課程示範一個有時稱為「將量擴大到雲端」的方法，此方法使用 Azure 中可調整的隨選計算資源來執行大量計算的應用程式。

本教學課程假設您先前沒有使用計算叢集或 HPC Pack 2012 R2 的經驗。 其只是要協助您快速部署一個示範性質的混合式計算叢集。 如需有關使用 HPC Pack 2016，或有關在生產環境中以較大規模部署混合式 HPC Pack 叢集的考量和步驟，請參閱[詳細指引 (英文)](http://go.microsoft.com/fwlink/p/?LinkID=200493)。 如需使用 HPC Pack 的其他案例，包括 Azure 虛擬機器中的自動化叢集部署，請參閱[使用 HPC Pack 在 Azure 中建立及管理 Windows HPC 叢集的選項](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="prerequisites"></a>必要條件
* **Azure 訂用帳戶** - 如果您沒有 Azure 訂用帳戶，只需要幾分鐘就可以建立 [免費帳戶](https://azure.microsoft.com/free/) 。
* **一部執行 Windows Server 2012 R2 或 Windows Server 2012 的內部部署電腦** - 這部電腦將作為 HPC 叢集的前端節點。 如果您目前執行的不是 Windows Server，可以下載並安裝 [評估版](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2)。
  
  * 電腦必須加入 Active Directory 網域。 若為全新安裝 Windows Server 的測試案例，您可以加入 Active Directory 網域服務伺服器角色，並將前端節點電腦升級為新的網域樹系的網域控制站 (請參閱 Windows Server 的文件)。
  * 為了支援 HPC Pack，作業系統必須以下列其中一種語言安裝：英文、日文或簡體中心。
  * 確認已安裝重要及重大更新。
* **HPC Pack 2012 R2** - [下載](http://go.microsoft.com/fwlink/p/?linkid=328024) 最新版本的免費版安裝套件，並將檔案複製到前端節點電腦或網路位置。 選擇與安裝的 Windows Server 語言相同語言的安裝檔。

    >[!NOTE]
    > 如果您想要使用 HPC Pack 2016 而不是 HPC Pack 2012 R2，則需要額外設定。 請參閱[詳細指引](http://go.microsoft.com/fwlink/p/?LinkID=200493)。
    > 
* **網域帳戶** - 必須在前端節點上以本機系統管理員權限設定此帳戶，才能安裝 HPC Pack。
* **連接埠 443 上的 TCP 連線** 。

## <a name="install-hpc-pack-on-the-head-node"></a>在前端節點安裝 HPC Pack
您需先在將作為叢集前端節點、執行 Windows Server 的內部部署電腦上安裝 Microsoft HPC Pack。

1. 使用具備本機系統管理員權限的網域帳戶登入前端節點。
2. 執行 HPC Pack 安裝檔中的 Setup.exe 來啟動 HPC Pack 安裝精靈。
3. 在 [HPC Pack 2012 R2 設定] 畫面上，按一下 [全新安裝或將新功能新增至現有安裝]。
   
    ![HPC Pack 2012 Setup][install_hpc1]
4. 在 [Microsoft 軟體使用者合約] 頁面上，按 [下一步]。
5. 在 [選取安裝類型] 頁面上，按一下 [藉由建立前端節點來建立新 HPC 叢集]，然後按 [下一步]。
   
    ![Select Installation Type][install_hpc2]
6. 精靈會執行數項安裝前的測試。 如果所有測試皆通過，請在 [安裝規則]  on the  。 否則，請檢閱系統提供的資訊，並在您的環境中進行任何必要的變更。 然後重新執行測試，或是視需要重新啟動安裝精靈。
   
    ![Installation Rules][install_hpc3]
7. 在 [HPC DB 設定] 頁面上，確定已為所有 HPC 資料庫選取 [前端節點]，然後按 [下一步]。
   
    ![DB Configuration][install_hpc4]
8. 接受精靈剩餘頁面上的預設選項。 在 [安裝必要元件] 頁面上，按一下 [安裝]。
   
    ![Install][install_hpc6]
9. 安裝完成之後，請取消勾選 [啟動 HPC 叢集管理員]，然後按一下 [完成]。 (您將在稍後的步驟中啟動 HPC 叢集管理員。)
   
    ![完成][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>準備 Azure 訂閱
使用 [Azure 傳統入口網站](https://manage.windowsazure.com) 對您的 Azure 訂用帳戶執行下列步驟。 您必須執行這些步驟，稍後才能從內部部署前端節點部署 Azure 節點。 詳細程序位於後續幾節。

* 上傳管理憑證 (前端節點與 Azure 服務之間的安全連線所需)
* 建立要在其中執行 Azure 節點 (背景工作角色執行個體) 的 Azure 雲端服務
* 建立 Azure 儲存體帳戶
  
  > [!NOTE]
  > 請一併記下您的 Azure 訂用帳戶識別碼，稍後將需要用到。 在傳統入口網站中，按一下 [設定] > [訂用帳戶]可找到此資訊。
  > 
  > 

### <a name="upload-the-default-management-certificate"></a>上傳預設管理憑證
HPC Pack 會在前端節點安裝一個自我簽署憑證 (稱為 Default Microsoft HPC Azure Management 憑證)，您可以將它上傳作為 Azure 管理憑證。 這個憑證是為了方便進行測試及概念證明部署而提供。

1. 從前端節點電腦登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 按一下 [設定] > [管理憑證]。
3. 在命令列上，按一下 [上傳] 。
   
    ![Certificate Settings][upload_cert1]
4. 瀏覽前端節點以找出 C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer 檔案。 然後按一下 [ **檢查** ] 按鈕。
   
    ![Upload Certificate][install_hpc10]

您將在管理憑證清單中看到 **Default HPC Azure Management** 。

### <a name="create-an-azure-cloud-service"></a>建立 Azure 雲端服務
> [!NOTE]
> 為了獲得最佳效能，請在稍後的步驟中，將雲端服務和儲存體帳戶建立在同一個地理區域中。
> 
> 

1. 在傳統入口網站的命令列上，按一下 [新增] 。
2. 按一下 [計算] > [雲端服務] > [快速建立]。
3. 輸入雲端服務的 URL，然後按一下 [建立雲端服務] 。
   
    ![Create Service][createservice1]

### <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶
1. 在傳統入口網站的命令列上，按一下 [新增] 。
2. 按一下 [資料服務] > [儲存體] > [快速建立]。
3. 輸入帳戶的 URL，然後按一下 [建立儲存體帳戶] 。
   
    ![Create Storage][createstorage1]

## <a name="configure-the-head-node"></a>設定前端節點
若要使用 HPC 叢集管理員來部署 Azure 節點及提交工作，請先執行一些必要的叢集設定步驟。

1. 在前端節點上，啟動 HPC 叢集管理員。 如果顯示 [選取前端節點] 對話方塊，請按一下 [本機電腦]。 [Deployment To-do List]  隨即出現。
2. 在 [必要部署工作] 底下，按一下 [設定您的網路]。
   
    ![Configure Network][config_hpc2]
3. 在 [網路設定精靈] 中，選取 [All nodes only on an enterprise network]  \(拓撲 5)。
   
    ![Topology 5][config_hpc3]
   
   > [!NOTE]
   > 這是示範性質的最簡單組態，因為前端節點只需要一張網路介面卡，即可連線到 Active Directory 和網際網路。 本教學課程並未涵蓋需要更多網路的叢集案例。
   > 
   > 
4. 按 [下一步]  以接受精靈剩餘頁面上的預設值。 然後，在 [檢閱] 索引標籤上，按一下 [設定] 以完成網路設定。
5. 在 [部署待辦事項清單] 中，按一下 [提供安裝認證]。
6. 在 [Installation Credentials]  對話方塊中，輸入您用來安裝 HPC Pack 之網域帳戶的認證。 然後按一下 [確定] 。
   
    ![Installation Credentials][config_hpc6]
   
   > [!NOTE]
   > HPC Pack 服務只會將安裝認證用於部署已加入網域的計算節點。 您在本教學課程中新增的 Azure 節點未加入網域。
   > 
   > 
7. 在 [部署待辦事項清單] 中，按一下 [設定新節點的命名]。
8. 在 [指定節點命名序列] 對話方塊中，接受預設的命名序列，然後按一下 [確定]。
   
    ![Node Naming][config_hpc8]
   
   > [!NOTE]
   > 命名序列只會為加入網域的計算節點產生名稱。 Azure 背景工作節點的名稱是自動產生的。
   > 
   > 
9. 在 [部署待辦事項清單] 中，按一下 [建立節點範本]。 您將使用節點範本將 Azure 節點新增至叢集。
10. 在 [Create Node Template Wizard] 中，執行下列動作：
    
    a. 在 [選擇節點範本類型] 頁面上，按一下 [Azure 節點範本]，然後按 [下一步]。
    
    ![Node Template][config_hpc10]
    
    b. 按 [下一步] 以接受預設範本名稱。
    
    c. 然後，在 [管理憑證]  。 然後，在 [管理憑證] 中，按一下 [瀏覽] 並選取 [預設 HPC Azure 管理]。 然後按 [下一步] 。
    
    ![Node Template][config_hpc12]
    
    d. 在 [提供服務資訊] 頁面上，選取您在先前步驟中建立的雲端服務和儲存體帳戶。 然後按 [下一步] 。
    
    ![Node Template][config_hpc13]
    
    e. 按 [下一步]  以接受精靈剩餘頁面上的預設值。 然後，在 [檢閱] 索引標籤上，按一下 [建立] 以建立節點範本。
    
    > [!NOTE]
    > 根據預設，Azure 節點範本包含可讓您使用 HPC 叢集管理員手動啟動 (佈建) 和停止節點的設定。 您可以選擇性地設定排程來自動啟動和停止 Azure 節點。
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>將 Azure 節點新增至叢集
您現在可以使用節點範本將 Azure 節點新增至叢集。 將節點新增至叢集會儲存其組態資訊，讓您可以隨時在雲端服務中啟動 (佈建) 這些節點作為角色執行個體。 在角色執行個體於雲端服務中執行之後，您的訂閱才須為 Azure 節點付費。

在本教學課程中，您將需要新增兩個小型節點。

1. 在 HPC 叢集管理員中，於 [節點管理] \(在最新版本的 HPC Pack 中稱為**資源管理**) 的 [動作] 窗格中，按一下 [新增節點]。
   
    ![Add Node][add_node1]
2. 在「新增節點精靈」中的 [選取部署方法] 頁面上，按一下 [新增 Windows Azure 節點]，然後按 [下一步]。
   
    ![Add Azure Node][add_node1_1]
3. 在 [指定新節點] 頁面上，選取您先前建立的 Azure 節點範本 (預設稱為 [預設 Azure 節點範本])。 接著，指定 **2** 個大小為 [小型] 的節點，然後按 [下一步]。
   
    ![Specify Nodes][add_node2]
   
    如需可用大小的詳細資訊，請參閱 [雲端服務的大小](cloud-services-sizes-specs.md)。
4. 在 [完成新增節點精靈] 頁面上，按一下 [完成]。
   
     HPC 叢集管理員中現在會出現兩個 Azure 節點，名為 **AzureCN-0001** 和 **AzureCN-0002**。 兩者皆處於 [未部署]  狀態。
   
    ![Added Nodes][add_node3]

## <a name="start-the-azure-nodes"></a>啟動 Azure 節點
當您想要使用 Azure 中的叢集資源時，請使用 HPC 叢集管理員來啟動 (佈建) Azure 節點並讓節點上線。

1. 在 HPC 叢集管理員中，於 [節點管理] \(在最新版本的 HPC Pack 中稱為**資源管理**) 中按一或兩個節點，然後在 [動作] 窗格中按一下 [啟動]。
   
   ![Start Nodes][add_node4]
2. 在 [停止 Windows Azure 節點] 對話方塊中，按一下 [啟動]。
   
    ![Start Nodes][add_node5]
   
    節點會轉換至 [正在佈建]  狀態。 檢視佈建記錄檔以追蹤佈建進度。
   
    ![Provision Nodes][add_node6]
3. 幾分鐘之後，Azure 節點就會完成佈建並處於 [離線]  狀態。 在此狀態下，角色執行個體已在執行，但還沒準備要接受叢集工作。
4. 若要確認角色執行個體已在執行，請在[傳統入口網站](https://manage.windowsazure.com)中按一下 [雲端服務] > 您雲端服務的名稱 > [執行個體]。
   
    ![Running Instances][view_instances1]
   
    您會看到服務中有兩個執行中的背景工作角色執行個體。 HPC Pack 也會自動部署兩個 **HpcProxy** 執行個體 (中型大小) 以處理前端節點與 Azure 之間的通訊。
5. 若要讓 Azure 節點上線以執行叢集工作，請選取節點，按一下滑鼠右鍵，然後按一下 [上線] 。
   
    ![Offline Nodes][add_node7]
   
    HPC 叢集管理員會指出節點處於 [線上]  狀態。

## <a name="run-a-command-across-the-cluster"></a>在叢集執行命令
若要追蹤安裝，請使用 HPC Pack **clusrun** 命令在一或多個叢集節點上執行命令或應用程式。 其中一個簡單的範例就是使用 **clusrun** 來取得 Azure 節點的 IP 設定。

1. 在前端節點上，開啟命令提示字元。
2. 輸入以下命令：
   
    `clusrun /nodes:azurecn* ipconfig`
3. 您將看到類似以下的輸出：
   
    ![clusrun][clusrun1]

## <a name="run-a-test-job"></a>執行測試工作
現在提交一個在混合式叢集上執行的測試作業。 這個範例是非常簡單的參數式掃掠作業 (一種本質平行計算)。 本例會執行使用 **set /a** 命令將整數加入自己本身的子工作。 叢集中的所有節點皆參與完成從 1 到 100 之整數的子工作。

1. 在 HPC 叢集管理員中，於 [作業管理] 的 [動作] 窗格中，按一下 [新增參數式掃掠作業]。
   
    ![New Job][test_job1]
2. 在 [新增參數式掃掠作業] 對話方塊中，於 [命令列] 中輸入 `set /a *+*` (覆寫出現的預設命令列)。 保留其餘設定的預設值，然後按一下 [提交]  提交工作。
   
    ![Parametric Sweep][param_sweep1]
3. 當工作完成時，按兩下 [My Sweep Task]  工作。
4. 按一下 [檢視工作] ，然後按一下子工作以檢視該子工作的計算結果輸出。
   
    ![Task Results][view_job361]
5. 若要查看是哪個節點執行該子工作的計算，請按一下 [已配置的節點]。 (您的叢集可能會顯示不同的節點名稱。)
   
    ![Task Results][view_job362]

## <a name="stop-the-azure-nodes"></a>停止 Azure 節點
試驗完叢集之後，請停止 Azure 節點，以避免給您的帳戶產生不必要的費用。 這樣會停止雲端服務並移除 Azure 角色執行個體。

1. 在 HPC 叢集管理員中，於 [節點管理] \(在最新版本的 HPC Pack 中稱為**資源管理**) 中，選取這兩個 Azure 節點。 然後，在 [動作] 窗格中，按一下 [停止]。
   
    ![Stop Nodes][stop_node1]
2. 在 [停止 Windows Azure 節點] 對話方塊中，按一下 [停止]。
   
    ![Stop Nodes][stop_node2]
3. 節點會轉換至 [正在停止]  狀態。 幾分鐘之後，HPC 叢集管理員就會顯示這些節點為 [未部署] 狀態。
   
    ![Not Deployed Nodes][stop_node4]
4. 若要確認角色執行個體已不再於 Azure 中執行，請在[傳統入口網站](https://manage.windowsazure.com)中按一下 [雲端服務] > 「您雲端服務的名稱」 > [執行個體]。 將不會有任何執行個體部署於生產環境中。
   
    ![No Instances][view_instances2]
   
    這樣就完成了教學課程。

## <a name="next-steps"></a>後續步驟
* 瀏覽 [HPC Pack](https://technet.microsoft.com/library/cc514029) 文件。
* 若要設定較大規模的混合式 HPC Pack 叢集部署，請參閱 [Burst to Azure with Microsoft HPC Pack (使用 Microsoft HPC Pack 高載至 Azure 背景工作角色執行個體)](http://go.microsoft.com/fwlink/p/?LinkID=200493)。
* 如需在 Azure 中建立 HPC Pack 叢集的其他方式，包括使用 Azure Resource Manager 範本，請參閱[使用 HPC Pack 在 Azure 中建立及管理 Windows HPC 叢集的選項](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 如需 Azure 中大量計算範圍和 HPC 雲端方案的詳細資訊，請參閱 [Azure 中的大量計算：Batch 和高效能計算 (HPC) 的技術資源](../batch/big-compute-resources.md) 。

[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc2.png
[install_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc3.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[upload_cert1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/upload_cert1.png
[createstorage1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createstorage1.png
[createservice1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createservice1.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node5]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node5.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node2.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png

