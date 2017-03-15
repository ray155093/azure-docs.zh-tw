---
title: "Azure AD Connect 和同盟 | Microsoft Docs"
description: "此頁面是有關使用 Azure AD Connect 執行 AD FS 作業的所有文件的中心位置"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 39b0b15dba098c0ddc59e39a81bfb6809f8cb2eb
ms.lasthandoff: 03/07/2017


---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect 和同盟
Azure AD Connect 可讓您設定與內部部署 AD FS 和 Azure AD 同盟。 使用同盟登入，您可以讓使用者使用內部部署密碼登入 Azure AD 服務；使用公司網路時，無須再次輸入密碼就可登入服務。 使用 AD FS 的同盟選項可讓您在 Windows Server 2012 R2 伺服器陣列中部署新的或指定現有的 AD FS。

本主題是 Azure AD Connect 的「同盟」相關功能的主要資訊來源，並列出其他所有相關主題的連結。 如需 Azure AD Connect 的連結，請參閱＜整合內部部署身分識別與 Azure Active Directory＞。

## <a name="azure-ad-connect---federation-topics"></a>Azure AD Connect - 同盟主題
| 主題 | 涵蓋內容和讀取時機 |
|:--- |:--- |
| **Azure AD Connect 使用者登入選項** | |
| [了解使用者登入選項](active-directory-aadconnect-user-signin.md) |描述各種使用者登入選項及其如何影響 Azure 登入使用者體驗 |
| **使用 Azure AD Connect 安裝 AD FS** | |
| [必要條件](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |透過 Azure AD Connect 成功安裝 AD FS 安裝的必要條件 |
| [設定 AD FS 伺服器陣列](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |如何使用 Azure AD Connect 安裝新的 AD FS 伺服器陣列 |
| **修改 AD FS 組態** | |
| [修復信任](active-directory-aadconnect-federation-management.md#repairthetrust) |如何修復內部部署 AD FS 和 Office 365 / Azure 之間目前的信任 |
| [新增新的 AD FS 伺服器](active-directory-aadconnect-federation-management.md#addadfsserver) |初始安裝後增加 AD FS 伺服器來擴充 AD FS 伺服器陣列 |
| [新增新的 AD FS WAP 伺服器](active-directory-aadconnect-federation-management.md#addwapserver) |初始安裝後增加 WAP 伺服器來擴充 AD FS 伺服器陣列 |
| [新增新的同盟網域](active-directory-aadconnect-federation-management.md#addfeddomain) |新增要與 Azure AD 同盟的其他網域 |
| [更新 SSL 憑證](active-directory-aadconnectfed-ssl-update.md)| 更新 Active Directory Federation Services (AD FS) 伺服器陣列的 SSL 憑證|
| **後續安裝工作** | |
| [新增自訂公司標誌/圖例](active-directory-aadconnect-federation-management.md#customlogo) |指定將顯示在 AD FS 登入頁面的自訂標誌來修改登入體驗 |
| [新增登入說明](active-directory-aadconnect-federation-management.md#addsignindescription) |變更 AD FS 登入頁面上的登入說明 |
| [修改 AD FS 宣告規則](active-directory-aadconnect-federation-management.md#modclaims) |修改/新增 AD FS 中對應至 Azure AD Connect 同步組態的宣告規則 |

## <a name="additional-resources"></a>其他資源
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)
* [Azure 中的 AD FS 部署](active-directory-aadconnect-azure-adfs.md)
* [使用 Azure 流量管理員在 Azure 中部署高可用性跨地區 AD FS](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

