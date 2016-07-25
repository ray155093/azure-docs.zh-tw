<properties
   pageTitle="在 Azure 資訊安全中心提供安全性連絡人詳細資料 | Microsoft Azure"
   description="本文件說明如何在 Azure 資訊安全中心提供安全性連絡人詳細資料。"
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
   ms.date="07/11/2016"
   ms.author="terrylan"/>

# 在 Azure 資訊安全中心提供安全性連絡人詳細資料

Azure 資訊安全中心會建議您針對您的 Azure 訂用帳戶提供安全性連絡人詳細資料 (如果您還沒有這麼做)。如果 Microsoft 安全性回應中心 (MSRC) 發現您的客戶資料遭到非法或未經授權的對象存取，Microsoft 會使用此資訊連絡您。MSRC 執行 Azure 網路和基礎結構的選取安全性監視，並接收來自協力廠商的威脅情報和濫用客訴。

> [AZURE.NOTE] 本文件中的資訊適用於「Azure 資訊安全中心」的預覽版本。本文件將使用範例部署來介紹服務。這不是逐步指南。

## 實作建議

1. 在 [建議] 刀鋒視窗中，選取 [提供安全性連絡人詳細資料]。![提供安全性連絡人][1]

2. [提供安全性連絡人詳細資料] 刀鋒視窗隨即開啟。選取要提供連絡人資訊的 Azure 訂用帳戶。![提供安全性連絡人詳細資料][2]

3. 第二個 [提供安全性連絡人詳細資料] 刀鋒視窗隨即開啟。輸入安全性連絡人的電子郵件地址，若有多個則以逗號分隔。您可以輸入的電子郵件地址數沒有限制。輸入一個安全性連絡人國際電話號碼。

4. 選取 [確定] 以將安全性連絡人資訊套用至您的訂用帳戶。

## 後續步驟

如要深入了解資訊安全中心，請參閱下列主題：

- [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) --了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健全狀況。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
- [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健全狀況。
- [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) -- 取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]: ./media/security-center-provide-security-contacts/provide-contact-details.png

<!---HONumber=AcomDC_0713_2016-->