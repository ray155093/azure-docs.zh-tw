<properties
   pageTitle="保護 Azure 資訊安全中心內的虛擬機器 | Microsoft Azure"
   description="本文件說明可協助您保護虛擬機器及遵守安全性原則的 Azure 資訊安全中心建議。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# 保護 Azure 資訊安全中心內的虛擬機器

「Azure 資訊安全中心」會分析 Azure 資源的安全性狀態。當資訊安全中心發現潛在的安全性弱點時，它會建立可引導您完成所需控制之設定程序的建議。這些建議適用於下列 Azure 資源類型︰虛擬機器 (VM)、網路、SQL 和應用程式。

本文說明適用於 VM 的建議。VM 建議圍繞在資料收集、套用系統更新、佈建反惡意程式碼、加密 VM 磁碟等等。請使用下表做為參考，以協助您了解可用的 VM 建議，以及如果套用建議，每一個建議將產生的作用。

## 可用的 VM 建議

|建議|說明|
|-----|-----|
|[啟用訂用帳戶的資料收集](security-center-enable-data-collection.md)|建議您為每個訂用帳戶和訂用帳戶中的所有虛擬機器 (VM) 開啟安全性原則中的 [資料收集]。|
|[修復 OS 弱點](security-center-remediate-os-vulnerabilities.md)|建議您讓作業系統組態符合建議的設定規則，例如不允許儲存密碼。|
|[套用系統更新](security-center-apply-system-updates.md)|建議您將遺漏的系統安全性與重大更新部署到 VM。|
|[在系統更新之後重新開機](security-center-apply-system-updates.md#reboot-after-system-updates)|建議您重新啟動 VM 以完成套用系統更新的程序。|
|[安裝端點保護](security-center-install-endpoint-protection.md)|建議您將反惡意程式碼程式佈建到 VM (僅適用於 Windows VM)。|
|[解決端點保護健全狀況警示](security-center-resolve-endpoint-protection-health-alerts.md)|建議您先解決端點保護失敗。|
|[啟用 VM 代理程式](security-center-enable-vm-agent.md)|可讓您查看哪些 VM 需要「VM 代理程式」。為了佈建修補程式掃描、基準掃描及反惡意程式碼程式，必須在 VM 上安裝「VM 代理程式」。預設會為從 Azure Marketplace 部署的 VM 安裝「VM 代理程式」。如需如何安裝 VM 代理程式的相關資訊，請參閱 [VM 代理程式和擴充功能 – 第 2 部分](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)。|
| [套用磁碟加密](security-center-apply-disk-encryption.md) |建議您使用 Azure 磁碟加密來加密您的 VM 磁碟 (Windows 和 Linux VM)。建議您的 VM 上的作業系統和資料磁碟區都進行加密。|
| [更新作業系統版本](security-center-update-os-version.md) | 建議您將雲端服務的作業系統 (OS) 版本更新為作業系統系列可用的最新版本。若要深入了解雲端服務，請參閱[雲端服務概觀](../cloud-services/cloud-services-choose-me.md)。 |

## 另請參閱

若要深入了解適用於其他 Azure 資源類型的建議，請參閱下列文章︰

- [保護 Azure 資訊安全中心內的應用程式](security-center-application-recommendations.md)
- [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)
- [保護 Azure 資訊安全中心內的 Azure SQL 服務](security-center-sql-service-recommendations.md)

如要深入了解資訊安全中心，請參閱下列主題：

- [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) -- 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
- [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。

<!---HONumber=AcomDC_0810_2016-->