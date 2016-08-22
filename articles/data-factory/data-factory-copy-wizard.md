<properties 
	pageTitle="Data Factory 複製精靈 | Microsoft Azure" 
	description="了解如何使用 Data Factory 複製精靈，將資料從支援的資料來源複製到接收。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/27/2016" 
	ms.author="spelluru"/>

# Data Factory 複製精靈
**Data Factory 複製精靈**可讓您建立管線，以在不需要撰寫連結服務、資料集和管線之 JSON 定義的情況下，將資料從支援的來源複製到目的地。若要啟動複製精靈，請在 Data Factory 首頁按一下 [複製資料] 圖格。

![複製資料精靈](./media/data-factory-copy-wizard/copy-data-wizard.png)

## 特性

### 用法直覺且操作流暢的資料複製精靈 
此精靈可讓您在幾分鐘內輕鬆地將資料從來源移動到目的地，只需要完成以下簡單的步驟即可︰

1.	選取 [來源]
2.	選取 [目的地]
3.	配置 [設定]

![選取資料來源](./media/data-factory-copy-wizard/select-data-source-page.png)

### 豐富的資料探索和結構描述對應
在精靈中，您可以瀏覽資料表/資料夾、預覽資料、對應結構描述、驗證運算式及執行簡單的資料轉換。

**瀏覽資料表/資料夾** ![瀏覽資料表和資料夾](./media/data-factory-copy-wizard/browse-tables-folders.png)

### 適用於多樣化資料和物件類型的可調整經驗
經驗的設計之初即是以巨量資料為出發點。它是撰寫 Data Factory 管線既簡單又有效率的方式，讓您得以移動數百個資料夾、檔案或資料表。

**預覽資料、對應結構描述及執行簡單的轉換** ![檔案格式設定](./media/data-factory-copy-wizard/file-format-settings.png) ![結構描述對應](./media/data-factory-copy-wizard/schema-mapping.png) ![驗證運算式](./media/data-factory-copy-wizard/validate-expressions.png)

### 適用於多樣化資料和物件類型的可調整經驗
經驗的設計之初即是以巨量資料為出發點。移動數百個資料夾、檔案或資料表，就像使用複製精靈撰寫一樣簡單而有效率。

![選取要複製資料的資料表](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

### 更豐富的排程選項
您可以執行單次的複製作業，或按照排程 (每小時、每日等) 執行。這兩個選項均適用於範圍跨越內部部署、雲端和本機桌面複本的廣闊連接器。一次性複製可讓您從來源將資料移動到目的地一次，適用於所有規模和支援之格式的資料。排程複製可讓您依照指定的週期複製資料。在設定排程複製時，您可以運用豐富的設定 (如重試、逾時、警示等)。

![排程屬性](./media/data-factory-copy-wizard/scheduling-properties.png)


## 教學課程
如需使用 **Data Factory 複製精靈**建立含複製活動之管線的快速逐步解說，請參閱[教學課程：使用 Data Factory 複製精靈建立具有複製活動的管線](data-factory-copy-data-wizard-tutorial.md)。


## Azure Blob 資料夾路徑中的變數
您可以在資料夾路徑中使用變數，以複製在執行階段根據 [WindowStart 系統變數](data-factory-functions-variables.md#data-factory-system-variables)決定之資料夾內的資料。支援的變數包括︰**year**、**month**、**day**、**hour**、**minute** 及 **{custom}**。範例︰inputfolder/{year}/{month}/{day}。

假設您的輸入資料夾格式如下︰
	
	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

按一下 [瀏覽]按鈕以取用 [檔案或資料夾]，瀏覽至其中一個資料夾 (假設是 2016->03->01->02)，然後按一下 [選擇]。您現在應該會在文字方塊中看到 **2016/03/01/02**。現在，請將 **2016** 取代為 **{year}**、**03** 取代為 **{month}**、**01** 取代為 **{day}**、**02** 取代為 **{hour}**，然後按下 **TAB** 鍵。您應該會看到選取這四個變數之**格式**的下拉式清單，如下所示︰

![使用系統變數](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)

您也可以使用如下所示的 **custom** 變數，並使用任何[支援的格式字串](https://msdn.microsoft.com/library/8kb3ddd4.aspx)。請務必先使用 [瀏覽] 按鈕來選取該結構的資料夾、將值取代為 **{custom}**，然後按下 **TAB** 鍵來查看用來輸入格式字串的文字方塊。

![使用自訂變數](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## 後續步驟
- [教學課程：使用 Data Factory 複製精靈建立具有複製活動的管線](data-factory-copy-data-wizard-tutorial.md)

<!---HONumber=AcomDC_0810_2016---->