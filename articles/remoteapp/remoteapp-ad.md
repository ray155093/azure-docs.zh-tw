---
title: "Azure RemoteApp 的 Azure AD + Active Directory 需求 | Microsoft Docs"
description: "了解如何設定 Active Directory 以使用 Azure RemoteApp。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 66366b25-6012-45fa-a4f6-da0ddfe0b486
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 232901ab919c63ea70e52afb845240b41a517c51
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure RemoteApp 的 Azure AD + Active Directory 需求
> [!IMPORTANT]
> Azure RemoteApp 即將於 2017 年 8 月 31 日停止服務。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
> 
> 

針對 Azure RemoteApp 混合式集合，或您要使用 AD Connect 建立同盟的雲端集合，您必須執行下列動作。

### <a name="connect-azure-ad-and-active-directory"></a>連接 Azure AD 和 Active Directory
如果您要連接 Azure AD 租用戶和內部部署的 Active Directory 環境，請使用 AD Connect。 您只要 [按 4 下](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) 即可連接兩個目錄。

注意 - 混合式集合需使用目錄同步處理。

### <a name="make-sure-your-domaincom-match"></a>請確定您的 "@domain.com" 是相符的
在開始之前，請確定您在內部部署樹系的 UPN 符合您 Azure AD 網域的尾碼。 

當您在 Azure AD 中設定 UPN 網域尾碼後，所有登入 Azure RemoteApp 的使用者都會以 "user@<the suffix you set up>" 的身分登入。 請確定使用者也可以用相同的 user@suffix 登入到內部部署網域。 在某些情況下，您可以在 Azure AD 中設定一個網域名稱，並同時為內部部署使用者指定不同的網域尾碼。 在此情況下，您的使用者將無法透過 Azure RemoteApp，連接至任何已加入網域的電腦或資源。

例如，如果您在 AAD 中將 UPN 網域尾碼設定為 contoso.com，但內部部署/AD 的某些使用者是設為以 @contoso.uk 登入，則那些使用者將無法正確登入 ARA 集合。 AAD 和 AD 中的使用者 UPN 必須相同才能登入。

### <a name="create-objects-for-azure-remoteapp"></a>建立 Azure RemoteApp 的物件
您也必須建立下列內部部署 Active Directory 物件：

* 服務帳戶，以透過將 RDSH 端點加入內部部署的網域，提供 RemoteApp 程式的網域資源存取權。
* 組織單位 (OU)，以包含 RemoteApp 電腦物件。 建議您使用 OU (但非必要) 來隔離帳戶與將用於 RemoteApp 的原則。

當您建立 RemoteApp 集合時，您會需要這兩個物件，因此請務必先執行這些步驟。


