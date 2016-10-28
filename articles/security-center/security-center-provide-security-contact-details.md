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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# 在 Azure 資訊安全中心提供安全性連絡人詳細資料

Azure 資訊安全中心會建議您針對您的 Azure 訂用帳戶提供安全性連絡人詳細資料 (如果您還沒有這麼做)。如果 Microsoft 安全性回應中心 (MSRC) 發現您的客戶資料遭到非法或未經授權的對象存取，Microsoft 會使用此資訊連絡您。MSRC 執行 Azure 網路和基礎結構的選取安全性監視，並接收來自協力廠商的威脅情報和濫用客訴。

在每天第一個警示發生時且僅針對高嚴重性警示，才會傳送電子郵件通知。電子郵件喜好設定只能針對訂用帳戶原則設定。在訂用帳戶內的資源群組會繼承這些設定。

> [AZURE.NOTE] 本文件將使用範例部署來介紹服務。這不是逐步指南。

## 實作建議

1. 在 [建議] 刀鋒視窗中，選取 [提供安全性連絡人詳細資料]。![提供安全性連絡人][1]

2. [提供安全性連絡人詳細資料] 刀鋒視窗隨即開啟。選取要提供連絡人資訊的 Azure 訂用帳戶。![提供安全性連絡人詳細資料][2]

3. 第二個 [提供安全性連絡人詳細資料] 刀鋒視窗隨即開啟。

  - 輸入安全性連絡人的電子郵件地址，若有多個則以逗號分隔。您可以輸入的電子郵件地址數沒有限制。
  - 輸入一個安全性連絡人國際電話號碼。
  - 若要接收有關高嚴重性警示的電子郵件，請開啟 [傳送給我有關警示的電子郵件] 選項。
  - 未來，您會有將電子郵件通知傳送給訂用帳戶擁有者的選項。此選項目前無法使用。
  - 選取 [確定] 將安全性連絡人資訊套用至您的訂用帳戶。

## 另請參閱

如要深入了解資訊安全中心，請參閱下列主題：

- [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) --了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健康情況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健康情況。
- [管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理及回應安全性警示。
- [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健康情況。
- [Azure 資訊安全中心常見問題集 (FAQ)](security-center-faq.md) -- 尋找有關使用服務的常見問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) -- 取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]: ./media/security-center-provide-security-contacts/provide-contact-details.png

<!---HONumber=AcomDC_0727_2016-->