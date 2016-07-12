<properties 
	pageTitle="如何管理 Azure Redis 快取 | Microsoft Azure"
	description="了解如何執行管理工作，例如重新啟動以及排程 Azure Redis 快取更新"
	services="redis-cache"
	documentationCenter="na"
	authors="steved0x"
	manager="douge"
	editor="tysonn" />
<tags 
	ms.service="cache"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="06/28/2016"
	ms.author="sdanie" />

# 如何管理 Azure Redis 快取

本主題說明如何執行管理工作，例如重新啟動，並排程您的 Azure Redis 快取執行個體更新。

>[AZURE.IMPORTANT] 本文所述的設定和功能只適用於進階層快取。


## 管理設定

Azure Redis 快取**管理**設定可讓您針對進階快取執行下列管理工作。若要存取管理設定，從 Redis 快取刀鋒視窗按一下 [設定] 或 [所有設定]，然後捲動到 [設定] 刀鋒視窗中的 [管理] 區段。

![系統管理](./media/cache-administration/redis-cache-administration.png)

-	[重新啟動](#reboot)
-	[排程更新](#schedule-updates)

## 重新啟動

[重新啟動] 刀鋒視窗可讓您重新啟動快取的一或多個節點。這可讓您測試應用程式，以便在發生失敗時加以復原。

![重新啟動](./media/cache-administration/redis-cache-reboot.png)

如果您的進階快取已啟用叢集，您可以選取要重新啟動的快取分區。

![重新啟動](./media/cache-administration/redis-cache-reboot-cluster.png)

若要重新啟動快取的一或多個節點，選取所需的節點，然後按一下 [重新啟動]。如果您的進階快取已啟用叢集，選取要重新啟動的分區，然後按一下 [重新啟動]。稍候幾分鐘之後，選取的節點會重新啟動，並在幾分鐘之後重新上線。

對於用戶端應用程式的影響，會根據您重新啟動的節點而有所不同。

-	**主要** - 重新啟動主要節點時，Azure Redis 快取會容錯移轉至複本節點，並將其升級為主要節點。在此容錯移轉期間，可能會有一小段時間無法連接快取。
-	**從屬** - 重新啟動從屬節點時，通常不會對快取用戶端產生任何影響。
-	**主要和從屬** - 重新啟動這兩個快取節點時，在主要節點恢復上線之前，將會遺失快取中的所有資料且無法連接快取。如果您已設定[資料持續性](cache-how-to-premium-persistence.md)，則當快取恢復連線時，將會還原最近一次備份。
-	**已啟用叢集的進階快取節點** - 當您重新啟動已啟用叢集的進階快取節點時，其行為與您重新啟動非叢集快取的節點時相同。


>[AZURE.IMPORTANT] 重新啟動僅適用於進階層快取。

## 重新啟動常見問題集

-	[若要測試我的應用程式，我應該重新啟動哪一個節點？](#which-node-should-i-reboot-to-test-my-application)
-	[我可以重新啟動快取來清除用戶端連線嗎？](#can-i-reboot-the-cache-to-clear-client-connections)
-	[如果我重新啟動，將會遺失快取中的資料嗎？](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-	[我可以使用 PowerShell、CLI 或其他管理工具重新啟動我的快取嗎？](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-	[哪些定價層可以使用重新啟動功能？](#what-pricing-tiers-can-use-the-reboot-functionality)


### 若要測試我的應用程式，我應該重新啟動哪一個節點？

若要針對快取的主要節點失敗測試應用程式復原功能，重新啟動**主要**節點。若要針對次要節點失敗測試應用程式復原功能，重新啟動**從屬**節點。若要針對整體快取失敗測試應用程式復原功能，重新啟動**這兩個**節點。

### 我可以重新啟動快取來清除用戶端連線嗎？

是，如果您重新啟動快取，即會清除所有用戶端連線。若發生所有用戶端連線都已用盡的情況 (例如，因為發生邏輯錯誤或用戶端應用程式中發生錯誤)，則這非常實用。每個定價層對於各種不同大小都有不同的[用戶端連線限制](cache-configure.md#default-redis-server-configuration)，一旦觸達這些限制之後，就無法再接受任何用戶端連線。重新啟動快取提供一種方式來清除所有用戶端連線。

### 如果我重新啟動，將會遺失快取中的資料嗎？

如果您重新啟動**主要**和**從屬**節點，叢集中 (或者，如果您使用的是已啟用叢集的進階快取，則是在該分區中) 的所有資料都會遺失。如果您已設定[資料持續性](cache-how-to-premium-persistence.md)，則當快取恢復連線時，將會還原最近一次備份。

如果您只重新啟動這其中一個節點，通常不會遺失資料，但仍有可能發生。例如，如果重新啟動主要節點且快取寫入正在進行中，則來自快取寫入的資料即會遺失。

### 我可以使用 PowerShell、CLI 或其他管理工具重新啟動我的快取嗎？

目前無法使用，但即將推出此功能。

### 哪些定價層可以使用重新啟動功能？

重新啟動僅適用於進階定價層。

## 排程更新

[排程更新] 刀鋒視窗可讓您指定適用於快取的維護期間。若指定了維護期間，即會在此期間進行任何 Redis 伺服器更新。請注意，維護期間僅適用於 Redis 伺服器更新，不適用於任何 Azure 更新，或是在裝載快取的 VM 上更新作業系統。

![排程更新](./media/cache-administration/redis-schedule-updates.png)

若要指定維護期間，請檢查所需的天數，並指定每一天的維護期間開始小時，然後按一下 [確定]。請注意，維護期間時間是 UTC。

## 排程更新常見問題集

-	[如果我未使用排程更新功能，更新會在何時發生？](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-	[在排程維護期間，會進行何種類型的更新？](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-	[哪些定價層可以使用排程更新功能？](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### 如果我未使用排程更新功能，更新會在何時發生？

如果您未指定維護期間，隨時都可進行更新。

### 在排程維護期間，會進行何種類型的更新？

在排程維護期間，只會進行 Redis 伺服器更新。維護期間不適用於 Azure 更新或 VM 作業系統的更新。

### 哪些定價層可以使用排程更新功能？

排程更新僅適用於進階定價層。

<!---HONumber=AcomDC_0629_2016-->