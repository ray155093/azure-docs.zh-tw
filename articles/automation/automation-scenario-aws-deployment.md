<properties
   pageTitle="自動化 Amazon Web Services 中 VM 的部署 | Microsoft Azure"
   description="本文示範如何使用 Azure 自動化來自動化 Amazon Web 服務 VM 的建立"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="tiandert; bwren" />

# Azure 自動化案例 - 佈建 AWS 虛擬機器 

在本文中，我們會示範如何利用 Azure 自動化在 Amazon Web Service (AWS) 訂用帳戶中佈建虛擬機器，並且提供該 VM 的特定名稱 (AWS 稱為「標記」VM)。

## 必要條件

基於本文的目的，您需要擁有 Azure 自動化帳戶和 AWS 訂用帳戶。如需設定 Azure 自動化帳戶以及使用 AWS 訂用帳戶認證進行設定的詳細資訊，請檢閱[使用 Amazon Web Services 設定驗證](../automation/automation-sec-configure-aws-account.md)。繼續之前，應該使用您的 AWS 訂用帳戶認證來建立或更新此帳戶，因為我們將在下列步驟中參考此帳戶。


## 部署 Amazon Web Services PowerShell 模組

我們的 VM 佈建 Runbook 將會利用 AWS PowerShell 模組來執行其工作。執行下列步驟，將模組新增至使用 AWS 訂用帳戶認證所設定的自動化帳戶。

1. 開啟網頁瀏覽器，並瀏覽至 [PowerShell 資源庫](http://www.powershellgallery.com/packages/AWSPowerShell/)，然後按一下 [Deploy to Azure Automation](部署至 Azure 自動化) 按鈕。<br> ![AWS PS 模組匯入](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)

2. 系統會帶您前往 Azure 登入頁面，而在驗證之後，則會帶您到 Azure 入口網站，並出現下列刀鋒視窗。<br> ![匯入模組刀鋒視窗](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)

3. 從 [資源群組] 下拉式清單中選取資源群組，並在 [參數] 刀鋒視窗上提供下列資訊：
   * 從 [新增或現有自動化帳戶 (字串)] 下拉式清單中，選取 [現有]。  
   * 在 [自動化帳戶名稱 (字串)] 方塊中，輸入包括 AWS 訂用帳戶認證的確切自動化帳戶名稱。例如，如果您已建立名為 **AWSAutomation** 的專用帳戶，則這是您在此方塊中輸入的名稱。
   * 從 [自動化帳戶位置] 下拉式清單中，選取適當的區域。

4. 輸入完所需的資訊後，請按一下 [建立]。

    >[AZURE.NOTE] 將 PowerShell 模組匯入 Azure 自動化時，也會擷取 Cmdlet，而且在模組完全完成匯入並擷取 Cmdlet 之前不會出現這些活動。此程序需要數分鐘的時間。<br>
5. 在 Azure 入口網站中，開啟步驟 3 中所參考的自動化帳戶。
6. 按一下 [ 資產] 磚，並在 [資產] 刀鋒視窗上選取 [模組] 圖格 。
7. 在 [模組] 刀鋒視窗上，您將會在清單中看到 **AWSPowerShell** 模組。

## 建立 AWS 部署 VM Runbook

部署 AWS PowerShell 模組之後，我們現在可以製作 Runbook，以使用 PowerShell 指令碼自動在 AWS 中佈建虛擬機器。下面的步驟將示範如何在 Azure 自動化中利用原生 PowerShell 指令碼。

>[AZURE.NOTE] 如需進一步選項以及此指令碼的相關資訊，請瀏覽 [PowerShell 資源庫](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript)。


1. 開啟 PowerShell 工作階段並且輸入下列指令，以從 PowerShell 資源庫下載 PowerShell 指令碼 New-AwsVM：<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
<br>
2. 從 Azure 入口網站中，開啟您的自動化帳戶，然後按一下 [Runbook] 圖格 。  
3. 從 [Runbook] 刀鋒視窗中，選取 [新增 Runbook]。
4. 在 [新增 Runbook] 刀鋒視窗上，選取 [快速建立] \ (建立新的 Runbook)。
5. 在 [Runbook] 屬性刀鋒視窗上，於 Runbook 的 [名稱] 方塊中輸入名稱，並從 [Runbook 類型] 下拉式清單中選取 [PowerShell]，然後按一下 [建立]。<br> ![匯入模組刀鋒視窗](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. [編輯 PowerShell Runbook] 刀鋒視窗出現時，請將 PowerShell 指令碼複製並貼入 Runbook 製作畫布。<br> ![Runbook PowerShell 指令碼](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>

    >[AZURE.NOTE] 使用範例 PowerShell 指令碼時，請注意下列事項：
    >
    > - Runbook 包含一些預設參數值。請評估所有預設值，並且視需要更新。
    > - 如果您已將 AWS 認證儲存為名稱與 **AWScred** 不同的認證資產，則需要更新指令碼的第 57 行，使其相符。  
    > - 在 PowerShell 中使用 AWS CLI 命令 (特別是使用此範例 Runbook) 時，您必須指定 AWS 區域。否則，Cmdlet 會失敗。如需進一步詳細資訊，請檢視 AWS Tools for PowerShell 文件中的 AWS 主題：[指定 AWS 區域](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html)。<br>
7. 若要從 AWS 訂用帳戶擷取映像名稱清單，請啟動 PowerShell ISE 並匯入 AWS PowerShell 模組。將 ISE 環境中的 **Get-AutomationPSCredential** 取代為 **AWScred = Get-Credential**，以驗證 AWS。這將會提示您輸入認證，您可以提供您的**便捷鍵識別碼**和**密碼便捷鍵**作為使用者名稱和密碼。請參閱下面的範例：

		#Sample to get the AWS VM available images
		#Please provide the path where you have downloaded the AWS PowerShell module
		Import-Module AWSPowerShell
		$AwsRegion = "us-west-2"
		$AwsCred = Get-Credential
		$AwsAccessKeyId = $AwsCred.UserName
		$AwsSecretKey = $AwsCred.GetNetworkCredential().Password

		# Set up the environment to access AWS
		Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
		Set-DefaultAWSRegion -Region $AwsRegion

		Get-EC2ImageByName -ProfileName AWSProfile
會傳回下列輸出：<br> ![取得 AWS 映像](./media/automation-scenario-aws-deployment/powershell-ise-output.png)  
8. 在自動化變數中複製並貼上其中一個映像名稱，在 Runbook 中參考為 **$InstanceType**。因為我們在此範例中使用免費 AWS 層訂用帳戶，所以將使用 **t2.micro** 作為 Runbook 範例。
9. 儲存 Runbook，然後按一下 [發佈] 來發佈 Runbook，並在出現提示時按一下 [是]。


### 測試 AWS VM Runbook
繼續測試 Runbook 之前，需要驗證一些事項。具體而言：

   -  驗證 AWS 的資產已建立並命名為 **AWScred**，或指令碼已更新成參考您認證資產的名稱。  
   -  AWS PowerShell 模組已匯入 Azure 自動化
   -  已建立新的 Runbook，已驗證參數值並且視需要更新
   -  Runbook 設定 [記錄和追蹤] 下的 [記錄詳細資訊記錄] 和選擇性的 [記錄進度記錄] 已設為 [開啟]。<br> ![Runbook 記錄和追蹤](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)

1. 我們想要啟動 Runbook，因此按一下 [開始]，然後在 [啟動 Runbook] 刀鋒視窗開啟時按一下 [確定]。
2. 在 [啟動 Runbook] 刀鋒視窗上，提供 **VMname**。接受您稍早已在指令碼中為其他參數預先設定的預設值。按一下 [確定] 啟動 Runbook 工作。<br> ![啟動 New-AwsVM Runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. 工作窗格會開啟我們剛剛建立的 Runbook 工作。關閉此窗格。
4. 從 Runbook 工作刀鋒視窗中選取 [所有記錄檔] 圖格 ，即可檢視工作進度以及檢視輸出**資料流**。<br> ![串流輸出](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. 若要確認正在佈建 VM，請登入 AWS 管理主控台 (如果目前尚未登入)。<br> ![AWS 主控台部署 VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## 後續步驟
-   若要開始使用圖形化 Runbook，請參閱[我的第一個圖形化 Runbook](automation-first-runbook-graphical.md)
-	若要開始使用 PowerShell 工作流程 Runbook，請參閱[我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)
-	若要深入了解 Runbook 類型、其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)
-	如需 PowerShell 指令碼支援功能的詳細資訊，請參閱 [Azure 自動化中的原生 PowerShell 指令碼支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

<!---HONumber=AcomDC_0622_2016-->