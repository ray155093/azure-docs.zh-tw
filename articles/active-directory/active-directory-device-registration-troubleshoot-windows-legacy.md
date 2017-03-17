---
title: "針對已加入 Azure AD 網域之 Windows 下層用戶端電腦的自動註冊進行疑難排解 | Microsoft Docs"
description: "針對已加入 Azure AD 網域之 Windows 下層用戶端電腦的自動註冊進行疑難排解。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 49af838d750ba61694bdb28a83ee6e531627527d
ms.lasthandoff: 03/10/2017


---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad-for-windows-down-level-clients"></a>針對已加入網域之 Windows 下層用戶端電腦的自動註冊 Azure AD 進行疑難排解 

本主題僅適用於下列用戶端： 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

針對 Windows 10 或 Windows Server 2016，請參閱[針對已加入網域之電腦的自動註冊 Azure AD 進行疑難排解 – Windows 10 和 Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md)。

本主題假設您已經依照[如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊](active-directory-device-registration-get-started.md)所述，設定讓已加入網域的裝置自動註冊。
 
本主題提供有關如何解決潛在問題的疑難排解指引。  
以下是獲得成功結果的一些注意事項： 

- 在 Azure AD 上註冊這些用戶端時是依據個別使用者/裝置。 舉例來說：如果 jdoe 和 jharnett 登入此裝置，在 [使用者資訊] 索引標籤中就會為這每位使用者建立個別的註冊 (DeviceID)。  

- 預設是設定為在登入或鎖定/解除鎖定時嘗試註冊這些用戶端，而且可能會有 5 分鐘的延遲，這是使用「工作排程器」工作來觸發。 

- 重新安裝作業系統或手動取消註冊再重新註冊時，可能會在 Azure AD 上建立新的註冊，而將導致在 Azure 入口網站中 [使用者資訊] 索引標籤底下有多個項目。 


## <a name="step-1-retrieve-the-registration-status"></a>步驟 1：擷取註冊狀態 

**確認註冊狀態：**  

1. 以系統管理員身分開啟命令提示字元 

2. 輸入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

此命令會顯示一個對話方塊，可提供您有關加入狀態的更多詳細資料。

![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-registration-status"></a>步驟 2：評估註冊狀態 

如果未成功加入，對話方塊會提供有關所發生問題的詳細資料。

**最常見的問題包括：**

- AD FS 或 Azure AD 設定不正確

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- 您不是以網域使用者身分登入

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)

- 已達到配額限制

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- 服務沒有回應 

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

您也可以在事件記錄檔的 [應用程式及服務記錄檔] > [Microsoft-Workplace Join] 底下找到狀態資訊。
  
**註冊失敗的最常見原因包括︰** 

- 您的電腦不在組織的內部網路上，或不在可連線到內部部署 AD 網域控制站的 VPN 上。

- 您是使用本機電腦帳戶來登入您的電腦。 

- 服務組態問題： 

  - 同盟伺服器已設定為支援 **WIAORMULTIAUTHN**。 

  - 沒有「服務連接點」物件指向電腦所屬 AD 樹系之 Azure AD 中已驗證的網域名稱。

  - 使用者已達到裝置限制。 請參閱＜開始使用 Azure Active Directory 裝置註冊＞。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[自動裝置註冊常見問題集](active-directory-device-registration-faq.md) 

