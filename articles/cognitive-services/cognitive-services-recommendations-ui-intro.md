---
title: "使用 Recommendations UI 建置模型 | Microsoft Docs"
description: "Azure 機器學習服務建議 - 使用 Recommendations UI 建置模型"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: b264fe44-f94e-40ae-9754-60ad7d6cfeb9
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 74cba2d29dec0c302fb969371f7b8fa58e4e4603


---
# <a name="building-a-model-with-the-recommendations-ui"></a>使用 Recommendations UI 建置模型
本文件是一份逐步解說指南。 我們的目標是引導您進行使用 [Recommendations UI](https://recommendations-portal.azurewebsites.net/) 來訓練模型所需的步驟。
進行練習可讓您先了解建置模型的程序，再透過程式設計方式執行。 它也會讓您熟悉 UI，在您開始使用服務時，這十分方便。

本練習大約需要 30 分鐘。

<a name="Step1"></a>

## <a name="step-1---sign-in-to-the-recommendations-ui"></a>步驟 1 - 登入 Recommendations UI
1. 如果您還沒有這麼做，則需要[註冊](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)新的 [Recommendations API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) 訂用帳戶。 您可以在 **Azure** (網址為 [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)) 上註冊服務，然後使用 Azure 帳戶登入。 [入門指南](cognitive-services-recommendations-quick-start.md)的*工作 1* 中提供詳細註冊程序指示 
2. 取得 Recommendations API 訂用帳戶的**金鑰**之後，請移至 [Recommendations UI](https://recommendations-portal.azurewebsites.net/)。 
3. 在 [帳戶金鑰] 欄位中輸入金鑰，然後按一下 [登入] 按鈕，以登入入口網站。
   
    ![Recommendations UI︰[登入] 對話方塊。][reco_signin]

<a name="Step2"></a>

## <a name="step-2---lets-gather-some-training-data"></a>步驟 2 - 收集一些訓練資料
引擎需要兩部分的資訊，才能建立組建︰目錄檔案以及一組使用方式檔案。 

目錄檔案包含您要提供給客戶之項目的相關資訊。 使用狀況檔案包含這些項目的使用方式，或者您商務交易的相關資訊。

通常您會針對這些部分的資訊來查詢存放區資料庫。 現在，我們提供一些範例資料，讓您可以了解如何使用 Recommendations API。

您可以從 [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData) 下載資料。 複製 **Books.Zip** 檔案，並將其解壓縮到本機電腦上的資料夾。 例如，**c:\data**。

如需目錄和使用方式檔案之結構描述的詳細資訊，請參閱[收集資料以訓練您的模型](cognitive-services-recommendations-collecting-data.md)一文。

在此練習中，我們將使用小型檔案，因此，您的訓練時間不會太長。 如果您想要嘗試更實際的檔案，我們已將包含 Microsoft 市集中範例交易的 **MsStoreData.zip** 放到[相同位置](http://aka.ms/RecoSampleData)中。

<a name="Step3"></a>

## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>步驟 3 - 建立專案，並上傳目錄和使用方式資料
登入 [Recommendations UI](https://recommendations-portal.azurewebsites.net/) 時，您會看到 [專案] 頁面。 如果您之前建立過任何專案，則應該會在這裡看到它們。
專案 (API 參考中也稱為「模型」) 是您目錄和使用方式資料的容器。 您可以在專案內建立數個 *builds*。 在接下來的步驟中，我們將逐步引導您完成這項程序。

1. 若要建立新的專案，請在文字方塊上輸入名稱 (與 “MyFirstModel” 類似)，然後按一下 [新增專案]。
   
    ![Recommendations UI︰[專案] 頁面。][reco_projects]
2. 建立專案之後，按一下 [新增目錄檔案] 區段上的 [瀏覽檔案] 按鈕。 
   上傳您在步驟 2 中取得的目錄。 如果您已將它儲存在 *c:\data*，則需要瀏覽至該資料夾。
   
     ![Recommendations UI︰將資料新增至專案。][reco_firstmodel]
3. 上傳目錄之後，請按一下 [新增使用方式檔案] 區段上的 [瀏覽檔案] 按鈕。 新增 usage_large.txt 檔案。

> **如果我有大型使用方式資料檔案，該怎麼辦？**
> 
> 只能上傳小於 200 MB 的使用方式檔案。 亦即，系統最多可以保留 2 GB 的交易資料，因此必要時，您可以上傳多個檔案。
> 您可能不需要那麼多資料就能產生不錯的模型，而這不只事關資料大小，也事關資料品質。 如果大部分交易只是針對少量常用項目，而且有其他項目的「小型信號」，則請查看使用方式資料。
> 
> 

<a name="Step4"></a>

## <a name="step-4---lets-do-some-training"></a>步驟 4 - 進行一些訓練！
現在，您已上傳目錄和使用方式資料，我們已準備好訓練引擎，以了解資料模式。

1. 按一下 [新增組建] 按鈕。
2. 選取 [建議] 作為建置類型。 請注意，我們也支援「排名組建」和 FBT (通常會一起購買) 組建類型。
   
   ![Recommendations UI︰[建置] 對話方塊。][reco_build_dialog.png]

    FBT 組建可讓您找出通常在相同交易中購買/使用之產品的模式。
    排名組建用來識別感興趣的功能。 
    在這場研討會中，我們不會深入探討 FBT 或排名組建，但是，如果您有興趣，則應該查看[組建類型與模型品質文件頁面](cognitive-services-recommendations-buildtypes.md)。

1. 按一下 [組建] 按鈕。 如果您使用 Books 資料，則建置程序大約需要五分鐘的時間。 較大型資料集需要較久的時間。

<a name="Step5"></a>

## <a name="step-5---lets-find-out-what-the-machine-learned"></a>步驟 5 - 找出電腦學到的內容！
建置完成之後，即會在建置清單中看到新的建置。 您可以查詢這個組建中的項目和使用者建議。

1. 建置完成之後，請按一下 [分數]。 這可讓您使用模型執行以及查看建議的項目。
   
    ![Recommendations UI︰[分數] 按鈕][reco_score_button]
2. 選取項目，以查看要傳回作為該項目之建議的項目。 請注意，如果交易不足無法預測特定項目的建議，則系統不會傳回該項目的任何建議。  如果您基於某個原因而有不傳回任何建議的項目，請嘗試對其他項目進行評分。

<a name="Step6"></a>

## <a name="step-6---next-steps"></a>步驟 6 - 後續步驟
恭喜！ 您已訓練模型，並且取得該模型的建議。  Recommendations UI 是很有用的工具，可讓您查看專案和建置的狀態。 

現在，您已經有一個模型，因此可能會想要了解如何透過程式設計方式執行上述所有步驟。 為了確實了解如何透過程式設計方式呼叫 API，請參閱 [Recommendations API Reference](http://go.microsoft.com/fwlink/?LinkId=759348) (Recommendations API 參考)，並下載 [Recommendations Sample Application](http://go.microsoft.com/fwlink/?LinkID=759344) (Recommendations 範例應用程式)。

[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png



<!--HONumber=Nov16_HO3-->


