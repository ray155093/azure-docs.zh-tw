---
title: "Microsoft Azure 雲端服務之設定和管理問題的常見問題集 | Microsoft Docs"
description: "本文列出 Microsoft Azure 雲端服務之設定和管理的相關常見問題集。"
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7d7676be26a8b68ab9fda18388e2b8cd5ed5f60b
ms.contentlocale: zh-tw
ms.lasthandoff: 06/15/2017


---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure 雲端服務之設定和管理問題：常見問題集 (FAQ)

本文包含 [Microsoft Azure 雲端服務](https://azure.microsoft.com/services/cloud-services)之設定和管理問題的相關常見問題集。 您也可以參閱 [雲端服務 VM 大小頁面](cloud-services-sizes-specs.md) 以取得大小資訊。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>如何將 "nosniff" 新增至我的網站？
若要防止用戶端探查 MIME 類型，請在您的 *web.config* 檔案中加入一項設定。

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

您也可以在 IIS 中將此加入為設定。 請參考[常見的啟動工作](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)一文來使用下列命令。

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

## <a name="how-do-i-customize-iis-for-a-web-role"></a>如何自訂 Web 角色的 IIS？
請從[常見的啟動工作](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)一文使用 IIS 啟動指令碼。

## <a name="i-cannot-scale-beyond-x-instances"></a>我不能調整超過 X 個執行個體
您的 Azure 訂用帳戶對於您可以使用的核心數目有限制。 如果您已使用所有可用的核心，調整將無法運作。 例如，如果您有 100 個核心的限制，這表示您的雲端服務可以有 100 個 A1 大小的虛擬機器執行個體，或 50 個 A2 大小的虛擬機器執行個體。

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>如何實作雲端服務的角色型存取？
雲端服務不支援角色型存取控制 (RBAC) 模型，因為它不是以 Azure Resource Manager 為基礎的服務。

請參閱 [Azure RBAC 與傳統訂用帳戶系統管理員](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators)。

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>如何設定 Azure Load Balancer 的閒置逾時？
您可以在服務定義 (csdef) 檔中指定逾時，就像這樣：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
如需詳細資訊，請參閱[新增：Azure Load Balancer 的可設定閒置逾時](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/)。

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>Microsoft 內部工程師是否可在沒有權限的情況下，RDP 到雲端服務執行個體？
Microsoft 會遵循嚴格的程序，不允許內部工程師在沒有擁有者或其受指派者寫入權限的情況下，RDP 到您的雲端服務 (電子郵件或其他撰寫的通訊) 中。

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>為什麼我雲端服務 SSL 憑證的憑證鏈結是不完整的？
我們建議客戶安裝完整的憑證鏈結而不是分葉憑證 (分葉憑證、中繼憑證、和根憑證)。 當您安裝分葉憑證時，會依賴 Windows 透過查核 CTL 來建置憑證鏈結。 如果當 Windows 嘗試驗證憑證時，在 Azure 或 Windows Update 中發生間歇性網路或 DNS 問題，就可能會將憑證視為無效。 藉由安裝完整的憑證鏈結，就可以避免這個問題。 [如何安裝鏈結的 SSL 憑證](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/)中的部落格會示範如何執行這項操作。

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>如何將靜態 IP 位址關聯到我的雲端服務？
若要設定靜態 IP 位址，您必須建立保留的 IP。 這個保留的 IP 可以關聯到新的雲端服務或現有的部署。 請參閱以下文件了解詳細資料：
* [如何建立保留的 IP 位址](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [保留現有雲端服務的 IP 位址](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [建立保留的 IP 至新雲端服務的關聯](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [建立保留的 IP 至執行中部署的關聯](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [使用服務組態檔建立保留的 IP 至雲端服務的關聯](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>什麼是我的雲端服務配額限制？
請參閱[特定服務的限制](../azure-subscription-service-limits.md#subscription-limits)。

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>為什麼我雲端服務 VM 上的磁碟機顯示幾乎沒有可用的磁碟空間？
這是預期的行為，並不會對您的應用程式造成任何問題。 在 Azure PaaS VM 中會開啟 %uproot% 磁碟機的日誌記錄，基本上會消耗兩倍檔案通常所佔用的空間數量。 不過，要留意幾件事，基本上這就會變得沒有問題。

%approot% 磁碟機大小會以 <.cspkg 的大小 + 最大的日誌大小 + 可用空間的邊界> 來計算，或 1.5 GB，兩者取其較大。 您 VM 的大小對這個計算方式並無任何影響。 (VM 大小只會影響暫存 C: 磁碟機的大小。) 

它不支援寫入 %approot% 磁碟機。 如果您要寫入 Azure VM 中，必須在暫存 LocalStorage 資源中進行 (或其他選項，例如 Blob 儲存體、Azure 檔案等)。 因此在 %approot% 資料夾上的可用空間數量沒有任何意義。 如果您不確定應用程式是否要寫入 %approot% 磁碟機中，一律可以讓您的服務執行幾天，然後比較「之前」和「之後」的大小。 

Azure 不會將任何內容寫入 %approot% 磁碟機。 一旦從 .cspkg 建立 VHD 並將其掛接至 Azure VM 後，唯一可能會寫入此磁碟機的就是您的應用程式。 

日誌設定是不可設定的，因此您無法將它關閉。

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Azure 的基本 IPS/IDS 和 DDOS 提供的特性和功能是什麼？
Azure 在資料中心實體伺服器中擁有 IP/ID 可以防範潛威脅。 此外，客戶可以部署第三方安全性解決方案，例如 Web 應用程式防火牆、網路防火牆、反惡意程式碼軟體、入侵偵測、預防系統 (IDS/IPS) 等等。 如需詳細資訊，請參閱[保護您的資料和資產，以及符合全域安全性標準](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)。

Microsoft 會持續監視伺服器、網路和應用程式來偵測威脅。 Azure 的多元化威脅管理方法會使用入侵偵測、分散式阻斷服務 (DDoS) 攻擊防護、滲透測試、行為分析、異常偵測和機器學習，不斷地加強其防禦並降低風險。 適用於 Azure 保護的 Azure 雲端服務和虛擬機器的 Microsoft Antimalware。 您可以選擇額外部署第三方安全性解決方案，例如 Web 應用程式防火牆、網路防火牆、反惡意程式碼軟體、入侵偵測以及預防系統 (IDS/IPS) 等等。

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>為什麼 IIS 會停止寫入記錄目錄？
您已耗盡寫入記錄目錄的本機儲存體配額。 若要修正此問題，您可以執行下列三個項目的其中一項：
* 啟用 IIS 的診斷，並定期將診斷移至 blob 儲存體中。
* 從記錄目錄中手動移除記錄檔。
* 增加本機資源的配額限制。

如需詳細資訊，請參閱下列文件：
* [在 Azure 儲存體中儲存和檢視診斷資料](cloud-services-dotnet-diagnostics-storage.md)
* [IIS 記錄會停止在雲端服務中寫入](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>「Windows Azure Tools 擴充功能的加密憑證」用途為何？
每當有擴充功能新增至雲端服務時，就會自動建立這些憑證。 大多數情況下，這是 WAD 擴充功能或 RDP 擴充功能，但也可能是其他的，例如反惡意程式碼軟體或記錄收集器擴充功能。 這些憑證僅用於將擴充功能的私用組態進行加密和解密。 永遠不會檢查到期日，因此憑證是否過期並不重要。 

您可以忽略這些憑證。 如果您想要清除憑證，可以嘗試將它們全部刪除。 如果您嘗試刪除的憑證正在使用中，Azure 就會擲回錯誤。

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>如何能夠產生憑證簽署要求 (CSR)，而不 "RDP" 到執行個體中？
請參閱下列指導方針文件：

>[使用 Windows Azure 網站 (WAWS) 取得要使用的憑證](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

請注意，CSR 只是一個文字檔。 不必從最終會使用憑證的電腦建立它。 雖然是針對 App Service 寫入這份文件，但 CSR 建立為泛型，且也適用於雲端服務。

