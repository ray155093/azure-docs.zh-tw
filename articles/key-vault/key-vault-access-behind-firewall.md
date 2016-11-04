---
title: 存取防火牆後面的金鑰保存庫 | Microsoft Docs
description: 了解如何從防火牆後面的應用程式存取金鑰保存庫
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/13/2016
ms.author: ambapat

---
# 存取防火牆後面的金鑰保存庫
### 問︰我的金鑰保存庫用戶端應用程式必須在防火牆後面，我應開啟哪些連接埠/主機 IP 位址才能夠存取金鑰保存庫？
若要存取金鑰保存庫，您的金鑰保存庫用戶端應用程式必須能夠存取多個端點才能使用各種功能。

* 驗證 (透過 Azure Active Directory)
* 透過 Azure Resource Manager 管理金鑰保存庫 (其中包含建立/讀取/更新/刪除以及設定存取原則)
* 經由金鑰保存庫特定端點 (例如 [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net))，存取和管理金鑰保存庫本身儲存的物件 (金鑰和密碼)。

視您的組態和環境而定，會有一些變化。

## 連接埠
3 項功能 (驗證、管理和資料面存取) 的金鑰保存庫的所有流量都會透過 HTTPS︰連接埠 443 傳送。不過對於 CRL，偶爾會有 HTTP (連接埠 80) 流量。支援 OCSP 的用戶端不應該觸達 CRL，但可能偶爾會觸達 [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl)。

## 驗證
金鑰保存庫用戶端應用程式必須存取 Azure Active Directory 端點以便驗證。使用的端點取決於 AAD 租用戶組態和主體類型 (使用者主體、服務主體) 和帳戶類型 (例如 Microsoft 帳戶或組織識別碼)。

| 主體類型 | 端點:連接埠 |
| --- | --- |
| 使用 Microsoft 帳戶的使用者<br> (例如 user@hotmail.com) |**全域：**<br> login.microsoftonline.com:443<br><br> **Azure China：**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government：**<br> login-us.microsoftonline.com:443<br><br>**Azure Germany：**<br> login.microsoftonline.de:443<br><br> 和 <br>login.live.com:443 |
| 在 AAD 中使用組織識別碼的使用者/服務主體 (例如 user@contoso.com) |**全域：**<br> login.microsoftonline.com:443<br><br> **Azure China：**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government：**<br> login-us.microsoftonline.com:443<br><br>**Azure Germany：**<br> login.microsoftonline.de:443 |
| 使用組織識別碼 + ADFS 或其他同盟端點的使用者/服務主體 (例如 user@contoso.com) |組織識別碼的所有上述端點加上 ADFS 或其他同盟端點 |

還有其他可能的複雜案例。如需其他資訊，請參閱 [Azure Active Directory 驗證流程](/documentation/articles/active-directory-authentication-scenarios/)、[整合應用程式與 Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) 及 [Active Directory 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn151124.aspx)。

## 金鑰保存庫管理
對於金鑰保存庫管理 (CRUD 和設定存取原則)，金鑰保存庫用戶端應用程式需要存取 Azure Resource Manager 端點。

| 作業類型 | 端點:連接埠 |
| --- | --- |
| 透過 Azure Resource Manager 的<br>金鑰保存庫控制項面作業 |**全域：**<br> management.azure.com:443<br><br> **Azure China：**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government：**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> management.microsoftazure.de:443 |
| Azure Active Directory 圖形 API |**全域：**<br> graph.windows.net:443<br><br> **Azure China：**<br> graph.chinacloudapi.cn:443<br><br> **Azure US Government：**<br> graph.windows.net:443<br><br> **Azure Germany：**<br> graph.cloudapi.de:443 |

## 金鑰保存庫作業
對於所有金鑰保存庫物件 (金鑰和密碼) 管理和密碼編譯作業，金鑰保存庫用戶端需要存取金鑰保存庫端點。視您的金鑰保存庫的位置而定，端點 DNS 尾碼會有所不同。金鑰保存庫端點的格式為︰<vault-name>.<region-specific-dns-suffix>，如下表所述。

| 作業類型 | 端點:連接埠 |
| --- | --- |
| 金鑰保存庫作業，例如：金鑰的密碼編譯作業、建立/讀取/更新/刪除金鑰和密碼、在金鑰保存庫物件 (索引鍵/密碼) 設定/取得標籤和其他屬性 |**全域：**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China：**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government：**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## IP 位址範圍
金鑰保存庫服務接著會使用其他 Azure 資源 (如 PaaS 基礎結構)，因此不可能提供金鑰保存庫服務端點在任何指定時間會有的特定 IP 位址範圍。不過，如果您的防火牆只支援 IP 位址範圍然後，請參閱 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)文件。如需驗證和身分識別 (Azure Active Directory)，您的應用程式必須能夠連接至[驗證和身分識別位址](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中所述的端點。

## 後續步驟
* 如果您有關於金鑰保存庫的問題，請造訪 [Azure 金鑰保存庫論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!---HONumber=AcomDC_0921_2016-->