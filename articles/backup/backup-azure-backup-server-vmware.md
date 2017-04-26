---
title: "使用 Azure 備份伺服器保護 VMware 伺服器 | Microsoft Docs"
description: "使用 Azure 備份伺服器將 VMware 伺服器備份至 Azure 或磁碟。 使用本文來保護您的 VMware 工作負載。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/28/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 779841483e72b42725354d85a0f593aee7db8278
ms.lasthandoff: 04/11/2017


---
# <a name="back-up-vmware-server-to-azure"></a>將 VMware 伺服器備份到 Azure

本文說明如何將 VMware 伺服器連線至 Azure 備份伺服器，以便您將 VMware 伺服器內容備份到雲端。 本文假設您已安裝 Azure 備份伺服器。

## <a name="create-secure-connection-to-vmware-server"></a>建立 VMware 伺服器的安全連線

若要保護 VMware 伺服器，Azure 備份伺服器必須能夠安全地連線到 VMware 伺服器。 若要啟用安全連線，請在 VMware 伺服器和 Azure 備份伺服器上安裝有效的憑證。

當您連線到 VMware 伺服器時，如果 URL 不安全，您需要匯出憑證，網站的連線才會安全。
![VMware 伺服器的不安全連線範例](./media/backup-azure-backup-server-vmware/unsecure-url.png)

1. 按一下 https (含刪除線)，然後在快顯功能表上，按一下 [詳細資料] 連結。

  視您的瀏覽器而定，您可能需要按一下 [設定] > [更多工具] > [開發人員工具]，然後選取 [安全性] 索引標籤。

  ![不安全的連線錯誤訊息範例](./media/backup-azure-backup-server-vmware/security-tab.png)

2. 在 [安全性] 索引標籤上的 [詳細資料] 資訊中，按一下 [檢視憑證]。

  ![不安全的連線錯誤訊息範例](./media/backup-azure-backup-server-vmware/security-tab-view-certificate.png)

  [憑證] 對話方塊隨即開啟。

3. 在 [憑證] 對話方塊中按一下 [憑證路徑] 索引標籤。  

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/certificate-certification-path.png)

  醒目提示的憑證不受信任，因為在此憑證的情況下，找不到簽發者。 憑證不受信任可能有其他原因。

4. 若要將憑證匯出至本機電腦，請按一下 [詳細資料] 索引標籤，然後按一下 [複製到檔案]。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/certificate-details-tab.png)

  [憑證匯出精靈] 隨即開啟。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/certificate-wizard1.png)

  按 [下一步] 繼續執行精靈。

5. 在 [匯出檔案格式] 畫面上，指定您偏好的憑證格式。 如果您沒有慣用的格式，請接受憑證的預設檔案格式，然後按 [下一步]。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/certificate-wizard1b.png)

6. 在 [要檔案的匯出] 畫面上，指定憑證的名稱，然後按一下 [瀏覽] 以選擇本機電腦上要儲存憑證的位置。 將憑證儲存至您可以找到的地方。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/certificate-wizard2.png)

7. 匯出憑證之後，請移至您儲存它的位置，以滑鼠右鍵按一下憑證，然後從功能表中選取 [安裝憑證]。

  [憑證匯入精靈] 隨即開啟。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/cert-import-wizard1.png)

8. 在 [憑證匯入精靈] 中，選取 [本機電腦] 作為憑證目的地，然後按 [下一步] 繼續執行。

9. 在 [憑證存放區] 上，選取 [將所有憑證放入以下的存放區]，然後按一下 [瀏覽]。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

  [選取憑證存放區] 對話方塊隨即開啟。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/cert-store.png)

  選擇憑證的目的地資料夾，然後按一下 [確定]。 如果您不知道要使用哪個資料夾，請選擇 [受信任的根憑證授權單位]。 所選的目的地資料夾會出現在 [憑證存放區] 對話方塊中。 按 [下一步] 以匯出憑證。

10. 在 [完成憑證匯入精靈] 畫面上，確認憑證是否位於所要的資料夾中，然後按一下 [完成] 以完成精靈。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

  隨即出現一個對話方塊，讓您知道匯入是否成功。

11. 登入 VMware VM，檢查您有 VMware 伺服器的安全連線。 Azure 備份伺服器會透過安全的 HTTPs 通道連線至 VMware 伺服器。 如果您的組織中有安全的界限，而且不想啟用 HTTPs 通訊協定，則會透過登錄停用安全的通訊。 不過，建議您在 Azure 備份伺服器和 VMware 伺服器上安裝憑證，以啟用安全的通訊。


## <a name="create-role-and-user-account-on-vmware-server"></a>在 VMware 伺服器上建立角色和使用者帳戶

Azure 備份伺服器藉由驗證所指定 VMware 使用者的認證來與遠端 VMware 伺服器通訊。 Azure 備份伺服器會驗證所有備份作業的 VMware 使用者認證。 使用 Azure 備份伺服器的 [新增實際執行伺服器] 精靈，讓 Azure 備份伺服器能夠與 VMware 伺服器進行安全通訊。 設定 Azure 備份伺服器與下列各項之間的關聯性共有三個階段： 

- 建立具有指派權限的使用者角色
- 建立具有認證的使用者帳戶 - 使用者名稱和密碼
- 將 VMware 伺服器使用者帳戶新增至 Azure 備份伺服器

這三個階段均透過 [新增實際執行伺服器] 精靈進行。 使用者名稱和密碼組會儲存為認證。


### <a name="create-user-role-and-add-privileges"></a>建立使用者角色並新增權限
在 Azure 備份伺服器憑證中指定的 VMware 使用者帳戶必須具有某些關聯的權限。 不過，權限會與使用者角色相關聯，因此我們會先建立使用者角色，然後將特定權限新增至該角色。 與使用者角色相關聯的權限適用於備份管理員。

1. 若要建立新的 VMware 使用者角色，請登入您的 VMware 伺服器，然後按一下 [導覽] 面板上的 [管理]。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. 若要建立新的角色，在 [管理] 區段中選取 [角色]，然後在 [角色] 面板中按一下 [新增角色] 圖示(+ 符號)。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

  [建立角色] 對話方塊隨即開啟。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. 在 [建立角色] 對話方塊的 [角色名稱] 欄位中，輸入角色的名稱。 在此範例中，我們將使用 BackupAdminRole 這個名稱。 角色名稱可以是您喜歡的名稱，但此名稱應該可辨識該角色。

4. 選取權限以套用至使用者角色。 選取下列清單中的權限。 選取權限時，按一下標籤上的＞形箭號，以展開父代並檢視子權限。 您不需要選取父代權限內的所有子權限。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  - Privilege.Datastore.AllocateSpace.label
  - Privilege.Global.ManageCustomerFields.label
  - privilege.Network.Assign.label
  - Privilege.VirtualMachine.Config.AddNewDisk.label
  - Privilege.VirtualMachine.Config.AdvanceConfig.label
  - Privilege.VirtualMachine.Config.ChangeTracking.label
  - Privilege.VirtualMachine.Config.HostUSBDevice.label
  - Privilege.VirtualMachine.Config.SwapPlacement.label  
  - Privilege.VirtualMachine.Interact.PowerOff.label
  - Privilege.VirtualMachine.Inventory.Create.label
  - Privilege.VirtualMachine.Provisioning.DiskRandomRead.summary
  - Privilege.VirtualMachine.State.CreateSnapshot.label
  - Privilege.VirtualMachine.State.RemoveSnapshot.label
</br>

  選取權限後，按一下 [確定]。 新的角色會出現在 [角色] 面板上的清單中。

### <a name="create-a-user-account-and-assign-permissions"></a>建立使用者帳戶並指派權限

一旦您已定義具有權限的使用者角色，請建立使用者帳戶。 當您建立使用者帳戶時，您會將它指派給特定使用者角色，讓帳戶有相關聯的權限。 使用者帳戶具有名稱和密碼，可提供用於驗證的認證。

1. 若要建立新的使用者帳戶，請在 VMware 伺服器的 [導覽] 窗格上，按一下 [使用者和群組]。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

  [使用者和群組] 面板隨即出現。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. 在 [VMware 使用者和群組] 面板的 [使用者] 索引標籤上，按一下 [新增使用者] 圖示 (+ 符號)。

  [新增使用者] 對話方塊隨即開啟。

3. 您建立的使用者帳戶包含作為認證的使用者名稱和密碼組。 在 [新增使用者] 對話方塊中，填妥欄位，然後按一下 [確定]。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

  新的使用者帳戶會出現在清單中。

4. 您現在已建立使用者帳戶，請將它與使用者角色 (具有所要權限) 產生關聯。 在 [導覽] 窗格上，按一下 [通用權限]。 在 [通用權限] 面板上，按一下 [管理] 索引標籤，然後按一下 [新增] 圖示 (+ 符號)。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

  [通用權限根目錄 - 新增權限] 對話方塊隨即開啟。

5. 在 [通用權限根目錄 - 新增權限] 對話方塊中，按一下 [新增] 來選擇使用者或群組。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

  [選取使用者/群組] 對話方塊隨即開啟。

6. 在 [選取使用者/群組] 對話方塊中，選取您所建立的使用者帳戶，然後按一下 [新增]。 選取的使用者帳戶會出現在 [使用者] 欄位中。 使用者名稱會出現在 [使用者] 欄位中，其格式為*網域*`\`*使用者名稱*。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

  按一下 [確定] 將選取的使用者新增至 [新增權限] 對話方塊。

7. 您現在已找出使用者，請將使用者指派給角色。 在 [指派的角色] 區域中，從下拉式功能表中選取角色並按一下 [確定]。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  在 [通用權限] 的 [管理] 索引標籤上，新的使用者帳戶和相關聯的角色會出現在清單中。


### <a name="add-the-vmware-user-account-credentials-to-azure-backup-server"></a>將 VMware 使用者帳戶認證新增至 Azure 備份伺服器

將 VMware 伺服器新增至 Azure 備份伺服器之前，先確定您已安裝 [Microsoft Azure 備份伺服器的 Update 1](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server)。

1. 按一下下列圖示 (位於伺服器桌面) 以開啟 Azure 備份伺服器主控台。

  ![Azure 備份伺服器圖示](./media/backup-azure-backup-server-vmware/mabs-icon.png)

  如果您在桌面上找不到此圖示，您可以從已安裝的應用程式清單開啟 Azure 備份伺服器。 在安裝的應用程式清單中，Azure 備份伺服器應用程式名為 Microsoft Azure 備份。

2. 在 Azure 備份伺服器主控台中，按一下 [管理]，然後按一下 [實際執行伺服器]，然後在工具功能區中按一下 [管理 VMware]。

  ![MABS 主控台](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

  [管理認證] 對話方塊隨即開啟。

  ![MABS 管理認證對話方塊](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. 在 [管理認證] 對話方塊中，按一下 [新增] 以開啟 [新增認證] 對話方塊。

4. 在 [新增認證] 對話方塊中，輸入新認證的名稱和描述。 使用者名稱和密碼應該與您在 VMware 伺服器中建立使用者帳戶時所用的使用者名稱和密碼相同。

  ![MABS 管理認證對話方塊](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog.png)

  按一下 [新增] 將新的認證新增至 Azure 備份伺服器。 新的認證會出現在 [管理認證] 對話方塊的清單中。
  ![MABS 管理認證對話方塊](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. 按一下右上角的 **X** 以關閉 [管理認證] 對話方塊。


## <a name="add-vmware-server-to-azure-backup-server"></a>將 VMware 伺服器新增至 Azure 備份伺服器

若要開啟 [新增實際執行伺服器] 精靈：

1. 在 Azure 備份伺服器主控台中，依序按一下 [管理][實際執行伺服器] 及 [新增]。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

  [新增實際執行伺服器精靈] 隨即開啟。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. 在 [選取實際執行伺服器類型] 畫面上，選取 [VMware 伺服器]，然後按 [下一步]。

3. 在 [伺服器名稱/IP 位址] 中，指定 VMware 伺服器的完整網域名稱 (FQDN) 或 IP 位址。 如果所有 ESXi 伺服器均由相同的 Vcenter 管理，您可以輸入 VMware 名稱。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. 在 [SSL 連接埠] 對話方塊中，輸入用來與 VMware 伺服器通訊的連接埠。 使用連接埠 443，這是預設連接埠，除非您知道需要不同的連接埠。

5. 在 [指定認證] 對話方塊中，您可以建立新的認證，或選取現有的認證。 在上一個區段中，您已建立認證。 選取此認證。

  如果沒有可用的認證，或您需要建立新的認證，請按一下 [新增認證]，建立新認證，然後按一下 [確定]。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/specify-new-cred.png)

6. 按一下 [新增] 將 VMware 伺服器新增至 [新增的 VMware 伺服器] 清單，然後按 [下一步] 移至精靈的下一個畫面。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. 在 [工作] 畫面中，按一下 [新增] 將指定的 VMware 伺服器新增至 Azure 備份伺服器。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  由於 VMware 伺服器備份是無代理程式的備份，所以幾秒內就會進行伺服器新增。 您可以重複本節先前的步驟，將多部 VMware 伺服器新增至 Azure 備份伺服器。

您現在已將 VMware 伺服器新增到 Azure 備份伺服器，下一個步驟是建立保護群組。 保護群組可指定短期或長期保留的各種詳細資料，而且這是您定義及套用備份原則的地方。 備份原則包含執行備份的排程以及備份的內容。


## <a name="configure-a-protection-group-to-back-up-vmware-server"></a>設定保護群組以備份 VMware 伺服器

如果您未曾使用 System Center Data Protection Manager 或 Azure 備份伺服器，請參閱[規劃磁碟備份](https://technet.microsoft.com/library/hh758026.aspx)主題，以準備您的硬體環境。 您已檢查過您有適當的儲存體後，請使用 [建立新保護群組] 精靈來新增特定 VM。

1. 在 Azure 備份伺服器主控台中，按一下 [保護]，然後按一下工具功能區中的 [新增]，以開啟 [建立新保護群組] 精靈。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

  [建立新保護群組] 精靈隨即開啟。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/protection-wizard.png)

  按 [下一步] 前往 [選取保護群組類型] 畫面。

2. 在 [選取保護群組類型] 畫面上，選取 [伺服器]，然後按 [下一步]。

3. 在 [選擇群組成員] 畫面上，您可以看到可用的成員以及已選取的成員。 選取您想要保護的成員，然後按 [下一步] 。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/server-add-to-selected-members.png)

  選取成員時，如果您選取的資料夾包含其他資料夾或 VM，也會選取這些資料夾和 VM。 在父資料夾中包含資料夾和 VM 稱為資料夾層級保護。 您可以取消選取核取方塊，以排除任何資料夾或 VM。

  如果 VM 或包含 VM 的資料夾已受到 Azure 保護，您就無法再次選取該 VM。 也就是說，一旦 VM 受到 Azure 保護，就無法再次受到保護，這可防止針對一部 VM 建立重複的復原點。 如果您想要查看哪部 Azure 備份伺服器已經保護成員，請將滑鼠移到成員上，以查看保護伺服器的名稱。

4. 在 [選取資料保護方式] 畫面上，輸入保護群組的名稱。 然後，針對 [保護方法]，選取您想要備份資料的位置。 資料會備份到磁碟進行短期保護。 資料會備份到雲端 (Azure) 進行長期保護。 按 [下一步] 以繼續進行短期保護範圍。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. 在 [指定短期目標] 畫面上，針對 [保留範圍]，指定您想要保留復原點「儲存到磁碟」的天數。 如果您想要變更取得復原點的時間和天數，請按一下 [修改]。 短期復原點是完整備份。 而不是增量備份。 當您滿意短期目標時，請按 [下一步]。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. 在 [檢閱磁碟配置] 畫面上，檢閱並視需要修改 VM 的磁碟空間。 建議的磁碟配置是以上一個畫面中指定的保留範圍、工作負載類型及受保護的資料大小 (在步驟 3 中識別) 為基礎。  

  - 資料大小 - 保護群組中的資料大小。
  - 磁碟空間 - 針對保護群組建議的磁碟空間數量。 如果您想要修改此設定，您配置的總空間應該稍微大於您預估每個資料來源將成長的數量。
  - 共置資料 - 如果您啟用共置，保護中的多個資料來源可以對應至單一複本和復原點磁碟區。 不支援所有工作負載的共置。
  - 自動成長 - 如果您啟用此設定，而且受保護群組中的資料成長超過初始配置，則 DPM 會嘗試增加 25%的磁碟大小。
  - 儲存體集區詳細資料 - 顯示儲存體集區的目前狀態，包括總計和剩餘的磁碟大小。

    ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

  當您完成時，請按 [下一步]。

7. 在 [選擇複本的建立方式] 畫面上，指定您要在 Azure 備份伺服器上產生受保護資料之初始副本或複本的方式。

  預設值是 [自動透過網路] 和 [立即]。 如果您使用預設值，建議您指定離峰時間。 選擇 [稍後] 並指定日期與時間。

  對於大量資料或較差的網路狀況，請考慮使用卸除式媒體來離線複寫資料。

  做好選擇後，請按 [下一步]。

  ![建立新保護群組精靈](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. 在 [一致性檢查選項] 畫面上，選取如何及何時自動執行一致性檢查。 當複本資料變得不一致時，或依據設定的排程，您可以執行一致性檢查。

  如果您不想設定自動一致性檢查，您可以用滑鼠右鍵按一下 Azure 備份伺服器主控台的 [保護] 區域中的保護群組，然後選取 [執行一致性檢查]，隨時執行手動檢查。

  按 [下一步] 移至下一個畫面。

9. 在 [指定線上保護資料] 畫面上，選取您想要保護的資料來源。 您可以個別地選取成員，或按一下 [全選] 來選擇所有成員。 選擇成員後，請 [下一步]*。

  ![建立新保護群組精靈](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. 在 [指定線上備份排程] 畫面上，指定用於從磁碟備份產生復原點的排程。 產生復原點後，它會傳輸至 Azure 中的復原服務保存庫。 當您滿意線上備份排程時，請 [下一步]。

  ![建立新保護群組精靈](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. 在 [指定線上保留原則] 畫面上，指出您要在 Azure 中保留備份資料的時間長度。 定義原則之後，按 [下一步]。

  ![建立新保護群組精靈](./media/backup-azure-backup-server-vmware/retention-policy.png)

  您可以在 Azure 中保留資料的時間長度沒有限制。 在 Azure 中儲存復原點資料時，唯一的限制是每個受保護的執行個體不能有超過 9999 個復原點。 在此範例中，受保護的執行個體是 VMware 伺服器。

12. 在 [摘要] 畫面上，檢閱保護群組的詳細資料。 請注意群組成員和設定。 當您滿意設定時，請按一下 [建立群組]。

  ![建立新保護群組精靈](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>後續步驟
如果您要使用 Azure 備份伺服器來保護 VMware 工作負載，您可能對於使用 Azure 備份伺服器來保護 [Microsoft Exchange Server](./backup-azure-exchange-mabs.md)、[Microsoft SharePoint 伺服器陣列](./backup-azure-backup-sharepoint-mabs.md)或 [SQL Server](./backup-azure-sql-mabs.md) 很感興趣。

如需註冊代理程式、設定保護群組及備份作業相關問題的資訊，請參閱[針對 Azure 備份伺服器進行疑難排解](./backup-azure-mabs-troubleshoot.md)。

