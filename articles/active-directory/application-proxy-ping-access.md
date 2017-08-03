---
title: "標頭型驗證搭配 Azure AD 應用程式 Proxy 的 PingAccess | Microsoft Docs"
description: "使用 PingAccess 與應用程式 Proxy 來發行應用程式可支援標頭型驗證。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: kgremban
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 0983f346bed012976d48d05d9c9246683205b1e1
ms.contentlocale: zh-tw
ms.lasthandoff: 06/16/2017

---

# <a name="publish-applications-that-support-header-based-authentication-with-azure-ad-application-proxy-and-pingaccess"></a>發行支援標頭型驗證搭配 Azure AD 應用程式 Proxy 和 PingAccess 的應用程式

Azure Active Directory 應用程式 Proxy 和 PingAccess 通力合作，為 Azure Active Directory 客戶提供甚至更多應用程式的存取權。 PingAccess 會展開[現有應用程式 Proxy 供應項目](active-directory-application-proxy-get-started.md)以包含遠端存取使用標頭進行驗證的應用程式。

## <a name="what-is-pingaccess-for-azure-ad"></a>什麼是 Azure AD 的 PingAccess？

若要讓使用者可存取使用標頭進行驗證的應用程式，請在應用程式 Proxy 與 PingAccess 中發行遠端存取的應用程式。 應用程式 Proxy 會如同任何其他應用程式一樣處理這些應用程式，使用 Azure AD 驗證存取，然後透過連接器服務傳遞流量。 PingAccess 位於前方的應用程式，並將 Azure AD 的存取權杖轉譯為標頭，使應用程式以它可以讀取的格式收到驗證。

您的使用者在登入使用您公司的應用程式時，將不會注意到什麼不同。 這些還是可以在任何裝置上從任何地方運作。 當使用者在辦公室時，應用程式 Proxy 或 PingAccess 都不會攔截流量，讓您的使用者能夠一如往常般獲得相同的體驗。

由於應用程式 Proxy 連接器會將遠端流量導向至所有應用程式，而不論其驗證類型為何，因此它們也將繼續自動載入平衡。

## <a name="how-do-i-get-access"></a>如何取得存取權？

因為這種情況是透過 Azure Active Directory 和 PingAccess 之間的合作關係提供，您會需要這兩種服務的授權。 Azure Active Directory Premium 訂用帳戶隨附入門 PingAccess 授權，讓您可以使用這個流量設定最多 20 個應用程式。

如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

## <a name="publish-your-first-application"></a>發佈您的第一個應用程式

本文是針對第一次要以此案例發佈應用程式的人所設計。 除了發佈的步驟之外，並逐步解說如何開始使用應用程式和 PingAccess。 如果您已經設定這兩項服務，但想要發佈步驟上的重新整理程式，可以略過兩個註冊區段。

>[!NOTE]
>因為此案例是 Azure AD 和 PingAccess 之間的合作關係，有些指示存在於 Ping 身分識別站台。

### <a name="install-an-application-proxy-connector"></a>安裝應用程式 Proxy 連接器

如果您已啟用應用程式 Proxy 並已安裝連接器，您可以略過這些步驟。

應用程式 Proxy 連接器是 Windows Server 服務，可將流量從您的遠端員工引導至發行的應用程式。 如需細安裝指示，請參閱[在 Azure 入口網站中啟用應用程式 Proxy](active-directory-application-proxy-enable.md)。

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 **Azure Active Directory** > **應用程式 Proxy**。
3. 選取**下載連接器**以啟動應用程式 Proxy 連接器下載。 請遵循安裝指示。
4. 下載連接器應會自動啟用您目錄的應用程式 Proxy，但如果沒有，您可以選取**啟用應用程式 Proxy**。

![啟用應用程式 Proxy 並下載連接器](./media/application-proxy-ping-access/install-connector.png)

### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>使用應用程式 Proxy 將您的應用程式新增至 Azure AD

此節分成兩部分。 首先，您必須將應用程式發佈至 Azure AD。 然後，您需要收集一些關於該應用程式的資訊，可供您在 PingAccess 步驟期間使用。

#### <a name="publish-the-app"></a>發佈應用程式

1. 如果您在上一節中未登入，請以全域系統管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [Azure Active Directory]  >  [企業應用程式]。
3. 在刀鋒視窗頂端選取 [新增]。
4. 選取**內部部署應用程式**。
5. 使用新應用程式的相關資訊填寫必要的欄位。 使用下列指導方針設定︰
   - **內部 URL**︰當您在公司網路上時，通常會提供此 URL 以帶您前往應用程式登入頁面。 基於此合作關係，連接器需要將 PingAccess Proxy 視為應用程式的首頁。 使用此格式︰`https://<host name of your PA server>:<port>`。 連接埠預設為 3000，但您可以在 PingAccess 中設定它。
   - **預先驗證方法**︰Azure Active Directory
   - **轉譯標頭中的 URL**：否

   >[!NOTE]
   >如果這是您的第一個應用程式，請在變更 Ping 存取設定時，使用連接埠 3000 啟動並返回以更新這項設定。 如果這是您的第二個或更後面的應用程式，則這必須符合您已在 PingAccess 中設定的接聽程式。 深入了解 [PingAccess 中的接聽程式](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html)。

6. 選取刀鋒視窗底部的 [新增]。 已新增您的應用程式，快速入門功能表隨即開啟。
7. 在 [快速啟動] 功能表中，選取 [指派測試使用者]，並將至少一個使用者新增至應用程式。 請確定此測試帳戶可存取內部部署應用程式。
8. 選取**指派**以儲存測試使用者指派。
9. 在 [應用程式管理] 刀鋒視窗中，選取 [單一登入]。
10. 從下拉式選單選擇 [標頭型登入]。 選取 [ **儲存**]。

  ![選取標頭形式的登入](./media/application-proxy-ping-access/sso-header.PNG)

11. 關閉企業應用程式刀鋒視窗或捲動到最左邊，回到 Azure Active Directory 功能表。
12. 選取 [應用程式註冊]。
13. 選取您剛才新增的應用程式，然後**回覆 URL**。
14. 請檢查外部 URL，看看您在步驟 5 中所指派的應用程式是否在回覆 URL 清單。 如果不存在，請立即新增。
15. 在應用程式設定刀鋒視窗中，選取 [必要權限]。
16. 選取 [新增] 。 針對 API，依序選擇 [Windows Azure Active Directory]、[選取]。 針對權限，依序選擇 [讀取及寫入所有應用程式]、[登入及讀取使用者個人檔案]、[選取] 和 [完成]。  

  ![選取權限](./media/application-proxy-ping-access/select-permissions.png)

#### <a name="collect-information-for-the-pingaccess-steps"></a>收集 PingAccess 步驟的資訊

1. 仍從應用程式設定刀鋒視窗中，選取 [屬性]。 儲存 [應用程式識別碼] 值。 當您設定 PingAccess 時，這會用於用戶端識別碼。
2. 在應用程式設定刀鋒視窗中，選取 [金鑰]。
3. 輸入金鑰描述，然後從下拉式選單中選擇到期日來建立金鑰。
4. 選取 [ **儲存**]。 GUID 會出現在 [值] 欄位中。

  立即儲存此值，因為您關閉此視窗之後將無法再次看見它。

5. 關閉應用程式註冊刀鋒視窗或捲動到最左邊，回到 Azure Active Directory 功能表。
6. 選取 [屬性] 。
7. 儲存**目錄識別碼** GUID。

### <a name="download-pingaccess-and-configure-your-app"></a>下載 PingAccess 及設定您的應用程式

這個案例的 PingAccess 部分詳細步驟會在 Ping 身分識別文件中[設定 Azure AD 的 PingAccess](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) 繼續進行。

這些步驟會逐步引導您取得 PingAccess 帳戶 (如果您還沒有)、安裝 PingAccess 伺服器，以及使用您從 Azure 入口網站複製之目錄識別碼建立 Azure AD OIDC 提供者連線的程序。 然後，您可以使用應用程式識別碼和金鑰值在 PingAccess 上建立 Web 工作階段。 之後，您可以設定身分識別對應，並建立虛擬主機、網站和應用程式。

### <a name="test-your-app"></a>測試應用程式

當您完成所有這些步驟時，您的應用程式應該啟動並執行。 若要測試它，請開啟瀏覽器並瀏覽至您在 Azure 中發佈應用程式時所建立的外部 URL。 使用您指派給應用程式的測試帳戶來登入。

## <a name="next-steps"></a>後續步驟

- [疑難排解應用程式 Proxy](active-directory-application-proxy-troubleshoot.md)

