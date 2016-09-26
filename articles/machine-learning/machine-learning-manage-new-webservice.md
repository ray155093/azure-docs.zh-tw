<properties
	pageTitle="管理新的機器學習 Web 服務 | Microsoft Azure"
	description="管理 Azure 機器學習工作區的存取權，並部署和管理 ML API Web 服務"
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="v-donglo"/>


# 管理新的機器學習 Web 服務

> [AZURE.NOTE] 本文中的程序都與 Azure Machine Learning 新的 Web 服務有關。如需管理傳統 Web 服務的資訊，請參閱[管理 Azure Machine Learning 工作區](machine-learning-manage-workspace.md)。

您可以使用 Microsoft Azure Machine Learning Web Services 入口網站，管理新的機器學習 Web 服務。您可以：

- 監視 Web 服務的使用方式。
- 設定描述、更新 Web 服務的金鑰、更新您的儲存體帳戶金鑰，以及啟用或停用範例資料。
- 刪除 Web 服務。
- 建立、刪除或更新計費方案。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> [AZURE.TIP] 在 Azure Machine Learning Studio 中，您可以在 [**Web 服務**] 索引標籤上加入、更新或刪除 Machine Learning Web 服務。

管理您的 Web 服務：

1.	使用您的 Microsoft Azure 帳戶登入 [Microsoft Azure Machine Learning Web 服務入口網站](https://services.azureml.net/quickstart) - 使用與 Azure 訂用帳戶相關聯的帳戶。
2.	在功能表上，按一下 [Web 服務]。

這會針對您的訂用帳戶顯示一份已部署的 Web 服務清單。若要管理 Web 服務，請按一下清單中的名稱以開啟 Web 服務頁面。

您可以從 [Web 服務] 頁面上︰

- 按一下 Web 服務進行管理。
- 按一下 Web 服務的 [計費方案] 進行更新。
- 刪除 Web 服務。
- 將 Web 服務複製到另一個區域。
- 複製 Web 服務並將它部署到另一個區域。

當您按一下某個 Web 服務時，Web 服務的 [快速入門] 頁面就會開啟。Web 服務 [快速入門] 頁面有兩個功能表選項可讓您管理您的 Web 服務︰

- **儀表板** -可讓您檢視 Web 服務使用量。
- **設定** -可讓您加入描述性文字、開啟和關閉錯誤記錄、更新與 Web 服務相關聯的儲存體帳戶金鑰，以及啟用和停用範例資料。

## 監視 Web 服務的使用方式

按一下 [**儀表板**] 索引標籤。

您可以從儀表板中檢視您的 Web 服務經過一段時間的整體使用量。您可以從使用量圖表右上角的 [期間] 下拉式功能表中選取要檢視的期間。儀表板會顯示下列資訊：

- **一段時間的要求數**顯示選取的一段時間內，要求數目的步階圖形。它可以協助識別您所遇到的使用量尖峰。
- **要求-回應要求數**顯示服務在選取的一段時間內收到的要求-回應呼叫總數，以及失敗的數目。
- **平均要求-回應計算時間**顯示執行收到的要求所需的平均時間。
- **批次要求數**顯示服務在選取的一段時間內收到的批次要求總數，以及失敗的數目。
- **平均作業延遲**顯示執行收到的要求所需的平均時間。
- **錯誤數**顯示呼叫 Web 服務時所發生的錯誤彙總數目。
- **服務成本**顯示與服務相關聯的計費方案費用。

## 設定 Web 服務 ##

按一下 [設定] 功能表選項。

您可以更新下列屬性：

* **描述**可讓您輸入 Web 服務的描述。
* **標題**可讓您輸入 Web 服務的標題。
* **金鑰**可讓您交換您的主要和次要 API 金鑰。
* **儲存體帳戶金鑰**可讓您為與 Web 服務變更相關聯的儲存體帳戶更新金鑰。
* **啟用範例資料**可讓您提供範例資料，用來測試要求-回應服務。如果您是在 Machine Learning Studio 中建立 Web 服務，範例資料會取自您用來訓練模型的資料。如果您是以程式設計方式建立服務，資料會取自您提供做為 JSON 套件一部分的範例資料。

## 管理計費方案

從 Web 服務 [快速入門] 頁面按一下 [方案] 功能表選項。您也可以按一下與特定 Web 服務相關聯的方案來管理該方案。

* **新增**可讓您建立新的方案。
* **新增/移除方案執行個體**可讓您「相應放大」現有的方案以增加容量。
* **升級/降級**可讓您「相應增加」現有的方案以增加容量。
* **刪除**可讓您刪除方案。

按一下方案可檢視其儀表板。儀表板可提供所選一段時間的快照或方案使用量。按一下儀表板資訊右上角的 [期間] 下拉式清單，可選取時間期間。

方案儀表板會提供下列資訊：

* **方案描述**顯示成本相關資訊和與方案相關聯的容量。
* **方案使用量**顯示交易數目和已依方案計費的計算時數。
* **Web 服務數**顯示使用此方案的 Web 服務數目。
* **依呼叫數的前幾名 Web 服務**顯示依方案計費進行呼叫的前四個 Web 服務。
* **依計算時數的前幾名 Web 服務**顯示依方案計費使用計算資源的前四個 Web 服務。

<!---HONumber=AcomDC_0914_2016-->