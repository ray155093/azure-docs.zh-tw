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
ms.sourcegitcommit: c43fb7b513136bb3ea964419d68f58585bba48fd
ms.openlocfilehash: 348971e07169ae9c155753c7943124e96e6e9597


---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>使用本機執行和 Azure Data Lake U-SQL SDK 對 U-SQL 作業進行測試和偵錯

您可以使用 Azure Data Lake Tools for Visual Studio 和 Azure Data Lake U-SQL SDK，和在 Azure Data Lake 服務中一樣地在工作站上執行 U-SQL 作業。 這兩個本機執行功能可節省您對 U-SQL 作業進行測試和偵錯的時間。

必要條件：

- Azure Data Lake Analytics 帳戶。 請參閱[開始使用 Azure Data Lake Analytics ](data-lake-analytics-get-started-portal.md)。
- 適用於 Visual Studio 的 Azure Data Lake 工具。 請參閱[使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
- U-SQL 指令碼開發經驗。 請參閱[開始使用 Azure Data Lake Analytics ](data-lake-analytics-get-started-portal.md)。


## <a name="understand-the-data-root-folder-and-the-file-path"></a>了解資料根資料夾和檔案路徑

本機執行和 U-SQL SDK 兩者都需要資料根資料夾。 資料根資料夾是本機計算帳戶的「本機存放區」。 它就相當於 Data Lake Analytics 帳戶的 Azure Data Lake Store 帳戶。 切換到不同的資料根資料夾，就如同切換到不同的存放區帳戶。 如果您想要存取具有不同資料根資料夾的常用共用資料，便必須在指令碼中使用絕對路徑。 或者，您可以在資料根資料夾下建立檔案系統符號連結 (例如 NTFS 上的 **mklink**)，來指向共用資料。

資料根資料夾是用來︰

- 儲存中繼資料，包括資料庫、資料表，資料表值函式 (TVF)，以及組件。
- 查詢在 U-SQL 中定義為相對路徑的輸入和輸出路徑。 使用相對路徑能夠更容易將 U-SQL 專案部署至 Azure。

您可以在 U-SQL 指令碼中使用相對路徑和本機絕對路徑。 相對路徑是相對於指定的資料根資料夾路徑。 我們建議您使用 "/" 做為路徑分隔符號，讓您的指令碼與伺服器端相容。 以下是一些相對路徑及其對等絕對路徑的範例。 在這些範例中，C:\LocalRunDataRoot 是資料根資料夾。

|相對路徑|絕對路徑|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>從 Visual Studio 使用本機執行

Data Lake Tools for Visual Studio 提供 Visual Studio 中的 U-SQL 本機執行體驗。 透過使用這項功能，您可以︰

- 在本機執行 U-SQL 指令碼及 C# 組件。
- 在本機對 C# 組件進行偵錯。
- 從伺服器總管建立、檢視和刪除 U-SQL 目錄 (本機資料庫、組件、結構描述和資料表)。 您也可以從伺服器總管中找到本機目錄。

    ![Data Lake Tools for Visual Studio 本機執行的本機目錄](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Data Lake Tools 安裝程式會建立 C:\LocalRunRoot 資料夾，做為預設的資料根資料夾。 預設的本機執行平行處理原則是 1。

### <a name="to-configure-local-run-in-visual-studio"></a>在 Visual Studio 中設定本機執行

1. 開啟 Visual Studio。
2. 開啟 [伺服器總管]。
3. 展開 [Azure]  >  [Data Lake Analytics]。
4. 按一下 [Data Lake] 功能表，然後按一下 [選項和設定]。
5. 在左邊的樹狀目錄中，展開 [Azure Data Lake]，然後展開 [一般]。

    ![Data Lake Tools for Visual Studio 本機執行的組態設定](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

必須要有 Visual Studio U-SQL 專案才能執行本機執行。 這與從 Azure 執行 U-SQL 指令碼不同。

### <a name="to-run-a-u-sql-script-locally"></a>在本機執行 U-SQL 指令碼
1. 從 Visual Studio 開啟您的 U-SQL 專案。   
2. 在 [方案總管] 中的 U-SQL 指令碼上按一下滑鼠右鍵，然後按一下 [提交指令碼]。
3. 選取 [(本機)] 做為要在本機執行指令碼的 Analytics 帳戶。
您也可以按一下指令碼視窗頂端的 [(本機)] 帳戶，然後按一下 [提交] (或使用 Ctrl + F5 鍵盤快速鍵)。

    ![Data Lake Tools for Visual Studio 本機執行的提交作業](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>從 Data Lake U-SQL SDK 使用本機執行

除了使用 Visual Studio 在本機執行 U-SQL 指令碼之外，您可以利用 Azure Data Lake U-SQL SDK，使用命令列和程式設計介面在本機執行 U-SQL 指令碼。 這能讓您調整 U-SQL 本機測試。

### <a name="install-the-sdk"></a>安裝 SDK

Data Lake U-SQL SDK 需要下列相依性︰

- [Microsoft .NET Framework 4.6 或更新版本](https://www.microsoft.com/en-us/download/details.aspx?id=17851)。
- Microsoft Visual C++ 14 和 Windows SDK 10.0.10240.0 或更新版本。 有兩種方式可取得此項目：

    - 安裝 [Visual Studio Community 版本](https://developer.microsoft.com/downloads/vs-thankyou)。 在 Program Files 資料夾下應該會有 \Windows Kits\10 資料夾，例如 C:\Program Files (x86)\Windows Kits\10\.。您也會在 \Windows Kits\10\Lib 下找到 Windows 10 SDK 版本。 如果您看不見這些資料夾，請重新安裝 Visual Studio，並務必在安裝期間選取 Windows 10 SDK。 U-SQL 本機編譯器指令碼會自動尋找這些相依性。

    ![Data Lake Tools for Visual Studio 本機執行的 Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - 安裝 [Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)。 您可以在 C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK 找到預先封裝的 Visual C++ 和 Windows SDK 檔案。 在此情況下，U-SQL 本機編譯器就無法自動找到相依性。 您必須為它指定 CppSDK 路徑。 您可以將檔案複製到另一個位置，或直接使用它。 然後，您可以選擇對目錄設定環境變數 **SCOPE_CPP_SDK**，或是在本機執行輔助應用程式的命令列中對此目錄指定 **-CppSDK** 引數。

安裝完 SDK 之後，您必須執行下列設定步驟︰

- 設定 **SCOPE_CPP_SDK** 環境變數。

    如果您是透過安裝 Data Lake Tools for Visual Studio 來取得 Microsoft Visual C++ 和 Windows SDK，請確認您有下列資料夾︰

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    定義一個名為 **SCOPE_CPP_SDK** 的新環境變數來指向此目錄。 或將資料夾複製到其他位置，並依同樣方式將 **SCOPE_CPP_SDK** 指定為該資料夾。

    除了設定環境變數，您可以在使用命令列時指定 **-CppSDK** 引數。 這個引數會覆寫預設的 CppSDK 環境變數。

- 設定 **LOCALRUN_DATAROOT** 環境變數。

    定義一個名為 **LOCALRUN_DATAROOT** 的新環境變數指向資料根目錄。

    除了設定環境變數，您可以在使用命令列時對資料根目錄路徑指定 **-DataRoot** 引數。 這個引數會覆寫預設的資料根環境變數。 您必須將這個引數加入您要執行的每個命令列，以覆寫所有作業的預設資料根環境變數。

### <a name="use-the-sdk-from-the-command-line"></a>從命令列使用 SDK

#### <a name="command-line-interface-of-the-helper-application"></a>輔助應用程式的命令列介面

在 SDK 中，LocalRunHelper.exe 是命令列輔助應用程式，提供最常使用的本機執行函式的介面。 請注意，命令和引數參數都區分大小寫。 若要叫用此應用程式︰

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

不使用引數執行 LocalRunHelper.exe，或使用 **help** 參數顯示說明資訊︰

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
-  **Optional Argument** 列出選擇性的引數，並具有預設值。  選擇性的布林引數沒有參數，如果出現參數則表示其預設值的負值。

#### <a name="return-value-and-logging"></a>傳回值和記錄

如果成功，輔助應用程式會傳回 **0**；如果失敗，則會傳回 **-1**。 根據預設，協助程式會將所有訊息傳送到目前的主控台。 不過，大部分的命令都支援 **-MessageOut path_to_log_file** 選擇性引數，該引數會將輸出重新導向至記錄檔。


### <a name="sdk-usage-samples"></a>SDK 使用範例

#### <a name="compile-and-run"></a>編譯和執行

**run** 命令用來編譯指令碼，然後執行編譯的結果。 其命令列引數結合 **compile** 和 **run** 的引數。

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

以下是範例：

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

除了一起結合 **compile** 和 **run**，您可以分別編譯和執行編譯的可執行檔。

#### <a name="compile-a-u-sql-script"></a>編譯 U-SQL 指令碼

**compile** 命令用來將 U-SQL 指令碼編譯為可執行檔。

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

下列為編譯的選擇性引數：

|引數|說明|
|--------|-----------|
|-CppSDK [預設值 '']|CppSDK 目錄。|
|-DataRoot [預設值 '']|資料和中繼資料的資料根。 它預設為 **LOCALRUN_DATAROOT** 環境變數。|
|-MessageOut [預設值 '']|將主控台上的訊息傾印成檔案。|
|-Shallow [預設值 'False']|淺層編譯。 它只對指令碼進行語法檢查並傳回。|
|-WorkDir [預設值 'D:\localrun\t\ScopeWorkDir']|編譯器使用方式和輸出的目錄。 如需詳細資訊，請參閱附錄中的＜工作目錄＞。|

下列為組件和程式碼後置的選擇性引數︰

|引數|說明|
|--------|-----------|
|-CodeBehind [預設值 'False']|指令碼具有 .cs 程式碼後置的指示器，將會編譯並自動註冊為使用者定義物件 (UDO)|
|-References [預設值 '']|列出程式碼後置額外的參考組件或資料檔案的路徑，以 ';' 分隔|
|-UseDatabase [預設值 'master']|供程式碼後置暫時註冊組件使用的資料庫|
|-UdoRedirect [預設值 'False']|UDO 組件重新導向設定，告訴 .NET 執行階段在呼叫 UDO 時先從編譯的輸出目錄中探查相依的組件|

這裡有一些使用範例。

編譯 U-SQL 指令碼：

    LocalRunHelper compile -Script d:\test\test1.usql

編譯 U-SQL 指令碼並設定資料根資料夾。 請注意，這將會覆寫設定環境變數。

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

編譯 U-SQL 指令碼並設定工作目錄、參考組件和資料庫：

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>執行編譯的結果

**execute** 命令用來執行編譯的結果。   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

下列為選擇性引數：

|引數|說明|
|--------|-----------|
|-DataRoot [預設值 '']|中繼資料執行的資料根。 它預設為 **LOCALRUN_DATAROOT** 環境變數。|
|-MessageOut [預設值 '']|將主控台上的訊息傾印成檔案。|
|-Parallel [預設值 '1']|使用指定的平行處理原則層級執行產生本機執行步驟的指示器。|
|-Verbose [預設值 'False']|顯示詳細執行階段輸出的指示器。|

以下是使用範例︰

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="use-the-sdk-with-programming-interfaces"></a>透過程式設計介面使用 SDK

程式設計介面都位於 Microsoft.Analytics.LocalRun 組件中。 您可以使用它們來整合 U-SQL SDK 的功能性及 C# 測試架構，以調整您的 U-SQL 指令碼本機測試。 如需有關介面的詳細資訊，請參閱附錄。

## <a name="appendix"></a>附錄

### <a name="working-directory"></a>工作目錄

當您於本機執行 U-SQL 指令碼時，會在編譯期間建立一個工作目錄。 除了編譯輸出，本機執行所需的執行階段檔案會陰影複製到這個工作目錄。 如果命令列中未指定 **-WorkDir** 引數，將會在目前的目錄下建立預設的工作目錄 ScopeWorkDir。 工作目錄下的檔案如下：

|目錄/檔案|定義|說明|
|--------------|----------|-----------|
|ScopeWorkDir|工作目錄|根資料夾|
|C6A101DDCB470506|執行階段版本的雜湊字串|陰影複製本機執行所需的執行階段檔案|
|\.\Script_66AE4909AA0ED06C|指令碼名稱加上指令碼路徑的雜湊字串|編譯輸出和執行步驟記錄|
|\.\.\\_\_script\_\_.abr|編譯器輸出|代數檔案|
|\.\.\\_\_ScopeCodeGen\_\_.*|編譯器輸出|產生的 Managed 程式碼|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|編譯器輸出|產生的原生程式碼|
|\.\.\referenced_assemblies|組件參考|參考組件檔案|
|\.\.\deployed_resources|資源部署|資源部署檔案|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|執行記錄檔|執行步驟的記錄檔|

### <a name="programming-interfaces-for-the-azure-data-lake-u-sql-sdk"></a>Azure Data Lake U-SQL SDK 的程式設計介面

程式設計介面都位於 Microsoft.Analytics.LocalRun 組件中。

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
Microsoft.Analytics.LocalRun.Configuration 為編譯組態參數類別。

**建構函式**

public Configuration(字串 rootPath)

|參數|類型|說明|
|---------|----|-----------|
|rootPath|System.String|工作內容目前目錄的路徑。 如果未設定 WorkingDirectory，預設的工作目錄為 rootPath 加上 ScopeWorkDir。|

**屬性**

|名稱|說明|
|----|-----------|
|CppSDK|CppSDK 的位置 (在不是系統預設組態的情況下)。 |
|DataDirectory|儲存資料表、組件及輸入/輸出資料的位置。 預設為 ScopeWorkDir\DataDir。 |
|GenerateUdoRedirect|是否要產生組件載入重新導向覆寫設定的指示器。|
|WorkingDirectory|編譯器的工作目錄。 預設為 ScopeWorkDir。|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
Microsoft.Analytics.LocalRun.LocalCompiler 為 U-SQL 本機編譯器類別。

**建構函式**

public LocalCompiler(組態 configuration)

|參數|類型|說明|
|---------|----|-----------|
|組態|Microsoft.Analytics.LocalRun.Configuration||

**方法**

public bool Compile( 字串 script, 字串 filePath, 布林值 shallow, 輸出 CommonCompileResult 結果 )

|參數|類型|說明|
|---------|----|-----------|
|script|System.String|輸入指令碼的字串。|
|filePath|System.String|指令碼檔案的路徑。 設定為 null 以使用預設值。|
|shallow|System.Boolean|淺層編譯 (語法驗證) 或完整編譯。|
|結果|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|詳細的編譯結果。|
|傳回值|System.Boolean|True︰編譯沒有任何嚴重錯誤。 <br>False︰編譯有嚴重錯誤。|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
Microsoft.Analytics.LocalRun.LocalRunner : IDisposable 為 U-SQL 本機執行器類別。

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
|algebraFilePath|System.String|代數檔案的路徑。|
|dataRoot|System.String|DataRoot 的路徑。|
|cachePath|System.String|編譯結果目錄的路徑。 設定為 null 以使用預設值，也就是代數檔案所在的位置。|
|runtimePath|System.String|陰影複製執行階段目錄的路徑。 設定為 null 以使用預設值，也就是 cachePath 上層目錄所在的位置。|
|tempPath|System.String|暫存路徑，僅供內部使用。 設定為 null。|
|logPath|System.String|執行記錄檔會寫入的路徑。 設定為 null 以使用 cachePath 做為預設值。|
|execEventHandler|System. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|執行狀態變更的事件通知處理常式。|
|eventContext|System. Object|事件處理常式的內容。|
|postMessage (選擇性)|System.Action<String>|進度的記錄處理常式。|

**屬性**

|名稱|說明|
|----|-----------|
|AlgebraPath|代數檔案的路徑。|
|CachePath|產生的二進位檔所在位置的編譯器結果快取路徑。|
|CompletedSteps|完成的步驟數目。|
|DataRoot|中繼資料的 DataRoot。|
|LastErrorMessage|(繼承自 ExecutionStatusBase)|
|LogPath|記錄檔案儲存位置。 Setter 會在目錄不存在的情況下建立它。 先前建立的記錄檔路徑將不會清除。|
|OutputHeader|在文字輸出中傾印結構描述標頭。|
|Parallelism|平行處理原則。 預設為邏輯處理器：1。 於開始後變更此設定將導致例外狀況。|
|Progress|0 到 100% 級別的執行進度。|
|RuntimePath|執行階段檔案所在的位置。 它必須是由編譯器進行陰影複製時 CachePath 上方的一個目錄。|
|狀態|執行狀態。 <br><br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Initialized, // 已初始化。 <br>Running,     // 它正在執行。  WaitOne 只會檢查處於此狀態的事件。 <br>Success,     // 它已順利完成。 <br>Error,       // 它已失敗。 <br>}|
|TotalSteps|要執行的步驟總數。 只有在建置頂點 DAG 之後才會有有效值。|
|Verbose|執行期間的詳細資訊。|

**方法**

|方法|說明|
|------|-----------|
|Cancel()|取消執行代數。 <br><br>傳回值的類型為布林值。 <br><br>傳回值為 false︰因為發生錯誤無法取消，請查看 LastErrorMessage 以了解詳細資料。|
|Start()|開始執行代數。 <br><br>傳回值的類型為布林值。 <br><br>傳回值為 false︰因為發生錯誤無法開始，請查看 LastErrorMessage 以了解詳細資料。|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|等候完成。 參考 WaitHandle.WaitOne。|
|Dispose()||


## <a name="next-steps"></a>後續步驟

* 若要取得資料湖分析概觀，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。
* 若要開始開發 U-SQL 應用程式，請參閱 [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，請參閱 [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)。
* 針對管理工作，請參閱 [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。
* 若要記錄診斷資訊，請參閱 [為 Azure Data Lake Analytics 存取診斷記錄檔](data-lake-analytics-diagnostic-logs.md)。
* 若要了解更複雜的查詢，請參閱 [使用 Azure Data Lake Analytics 來分析網站記錄檔](data-lake-analytics-analyze-weblogs.md)。
* 若要檢視作業詳細資料，請參閱[針對 Azure Data Lake Analytics 作業使用作業瀏覽器和作業檢視](data-lake-analytics-data-lake-tools-view-jobs.md)。
* 若要使用頂點執行檢視，請參閱[在 Data Lake Tools for Visual Studio 中使用頂點執行檢視](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)。



<!--HONumber=Dec16_HO1-->


