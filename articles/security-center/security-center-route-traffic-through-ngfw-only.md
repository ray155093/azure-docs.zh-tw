<properties
   pageTitle="在 Azure 資訊安全中心設定只透過新一代防火牆路由傳送流向 | Microsoft Azure"
   description="本文件說明如何實作 Azure 資訊安全中心建議的「僅透過 NGFW 路由傳送流量」。"
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

# 在 Azure 資訊安全中心設定只透過新一代防火牆路由傳送流向

當您部署新一代防火牆 (NGFW) 後，Azure 資訊安全中心會自動偵測。如果您有網際網路面向端點，Azure 資訊安全中心會建議您透過 NGFW 設定網路安全性群組規則，強制將輸入流量傳送到虛擬機器 (VM)。

> [AZURE.NOTE] 本文件將使用範例部署來介紹服務。這不是逐步指南。

## 實作建議

1. 在 [建議] 刀鋒視窗中，選取 [僅透過 NGFW 路由傳送流量]。![僅透過 NGFW 路由傳送流量][1]

2. 這樣會開啟 [僅透過 NGFW 路由傳送流量] 刀鋒視窗，其中列出您可以路由傳送流量的 VM。從清單中選取 VM。![選取 VM][2]

3. 所選 VM 的刀鋒視窗隨即開啟，其中顯示相關的輸入規則。說明提供後續可能步驟的詳細資訊。![設定規則以限制存取][3]

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
[1]: ./media/security-center-route-traffic-through-ngfw/route-traffic-through-ngfw.png
[2]: ./media/security-center-route-traffic-through-ngfw/select-vm.png
[3]: ./media/security-center-route-traffic-through-ngfw/configure-rules-to-limit-access.png

<!---HONumber=AcomDC_0720_2016-->