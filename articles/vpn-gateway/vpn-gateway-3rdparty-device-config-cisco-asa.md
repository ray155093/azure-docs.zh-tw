---
title: "範例組態：連線到 Azure VPN 閘道的 Cisco ASA 裝置 | Microsoft Docs"
description: "本文提供適用於連線到 Azure VPN 閘道之 Cisco ASA 裝置的範例組態。"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 10466b8928e2cd687f7961a2956b6d60823b82be
ms.contentlocale: zh-tw
ms.lasthandoff: 07/10/2017


---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>範例組態：Cisco ASA 裝置 (IKEv2/無 BGP)
本文提供適用於連線到 Azure VPN 閘道之 Cisco ASA 裝置的範例組態。

## <a name="device-at-a-glance"></a>裝置速覽

|                        |                                   |
| ---                    | ---                               |
| 裝置廠商          | Cisco                             |
| 裝置型號           | ASA (Adaptive Security Appliance) |
| 目標版本         | 8.4+                              |
| 測試的型號           | ASA 5505                          |
| 測試的版本         | 9.2                               |
| IKE 版本            | **IKEv2**                         |
| BGP                    | **否**                            |
| Azure VPN 閘道類型 | **路由式** VPN 閘道       |
|                        |                                   |

> [!NOTE]
> 1. 下列組態會搭配 "UserPolicyBasedTrafficSelectors" 選項使用自訂 IPsec/IKE 原則，將 Cisco ASA 裝置連線到 Azure **路由式** VPN 閘道，如[這篇文章](vpn-gateway-connect-multiple-policybased-rm-ps.md)中所述。
> 2. 它會要求 ASA 裝置使用**IKEv2** 搭配以存取清單為基礎的組態，而非 VTI 式組態。
> 3. 請確認 VPN 裝置廠商規格，確保內部部署 VPN 裝置支援原則。

## <a name="vpn-device-requirements"></a>VPN 裝置需求
Azure VPN 閘道會使用標準的 IPsec/IKE 通訊協定組合來建立 S2S VPN 通道。 請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)，以取得詳細的 IPsec/IKE 通訊協定參數，以及 Azure VPN 閘道的預設密碼編譯演算法。 您可以選擇性地針對特定連線指定密碼編譯演算法和金鑰長度的確切組合，如[關於密碼編譯需求](vpn-gateway-about-compliance-crypto.md)中所述。 如果您選取密碼編譯演算法和金鑰長度的特定組合，請確定您會在 VPN 裝置上使用對應的規格。

## <a name="single-vpn-tunnel"></a>單一 VPN 通道
此拓撲是由 Azure VPN 閘道與您內部部署 VPN 裝置之間的單一 S2S VPN 通道所組成。 您可以選擇透過 VPN 通道設定 BGP。

![單一通道](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

請參閱[單一通道設定](vpn-gateway-3rdparty-device-config-overview.md#singletunnel)，以取得建置 Azure 組態的詳細逐步指示。

### <a name="network-and-vpn-gateway-information"></a>網路和 VPN 閘道資訊
本節會列出適用於此範例的參數。

| **參數**                | **值**                    |
| ---                          | ---                          |
| VNet 位址首碼        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN 閘道 IP         | Azure_Gateway_Public_IP      |
| 內部部署位址首碼 | 10.51.0.0/16<br>10.52.0.0/16 |
| 內部部署 VPN 裝置 IP    | OnPrem_Device_Public_IP     |
| *VNet BGP ASN                | 65010                        |
| *Azure BGP 對等 IP           | 10.12.255.30                 |
| *內部部署 BGP ASN         | 65050                        |
| *內部部署 BGP 對等 IP     | 10.52.255.254                |
|                              |                              |

* (*) 僅適用於 BGP 的選擇性參數。

### <a name="ipsecike-policy--parameters"></a>IPsec/IKE 原則與參數

下表列出範例中所使用的 IPsec/IKE 演算法與參數。 請參閱您的 VPN 裝置規格，以確定您的 VPN 裝置型號和軔體版本支援以上所列的所有演算法。

| **IPsec/IKEv2**  | **值**                            |
| ---              | ---                                  |
| IKEv2 加密 | AES256                               |
| IKEv2 完整性  | SHA384                               |
| DH 群組         | DHGroup24                            |
| IPsec 加密 | AES256                               |
| IPsec 完整性  | SHA1                                 |
| PFS 群組        | PFS24                                |
| QM SA 存留期   | 7200 秒                         |
| 流量選取器 | UsePolicyBasedTrafficSelectors $True |
| 預先共用金鑰   | PreSharedKey                         |
|                  |                                      |

- (*) 在某些裝置上，如果使用 GCM AES 作為 IPsec 加密演算法，IPsec 完整性就必須為 "null"。

### <a name="device-notes"></a>裝置注意事項

>[!NOTE]
>
> 1. IKEv2 支援需要 ASA 8.4 版及更新版本。
> 2. 較高的 DH 和 PFS 群組支援 (除了群組 5) 需要 ASA 版本 9.x。
> 3. 在較新的 ASA 硬體上，支援透過 AES-GCM 進行的 IPsec 加密及具備 SHA-256、SHA-384、SHA-512 的 IPsec 完整性需要 ASA 版本 9.x；**不**支援 ASA 5505、5510、5520、5540、5550、5580 (請檢查廠商規格加以確認)。
>


### <a name="sample-device-configurations"></a>範例裝置組態
下列指令碼會根據上面所列的參數與拓撲提供範例組態。 S2S VPN 通道組態包含下列組件：

1. 介面與路由
2. 存取清單存取清單
3. IKE 原則和參數 (第 1 階段或主要模式)
4. IPsec 原則和參數 (第 2 階段或快速模式)
5. 其他參數 (TCP MSS 固定等)

>[!IMPORTANT] 
>請確定您會完成下面所列的其他組態，並使用實際值取代預留位置：
> 
> - 適用於內部和外部介面的介面組態
> - 適用於您內部/私用和外部/公用網路的路由
> - 確定所有名稱和原則編號在裝置上都是唯一的
> - 確定您的裝置上支援密碼編譯演算法
> - 使用實際的值取代下列預留位置
>   - 外部介面名稱："outside"
>   - Azure_Gateway_Public_IP
>   - OnPrem_Device_Public_IP
>   - IKE Pre_Shared_Key
>   - VNet 與區域網路閘道名稱 (VNetName、LNGName)
>   - VNet 和內部部署網路位址首碼
>   - 適當的網路遮罩

#### <a name="sample-configuration"></a>範例組態

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>簡單偵錯命令

以下是一些可基於偵錯用途使用的 ASA 命令：

1. 顯示 IPsec 和 IKE SA 的命令
    - "show crypto ipsec sa"
    - "show crypto ikev2 sa"
2. 進入偵錯模式：這在主控台上會變得非常冗長
    - "debug crypto ikev2 platform <level>"
    - "debug crypto ikev2 protocol <level>"
3. 列出目前的組態
    - "show run"：顯示裝置上目前的組態；您可以使用各種子命令來列出組態的特定部分。 例如，"show run crypto"、"show run access-list"、"show run tunnel-group" 等。


## <a name="next-steps"></a>後續步驟
如需設定主動-主動跨單位和 VNet 對 VNet 連線的步驟，請參閱[設定跨單位和 VNet 對 VNet 連線的主動-主動 VPN 閘道](vpn-gateway-activeactive-rm-powershell.md)。


