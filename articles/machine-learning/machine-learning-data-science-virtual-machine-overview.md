---
title: "什麼是資料科學虛擬機器？ | Microsoft Docs"
description: "了解重要案例、功能，以及如何開始使用資料科學虛擬機器、準備進行分析的環境和工具組。"
keywords: "資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 6c664a055e577881d4fcccd5b0ba4047d88aa9ef
ms.openlocfilehash: 64b413451c6ce47cc3aa14322b2aa0342e1e3ffe


---
# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>適用於 Linux 和 Windows 的雲端型資料科學虛擬機器簡介
資料科學虛擬機器是 Microsoft Azure 雲端上的自訂 VM 映像，專為進行資料科學建置。 它已預先安裝和預先設定許多常用的資料科學和其他工具，以開始建置智慧應用程式進行進階分析。 可用於 Windows Server 2012 或以 OpenLogic 7.2 CentOS 為基礎的 Linux 版本。 

本主題討論您可以使用資料科學 VM 來做什麼、說明使用 VM 的一些主要案例、逐項列出 Windows 和 Linux 版本中可使用的主要功能，並提供如何開始使用的相關指示。

## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>我可以使用資料科學虛擬機器來做什麼？
資料科學虛擬機器的目標是提供資料專業人員具有友善資料科學環境的所有技能等級和角色。 如果您已自行推出相當的環境，此 VM 可為您節省可觀的時間。 相反地，立即在新建立的 VM 執行個體中啟動您的資料科學專案。 

資料科學 VM 是針對使用廣泛用途案例而設計和設定的。 您可以在您的專案需要變更時，相應增加或相應減少您的環境。 可以使用您偏好的語言對資料科學工作進行程式設計。 您可以針對您確切的需求安裝其他工具和自訂系統。

## <a name="key-scenarios"></a>主要案例
本節建議一些資料科學 VM 可以部署的主要案例。

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>在雲端中預先設定分析桌面
資料科學 VM 提供資料科學小組外觀的基準組態，將其本機桌面取代為受管理的雲端桌面。 此基準確保小組的所有資料科學家都有一致的設定，用來確認實驗，並提升共同作業。 它也會藉由降低系統管理員的負擔並且節省評估、安裝所需的時間，及維護執行進階分析所需的各種軟體套件，來減少成本。  

### <a name="data-science-training-and-education"></a>資料科學訓練和教育
教導資料科學課程的企業訓練者和教育者，通常會提供虛擬機器映像，以確保他們的學生擁有一致的設定且範例如預期般運作。 資料科學 VM 會建立具有一致設定的隨選環境，緩解支援和不相容性挑戰。 在必須經常建置這些環境的案例中，特別是針對較短的訓練課程，可以有大幅獲益。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>大型專案的隨選彈性容量
資料科學黑客松 (Hackathon)/競賽或大型資料模型化和探索需要相應放大硬體容量，通常是很短的期間。 資料科學 VM 可幫助在相應放大的伺服器上快速隨選複寫資料科學環境，允許需要強大運算資源執行的實驗。

### <a name="short-term-experimentation-and-evaluation"></a>短期實驗和評估
資料科學 VM 可以用來評估或學習工具，例如 Microsoft R Server、SQL Server、Visual Studio 工具、Jupyter、深入學習/ML 工具組，與社群中需要最少設定的受歡迎新工具。 因為資料科學 VM 可快速設定，它可以套用至其他短期使用案例，例如複寫已發佈的實驗、執行示範、遵循線上課程或會議教學課程的逐步解說。

## <a name="whats-included-in-the-data-science-vm"></a>資料科學 VM 中包含什麼？
資料科學虛擬機器已安裝和設定許多常用的資料科學工具。 它也包含工具，可讓您輕鬆地使用各種 Azure 資料與分析產品。 您可以使用 Microsoft R Server 或 SQL Server 2016 在大型資料集上探索和建置預測模型。 來自開放原始碼社群及 Microsoft 的許多其他工具也會包含在內，以及範例程式碼和 Notebook。 下表列舉並比較 Windows 和 Linux 版本的資料科學虛擬機器所包含的主要元件。

| **Windows 版本** | **Linux 版本** |
| -- | --|
| 已預先安裝熱門套件的 Microsoft R Open | 已預先安裝熱門套件的 Microsoft R Open |
| 含 MicrosoftML 演算法及 Microsoft R 實作的 Microsoft R Server Developer Edition  |Microsoft R Server Developer Edition |
| Anaconda Python 2.7, 3.5 |已預先安裝熱門套件的 Anaconda Python 2.7、3.5|
| 已預先安裝熱門套件的 JuliaPro | 已預先安裝熱門套件的 JuliaPro |
| Jupyter Notebook Server (R、Python、Julia) |JupyterHub：多使用者 Jupyter Notebook (R、Python、Julia、PySpark) |
| SQL Server 2016 SP1 Developer Edition：可調整資料庫中分析與 R 服務 |PostgreSQL、SQuirreL SQL (資料庫工具)、SQL Server 驅動程式和命令列 (bcp、sqlcmd) |
|-  Visual Studio Community 版本 2015 (IDE) </br> - Azure HDInsight (Hadoop)、Data Lake、SQL Server Data Tools </br> - Node.js、Python 和適用於 Visual Studio 的 R 工具 (RTVS 0.5) </br>-R Studio 桌面|IDE 和編輯器 </br> - Eclipse 與 Azure 工具組外掛程式 </br> - Emacs (含 ESS, auctex) gedit </br> - IntelliJ IDEA</br> - PyCharm</br> - Atom</br> - Visual Studio 程式碼|
| Power BI 桌面 |-- |
| 機器學習工具 </br> - 與 Azure Machine Learning 整合 </br> - Microsoft Cognitive Toolkit (CNTK 2.0) 深入學習/人工智慧 </br> - Xgboost (資料科學競賽中的熱門 ML 工具) </br> - Vowpal Wabbit (快速線上學習模組) </br> - Rattle (視覺效果快速入門資料和分析工具) </br> - Mxnet (深入學習/AI) </br> -Tensorflow  |機器學習工具 </br> - 與 Azure Machine Learning 整合 </br> - CNTK (深入學習/AI) </br> - Xgboost (資料科學競賽中的熱門 ML 工具) </br> - Vowpal Wabbit (快速線上學習模組) </br> - Rattle (視覺效果快速入門資料和分析工具) </br> -Mxnet (深入學習/AI)|
| SDK，用以存取服務的 Azure 和 Cortana Intelligence Suite |SDK，用以存取服務的 Azure 和 Cortana Intelligence Suite |
| Azure 和巨量資料資源的資料移動和管理工具：Azure 儲存體總管、CLI、PowerShell、AdlCopy (Azure Data Lake)、AzCopy、dtui (適用於 DocumentDB)、Microsoft 資料管理閘道器 |Azure 和巨量資料資源的資料移動和管理工具：Azure 儲存體總管、CLI |
| Git、Visual Studio Team Services 外掛程式 |Git |
| 最受歡迎 Linux/Unix 命令列公用程式的 Windows 連接埠可透過 GitBash/命令提示字元存取 |-- |
| Weka | Weka |
| 深入探詢 | 深入探詢 |
| --| Spark 本機 |

## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>如何開始使用 Windows 資料科學 VM
* 在 Windows 上建立 VM 的執行個體，方法是瀏覽至[本頁面](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)並選取綠色 [建立虛擬機器] 按鈕。
* 使用您建立 VM 時指定的認證，從遠端桌面登入 VM。
* 若要探索和啟動可用的工具，按一下 [啟動] 功能表。

## <a name="get-started-with-the-linux-data-science-vm"></a>開始使用 Linux 資料科學 VM
* 在 Linux (以 OpenLogic CentOS 為基礎) 上建立 VM 的執行個體，方法是瀏覽至[本頁面](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)並選取 [建立虛擬機器] 按鈕。
* 使用您建立 VM 時指定的認證，從 SSH 用戶端登入 VM，例如 Putty 或 SSH 命令。
* 在殼層提示字元中，輸入 dsvm-more-info。
* 對於圖形化桌面，在[這裡](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)針對您的用戶端平台下載 X2Go 用戶端，並且遵循 Linux 資料科學 VM 文件[佈建 Linux 資料科學虛擬機器](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client)中的指示。

## <a name="next-steps"></a>後續步驟
### <a name="for-the-windows-data-science-vm"></a>對於 Windows 資料科學 VM
* 如需有關如何在 Windows 版本上執行特定工具的詳細資訊，請參閱[佈建 Microsoft 資料科學虛擬機器](machine-learning-data-science-provision-vm.md)和
* 如需有關如何執行您在 Windows VM 上的資料科學專案所需的各種工作的詳細資訊，請參閱[您可以在資料科學虛擬機器上做的十件事](machine-learning-data-science-vm-do-ten-things.md)。

### <a name="for-the-linux-data-science-vm"></a>對於 Linux 資料科學 VM
* 如需有關如何在 Linux 版本上執行特定工具的詳細資訊，請參閱[佈建 Linux 資料科學虛擬機器](machine-learning-data-science-linux-dsvm-intro.md)。
* 如需為您示範如何使用 Linux VM 執行數個常見資料科學工作的逐步解說，請參閱 [Linux 資料科學虛擬機器上的資料科學](machine-learning-data-science-linux-dsvm-walkthrough.md)。




<!--HONumber=Feb17_HO1-->


