<properties
   pageTitle="在 Azure 資訊安全中心管理合作夥伴解決方案 | Microsoft Azure"
   description="本文件逐步引導您使用 Azure 資訊安全中心，讓您監視與您的 Azure 訂用帳戶整合之合作夥伴解決方案的健康狀態，一目了然。"
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

# 使用 Azure 資訊安全中心監視合作夥伴解決方案

本文件逐步引導您在 Azure 資訊安全中心監視合作夥伴解決方案的健康狀態。

> [AZURE.NOTE] 本文件將使用範例部署來介紹服務。這不是逐步指南。

## 監視合作夥伴解決方案

[資訊安全中心] 刀鋒視窗上的 [合作夥伴解決方案] 圖格可讓您監視與您的 Azure 訂用帳戶整合之合作夥伴解決方案的健全狀況，一目了然。![合作夥伴解決方案圖格][1]

[合作夥伴解決方案] 圖格會顯示合作夥伴解決方案的數目以及這些解決方案的狀態摘要。

合作夥伴解決方案的 [狀態] 可以是︰

- 受保護 (綠色) - 沒有任何健康問題。
- 狀況不良 (紅色) - 有需要立即注意的健康狀態問題。
- 停止報告 (橘色) - 解決方案已停止報告其健康狀態。
- 未知保護狀態 (橘色) - 此時解決方案的健康狀態因為將新資源加入至現有解決方案的程序失敗，而呈現未知狀態
- 未報告 (灰色) - 解決方案尚未報告任何狀態，如果解決方案才剛連接且仍在部署中，則可能未報告解決方案的狀態

如果沒有任何與您的訂用帳戶整合的解決方案，此圖格會表示沒有任何解決方案。選取 [合作夥伴解決方案] 圖格可讓您開啟 [建議] 刀鋒視窗，以部署合作夥伴安全性解決方案。![沒有合作夥伴解決方案][2]

若要檢視合作夥伴解決方案的健康狀態：

1. 選取 [合作夥伴解決方案] 圖格。隨即開啟一個刀鋒視窗，其中顯示已連接到資訊安全中心的合作夥伴解決方案清單。![合作夥伴解決方案][3]

2. 選取合作夥伴解決方案。在此範例中，選取 **F5 WAF2** 解決方案。隨即開啟一個刀鋒視窗，其中顯示合作夥伴解決方案的狀態和解決方案相關聯的資源。選取 [解決方案主控台] 以開啟此解決方案的合作夥伴管理體驗。![合作夥伴解決方案詳細資料][4]

3. 返回至 [F5-WAF2] 刀鋒視窗，然後選取 [連結應用程式]。[連結應用程式] 刀鋒視窗隨即開啟。您可以在這裡將資源連接到合作夥伴解決方案。![將資源連結至第三方解決方案][5]

## 另請參閱
在本文件中，已向您介紹「資訊安全中心」的 [合作夥伴解決方案] 圖格。如要深入了解資訊安全中心，請參閱下列主題：

- [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) — 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) — 了解建議如何協助保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) — 了解如何監視 Azure 資源的健全狀況。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) — 了解如何管理與回應安全性警示。
- [Azure 資訊安全中心常見問題集](security-center-faq.md) — 尋找有關使用服務的常見問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) — 取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

<!---HONumber=AcomDC_0810_2016-->