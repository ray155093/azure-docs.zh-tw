---
title: "使用本機執行和 Azure Data Lake U-SQL SDK 對 U-SQL 作業進行測試和偵錯 | Microsoft Docs"
description: "了解如何使用 Azure Data Lake Tools for Visual Studio 和 Azure Data Lake U-SQL SDK 在本機工作站上對 U-SQL 作業進行測試和偵錯。"
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 9d088a3afd8830c80462099cc869f1493d09fb41
ms.openlocfilehash: e0385315434ac7a10e121f88469aaaec45160b86


---
# <a name="test-and-debug-u-sql-jobs-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>使用本機執行和 Azure Data Lake U-SQL SDK 對 U-SQL 作業進行測試和偵錯

了解如何使用 Azure Data Lake Tools for Visual Studio 和 Azure Data Lake U-SQL SDK 在本機工作站上對 U-SQL 作業進行測試和偵錯。  這兩個本機執行的功能可讓您在工作站上執行 U-SQL 作業，如同您可以在 Azure Data Lake 服務中執行一樣。 這些功能可節省您對 U-SQL 作業進行測試和偵錯的時間。

必要條件： 

- Azure Data Lake Analytics 帳戶。 請參閱[開始使用 Azure Data Lake Analytics ](data-lake-analytics-get-started-portal.md)。 
- Azure Data Lake Tools for Visual Studio。  請參閱[使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。 
- U-SQL 指令碼開發經驗。 請參閱[開始使用 Azure Data Lake Analytics ](data-lake-analytics-get-started-portal.md)。 


## <a name="understand-data-root-and-file-path"></a>了解資料根目錄和檔案路徑

本機執行和 U-SQL SDK 兩者都需要資料根資料夾。 資料根目錄是本機計算帳戶的「本機存放區」。 它就相當於 Azure 中 Data Lake Analytics 帳戶的 Data Lake Store 帳戶。 切換到不同的資料根資料夾，就如同切換到不同的存放區帳戶。 如果您想要存取不同資料根資料夾的常用共用資料，您必須在指令碼中使用絕對路徑，或在這個資料根資料夾下建立檔案系統符號連結 (例如，NTFS 的 mklink) 指向共用資料。 

資料根資料夾是用來︰

- 儲存中繼資料，包括資料庫、資料表，TVF、組件等等。
- 查詢在 U-SQL 中定義為相對路徑的輸入和輸出路徑。 使用相對路徑能夠更容易將 U-SQL 專案部署至 Azure。

您可以在 U-SQL 指令碼中使用相對路徑和本機絕對路徑，相對路徑是相對於指定資料根資料夾的路徑。 建議使用 "/" 做為路徑分隔符號，讓您的指令碼與伺服器端相容。 以下是一些相對路徑及其對等絕對路徑的範例。 在這些範例中，"C:\LocalRunDataRoot" 是資料根目錄︰

|相對路徑|絕對路徑|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>從 Visual Studio 使用本機執行

Data Lake Tools for Visual Studio 提供 Visual Studio 中的 U-SQL 本機執行體驗。 您可以使用這項功能︰

- 在本機執行 U-SQL 指令碼以及 C# 組件。
- 在本機對 C# 組件進行偵錯。
- 從伺服器總管建立、檢視和刪除 U-SQL 目錄 (本機資料庫、組件、結構描述和資料表)。 從伺服器總管中也可以找到本機目錄。

    ![Data Lake Tools for Visual Studio 本機執行的本機目錄](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Data Lake Tools 安裝程式會建立 "C:\LocalRunRoot" 資料夾，做為預設的資料根資料夾。 預設的本機執行平行處理原則是 1。 

### <a name="to-configure-local-run-in-visual-studio"></a>在 Visual Studio 中設定本機執行

1. 開啟 Visual Studio。
2. 開啟 [伺服器總管]。
3. 展開 [Azure]、[Data Lake Analytics]。
4. 按一下 [Data Lake] 功能表，然後按一下 [選項和設定]。 
5. 在左邊的樹狀目錄中，展開 [Azure Data Lake]，然後展開 [一般]。

    ![Data Lake Tools for Visual Studio 本機執行的組態設定](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

必須要有 Visual Studio U-SQL 專案才能執行本機執行。 這與從 Azure 執行 U-SQL 指令碼不同。

### <a name="to-run-a-u-sql-script-locally"></a>在本機執行 U-SQL 指令碼
1. 從 Visual Studio 開啟您的 U-SQL 專案。   
2. 在 [方案總管] 中的 U-SQL 指令碼上按一下滑鼠右鍵，然後按一下 [提交指令碼]。 選取 [(本機)] 做為要在本機執行指令碼的 Analytics 帳戶。
3. 您也可以按一下指令碼視窗頂端的 [(本機)] 帳戶，然後按一下 [提交] (或使用 **CTRL + F5** 快速鍵)。

    ![Data Lake Tools for Visual Studio 本機執行的提交作業](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-data-lake-u-sql-sdk"></a>從 Data Lake U-SQL SDK 使用本機執行
 
除了使用 Visual Studio 在本機執行 U-SQL 指令碼之外，您也可以利用 Azure Data Lake U-SQL SDK，使用命令列和程式設計介面在本機執行 U-SQL 指令碼，讓您能夠調整 U-SQL 本機測試。

### <a name="install-the-sdk"></a>安裝 SDK

Data Lake U-SQL SDK 需要下列相依性︰

- [Microsoft .Net Framework 4.6 或更新版本](https://www.microsoft.com/en-us/download/details.aspx?id=17851)。
- Microsoft Visual C++ 14 和 Windows SDK 10.0.10240.0 或更新版本。 有 2 個方式可以取得這個項目︰

    - 安裝 Visual Studio ([Visual Studio Community 版本](https://developer.microsoft.com/downloads/vs-thankyou))。 在 [Program Files] 資料夾下應該會有 "\Windows Kits\10" 資料夾，例如 "C:\Program Files (x86)\Windows Kits\10\"；您也應該會在 "\Windows Kits\10\Lib" 下找到 Windows 10 SDK 版本。 如果您沒有看到這些資料夾，請重新安裝 Visual Studio，並確定在安裝時核取了 [Windows 10 SDK]。 U-SQL 本機編譯器指令碼會透過這種方式自動尋找這些相依性。

    ![Data Lake Tools for Visual Studio 本機執行的 Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)
 
    - 或者也可以安裝 [Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)。 預先封裝的 VC++ 和 Windows SDK 檔案位於 C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK。 在此情況下，U-SQL 本機編譯器就無法自動找到相依性，您必須為它指定 CppSDK 路徑。 您可以將檔案複製到另一個位置，或直接使用它。 然後，您可以選擇對目錄設定環境變數 "SCOPE_CPP_SDK"，或是在本機執行輔助應用程式的命令列中對此目錄指定 "-CppSDK" 引數。 

安裝完 SDK 之後，您必須執行下列設定步驟︰

- 設定 **SCOPE_CPP_SDK** 環境變數

    如果您是安裝 Data Lake Tools for Visual Studio 而取得 Microsoft Visual C++ 和 Windows SDK，請確認您有下列資料夾︰

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    定義一個名為 "SCOPE_CPP_SDK" 的新環境變數指向此目錄。 或將資料夾複製到其他位置，並依同樣方式指定 "SCOPE_CPP_SDK"。

    除了設定環境變數，您也可以在使用命令列時指定 "-CppSDK" 引數。 這個引數會覆寫預設的 CppSDK 環境變數。 

- 設定 **LOCALRUN_DATAROOT** 環境變數

    定義一個名為 "LOCALRUN_DATAROOT" 的新環境變數指向資料根目錄。 

    除了設定環境變數，您也可以在使用命令列時對資料根目錄路徑指定 "-DataRoot" 引數。 這個引數會覆寫預設的 Data Root 環境變數。 而且您必須將這個引數加入您要執行的每個命令列，這樣才能為所有作業使用相同新覆寫的資料根目錄。 

### <a name="using-the-sdk-from-command-line"></a>從命令列使用 SDK

#### <a name="the-command-line-interface-of-the-helper-application"></a>輔助應用程式的命令列介面

SDK 的 LocalRunHelper.exe 是命令列輔助應用程式，提供最常使用的本機執行函式的介面。 而且請注意，命令和引數參數都區分大小寫。 若要叫用此應用程式︰

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

不使用引數執行 "LocalRunHelper.exe"，或使用 **help** 參數顯示說明資訊︰

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

在說明資訊中︰ 

-  **Command** 提供命令的名稱。  
-  **Required Argument** 列出必須提供的引數。  
-  **Optional Argument** 列出選擇性的引數，有預設值。  選擇性的布林引數沒有參數，如果出現參數則表示其預設值的負值。

#### <a name="return-value-and-logging"></a>傳回值和記錄

輔助應用程式在成功的情況會傳回 **0**，在失敗的情況會傳回 **-1**。 根據預設，協助程式會將所有訊息輸出到目前的主控台。  不過，大部分的命令都支援 **-MessageOut path_to_log_file** 選擇性引數，將輸出重新導向至記錄檔。


### <a name="the-sdk-usage-samples"></a>SDK 使用範例

#### <a name="compile-and-run"></a>編譯和執行

"run" 命令用來編譯指令碼，然後執行編譯的結果。 其命令列引數結合 "compile" 和 "run" 的引數。

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

下列是一個範例：

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

除了一起結合 "compile" 和 "run"，您可以分別編譯和執行編譯的可執行檔。 

#### <a name="compile-u-sql-script"></a>編譯 U-SQL 指令碼

"compile" 命令用來將 U-SQL 指令碼編譯為可執行檔。 

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

您可以在附錄中的工作目錄 (-WorkDir)，深入了解編譯的選擇性引數。

|引數|說明|
|--------|-----------|
|-CppSDK [預設值 '']|CppSDK 目錄|
|-DataRoot [預設值 '']|資料和中繼資料的資料根目錄，預設為 'LOCALRUN_DATAROOT' 環境變數|
|-MessageOut [預設值 '']|將主控台上的訊息傾印成檔案|
|-Shallow [預設值 'False']|淺層編譯，只對指令碼進行語法檢查並傳回。|
|-WorkDir [預設值 'D:\localrun\t\ScopeWorkDir']|供編譯器使用和輸出的目錄，請在「附錄 – 工作目錄」中參閱詳細資訊。|

組件和程式碼後置的選擇性引數︰

|引數|說明|
|--------|-----------|
|-CodeBehind [預設值 'False']|具有 .cs 程式碼後置的指令碼將編譯並自動註冊為 UDO 物件|
|-References [預設值 '']|列出程式碼後置額外的參考組件或資料檔案的路徑，以 ';' 分隔|
|-UseDatabase [預設值 'master']|供程式碼後置暫時註冊組件使用的資料庫|
|-UdoRedirect [預設值 'False']|產生 Udo 組件重新導向設定，告訴 .Net 執行階段在呼叫 UDO 時先從編譯的輸出目錄中探查相依的組件|

這裡有一些使用範例：

編譯 U-SQL 指令碼：

    LocalRunHelper compile -Script d:\test\test1.usql

編譯 U-SQL 指令碼並設定資料根資料夾，請注意，這會覆寫設定的環境變數。

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

編譯 U-SQL 指令碼並設定工作目錄、參考組件和資料庫。

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-result"></a>執行編譯的結果

"execute" 命令用來執行編譯的結果。   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

選擇性的引數︰

|引數|說明|
|--------|-----------|
|-DataRoot [預設值 '']|中繼資料執行的資料根目錄，預設為 'LOCALRUN_DATAROOT' 環境變數|
|-MessageOut [預設值 '']|將主控台上的訊息傾印成檔案|
|-Parallel [預設值 '1']|使用指定的平行處理原則層級執行產生的本機執行步驟|
|-Verbose [預設值 'False']|顯示詳細的執行階段輸出|

以下是使用範例︰

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="using-the-sdk-with-programming-interface"></a>透過程式設計介面使用 SDK

程式設計介面都位於 “Microsoft.Analytics.LocalRun” 組件中，您可以透過此組件整合 U-SQL SDK 的功能和 C# 測試架構，以調整 U-SQL 指令碼的本機測試，此功能的新文件即將推出。 如需有關介面的詳細資訊，請參閱附錄。

## <a name="appendix"></a>附錄

### <a name="working-directory"></a>工作目錄

在本機執行 U-SQL 指令碼時，會在編譯期間建立一個工作目錄。  除了編譯輸出，本機執行所需的執行階段檔案也會陰影複製到這個工作目錄。   如果命令列中未指定 **-WorkDir** 引數，將會在目前的目錄下建立預設的工作目錄 “ScopeWorkDir”。 工作目錄下的檔案顯示如下。

|目錄/檔案|定義|說明|
|--------------|----------|-----------|
|ScopeWorkDir|工作目錄|根資料夾|
|C6A101DDCB470506|執行階段版本的雜湊字串|陰影複製本機執行所需的執行階段檔案|
|\.\Script_66AE4909AA0ED06C|指令碼名稱 + 指令碼路徑的雜湊字串|編譯輸出和執行步驟記錄|
|\.\.\\_\_script\_\_.abr|編譯器輸出|代數檔案|
|\.\.\\_\_ScopeCodeGen\_\_.*|編譯器輸出|產生的 Managed 程式碼|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|編譯器輸出|產生的原生程式碼|
|\.\.\referenced_assemblies|組件參考|參考組件檔案|
|\.\.\deployed_resources|資源部署|資源部署檔案|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|執行記錄檔|執行步驟的記錄檔|

### <a name="programming-interfaces-for-azure-data-lake-u-sql-sdk"></a>Azure Data Lake U-SQL SDK 的程式設計介面

程式設計介面都位於 “Microsoft.Analytics.LocalRun” 組件中。

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
編譯組態參數類別

**建構函式**

public Configuration(字串 rootPath)

|參數|類型|說明|
|---------|----|-----------|
|rootPath|System.String|工作內容目前目錄的路徑。 如果未設定 WorkingDirectory，預設的工作目錄為 rootPath + "ScopeWorkDir"。|

**屬性**

|名稱|說明|
|----|-----------|
|CppSDK|可找到 Cpp SDK 的位置，預設為使用系統預設組態 |
|DataDirectory|資料表和組件以及輸入輸出資料的儲存位置，預設為 ScopeWorkDir\DataDir |
|GenerateUdoRedirect|是否要產生載入重新導向覆寫設定的組件|
|WorkingDirectory|編譯器的工作目錄，若未設定則預設為 ScopeWorkDir|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
U-SQL 本機編譯器類別

**建構函式**

public LocalCompiler(組態 configuration)

|參數|類型|說明|
|---------|----|-----------|
|組態|Microsoft.Analytics.LocalRun.Configuration||

**方法**

public bool Compile( 字串 script, 字串 filePath, 布林值 shallow, 輸出 CommonCompileResult 結果 )

|參數|類型|說明|
|---------|----|-----------|
|script|System.String|輸入指令碼的字串|
|filePath|System.String|指令碼檔案的路徑，設定為 null 表示使用預設值|
|shallow|System.Boolean|淺層編譯 (語法驗證) 或完整編譯|
|結果|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|詳細的編譯結果|
|Return Value|System.Boolean|true︰編譯沒有任何嚴重錯誤。 <br>false︰編譯有嚴重錯誤|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
U-SQL 本機執行器類別

**建構函式**

public LocalRunner( 字串 algebraFilePath, 字串 dataRoot, 動作<string> postMessage = null )

|參數|類型|說明|
|---------|----|-----------|
|algebraFilePath|System.String|代數檔案的路徑|
|dataRoot|System.String|DataRoot 的路徑|
|postMessage (選擇性)|System.Action<String>|進度的記錄處理常式|

public LocalRunner( 字串 algebraFilePath, 字串 dataRoot, 字串 cachePath, 字串 runtimePath, 字串 tempPath, 字串 logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, 物件 eventContext, Action<string> postMessage = null )

|參數|類型|說明|
|---------|----|-----------|
|algebraFilePath|System.String|代數檔案的路徑|
|dataRoot|System.String|DataRoot 的路徑|
|cachePath|System.String|編譯結果的目錄路徑，設為 null 表示使用代數檔案所在的預設位置|
|runtimePath|System.String|陰影複製之執行階段的目錄路徑，設定為 null 表示使用 cachePath 之父目錄的預設位置|
|tempPath|System.String|暫存路徑，僅供內部使用，設為 null|
|logPath|System.String|執行記錄檔將寫入的路徑，設為 null 表示使用 cachePath 為預設值|
|execEventHandler|System. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|執行狀態變更事件通知處理常式|
|eventContext|System. Object|事件處理常式的內容|
|postMessage (選擇性)|System.Action<String>|進度的記錄處理常式|

**屬性**

|名稱|說明|
|----|-----------|
|AlgebraPath|代數檔案的路徑|
|CachePath|產生的二進位檔所在位置的編譯器結果快取路徑|
|CompletedSteps|完成的步驟數目|
|DataRoot|中繼資料的 DataRoot|
|LastErrorMessage|(繼承自 ExecutionStatusBase)|
|LogPath|記錄檔案儲存 Setter 會建立目錄 (如果不存在)。不會清除先前建立的記錄檔路徑|
|OutputHeader|在文字輸出中傾印結構描述標頭|
|Parallelism|平行處理原則，預設值為邏輯處理器 -1。在開始後變更此值將會導致例外狀況|
|Progress|0 到 100% 級別的執行進度|
|RuntimePath|執行階段檔案所在的位置，必須是由編譯器進行陰影複製時 CachePath 上方的一個目錄|
|Status|執行狀態 <br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Initialized, // 初始化 <br>Running,     // 執行中，WaitOne 只檢查此狀態的事件 <br>Success,     // 順利完成 <br>Error,       // 失敗 <br>}|
|TotalSteps|要執行的步驟總數，只有在建置頂點 DAG 之後才會有有效值|
|Verbose|執行期間的詳細資訊|

**方法**

|方法|說明|
|------|-----------|
|Cancel()|取消執行代數 <br>傳回值 <br>型別：布林值 <br>false︰因為發生錯誤無法取消，查看 LastErrorMessage 了解詳細資訊|
|Start()|開始執行代數 <br>傳回值 <br>型別：布林值 <br>false︰因為發生錯誤無法開始，查看 LastErrorMessage 了解詳細資訊|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|等候完成，請參閱 WaitHandle.WaitOne|
|Dispose()||


## <a name="see-also"></a>另請參閱

* 若要取得資料湖分析概觀，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。
* 若要開始開發 U-SQL 應用程式，請參閱 [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，請參閱 [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)。
* 針對管理工作，請參閱 [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。
* 若要記錄診斷資訊，請參閱 [為 Azure Data Lake Analytics 存取診斷記錄檔](data-lake-analytics-diagnostic-logs.md)
* 若要了解更複雜的查詢，請參閱 [使用 Azure 資料湖分析來分析網站記錄檔](data-lake-analytics-analyze-weblogs.md)。
* 若要檢視作業詳細資料，請參閱[針對 Azure Data Lake Analytics 作業使用作業瀏覽器和作業檢視](data-lake-analytics-data-lake-tools-view-jobs.md)
* 若要檢視使用頂點執行檢視，請參閱[在適用於 Visual Studio 的 Data Lake 工具中使用頂點執行檢視](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)


<!--HONumber=Nov16_HO4-->


