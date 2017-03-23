---
title: "從另一個實驗匯入資料到 Machine Learning Studio | Microsoft Docs"
description: "如何將訓練資料儲存在 Azure Machine Learning Studio 並將它使用於另一個實驗中。"
keywords: "匯入資料,資料,資料來源,定型資料"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 7da9dcec-5693-4bb6-8166-15904e7f75c3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 1738899a271045e37cdcedaaf862553f29ec92f1
ms.openlocfilehash: b1c06135cf8628197fb9601affd6a7131b41d0ca
ms.lasthandoff: 12/19/2016


---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>從另一個實驗匯入資料到 Azure Machine Learning Studio。
[!INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

有時候您會想要從實驗取得中繼結果，並且用來做為其他實驗的一部分。 若要這樣做，您可以將模組另存為資料集：

1. 按一下您要儲存為資料集的模組輸出。
2. 按一下 [ **另存為資料集**]。
3. 系統提示時，請輸入可讓您輕易識別資料集的名稱和說明。
4. 按一下 [ **確定** ] 核取記號。

儲存完成時，資料集可用於工作區中的任何實驗。 您可以在模組調色盤的 [ **儲存的資料集** ] 清單中找到它。


