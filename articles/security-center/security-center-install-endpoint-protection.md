<properties
   pageTitle="在 Azure 資訊安全中心安裝端點保護 | Microsoft Azure"
   description="本文件說明如何實作 Azure 資訊安全中心建議的「安裝端點保護」。"
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
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# 在 Azure 資訊安全中心安裝端點保護

如果還未啟用反惡意程式碼，Azure 資訊安全中心將會建議您在 Azure 虛擬機器 (VM) 中佈建反惡意程式碼程式。這項建議僅適用於 Windows VM。

> [AZURE.NOTE] 本文件將使用範例部署來介紹服務。這不是逐步指南。

## 實作建議

1. 在 [建議] 刀鋒視窗中，選取 [安裝端點保護]。![選取安裝端點保護][1]

2. [安裝端點保護] 刀鋒視窗隨即開啟，顯示未啟用反惡意程式碼的 VM 清單。從清單中選取您想要在上面安裝反惡意程式碼的 VM，然後按一下 [在 VM 上安裝]。![選取要在上面安裝反惡意程式碼的 VM][2]

3. [選取端點保護] 刀鋒視窗隨即開啟，讓您選取想要使用的反惡意程式碼解決方案。在此範例中，讓我們選取 [Microsoft Antimalware]。![選取端點保護][3]

4. 將會顯示反惡意程式碼解決方案的其他相關資訊。選取 [建立]。![建立反惡意程式碼解決方案][4]

5. 在 [加入擴充功能] 刀鋒視窗上輸入必要的組態設定，然後選取 [確定]。若要深入了解組態設定，請參閱[預設和自訂的反惡意程式碼組態](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration)。

[Microsoft Antimalware](../azure-security-antimalware.md) 現在已在選取的 VM 上使用。

## 另請參閱

本文說明了如何實作資訊安全中心建議的「安裝端點保護」。 若要深入了解如何在 Azure 中啟用反惡意程式碼程式，請參閱下列各項：

- [適用於雲端服務和虛擬機器的 Microsoft Antimalware](../azure-security-antimalware.md) -- 了解如何部署 Microsoft 反惡意程式碼。

如要深入了解資訊安全中心，請參閱下列主題：

- [設定 Azure 資訊安全中心的安全性原則](security-center-policies.md) -- 了解如何設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健全狀況。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
- [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健康狀態。
- [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) -- 尋找有關 Azure 安全性與相容性的部落格文章。

<!--Image references-->
[1]: ./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]: ./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]: ./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]: ./media/security-center-install-endpoint-protection/create-antimalware-solution.png

<!---HONumber=AcomDC_0817_2016-->