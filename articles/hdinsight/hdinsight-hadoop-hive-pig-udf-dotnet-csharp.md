---
title: "搭配使用 C# 與 HDInsight 的 Hadoop 上的 Hive 和 Pig | Microsoft Docs"
description: "了解如何搭配使用 C# 使用者定義函數 (UDF) 與 Azure HDInsight 中的 Hive 和 Pig 串流處理。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/02/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 257138fddc75b39985ba974b1314e978a554b1e2
ms.lasthandoff: 03/07/2017


---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>搭配使用 C# 使用者定義函數與 HDInsight 的 Hadoop 上的 Hive 和 Pig 串流處理。

Hive 與 Pig 很適合在 Azure HDInsight 中處理資料，但您有時需要更通用的語言。 Hive 與 Pig 都可讓您透過使用者定義函數 (UDF) 或串流處理來呼叫外部程式碼。

在本文中，您將了解如何搭配使用 C# 與 Hive 和 Pig。

> [!IMPORTANT]
> 這份文件中的步驟需要使用 Windows 做為作業系統的 HDInsight 叢集。 Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

## <a name="prerequisites"></a>必要條件

* Windows 7 或更新版本。

* 含有下列版本的 Visual Studio：

  * Visual Studio 2012 Professional/Premium/Ultimate，含 [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 Community/Professional/Premium/Ultimate，含 [Update 4](https://www.microsoft.com/download/details.aspx?id=44921)

  * Visual Studio 2015

  * Visual Studio 2017

* HDInsight 叢集上的 Hadoop - 如需建立叢集的步驟，請參閱 [佈建 HDInsight 叢集](hdinsight-provision-clusters.md)

* Hadoop Tools for Visual Studio 或 Data Lake tools for Visual Studio。 如需安裝和設定工具的步驟，請參閱 [開始使用 HDInsight Hadoop Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) 。

    > [!NOTE]
    > Data Lake 工具是透過在安裝期間選取 [Azure 開發] 工作負載安裝在 Visual Studio 2017。

## <a name="net-on-hdinsight"></a>HDInsight 上的 .NET

依預設，會在以 Windows 為基礎的 HDInsight 叢集上安裝 .NET common language runtime (CLR) 和架構。 .NET CLR 可讓您搭配使用 C# 應用程式與 Hive 和 Pig 串流處理 (資料會在 Hive/Pig 和 C# 應用程式之間透過 stdout/stdin 進行傳遞)。

> [!NOTE]
> 目前不支援在以 Linux 為基礎的 HDInsight 叢集上執行 .NET Framework UDF。


## <a name="net-and-streaming"></a>.NET 和串流處理

串流處理包括 Hive 和 Pig 透過 stdout 傳遞資料到外部應用程式，以及透過 stdin 接收結果。 對於 C# 應用程式，使用 `Console.ReadLine()` 和 `Console.WriteLine()`。

因為 Hive 和 Pig 必須在執行階段叫用應用程式，因此應在您的 C# 專案中使用**主控台應用程式 (.NET Framework)** 範本。

## <a name="hive-and-c35"></a>Hive 和 C&#35;

### <a name="create-the-c-project"></a>建立 C# 專案

1. 開啟 Visual Studio 並建立解決方案。 在專案類型中選取 [主控台應用程式 (.NET Framework)]，然後將新專案命名為 **HiveCSharp**。

2. 使用下列程式碼取代 **Program.cs** 的內容：

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. 建置專案。

### <a name="upload-to-storage"></a>上傳至儲存體

1. 在 Visual Studio 中，開啟 [伺服器總管] 。

2. 展開 [Azure]，然後展開 [HDInsight]。

3. 如果出現提示，請輸入您的 Azure 訂用帳戶認證，然後按一下 [登入] 。

4. 展開您要部署此應用程式的 HDInsight 叢集，然後展開 [ **預設儲存體帳戶**]。

    ![伺服器總管顯示此叢集的儲存體帳戶](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

5. 按兩下叢集的 [預設容器]，以檢視預設容器的內容。
6. 按一下 [上傳] 圖示，然後瀏覽至 **HiveCSharp** 專案的 **bin\debug** 資料夾。 最後，選取 **HiveCSharp.exe** 檔案並按一下 [確定]。

    ![上傳圖示](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

7. 上傳完成後，您可以透過 Hive 查詢使用此應用程式。

### <a name="hive-query"></a>Hive 查詢

1. 在 Visual Studio 中，開啟 [伺服器總管] 。

2. 展開 [Azure]，然後展開 [HDInsight]。

3. 以滑鼠右鍵按一下部署 **HiveCSharp** 應用程式的叢集，然後選取 [撰寫 Hive 查詢]。

4. 在 Hive 查詢中使用下列文字：

    ```hiveql
    add file wasbs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    此查詢會從 `hivesampletable` 中選取 `clientid`、`devicemake` 和 `devicemodel` 欄位，然後將這些欄位傳遞給 HiveCSharp.exe 應用程式。 此查詢預期應用程式會傳回儲存為 `clientid`、`phoneLabel` 和 `phoneHash` 的三個欄位。 此查詢還預期在預設儲存體容器的根目錄中找到 HiveCSharp.exe (`add file wasbs:///HiveCSharp.exe`)。

5. 按一下 [ **提交** ]，將工作提交至 HDInsight 叢集。 [Hive 作業摘要] 視窗隨即開啟。

6. 按一下 [重新整理] 以重新整理摘要，直到 [工作狀態] 變更為 [已完成] 為止。 若要檢視工作輸出，請按一下 [ **工作輸出**]。

## <a name="pig-and-c35"></a>Pig 和 C&#35;

### <a name="create-the-c-project"></a>建立 C# 專案

1. 開啟 Visual Studio 並建立解決方案。 在專案類型中選取 [主控台應用程式]，然後將新專案命名為 **PigUDF**。

2. 使用下列程式碼取代 **Program.cs** 檔案的內容：

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    此應用程式將剖析 Pig 送出的程式碼行，並將其重新格式化為以 `java.lang.Exception` 開頭的程式碼行。

3. 儲存 **Program.cs**，然後建置專案。

### <a name="upload-the-application"></a>上傳應用程式

1. Pig 串流處理預期此應用程式會成為叢集檔案系統上的本機。 啟用 HDInsight 叢集的「遠端桌面」，然後依照 [使用 RDP 連線到 HDInsight 叢集](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)中的指示進行連線。

2. 連線之後，從您本機電腦上 PigUDF 專案的 **bin/debug** 目錄中複製 **PigUDF.exe**，然後將它貼到叢集上的 **%PIG_HOME%** 目錄。

### <a name="use-the-application-from-pig-latin"></a>從 Pig Latin 使用應用程式

1. 在遠端桌面工作階段中，使用桌面上的 [Hadoop 命令列] 圖示來啟動 Hadoop 命令列。

2. 使用下列命令來啟動 Pig 命令列：

        cd %PIG_HOME%
        bin\pig

    `grunt>` 提示隨即顯示。
    
3. 輸入下列命令，透過使用 .NET Framework 應用程式執行 Pig 作業：

        DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    `DEFINE` 陳述式會為 pigudf.exe 應用程式建立別名 `streamer`，然後 `SHIP` 會將它分散到叢集中的節點。 稍後，`streamer` 會與 `STREAM` 運算子搭配使用，進而處理 LOG 中所包含的單行，並以一連串資料行的方式傳回資料。

    > [!NOTE]
    > 建立別名時，用於串流處理的應用程式名稱的兩邊必須加上 \` (反引號) 字元，與 `SHIP` 搭配使用時，必須加上 ' (單引號) 字元。

4. 輸入最後一行後，工作應該就會開始。 它會傳回類似下列文字的輸出：

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="summary"></a>摘要

在本文中，您已學會如何從 HDInsight 上的 Hive 和 Pig 使用 .NET Framework 應用程式。 如果您想要了解如何搭配使用 Python 與 Hive 和 Pig，請參閱 [搭配使用 Python 與 HDInsight 中的 Hive 和 Pig](hdinsight-python.md)。

若要了解Pig 和 Hive 的其他使用方式，以及了解使用 MapReduce，請參閱下列文件：

* [〈搭配 HDInsight 使用 Hivet〉](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)

