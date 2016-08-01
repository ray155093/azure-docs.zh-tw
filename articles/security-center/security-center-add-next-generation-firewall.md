<properties
   pageTitle="在 Azure 資訊安全中心新增新一代防火牆 | Microsoft Azure"
   description="本文件說明如何實作 Azure 資訊安全中心建議的「新增新一代防火牆」。"
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
   ms.date="07/15/2016"
   ms.author="terrylan"/>

# 在 Azure 資訊安全中心新增新一代防火牆

Azure 資訊安全中心可能會建議您新增由 Microsoft 合作夥伴提供的新一代防火牆 (NGFW)，以提升您的安全防護。本文件逐步解說如何進行這項操作的範例。

> [AZURE.NOTE] 本文件中的資訊適用於「Azure 資訊安全中心」的預覽版本。本文件將使用範例部署來介紹服務。這不是逐步指南。

## 實作建議

1. 在 [建議] 刀鋒視窗中，選取 [新增新一代防火牆]。![新增新一代防火牆][1]

2. 在 [新增新一代防火牆] 刀鋒視窗中，選取一個端點。![選取端點][2]

3. 第二個 [新增新一代防火牆] 刀鋒視窗隨即開啟。您可以選擇使用現有的解決方案 (如果有的話)，或是建立新的解決方案。此範例中沒有任何可用的現有解決方案，因此我們將建立一個新的 NGFW。![新建新一代防火牆][3]

4. 若要建立新的 NGFW，請從整合式合作夥伴的清單中選取一個解決方案。在此範例中，我們將會選取 **檢查點**。![選取新一代防火牆解決方案][4]

5. [檢查點] 刀鋒視窗隨即開啟，為您提供合作夥伴解決方案的相關資訊。選取資訊刀鋒視窗中的 [建立]。![防火牆資訊刀鋒視窗][5]

6. [建立虛擬機器] 刀鋒視窗就會開啟。您可在此輸入啟動將執行 NGFW 的虛擬機器所需的資訊。依照下列步驟執行，並提供所需的 NGFW 資訊。選取 [確定] 以套用。![建立虛擬機器以執行 NGFW][6]

## 後續步驟

本文件說明如何實作資訊安全中心建議的「新增新一代防火牆」。 若要了解 NGFW 與檢查點合作夥伴解決方案的詳細資訊，請參閱：

- [新一代防火牆](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [檢查點 vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

如要深入了解資訊安全中心，請參閱下列主題：

- [設定 Azure 資訊安全中心的安全性原則](security-center-policies.md) -- 了解如何設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健全狀況。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
- [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健康狀態。
- [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) -- 尋找有關 Azure 安全性與相容性的部落格文章。

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png

<!---HONumber=AcomDC_0720_2016-->