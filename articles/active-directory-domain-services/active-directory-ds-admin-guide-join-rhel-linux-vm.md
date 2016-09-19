<properties
	pageTitle="Azure Active Directory 網域服務預覽：管理指南 | Microsoft Azure"
	description="將 Red Hat Enterprise Linux 虛擬機器加入 Azure AD 網域服務"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="maheshu"/>

# 將 Red Hat Enterprise Linux 7 虛擬機器加入受管理的網域
本文說明如何將 Red Hat Enterprise Linux (RHEL) 7 虛擬機器加入 Azure AD 網域服務受管理網域。

## 佈建 Red Hat Enterprise Linux 虛擬機器
請執行下列步驟，以使用 Azure 入口網站佈建 RHEL 7 虛擬機器。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

    ![Azure 入口網站儀表板](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. 按一下左邊窗格中的 [新增]，並如以下螢幕擷取畫面中所示在搜尋列中輸入 **Red Hat**。搜尋結果中會出現 Red Hat Enterprise Linux 的項目。按一下 [Red Hat Enterprise Linux 7.2]。

    ![在結果中選取 RHEL](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. [所有項目] 窗格中的搜尋結果應該會列出 Red Hat Enterprise Linux 7.2 映像。按一下 [Red Hat Enterprise Linux 7.2] 來檢視虛擬機器映像的詳細資訊。

    ![在結果中選取 RHEL](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. 在 [Red Hat Enterprise Linux 7.2] 窗格中，您應該會看到虛擬機器映像的詳細資訊。在 [選取部署模型] 下拉式清單中，選取 [傳統]。然後按一下 [建立] 按鈕。

    ![檢視映像的詳細資料](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. 在 [建立 VM] 窗格中，輸入新虛擬機器的 [主機名稱]。另外，請在 [使用者名稱] 欄位中指定本機系統管理員使用者名稱和 [密碼]。您也可以選擇使用 SSH 金鑰來驗證本機系統管理員使用者。另外，請選取虛擬機器的 [定價層]。

    ![建立 VM - 基本詳細資料](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. 按一下 [選擇性組態]。這應該會開啟標題為 [選擇性組態] 的窗格。在 [選擇性組態] 窗格中，按一下 [網路]。

    ![建立 VM - 設定虛擬網路](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. 這會顯示標題為 [網路] 的窗格。在 [網路] 窗格中按一下 [虛擬網路]，以選取應部署 Linux VM 的虛擬網路。這會開啟 [虛擬網路] 窗格。在 [虛擬網路] 窗格中，選擇 [使用現有的虛擬網路] 選項。然後選取有提供 Azure AD 網域服務的虛擬網路。在此範例中，我們挑選 'MyPreviewVNet' 虛擬網路。

    ![建立 VM - 選取虛擬網路](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. 在 [選擇性組態] 窗格中，按一下 [確定] 按鈕。

    ![建立 VM - 選取的虛擬網路](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. 現在，您已準備好建立虛擬機器。在 [建立 VM] 窗格中，按一下 [建立] 按鈕。

    ![建立 VM - 就緒](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. 此時應該會開始根據 RHEL 7.2 映像部署新虛擬機器。

  ![建立 VM - 部署開始](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. 幾分鐘後，虛擬機器應該會部署成功並可供使用。

  ![建立 VM - 已部署](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## 遠端連線到新佈建的 Linux 虛擬機器
RHEL 7.2 虛擬機器已佈建在 Azure 中。下一個工作是從遠端連線至虛擬機器。

**連線到 RHEL 7.2 虛擬機器** 遵循[如何登入執行 Linux 的虛擬機器](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)一文中的指示。

下列剩餘步驟假設您使用 PuTTY 做為 SSH 用戶端來連線至 RHEL 虛擬機器。如需詳細資訊，請參閱 [PuTTY 下載頁面](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

1. 開啟 PuTTY 程式。

2. 針對新建立的 RHEL 虛擬機器輸入 [主機名稱]。在此範例中，我們的虛擬機器的主機名稱為 'contoso-rhel.cloudapp.net'。如果您不確定您的 VM 的主機名稱，請參閱 Azure 入口網站上的 VM 儀表板。

    ![PuTTY 連線](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. 使用建立虛擬機器時指定的本機系統管理員認證來登入虛擬機器。在此範例中，我們使用本機系統管理員帳戶 'mahesh'。

    ![PuTTY 登入](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## 在 Linux 虛擬機器上安裝必要封裝
在連線到虛擬機器後，下一個工作是在虛擬機器上安裝要加入網域所需的封裝。執行下列步驟：

1. **安裝 realmd：**realmd 封裝可用於加入網域。在 PuTTY 終端機中輸入下列命令。

    sudo yum install realmd

    ![安裝 realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    幾分鐘之後，realmd 封裝應該就會安裝在虛擬機器上。

    ![realmd 已安裝](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **安裝 sssd：**realmd 套件需倚賴 sssd 執行加入網域作業。在 PuTTY 終端機中輸入下列命令。

    sudo yum install sssd

	![安裝 sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    幾分鐘之後，sssd 封裝應該就會安裝在虛擬機器上。

    ![realmd 已安裝](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **安裝 kerberos：**在 PuTTY 終端機中輸入下列命令。

    sudo yum install krb5-workstation krb5-libs

	![安裝 kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

	幾分鐘之後，realmd 封裝應該就會安裝在虛擬機器上。

	![Kerberos 已安裝](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## 將 Linux 虛擬機器加入受管理的網域
既然 Linux 虛擬機器上已安裝必要的封裝，下一個工作是將虛擬機器加入受管理的網域。

1. 探索 AAD 網域服務受管理網域。在 PuTTY 終端機中輸入下列命令。

    sudo realm discover CONTOSO100.COM

	![探索領域](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

	如果 **realm discover** 無法找到受管理的網域，請確定可從虛擬機器連線到網域 (請嘗試 ping)。也請確定虛擬機器已確實部署到有提供受管理網域的相同虛擬網路上。

2. 初始化 kerberos。在 PuTTY 終端機中輸入下列命令。請確定您是指定屬於 'AAD DC Administrators' 群組的使用者。只有這些使用者可以將電腦加入受管理的網域。

    kinit bob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    請確定您是以大寫字母指定網域名稱，否則 kinit 會失敗。

3. 將電腦加入網域。在 PuTTY 終端機中輸入下列命令。請確定您是指定上述步驟中所指定的相同使用者 ('kinit')。

    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'

	![加入領域](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

當電腦成功加入受管理的網域時，您應該會收到訊息 (「已成功在領域中註冊電腦」)。


## 確認加入網域
您可以快速確認電腦是否已成功加入受管理的網域。使用 SSH 和網域使用者帳戶連接到新加入網域的 RHEL VM，然後查看使用者帳戶是否解析正確。

1. 在 PuTTY 終端機中輸入下列命令，以使用 SSH 連線到新加入網域的 RHEL 虛擬機器。使用屬於受管理網域的網域帳戶 (例如，在此例中為 'bob@CONTOSO100.COM')。

    ssh -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net

2. 在 PuTTY 終端機中輸入下列命令，以查看是否已正確初始化主目錄。

	pwd

3. 在 PuTTY 終端機中輸入下列命令，以查看是否會正確解析群組成員資格。

    id

這些命令的輸出範例如下所示。

![確認加入網域](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## 針對加入網域進行疑難排解
請參閱[針對加入網域進行疑難排解](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join)一文。


## 相關內容
- [Azure AD 網域服務 - 開始使用](./active-directory-ds-getting-started.md)

- [將 Windows Server 虛擬機器加入 Azure AD 網域服務受管理的網域](active-directory-ds-admin-guide-join-windows-vm.md)

- [如何登入執行 Linux 的虛擬機器](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)。

- [安裝 Kerberos](https://access.redhat.com/documentation/zh-TW/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)

- [Red Hat Enterprise Linux 7 - Windows 整合指南](https://access.redhat.com/documentation/zh-TW/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)

<!----HONumber=AcomDC_0907_2016-->