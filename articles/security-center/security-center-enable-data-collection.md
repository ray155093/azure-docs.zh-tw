<properties
   pageTitle="在 Azure 資訊安全中心啟用資料收集 | Microsoft Azure"
   description=" 了解如何在 Azure 資訊安全中心啟用資料收集。"
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

# 在 Azure 資訊安全中心啟用資料收集

為了協助客戶防範、偵測和回應威脅，Azure 資訊安全中心會收集和處理 Azure 虛擬機器的相關資料，包含組態資訊、中繼資料、事件記錄檔等等。當您第一次存取資訊安全中心時，訂用帳戶中的所有虛擬機器都會啟用資料收集。建議啟用資料收集，但您可以在資訊安全中心原則中關閉資料收集來選擇退出 (請參閱[停用資料收集](#disabling-data-collection))。如果您關閉資料收集，資訊安全中心會建議您在該訂用帳戶的安全性原則中開啟資料收集。

> [AZURE.NOTE] 本文件將使用範例部署來介紹服務。這不是逐步指南。

## 實作建議

1. 選取 [資訊安全中心] 刀鋒視窗上的 [建議] 圖格。這會開啟 [建議] 刀鋒視窗。![[資訊安全中心] 刀鋒視窗][1]

2. 在 [建議] 刀鋒視窗中，選取 [為訂用帳戶啟用資料收集]。這會開啟 [開啟資料收集] 刀鋒視窗。![建議刀鋒視窗][2]

3. 在 [開啟資料收集] 刀鋒視窗中，選取您的訂用帳戶。該訂用帳戶的 [安全性原則] 刀鋒視窗隨即開啟。

4. 在 [安全性原則] 刀鋒視窗中，選取 [資料收集] 底下的 [開啟] 以自動收集記錄檔。開啟資料收集也會將監視擴充功能佈建在訂用帳戶的所有目前支援和新支援的 VM 上。![安全性原則刀鋒視窗][3]

5.	選取 [**儲存**]。

6.	選取 [選擇每個區域的儲存體帳戶]。針對每個有虛擬機器在其中執行的區域，您需選擇儲存體帳戶，以儲存從這些虛擬機器收集到的資料。如不為每個區域選擇儲存體帳戶，系統會自動為您建立。在此範例中，我們會選擇 **newstoracct**。您可以在稍後回到訂用帳戶的安全性原則並選擇不同的儲存體帳戶，以變更儲存體帳戶。![選擇儲存體帳戶][4]

7.	選取 [確定]。

> [AZURE.NOTE] 建議您開啟資料收集，並先在訂用帳戶層級選擇儲存體帳戶。安全性原則可以在 Azure 訂用帳戶層級和資源群組層級進行設定，但資料收集和儲存體帳戶只可在訂用帳戶層級進行設定。

## 啟用資料收集之後

資料收集是透過「Azure 監視代理程式」與「Azure 安全性監視」擴充功能來啟用的。「Azure 安全性監視」擴充功能會掃描各種安全性相關組態，並將其傳送至 [Windows 事件追蹤](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 追蹤。此外，作業系統會建立事件記錄項目。「Azure 監視代理程式」會讀取事件記錄項目和 ETW 追蹤，並將它們複製到您的儲存體帳戶進行分析。監視代理程式也會將損毀傾印檔案複製到儲存體帳戶。這是您在安全性原則中設定的儲存體帳戶。

## 停用資料收集

您可以隨時停用資料收集，這將會移除資訊安全中心先前所安裝的任何監視代理程式。您必須選取要關閉資料收集的訂用帳戶。

> [AZURE.NOTE] 安全性原則可以在 Azure 訂用帳戶層級和資源群組層級進行設定，但您必須選取訂用帳戶以關閉資料收集。

1.	返回 [資訊安全中心] 刀鋒視窗，然後選取 [原則] 圖格。這會開啟 [安全性原則 – 定義每個訂用帳戶或資源群組的原則] 刀鋒視窗。![選取原則圖格][5]

2.	在 [安全性原則 – 定義每個訂用帳戶或資源群組的原則] 刀鋒視窗中，選取要停用資料收集的訂用帳戶。![選取要停用資料收集的訂用帳戶][6]

3.	該訂用帳戶的 [安全性原則] 刀鋒視窗隨即開啟。選取 [資料收集] 底下的 [關閉]。

4.	在最上方的功能區中，選取 [儲存]。

5.	選取最上方功能區中的 [刪除代理程式]，以從現有的虛擬機器移除代理程式。

## 另請參閱

本文說明了如何實作資訊安全中心建議的「啟用資料收集」。 如要深入了解資訊安全中心，請參閱下列主題：

- [在 Azure 資訊安全中心內設定安全性原則](security-center-policies.md) -- 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助您保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)--了解如何管理與回應安全性警示。
- [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健全狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)--尋找使用服務的常見問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)--取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/security-center-blade.png
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

<!---HONumber=AcomDC_0727_2016-->