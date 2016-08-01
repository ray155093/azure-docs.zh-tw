<properties
   pageTitle="解決 Azure 資訊安全中心的端點保護健全狀況警示 | Microsoft Azure"
   description="本文件說明如何實作 Azure 資訊安全中心建議的「解決端點保護健全狀況警示」。"
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
   ms.date="07/20/2016"
   ms.author="terrylan"/>

# 解決 Azure 資訊安全中心的端點保護健全狀況警示

Azure 資訊安全中心建議您先解決偵測到的端點保護健全狀況警示。資訊安全中心可讓您查看哪些虛擬機器 (VM) 發生端點保護失敗及失敗的數目。

> [AZURE.NOTE] 本文件將使用範例部署來介紹服務。這不是逐步指南。

## 實作建議

1. 在 [建議] 刀鋒視窗中，選取 [解決端點保護健全狀況警示]。![解決端點保護健全狀況警示][1]

2. 這樣會開啟 [端點保護失敗] 刀鋒視窗，其中列出失敗的 VM 和每部 VM 的失敗數目。從清單中選取 VM。![端點保護失敗][2]

3. 隨即開啟選取的 VM 的 [失敗清單] 刀鋒視窗，並顯示失敗的清單。從清單中選取要深入了解的失敗。便會開啟一個含有所選失敗相關資訊的刀鋒視窗。![失敗清單][3] ![失敗事件][4]

## 另請參閱

如要深入了解資訊安全中心，請參閱下列主題：

- [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) --了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健全狀況。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
- [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健康狀態。
- [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) -- 取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png

<!---HONumber=AcomDC_0720_2016-->