<properties
	pageTitle="從方案庫加入 Log Analytics 方案 | Microsoft Azure"
	description="Log Analytics 方案是邏輯、視覺效果和資料擷取規則的集合，可提供針對特定問題領域進行計量的樞紐分析。"
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
	ms.date="04/28/2016"
	ms.author="banders"/>

# 從方案庫加入 Log Analytics 方案

Log Analytics 方案是**邏輯**、**視覺效果**和**資料擷取規則**的集合，可提供針對特定問題領域進行計量的樞紐分析。本文列出支援 Log Analytics 的方案，並告訴您如何使用方案庫加入和移除這些方案。

方案可允許更深入的探討，以：
- 協助調查並更快解決操作問題、
- 收集並相互關聯各種類型的機器資料、
- 協助您主動出擊，例如容量規劃、修補狀態報告和安全性稽核。


>[AZURE.NOTE] OMS 包含基底「記錄搜尋」功能，因此您不需要安裝方案即可啟用。不過，您可以從 [方案庫] 頁面加入方案以取得其他功能。

加入方案後，系統會從基礎結構中的伺服器收集資料，然後再傳送到 OMS 服務。OMS 服務的處理時間少則幾分鐘，多則達數小時之久。待服務處理完資料後，您可以在 OMS 中予以檢視。

當您不再需要解決方案時，您可以輕易地將它移除。移除方案後，系統不會將其資料傳送到 OMS，因此可減少每日配額 (如果有的話) 所使用的資料量。


## Microsoft Monitoring Agent 支援的解決方案

如果伺服器是透過 Microsoft Monitoring Agent 連接至 OMS，則可使用目前提供的大多數方案，包括：

- 系統更新
- 反惡意程式碼
- 變更追蹤
- SQL 評估
- Active Directory 評估
- 警示管理 (不含 SCOM 警示)

不過，Microsoft Monitoring Agent 並「不支援」下列方案，且需要 System Center Operations Manager (SCOM) 代理程式。

- 產能管理
- 警示管理 (包括 SCOM 警示)
- 組態評估

請參閱[將 Operations Manager 連接至 Log Analytics](log-analytics-om-agents.md)，以取得將 SCOM 代理程式連接到 Log Analytics 的資訊。

### 使用 [解決方案資源庫] 新增解決方案

1. 在 OMS 的 [概觀] 頁面中，按一下 [方案庫] 圖格。![方案庫](./media/log-analytics-add-solutions/sol-gallery.png)
2. 在 [OMS 方案庫] 頁面中，您可以了解每個可用的方案。按一下要加入 OMS 之方案的名稱。
3. 在所選解決方案的頁面中，該解決方案的詳細資料會顯示於此。按一下 [新增]。
4. 加入方案之後，新建立的圖格會出現在 OMS 的 [概觀] 頁面中；待 OMS 服務處理資料後，您便可以開始使用該方案。

## 若要設定方案
1. 有些方案需要進行設定。例如，您必須先設定自動化、Azure Site Recovery 以及備份，才能使用它們。
2. 若是這類方案，請按一下 [概觀] 頁面上該方案的圖格。![設定方案](./media/log-analytics-add-solutions/configure-additional.png)
3. 然後，使用必要資訊設定方案，再按一下 [儲存]。![設定方案](./media/log-analytics-add-solutions/configure.png)

### 使用 [解決方案資源庫] 移除解決方案

1. 在 OMS 的 [概觀] 頁面中，按一下 [設定] 圖格。
2. 在 [設定] 頁面中，於要移除之方案的 [方案] 索引標籤下方按一下 [移除]。
3. 在確認對話方塊中，按一下 [是] 以移除方案。

## OMS 功能和方案的資料收集詳細資料

下表顯示 OMS 功能和方案的資料收集方法和其他資料收集方式的詳細資料。

|資料類型| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
|---|---|---|---|---|---|---|---|
|AD 評估|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 天|
|AD 複寫狀態|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 天|
|警示 (Nagios)|Linux|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|與抵達同時|
|警示 (Zabbix)|Linux|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 分鐘|
|警示 (Operations Manager)|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 分鐘|
|反惡意程式碼|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 每小時|
|產能管理|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 每小時|
|變更追蹤|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 每小時|
|變更追蹤|Linux|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|每小時|
|組態評估 (舊版建議程式)|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 一天兩次|
|ETW|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分鐘|
|IIS 記錄檔|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分鐘|
|網路安全性群組|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分鐘|
|Office 365|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|與通知同時|
|效能計數器|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|依排程，最少 10 秒|
|效能計數器|Linux|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|依排程，最少 10 秒|
|Service Fabric|Windows|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分鐘|
|SQL 評估|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 天|
|Surface Hub|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|與抵達同時|
|Syslog|Linux|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|從 Azure 儲存體 ：10 分鐘；從代理程式：與抵達同時|
|系統更新|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 安裝更新之後 15 分鐘，至少一天 2 次|
|Windows 安全性事件記錄檔|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)| 若為 Azure 儲存體：10 分鐘；代理程式：與抵達同時|
|Windows 防火牆記錄檔|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)| 與抵達同時|
|Windows 事件記錄檔|Windows|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)| 若為 Azure 儲存體：1 分鐘；代理程式：與抵達同時|
|連線資料|Windows (2012 R2/8.1 或更新版本)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![是](./media/log-analytics-add-solutions/oms-bullet-green.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)|![否](./media/log-analytics-add-solutions/oms-bullet-red.png)| 每隔 1 分鐘|


## 後續步驟

- [搜尋記錄](log-analytics-log-searches.md)以檢視方案所蒐集的詳細資訊。

<!---HONumber=AcomDC_0504_2016-->