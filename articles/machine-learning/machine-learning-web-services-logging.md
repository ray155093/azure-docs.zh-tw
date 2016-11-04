---
title: Machine Learning Web 服務的記錄 | Microsoft Docs
description: 了解如何為 Machine Learning Web 服務啟用記錄。 記錄提供可協助疑難排解 API 的其他資訊。
services: machine-learning
documentationcenter: ''
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/05/2016
ms.author: raymondl;garye

---
# <a name="enable-logging-for-machine-learning-web-services"></a>為 Machine Learning Web 服務啟用記錄
本文件提供傳統 Web 服務記錄功能的相關資訊。 在 Web 服務中啟用記錄會提供其他資訊，而不只是錯誤碼和訊息而已，可協助您針對機器學習 API 的呼叫進行疑難排解。  

在 Azure 傳統入口網站啟用 Web 服務中的記錄︰   

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)
2. 在左邊功能欄中，按一下 [MACHINE LEARNING]。
3. 按一下工作區，然後按一下 [Web 服務]。
4. 在 [Web 服務] 清單中，按一下 Web 服務的名稱。
5. 在 [端點] 清單中，按一下端點名稱。
6. 按一下 [設定] 。
7. 將 [診斷追蹤層級] 設為 [錯誤] 或 [全部]，然後按一下 [儲存]。

在 Azure Machine Learning Web 服務入口網站中啟用記錄。

1. 登入 [Azure Machine Learning Web 服務](https://services.azureml.net)入口網站。
2. 按一下 [傳統 Web 服務]。
3. 在 [Web 服務] 清單中，按一下 Web 服務的名稱。
4. 在 [端點] 清單中，按一下端點名稱。
5. 按一下 [TestLab] 。
6. 將 [記錄] 設為 [錯誤] 或 [全部]，然後按一下 [儲存]。

## <a name="the-effects-of-enabling-logging"></a>啟用記錄的效果
記錄啟用時，所有的診斷和錯誤都會從已選取的端點記錄到與使用者工作區連結的 Azure 儲存體帳戶。 您可以他們工作區的 Azure 傳統入口網站 [儀表板] 檢視 (在 [快速概覽] 的底部) 中看到此儲存體帳戶 。  

記錄檔可使用任何可用於「探索」儲存體帳戶的多種工具來檢視。 最簡單的方法，請直接瀏覽到 Azure 傳統入口網站中的 [儲存體帳戶]，然後按一下 [容器]。 您將會看到名為 [ML-診斷] 的容器。 這個容器針對所有與此儲存體帳戶相關聯的工作區，存放所有 Web 服務端點的診斷資訊。 

## <a name="log-blob-detail-information"></a>記錄檔 blob 詳細資訊
在容器中的每個 blob，只會存放下列其中一項的診斷資訊：

* 批次執行方法的執行  
* 要求-回應方法的執行  
* 要求-回應容器的初始化

每個 blob 的名稱具有下列形式的前置詞︰ 

{工作區識別碼}-{Web 服務識別碼}-{端點識別碼}/{記錄類型}  

其中的「記錄類型」是下列其中一個值：  

* batch  
* 分數/要求  
* 分數/初始  

<!--HONumber=Oct16_HO2-->


