---
title: "Azure Active Directory Domain Services：將 Windows Server VM 加入受管理的網域 | Microsoft Docs"
description: "將 Windows Server 虛擬機器加入 Azure AD 網域服務"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 74dbdb33-05db-4d47-badc-0d7bb6d0c8cb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: aad6bcd3eb704f090156d2ace80d2540a9543bd7


---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>將 Windows Server 虛擬機器加入受管理的網域
> [!div class="op_single_selector"]
> * [Azure 傳統入口網站 - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
> 
> 

<br>

本文說明如何使用 Azure 傳統入口網站，將執行 Windows Server 2012 R2 的虛擬機器加入 Azure AD 網域服務受管理網域。

## <a name="step-1-create-the-windows-server-virtual-machine"></a>步驟 1︰建立 Windows Server 虛擬機器
請依照[在 Azure 傳統入口網站中建立執行 Windows 的虛擬機器](../virtual-machines/virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)教學課程中所述的指示操作。 務必要確實將此新建立的虛擬機器加入已在其中啟用 Azure AD 網域服務的相同虛擬網路。 [快速建立] 選項無法讓您將虛擬機器加入虛擬網路。 因此，您必須使用 [從資源庫] 選項來建立虛擬機器。

執行下列步驟，以建立 Windows 虛擬機器，並將其加入已在其中啟用 Azure AD 網域服務的虛擬網路。

1. 在 Azure 傳統入口網站中，按一下視窗底部命令列上的 [新增] 。
2. 在 [計算] 底下，依序按一下 [虛擬機器] 和 [從資源庫]。
3. 第一個畫面可讓您從可用的映像清單中，為您的虛擬機器 [選擇映像]  。 請選擇適當的映像。
   
    ![選取映像](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. 第二個畫面可讓您挑選電腦名稱、大小，及系統管理使用者名稱和密碼。 使用執行應用程式或工作負載所需的層次和大小。 您在此挑選的使用者名稱會是機器上的本機系統管理員使用者。 請勿在此輸入網域使用者帳戶的認證。
   
    ![設定虛擬機器](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)
5. 第三個畫面可讓您設定網路、儲存體和可用性的資源。 請在 [區域/同質群組/虛擬網路]  下拉式清單中確實選取已在其中啟用 Azure AD 網域服務的虛擬網路。 為虛擬機器指定合適的 [雲端服務 DNS 名稱]  。
   
    ![選取虛擬機器的虛擬網路](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)
   
   > [!WARNING]
   > 請確定將虛擬機器加入您已在其中啟用 Azure AD Domain Services 的相同虛擬網路。 因此，虛擬機器可以「看到」網域並執行加入網域之類的工作。 如果您選擇在不同虛擬網路中建立虛擬機器，請將該虛擬網路連接到您已在其中啟用 Azure AD Domain Services 的虛擬網路。
   > 
   > 
6. 第四個畫面可讓您安裝 VM 代理程式及設定部分可用延伸模組。
   
    ![完成](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
7. 建立虛擬機器後，傳統入口網站會在 [虛擬機器]  節點底下列出新的虛擬機器。 虛擬機器和雲端服務都會自動啟動，而且它們的狀態會顯示為 [執行中] 。
   
    ![虛擬機器已啟動並執行](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)

## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>步驟 2︰使用本機系統管理員帳戶連線到 Windows Server 虛擬機器
現在，我們會連線到新建立的 Windows Server 虛擬機器，以便將其加入網域。 使用建立虛擬機器時所指定的本機系統管理員認證，以便與其連線。

執行下列步驟以連線到虛擬機器。

1. 在傳統入口網站中瀏覽至 [虛擬機器]  節點。 選取您在步驟 1 建立的虛擬機器，然後按一下視窗底部命令列上的 [連線]  。
   
    ![連線至 Windows 虛擬機器](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. 傳統入口網站會提示您開啟或儲存副檔名為 '.rdp' 的檔案，其可供用來連線到虛擬機器。 在檔案下載完成時按一下加以開啟。
3. 在登入提示中，輸入您在建立虛擬機器時所指定的 [本機系統管理員認證] 。 例如，我們在此範例中使用 'localhost\mahesh'。

此時，您應該已使用本機系統管理員認證登入到新建立的 Windows 虛擬機器。 下一個步驟是將虛擬機器加入網域。

## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>步驟 3︰將 Windows Server 虛擬機器加入 AAD-DS 受管理網域
請執行下列步驟，以將 Windows Server 虛擬機器加入 AAD-DS 受管理網域。

1. 如步驟 2 所示連線到 Windows Server。 在 [開始] 畫面中開啟 [伺服器管理員] 。
2. 按一下 [伺服器管理員] 視窗之左窗格中的 [本機伺服器]  。
   
    ![啟動虛擬機器上的伺服器管理員](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. 按一下 [屬性] 區段底下的 [工作群組]。 在 [系統屬性] 屬性頁中，按一下 [變更] 來加入網域。
   
    ![[系統屬性] 頁面](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. 在 [網域] 文字方塊中指定 Azure AD Domain Services 受管理網域的網域名稱，然後按一下 [確定]。
   
    ![指定要加入的網域](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. 系統會提示您輸入認證以便加入網域。 請確定您是 **指定屬於 AAD DC 系統管理員群組之使用者的認證** 。 只有此群組的成員才有權限可以將機器加入受管理的網域。
   
    ![指定用於加入網域的認證](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. 您可以透過下列任一方式指定認證︰
   
   * UPN 格式︰指定 Azure AD 中所設定使用者帳戶的 UPN 尾碼。 在此範例中，使用者 'bob' 的 UPN 尾碼是 'bob@domainservicespreview.onmicrosoft.com'。
   * SAMAccountName 格式︰您可以使用 SAMAccountName 格式指定帳戶名稱。 在此範例中，使用者 'bob' 必須輸入 'CONTOSO100\bob'。
     
     > [!NOTE]
     > **建議使用 UPN 格式來指定認證。** 如果使用者的 UPN 前置詞太長 (例如 'joereallylongnameuser')，就可能自動產生 SAMAccountName。 如果您的 Azure AD 租用戶中有多個使用者擁有相同的 UPN 前置詞 (例如 'bob')，則服務可能會自動產生它們的 SAMAccountName 格式。 在這些情況下，您可以使用 UPN 格式可靠地登入網域。
     > 
     > 
7. 順利加入網域後，您會看到下列訊息歡迎您加入網域。 重新啟動虛擬機器，以便完成加入網域作業。
   
    ![歡迎加入網域](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

## <a name="troubleshooting-domain-join"></a>針對加入網域進行疑難排解
### <a name="connectivity-issues"></a>連線能力問題
如果虛擬機器找不到網域，請參閱下列疑難排解步驟︰

* 確定虛擬機器已連線到您已在其中啟用網域服務的相同虛擬網路。 若非如此，虛擬機器便無法連線到網域，因此無法加入網域。
* 如果虛擬機器連線到其他虛擬網路，請確定此虛擬網路已連線到您已在其中啟用網域服務的虛擬網路。
* 嘗試使用受管理網域的網域名稱來 ping 網域 (例如 'ping contoso100.com')。 如果您無法這麼做，請嘗試 ping 頁面上所顯示、您已在其中啟用 Azure AD 網域服務之網域的 IP 位址 (例如 'ping 10.0.0.4')。 如果您能夠 ping 該 IP 位址，但無法 ping 網域，則表示 DNS 的設定可能不正確。 您可能尚未將網域的 IP 位址設定為虛擬網路的 DNS 伺服器。
* 請嘗試排清虛擬機器上的 DNS 解析程式快取 ('ipconfig /flushdns')。

如果您看到對話方塊要求您提供認證以加入網域，則表示您沒有連線問題。

### <a name="credentials-related-issues"></a>與認證相關的問題
如果您的認證有問題因而無法加入網域，請參閱下列步驟。

* 嘗試使用 UPN 格式來指定認證。 如果您的租用戶中有多個使用者具有相同的 UPN 前置詞，或您的 UPN 前置詞太長，可能就會自動為您的帳戶產生 SAMAccountName。 因此，您帳戶的 SAMAccountName 格式可能會與您在內部部署網域中預期或使用的格式不同。
* 嘗試使用屬於「AAD DC 系統管理員」群組之使用者帳戶的認證，來將電腦加入受管理的網域。
* 確定您已根據《入門指南》中所述的步驟來 [啟用密碼同步處理](active-directory-ds-getting-started-password-sync.md) 。
* 確定您是使用 Azure AD 中所設定的使用者 UPN (例如 'bob@domainservicespreview.onmicrosoft.com')) 來登入。
* 確定您已如《入門指南》中所指定的等候夠久的時間以讓密碼同步處理完成。

## <a name="related-content"></a>相關內容
* [Azure AD Domain Services - 入門指南](active-directory-ds-getting-started.md)
* [Administer an Azure AD Domain Services managed domain (管理 Azure AD 網域服務受管理的網域)](active-directory-ds-admin-guide-administer-domain.md)




<!--HONumber=Nov16_HO3-->


