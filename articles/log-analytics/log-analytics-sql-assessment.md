<properties
	pageTitle="在 Log Analytics 中使用 SQL 評估方案進行環境最佳化 | Microsoft Azure"
	description="您可以使用 SQL 評估方案定期評估伺服器環境的風險和健全狀況。"
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

# 在 Log Analytics 中使用 SQL 評估方案進行環境最佳化


您可以使用 SQL 評估方案定期評估伺服器環境的風險和健全狀況。本文將協助您安裝方案，讓您可以針對潛在問題採取修正動作。

此方案能針對已部署的伺服器基礎結構提供依照優先順序排列的具體建議清單。建議分為六個焦點領域，它們可以幫助您快速了解風險並採取修正動作。

智慧套件提供的建議乃源自 Microsoft 工程師數千次客戶拜訪所得到的知識和經驗。每項建議均提供問題的影響層面，以及如何實作建議變更等相關指引。

您可以選擇對組織而言最重要的焦點區域，同時追蹤經營無風險且健康狀態良好之環境的進度。

加入方案且評估完成之後，系統會將焦點區域的摘要資訊顯示在環境之基礎結構的 [SQL 評估] 儀表板中。下列章節說明如何使用 [SQL 評估] 儀表板上的資訊，您可以在這裡檢視 SQL 伺服器基礎結構的建議動作並予以實施。

![SQL 評估磚的影像](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![SQL 評估儀表板的影像](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## 安裝和設定方案
SQL 評估適用於 Standard、Developer 和 Enterprise 版本之 SQL Server 目前支援的所有版本。

請使用下列資訊來安裝和設定方案。

- SQL 評估方案需要具有 OMS 代理程式的電腦上都安裝 .NET Framework 4。
- 搭配使用 Operations Manager 代理程式和 SQL 評估時，您必須使用 Operations Manager 執行身分帳戶。如需詳細資訊，請參閱底下的 [OMS 的 Operations Manager 執行身分帳戶](#operations-manager-run-as-accounts-for-oms)。

    >[AZURE.NOTE] MMA 代理程式不支援 Operations Manager 執行身分帳戶。

- 使用[從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)中的程序，將 SQL 評估方案加入您的 OMS 工作區中。不需要進一步的組態。

>[AZURE.NOTE] 您加入方案之後，AdvisorAssessment.exe 檔案會以代理程式加入伺服器中。組態資料會先讀取後再傳送至雲端中的 OMS 服務，以便進行處理。會將邏輯套用至接收的資料，且雲端服務會記錄資料。

## SQL 評估資料收集詳細資料

下表顯示代理程式的資料收集方法、是否需要 Operations Manager (SCOM)，以及如何代理程式收集資料的頻率。

| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
|---|---|---|---|---|---|---|
|Windows|![是](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![是](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![否](./media/log-analytics-sql-assessment/oms-bullet-red.png)|	![否](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![是](./media/log-analytics-sql-assessment/oms-bullet-green.png)|	7 天|

## OMS 的 Operations Manager 執行身分帳戶

OMS 中的 Log Analytics 會使用 Operations Manager 代理程式及管理群組，收集資料並將資料傳送給 OMS 服務。OMS 會建立工作負載的管理套件以提供加值服務。每個工作負載都需要具有特定的工作負載權限，才能在不同的安全性內容中執行管理套件，例如網域帳戶。您需要藉由設定 Operations Manager 執行身分帳戶來提供認證資訊。

請使用下列資訊來設定 SQL 評估的 Operations Manager 執行身分帳戶。

### 設定 SQL 評估的執行身分帳戶

 如果您已經在使用 SQL Server 管理套件，您應該使用該執行身分帳戶。

#### 在 Operations 主控台中設定 SQL 執行身分帳戶

1. 在 Operations Manager 中開啟 Operations 主控台，然後按一下 [管理]。

2. 在 [執行身分組態] 下，按一下 [設定檔]，並開啟 [OMS SQL Assessment Run As Profile] (OMS SQL 評估執行身分設定檔)。

3. 在 [執行身分帳戶] 頁面上，按一下 [新增]。

4. 選取包含 SQL Server 所需認證的 Windows 執行身分帳戶，或按一下 [新增] 建立一個。
	>[AZURE.NOTE] 執行身分帳戶類型必須是 Windows。執行身分帳戶也必須屬於裝載 SQL Server 執行個體的所有 Windows 伺服器上的本機系統管理員群組。

5. 按一下 [儲存]。

6. 修改，然後在每個 SQL Server 執行個體上執行下列 T-SQL 範例，授與執行身分帳戶所需的最小權限授以執行 SQL 評估。不過，如果執行身分帳戶已是 SQL Server 執行個體上 sysadmin 伺服器角色的一部分，您就不需要這樣做。

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## Understanding how recommendations are prioritized

Every recommendation made is given a weighting value that identifies the relative importance of the recommendation. Only the ten most important recommendations are shown.

### How weights are calculated

Weightings are aggregate values based on three key factors:

- The *probability* that an issue identified will cause problems. A higher probability equates to a larger overall score for the recommendation.

- The *impact* of the issue on your organization if it does cause a problem. A higher impact equates to a larger overall score for the recommendation.

- The *effort* required to implement the recommendation. A higher effort equates to a smaller overall score for the recommendation.

The weighting for each recommendation is expressed as a percentage of the total score available for each focus area. For example, if a recommendation in the Security and Compliance focus area has a score of 5%, implementing that recommendation will increase your overall Security and Compliance score by 5%.

### Focus areas

**Security and Compliance** - This focus area shows recommendations for potential security threats and breaches, corporate policies, and technical, legal and regulatory compliance requirements.

**Availability and Business Continuity** - This focus area shows recommendations for service availability, resiliency of your infrastructure, and business protection.

**Performance and Scalability** - This focus area shows recommendations to help your organization's IT infrastructure grow, ensure that your IT environment meets current performance requirements, and is able to respond to changing infrastructure needs.

**Upgrade, Migration and Deployment** - This focus area shows recommendations to help you upgrade, migrate, and deploy SQL Server to your existing infrastructure.

**Operations and Monitoring** - This focus area shows recommendations to help streamline your IT operations, implement preventative maintenance, and maximize performance.

**Change and Configuration Management** - This focus area shows recommendations to help protect day-to-day operations, ensure that changes don't negatively affect your infrastructure, establish change control procedures, and to track and audit system configurations.

### Should you aim to score 100% in every focus area?

Not necessarily. The recommendations are based on the knowledge and experiences gained by Microsoft engineers across thousands of customer visits. However, no two server infrastructures are the same, and specific recommendations may be more or less relevant to you. For example, some security recommendations might be less relevant if your virtual machines are not exposed to the Internet. Some availability recommendations may be less relevant for services that provide low priority ad hoc data collection and reporting. Issues that are important to a mature business may be less important to a start-up. You may want to identify which focus areas are your priorities and then look at how your scores change over time.

Every recommendation includes guidance about why it is important. You should use this guidance to evaluate whether implementing the recommendation is appropriate for you, given the nature of your IT services and the business needs of your organization.

## Use assessment focus area recommendations

Before you can use an assessment solution in OMS, you must have the solution installed. To read more about installing solutions, see [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md). After it is installed, you can view the summary of recommendations by using the SQL Assessment tile on the Overview page in OMS.

View the summarized compliance assessments for your infrastructure and then drill-into recommendations.

### To view recommendations for a focus area and take corrective action

1. On the **Overview** page, click the **SQL Assessment** tile.
2. On the **SQL Assessment** page, review the summary information in one of the focus area blades and then click one to view recommendations for that focus area.
3. On any of the focus area pages, you can view the prioritized recommendations made for your environment. Click a recommendation under **Affected Objects** to view details about why the recommendation is made.  
    ![image of SQL Assessment recommendations](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. You can take corrective actions suggested in **Suggested Actions**. When the item has been addressed, later assessments will record that recommended actions were taken and your compliance score will increase. Corrected items appear as **Passed Objects**.

## Ignore recommendations

If you have recommendations that you want to ignore, you can create a text file that OMS will use to prevent recommendations from appearing in your assessment results.

### To identify recommendations that you will ignore

1.	Sign in to your workspace and open Log Search. Use the following query to list recommendations that have failed for computers in your environment.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Here's a screen shot showing the Log Search query:
    ![failed recommendations](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.	選擇您想要忽略的建議。您將使用下一個程序中的 RecommendationId 值。


### 建立及使用 IgnoreRecommendations.txt 文字檔案

1.	建立名為 IgnoreRecommendations.txt 的檔案。
2.	在個別行上貼上或輸入您想要 OMS 忽略之每個建議的各個 RecommendationId，然後儲存並關閉檔案。
3.	將檔案放在您想要 OMS 忽略建議之每一部電腦的下列資料夾中。
    - 在具有 Microsoft 監視代理程式的電腦 (直接或透過 Operations Manager 連線) 上 - *SystemDrive*:\\Program Files\\Microsoft Monitoring Agent\\Agent
    - 在 Operations Manager 管理伺服器上 - *SystemDrive*:\\Program Files\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### 驗證已忽略建議

1.	在執行下一個排定的評估之後，依預設是每隔 7 天執行一次，指定的建議會標示為忽略，且不會出現在評估儀表板。
2.	您可以使用下列記錄搜尋查詢列出所有已忽略的建議。

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.	如果您稍後決定想要查看忽略的建議，請移除任何 IgnoreRecommendations.txt 檔案，或從中移除 RecommendationID。

## SQL 評估方案常見問題集

*評估的執行頻率為何？*
- 評估會每隔 7 天執行一次。

*是否有設定評估執行頻率的方法？*
- 目前沒有。

*如果我在加入 SQL 評估方案後探索到另一部伺服器，方案也會評估這部伺服器嗎？*
- 是的。智慧套件會在探索到該伺服器之後每隔 7 天評估一次。

*如果把伺服器除役，何時能將它從評估中移除？*
- 如果伺服器在 3 週內未提交任何資料，智慧套件便會將其移除。

*負責收集資料之處理序的名稱為何？*
- AdvisorAssessment.exe

*收集資料需要花費多少時間？*
- 伺服器上的實際資料收集需費時約 1 小時。對於擁有大量 SQL 執行個體或資料庫的伺服器，資料收集可能需要花費更久的時間。

*收集的資料類型為何？*
- 收集的資料類型如下：
    - WMI
    - 登錄
    - 效能計數器
    - SQL 動態管理檢視 (DMV)。

*是否有設定資料收集時間的方法？*
- 目前沒有。

*為什麼我必須設定執行身分帳戶？*
- 智慧套件會針對 SQL Server 執行少量的 SQL 查詢。為了要執行 SQL 查詢，智慧套件必須使用具備「檢視伺服器狀態」權限的執行身分帳戶。此外，為了要查詢 WMI，智慧套件還需要本機系統管理員認證。

*為什麼只顯示前 10 項建議？*
- 與其提供鉅細靡遺的工作清單，我們建議您先著重於解決優先建議事項。解決後，智慧套件將會提供其他建議。如果您想要查看詳細清單，可以使用 OMS 記錄搜尋來檢視所有建議。

*是否有忽略建議的方法？*
- 是，請參閱上面的[忽略建議](#ignore-recommendations)一節。



## 後續步驟

- [搜尋記錄](log-analytics-log-searches.md)以檢視詳細的 SQL 評估資料和建議。

<!---HONumber=AcomDC_0504_2016-->