---
title: "Azure Data Lake 的 U-SQL 可程式性指南 | Microsoft Docs"
description: "深入了解在 Azure Data Lake 可讓您建立雲端型巨量資料平台的服務集。"
services: data-lake-analytics
documentationcenter: 
author: MikeRys
manager: arindamc
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: mrys
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 6efdd600386a603b38a5733977067f1a987cdac3
ms.lasthandoff: 03/17/2017


---

# <a name="u-sql-programmability-guide"></a>U-SQL 可程式性指南
## <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake 包括的功能可方便開發人員、資料科學家和分析師儲存任何大小、形狀和速度的資料。 它也可讓開發人員跨平台和語言使用許多類型的處理和分析。 其移除了內嵌及儲存所有資料所帶來的複雜性，同時可讓使用 Batch、串流及互動式分析變得更加迅速。

Azure Data Lake 是一組一起運作以提供雲端架構巨量資料平台的服務。 這些服務包括：

- Azure HDInsight
- Azure Data Lake Store
- Azure Data Lake Analytics

U-SQL 是為巨量資料類型的工作負載所設計的查詢語言。 U-SQL 的其中一項獨特功能是，可將類 SQL 的宣告式語言與 C# 所提供的擴充性和可程式性結合在一起。 它也能讓您存取和處理結構描述中繼資料，並建立資料處理器和歸納器等自訂元件。

在本指南中，我們將著重於介紹由 C# 所實現的 U-SQL 語言之擴充性和可程式性。

## <a name="requirements"></a>需求
若要開始開發 Azure Data Lake，您必須下載並安裝 [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)。

## <a name="get-started-with-u-sql"></a>開始使用 U-SQL  
U-SQL 語言的說明不在本文介紹範圍內。 不過，我們會說明基本的 U-SQL 結構，以逐漸導入 U-SQL 可程式性功能的介紹。 如需詳細資訊，請參閱 [U-SQL 語言參考](http://aka.ms/usql_reference)指南。

我們來看看下面的範例：

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

在此範例中，我們有「輸入檔」input_file.tsv，由 **Local Variable** @input_file 定義。

下列動作是由此範例中顯示之 U-SQL 指令碼執行︰

* 初始 **EXTRACT** 陳述式會將輸入檔轉換成**記憶體資料列集**，以將資料載入到記憶體。
* **SELECT** 會對資料列集進行操作，以彙總資料並準備進行匯出。
* **OUTPUT** 會將資料列集匯出至**輸出檔** (一個外部檔案)。

首先，讓我們看看一些可直接在 U-SQL 指令碼中使用 C# 運算式的選項。

## <a name="c-types-and-expressions-in-u-sql-script"></a>U-SQL 指令碼中的 C# 類型和運算式
U-SQL C# 運算式和一般 C# 中的運算式類似，皆為一連串可評估為單一值、物件、方法或命名空間的一個或多個運算子。 運算式中可包含常值、方法引動過程、運算子或簡單名稱。 簡單名稱則可以是變數、類型成員、方法參數、命名空間或類型的名稱。

當我們談到 U-SQL C# 運算式時，我們專指 U-SQL 基底指令碼 C# 運算式。 本文稍後會討論到的基礎 C# 程式碼後置區段，也能以一般 C# 程式碼式元素的形式來包含 C# 運算式。

運算式可以使用將其他運算式用來做為參數的運算子。 它們也可以使用具有做為其他方法呼叫之參數的方法呼叫。 以下是運算式的範例：  

```c#
    Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd"))
```

U-SQL 語言可使用來自內建命名空間的標準 C# 運算式。  

```c#
    Microsoft.Analytics.Interfaces;  
    Microsoft.Analytics.Types.Sql;  
    System;  
    System.Collections.Generic;  
    System.Linq;  
    System.Text;  
```

一般 C# 運算式可用於 U-SQL SELECT、EXTRACT。

C# 運算式也可用於 DECLARE 或 IF 陳述式。 此類型運算式的範例如下所示︰   

```c#
    DateTime.Today.Day   
    Convert.ToDateTime
```

U-SQL 基底指令碼範例︰  

```sql
    DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");
```

在處理資料列集裡面的資料行時，C# 運算式可提供延伸功能。 例如，如果您想要將日期時間資料行轉換成零小時的日期，您可以使用 U-SQL 基底指令碼的 SELECT 部分 (如下所示)︰

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
     MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd")))
AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

誠如所見，您使用 `System.Convert.ToDateTime` 方法來貫穿轉換執行過程。

以下是稍微複雜一點的案例，示範一些基本 C# 運算子的用法︰

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
          MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

其顯示了 C# 條件運算子運算式的範例。

先前範例示範如何在基底 U-SQL 指令碼中使用 C# 運算式。 不過，U-SQL 可實現更具擴充能力的可程式性功能 (本文後面會有說明)。  

完整指令碼：

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="use-c-expressions-for-data-type-conversions"></a>使用 C# 運算式轉換資料類型
下列範例示範如何使用 C# 運算式進行日期時間資料轉換。 在這個特別案例中，字串日期時間資料會轉換成標準日期時間，也就是以午夜 00:00:00 為準的時間標記法。

```sql
DECLARE @dt String = "2016-07-06 10:23:15";
@rs1 =
    SELECT Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
           dt AS olddt
    FROM @rs0;
OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>使用 C# 運算式來表示今天的日期
若要提取今天的日期，我們可以將下列 C# 運算式：

```c#
DateTime.Now.ToString("M/d/yyyy")
```

如何在指令碼中使用此運算式的範例如下︰

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

## <a name="use-inline-c-function-expressions"></a>使用內嵌 C# 函式運算式
U-SQL 允許在 C# 運算式中使用內嵌函式運算式定義。 這可建立另外一種可讓您搭配使用 C# 函式與輸出參考參數的辦法。

下列範例是一般內嵌函式運算式定義：

```c#
    (Func<type of param1, type of param2>)
    (param1 =>
         { … return param2}
    ) (Rowset Column)
```

範例：

```c#
    (Func<string, DateTime?>)
    (input_p =>
         {
            DateTime dt_result;
            return DateTime.TryParse(input_p, out dt_result)
                   ? (DateTime?) dt_result : (DateTime?) null;
         }
    )
    ) (dt)
```

在此範例中，我們會使用字串輸入參數 input_p 定義內嵌函式。 我們會在此函式內部驗證輸入字串是否為有效的日期時間值。 如果是便將它傳回，否則傳回 null。

此案例需要內嵌函式，因為 DateTime.TryParse 函式包含輸出參數 `out dt_result`。 我們將它定義為 `DateTime dt_result;`。


## <a name="verify-data-type-values"></a>驗證資料類型值
某些 C# 函式不能以 C# 運算式的形式直接用在基底 U-SQL 指令碼中。 具體而言，不能直接使用的函式是指需要輸出參考參數的函式。 不過，您可以按照前面所說的方式，定義這些函式並用於內嵌函式運算式中。

### <a name="use-inline-function-expressions"></a>使用內嵌函式運算式
若要確認日期時間值是否有效，我們可以使用 `DateTime.TryParse`。

以下是完整的範例，示範如何使用內嵌函式運算式來確認資料類型值與 `DateTime.TryParse`。

在此案例中，我們會驗證日期時間資料類型值：

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt string,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN((
             (Func<string, DateTime?>)
             (input_parameter =>
                { DateTime dt_result;
                  return
                     DateTime.TryParse(input_parameter, out dt_result)
                       ?(DateTime?) dt_result : (DateTime?) null;
                }
             )
             ) (dt)) AS start_time,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="use-code-behind"></a>使用程式碼後置
若要在 U-SQL 程式的程式碼後置區段中使用相同的功能，我們可以定義 C# 函式 ToDateTime。

以下是基底 U-SQL 指令碼區段，我們在其中做了必要的變更︰

```sql
     @rs1 =
        SELECT
          MAX(guid) AS start_id,
          MIN(USQL_Programmability.CustomFunctions.ToDateTime(dt)) AS start_time,
          DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
          user,
          des
          FROM @rs0
        GROUP BY user, des;
```

以下是程式碼後置函式︰

```c#
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }
```

## <a name="use-code-behind"></a>使用程式碼後置
程式碼後置是 U-SQL 專案的 C# 可程式性區段。 在概念上，程式碼後置就是 U-SQL 指令碼中所參考的已編譯組件 (DLL)。 Visual Studio Tools 可讓您管理和偵錯 U-SQL 專案中的 C# 可程式性區段。

當您在 Visual Studio 中建立典型的 U-SQL 專案時，專案內會有兩個部分：基本指令碼及副檔名為 **.usql**的檔案。

![基本指令碼檔案](./media/data-lake-analytics-u-sql-programmability-guide/base-script-file.png)


典型的方案專案：   
![典型的方案專案](./media/data-lake-analytics-u-sql-programmability-guide/typical-solution-project.png)


在專案的第二個部分，我們呼叫程式碼後置檔案：Script.usql.cs。  
![程式碼後置](./media/data-lake-analytics-u-sql-programmability-guide/code-behind.png)

這個檔案包含可程式性物件的預設命名空間定義。

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{

}
```

先前的程式碼後置範本是自動產生的。 這個檔案包含可程式性物件的預設命名空間定義。 在專案執行時，此檔案會進行編譯並受到基底 U-SQL 指令碼的參考。

若要開始開發可程式性物件，我們需要建立**公用**類別。

```c#
namespace USQL_Programmability
{
    public class MyClass
    {
        public static string MyFunction(string param1)
        {
            return "my result";
        }

    }

}
```

可程式性物件可以是使用者定義函式、UDF、使用者定義類型、UDT、PROCESS 或 REDUCER 等項目。

## <a name="register-u-sql-assemblies"></a>註冊 U-SQL 組件
U-SQL 的擴充性模型極為依賴新增自訂程式碼的能力。 目前，U-SQL 會為您提供簡單的方法，以新增您自己的 Microsoft .NET 為基礎的程式碼 (尤其是 C#)。 不過，您也可以新增以其他 .NET 語言 (例如 VB.NET 或 F#) 撰寫的自訂程式碼。

您甚至可以部署其他語言的自有執行階段，不過您仍必須自行提供透過 .Net 層的互通性。 如果您希望我們支援特定語言，請到 http://aka.ms/adlfeedback 提出功能要求或留言。

### <a name="learn-the-difference-between-code-behind-and-assembly-registration-through-azure-data-lake-tools-in-visual-studio"></a>了解透過 Azure Data Lake Tools 在 Visual Studio 中執行程式碼後置和組件註冊的差異
使用自訂程式碼最簡單的方法是使用 Azure Data Lake Tools for Visual Studio 的程式碼後置功能。

如先前所述，您會將指令碼的自訂程式碼 (例如，Script.usql) 填入到其程式碼後置檔案 (例如，Script.usql.cs)。

![程式碼後置範例](./media/data-lake-analytics-u-sql-programmability-guide/code-behind-example.jpg)
**圖 1**︰在 Visual Studio 中的 Azure Data Lake Tools 中的程式碼後置範例。 (按一下影像以放大。 提供[範例程式碼](https://github.com/Azure/usql/tree/master/Examples/TweetAnalysis)。)


程式碼後置的優點是，當您提交指令碼時，工具會為您執行下列步驟︰  

1. 它會建置程式碼後置檔案的組件。  

2. 它會將序言新增至使用 [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) 陳述式來註冊組件檔案的指令碼。 它也會使用 [REFERENCE ASSEMBLY] (https://msdn.microsoft.com/library/azure/mt763294.aspx) 將組件載入指令碼的內容。

3. 它會對指令碼新增結語，使用 [DROP ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763295.aspx) 再次移除暫時註冊的組件。

當您開啟指令碼時，您可以看到產生的序言和結語︰

![產生的序言](./media/data-lake-analytics-u-sql-programmability-guide/generated-prologue.png)

**圖 2**：為程式碼後置自動產生的序言和結語
<br />

程式碼後置的部分缺點如下：

* 每次提交指令碼時都會上傳程式碼。
* 無法與他人共用功能。

因此，您可以將個別 C# 類別庫 (適用於 U-SQL) 新增至您的方案 (見圖 3)、開發程式碼，或複製現有的程式碼後置程式碼 (如圖 4 所示，不需要在 C# 程式碼中進行變更)。 然後使用專案上的**註冊組件**功能表選項，以註冊組件 (如圖 5 的步驟 1 所示)。

![建立專案](./media/data-lake-analytics-u-sql-programmability-guide/creating-project.png)
**圖 3**：建立 U-SQL C# 程式碼專案  
<br />

![類別庫](./media/data-lake-analytics-u-sql-programmability-guide/class-library.png)
**圖 4**：程式碼後置檔案旁的 U-SQL C# 類別庫
<br />

![註冊程式碼](./media/data-lake-analytics-u-sql-programmability-guide/register-code.png)
**圖 5**︰如何註冊 U-SQL C# 程式碼專案
<br />

註冊對話方塊 (請參閱圖 5 的步驟 2) 可讓您選擇組件註冊的方式 (例如，要使用的 Data Lake Analytics 帳戶、要使用的資料庫)。 它也提供有關如何命名組件的資訊。 (本機組件路徑會工具填入。)此外，它也可讓您選擇重新註冊已註冊的組件，以及兩個用來新增其他相依性的選項︰

**受管理的相依性**︰顯示需要的受管理組件。 所選取的每個組件都會個別註冊，並可供指令碼參考。 此選項適用於其他 .Net 組件。

**其他檔案**︰可讓您新增組件所需的其他資源檔。 當組件受到參考時，這些資源檔便會隨組件一起註冊並自動載入。 此選項適用於組態檔、原生組件、其他語言執行階段和這些項目的資源等。

我們在下列範例使用兩種選項。 [近期有關映像處理的部落格文章](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/18/introducing-image-processing-in-u-sql/)是另一個範例，其示範如何使用可利用這些選項進行註冊的預先定義組件。

現在，您可以從任何擁有已註冊組件之資料庫存取權限的任何 U-SQL 指令碼，參考已註冊的組件。 (如需詳細資訊，請參閱圖 4 中 U-SQL 指令碼中的程式碼)。您必須針對每個個別註冊的組件新增參考。 其他資源檔案則會自動部署。 該指令碼應該不會再擁有所參考組件之程式碼的程式碼後置檔案，但程式碼後置檔案仍可提供其他程式碼。

### <a name="register-assemblies-via-azure-data-lake-tools-in-visual-studio-and-in-u-sql-scripts"></a>透過 Azure Data Lake Tools 在 Visual Studio 和 U-SQL 指令碼中註冊組件
雖然 Visual Studio 中的 Azure Data Lake Tools 可讓您輕鬆地註冊組件，但如果您要 (舉例來說) 在不同平台上進行開發，且該平台上具有已編譯好之欲上傳和註冊的組件，或您也可以使用指令碼來進行註冊工作 (方式和 Tools 為您所做的一樣)。 請執行下列步驟：

1. 將您的組件 DLL 以及所有的非系統 DLL 和資源檔案上傳到您選擇的位置。 您也可以上傳至您的 Azure Data Lake 儲存體帳戶，或甚至是連結至您 Azure Data Lake 帳戶的 Microsoft Azure Blob 儲存體帳戶。 您可以使用您所能取得的任何上傳工具 (例如 Windows PowerShell 命令、Visual Studio 的 Azure Data Lake 工具 Data Lake Explorer 上傳、您愛用之 SDK 的上傳命令或透過 Azure 入口網站存取的工具)。

1. 上傳了 DLL 之後，使用 [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) 陳述式來註冊它們。

我們會在下面的空間範例中使用此方法。

### <a name="register-assemblies-that-use-other-net-assemblies-based-on-the-json-and-xml-sample-library"></a>註冊使用其他 .Net 組件 (以 JSON 和 XML 範例程式庫為基礎) 的組件
我們的 [U-SQL GitHub 網站](https://github.com/Azure/usql/)提供一組共用的範例組件供您使用。 其中一個稱為 [Microsoft.Analytics.Samples.Formats](https://github.com/Azure/usql/tree/master/Examples/DataFormats) 的組件可提供擷取器、函式和輸出器來處理 JSON 和 XML 文件。 Microsoft.Analytics.Samples.Formats 組件依賴兩個現有的網域特有組件來分別進行 JSON 和 XML 的處理。 它會使用 [Newtonsoft Json.Net](http://www.newtonsoft.com/) 程式庫來處理 JSON 文件，以及 [System.Xml](https://msdn.microsoft.com/data/bb291078.aspx) 組件來處理 XML。 我們用它來示範如何進行註冊，並在我們的指令碼中使用這些組件。

首先，將 [Visual Studio 專案](https://github.com/Azure/usql/tree/master/Examples/DataFormats)下載至本機開發環境 (例如，使用適用於 Windows 的 GitHub 工具建立本機複本)。 接著在 Visual Studio 中開啟方案，(如先前所述) 以滑鼠右鍵按一下專案以註冊組件。

雖然這個組件有兩個相依性，但我們只需要包含 Newtonsoft 相依性，因為 System.Xml 已可在 Azure Data Lake 中取得 (不過，必須明確地參考)。 圖 6 說明我們是如何為組件命名 (請注意，您也可以選擇不含點的其他名稱)，以及如何新增 Newtonsoft DLL。 這兩個組件會各自註冊到指定的資料庫 (例如，JSONBlog)。

![註冊組件](./media/data-lake-analytics-u-sql-programmability-guide/register-assembly.png)

**圖 6**︰如何從 Visual Studio 註冊 Microsoft.Analytics.Samples.Formats 組件
<br />

如果您或其他人 (您透過賦予資料庫讀取權限而與其共用已註冊之組件者) 現在想要在指令碼中使用 JSON 功能，需要在指令碼中新增下列兩個參考︰

```
REFERENCE ASSEMBLY JSONBlog.[NewtonSoft.Json];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

而如果您想要使用 XML 功能，則需要新增系統組件的參考，以及對已註冊組件的參考︰

```
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

如需如何使用 JSON 功能的詳細資訊，請參閱[此部落格文章](https://blogs.msdn.microsoft.com/mrys/?p=755)。

### <a name="register-assemblies-that-use-native-c-assemblies-using-the-sql-server-2016-spatial-type-assembly-from-the-feature-pack"></a>註冊使用原生 C++ 組件 (使用來自功能套件的 SQL Server 2016 空間類型組件) 的組件
現在，讓我們來看看稍微不同的案例。 假設我們想要使用的組件版本具有的相依性不是 .NET 型的程式碼。 更具體來說，組件具有原生 C++ 組件相依性。 這類組件的範例是 SQL Server 類型組件 [Microsoft.SqlServer.Types.dll](https://www.microsoft.com/download/details.aspx?id=52676)。 它提供 .NET 為基礎的 SQL Server hierarchyID、geometry 和由 SQL Server 用戶端應用程式用來處理 SQL Server 類型的地理位置類型等實作。 (原本也是提供 SQL Server 2016 版本之前的 SQL Server 空間類型實作的組件。)

讓我們看看如何在 U-SQL 中註冊這個組件。

首先，從 [SQL Server 2016 功能套件](https://www.microsoft.com/download/details.aspx?id=52676)下載並安裝組件。 若要確保您擁有 64 位元版本的程式庫，選取 64 位元版本的安裝程式 (ENU\x64\SQLSysClrTypes.msi)。

安裝程式會將受管理的組件 Microsoft.SqlServer.Types.dll 安裝到 C:\Program Files (x86)\Microsoft SQL Server\130\SDK\Assemblies，並將原生組件 SqlServerSpatial130.dll 安裝到 \Windows\System32\. 現在，將這兩個組件上傳到 Azure Data Lake Store (例如，上傳到稱為 /upload/asm/spatial 的資料夾)。

安裝程式已將原生程式庫安裝到系統資料夾 c:\Windows\System32，因此我們必須確定我們會先從該資料夾複製 SqlServerSpatial130.dll 再將它上傳，或我們使用的工具不會對系統資料夾執行[檔案系統重新導向](https://msdn.microsoft.com/library/windows/desktop/aa384187(v=vs.85).aspx)。

例如，如果您想要使用目前 Visual Studio Azure Data Lake 檔案總管將它上傳，必須先將檔案複製到另一個目錄。 否則--以撰寫這篇文章的時間為準--您上傳 32 位元版本。 原因是，Visual Studio 是 32 位元應用程式，會在其 Azure Data Lake 上傳檔案選取範圍視窗中進行檔案系統重新導向。 然後，當您執行呼叫原生組件的 U-SQL 指令碼時，請在執行階段取得下列 (內部) 錯誤︰

來自使用者運算式的內部例外狀況︰嘗試載入錯誤格式的程式 (例外狀況發生於 HRESULT：0x8007000B)

上傳了兩個組件檔案後，我們現在會使用下列指令碼將它們註冊到資料庫 SQLSpatial︰

```sql
DECLARE @ASSEMBLY_PATH string = "/upload/asm/spatial/";
DECLARE @SPATIAL_ASM string = @ASSEMBLY_PATH+"Microsoft.SqlServer.Types.dll";
DECLARE @SPATIAL_NATIVEDLL string = @ASSEMBLY_PATH+"SqlServerSpatial130.dll";

CREATE DATABASE IF NOT EXISTS SQLSpatial;
USE DATABASE SQLSpatial;

DROP ASSEMBLY IF EXISTS SqlSpatial;
CREATE ASSEMBLY SqlSpatial
FROM @SPATIAL_ASM
WITH ADDITIONAL_FILES =
     (
         @SPATIAL_NATIVEDLL
     );
```

在此案例中，我們只會註冊一個 U-SQL 組件，並將原生組件包含做為 U-SQL 組件的字串相依性。 若要使用空間組件，我們只需要參考 U-SQL 組件。 其他檔案會自動可供組件使用。 以下是使用空間組件的簡單範例指令碼︰

```sql
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY SQLSpatial.SqlSpatial;

USING Geometry = Microsoft.SqlServer.Types.SqlGeometry;
USING Geography = Microsoft.SqlServer.Types.SqlGeography;
USING SqlChars = System.Data.SqlTypes.SqlChars;

@spatial =
    SELECT * FROM (VALUES
                   // The following expression is not using the native DDL
                   ( Geometry.Point(1.0,1.0,0).ToString()),    
                   // The following expression is using the native DDL
                   ( Geometry.STGeomFromText(new SqlChars("LINESTRING (100 100, 20 180, 180 180)"), 0).ToString())
                  ) AS T(geom);

OUTPUT @spatial
TO "/output/spatial.csv"
USING Outputters.Csv();
```

SQL 類型程式庫相依於 System.XML 組件，因此我們需要參考它。 此外，某些方法會使用 System.Data.SqlTypes 類型而不是內建的 C# 類型。 因為預設已包含 System.Data，我們可以參考所需的 SQL 類型。 先前的程式碼可於 [Github 網站](https://github.com/Azure/usql/tree/master/Examples/SQLSpatialExample)取得。


### <a name="use-assembly-versioning"></a>使用組件版本控制
U-SQL 目前使用 .Net Framework 4.5 版。 因此，請確定您自己的組件與該執行階段版本相容。

如前面所述，U-SQL 會執行 64 位元 (x64) 格式的程式碼。 因此，請確定您的程式碼是編譯為可在 x64 上執行。 否則，您會得到如上所示的格式不正確錯誤。

每個上傳的組件 DLL 和資源檔，例如不同的執行階段、原生組件或組態檔中，最多可達 400 MB。 已部署資源的大小總計 (透過 DEPLOY RESOURCE 或透過參考組件及其他檔案) 不能超過 3 GB。

最後請注意，每一個 U-SQL 資料庫所包含的任何指定組件只能有一個版本。 例如，如果您同時需要第 7 版和第 8 版的 NewtonSoft Json.Net 程式庫，您需要將它們註冊到兩個不同的資料庫。 此外，每個指令碼所參考的指定組件 DLL 只能有一個版本。 在這方面，U-SQL 會遵循 C# 組件的管理和版本設定語意。


## <a name="use-user-defined-functions-udf"></a>使用使用者定義函式：UDF
U-SQL 使用者定義函數 (簡稱 UDF) 會編寫常式，以接受參數、執行動作 (例如複雜計算)，以及傳回該動作結果的值。 UDF 的傳回值只能是單一純量。 U-SQL UDF 可以和任何其他 C# 純量函式一樣，在 U-SQL 基底指令碼中進行呼叫。

我們建議您將 U-SQL 使用者定義函式初始化為**公用**且**靜態**的函式。

```c#
        public static string MyFunction(string param1)
        {
            return "my result";
        }
```

我們先看看簡單的建立 UDF 範例。

在此使用案例中，我們必須決定特定使用者首次登入的會計週期 (包括會計季度和會計月份)。 在我們的案例中，一年的第一個會計月份為六月。

為了計算會計週期，我們引進了下列 C# 函式：

```c#
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
        }
```

它只會計算會計月份和季度，並傳回字串值。 六月 (第一個會計季度的第一個月份)，我們使用「Q1:P1」。 七月，我們使用「Q1:P2」，以此類推。

這是一般 C# 函式，我們將使用我們的 U-SQL 專案。

此案例中的程式碼後置區段外觀如下：

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }

    }

}
```

現在，我們要從基底 U-SQL 指令碼呼叫此函式。 若要這樣做，我們必須提供完整函式名稱，包括命名空間，在此案例中為 NameSpace.Class.Function(parameter)。

```sql
    USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

以下是實際的 U-SQL 基底指令碼：

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

以下是指令碼執行的輸出檔案：

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

這個範例示範 U-SQL 中內嵌 UDF 的簡易用法。

### <a name="keep-state-between-udf-invocations"></a>在 UDF 的引動過程之間保持狀態
U-SQL C# 可程式性物件可以透過程式碼後置全域變數，更加複雜地利用互動性。 讓我們看看下列商務使用案例。

在大型組織中，使用者可以切換使用各種內部應用程式。 這些可能包括 Microsoft Dynamics CRM、PowerBI 等等。 客戶可能想要以遙測資料分析使用者切換使用不同應用程式的方式、使用趨勢為何等等的資訊。 企業的目的是獲得最佳的應用程式使用情況。 他們或許也想要會合併不同應用程式或特定登入常式。

為了達成此目標，我們必須確定工作階段識別碼，以及最後發生之工作階段間的延遲時間。

我們必須找到上一次登入，然後對所有將為相同應用程式產生的工作階段指派此登入。 第一項挑戰是 U-SQL 基底指令碼無法讓我們使用 LAG 函數，對所有已經過計算的資料行套用計算。 第二項挑戰在於，我們必須讓相同時間週期內的所有工作階段保持在特定工作階段。

為了解決這個問題，我們將在程式碼後置區段內使用全域變數：`static public string globalSession;`。

在我們執行指令碼期間，此全域變數會套用到整個資料列集。

以下是 U-SQL 程式的程式碼後置區段：

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60)
                    return Session;
                else
                    return Guid.NewGuid().ToString();
            }
            else
                return Guid.NewGuid().ToString();

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session))
            {
                globalSession = Session;
            }
            return globalSession;
        }

    }
}
```

這個範例會示範全域變數 `static public string globalSession;` 用於 `getStampUserSession` 函式內，每次工作階段參數變更時都會重新初始化。

U-SQL 基礎指令碼如下所示︰

```sql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT EventDateTime,
           UserName,
LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
           USQLApplication21.UserSession.StampUserSession
           (
               EventDateTime,
               LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
               LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           )
           AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT EventDateTime,
           UserName,
           LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
           USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
TO @out2
ORDER BY UserName, EventDateTime ASC
USING Outputters.Csv();
```

第二次計算記憶體資料列集期間會在此呼叫函式 `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)`。 它會傳遞 `UserSessionTimestamp` 資料行，並傳回值直到 `UserSessionTimestamp` 變更。

輸出檔案如下所示：

```
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

此範例示範當我們在程式碼後置區段內使用套用至整個記憶體資料列集之全域變數時的更複雜使用案例。

## <a name="use-user-defined-types-udt"></a>使用使用者定義的類型：UDT
使用者定義類型 (UDT) 是 U-SQL 的另一個可程式性功能。 U-SQL UDT 的作用就像一般的 C# 使用者定義類型。 C# 是強類型的語言，允許使用內建和自訂的使用者定義類型。

在資料列集的頂點之間傳遞 UDT 時，U-SQL 無法以隱含方式序列化或還原序列化任意 UDT。 這表示使用者必須使用 IFormatter 介面提供明確的格式器。 這將提供 U-SQL 將 UDT 序列化和還原序列化的方法。

> [!NOTE]
> U-SQL 的內建擷取器和 outputter 目前無法將 UDT 資料從檔案往返序列化或還原序列化，即使是已設定 IFormatter 亦然。 因此當您使用 OUTPUT 陳述式撰寫 UDT 資料至檔案，或是使用解壓縮讀取它時，您必須將它做為字串或位元組陣列傳遞。 然後呼叫序列化，並明確地還原序列化程式碼 (也就是 UDT 的 ToString() 方法)。 另一方面，使用者定義的擷取器和 outputter 可以讀取並寫入 UDT。

如果我們嘗試在 EXTRACTOR 或 OUTPUTTER 中使用 UDT (出自上一個 SELECT)，如下所示︰

```sql
@rs1 =
    SELECT MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

我們會收到下列錯誤：

```
    Error    1    E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
    MyNameSpace.Myfunction_Returning_UDT.

    Description:

    Outputters.Text only supports built-in types.

    Resolution:

    Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
    the preceding SELECT.    C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
    USQL-Programmability\Types.usql    52    1    USQL-Programmability
```

若要在 Outptutter 中使用 UDT，我們必須使用 ToString() 方法將它序列化為字串，或建立自訂 Outputter。

UDT 目前不能用於 GROUP BY。 如果將 UDT 用於 GROUP BY，系統會擲回下列錯誤︰

```
    Error    1    E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
    for column myfield

    Description:

    GROUP BY doesn't support UDT or Complex types.

    Resolution:

    Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
    C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
    62    5    USQL-Programmability
```

若要定義 UDT，我們必須︰

* 新增下列命名空間：

```c#
    using Microsoft.Analytics.Interfaces
    using System.IO;
```

* 新增 `Microsoft.Analytics.Interfaces`，其為 UDT 介面所需。 此外，`System.IO` 可能需要用來定義 IFormatter 介面。

* 使用 SqlUserDefinedType 屬性來定義使用者定義類型。

**SqlUserDefinedType** 可用來將組件中的類型定義標示為 U-SQL 中的使用者定義類型 (UDT)。 屬性 (attribute) 上的屬性 (property) 會反映 UDT 的實際特性。 這個類別無法繼承。

SqlUserDefinedType 是 UDT 定義的必要屬性 (attribute)。

類別的建構函式：  

* SqlUserDefinedTypeAttribute (類型格式器)

* 輸入格式器︰需要參數來定義 UDT 格式器--明確地說，必須在這裡傳遞 `IFormatter` 介面的類型。

```c#
    [SqlUserDefinedType(typeof(MyTypeFormatter))]
      public class MyType
              {
             …
           }
```

* 典型的 UDT 也需要 IFormatter 介面定義，如下列範例所示︰

```c#
       public class MyTypeFormatter : IFormatter<MyType>
        {
            public void Serialize(MyType instance, IColumnWriter writer,
                           ISerializationContext context)
            {
        …
            }

            public MyType Deserialize(IColumnReader reader,
                                  ISerializationContext context)
            {
        …
            }
        }
```

用來將根類型為 \<typeparamref name="T"> 之物件圖形序列化和還原序列化的 `IFormatter` 介面。

\<typeparam name="T"> 要序列化和還原序列化之物件圖形的根類型。

* **還原序列化**：對所提供串流上的資料還原序列化，並重組物件的圖形。

* **序列化**：使用指定根將物件或物件圖形序列化到所提供的串流。

`MyType` 執行個體：類型的執行個體。  
`IColumnWriter` writer / `IColumnReader` 讀取器：基礎資料行串流。  
`ISerializationContext` 內容：定義一組旗標的列舉，可在序列化期間指定串流的來源或目的地內容。

   * **中繼**：指定來源或目的地內容不是持續性存放區。

   * **持續性**：指定來源或目的地內容是持續性存放區。

做為一般的 C# 類型，U-SQL UDT 定義可包括 +/==/!= 等運算子的覆寫。 它也可以包含靜態方法。 例如，如果我們要使用此 UDT 做為 U-SQL MIN 彙總函式的參數，我們必須定義 < 運算子覆寫。

本指南稍早曾示範過從格式為 Qn:Pn (Q1:P10) 之特定日期識別會計週期的範例。 下列範例將示範如何為會計週期值定義自訂類型。

以下是具有自訂 UDT 和 IFormatter 介面的程式碼後置區段範例︰

```c#
        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
```

已定義的類型包含兩個數字：季度和月份。 運算子 ==/!=/>/< 和靜態方法 ToString() 定義於此。

如先前所述，UDT 可用於 SELECT 運算式中，但不能用在沒有自訂序列化的 OUTPUTTER/EXTRACTOR。 它必須使用 ToString() 序列化為字串或與自訂 OUTPUTTER/EXTRACTOR 搭配使用。

現在我們來討論一下 UDT 的使用方式。 在程式碼後置區段中，我們已將 GetFiscalPeriod 函式變更為：

```c#
        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }
```

如您所見，它會傳回 FiscalPeriod 類型的值。

這裡我們提供如何在 U-SQL 基底指令碼中進一步使用它的範例。 這個範例會示範從 U-SQL 指令碼叫用 UDT 的不同方式。

```sql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT guid AS start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs0;

@rs2 =
    SELECT start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```

以下是完整程式碼後置區段的範例︰

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }

    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>使用使用者定義彙總：UDAGG
使用者定義彙總是指並非 U-SQL 現成提供的彙總相關函式。 其範例包括用來執行自訂數學計算、字串串連、使用字串之操作等彙總。

使用者定義彙總的基底類別定義如下所示：

```c#
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** 表示類型應該註冊為使用者定義彙總。 這個類別無法繼承。

SqlUserDefinedType 是 UDAGG 定義的**選擇性**屬性。


基底類別可讓您傳遞三個抽象參數：其中兩個做為輸入參數，一個做為結果參數。 資料類型會變動，但應該會在繼承類別時進行定義。

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
        …
        }

        public override void Accumulate(string guid, string user)
        {
        …
        }

        public override string Terminate()
        {
        …
        }

    }
```

* **Init** 會在計算期間，為每個群組叫用一次。 Init 可為每個彙總群組提供初始化常式。  
* **Accumulate** 會為每個值執行一次。 它提供彙總演算法的主要功能。 在繼承類別時，它可用來彙總各種資料類型的值。 它可接受兩個變數資料類型的參數。
* **Terminate** 在處理結束時對每個彙總群組執行一次，以輸出每個群組的結果。

若要宣告正確的輸入和輸出資料類型，使用類別定義如下所示︰

```c#
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1：要累積的第一個參數
* T2：要累積的第二個參數
* TResult：傳回終止的類型

例如：

```c#
    public class GuidAggregate : IAggregate<string, int, int>
```

或

```c#
    public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>在 U-SQL 中使用 UDAGG
若要使用 UDAGG，請先在程式碼後置中加以定義，或如稍早所述從現存的可程式性 DLL 參考它。

然後使用下列語法：

```c#
    AGG<UDAGG_functionname>(param1,param2)
```

UDAGG 範例如下：

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
            guid_agg = "";
        }

        public override void Accumulate(string guid, string user)
        {
            if (user.ToUpper()== "USER1")
            {
                guid_agg += "{" + guid + "}";
            }
        }

        public override string Terminate()
        {
            return guid_agg;
        }

    }
```

以及基底 U-SQL 指令碼：

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

在此使用案例中，我們會串連特定使用者的類別 GUID。

## <a name="use-user-defined-objects-udo"></a>使用使用者定義物件：UDO
U-SQL 可讓您定義自訂可程式性物件，我們將它稱為使用者定義物件，簡稱 UDO。

以下是 U-SQL 中的 UDO 清單：

* 使用者定義擷取器
    * 逐列擷取
    * 用來實作從自訂結構化檔案擷取資料的作業

* 使用者定義輸出器
    * 逐列輸出
    * 用來輸出自訂資料類型或用來自訂檔案格式

* 使用者定義處理器
    * 擷取一列並產生一列
    * 用來減少資料行數目，或以衍生自現存資料行集的值產生新的資料行

* 使用者定義套用器
    * 擷取一列並產生 0 到 n 列
    * 與 OUTER/CROSS APPLY 搭配使用

* 使用者定義結合器
    * 結合資料列集--使用者定義 JOIN

* 使用者定義歸納器
    * 擷取 n 列並產生一列
    * 用來減少資料列數目

U-SQL 指令碼中通常會明確地呼叫 UDO 以做為下列 U-SQL 陳述式的一部分︰

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

## <a name="use-user-defined-extractors"></a>使用使用者定義擷取器
U-SQL 可讓您使用 EXTRACT 陳述式來匯入外部資料。 EXTRACT 陳述式可以使用內建的 UDO 擷取器：  

* Extractors.Text()︰可從不同編碼的分隔文字檔進行擷取。

* Extractors.Csv()︰可從不同編碼的逗號分隔值 (CSV) 檔案進行擷取。

* Extractors.Tsv()︰可從不同編碼的定位鍵分隔值 (TSV) 檔案進行擷取。

它很適合用來開發自訂擷取器。 在匯入資料期間，如果我們想要執行下列任何作業，這會很有幫助：

* 分割資料行並修改個別值，以修改輸入資料。 PROCESSOR 功能更適合用來結合資料行。
* 剖析非結構化資料 (例如網頁/電子郵件) 或半非結構化資料 (例如 XML/JSON)。
* 剖析編碼不受支援的資料。

若要定義使用者定義擷取器 (UDE)，我們需要建立 `IExtractor` 介面。 擷取器的所有輸入參數 (例如資料行/資料列分隔符號和編碼等) 都必須在類別的建構函式中加以定義。 `IExtractor` 介面也應包含 `IEnumerable<IRow>` 覆寫的定義，如下所示︰

```c#
     [SqlUserDefinedExtractor]
     public class SampleExtractor : IExtractor
     {
         public SampleExtractor(string row_delimiter, char col_delimiter)
         {
            …

         }

         public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
         {
             …
         }
     }
```

**SqlUserDefinedExtractor** 表示類型應該註冊為使用者定義擷取器。 這個類別無法繼承。

SqlUserDefinedExtractor 是 UDE 定義的選擇性屬性。 它可用來定義 UDE 物件的 AtomicFileProcessing 屬性。

* bool     AtomicFileProcessing   

* **true** = 表示此擷取器需要不可部分完成的輸入檔 (JSON、XML ...)
* **false** = 表示此擷取器可以處理分割/分散式檔案 (CSV、SEQ ...)

主要的 UDE 可程式性物件為「輸入」和「輸出」。 輸入物件用來列舉輸入資料做為 `IUnstructuredReader`。 輸入物件可用來將輸出資料設定為擷取器活動的結果。

輸入資料是透過 `System.IO.Stream` 和 `System.IO.StreamReader` 來存取。

若要列舉輸入資料行，我們必須先使用資料列分隔符號分割輸入串流。

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
    }
```

然後，將輸入資料列進一步分割為資料行組件。

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
        string[] parts = line.Split(my_column_delimiter);
               foreach (string part in parts)
        {
        …
        }
    }
```

若要設定輸出資料，我們可以使用 `output.Set` 方法。

請務必了解，自訂擷取程式只會輸出資料行和使用輸出定義的值。 設定方法呼叫。

```c#
    output.Set<string>(count, part);
```

呼叫 `yield return output.AsReadOnly();` 就會觸發實際的擷取器輸出。

以下是擷取器範例：

```c#
    [SqlUserDefinedExtractor(AtomicFileProcessing = true)]
    public class FullDescriptionExtractor : IExtractor
    {
         private Encoding _encoding;
         private byte[] _row_delim;
         private char _col_delim;

        public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
        {
             this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
             this._row_delim = this._encoding.GetBytes(row_delim);
             this._col_delim = col_delim;

        }

        public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
        {
             string line;
             //Read the input line by line
             foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
             {
                using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
                 {
                     line = streamReader.ReadToEnd().Trim();
                     //Split the input by the column delimiter
                     string[] parts = line.Split(this._col_delim);
                     int count = 0; // start with first column
                     foreach (string part in parts)
                     {
    if (count == 0)
                         {  // for column “guid”, re-generated guid
                             Guid new_guid = Guid.NewGuid();
                             output.Set<Guid>(count, new_guid);
                         }
                         else if (count == 2)
                         {
                             // for column “user”, convert to UPPER case
                             output.Set<string>(count, part.ToUpper());

                         }
                         else
                         {
                             // keep the rest of the columns as-is
                             output.Set<string>(count, part);
                         }
                         count += 1;
                     }

                 }
                 yield return output.AsReadOnly();
             }
             yield break;
         }
     }
```

在此使用案例中，擷取器會為「guid」資料行重新產生 GUID，並將「使用者」資料行的值轉換為大寫。 自訂擷取器可藉由剖析輸入資料並用它進行操作，來產生更複雜的結果。

以下為使用自訂擷取器的基底 U-SQL 指令碼：

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
    USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>使用使用者定義輸出器
使用者定義輸出器是另一個 U-SQL UDO，其可讓您擴充內建的 U-SQL 功能。 和擷取器類似，系統也有數個內建輸出器。

* Outputters.Text()︰將資料寫入不同編碼的分隔文字檔。
* Outputters.Csv()︰將資料寫入不同編碼的逗號分隔值 (CSV) 檔案。
* Outputters.Tsv()︰將資料寫入不同編碼的定位鍵分隔值 (TSV) 檔案。

自訂輸出器可讓您以自訂的定義格式寫入資料。 這可適用於下列工作︰

* 將資料寫入半結構化檔案或非結構化檔案。
* 寫入編碼不受支援的資料。
* 修改輸出資料或新增自訂屬性。

若要定義使用者定義輸出器，我們需要建立 `IOutputter` 介面。

以下是基底 `IOutputter` 類別實作︰

```c#
    public abstract class IOutputter : IUserDefinedOperator
    {
        protected IOutputter();

        public virtual void Close();
        public abstract void Output(IRow input, IUnstructuredWriter output);
    }
```

輸出器的所有輸入參數 (例如資料行/資料列分隔符號、編碼等) 都必須在類別的建構函式中加以定義。 `IOutputter` 介面也應包含 `void Output` 覆寫的定義。 可選擇性地將屬性 `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` 設定為不可部分完成檔案處理。 如需詳細資訊，請參閱下列詳細資料。

```c#
        [SqlUserDefinedOutputter(AtomicFileProcessing = true)]
        public class MyOutputter : IOutputter
        {

            public MyOutputter(myparam1, myparam2)
            {
              …
            }

            public override void Close()
            {
              …
            }

            public override void Output(IRow row, IUnstructuredWriter output)
            {
              …
            }
        }
```

* 會針對每個輸入資料列呼叫 `Output`。 它會傳回 `IUnstructuredWriter output` 資料列集。
* 建構函式類別可用來將參數傳遞至使用者定義輸出器。
* `Close` 可選擇性地覆寫，以釋出耗費資源的狀態或判斷最後一個資料列的寫入時間。

**SqlUserDefinedOutputter** 表示類型應該註冊為使用者定義輸出器。 這個類別無法繼承。

SqlUserDefinedOutputter 是使用者定義輸出器之定義的選擇性屬性。 它可用來定義 AtomicFileProcessing 屬性。

* bool     AtomicFileProcessing   

* **true** = 表示此輸出器需要不可部分完成的輸出檔 (JSON、XML ...)
* **false** = 表示此輸出器可以處理分割/分散式檔案 (CSV、SEQ ...)

主要的可程式性物件為「資料列」和「輸出」。 **列**物件可用來列舉輸出資料做為 `IRow` 介面。 **輸出**用來設定輸出資料至目標檔案。

輸出資料是透過 `IRow` 介面來存取。 一次會對輸出資料傳遞一個資料列。

藉由呼叫 IRow 介面的 Get 方法可列舉個別的值：

```c#
    row.Get<string>("column_name")
```

呼叫 `row.Schema` 即可判斷個別資料行名稱：

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

這種方法可讓您為任何中繼資料結構描述建置彈性的輸出器。

會使用 `System.IO.StreamWriter` 將輸出資料寫入檔案。 資料流參數會設為 `output.BaseStrea` 做為 `IUnstructuredWriter output` 的一部分。

請注意，必須在每個資料列反覆項目之後排清檔案的資料緩衝區。 此外，`StreamWriter` 物件必須可用於啟用可處置的屬性 (預設值) 與**使用**關鍵字︰

```c#
    using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
            {
        …
            }
```

否則，在每個反覆項目之後明確地呼叫 Flush() 方法。 我們將此顯示在下列範例中。

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>設定使用者定義輸出器的頁首和頁尾
若要設定頁首，請使用單一的反覆運算執行流程。

```c#
            public override void Output(IRow row, IUnstructuredWriter output)
            {
             …
                if (isHeaderRow)
                {
                    …                
                }

             …
                if (isHeaderRow)
                {
                    isHeaderRow = false;
                }
             …
            }
        }
```

第一個 `if (isHeaderRow)` 區塊中的程式碼只會執行一次。

針對頁尾，使用參考 `System.IO.Stream` 物件的執行個體 (`output.BaseStream`)。 在 `IOutputter` 介面的 Close() 方法中撰寫頁尾。  (如需詳細資訊，請參閱下列範例。)

以下是使用者定義 outputter 的範例︰

```c#
        [SqlUserDefinedOutputter(AtomicFileProcessing = true)]
        public class HTMLOutputter : IOutputter
        {
            // Local variables initialization
            private string row_delimiter;
            private char col_delimiter;
            private bool isHeaderRow;
            private Encoding encoding;
            private bool IsTableHeader = true;
            private Stream g_writer;

            // Parameters definition            
            public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                this.isHeaderRow = isHeader;
                this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
            }

            // The Close method is used to write the footer to the file. It's executed only once, after all rows
            public override void Close().
            {
                //Reference to IO.Stream object - g_writer
                StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
                streamWriter.Write("</table>");
                streamWriter.Flush();
                streamWriter.Close();
            }

            public override void Output(IRow row, IUnstructuredWriter output)
            {
                System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

                // Metadata schema initialization to enumerate column names
                ISchema schema = row.Schema;

                // This is a data-independent header--HTML table definition
                if (IsTableHeader)
                {
                    streamWriter.Write("<table border=1>");
                    IsTableHeader = false;
                }

                // HTML table attributes
                string header_wrapper_on = "<th>";
                string header_wrapper_off = "</th>";
                string data_wrapper_on = "<td>";
                string data_wrapper_off = "</td>";

                // Header row output--runs only once
                if (isHeaderRow)
                {
                    streamWriter.Write("<tr>");
                    for (int i = 0; i < schema.Count(); i++)
                    {
                        var col = schema[i];
                        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
                    }
                    streamWriter.Write("</tr>");
                }

                // Data row output
                streamWriter.Write("<tr>");                
                for (int i = 0; i < schema.Count(); i++)
                {
                    var col = schema[i];
                    string val = "";
                    try
                    {
                        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
                        switch (col.Type.Name.ToString().ToLower())
                        {
                            case "string": val = row.Get<string>(col.Name).ToString(); break;
                            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
                            default: break;
                        }
                    }
                    // Handling NULL values--keeping them empty
                    catch (System.NullReferenceException)
                    {
                    }
                    streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
                }
                streamWriter.Write("</tr>");

                if (isHeaderRow)
                {
                    isHeaderRow = false;
                }
                // Reference to the instance of the IO.Stream object for footer generation
                g_writer = output.BaseStream;
                streamWriter.Flush();
            }
        }

        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

以及 U-SQL 基底指令碼：

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
    USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

這是 HTML 輸出器，會以資料表資料建立 HTML 檔案。

### <a name="call-outputter-from-u-sql-base-script"></a>從 U-SQL 基底指令碼呼叫輸出器
若要從基底 U-SQL 指令碼呼叫自訂輸出器，您必須為輸出器物件建立新的執行個體。

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

若不要在基底指令碼中建立物件執行個體，我們可以建立函式的包裝函式，如先前的範例所示︰

```c#
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

在此案例中，原始呼叫看起來像：

```sql
OUTPUT @rs0 TO @output_file USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>使用使用者定義處理器
使用者定義處理器 (簡稱 UDP) 是一種 U-SQL UDO，可藉由套用可程式性功能處理傳入的資料列。 UDP 可讓您視需要結合資料行、修改值，以及新增資料行。 基本上，它能夠協助您處理資料列集，以產生所需的資料元素。

若要定義 UDP，我們必須以 `SqlUserDefinedProcessor` 屬性 (這是 UDP 的選擇性屬性) 建立 `IProcessor`。

此介面應該包含 `IRow` 介面資料列集覆寫的定義，如下列範例所示︰

```c#
    [SqlUserDefinedProcessor]
     public class MyProcessor: IProcessor
     {
        public override IRow Process(IRow input, IUpdatableRow output)
         {
            …
         }
     }
```

**SqlUserDefinedProcessor** 表示類型應該註冊為使用者定義處理器。 這個類別無法繼承。

SqlUserDefinedProcessor 是 UDP 定義的**選擇性**屬性。

主要的可程式性物件為「輸入」和「輸出」。 輸入物件可用來列舉輸入資料欄和輸出，以將輸出資料設定為處理器活動的結果。

若要列舉輸入資料行，我們可以使用 `input.Get` 方法。

```c#
    string column_name = input.Get<string>("column_name");
```

`input.Get` 方法的參數是傳遞做為 U-SQL 基底指令碼之 `PROCESS` 陳述式內 `PRODUCE` 子句一部分的資料行。 在此，我們必須使用正確的資料類型。

針對輸出，使用 `output.Set` 方法。

請務必注意，自訂產生程式只會輸出資料行和使用 `output.Set` 方法呼叫定義的值。

```c#
    output.Set<string>("mycolumn", mycolumn);
```

呼叫 `return output.AsReadOnly();` 就會觸發實際的處理器輸出。

以下是處理器範例：

```c#
     [SqlUserDefinedProcessor]
     public class FullDescriptionProcessor : IProcessor
     {
        public override IRow Process(IRow input, IUpdatableRow output)
         {
             string user = input.Get<string>("user");
             string des = input.Get<string>("des");
             string full_description = user.ToUpper() + "=>" + des;
             output.Set<string>("dt", input.Get<string>("dt"));
             output.Set<string>("full_description", full_description);
             output.Set<Guid>("new_guid", Guid.NewGuid());
             output.Set<Guid>("guid", input.Get<Guid>("guid"));
             return output.AsReadOnly();
         }
     }
```

在此使用案例中，處理器會結合現存資料行 (在此使用案例中為大寫的「user」和「des」) 來產生稱為「full_description」的新資料行。 它也會重新產生 GUID，並傳回原始及新的 GUID 值。

如您在上一個範例中所看到，您可以在 `output.Set` 方法呼叫期間呼叫 C# 方法。

以下是使用自訂處理器的基底 U-SQL 指令碼範例：

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>使用使用者定義套用器
U-SQL 使用者定義套用器可讓您針對查詢的外部資料表運算式所傳回的每個資料列，叫用自訂 C# 函式。 系統會針對左邊輸入中的每個資料列評估右邊輸入，然後結合所產生的資料列來產生最終輸出。 APPLY 運算子所產生的資料行清單是左邊輸入和右邊輸入之資料行集的組合。

U-SQL SELECT 運算式中會叫用使用者定義套用器。

使用者定義套用器的典型呼叫看起來如下所示︰

```sql
    SELECT …
    FROM …
    CROSS APPLYis used to pass parameters
    new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

如需在 SELECT 運算式中使用套用器的詳細資訊，請參閱[從 CROSS APPLY 和 OUTER APPLY 選取的 U-SQL SELECT](https://msdn.microsoft.com/library/azure/mt621307.aspx)。

使用者定義套用器的基底類別定義如下所示：

```c#
    public abstract class IApplier : IUserDefinedOperator
    {
        protected IApplier();

        public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
    }
```

若要定義使用者定義套用器，我們必須以 [`SqlUserDefinedCombiner`] 屬性 (這是使用者定義套用器之定義的選擇性屬性) 建立 `IApplier` 介面。

```c#
    [SqlUserDefinedApplier]
    public class ParserApplier : IApplier
    {

        public ParserApplier()
        {
        …
        }

        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
        {
        …
        }
    }
```

* 會針對外部資料表的每個資料列呼叫 Apply。 它會傳回 `IUpdatableRow` 輸出資料列集。
* 建構函式類別可用來將參數傳遞至使用者定義套用器。

**SqlUserDefinedApplier** 表示類型應該註冊為使用者定義套用器。 這個類別無法繼承。

**SqlUserDefinedApplier** 是使用者定義套用器定義的**選擇性**。


主要的可程式性物件如下所示︰

```c#
        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

輸入資料列集會傳遞做為 `IRow` 輸入。 輸出資料列會產生為 `IUpdatableRow` 輸出介面。

呼叫 `IRow` 結構描述方法即可判斷個別資料行名稱。

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

若要從傳入的 `IRow` 取得實際資料值，我們可以使用 `IRow` 介面的 Get() 方法。

```c#
    mycolumn = row.Get<int>("mycolumn")
```

或者，我們使用結構描述資料行名稱︰

```c#
    row.Get<int>(row.Schema[0].Name)
```

輸出值必須使用 `IUpdatableRow` 輸出進行設定：

```c#
    output.Set<int>("mycolumn", mycolumn)
```

請務必了解，自訂套用器只會輸出以 `output.Set` 方法呼叫定義的資料行和值。

呼叫 `yield return output.AsReadOnly();` 就會觸發實際的輸出。

使用者定義套用器參數可以傳遞給建構函式。 在基底 U-SQL 指令碼的套用器呼叫期間，套用器可傳回數目不定且需要加以定義的資料行。

```c#
  new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

以下是使用者定義套用器的範例︰

```c#
   [SqlUserDefinedApplier]
    public class ParserApplier : IApplier
    {
        private string parsingPart;

        public ParserApplier(string parsingPart)
        {
            if (parsingPart.ToUpper().Contains("ALL")
                || parsingPart.ToUpper().Contains("MAKE")
                || parsingPart.ToUpper().Contains("MODEL")
                || parsingPart.ToUpper().Contains("YEAR")
                || parsingPart.ToUpper().Contains("TYPE")
                || parsingPart.ToUpper().Contains("MILLAGE")
                )
            {
                this.parsingPart = parsingPart;
            }
            else
            {
                throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
            }
        }

        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
        {

            string[] properties = input.Get<string>("properties").Split(',');

            //  only process with correct number of properties
            if (properties.Count() == 5)
            {

                string make = properties[0];
                string model = properties[1];
                string year = properties[2];
                string type = properties[3];
                int millage = -1;

                // Only return millage if it is number, otherwise, -1
                if (!int.TryParse(properties[4], out millage))
                {
                    millage = -1;
                }

                if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
                if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
                if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
                if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
                if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
            }
            yield return output.AsReadOnly();            
        }
    }
```

以下是此使用者定義套用器的基底 U-SQL 指令碼：

```sql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

在此使用案例中，使用者定義套用器可做為車隊屬性的逗號分隔值剖析器。 輸入檔資料列看起來如下所示︰

```
103    Z1AB2CD123XY45889    Ford,Explorer,2005,SUV,152345
303    Y0AB2CD34XY458890    Shevrolet,Cruise,2010,4Dr,32455
210    X5AB2CD45XY458893    Nissan,Altima,2011,4Dr,74000
```

這是典型的定位點分隔符號 TSV 檔案，其屬性資料行內含製造商、型號等汽車屬性。 這些屬性必須剖析到資料表資料行。 所提供的套用者也可讓您根據傳遞的參數，在結果資料列集中產生屬性的動態數字。 您可以產生所有屬性或僅一組特定的內容。

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

使用者定義套用器可以呼叫做為套用器物件的新執行個體：

```c#
    CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

或透過叫用包裝函式 Factory 方法來呼叫：

```c#
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>使用使用者定義結合器
使用者定義結合器 (簡稱 UDC) 可讓您根據自訂邏輯結合左邊和右邊資料列集中的資料列。 使用者定義結合器會與 COMBINE 運算式搭配使用。

結合器會使用 COMBINE 運算式來叫用，以提供關於輸入資料列集、群組資料行、預期的結果結構描述和其他資訊的必要資訊。

若要在基底 U-SQL 指令碼中呼叫結合器，我們可以使用下列語法：

```sql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

如需詳細資訊，請參閱 [COMBINE 運算式 (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx)。

若要定義使用者定義結合器，我們必須以 [`SqlUserDefinedCombiner`] 屬性 (這是使用者定義結合器之定義的選擇性屬性) 建立 `ICombiner` 介面。

基底 `ICombiner` 類別定義：

```c#
    public abstract class ICombiner : IUserDefinedOperator
    {
        protected ICombiner();
        public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
               IUpdatableRow output);
        public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
               IUpdatableRow output);
    }
```

`ICombiner` 介面的自訂實作應該包含 `IEnumerable<IRow>` 結合覆寫的定義。

```c#
    [SqlUserDefinedCombiner]
    public class MyCombiner : ICombiner
    {

        public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output)
        {
        …
        }
    }
```

**SqlUserDefinedCombiner** 表示類型應該註冊為使用者定義結合器。 這個類別無法繼承。

**SqlUserDefinedCombiner** 用來定義合併程式模式屬性。 它是使用者定義結合器定義的選擇性屬性。

CombinerMode     Mode

CombinerMode 列舉可以採用下列值︰

* 完整 (0) 每個輸出資料列可能相依於所有具有相同金鑰值的左邊和右邊輸入資料列。

* 左邊 (1) 每個輸出資料列相依於左邊的單一輸入資料列 (並可能相依於所有具有相同金鑰值的右邊資料列)。

* 右邊 (2) 每個輸出資料列相依於右邊的單一輸入資料列 (並可能相依於所有具有相同金鑰值的左邊資料列)。

* 內部 (3) 每個輸出資料列相依於具有相同值的左邊和右邊單一輸入資料列。

範例：     [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


主要的可程式性物件為：

```c#
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

輸入資料列集會傳遞做為介面的「左邊」和「右邊」 `IRowset` 類型。 這兩個資料列集必須列舉以進行處理。 您僅可以允許每個介面列舉一次，因此我們必須在必要時加以列舉和快取。

若要快取，我們可以透過執行 LINQ 查詢來建立記憶體結構的 List\<T\> 類型，特別是 List<`IRow`>。 列舉期間也可以使用匿名資料類型。

如需 LINQ 查詢的詳細資訊，請參閱 [LINQ 查詢 (C#) 簡介](https://msdn.microsoft.com/library/bb397906.aspx)，如需 IEnumerable\<T\> 介面的詳細資訊，請參閱 [IEnumerable\<T\> 介面](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx)。

若要從傳入的 `IRowset` 取得實際資料值，我們可以使用 `IRow` 介面的 Get() 方法。

```c#
    mycolumn = row.Get<int>("mycolumn")
```

呼叫 `IRow` 結構描述方法即可判斷個別資料行名稱。

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

或者，使用結構描述資料行名稱︰

```
    c# row.Get<int>(row.Schema[0].Name)
```

LINQ 的一般列舉看起來如下所示︰

```c#
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

列舉這兩個資料列集之後，我們將所有資料列執行迴圈。 針對資料列集左邊的每個資料列，我們要尋找符合我們合併程式條件的所有資料列。

輸出值必須使用 `IUpdatableRow` 輸出進行設定。

```c#
    output.Set<int>("mycolumn", mycolumn)
```

呼叫 `yield return output.AsReadOnly();` 就會觸發實際的輸出。

以下是結合器範例：

```c#
    [SqlUserDefinedCombiner]
    public class CombineSales : ICombiner
    {

        public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output)
        {
            var internetSales =
            (from row in left.Rows
                          select new
                          {
                              ProductKey = row.Get<int>("ProductKey"),
                              OrderDateKey = row.Get<int>("OrderDateKey"),
                              SalesAmount = row.Get<decimal>("SalesAmount"),
                              TaxAmt = row.Get<decimal>("TaxAmt")
                          }).ToList();

            var resellerSales =
            (from row in right.Rows
             select new
             {
                 ProductKey = row.Get<int>("ProductKey"),
                 OrderDateKey = row.Get<int>("OrderDateKey"),
                 SalesAmount = row.Get<decimal>("SalesAmount"),
                 TaxAmt = row.Get<decimal>("TaxAmt")
             }).ToList();

            foreach (var row_i in internetSales)
            {
                foreach (var row_r in resellerSales)
                {

                    if (
                        row_i.OrderDateKey > 0
                        && row_i.OrderDateKey < row_r.OrderDateKey
                        && row_i.OrderDateKey == 20010701
                        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
                    {
                        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
                        output.Set<int>("ProductKey", row_i.ProductKey);
                        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
                        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
                    }

                }
            }
            yield return output.AsReadOnly();
        }
    }
```

在此使用案例中，我們會建置零售商的分析報告。 目標是要尋找成本超過 20,000 美元，且特定時間範圍內透過網站的銷售速度快過一般零售商的所有產品。

以下是基底 U-SQL 指令碼。 您可以比較一般 JOIN 與結合器之間的邏輯︰

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

使用者定義結合器可以呼叫做為套用器物件的新執行個體︰

```c#
    USING new MyNameSpace.MyCombiner();
```


或透過叫用包裝函式 Factory 方法來呼叫：

```c#
    USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>使用使用者定義歸納器
U-SQL 可讓您藉由實作 IReducer 介面，在 C# 中使用使用者定義運算子擴充性架構撰寫自訂的資料列集歸納器。

使用者定義歸納器 (簡稱 UDR) 可用來在資料擷取 (匯入) 期間消除不必要的資料列。 也可以用來操作和評估資料列和資料行。 根據程式設計邏輯，它也可以定義需要擷取哪些資料列。

若要定義 UDR 類別，我們需要使用選擇性的 `SqlUserDefinedReducer` 屬性建立 `IReducer` 介面。

此類別介面應該包含 `IEnumerable` 介面資料列集覆寫的定義。

```c#
        [SqlUserDefinedReducer]
        public class EmptyUserReducer : IReducer
        {

            public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
            {
            …
            }

        }
```

**SqlUserDefinedReducer** 表示類型應該註冊為使用者定義歸納器。 這個類別無法繼承。
**SqlUserDefinedReducer** 是使用者定義歸納器之定義的選擇性屬性。 它可用來定義 IsRecursive 屬性。

* bool     IsRecursive    
* **true** = 表示此歸納器是否等冪

主要的可程式性物件為「輸入」和「輸出」。 輸入物件用來列舉輸入資料列。 輸出用來設定減少活動結果的輸出資料列。

若要列舉輸入資料列，我們可以使用 `Row.Get` 方法。

```c#
            foreach (IRow row in input.Rows)
            {
                        row.Get<string>("mycolumn");
            }
```

`Row.Get` 方法的參數是傳遞做為 U-SQL 基底指令碼之 `REDUCE` 陳述式內 `PRODUCE` 類別一部分的資料行。 在此，我們也必須使用正確的資料類型。

針對輸出，使用 `output.Set` 方法。

請務必了解，自訂歸納器只會輸出以 `output.Set` 方法呼叫定義的值。

```c#
    output.Set<string>("mycolumn", guid);
```

呼叫 `yield return output.AsReadOnly();` 就會觸發實際的歸納器輸出。

以下是歸納器範例：

```c#
        [SqlUserDefinedReducer]
        public class EmptyUserReducer : IReducer
        {

            public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
            {
                string guid;
                DateTime dt;
                string user;
                string des;

                foreach (IRow row in input.Rows)
                {
                    guid = row.Get<string>("guid");
                    dt = row.Get<DateTime>("dt");
                    user = row.Get<string>("user");
                    des = row.Get<string>("des");

                    if (user.Length > 0)
                    {
                        output.Set<string>("guid", guid);
                        output.Set<DateTime>("dt", dt);
                        output.Set<string>("user", user);
                        output.Set<string>("des", des);

                        yield return output.AsReadOnly();
                    }
                }
            }

        }
```

在此使用案例中，歸納器會略過使用者名稱空白的資料列。 針對資料列集中的每個資料列，它會讀取每個必要的資料行，然後評估使用者名稱的長度。 只有當使用者名稱值的長度大於 0 時，它才會輸出實際的資料列。

以下為使用自訂歸納器的基底 U-SQL 指令碼：

```sql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```

