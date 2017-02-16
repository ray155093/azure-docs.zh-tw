---
title: "使用 Machine Learning 建立的信用風險預測解決方案 | Microsoft Docs"
description: "詳細的逐步解說說明如何在 Azure Machine Learning 中為信用風險評估建立預測分析解決方案。"
keywords: "信用風險, 預測性分析解決方案, 風險評估"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 07cb3fe0d5688d5b63fe3312cad14c2274a58a09
ms.openlocfilehash: e98a64910f28da0a8a9b4a58c717c40d791ccf00


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>逐步解說：在 Azure Machine Learning 中為信用風險評估開發預測分析解決方案

在本逐步解說中，我們將探討在 Machine Learning Studio 中開發解決方案的程序。 我們將在 Machine Learning Studio 中開發預測性分析模型，然後將其部署為 Azure Machine Learning Web 服務，以便模型使用新資料進行預測。 

> [!TIP]
> 本逐步解說假設您之前已使用 Machine Learning Studio 至少一次，而且您有些了解機器學習服務的概念 (雖然也會假設您不是專家)。
> 
>如果您未曾使用過 **Azure Machine Learning Studio**，您可以從[在 Azure Machine Learning Studio 中建立您的第一個資料科學實驗](machine-learning-create-experiment.md)教學課程著手。 本教學課程會引導您第一次使用 Machine Learning Studio，並說明如何拖放模組到您的實驗、將它們連接在一起、執行實驗及檢視結果的基本概念。
>
>如果您是機器學習服務的新手，[初學者資料科學](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md)影片系列可能是不錯的起點。 此影片系列使用日常用語和概念，是絕佳的機器學習服務介紹。
> 

## <a name="the-problem"></a>問題

假設您必須根據某人在信用申請書上提供的資訊預測其信用風險。  

當然，信用風險評估是一個複雜的問題，但是我們會稍微簡化問題的參數。 然後，我們會將其做為範例，讓您據以搭配 Machine Learning Studio 與 Machine Learning Web 服務使用 Microsoft Azure Machine Learning，以建立此類的預測分析解決方案。  

## <a name="the-solution"></a>解決方案

在此詳細的逐步解說中，我們將從公開的信用風險資料開始，根據這項資料開發並訓練預測性模型，然後將模型部署為可供其他人用於信用風險評估的 Web 服務。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

我們將依照以下步驟建立信用風險評估解決方案：  

1. [建立機器學習服務工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [上傳現有資料](machine-learning-walkthrough-2-upload-data.md)
3. [建立新實驗](machine-learning-walkthrough-3-create-new-experiment.md)
4. [訓練及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [部署 Web 服務](machine-learning-walkthrough-5-publish-web-service.md)
6. [存取 Web 服務](machine-learning-walkthrough-6-access-web-service.md)

此逐步解說以 [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) 中的簡化版[二進位分類：信用風險預測](http://go.microsoft.com/fwlink/?LinkID=525270)範例實驗為基礎。


> [!TIP]
> 若要下載並列印提供 Machine Learning Studio 功能概觀的圖表，請參閱 [Azure Machine Learning Studio 功能的概觀圖](machine-learning-studio-overview-diagram.md)。
> 
> 



<!--HONumber=Dec16_HO3-->


