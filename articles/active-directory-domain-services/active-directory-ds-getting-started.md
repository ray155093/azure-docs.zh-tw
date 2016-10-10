<properties
	pageTitle="Azure AD 網域服務：建立 AAD DC 系統管理員群組 | Microsoft Azure"
	description="開始使用 Azure Active Directory 網域服務"
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
	ms.date="09/21/2016"
	ms.author="maheshu"/>

# 開始使用 Azure AD 網域服務

本文將逐步解說針對 Azure AD 租用戶啟用 Azure AD 網域服務所需的組態工作。

## 工作 1：建立「AAD DC 系統管理員」群組
第一個工作是在您的 Azure Active Directory 租用戶中建立系統管理群組。這個特殊的系統管理群組稱為 **AAD DC 系統管理員**。此群組的成員會被授與電腦的系統管理權限，而這類電腦已加入 Azure AD 網域服務受管理網域的網域。在加入網域的電腦上，這個群組會新增到「系統管理員」群組。此外，此群組的成員可以使用遠端桌面，從遠端連接到已加入網域的電腦。

> [AZURE.NOTE] 您在使用 Azure AD 網域服務建立的網域內，沒有「網域系統管理員」或「企業系統管理員」權限。在受管理的網域上，服務會保留這些權限，並不會提供給租用戶中的使用者使用。不過，您可以使用在此組態工作中建立的特殊系統管理員群組，執行某些特殊權限的作業。這些作業包括將電腦加入網域、隸屬於已加入網域之電腦上的 Administrators 群組、設定群組原則等等。

在這個組態工作中，您會建立系統管理群組，並將目錄中的一或多位使用者加入該群組。執行下列步驟，以建立適用於 Azure AD 網域服務的系統管理群組：

1. 瀏覽到 **Azure 傳統入口網站** ([https://manage.windowsazure.com](https://manage.windowsazure.com))

2. 在左窗格中，選取 [Active Directory] 節點。

3. 選取您要啟用 Azure AD 網域服務的 Azure AD 租用戶 (目錄)。您只能針對每個 Azure AD 目錄建立一個網域。

    ![選取 Azure AD 目錄](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. 按一下 [**群組**] 索引標籤。

5. 若要將群組新增至 Azure AD 租用戶，在頁面底部的工作窗格中，按一下 [新增群組]。

6. 建立名為 **AAD DC 系統管理員**的群組。

    > [AZURE.WARNING] 建立使用此相同名稱的群組，才能啟用 Azure AD 網域服務內的存取權。

	![建立系統管理員群組](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. 新增此群組的說明，讓其他使用者了解此群組用來授與 Azure AD 網域服務內的系統管理權限。

8. 建立群組之後，按一下群組的名稱來查看這個群組的屬性。若要將使用者新增為此群組的成員，請按一下底部面板的 [新增成員] 按鈕。

9. 在 [新增成員] 對話方塊中，選取應該是這個群組成員的使用者，並在完成時選取此核取方塊。

    ![將使用者新增至系統管理員群組](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## 步驟 2：建立或選取 Azure 虛擬網路
下一個組態工作是[建立或選取 Azure 虛擬網路](active-directory-ds-getting-started-vnet.md)。

<!---HONumber=AcomDC_0928_2016-->