<properties
	pageTitle="Azure Active Directory 網域服務預覽：管理指南 | Microsoft Azure"
	description="在 Azure AD 網域服務受管理的網域上建立組織單位 (OU)"
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
	ms.date="04/08/2016"
	ms.author="maheshu"/>

# 在 Azure AD 網域服務受管理的網域上建立組織單位 (OU)
Azure AD 網域服務受管理的網域包含兩個內建的容器，分別稱為「AADDC 電腦」和「AADDC 使用者」。「AADDC 電腦」容器有已加入受管理的網域中全部電腦的電腦物件。「AADDC 使用者」容器包含 Azure AD 租用戶中的使用者和群組。有時候，可能需要在受管理的網域上建立服務帳戶，才能部署工作負載。為此目的，您可以在受管理的網域上建立自訂的組織單位 (OU)，並在此 OU 內建立服務帳戶。本文將示範如何在受管理的網域中建立 OU。


## 在已加入網域的虛擬機器上安裝 AD 系統管理工具進行遠端管理
使用 Active Directory 管理中心 (ADAC) 或 AD PowerShell 等熟悉的 Active Directory 系統管理工具，可以從遠端管理 Azure AD 網域服務受管理的網域。租用戶系統管理員沒有權限，不能透過遠端桌面連接受管理網域上的網域控制站。若要管理受管理的網域，請在加入受管理網域的虛擬機器上安裝 AD 系統管理工具功能。如需相關指示，請參閱 [dminister an Azure AD Domain Services managed domain (管理 Azure AD 網域服務受管理的網域)](active-directory-ds-admin-guide-administer-domain.md) 一文。

## 在受管理的網域上建立組織單位
既然加入網域的虛擬機器上已安裝了 AD 系統管理工具，我們就可以使用這些工具在受管理的網域上建立組織單位。請執行下列步驟：

> [AZURE.NOTE] 只有「AAD DC 管理員」群組的成員具有建立新 OU 的必要權限。請確保以這個群組成員的使用者身分執行下列步驟。

1. 從 [開始] 畫面中，按一下 [系統管理工具]。您應該會看到安裝在虛擬機器上的 AD 系統管理工具。

	![安裝在伺服器上的系統管理工具](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. 按一下 [Active Directory 管理中心]。

	![Active Directory 管理中心](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. 按一下左窗格中的網域名稱 (例如，'contoso100.com') 來檢視網域。

    ![ADAC - 檢視網域](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. 在右側的 [工作] 窗格中，按一下網域名稱節點下的 [新增]。本例會在右側的 [工作] 窗格中，按一下 'contoso100(local)' 節點下的 [新增]，如下所示。

    ![ADAC - 新的 OU](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. 您應該會看到建立組織單位的選項。按一下 [組織單位] 啟動 [Create Organizational Unit] (建立組織單位) 對話方塊。

6. 在 [Create Organizational Unit] (建立組織單位) 對話方塊中，指定新 OU 的 [名稱]。提供 OU 的簡短描述。您也可以設定 OU 的 [Managed By] (管理者) 欄位。按一下 [確定] 建立新 OU。

    ![ADAC - 建立 OU 對話方塊](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. 新建立的 OU 現在應該會出現在 AD 管理中心 (ADAC) 中。

    ![ADAC - OU 已建立](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## 新建 OU 的權限/安全性
建立新 OU 的使用者 (「AAD DC 系統管理員」群組的成員) 預設會被授與 OU 的系統管理權限 (完全控制)。這個使用者接著可以繼續將權限授與其他使用者，或視需要授與「AAD DC 系統管理員」群組。如以下的螢幕擷取畫面所示，建立新 'MyCustomOU' 組織單位的使用者 'bob@domainservicespreview.onmicrosoft.com' 獲授與該組織單位的完整控制權。

 ![ADAC - 新的 OU 安全性](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## 管理自訂 OU 的注意事項
既然您已建立了自訂的 OU，就可以繼續在這個 OU 中建立使用者、群組、電腦和服務帳戶。您無法將使用者或群組從「AAD DC 使用者」OU 移至自訂的 OU。

> [AZURE.WARNING] 您在自訂 OU 下建立的使用者帳戶、群組、服務帳戶和電腦物件無法在 Azure AD 租用戶中使用。換句話說，使用 Azure AD Graph API 或 Azure AD UI 中都不會顯示這些物件。這些物件只能用在 Azure AD 網域服務受管理的網域中。


## 相關內容

- [Administer an Azure AD Domain Services managed domain (管理 Azure AD 網域服務受管理的網域)](active-directory-ds-admin-guide-administer-domain.md)

- [Active Directory 管理中心：入門](https://technet.microsoft.com/library/dd560651.aspx)

- [服務帳戶的逐步指南](https://technet.microsoft.com/library/dd548356.aspx)

<!---HONumber=AcomDC_0420_2016-->