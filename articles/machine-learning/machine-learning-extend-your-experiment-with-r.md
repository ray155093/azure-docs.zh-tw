<properties
	pageTitle="透過 R 擴展您的經驗 | Microsoft Azure"
	description="如何利用「執行 R 指令碼」模組，透過 R 語言擴充 Azure Machine Learning Studio 的功能。"
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
	ms.date="08/19/2016"
	ms.author="garye" />


#透過 R 擴展您的經驗

您可以利用[執行 R 指令碼][execute-r-script]模組，透過 R 語言擴充 ML Studio 的功能。

此模組接受多個輸入資料集，並產生單一資料集作為輸出。您可以將 R 指令碼輸入至[執行 R 指令碼][execute-r-script]模組的 [**R 指令碼**] 參數。

您可使用類似下面的程式碼，存取模組的每個輸入連接埠：

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

##列出所有目前安裝的封裝

可以變更已安裝的封裝清單。若要取得目前已安裝封裝的最新完整清單，包括每個封裝的描述，請將下列程式碼輸入[執行 R 指令碼][execute-r-script]模組中︰

    out <- data.frame(installed.packages(,,,fields="Description"))
	maml.mapOutputPort("out")

這會將封裝的清單傳送至[執行 R 指令碼][execute-r-script]模組的輸出連接埠。若要檢視封裝清單，請將轉換模組 (例如[轉換為 CSV][convert-to-csv]) 連接至[執行 R 指令碼][execute-r-script]模組的左輸出，執行實驗，然後按一下轉換模組的輸出並選取 [下載]。

![](./media/machine-learning-extend-your-experiment-with-r/download-package-list.png)


##匯入封裝

您也可以使用[執行 R 指令碼][execute-r-script]模組和已壓縮封裝封存檔中的下列命令，匯入尚未從分段 ML Studio 儲存機制安裝的封裝：

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

其中 `my_favorite_package.zip` 包含封裝的壓縮檔。


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!---HONumber=AcomDC_0824_2016-->