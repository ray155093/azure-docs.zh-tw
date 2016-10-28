<properties
   pageTitle="保護 Azure 資訊安全中心內的 Azure SQL 服務 | Microsoft Azure"
   description="本文件說明可協助您保護 Azure SQL 服務及遵守安全性原則的 Azure 資訊安全中心建議。"
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

# 保護 Azure 資訊安全中心內的 Azure SQL 服務

「Azure 資訊安全中心」會分析 Azure 資源的安全性狀態。當資訊安全中心發現潛在的安全性弱點時，它會建立可引導您完成所需控制之設定程序的建議。這些建議適用於下列 Azure 資源類型︰虛擬機器 (VM)、網路、SQL 和應用程式。

本文說明適用於 Azure SQL 服務的建議。Azure SQL 服務建議圍繞在為 Azure SQL 伺服器和資料庫啟用稽核以及為 SQL 資料庫啟用加密。請使用下表做為參考，以協助您了解可用的 SQL 服務建議，以及如果套用建議，每一個建議將產生的作用。

## 可用的 SQL 服務建議

|建議|說明|
|-----|-----|
|[啟用伺服器 SQL 稽核](security-center-enable-auditing-on-sql-servers.md)|建議您針對 Azure SQL 伺服器開啟稽核 (僅適用於 Azure SQL 服務，不包括在您虛擬機器上執行的 SQL)。|
|[啟用資料庫 SQL 稽核](security-center-enable-auditing-on-sql-databases.md)|建議您針對 Azure SQL 資料庫開啟稽核 (僅適用於 Azure SQL 服務，不包括在您虛擬機器上執行的 SQL)。|
|[在 SQL 資料庫上啟用透明資料加密](security-center-enable-transparent-data-encryption.md)|建議您針對 SQL 資料庫啟用加密 (僅適用於 Azure SQL 服務)。|

## 另請參閱

若要深入了解適用於其他 Azure 資源類型的建議，請參閱下列文章︰

- [保護 Azure 資訊安全中心內的虛擬機器](security-center-virtual-machine-recommendations.md)
- [保護 Azure 資訊安全中心內的應用程式](security-center-application-recommendations.md)
- [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)

如要深入了解資訊安全中心，請參閱下列主題：

- [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) -- 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
- [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。

<!---HONumber=AcomDC_0810_2016------>