---
title: "使用 Azure MFA 與協力廠商 VPN 的進階案例"
description: "要與 Cisco、Citrix 和 Juniper 整合之 Azure MFA 的逐步設定指南。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 1f94a214-d6f6-48a8-8a12-006b5896ae45
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 6c8420a154d998aa95c0220049ee54b3039a872b
ms.openlocfilehash: 1724d9d482597cd8491ad1ea86f48beefbfa1531

---

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>使用 Azure Multi-Factor Authentication 與協力廠商 VPN 解決方案的進階案例
Azure Multi-Factor Authentication 可以用來與各種協力廠商 VPN 解決方案順暢地連接。 本文著重於 Cisco® ASA VPN 應用裝置、Citrix NetScaler SSL VPN 應用裝置和 Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN 應用裝置。 我們建立了這三種常見應用裝置的設定指南，但 Multi-Factor Authentication Server 可以與對 AD FS 使用 RADIUS、LDAP、IIS 或宣告式驗證的大部分系統整合。 您可以在 [MFA Server 組態](multi-factor-authentication-get-started-server.md#next-steps)中找到更多詳細資料。

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Cisco ASA VPN 應用裝置和 Azure Multi-Factor Authentication
Azure Multi-Factor Authentication 可以與您的 Cisco® ASA VPN 應用裝置緊密整合，以提供 Cisco AnyConnect® VPN 登入和入口網站存取的額外安全性。  可以使用 LDAP 或 RADIUS 通訊協定來完成這項操作。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | 說明 |
| --- | --- |
| [Cisco ASA with Anyconnect VPN 與 Azure MFA Configuration for LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) |使用 LDAP 緊密整合 Cisco ASA VPN 應用裝置與 Azure MFA |
| [Cisco ASA with Anyconnect VPN 與 Azure MFA Configuration for RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) |使用 RADIUS 緊密整合 Cisco ASA VPN 應用裝置與 Azure MFA |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN 與 Azure Multi-Factor Authentication
Azure Multi-Factor Authentication 可以與您的 Citrix NetScaler SSL VPN 應用裝置緊密整合，以提供 Citrix NetScaler SSL VPN 登入和入口網站存取的額外安全性。  可以使用 LDAP 或 RADIUS 通訊協定來完成這項操作。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | 說明 |
| --- | --- |
| [Citrix NetScaler SSL VPN 與 Azure MFA Configuration for LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) |使用 LDAP 緊密整合 Citrix NetScaler SSL VPN 與 Azure MFA |
| [Citrix NetScaler SSL VPN 與 Azure MFA Configuration for RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) |使用 RADIUS 緊密整合 Citrix NetScaler SSL VPN 應用裝置與 Azure MFA |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/Pulse Secure SSL VPN 應用裝置和 Azure Multi-Factor Authentication
Azure Multi-Factor Authentication 可以與您的 Juniper/Pulse Secure SSL VPN 應用裝置緊密整合，以提供 Juniper/Pulse Secure SSL VPN 登入和入口網站存取的額外安全性。  可以使用 LDAP 或 RADIUS 通訊協定來完成這項操作。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | 說明 |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 與 Azure MFA Configuration for LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) |使用 LDAP 緊密整合 Juniper/Pulse Secure SSL VPN 與 Azure MFA |
| [Juniper/Pulse Secure SSL VPN 與 Azure MFA Configuration for RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) |使用 RADIUS 緊密整合 Juniper/Pulse Secure SSL VPN 應用裝置與 Azure MFA |



<!--HONumber=Feb17_HO2-->


