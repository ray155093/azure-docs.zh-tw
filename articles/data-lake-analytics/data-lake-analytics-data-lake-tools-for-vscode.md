---
title: "Azure Data Lake Tools - 使用 Azure Data Lake Tools for Visual Studio Code | Microsoft Docs"
description: "了解如何使用 Azure Data Lake Tools for Visual Studio Code 來建立、測試和執行 U-SQL 指令碼。 "
Keywords: "VScode,Azure Data Lake Tools,本機執行,本機偵錯,預覽儲存體檔案,上傳至儲存體路徑"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 0ed3d7a0057eb446b3e1d16019ac74c641ae3138
ms.contentlocale: zh-tw
ms.lasthandoff: 07/17/2017

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>使用 Azure Data Lake Tools for Visual Studio Code

了解如何使用 Azure Data Lake Tools for Visual Studio Code (VSCode) 來建立、測試和執行 U-SQL 指令碼。  下列影片中也涵蓋此資訊︰

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>必要條件

Data Lake Tools 可以安裝在 VSCode 支援的平台上，包括 Windows、Linux 和 MacOS。 您可以找到不同平台的必要條件

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Java SE Runtime Environment version 8 update 77 或更新版本](https://java.com/download/manual.jsp)。 您必須將 java.exe 路徑新增至系統環境變數路徑。  如需指示，請參閱[我要如何設定或變更 Path 系統變數？]( https://www.java.com/download/help/path.xml) 路徑類似於 C:\Program Files\Java\jdk1.8.0_77\jre\bin
    - [.NET Core SDK 1.0.3 或 .NET Core 1.1 執行階段](https://www.microsoft.com/net/download)。
    
- Linux (我們建議使用 Ubuntu 14.04 LTS)

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。 使用下列命令來安裝：

        sudo dpkg -i code_<version_number>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/)。 

        - 執行下列命令更新 deb 套件來源︰

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - 執行命令安裝 mono︰

                sudo apt-get install mono-complete

            > [!NOTE] 
            > 不支援 Mono 4.6。  安裝 4.2.x 之前，您必須先 4.6 版完全解除安裝。  

        - [Java SE Runtime Environment version 8 update 77 或更新版本](https://java.com/download/manual.jsp)。 [這裡]( https://java.com/en/download/help/linux_x64_install.xml)提供相關指示。
        - [.NET Core SDK 1.0.3 或 .NET Core 1.1 執行階段](https://www.microsoft.com/net/download)。
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/)。 
    - [Java SE Runtime Environment version 8 update 77 或更新版本](https://java.com/download/manual.jsp)。 [這裡](https://java.com/en/download/help/mac_install.xml)提供相關指示。
    - [.NET Core SDK 1.0.3 或 .NET Core 1.1 執行階段](https://www.microsoft.com/net/download)。

## <a name="install-the-data-lake-tools"></a>安裝 Data Lake Tools

安裝完必要條件之後，您可以安裝 Data Lake Tools for VSCode。

**安裝 Data Lake Tools**

1. 開啟 **Visual Studio Code**。
2. 按下 **CTRL+P**，然後輸入︰
```
ext install usql-vscode-ext
```
您可以看到一份 Visual Studio 程式碼擴充功能清單。 其中一個是 **Azure Data Lake Tool**。

3. 按一下 [Azure Data Lake Tool] 旁邊的 [安裝]。 幾秒鐘後，[安裝] 按鈕將變為 [重新載入]。
4. 按一下 [重新載入] 以啟用擴充功能。
5. 按一下 [ **確定** ] 以確認。 您會在 [擴充功能] 窗格中看到 Azure Data Lake Tools。
    ![Data Lake Tools for Visual Studio Code 安裝](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>啟動 Azure Data Lake Tools
請建立一個新的 .USQL 檔案或開啟現有的 .USQL 檔案，以啟動擴充功能。 

## <a name="connect-to-azure"></a>連接到 Azure

您必須先連線到 Azure 帳戶，才能在 Azure Data Lake Analytics 中編譯和執行 U-SQL 指令碼。

**連接到 Azure**

1.  按下 **CTRL+SHIFT+P** 開啟命令選擇區。 
2.  輸入 **ADL: Login**。 登入資訊會顯示在輸出窗格中。

    ![Data Lake Tools for Visual Studio Code 命令選擇區](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Data Lake Tools for Visual Studio Code 登入資訊](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. 在登入 URL https://aka.ms/devicelogin 上按住 Ctrl 鍵再按一下滑鼠左鍵，以開啟登入網頁。 複製代碼 G567LX42V 並貼到下面的文字方塊中，然後按一下 [繼續] 來繼續操作。

   ![Data Lake Tools for Visual Studio Code 登入貼上代碼](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  依照網頁上的指示登入。 連線之後，您的 Azure 帳戶名稱會顯示在 VSCode 視窗左下角的狀態列上。 

    > [!NOTE] 
    > 如果您的帳戶已啟用兩個因素，建議使用電話驗證而不是 Pin。

若要登出，請使用命令 **ADL:Logout**

## <a name="list-data-lake-analytics-accounts"></a>列出 Data Lake Analytics 帳戶

若要測試連線，可以列出 Data Lake Analytics 帳戶︰

**列出您的 Azure 訂用帳戶下的 Data Lake Analytics 帳戶**

1. 按下 **CTRL+SHIFT+P** 開啟命令選擇區。
2. 輸入 **ADL: List Accounts**。  帳戶會出現在 [輸出] 窗格中。

## <a name="open-sample-script"></a>開啟範例指令碼

使用「命令選擇區」(**Ctrl+Shift+P**)，然後選擇 [ADL: Open Sample Script]。 它會接著為此範例開啟另一個執行個體。 您也可以在此執行個體上編輯、設定及送出指令碼。

## <a name="work-with-u-sql"></a>使用 U-SQL

您需要開啟 U-SQL 檔案或資料夾以使用 U-SQL。

**開啟 U-SQL 專案的資料夾**

1. 從 Visual Studio Code 中，按一下 [檔案] 功能表，然後按一下 [開啟資料夾]。
2. 指定資料夾，然後按一下 [選取資料夾]。
3. 按一下 [檔案] 功能表，然後按一下 [新增]。 專案中就會加入一個 **Untilted-1** 檔案。
4. 複製以下程式碼並貼到 Untitled-1 檔案中：

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO “/Output/departments.csv”

    這個指令碼會在 /output 資料夾中建立 departments.csv 檔案和一些資料。

5. 在開啟的資料夾中，將檔案儲存為 **myUSQL.usql**。 請注意專案中也會加入 **adltools_settings.json** 組態檔。
4. 開啟 **adltools_settings.json**，並使用下列屬性設定︰

    - 帳戶：在您的 Azure 訂用帳戶下的 Data Lake Analytics 帳戶。
    - 資料庫：您帳戶底下的資料庫。 預設值為 master。
    - 結構描述：您資料庫底下的結構描述。 預設值為 dbo。
    - 選擇性設定︰
        - 優先順序︰優先順序範圍是從 1 到 1000，1 是最高的優先順序。 預設值為 1000。
        - 平行處理原則︰平行處理原則的範圍是從 1 到 150。 預設值為您 ADLA 帳戶中允許的平行處理原則上限。 
        
        > [!NOTE] 
        > 如果設定無效，則會使用預設值。

    ![Data Lake Tools for Visual Studio Code 組態檔](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    編譯和執行 U-SQL 作業需要計算 Data Lake Analytics 帳戶。  編譯和執行 U-SQL 作業之前，您必須先設定電腦帳戶。
    
    儲存組態之後，帳戶|資料庫|結構描述資訊就會顯示在對應之 USQL 檔案左下角的狀態列上。 
 
 

與開啟檔案相比，開啟資料夾可讓您︰

- 使用程式碼後置檔案。  在單一檔案模式中，不支援程式碼後置。
- 使用組態檔。 當您開啟資料夾時，工作資料夾中的指令碼會共用一個組態檔。


U-SQL 指令碼編譯會由 Data Lake Analytics 服務在遠端完成。  當您發出編譯命令時，U-SQL 指令碼會傳送至 Data Lake Analytics 帳戶。 Visual Studio Code 比較晚才會收到編譯結果。 因為是遠端編譯，所以 Visual Studio 程式碼需要資訊才能連線到您在組態檔中的 Data Lake Analytics 帳戶。

**編譯 U-SQL 指令碼**

1. 按下 **CTRL+SHIFT+P** 開啟命令選擇區。 
2. 輸入 **ADL: Compile Script**。 編譯結果會顯示在輸出視窗中。 您可以也在指令碼檔案上按一下滑鼠右鍵，然後按一下 [ADL: Compile Script] 來編譯 U-SQL 作業。 編譯結果會顯示在輸出窗格中。
 

**提交 U-SQL 指令碼**

1. 按下 **CTRL+SHIFT+P** 開啟命令選擇區。 
2. 輸入 **ADL: Submit Job**。  您可以也在指令碼檔案上按一下滑鼠右鍵，然後按一下 [ADL: Submit Job] 來提交 U-SQL 作業。 

提交 U-SQL 作業之後，VSCode 的輸出視窗中會顯示提交記錄檔。 如果提交成功，也會顯示作業 URL。 您可以在網頁瀏覽器中開啟作業 URL 來追蹤即時的作業狀態。

若要啟用輸出作業的詳細資料︰請在 **vscode for u-sql_settings.json** 檔案中設定 ‘jobInformationOutputPath’。
 
## <a name="use-code-behind-file"></a>使用程式碼後置檔案

程式碼後置檔案是與一個 U-SQL 指令碼關聯的 CSharp 檔案。 您可以在程式碼後置檔案中定義指令碼專用的 UDO/UDA/UDT/UDF。 指令碼中可以直接使用 UDO/UDA/UDT/UDF，不需要先註冊組件。 程式碼後置檔案會放在與其對等互連 U-SQL 指令碼檔案相同的資料夾中。 如果指令碼名稱為 xxx.usql，程式碼後置就會被命名為 xxx.usql.cs。 手動刪除該程式碼後置檔案，會停用其相關聯之 U-SQL 指令碼的程式碼後置功能。 如需撰寫 U-SQL 指令碼之客戶程式碼的詳細資訊，請參閱[在 U-SQL 中撰寫和使用自訂程式碼 – 使用者定義函式]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/) (英文)。

若要支援程式碼後置，必須開啟工作資料夾。 

**產生程式碼後置檔案**

1. 開啟來源檔案。 
2. 按下 **CTRL+SHIFT+P** 開啟命令選擇區。
3. 輸入 **ADL: Generate Code Behind**。  程式碼後置檔案會建立在相同的資料夾中。 

您也可以在指令碼檔案上按一下滑鼠右鍵，然後按一下 [ADL: Generate Code Behind] 來產生程式碼後置檔案。 

編譯並提交有程式碼後置的 U-SQL 指令碼與獨立的 U-SQL 指令碼相同。

下列兩個螢幕擷取畫面顯示程式碼後置檔案及其相關聯的 U-SQL 指令碼檔案︰
 
![Data Lake Tools for Visual Studio Code 程式碼後置](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Data Lake Tools for Visual Studio Code 程式碼後置](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>使用組件

如需有關開發組件的資訊，請參閱[針對 Azure Data Lake Analytics 作業開發 U-SQL 組件](data-lake-analytics-u-sql-develop-assemblies.md)。

您可以使用 Data Lake Tools 向 Data Lake Analytics 目錄註冊自訂的程式碼組件。

**註冊組件**

您可以透過 **ADL: Register Assembly** 或 **ADL: Register Assembly through Configuration** 來註冊組件。

**ADL: Register Assembly**
1.  按下 **CTRL+SHIFT+P** 以開啟命令選擇區。
2.  輸入 **ADL: Register Assembly**。 
3.  指定本機組件路徑。 
4.  選取 Data Lake Analytics 帳戶。
5.  選取資料庫。

結果：入口網站會在瀏覽器中開啟，並顯示組件註冊程序。  

還有另一個觸發 **ADL: Register Assembly** 命令的便利方式，就是在 [總管] 中的 dll 檔案上按一下滑鼠右鍵。 

**ADL: Register Assembly through Configuration**。
1.  按下 **CTRL+SHIFT+P** 以開啟命令選擇區。
2.  輸入 **ADL: Register Assembly through Configuration**。 
3.  指定本機組件路徑。 
4.  隨即會顯示 JSON 檔案。 請視需要檢閱並編輯組件相依性，以及資源參數。 指示會顯示在輸出視窗中。 請儲存 (**CTRL+S**) JSON 檔案以繼續進行組件註冊。

![Data Lake Tools for Visual Studio Code 程式碼後置](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- 組件相依性：Azure Data Lake Tools 會自動偵測 DLL 是否有任何相依項目。 一旦偵測到相依項目，就會顯示在 JSON 檔案中。 
>- 資源：您可以在註冊組件時，一併上傳 DLL 資源 (例如 txt、png、csv 等)。 

還有另一個觸發 **ADL: Register Assembly through Configuration** 命令的更便利方式，就是在 [總管] 中的 dll 檔案上按一下滑鼠右鍵。 

下列 U-SQL 程式碼示範如何呼叫組件。 在此範例中，組件名稱是 *test*。

```
REFERENCE ASSEMBLY [test];

@a = 
    EXTRACT 
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string 
    FROM @"Sample/SearchLog.txt" 
    USING Extractors.Tsv();

@d =
    SELECT DISTINCT Region 
    FROM @a;

@d1 = 
    PROCESS @d
    PRODUCE 
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();

OUTPUT @d1 
    TO @"Sample/SearchLogtest.txt" 
    USING Outputters.Tsv();
```


## <a name="access-data-lake-analytics-catalog"></a>存取 Data Lake Analytics 目錄

連接至 Azure 之後，您可以使用下列步驟來存取 U-SQL 目錄︰

**存取 Azure Data Lake Analytics 中繼資料**

1.  按 **CTRL+SHIFT+P**，然後輸入 **ADL: List Tables**。
2.  按一下其中一個 Data Lake Analytics 帳戶。
3.  按一下其中一個 Data Lake Analytics 資料庫。
4.  按一下其中一個結構描述。 您會看到資料表。

## <a name="show-data-lake-analytics-jobs"></a>顯示 Data Lake Analytics 作業

使用「命令選擇區」(**Ctrl+Shift+P**)，然後選擇 [ADL: Show Job]。 

1.  選取 ADLA 或本機帳戶 
2.  等候顯示帳戶的作業清單
3.  從作業清單中選取一個作業，ADL Tools 就會在入口網站中開啟作業詳細資料，然後在 VSCode 中顯示 JobInfo 檔案。

![Data Lake Tools for Visual Studio Code IntelliSense 物件類型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-adls-integration"></a>Azure Data Lake Storage (ADLS) 整合

您可以使用 ADLS 相關的命令來瀏覽 ADLS 資源、預覽 ADLS 檔案，並在 VSCode 中直接將檔案上傳到 ADLS。  
### <a name="list-storage-path"></a>列出儲存體路徑

使用「命令選擇區」(**Ctrl+Shift+P**)，然後選擇 [ADL: List Storage Path]。
1.  開啟「命令選擇區」，然後輸入命令。

    ![Data Lake Tools for Visual Studio Code 列出儲存體路徑](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  選取您慣用的列出儲存體路徑方式。 本段使用 [Enter a path] \(輸入路徑\) 作為範例。

    ![用於選取一種方式的「Data Lake Tools for Visual Studio Code 列出儲存體路徑」](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)

    > [!NOTE]
    >- Vscode 會針對每個 ADLA 帳戶保留您最後瀏覽的路徑。 範例：/tt/ss。
    >- Browser from root path (從根路徑瀏覽)：從您選取的 ADLA 帳戶或本機列出根路徑。
    >- Enter a path (輸入路徑)：從您選取的 ADLA 帳戶或本機列出指定的路徑。
    
3. 從本機或 ADLA 帳戶選取一個帳戶。

    ![Data Lake Tools for Visual Studio Code 選取更多](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  按一下 [more] \(更多\) 以列出更多 ADLA 帳戶，然後選取一個 ADLA 帳戶。

    ![Data Lake Tools for Visual Studio Code 選取一個帳戶](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

5.  輸入 Azure 儲存體路徑。 例如：/output

       ![Data Lake Tools for Visual Studio Code 輸入儲存體路徑](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

6.  結果：「命令選擇區」會根據您的輸入列出路徑資訊。

    ![Data Lake Tools for Visual Studio Code 列出儲存體路徑結果](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

還有另一個列出相對路徑的更便利方式，就是透過右鍵操作功能表。

1.  在路徑字串上按一下滑鼠右鍵以選取 [List Storage Path]。

       ![Data Lake Tools for Visual Studio Code 右鍵操作功能表](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)
2. 選取的相對路徑會顯示在「命令選擇區」中。

   ![Data Lake Tools for Visual Studio Code 列出選取的路徑](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-relative-path.png)

3.  從本機或 ADLA 帳戶選取一個帳戶。

       ![Data Lake Tools for Visual Studio Code 選取一個帳戶](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  結果：「命令選擇區」會列出目前路徑的資料夾和檔案。

       ![Data Lake Tools for Visual Studio Code 列出目前的路徑](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-storage-file"></a>預覽儲存體檔案

使用「命令選擇區」(**Ctrl+Shift+P**)，然後選擇 [ADL: Preview Storage File]。
1.  開啟「命令選擇區」，然後輸入命令。

       ![Data Lake Tools for Visual Studio Code 預覽儲存體檔案](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  從本機或 ADLA 選取一個帳戶。

       ![Data Lake Tools for Visual Studio Code 列出帳戶](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  按一下 [more] \(更多\) 以列出更多 ADLA 帳戶，然後選取一個 ADLA 帳戶。

       ![Data Lake Tools for Visual Studio Code 選取一個帳戶](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  輸入 Azure 儲存體路徑或檔案。 例如：/output/SearchLog.txt

       ![Data Lake Tools for Visual Studio Code 輸入儲存體路徑和檔案](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

5.  結果：「命令選擇區」會根據您的輸入列出路徑資訊。

       ![Data Lake Tools for Visual Studio Code 預覽檔案結果](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

還有另一個預覽檔案的更便利方式，就是透過右鍵操作功能表。

1.  在檔案路徑上按一下滑鼠右鍵以預覽檔案。

       ![Data Lake Tools for Visual Studio Code 右鍵操作功能表](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png)

2.  從本機或 ADLA 帳戶選取一個帳戶。

       ![Data Lake Tools for Visual Studio Code 選取一個帳戶](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  結果：VSCode 會顯示檔案的預覽結果。

       ![Data Lake Tools for Visual Studio Code 預覽檔案結果](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-file"></a>上傳檔案 

您可以透過 **ADL: Upload File** 或 **ADL: Upload File through Configuration** 命令上傳檔案。

**ADL: Upload File**
1.  按 CTRL+SHIFT+P 以開啟「命令選擇區」，或在指令碼編輯器上按一下滑鼠右鍵，然後輸入 **Upload File**。
2. 輸入上傳時要使用的本機路徑。

    ![Data Lake Tools for Visual Studio Code 輸入本機路徑](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-input-local-path.png)

3. 選取一種列出儲存體路徑的方式。 本段使用 [Enter a path] \(輸入路徑\) 作為範例。

    ![Data Lake Tools for Visual Studio Code 列出儲存體路徑](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)
    >[!NOTE]
    >- Vscode 會針對每個 ADLA 帳戶保留您最後瀏覽的路徑。 範例：/tt/ss。
    >- Browse from root path (從根路徑瀏覽)：從您選取的 ADLA 帳戶或本機列出根路徑。
    >- Enter a path (輸入路徑)：從您選取的 ADLA 帳戶或本機列出指定的路徑。

4. 從本機或 ADLA 帳戶選取一個帳戶。

    ![Data Lake Tools for Visual Studio Code 在儲存體上按一下滑鼠右鍵](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

5.  輸入 Azure 儲存體路徑。 例如：/output/

       ![Data Lake Tools for Visual Studio Code 輸入儲存體路徑](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

6. 列出您輸入的 Azure 儲存體路徑。 選取 [Choose current folder] \(選擇目前的資料夾\)。

    ![Data Lake Tools for Visual Studio Code 選取一個資料夾](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-choose-current-folder.png)

7.  結果：輸出視窗會顯示檔案上傳狀態。

       ![Data Lake Tools for Visual Studio Code 上傳狀態](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

**ADL: Upload File through Configuration**
1.  按 CTRL+SHIFT+P 以開啟「命令選擇區」，或在指令碼編輯器上按一下滑鼠右鍵，然後輸入 **Upload File through Configuration**。
2.  VSCode 會顯示一個 JSON 檔案。 您可以輸入檔案路徑，然後同時上傳多個檔案。 指示會顯示在輸出視窗中。 請儲存 (**CTRL+S**) JSON 檔案以繼續進行檔案上傳。

       ![Data Lake Tools for Visual Studio Code 輸入檔案](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  結果：輸出視窗會顯示檔案上傳狀態。

       ![Data Lake Tools for Visual Studio Code 上傳狀態](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

還有另一個將檔案上傳到儲存體的便利方式，就是在指令碼編輯器中的檔案完整路徑或檔案相對路徑上，透過右鍵操作功能表來上傳。 請輸入本機檔案路徑並選擇帳戶，輸出視窗就會顯示上傳狀態。 

### <a name="open-web-azure-storage-explorer"></a>開啟 Web Azure 儲存體總管
您可以透過 **ADL: Open Web Azure Storage Explorer** 命令或從右鍵操作功能表開啟「Web Azure 儲存體總管」。

1. 按 CTRL+SHIFT+P 以開啟「命令選擇區」。
2. 輸入 **Open Web Azure Storage Explorer**，或在指令碼編輯器中的相對路徑或完整路徑上按一下滑鼠右鍵，然後選擇 **Open Web Azure Storage Explorer**。
3. 選取 Data Lake Analytics 帳戶。

ADL Tools 會在入口網站中開啟 Azure 儲存體路徑。 您可以從 Web 瀏覽路徑並檢視檔案。

### <a name="local-run-and-local-debug-windows-users"></a>針對 Windows 使用者進行本機執行和本機偵錯
系統已實作 U-SQL 本機執行，可先測試您的本機資料、在本機驗證指令碼，然後才將程式碼發佈到 ADLA。 本機偵錯功能可讓您先針對 C# 程式碼後置進行偵錯、逐步執行程式碼、在本機驗證指令碼，然後才提交給 ADLA。 請參閱指示：[使用 Visual Studio Code 來進行 U-SQL 本機執行和本機偵錯](data-lake-tools-for-vscode-local-run-and-debug.md)。

## <a name="additional-features"></a>其他功能

Data Lake Tools for VSCode 支援下列功能︰

-   IntelliSense 自動完成。 會在關鍵字、方法、變數等等周圍彈出建議。不同圖示代表不同類型的物件︰

    - Scala 資料類型
    - 複雜資料類型
    - 內建 UDT
    - .Net 集合和類別
    - C# 運算式
    - 內建 C# UDF、UDO 和 UDAAG 
    - U-SQL 函式
    - U-SQL 時間範圍函式
 
    ![Data Lake Tools for Visual Studio Code IntelliSense 物件類型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Data Lake Analytics 中繼資料上的 IntelliSense 自動完成。 Data Lake Tools 會在本機下載 Data Lake Analytics 中繼資料資訊。  IntelliSense 功能會從 Data Lake Analytics 中繼資料自動填入物件，包括資料庫、結構描述、 料表、檢視、TVF、程序、C# 組件。
 
    ![Data Lake Tools for Visual Studio Code IntelliSense 中繼資料](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense 錯誤標記。 Data Lake Tools 會為 U-SQL 和 C# 的編輯錯誤加上底線。 
-   語法醒目顯示。 Data Lake Tools 會使用不同的色彩來區別變數、關鍵字、資料類型、函式等等。 

    ![Data Lake Tools for Visual Studio Code 語法醒目顯示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>後續步驟

- 如需了解如何使用 Visual Studio Code 來進行 U-SQL 本機執行和本機偵錯，請參閱[使用 Visual Studio Code 來進行 U-SQL 本機執行和本機偵錯](data-lake-tools-for-vscode-local-run-and-debug.md)。
- 如需 Data Lake Analytics 的入門資訊，請參閱[教學課程︰開始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)。
- 如需使用 Data Lake Tools for Visual Studio 的相關資訊，請參閱[教學課程：使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
- 如需有關開發組件的資訊，請參閱[針對 Azure Data Lake Analytics 作業開發 U-SQL 組件](data-lake-analytics-u-sql-develop-assemblies.md)。




