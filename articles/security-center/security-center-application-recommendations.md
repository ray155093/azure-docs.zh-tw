<properties
   pageTitle="保護 Azure 資訊安全中心內的應用程式 | Microsoft Azure"
   description="本文件說明可協助您保護 Azure 應用程式及遵守安全性原則的 Azure 資訊安全中心建議。"
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

# 保護 Azure 資訊安全中心內的應用程式

「Azure 資訊安全中心」會分析 Azure 資源的安全性狀態。當資訊安全中心發現潛在的安全性弱點時，它會建立可引導您完成所需控制之設定程序的建議。這些建議適用於下列 Azure 資源類型︰虛擬機器 (VM)、網路、SQL 和應用程式。

本文說明適用於應用程式的建議。應用程式建議圍繞在 Web 應用程式防火牆的部署。請使用下表做為參考，以協助您了解可用的應用程式建議，以及如果套用建議，每一個建議將產生的作用。

## 可用的應用程式建議

|建議|說明|
|-----|-----|
|[新增 Web 應用程式防火牆](security-center-add-web-application-firewall.md)|建議您為 Web 端點部署「Web 應用程式防火牆」(WAF)。您可以將這些應用程式加入現有的 WAF 部署，以保護資訊安全中心的多個 Web 應用程式。WAF 應用裝置 (使用 Resource Manager 部署模型建立) 需要部署至不同的虛擬網路。WAF 應用裝置 (使用傳統部署模型所建立) 受限於只能使用網路安全性群組。這項支援在未來將會延伸至 WAF 應用裝置 (傳統) 的完全自訂部署。|
|[完成應用程式保護](security-center-add-web-application-firewall.md#finalize-application-protection)|若要完成 WAF 組態，必須將流量重新路由至 WAF 設備。遵循這項建議會完成必要的設定變更。|

## 另請參閱

若要深入了解適用於其他 Azure 資源類型的建議，請參閱下列文章︰

- [保護 Azure 資訊安全中心內的虛擬機器](security-center-virtual-machine-recommendations.md)
- [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)
- [保護 Azure 資訊安全中心內的 Azure SQL 服務](security-center-sql-service-recommendations.md)

如要深入了解資訊安全中心，請參閱下列主題：

- [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) -- 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
- [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。

<!---HONumber=AcomDC_0810_2016---->