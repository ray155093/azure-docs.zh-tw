---
title: "在 Azure Machine Learning 中為模型偵錯 | Microsoft Docs"
description: "說明如何在 Azure Machine Learning 中為模型偵錯。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: bradsev;garye
translationtype: Human Translation
ms.sourcegitcommit: 8c2fb72de830a699c96f3593e9f07a871614267b
ms.openlocfilehash: 58e6cc28a624b0d873e3eabeba971cc8e909267a


---
# <a name="debug-your-model-in-azure-machine-learning"></a>在 Azure Machine Learning 中為模型偵錯
本文說明如何在 Microsoft Azure Machine Learning 中為模型偵錯。 具體來說，它涵蓋了為什麼執行模型時可能會發生以下任一個失敗的潛在原因：

* [定型模型][train-model]模組產生錯誤 
* [計分模型][score-model]模組產生不正確的結果 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>定型模型模組產生錯誤
![image1](./media/machine-learning-debug-models/train_model-1.png)

[定型模型][train-model]模組預期以下 2 個輸入：

1. 來自 Azure Machine Learning 所提供的模型集合的分類/迴歸模型類型
2. 包含指定之 [標籤] 資料行的訓練資料。 [標籤] 資料行會指定要預測的變數。 包含的其餘資料行則假設為功能。

在以下情況下，此模組會產生錯誤：

1. 由於選取了多個資料行做為 [標籤]，或選取了不正確的資料行索引，因此指定的 [標籤] 資料行不正確。 例如，如果使用的資料行索引為 30，但輸入資料集只有 25 個資料行，則適用第二個狀況。
2. 資料集不包含任何 [功能] 資料行。 例如，如果輸入資料集只有 1 個標示為 [標籤] 資料行的資料行，則沒有用來建置模型的功能。 在此情況下，[定型模型][train-model]模組會產生錯誤。
3. 輸入資料集 ([功能] 或 [標籤]) 含有無限大值。

## <a name="score-model-module-does-not-produce-correct-results"></a>[評分模型] 模組不會產生正確的結果
![image2](./media/machine-learning-debug-models/train_test-2.png)

在受監督之學習的典型訓練/測試圖形中，[分割資料][split]模組會將原始資料集分成兩個部分：用於定型模型的部分，以及保留來用於計分的部分 (針對定型模型在未定型資料上的表現程度計分)。 接著，使用定型模型為測試資料評分，然後再評估結果以判斷模型的準確性。

[計分模型][score-model]模組需要兩個輸入：

1. 來自[定型模型][train-model]模組的定型模型輸出
2. 不同於用來培訓模型之資料集的評分資料集

即使實驗成功，[計分模型][score-model]模組還是可能會產生不正確的結果。 有幾個情況可能會導致這個結果：

1. 如果指定的 [標籤] 經過分類，且迴歸模型的資料已定型，則[計分模型][score-model]模組將會產生不正確的輸出。 這是因為迴歸需要持需回應變數。 在此情況下，更適合使用分類模型。 
2. 同樣地，如果分類模型針對 [標籤] 資料行中包含浮點數的資料集進行訓練，它可能會產生非預期的結果。 這是因為分類需要離散回應變數，該變數僅允許涉及一組有限且通常比較小的類別的值。
3. 如果計分資料集不包含用來定型模型的所有特徵，[計分模型][score-model]會產生錯誤。
4. 如果計分資料集中的資料列在其任何特徵中含有遺漏值或無限值，[計分模型][score-model]將不會產生對應於該資料列的任何輸出。
5. 對於計分資料集中的所有資料列，[計分模型][score-model]可能會產生相同的輸出。 例如，使用決策樹系嘗試分類時，如果選擇每個分葉節點範例的最小數目大於可用的訓練範例數目時，可能會發生這個情況。

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/




<!--HONumber=Dec16_HO2-->


