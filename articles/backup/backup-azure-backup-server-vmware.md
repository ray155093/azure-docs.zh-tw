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
ms.date: 04/20/2017
ms.author: markgal;
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: faa3c9d5de759e4d8c0589153ea93e83a580f983
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017


---
# <a name="back-up-vmware-server-to-azure"></a>將 VMware 伺服器備份到 Azure

本文說明如何設定 Azure 備份伺服器以保護 VMware 伺服器工作負載。 本文假設您已安裝 Azure 備份伺服器。 如果您尚未安裝 Azure 備份伺服器，請參閱[使用 Azure 備份伺服器準備將工作負載進行備份](backup-azure-microsoft-azure-backup.md)文章。

Azure 備份伺服器可以備份或保護 VMware vCenter Server 6.0 和 5.5 版。


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>建立 vCenter Server 的安全連線

Azure 備份伺服器預設會透過 HTTPS 通道與 vCenter Server 進行通訊。 若要啟用安全通訊，建議您在 Azure 備份伺服器上安裝 VMware 憑證授權單位 (CA) 憑證。 如果您不需要安全通訊，而且想要停用 HTTPS 要求，請參閱[停用安全通訊協定](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol)一節。 若要建立 Azure 備份伺服器與 vCenter Server 之間的安全連線，請將 Azure 備份伺服器上的受信任憑證匯入。

通常您會透過 vSphere Web 用戶端，使用 Azure 備份伺服器電腦上的瀏覽器連線到 vCenter Server。 您第一次使用 Azure 備份伺服器的瀏覽器連線到 vCenter Server 時，連線為不安全。 下圖顯示不安全的連線。

![VMware 伺服器的不安全連線範例](./media/backup-azure-backup-server-vmware/unsecure-url.png)

若要修正此問題，並建立安全連線，請下載受信任的根 CA 憑證。

1. 在 Azure 備份伺服器上的瀏覽器中，輸入 vSphere Web 用戶端的 URL。

  vSphere Web 用戶端登入頁面隨即出現。

  ![vSphere Web 用戶端](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

  系統管理員和開發人員的資訊底部是**下載受信任根 CA 憑證**的連結。

  ![下載 CA 信任根憑證的連結](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  如果您沒有看到 vSphere Web 用戶端登入頁面，請檢查您瀏覽器的 Proxy 設定。

2. 按一下 [下載受信任的根 CA 憑證]。

  vCenter Server 會將檔案下載到本機電腦。 檔案名稱為 **download**。 依您的瀏覽器而定，您會收到詢問您要開啟還是儲存檔案的訊息。

  ![下載憑證後，下載訊息](./media/backup-azure-backup-server-vmware/download-certs.png)

3. 將檔案儲存至 Azure 備份伺服器上的位置。 當您儲存檔案時，將副檔名 .zip 新增至檔案名稱。

  檔案是包含憑證相關資訊的 .zip 檔。 新增 .zip 副檔名讓您能夠使用解壓縮工具。

4. 以滑鼠右鍵按一下 **download.zip**，然後選取 [解壓縮全部]，將內容解壓縮。

  壓縮的檔案會將其內容解壓縮到名為 **certs** 的資料夾。 在 certs 資料夾中，有兩種檔案類型。 根憑證檔案的副檔名類似︰.0、.1 或 .number。 CRL 檔案具有以 .r0、.r1 等等開頭的副檔名。 與憑證相關聯的 CRL 檔案。

  ![下載檔案會在本機解壓縮 ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. 在 **certs** 資料夾中，以滑鼠右鍵按一下根憑證檔案，再按一下 [重新命名]。

  ![將根憑證重新命名 ](./media/backup-azure-backup-server-vmware/rename-cert.png)

  將根憑證的副檔名變更為 .crt。 當系統詢問您是否確定要變更副檔名時 - 因為您可以變更檔案的函式，請按一下 [是] 或 [確定]。 檔案的圖示會變更為根憑證的圖示。

6. 以滑鼠右鍵按一下根憑證，然後從快顯功能表中選取 [安裝憑證]。

  [憑證匯入精靈] 隨即開啟。

7. 在 [憑證匯入精靈] 中，選取 [本機電腦] 作為憑證目的地，然後按 [下一步] 繼續執行。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

  如果系統詢問您是否要允許對電腦進行變更，請對所有變更按一下 [是] 或 [確定]。

8. 在 [憑證存放區] 畫面上，選取 [將所有憑證放入以下的存放區]，然後按一下 [瀏覽] 來選擇憑證存放區。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

  [選取憑證存放區] 對話方塊隨即開啟。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/cert-store.png)

9. 選取**受信任的根憑證授權單位**作為憑證的目的地資料夾，然後按一下 [確定]。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

  選擇的憑證存放區會顯示在**憑證匯入精靈**中。 按一下 [下一步] 。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. 在 [完成憑證匯入精靈] 畫面上，確認憑證是否位於所要的資料夾中，然後按一下 [完成] 以完成精靈。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

  隨即出現一個對話方塊，讓您知道匯入是否成功。

11. 若要檢查您的連線是否安全，請登入 vCenter Server。

  如果您將憑證匯入時遇到問題，且無法建立安全連線，請參閱[取得伺服器憑證](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html)上的 VMware vSphere 文件。

  如果您的組織中有安全的界限，而且不想啟用 HTTPs 通訊協定，請使用下列程序停用安全的通訊。

### <a name="disable-secure-communication-protocol"></a>停用安全通訊協定

如果您的組織不需要安全通訊協定 (HTTPS)，請使用下列步驟停用 HTTPS。 若要停用預設行為，請建立會忽略預設行為的登錄機碼。

1. 複製以下文字並貼到 .txt 檔案中。

  ```
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
"IgnoreCertificateValidation"=dword:00000001
```
2. 使用 **DisableSecureAuthentication.reg** 名稱，將檔案儲存至 Azure 備份伺服器。

3. 按兩下檔案以啟動登錄項目。


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>在 vCenter Server 上建立角色和使用者帳戶

在 vCenter Server 上，角色是一組預先定義的權限。 vCenter Server 上的伺服器系統管理員會建立角色，並將使用者帳戶與要指派權限的角色進行配對。 若要建立備份 vCenter Server 所需的使用者認證，請使用特定權限來建立角色，並將使用者帳戶與角色產生關聯。

Azure 備份伺服器會使用使用者名稱和密碼來驗證 vCenter Server。 Azure 備份伺服器會使用這些認證作為所有備份作業的驗證。

若要新增 vCenter Server 角色和備份系統管理員的權限︰

1. 登入 vCenter Server，然後在 [導覽] 中按一下 [管理]。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. 在 [管理] 區段中選取 [角色]，然後在 [角色] 面板中按一下 [新增角色] 圖示(+ 符號)。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

  [建立角色] 對話方塊隨即開啟。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. 在 [建立角色] 對話方塊的 [角色名稱] 欄位中，輸入 BackupAdminRole。 角色名稱可以是您喜歡的名稱，但它應該是可辨識該角色的用途。

4. 選取 vCenter 適當版本的權限，然後按一下 [確定]。 下表指出 vCenter 6.0 和 vCenter 5.5 所需的權限。

  選取權限時，按一下父標籤上的＞形箭號，以展開父系並檢視子權限。 選取所需的 VirtualMachine 權限，需要進行數個 '層級' 深度。 您不需要選取父代權限內的所有子權限。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  按一下 [確定] 後，新的角色會出現在 [角色] 面板上的清單中。

|Vcenter 6.0 的權限| Vcenter 5.5 的權限|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-vcenter-server-user-account-and-permissions"></a>建立 vCenter Server 使用者帳戶和權限

一旦具有權限的角色存在之後，建立使用者帳戶。 使用者帳戶具有名稱和密碼，可提供用於驗證的認證。

1. 若要建立使用者帳戶，在 vCenter Server 導覽中，按一下 [使用者和群組]。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

  [使用者和群組] 面板隨即出現。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. 在 [vCenter 使用者和群組] 面板上，選取 [使用者] 索引標籤，然後按一下 [新增使用者] 圖示 (+ 符號)。

  [新增使用者] 對話方塊隨即開啟。

3. 在 [新增使用者] 對話方塊中，填妥欄位，然後按一下 [確定]。 在此範例中，輸入 **BackupAdmin**作為使用者名稱。 密碼必須為強式密碼。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

  新的使用者帳戶會出現在清單中。

4. 若要將使用者帳戶與角色產生關聯，在 [導覽] 上按一下 [通用權限]。 在 [通用權限] 面板上，選取 [管理] 索引標籤，然後按一下 [新增] 圖示 (+ 符號)。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

  [通用權限根目錄 - 新增權限] 對話方塊隨即開啟。

5. 在 [通用權限根目錄 - 新增權限] 對話方塊中，按一下 [新增] 來選擇使用者或群組。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

  [選取使用者/群組] 對話方塊隨即開啟。

6. 在 [選取使用者/群組] 對話方塊中，選擇 [BackupAdmin]，然後按一下 [新增]。

  [使用者] 欄位中的使用者帳戶格式為*網域*`\`*使用者名稱*。 如果您想要使用不同的網域，請從 [網域] 清單中選擇。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

  按一下 [確定] 將選取的使用者新增至 [新增權限] 對話方塊。

7. 您現在已找出使用者，請將使用者指派給角色。 在 [指派的角色] 區域中，從下拉式功能表選取 **BackupAdminRole**並按一下 [確定]。

  ![包含錯誤的憑證對話方塊 ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  在 [通用權限] 的 [管理] 索引標籤上，新的使用者帳戶和相關聯的角色會出現在清單中。


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>在 Azure 備份伺服器上建立 vCenter Server 認證

將 VMware 伺服器新增至 Azure 備份伺服器之前，請安裝 [Microsoft Azure 備份伺服器的 Update 1](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server)。

1. 若要開啟 Azure 備份伺服器，請按兩下 Azure 備份伺服器桌面上的圖示。

  ![Azure 備份伺服器圖示](./media/backup-azure-backup-server-vmware/mabs-icon.png)

  如果您在桌面上找不到此圖示，請從已安裝的應用程式清單開啟 Azure 備份伺服器。 Azure 備份伺服器應用程式名稱是 Microsoft Azure 備份。

2. 在 Azure 備份伺服器主控台中，按一下 [管理]，然後按一下 [實際執行伺服器]，然後在工具功能區中按一下 [管理 VMware]。

  ![MABS 主控台](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

  [管理認證] 對話方塊隨即開啟。

  ![MABS 管理認證對話方塊](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. 在 [管理認證] 對話方塊中，按一下 [新增] 以開啟 [新增認證] 對話方塊。

4. 在 [新增認證] 對話方塊中，輸入新認證的名稱和描述；然後指定使用者名稱和密碼。 認證名稱 (在此範例中為 Contoso Vcenter 認證) 是您識別下列程序中之認證的方法。 請使用與 vCenter Server 中所使用的相同使用者名稱和密碼。 如果 vCenter Server 和 Azure 備份伺服器不在相同網域中，請在使用者名稱中指定網域。

  ![MABS 管理認證對話方塊](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

  按一下 [新增] 將新的認證新增至 Azure 備份伺服器。 新的認證會出現在 [管理認證] 對話方塊的清單中。
  ![MABS 管理認證對話方塊](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. 若要關閉 [管理認證] 對話方塊，按一下右上角的 **X**。


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>將 vCenter Server 新增至 Azure 備份伺服器

若要開啟 [新增實際執行伺服器] 精靈：

1. 在 Azure 備份伺服器主控台中，依序按一下 [管理][實際執行伺服器] 及 [新增]。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

  [新增實際執行伺服器精靈] 隨即開啟。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. 在 [選取實際執行伺服器類型] 畫面上，選取 [VMware 伺服器]，然後按 [下一步]。

3. 在 [伺服器名稱/IP 位址] 中，指定 VMware 伺服器的完整網域名稱 (FQDN) 或 IP 位址。 如果所有 ESXi 伺服器均由相同的 vCenter 管理，您可以使用 vCenter 名稱。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. 在 [SSL 連接埠] 對話方塊中，輸入用來與 VMware 伺服器通訊的連接埠。 使用連接埠 443，這是預設連接埠，除非您知道需要不同的連接埠。

5. 在 [指定認證] 對話方塊中，選取您所建立的認證。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. 按一下 [新增] 將 VMware 伺服器新增至 [新增的 VMware 伺服器] 清單，然後按 [下一步] 移至精靈的下一個畫面。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. 在 [工作] 畫面中，按一下 [新增] 將指定的 VMware 伺服器新增至 Azure 備份伺服器。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  由於 VMware 伺服器備份是無代理程式的備份，所以幾秒內就會進行伺服器新增。 [完成] 畫面會顯示結果。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/summary-screen.png)

  若要重複本節先前的步驟，將多部 vCenter Server 新增至 Azure 備份伺服器。

將 vCenter Server 新增到 Azure 備份伺服器後，下一個步驟是建立保護群組。 保護群組可指定短期或長期保留的各種詳細資料，而且這是您定義及套用備份原則的地方。 備份原則包含執行備份的排程以及備份的內容。


## <a name="configure-a-protection-group"></a>設定保護群組

如果您未曾使用 System Center Data Protection Manager 或 Azure 備份伺服器，請參閱[規劃磁碟備份](https://technet.microsoft.com/library/hh758026.aspx)主題，以準備您的硬體環境。 一旦您檢查過您有適當的儲存體後，請使用 [建立新保護群組] 精靈來新增 VMware 虛擬機器。

1. 在 Azure 備份伺服器主控台中，按一下 [保護]，然後按一下工具功能區中的 [新增]，以開啟 [建立新保護群組] 精靈。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

  [建立新保護群組] 精靈隨即開啟。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/protection-wizard.png)

  按 [下一步] 前往 [選取保護群組類型] 畫面。

2. 在 [選取保護群組類型] 畫面上，選取 [伺服器]，然後按 [下一步]。

3. 在 [選擇群組成員] 畫面上，您可以看到可用的成員以及已選取的成員。 選取您想要保護的成員，然後按 [下一步] 。

  ![新增實際執行伺服器精靈](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

  選取成員時，如果您選取的資料夾包含其他資料夾或 VM，也會選取這些資料夾和 VM。 在父資料夾中包含資料夾和 VM 稱為資料夾層級保護。 您可以取消選取核取方塊，以排除任何資料夾或 VM。

  如果 VM 或包含 VM 的資料夾已受到 Azure 保護，您就無法再次選取該 VM。 也就是說，一旦 VM 受到 Azure 保護，就無法再次受到保護，這可防止針對一部 VM 建立重複的復原點。 如果您想要查看哪部 Azure 備份伺服器已經保護成員，請將滑鼠移到成員上，以查看保護伺服器的名稱。

4. 在 [選取資料保護方式] 畫面上，輸入保護群組的名稱。 選取短期保護 (磁碟) 和線上保護。 如果您想要使用線上保護 (Azure)，必須使用磁碟的短期保護。 按 [下一步] 以繼續進行短期保護範圍。

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

  當您滿意空間配置時，請按 [下一步]。

7. 在 [選擇複本的建立方式] 畫面上，指定您要在 Azure 備份伺服器上產生受保護資料之初始副本或複本的方式。

  預設值是 [自動透過網路] 和 [立即]。 如果您使用預設值，建議您指定離峰時間。 選擇 [稍後] 並指定日期與時間。

  對於大量資料或較差的網路狀況，請考慮使用卸除式媒體來離線複寫資料。

  做好選擇後，請按 [下一步]。

  ![建立新保護群組精靈](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. 在 [一致性檢查選項] 畫面上，選取如何及何時自動執行一致性檢查。 當複本資料變得不一致時，或依據設定的排程，您可以執行一致性檢查。

  如果您不想設定自動一致性檢查，可以執行手動檢查。 在 Azure 備份伺服器主控台的 [保護] 區域，以滑鼠右鍵按一下保護群組，然後選取 [執行一致性檢查]。

  按 [下一步] 移至下一個畫面。

9. 在 [指定線上保護資料] 畫面上，選取您想要保護的資料來源。 您可以個別地選取成員，或按一下 [全選] 來選擇所有成員。 選擇成員後，請 [下一步]。

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

