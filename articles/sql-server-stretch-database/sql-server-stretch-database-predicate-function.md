<properties
	pageTitle="使用篩選述詞來選取要移轉的資料列 (Stretch Database) | Microsoft Azure"
	description="了解如何使用篩選述詞來選取要移轉的資料列。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# 使用篩選述詞來選取要移轉的資料列 (Stretch Database)

如果您將歷程資料儲存在個別的資料表上，您可以設定 Stretch Database 以移轉整個資料表。相反地，如果您的資料表同時包含目前和歷程資料，您可以指定篩選述詞以選取要移轉的資料列。篩選述詞是嵌入資料表值函式。本主題說明如何撰寫嵌入資料表值函式以選取要移轉的資料列。

>   [AZURE.NOTE] 如果您提供執行不良的篩選述詞，則資料移轉也會執行不良。Stretch Database 使用 CROSS APPLY 運算子將篩選述詞套用至資料表。

如果您不指定篩選述詞，便會移轉整個資料表。

當您執行「為資料庫啟用延展功能精靈」時，可以移轉整個資料表，也可以在精靈中指定簡單的述詞。如果您想要使用不同類型的篩選述詞來選取要移轉的資料列，請執行下列其中一項操作。

-   結束精靈，然後執行 ALTER TABLE 陳述式來啟用資料表的 Stretch 以及指定述詞。

-   結束精靈之後，請執行 ALTER TABLE 陳述式來指定述詞。

本主題後面會說明用於加入述詞的 ALTER TABLE 語法。

## 篩選述詞的基本需求
Stretch Database 篩選述詞所需的嵌入資料表值函式看起來如下列範例所示。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE <predicate>
```
函式的參數必須是來自資料表的資料欄識別碼。

需要結構描述繫結，以避免篩選述詞使用的資料欄被卸除或改變。

### 傳回值
如果函式傳回非空白結果，則該資料列便符合移轉資格。否則，在函式未傳回結果的情況下，便代表該資料列不符合移轉資格。

### 條件
&lt;述詞&gt; 可以包含一個條件，或是包含以 AND 邏輯運算子結合的數個條件。

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
每個條件皆可以包含一個基本條件，或是包含以 OR 邏輯運算子結合的數個基本條件。

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### 基本條件
基本條件可以執行下列其中一個比較。

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   比較函式參數及常數運算式。例如，`@column1 < 1000`。

    以下範例會檢查 *date* 資料行的值是否為 &lt; 1/1/2016。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   將 IS NULL 或 IS NOT NULL 運算子套用至函式參數。

-   使用 IN 運算子來比較函式參數及常數值清單。

    以下範例會檢查 *shipment\_status* 資料行的值是否為 `IN (N'Completed', N'Returned', N'Cancelled')`。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

### 比較運算子
支援下列比較運算子。

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### 常數運算式
您在篩選述詞中使用的常數可以式任何可在定義函式時評估之具決定性的運算式。常數運算式可以包含下列項目。

-   常值。例如，`N’abc’, 123`。

-   代數運算式。例如，`123 + 456`。

-   具決定性的函式。例如，`SQRT(900)`。

-   使用 CAST 或 CONVERT 之具決定性的轉換。例如，`CONVERT(datetime, '1/1/2016', 101)`。

### 其他運算式
在您將 BETWEEN 和 NOT BETWEEN 運算子取代為對等的 AND 和 OR 運算式之後，如果產生的述詞符合此處所述的規則，便代表您可以使用 BETWEEN 和 NOT BETWEEN 運算子。

您不能使用子查詢或不具決定性的函式，例如 RAND() 或 GETDATE()。

## 將篩選式詞新增到資料表
透過執行 ALTER TABLE 陳述式並將現有的嵌入資料表值函式指定為 FILTER\_PREDICATE 參數的值，來將篩選述詞新增到資料表。例如：

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
) )
```
在您將函式做為述詞繫結至資料表之後，下列項目皆會成立。

-   發生下一次的資料移轉時，只有其函式傳回非空白值的資料列會被移轉。

-   函式所使用的資料欄皆為結構描述繫結。只要有資料表將該函式做為其篩選述詞使用，您便無法變更這些資料欄。

只要有資料表將該函式做為其篩選述詞使用，您便無法卸除嵌入資料表值函式。

>   [AZURE.NOTE] 若要改善篩選函數的效能，請在函數使用的資料行上建立索引。

### 將資料行名稱傳遞給篩選述詞
當您指派篩選函數給資料表時，請使用一段式名稱來指定傳遞給篩選函數的資料行名稱。如果您在傳遞資料行名稱時指定的是三段式名稱，後續針對已啟用延展功能之資料表的查詢將會失敗。

例如，如果您指定三段式資料行名稱 (如以下範例所示)，陳述式將會執行成功，但後續針對資料表的查詢將會失敗。

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

請改為使用一段式資料行名稱來指定篩選函數，如以下範例所示。

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="addafterwiz"></a>在執行精靈後新增篩選述詞  

如果您想要使用無法在「為資料庫啟用延展功能」精靈中建立的述詞，您可以在結束精靈後，執行 ALTER TABLE 陳述式來指定述詞。不過，您必須先停止已經在進行中的資料移轉並回復已移轉的資料，才能套用述詞。(如需有關為什麼必須這麼做的詳細資訊，請參閱[取代現有的篩選述詞](#replacePredicate)。

1. 反轉移轉方向並回復已經移轉的資料。開始這項作業之後，即無法將其取消。您也會因輸出資料傳輸 (輸出) 而在 Azure 上產生費用。如需詳細資訊請參閱 [Azure 定價機制](https://azure.microsoft.com/pricing/details/data-transfers/)。  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  

2. 等待移轉完成。您可以在 SQL Server Management Studio 的 [Stretch Database 監視] 中或查詢 **sys.dm\_db\_rda\_migration\_status** 檢視，來查看狀態。如需詳細資訊，請參閱[資料移轉的監視及疑難排解](sql-server-stretch-database-monitor.md)或 [sys.dm\_db\_rda\_migration\_status](https://msdn.microsoft.com/library/dn935017.aspx)。

3. 建立您想要套用到資料表的篩選述詞。

4. 將述詞新增到資料表，然後重新啟動資料移轉來移轉到 Azure。

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## 依日期篩選資料列
下列範例會移轉 **date** 資料行所含的值在 2016 年 1 月 1 日之前的資料列。

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## 依狀態資料行中的值篩選資料列
下列範例會移轉 **status** 資料行所含的值是其中一個指定值的資料列。

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## 使用滑動視窗篩選資料列
若要使用滑動視窗來篩選資料列，請記住 filter 函式的下列需求。

-   此函式必須具有決定性。因此，經過一段時間之後，您無法建立會自動重新計算滑動視窗的函式。

-   此函式會使用結構描述繫結。因此，呼叫 ALTER FUNCTION 來移動滑動視窗，並無法每天僅「就地」更新函式。

請從類似下列範例的篩選述詞著手，此篩選述詞會移轉 **systemEndTime** 資料行所含的值在 2016 年 1 月 1 日之前的資料列。

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

將篩選述詞套用至資料表。

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

當您想要更新滑動視窗時，請執行下列動作。

1.  建立可指定新滑動視窗的新函式。下列範例會選取 2016 年 1 月 2 日之前的日期，而不是 2016 年 1 月 1 日。

2.  呼叫 ALTER TABLE，以將先前的篩選述詞取代為新的篩選述詞 (如下列範例所示)。

3. 選擇性呼叫 DROP FUNCTION 來卸除不再使用的舊篩選函式(在此範例中，未顯示此步驟)。

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## 更多的有效篩選述詞範例

-   下列範例使用 AND 邏輯運算子結合兩個基本條件。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   下列範例使用數個條件和使用 CONVERT 之具決定性的轉換。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   下列範例使用數學運算子和函式。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   下列範例使用 BETWEEN 和 NOT BETWEEN 運算子。此使用方法有效，因為在您將 BETWEEN 和 NOT BETWEEN 運算子取代為對等的 AND 和 OR 運算式之後，所產生的述詞符合此處所述的規則。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 BETWEEN 0 AND 100
    			AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    在您將 BETWEEN 和 NOT BETWEEN 運算子取代為對等的 AND 和 OR 運算式之後，前面的函式與後面的函式相等。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## 無效的篩選述詞範例

-   下列函式無效，因為它包含不具決定性的轉換。

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   下列函式無效，因為它包含不具決定性的函式呼叫。

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   下列函式無效，因為它包含子查詢。

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   下列函式無效，因為使用代數運算子或內建函式的運算式必須在您定義函式時評估為常數。您無法包含以代數運算式或函式呼叫呈現的資料欄參考。

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE SQRT(@column1) = 30
    GO
    ```

-   下列函式無效，因為它在您將 BETWEEN 運算子取代為對等的 AND 運算式之後，違反此處所述的規則。

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    在您將 BETWEEN 運算子取代為對等的 AND 運算式之後，前面的函式與後面的函式相等。此含式無效，因為主要條件只能使用 OR 邏輯運算子。

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## Stretch Database 套用篩選述詞的方法
Stretch Database 使用 CROSS APPLY 運算子將篩選述詞套用至資料表，並決定合格的資料列。例如：

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
如果函式針對資料列傳回非空白結果，便代表該資料列符合移轉資格。

## <a name="replacePredicate"></a>取代現有的篩選述詞
您可以透過再次執行 ALTER TABLE 陳述式並為 FILTER\_PREDICATE 參數指定新的值，來取代先前指定的篩選述詞。例如：

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
```
新的嵌入資料表值函式擁有下列需求。

-   新函式必須比先前函式含有較少限制。

-   所有舊函式中的運算子皆必須存在於新函式中。

-   新函數不能包含舊函數中沒有的運算子。

-   不能變更運算子引數的順序。

-   只能變更 `<, <=, >, >=` 比較中所含的常數值來讓述詞限制變得較不嚴格。

### 有效取代的範例
假設下列函式為目前的篩選述詞。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
下列函式為有效取代，因為新的日期常數 (指定之後的截止日期) 能使述詞含有較少限制。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
			AND (@column2 < -50 OR @column2 > 50)
GO
```

### 無效取代的範例
下列函式不是有效取代，因為新的日期常數 (指定之前的截止日期) 不能使述詞含有較少限制。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
下列函式不是有效取代，因為其中一個比較運算子已被移除。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -50)
GO
```
下列函式不是有效取代，因為已新增包含 AND 邏輯運算子的新條件。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
			AND (@column2 <> 0)
GO
```

## 從資料表移除篩選述詞
若要移轉整個資料表，而非選取的資料列，請透過將現有的 FILTER\_PREDICATE 設定為 Null 來移除它。例如：

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = NULL,
	MIGRATION_STATE = <desired_migration_state>
) )
```
當您移除篩選述詞之後，資料表中的所有資料列皆符合移轉資格。因此，除非您先從 Azure 回復資料表的所有遠端資料，否則稍後無法指定相同資料表的篩選述詞。這項限制是要避免下列情況：提供新篩選述詞時不適合進行移轉的資料列已移轉至 Azure。

## 查看套用至資料表的篩選述詞
若要檢查套用至資料表的篩選述詞，請開啟目錄檢視 **sys.remote\_data\_archive\_tables** 並查看 **filter\_predicate** 資料行的值。如果值為 Null，便代表整個資料表皆符合封存資格。如需詳細資訊，請參閱 [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)。

## 篩選述詞的安全性注意事項  
遭入侵的帳戶若具備 db\_owner 權限，便能夠執行下列操作。

-   建立並套用耗用大量伺服器資源或長期等待而導致阻斷服務的資料表值函數。  

-   建立並套用可能推斷出已明確拒絕使用者讀取之資料表內容的資料表值函數。

## 另請參閱

[ALTER TABLE (TRANSACT-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0615_2016-->