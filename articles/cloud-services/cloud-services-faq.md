<properties
	pageTitle="雲端服務常見問題集 | Microsoft Azure"
	description="關於雲端服務的常見問題集。"
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="adegeo"/>

# 雲端服務常見問題集
本文提供 Microsoft Azure 雲端服務的一些常見問題解答。您也可以造訪 [Azure 支援常見問題集](http://go.microsoft.com/fwlink/?LinkID=185083)，以取得一般的 Azure 價格和支援資訊。您也可以參閱[雲端服務 VM 大小頁面](cloud-services-sizes-specs.md)以取得大小資訊。

## 憑證

### 無法移除過期的憑證

Azure 會防止您移除使用中的憑證。您必須刪除使用憑證的部署，或使用不同憑證或更新的憑證來更新部署。

### 刪除過期的憑證

只要憑證不在使用中，您就可以使用 [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) PowerShell Cmdlet 來移除憑證。

### 我有名為 Windows Azure Service Management for Extensions 的已過期憑證

每當有擴充功能新增至雲端服務 (例如遠端桌面擴充功能)，就會建立這些憑證。這些憑證只會用於加密和解密擴充功能的私用組態。這些憑證是否過期並不重要。系統不會檢查到期日期。

### 我已刪除的憑證一直重複出現

這些憑證會一直遭到刪除很可能是因為您所使用的工具，例如 Visual Studio。每當您以使用某憑證的工具重新連線，該憑證就會再次上傳至 Azure。

### 我的憑證一直消失

當虛擬機器執行個體回收時，所有本機變更都會遺失。請在每次角色啟動時使用[啟動工作](cloud-services-startup-tasks.md)將憑證安裝到虛擬機器。

### 我應該將憑證安裝在何處？

**My** 具有私密金鑰的應用程式憑證 (*.pfx、*.p12)。

**CA** 所有中繼憑證都會放入此存放區 (原則和子 CA)。

**ROOT** 根 CA 存放區，因此主要的根 CA 憑證應該放在這裡。

## 疑難排解

### 無法在多 VIP 的雲端服務中保留 IP

首先，請確定您想要保留其 IP 的虛擬機器執行個體已開啟。其次，請確定您會將保留的 IP 同時用於預備與生產部署。請**勿**於部署正在升級時變更設定。

<!---HONumber=AcomDC_0824_2016-->