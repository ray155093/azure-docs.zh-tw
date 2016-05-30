<properties
	pageTitle="Log Analytics 中的系統更新評估方案 | Microsoft Azure"
	description="您可以使用 Log Analytics 中的系統更新方案，協助您將遺失的更新套用到基礎結構中的伺服器。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/06/2016"
	ms.author="banders"/>

# Log Analytics 中的系統更新評估方案


您可以使用 Log Analytics 中的系統更新方案，協助您將遺失的更新套用到基礎結構中的伺服器。安裝此方案之後，您可以在 OMS 的 [概觀] 頁面上使用 [系統更新評估] 圖格，檢視受監視伺服器缺少的更新。

如果找到遺失的更新，[更新] 儀表板上會顯示詳細資料。您可以使用 [**更新**] 儀表板處理遺失的更新及開發計劃，將它們套用至所需要的伺服器。

## 安裝和設定方案
請使用下列資訊來安裝和設定方案。

- 使用[從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)所述的程序，將系統更新評估方案加入您的 OMS 工作區。不需要進一步的組態。

## 「系統更新」資料收集詳細資訊

下表顯示系統更新評估的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
|---|---|---|---|---|---|---|
|Windows|![是](./media/log-analytics-system-update/oms-bullet-green.png)|![是](./media/log-analytics-system-update/oms-bullet-green.png)|![否](./media/log-analytics-system-update/oms-bullet-red.png)| ![否](./media/log-analytics-system-update/oms-bullet-red.png)|![是](./media/log-analytics-system-update/oms-bullet-green.png)| 安裝更新之後每天至少 2 次且持續 15 分鐘|


### 使用更新

1. 在 [**概觀**] 頁面上，按一下 [**系統更新評估**] 磚。![概觀頁面的影像](./media/log-analytics-system-update/oms-updates01.png)
2. 在 [**更新**] 儀表板上，檢視更新類別。![更新頁面的影像](./media/log-analytics-system-update/oms-updates02.png)
3. 捲動到頁面的右邊，以檢視 [**遺失的更新類型**] 刀鋒視窗，然後按一下 [**安全性更新**]。![更新頁面的影像](./media/log-analytics-system-update/oms-updates03.png)
4. 在 [搜尋] 頁面上，會顯示在基礎結構的伺服器中遺失的安全性更新清單。按一下知識庫文章識別碼 (KBID) 以檢視遺失更新的詳細資訊。在此範例中，*KBID 3032323*。![更新頁面的影像](./media/log-analytics-system-update/oms-updates04.png)
5. 網頁瀏覽器會開啟描述更新的知識庫文章。![image of the knowledge base article](./media/log-analytics-system-update/oms-updates05.png)
6. 使用您已經找到的資訊，您可以建立計畫以套用遺失的更新。

## 後續步驟

- [搜尋記錄](log-analytics-log-searches.md)以檢視詳細的系統更新資料。

<!---HONumber=AcomDC_0518_2016-->