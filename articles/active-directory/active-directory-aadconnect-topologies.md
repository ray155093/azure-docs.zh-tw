<properties
    pageTitle="Azure AD Connect：支援的拓撲 | Microsoft Azure"
    description="本主題詳細說明 Azure AD Connect 的受支援和不受支援拓撲"
    services="active-directory"
    documentationCenter=""
    authors="AndKjell"
    manager="stevenpo"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
	ms.topic="article"
    ms.date="06/27/2016"
    ms.author="andkjell"/>

# Azure AD Connect 的拓撲

本主題的目標在於利用 Azure AD Connect 同步處理做為重要的整合方案，說明不同的內部部署和 Azure AD 拓撲。它會描述受支援和不受支援的組態。

文件中的圖片圖例：

說明 | 圖示
-----|-----
內部部署 Active Directory 樹系| ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)
內含篩選匯入的 Active Directory| ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)
Azure AD Connect 同步處理伺服器| ![Sync](./media/active-directory-aadconnect-topologies/LegendSync1.png)
Azure AD Connect 同步處理伺服器「預備模式」| ![Sync](./media/active-directory-aadconnect-topologies/LegendSync2.png)
內含 FIM2010 或 MIM2016 的 GALSync| ![Sync](./media/active-directory-aadconnect-topologies/LegendSync3.png)
Azure AD Connect 同步處理伺服器，詳細說明| ![Sync](./media/active-directory-aadconnect-topologies/LegendSync4.png)
Azure AD 目錄 |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)
不受支援的案例 | ![不支援](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)


## 單一樹系、單一 Azure AD 目錄
![單一樹系單一目錄](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

最常見的拓樸是單一樹系內部部署 (內含一或多個網域) 和單一 Azure AD 目錄 (又名租用戶)。對於 Azure AD 驗證，會使用密碼同步處理。Azure AD Connect 的快速安裝僅支援此拓撲。

### 單一樹系、多部同步處理伺服器連接到同一 Azure AD 目錄
![不支援單一樹系篩選](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

不支援多部的 Azure AD Connect 同步處理伺服器連接到相同 Azure AD 目錄 (除了[預備伺服器](#staging-server)之外)。即使設定為同步處理互斥的一組物件，也不支援。如果您無法從單一伺服器觸達樹系中的所有網域，或者將負載分散到數個伺服器，您可能已經考慮過這種情形。

## 多樹系、單一 Azure AD 目錄
![多樹系單一目錄](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

許多組織都有包含多個內部部署 Active Directory 樹系的環境。擁有多個內部部署 Active Directory 的原因有很多。常見的範例如包含帳戶資源樹系，和合併或收購之後的結果。

當您擁有多個樹系，所有樹系必須可由單一 Azure AD Connect 同步處理伺服器連線。您不需要將伺服器加入網域。如果必須觸達所有樹系，則伺服器可以放在網路 DMZ。

Azure AD Connect 安裝精靈會提供數個選項以合併多個樹系中表示的使用者。目標是使用者只會在 Azure AD 中顯示一次。有一些常見的拓撲，您可以在安裝精靈中的自訂安裝路徑中設定。選取對應的選項，表示您在 [唯一識別您的使用者] 頁面上的拓撲。只對使用者設定合併。重複群組不會和預設組態合併。

下一節中將討論常見的拓撲：[分隔拓撲](#multiple-forests-separate-topologies)、[完整網狀](#multiple-forests-full-mesh-with-optional-galsync)和[帳戶資源](#multiple-forests-account-resource-forest)。

Azure AD Connect 同步處理中的預設組態假設：

1. 使用者只有一個啟用的帳戶，且此帳戶所在之樹系用於驗證使用者。此假設適用於密碼同步處理和同盟。UserPrincipalName 和 sourceAnchor/immutableID 來自此樹系
2. 使用者只有一個信箱。
3. 裝載使用者信箱的樹系具有最佳資料品質，以供屬性在 Exchange 全域通訊清單 (GAL) 中顯示。如果使用者沒有信箱，則任何樹系皆可用於提供這些屬性值。
4. 如果您有連結的信箱，在不同的樹系中還會有另一個帳戶用來登入。

如果您的環境不符合這些假設，則會發生下列情況：

- 如果您有多個使用中的帳戶或多個信箱，同步處理引擎會挑選其中一個，其他全部忽略。
- 沒有使用中帳戶的已連結的信箱不會匯出至 Azure AD。使用者帳戶不會顯示為任何群組中的成員。DirSync 中已連結的信箱一律會顯示為一般信箱，因此這個變更是刻意的不同行為，可更有效支援多個樹系案例。

可以在[了解預設組態](active-directory-aadconnectsync-understanding-default-configuration.md)中找到更多詳細資料。

### 多個樹系、多部同步處理伺服器連接到單一 Azure AD 目錄
![不支援多樹系多個同步處理](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

不支援超過一部的 Azure AD Connect 同步處理伺服器連接到單一 Azure AD 目錄。例外狀況是使用[預備伺服器](#staging-server)。

### 多個樹系 – 個別拓撲
**使用者只能跨所有目錄顯示一次**

![多樹系使用者一次](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![多樹系不同拓撲](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

在此環境中，所有樹系內部部署都會視為個別的實體，而且沒有使用者會出現在任何其他樹系中。每個樹系具有自己的 Exchange 組織，且在樹系間沒有 GALSync。這個拓撲可能是合併/收購之後，或是在一個每個業務單位各自獨立作業的組織中。在 Azure AD 中，這些樹系將會在相同的組織中並與統一 GAL 一起出現。在此圖中，每個樹系中的個別物件都會在 Metaverse 中顯示一次，並在目標 Azure AD 目錄中彙總。

### 多個樹系 – 比對使用者
**使用者身分識別存在於多個目錄**

這些案例的常見狀況是通訊群組和安全性群組可以包含使用者、連絡人和 FSP (外部安全性主體) 的組合。

FSP 可在 ADDS 中用來代表安全性群組中來自其他樹系的成員。在 Azure AD 中，所有 FSP 都會解析為實際物件。

### 多個樹系 – 內含選擇性 GALSync 的完整網狀
**使用者身分識別存在於多個目錄。比對方法：郵件屬性**

![多樹系使用者郵件](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![多樹系完整網狀](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

完整網狀拓樸可以讓使用者與資源位於任何樹系中，且通常在樹系間會有雙向信任。

如果 Exchange 出現在一個以上的樹系中，有可能是選擇性的內部部署 GALSync 解決方案。每個使用者可以顯示為所有其他樹系中的連絡人。GALSync 通常是使用 Forefront Identity Manager 2010 或 Microsoft Identity Manager 2016 來實作。Azure AD Connect 無法用於內部部署 GALSync。

在此案例中，身分識別物件通常會使用郵件屬性來聯結。在一個樹系中擁有信箱的使用者會與其他樹系中的連絡人聯結。

### 多個樹系 – 帳戶資源樹系
**使用者身分識別存在於多個目錄。比對方法：ObjectSID 和 Msexchuseraccountcontrol 屬性**

![多樹系使用者 ObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![多樹系 AccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

在帳戶資源樹系拓樸中，您會有一個以上的帳戶樹系內含作用中的使用者帳戶。您也會有一或多個資源樹系具有停用的帳戶。

在此案例中一個 (或多個) **資源樹系**信任所有**帳戶樹系**。此資源樹系通常具有擴充的 AD 結構描述與 Exchange 和 Lync。所有的 Exchange 和 Lync 服務以及其他共用的服務都位於此樹系。使用者在此樹系中擁有停用的使用者帳戶，而信箱會連結至帳戶樹系。

## Office 365 和拓撲考量
有些 Office 365 工作負載對受支援的拓撲有某些限制。如果您打算使用其中任何一項，請參閱工作負載的受支援拓撲主題。

工作負載 |  
--------- | ---------
Exchange Online | 如果有一個以上的 Exchange 組織內部部署 (也就是 Exchange 已部署至一個以上的樹系)，則您必須使用 Exchange 2013 SP1 或更新版本。您可以在此找到詳細資料：[內含多個 Active Directory 樹系的混合式部署](https://technet.microsoft.com/library/jj873754.aspx)
商務用 Skype | 使用多個樹系內部部署時，只會支援帳戶資源樹系拓撲。您可以在此找到所支援拓撲的詳細資料：[商務用 Skype Server 2015 的環境需求](https://technet.microsoft.com/library/dn933910.aspx)

## 預備伺服器
![預備伺服器](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect 支援以「預備模式」安裝第二部伺服器。在此模式中的伺服器會讀取連線目錄中所有的資料，但是不會寫入任何項目。它使用一般的同步處理循環，因此也有身分識別資料的更新複本。在主要伺服器失敗的災害中，您可以容錯移轉到預備伺服器。您在 Azure AD Connect 精靈中執行這項操作。第二部伺服器可以依照喜好位於不同的資料中心，因為沒有和主要伺服器共用基礎結構。您必須手動將主要伺服器上進行的任何組態變更複製到第二部伺服器。

也可以使用預備伺服器以測試新的自訂組態以及它對您的資料所造成的影響。您可以預覽變更並調整組態。當您滿意新的組態時，您可以讓預備伺服器成為使用中的伺服器，並將舊的使用中伺服器設定為預備模式。

這個方法也可用來取代使用中的同步伺服器。準備新的伺服器，並且將其設定在預備模式。請確定它處於良好狀態、停用預備模式 (讓它成為使用中)，並關閉目前作用中的伺服器。

如果您想要在不同的資料中心有多個備份，您也可以擁有一個以上的預備伺服器。

## 多個 Azure AD 目錄
Microsoft 建議一個組織在 Azure AD 中有單一的目錄。在您打算使用多個 Azure AD 目錄之前，這些主題涵蓋了常見的案例，可讓您使用單一目錄。

主題 |  
--------- | ---------
使用管理單位的委派 | [在 Azure AD 中的管理單位管理](active-directory-administrative-units-management.md)

![多樹系多個目錄](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Azure AD Connect 同步處理伺服器和 Azure AD 目錄之間有 1:1 的關聯性。在每個 Azure AD 目錄中，您將需要一個 Azure AD Connect 同步處理伺服器安裝。Azure AD 目錄執行個體在設計上是隔離的，在其中之一的使用者將無法查看其他目錄中的使用者。如果這是預期的分隔，就是受支援的組態，否則您應該使用單一 Azure AD 目錄模型。

### 每個物件只在 Azure AD 目錄運作一次
![單一樹系篩選](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

在此拓撲中，一個 Azure AD Connect 同步處理伺服器會連接到每個 Azure AD 目錄。Azure AD Connect 同步處理伺服器必須設定篩選，以各自有一組要操作的互斥的物件。例如，將每個伺服器的範圍限定於特定網域或 OU。DNS 網域只能在單一 Azure AD 目錄中註冊。內部部署 AD 中的使用者 UPN 也必須使用個別的命名空間。例如，在以上三個個別 UPN 的圖片中，尾碼都登錄在內部部署 AD 中：contoso.com、fabrikam.com 和 wingtiptoys.com。每個內部部署 AD 網域中的使用者會使用不同的命名空間。

Azure AD 目錄執行個體之間沒有 GALsync。Exchange Online 和商務用 Skype 中的通訊錄只會顯示相同目錄中的使用者。

對於另外支援的案例，此拓撲具有下列限制︰

- 只有其中一個 Azure AD 目錄可以啟用 Exchange 與內部部署 Active Directory 的混合。
- Windows 10 裝置只能與一個 Azure AD 目錄相關聯。

物件互斥集的需求也適用於寫回。此拓撲不支援部分寫回功能，因為這些功能假設單一組態內部部署：

-	使用預設組態的群組回寫
-	裝置回寫

### 每個物件在 Azure AD 目錄運作多次
![不支援單一樹系多個目錄](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![不支援單一樹系多個連接器](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

- 它不支援同步處理相同使用者至多個 Azure AD 目錄。
- 不支援進行組態變更，讓一個 Azure AD 中的使用者顯示為另一個 Azure AD 目錄中的連絡人。
- 不支援將 Azure AD Connect 同步處理修改為連接到多個 Azure AD 目錄。

### 使用回寫 GALsync
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD 目錄在設計上是隔離的。

- 它不支援將 Azure AD Connect 同步處理變更為從另一個 Azure AD 目錄讀取資料。
- 不支援使用 Azure AD Connect 同步處理將使用者匯出為另一個內部部署 AD的連絡人。

### 具備內部部署同步處理伺服器的 GALsync
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

它支援使用 FIM2010/MIM2016 內部部署至兩個 Exchange 組織之間的 GALsync 使用者。一個組織中的使用者將會顯示為其他組織中的外部使用者/連絡人。這些不同的內部部署 AD 可同步處理至它們自己的 Azure AD 目錄。

## 後續步驟
若要了解如何安裝這些案例的 Azure AD Connect，請參閱[自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)。

深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)組態。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0629_2016-->