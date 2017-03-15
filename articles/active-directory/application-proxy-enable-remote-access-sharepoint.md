---
title: "使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取 | Microsoft Docs"
description: "涵蓋如何整合內部部署 SharePoint 伺服器與 Azure AD 應用程式 Proxy 的基本概念。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 12e8ff02c72d90513868d43bc34c564f1da1ae43
ms.lasthandoff: 03/08/2017


---

# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取

本文討論如何將內部部署 SharePoint 伺服器與 Azure Active Directory (Azure AD) 應用程式 Proxy 整合。

若要啟用使用 Azure AD 應用程式 Proxy 對 SharePoint 的遠端存取，請遵循本逐步解說文件中的各節。

## <a name="prerequisites"></a>必要條件

本文假設您的環境中已設定和執行 SharePoint 2013 或更新版本。 此外，請考慮下列必要條件︰

* 在升級至 Premium 或 Basic 版本的 Azure Active Directory (Azure AD) 後，才能使用應用程式 Proxy 功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

* SharePoint 包含 Kerberos 的原生支援。 因此，透過 Azure AD 應用程式 Proxy 遠端存取內部網站的使用者，應該可以有順暢的單一登入 (SSO) 體驗。

* 您必須對 SharePoint Server 進行一些組態變更。 建議您使用預備環境。 如此一來，您可以先更新預備伺服器，以便在進行測試週期後再進入生產環境。

* 我們假設您已設定 SharePoint 的 SSL，因為已發佈的 URL 需要使用 SSL。 必須啟用內部網站的 SSL，才能確保可正確傳送/對應連結。 如果您尚未設定 SSL，請參閱[設定 SharePoint 2013 的 SSL](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) 以取得相關指示。 另外，請確定連接器電腦可信任您發出的憑證  (此憑證不需公開發行。)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>步驟 1︰設定 SharePoint 的單一登入

在此案例中，我們的客戶想要在後端應用程式 SharePoint Server 中享有最佳 SSO 體驗。 在這個常見的 Azure AD 案例中，使用者只需驗證一次，因為系統不會提示他們再次進行驗證。

對於需要或使用 Windows 驗證的內部部署應用程式來說，您可以使用 Kerberos 驗證通訊協定和稱為 Kerberos 限制委派 (KCD) 的功能來達成 SSO。 KCD 一經設定，即可讓應用程式 Proxy 連接器為使用者取得 Windows 票證/權杖，即使使用者並未直接登入 Windows 也是如此。 若要深入了解 KCD，請參閱 [Kerberos 限制委派概觀](https://technet.microsoft.com/en-us/library/jj553400.aspx)。

若要設定 SharePoint 伺服器的 KCD，請使用下列後續章節中的程序。

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>確定 SharePoint 是在服務帳戶下執行

首先，確定 SharePoint 是在定義的服務帳戶下執行，而不是本機系統、本機服務或網路服務帳戶下。 請務必確定這一點，您才可以將服務主體名稱 (SPN) 附加至有效的帳戶。 SPN 是 Kerberos 通訊協定用來識別不同服務的方法。 稍候您需要使用該帳戶來設定 KCD。

若要確定網站是根據所定義的服務帳戶來執行，請執行下列作業︰

1. 開啟 [SharePoint 2013 管理中心] 網站。
2. 移至 [安全性]，然後選取 [設定服務帳戶]。
3. 選取 [Web 應用程式集區 - SharePoint - 80]。 根據 Web 集區的名稱或是否 Web 集區預設使用 SSL，選項可能會稍有不同。

  ![設定服務帳戶的選項](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-web-application.png)

4. 如果 [選取此元件的帳戶] 是 [本機服務] 或 [網路服務]，則需要建立帳戶。 若不是，則您已完成，可以進行下一節。
5. 選取 [註冊新的受管理帳戶]。 帳戶建立之後，您必須設定 [Web 應用程式集區] 才能使用該帳戶。

> [!NOTE]
您必須擁有先前為服務建立的 Azure AD 帳戶。 建議您允許密碼自動變更。 如需完整的步驟和針對問題進行疑難排解的詳細資訊，請參閱[在 SharePoint 2013 中設定自動密碼變更](https://technet.microsoft.com/EN-US/library/ff724280.aspx)。

### <a name="configure-sharepoint-for-kerberos"></a>設定 SharePoint 的 Kerberos

您會使用 KCD 來執行 SharePoint Server 的單一登入，而這只能搭配 Kerberos 來運作。

若要設定 SharePoint 網站的 Kerberos 驗證︰

1. 開啟 [SharePoint 2013 管理中心] 網站。
2. 移至 [應用程式管理]，選取 [管理 Web 應用程式]，然後選取 SharePoint 網站。 在此範例中為 **SharePoint - 80**。

  ![選取 SharePoint 網站](./media/application-proxy-remote-sharepoint/remote-sharepoint-manage-web-applications.png)

3. 按一下工具列中的 [驗證提供者]。
4. 在 [驗證提供者] 方塊中，按一下 [預設區域] 來檢視設定。
5. 在 [編輯驗證] 對話方塊中，向下捲動直到您看到 [宣告驗證類型]，並確定已選取 [啟用 Windows 驗證] 和 [整合式 Windows 驗證]。
6. 在下拉式清單方塊中，確定已選取 [交涉 (Kerberos)]。

  ![編輯 [驗證] 對話方塊](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-edit-authentication.png)

7. 在 [編輯驗證] 對話方塊的最下面，按一下 [儲存]。

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>為 SharePoint 服務帳戶設定服務主體名稱

在設定 KCD 之前，您需要識別以上面所設定之服務帳戶身分執行的 SharePoint 服務。 您可以透過設定 SPN 來執行此動作。 如需詳細資訊，請參閱[服務主體名稱](https://technet.microsoft.com/en-us/library/cc961723.aspx)。

SPN 格式如下︰

```
<service class>/<host>:<port>
```

在 SPN 格式中︰

* service class 是服務的唯一名稱。 針對 SharePoint，您會使用 **HTTP**。

* _host_ 是服務執行所在之主機的完整網域或 NetBIOS 名稱。 在 SharePoint 網站的案例中，這可能必須是網站的 URL，視您使用的 IIS 版本而定。

* port 是選擇性的。

如果 SharePoint Server 的 FQDN 是：

```
sharepoint.demo.o365identity.us
```

則 SPN 是︰

```
HTTP/ sharepoint.demo.o365identity.us demo
```

您可能也必須為伺服器上的特定網站設定 SPN。 如需詳細資訊，請參閱[設定 Kerberos 驗證](https://technet.microsoft.com/en-us/library/cc263449(v=office.12).aspx)。 密切注意＜使用 Kerberos 驗證為 Web 應用程式建立服務主體名稱＞一節。

設定 SPN 最簡單的方式是遵循網站上可能已存在的 SPN 格式。 複製這些 SPN 來對服務帳戶進行註冊。 作法：

1. 從另一部電腦使用 SPN 瀏覽至此網站。
 當您這樣做時，系統會快取電腦上的一組相關 Kerberos 票證。 這些票證包含您所瀏覽之目標網站的 SPN。

2. 您可以使用稱為 [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html) 的工具提取該網站的 SPN。 在執行於和瀏覽器中網站存取使用者相同之內容中的命令視窗中，執行下列命令︰
```
Klist
```
然後 Klist 會傳回目標 SPN 的集合。 在此範例中，醒目提示的值即為所需的 SPN：

  ![範例 Klist 結果](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)

4. 現在您已經有 SPN，接下來您必須確定，它已在稍早為 Web 應用程式所設定的服務帳戶上正確設定。 請以網域系統管理員身分從命令提示字元執行下列命令：

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 此命令會為以 _demo\sp_svc_ 身分執行的 SharePoint 服務帳戶設定 SPN。

 將 _http/sharepoint.demo.o365identity.us_ 取代為伺服器的 SPN，並將 _demo\sp_svc_ 取代為環境中的服務帳戶。 Setspn 命令會先搜尋 SPN 再予以新增。 在此情況下，您可能會看到 **SPN 值重複**錯誤。 如果您看到此錯誤，請確定值已與服務帳戶相關聯。

您可以執行 Setspn 命令並搭配 -l 選項，來確認是否已新增 SPN。 若要深入了解此命令，請參閱 [Setspn](https://technet.microsoft.com/en-us/library/cc731241.aspx)。

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>確定已將連接器設定為 SharePoint 信任的委派

設定 KCD，讓 Azure AD 應用程式 Proxy 服務可以將使用者識別委派給 SharePoint 服務。 其作法是啟用應用程式 Proxy 連接器，以便為已在 Azure AD 中驗證的使用者擷取 Kerberos 票證。 然後該伺服器會將內容傳遞至目標應用程式，在此案例中即為 SharePoint。

若要設定 KCD，請針對每個連接器電腦重複下列步驟︰

1. 以網域系統管理員身分登入 DC，然後開啟 **Active Directory 使用者和電腦**。
2. 尋找連接器執行所在的電腦。 在此範例中，它與 SharePoint 伺服器相同。
3. 按兩下該電腦，然後按一下 [委派] 索引標籤。
4. 確定委派設定已設為 [只針對指定服務的委派信任這台電腦]，然後選取 [使用任何驗證通訊協定]。

  ![委派設定](./media/application-proxy-remote-sharepoint/remote-sharepoint-delegation-box.png)

5. 按一下 [新增] 按鈕，按一下 [使用者或電腦]，並找出服務帳戶。

  ![新增服務帳戶的 SPN](./media/application-proxy-remote-sharepoint/remote-sharepoint-users-computers.png)

6. 在 SPN 的清單中，選取您稍早針對服務帳戶建立的 SPN。
7. 按一下 [確定] 。 再按一下 [確定] 以儲存變更。

## <a name="step-2-enable-remote-access-to-sharepoint"></a>步驟 2︰啟用 SharePoint 的遠端存取

現在您已啟用 SharePoint 的 Kerberos 並已設定 KCD，您已準備好可以設定 SharePoint 的單一登入。 接下來，您可以從連接器發佈 SharePoint 伺服器陣列，以便透過 Azure AD 應用程式 Proxy 進行遠端存取。

若要執行下列步驟，您必須是貴組織 Azure Active Directory 帳戶內全域管理員角色的成員。

1. 登入 [Azure 入口網站](https://manage.windowsazure.com)，並尋找您的 Azure AD 租用戶。
2. 按一下 [應用程式]，然後按一下 [新增]。
3. 選取 [發佈將可從您的網路外部存取的應用程式] 。 如果您沒看到這個選項，請確定您已在租用戶中設定了 Azure AD Basic 或 Premium。
4. 完成每個選項，如下所示︰
 * **名稱**︰使用任何您想要的值，例如 SharePoint。
 * **內部 URL**︰這是 SharePoint 網站的內部 URL，例如 **https://SharePoint/**。 在此範例中，請務必使用 **https**。
 * **預先驗證方法**︰選取 [Azure Active Directory]。

  ![用於新增應用程式的選項](./media/application-proxy-remote-sharepoint/remote-sharepoint-add-application.png)

5. 應用程式發佈後，按一下 [設定] 索引標籤。
6. 向下捲動至 [轉譯標頭中的 URL] 選項。 預設值為 [是]。 將它變更為 [否]。

 SharePoint 會使用 [主機標頭] 值來查閱網站。 它也會根據此值產生連結。 最後的結果是可確保 SharePoint 所產生的任何連結，都是已正確設定為使用外部 URL 的已發佈 URL。 將值設定為 [是] 也可讓連接器將要求轉送至後端應用程式。 不過，將值設定為 [否] 表示連接器不會傳送內部主機名稱。 相反地，連接器會傳送主機標頭做為對後端應用程式發佈的 URL。

 此外，若要確保 SharePoint 會接受此 URL，您必須於 SharePoint Server 上再完成一項組態。 您將在下一節中執行該動作。

7. 將 [內部驗證方法] 變更為 [整合式 Windows 驗證]。 如果 Azure AD 租用戶在雲端所使用的 UPN 不同於內部部署 UPN，請記得一併更新 [委派的登入識別]。
8. 將**內部應用程式 SPN** 設定為您先前設定的值。 例如，使用 **http/sharepoint.demo.o365identity.us**。
9. 將應用程式指派給目標使用者。

您的應用程式看起來應該像下面這樣：

  ![完成的應用程式](./media/application-proxy-remote-sharepoint/remote-sharepoint-internal-application-spn.png)

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>步驟 3︰確定 SharePoint 知道外部 URL

最後一個步驟是確定 SharePoint 可以根據外部 URL 找到網站，以便根據該外部 URL 轉譯連結。 其方法是設定 SharePoint 網站的備用存取對應。

1. 開啟 [SharePoint 2013 管理中心] 網站。
2. 在 [系統設定] 下，選取 [設定備用存取對應]。

 這會開啟 [備用存取對應] 方塊。

  ![[備用存取對應] 方塊](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access1.png)

3. 在 [備用存取對應集合] 旁的下拉式清單中，選取 [變更備用存取對應集合]。
4. 選取您的網站，例如 **SharePoint - 80**。

  ![選取網站](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access2.png)

5. 您可以選擇將已發佈的 URL 新增為內部 URL 或公用 URL。 本範例使用公用 URL 做為外部網路。
6. 按一下 [外部網路] 路徑中的 [編輯公用 URL]，然後和上一個步驟一樣，輸入用於所發佈應用程式的路徑。 例如，輸入 **https://sharepoint-iddemo.msappproxy.net**。

  ![輸入路徑](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access3.png)

7. 按一下 [儲存] 。

 您現在可以透過 Azure AD 應用程式 Proxy 從外部存取 SharePoint 網站。

## <a name="next-steps"></a>後續步驟

[如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)<br>
[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-understand-connectors.md)<br>
[使用 Azure AD 應用程式 Proxy 發佈 SharePoint 2016 和 Office Online Server](https://blogs.technet.microsoft.com/dawiese/2016/06/09/publishing-sharepoint-2016-and-office-online-server-with-azure-ad-application-proxy/)

