---
title: "服務對應與 System Center Operations Manager 的整合 | Microsoft Docs"
description: "服務對應是一個 Operations Management Suite (OMS) 解決方案，可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。  本文詳細說明如何使用服務對應在 SCOM 中自動建立分散式應用程式圖表。"
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: afba457134ba0fbc4ed35040828bb3745364ef78
ms.lasthandoff: 03/03/2017


---

# <a name="service-map-integration-with-system-center-operations-manager-integration"></a>服務對應與 System Center Operations Manager 的整合
  > [!NOTE]
  > 此功能為私人預覽版，因此不應用在生產環境系統上。
  > 
  
Operations Management Suite (OMS) 服務對應可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。 如果您想到您的伺服器，ADM 可讓您以互連系統 (提供重要服務) 的形式檢視它們。 不需要進行任何設定，只要安裝了代理程式，服務對應就會顯示橫跨任何 TCP 連接架構的伺服器、處理程序和連接埠之間的連接。  如需詳細資訊，請參閱[服務對應文件](operations-management-suite-service-map.md)。

透過「服務對應」與 System Center Operations Manager (SCOM) 之間的這項整合，您便可以在 SCOM 中，根據「服務對應」中的動態相依性對應，自動建立「分散式應用程式圖表」。

## <a name="prerequisites"></a>必要條件
1.    用於管理一組伺服器的 SCOM 管理群組。
2.    已啟用服務對應解決方案的 OMS 工作區。
3.    由 SCOM 管理並將資料傳送至服務對應的一組伺服器 (至少一個)。  支援 Windows 和 Linux 伺服器。
4.    可存取與 OMS 工作區相關聯之 Azure 訂用帳戶的服務主體。  [深入了解如何建立服務主體](#creating-a-service-principal)。

## <a name="installing-service-map-management-pack"></a>安裝服務對應管理組件
匯入 Microsoft.SystemCenter.ServiceMap 管理組件配套 (Microsoft.SystemCenter.ServiceMap.mpb)，以便啟用 SCOM 與服務對應之間的整合。  此配套包含下列管理組件︰
* Microsoft ServiceMap 應用程式檢視
* Microsoft System Center ServiceMap Internal
* Microsoft System Center ServiceMap Overrides
* Microsoft System Center ServiceMap

## <a name="configuring-the-service-map-integration"></a>設定服務對應整合
1. 安裝 ServiceMap 管理組件之後，[系統管理] 窗格中的 Operations Management Suite 之下會有新的節點 (服務對應)。
2. 按一下 [服務對應概觀] 窗格中的 [新增工作區]，以開啟設定精靈。

    ![SCOM 設定精靈](media/oms-service-map/scom-configuration.png)

3. 此精靈的第一個步驟是「連線設定」，您可以在此步驟中輸入「Azure 服務主體」的資訊。 請輸入租用戶名稱或識別碼、應用程式識別碼 (或是使用者名稱或 ClientID)，以及服務主體的密碼。  [深入了解如何建立服務主體](#creating-a-service-principal)。

    ![SCOM Config SPN](media/oms-service-map/scom-config-spn.png)

4. 下一個步驟是選取 Azure 訂用帳戶、Azure 資源群組 (包含 OMS 工作區) 和 OMS 工作區。

    ![SCOM Config 工作區](media/oms-service-map/scom-config-workspace.png)

5. 下一個步驟是設定服務對應伺服器群組，其中包含您要在 SCOM 與服務對應之間同步的伺服器。  按一下 [新增/移除伺服器...] 按鈕。 請注意，若要為了整合而建置伺服器的分散式應用程式圖表，伺服器必須︰1) 由 SCOM 管理，2) 由服務對應管理，以及 3) 列於服務對應伺服器群組中。

    ![SCOM Config 群組](media/oms-service-map/scom-config-group.png)

6. 選擇性 - 選取 [管理伺服器] 資源集區來與 OMS 通訊，然後按一下 [新增工作區]。

    ![SCOM Config 資源集區](media/oms-service-map/scom-config-pool.png)

7. 請注意，設定和註冊 OMS 工作區需要一點時間。 設定之後，SCOM 會從 OMS 起始第一次服務對應同步作業。

    ![SCOM Config 資源集區](media/oms-service-map/scom-config-success.png)

**注意：**預設同步間隔會設定為 60 分鐘。 使用者可以設定覆寫來變更同步間隔。 使用者也可以透過 [撰寫] 窗格 ([撰寫] 窗格 --> [群組]，然後搜尋「服務對應伺服器群組」)，以手動方式將伺服器新增至服務對應伺服器群組。 這些伺服器的伺服器對應將會與下一個同步作業同步處理 (根據設定的同步間隔)。

## <a name="monitoring-service-map"></a>監視服務對應
連線 OMS 工作區之後，新的服務對應資料夾會顯示於 SCOM 主控台的 [監視] 窗格中。
![SCOM 監視](media/oms-service-map/scom-monitoring.png)

服務對應資料夾有三個節點︰
### <a name="all-alerts"></a>所有警示︰
這可顯示有關 OMS 中 SCOM 與服務對應解決方案之間通訊的所有警示。

**注意︰**SCOM 中不會顯示任何 OMS 警示。
### <a name="servers"></a>伺服器︰
這會列出已設定為從服務對應同步處理的受監視伺服器。

![SCOM 監視伺服器](media/oms-service-map/scom-monitoring-servers.png)

### <a name="server-dependency-views"></a>伺服器相依性檢視︰
此檢視會列出從服務對應同步處理的所有伺服器。 使用者可以按一下任何伺服器，以檢視其分散式應用程式圖表。

![SCOM 分散式應用程式圖表](media/oms-service-map/scom-dad.png)

## <a name="editdelete-workspace"></a>編輯/刪除工作區︰
使用者可以透過 [服務對應概觀] 窗格 ([系統管理] 窗格 --> Operations Management Suite --> 服務對應) 編輯或刪除已設定的工作區。  請注意，您現在只能設定一個 OMS 工作區。

![SCOM 編輯工作區](media/oms-service-map/scom-edit-workspace.png)

## <a name="configuring-rules-and-overrides"></a>設定規則和覆寫︰
建立規則 **_Microsoft.SystemCenter.ServiceMap.Import.Rule_**，以便定期從服務對應擷取資訊。  使用者可以設定此規則的覆寫來變更同步時間。
[撰寫] 窗格 --> 規則 --> Microsoft.SystemCenter.ServiceMapImport.Rule

![SCOM 覆寫](media/oms-service-map/scom-overrides.png)
* **Enabled** – 啟用或停用自動更新 
* **IntervalSeconds** – 更新之間的時間。  預設值為 1 小時。 如果使用者想要更頻繁地同步處理伺服器對應，可以在這裡變更此值。
* **TimeoutSeconds** – 要求逾時之前的時間長度 
* **TimeWindowMinutes** – 資料查詢的時間範圍。  預設值為 60 分鐘的時間範圍。 最大值為 1 小時 (服務對應允許的最大值)。

## <a name="known-issueslimitations"></a>已知的問題/限制︰
在目前的設計中︰
1. 使用者可以透過 [撰寫] 窗格以手動方式將伺服器新增至「服務對應伺服器群組」，而這些伺服器的對應只會在下一個同步週期內從服務對應同步處理 (預設為&60; 分鐘。 使用者可以覆寫同步時間)。 
2. 使用者可以連線到單一 OMS 工作區。

## <a name="creating-a-service-principal"></a>建立服務主體
下列連結會帶您前往有關建立服務主體之三種不同方式的 Azure 正式文件。
* [使用 PowerShell 建立服務主體](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [使用 Azure CLI 建立服務主體](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [使用 Azure 入口網站建立服務主體](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>意見反應
您對「服務對應」或這份文件有任何意見反應要提供給我們嗎？  請瀏覽我們的 [User Voice 頁面](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)，您可以在此頁面提出功能建議或對現有的建議進行投票。

