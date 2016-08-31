<properties
	pageTitle="建立 Machine Learning 工作區 | Microsoft Azure"
	description="如何建立 Azure Machine Learning Studio 的工作區"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="garye;bradsev;ahgyger"/>


# 建立和共用 Azure Machine Learning 工作區

此功能表會連結至說明如何設定 Cortana Analytics 程序 (CAP) 所用的各種資料科學環境主題。

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

若要使用 Azure Machine Learning Studio，您需要有機器學習服務工作區。此工作區包含您建立、管理及發行實驗所需的工具。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

## 建立工作區

1. 登入 [Microsoft Azure 傳統入口網站]。

> [AZURE.NOTE] 若要登入，您必須是 Azure 訂用帳戶管理員。成為機器學習服務工作區的擁有者，並不會讓您存取 [Microsoft Azure 傳統入口網站]。如需詳細資訊，請參閱 [Azure 訂用帳戶管理員與工作區擁有者的權限](#subscriptionvsworkspace)。

2. 在 Microsoft Azure 服務面板中，按一下 [機器學習]。

    ![機器學習服務][1]

3. 按一下視窗底部的 [**+新增**]。
4. 按一下 [**資料服務**]，然後 [**機器學習服務**]，然後按一下 [**快速建立**]。

	![快速建立新工作區][3]

5. 輸入工作區的**工作區名稱**。
6. 指定 Azure **位置**，然後輸入現有的 Azure **儲存體帳戶**或選取 [**建立新的儲存體帳戶**] 來新建一個。
7. 按一下 [建立 ML 工作區]。

機器學習服務工作區建立後，您會看見它列在**機器學習服務**頁面上。

## 共用 Azure 機器學習服務工作區

建立機器學習服務工作區之後，您就可以邀請使用者加入您的工作區，並與您的工作區和其所有實驗共用存取權。我們支援兩個使用者角色：

- **使用者** - 工作區使用者可以在工作區中建立、開啟、修改和刪除資料集、實驗和 Web 服務。
- **擁有者** - 除了使用者可以執行的動作以外，擁有者可以邀請、移除和列出可存取工作區的使用者。他也可以存取筆記本。

### 共用工作區
1. 登入 [Machine Learning Studio]
2. 在 Machine Learning Studio 面板中，按一下 [設定]
3. 按一下 [使用者]
4. 按一下 [邀請更多使用者]

    ![邀請更多使用者][4]

5. 輸入一或多個電子郵件地址。使用者只需要有效的 Microsoft 帳戶 (例如 name@outlook.com) 或組織帳戶 (來自 Azure Active Directory)。
6. 按一下核取按鈕。

您加入的每個使用者會收到一封電子郵件，其中含有登入共用工作區的指示。

如需管理您的工作區的詳細資訊，請參閱[管理 Azure Machine Learning 工作區]。如果您對於建立您的工作區遇到問題，請參閱[疑難排解指南：建立及連接至機器學習服務工作區]。

## <a name="subscriptionvsworkspace"></a>Azure 訂用帳戶管理員與工作區擁有者的權限

下表釐清 Azure 訂用帳戶管理員和工作區擁有者之間差異。

| 動作 | Azure 訂用帳戶管理員 | 工作區擁有者 |
| --------------			|:------------------------:| :----------------:|
| 存取 [Microsoft Azure 傳統入口網站]| 是 | 否 |
| 建立新的工作區 | 是 | 否 |
| 刪除工作區 | 是 | 否 |
| 將端點加入至 Web 服務 | 是 | 否 |
| 從 Web 服務刪除端點 | 是 | 否 |
| 變更 Web 服務的並行存取 | 是 | 否 |
| 存取 [Machine Learning Studio] | 否 * | 是 |


> [AZURE.NOTE] * Azure 訂用帳戶管理員會自動加入至其以工作區擁有者身分建立的工作區中。不過，僅成為 Azure 訂用帳戶管理員，將不會為其授與存取該訂用帳戶下方任何工作區的權限。

<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw4.png
[4]: media/machine-learning-create-workspace/cw5.png


<!--Link references-->
[管理 Azure Machine Learning 工作區]: machine-learning-manage-workspace.md
[疑難排解指南：建立及連接至機器學習服務工作區]: machine-learning-troubleshooting-creating-ml-workspace.md
[Machine Learning Studio]: https://studio.azureml.net/
[Microsoft Azure 傳統入口網站]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0817_2016-->