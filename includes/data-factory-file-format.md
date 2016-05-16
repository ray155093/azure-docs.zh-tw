### 指定 TextFormat

如果格式設為 **TextFormat**，您可以在 **Format** 區段中指定下列**選擇性**屬性。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| columnDelimiter | 在檔案中做為資料行分隔符號的字元。目前只允許一個字元。此標記是選擇性的。預設值是逗號 (,)。 | 否 |
| rowDelimiter | 在檔案中做為資料列分隔符號的字元。目前只允許一個字元。此標記是選擇性的。預設值是下列任一項：[“\\r\\n”, “\\r”,” \\n”]。 | 否 |
| escapeChar | 用來逸出內容中顯示之資料行分隔符號的特殊字元。此標記是選擇性的。沒有預設值。您為此屬性指定的字元不得超過一個。<br/><br/>例如，如果您以逗號 (,) 作為資料行分隔符號，但您想要在文字中使用逗號字元 (例如：“Hello, world”)，您可以定義 ‘$’ 作為逸出字元，並在來源中使用字串 “Hello$, world”。<br/><br/>請注意，您無法同時為資料表指定 escapeChar 和 quoteChar。 | 否 | 
| quoteChar | 用來引用字串值的特殊字元。引號字元內的資料行和資料列分隔符號會被視為字串值的一部分。此標記是選擇性的。沒有預設值。您為此屬性指定的字元不得超過一個。<br/><br/>例如，如果您以逗號 (,) 做為資料行分隔符號，但您想要在文字中使用逗號字元 (例如：<Hello  world>)，您可以定義 ‘"’ 做為引用字元，並在來源中使用字串 <"Hello, world">。這個屬性同時適用於輸入和輸出資料表。<br/><br/>請注意，您無法同時為資料表指定 escapeChar 和 quoteChar。 | 否 |
| nullValue | 用來代表 Blob 檔案內容中 null 值的字元。此標記是選擇性的。預設值為 “\\N”。<br/><br/>例如，根據上述範例，Blob 中的 “NaN” 會在複製到 SQL Server 時轉換成 Null 值。 | 否 |
| encodingName | 指定編碼名稱。如需有效編碼名稱的清單，請參閱：[Encoding.EncodingName 屬性](https://msdn.microsoft.com/library/system.text.encoding.aspx)。例如：windows-1250 或 shift\_jis。預設值為 UTF-8。 | 否 | 

#### TextFormat 範例
下列範例顯示 TextFormat 的一些格式屬性。

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

若要使用 escapeChar 而不是 quoteChar，請以下列內容取代具有 quoteChar 的行：

	"escapeChar": "$",

### 指定 AvroFormat
如果格式設為 AvroFormat，您不需要在 typeProperties 區段內的 Format 區段中指定任何屬性。範例：

	"format":
	{
	    "type": "AvroFormat",
	}

若要在 Hive 資料表中使用 Avro 格式，您可以參考 [Apache Hive 的教學課程](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)。

### 指定 JsonFormat

如果格式設定為 **JsonFormat**，您可以在 **Format** 區段中指定下列**選擇性**屬性。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| filePattern | 表示每個 JSON 檔案中儲存的資料模式。允許的值為︰**setOfObjects** 和 **arrayOfObjects**。**預設**值為：**setOfObjects**。如需這些模式的詳細資料，請參閱下列各節。| 否 |
| encodingName | 指定編碼名稱。如需有效編碼名稱的清單，請參閱：[Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) 屬性。例如：windows-1250 或 shift\_jis。**預設**值為 **UTF-8**。 | 否 | 
| nestingSeparator | 用來分隔巢狀層級的字元。**預設**值為 **. (點)**。 | 否 | 


#### setOfObjects 檔案模式

每個檔案都會包含單一物件，或以行分隔/串連的多個物件。在輸出資料集中選擇此選項時，copy 將會產生單一 JSON 檔案，每行一個物件 (以行分隔)。

**單一物件**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}

**以行分隔的 JSON**

	{"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
	{"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
	{"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**串連的 JSON**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}
	{
		"time": "2015-04-29T07:13:21.0220000Z",
		"callingimsi": "466922202613463",
		"callingnum1": "123436380",
		"callingnum2": "789037573",
		"switch1": "US",
		"switch2": "UK"
	}
	{
		"time": "2015-04-29T07:13:21.4370000Z",
		"callingimsi": "466923101048691",
		"callingnum1": "678901578",
		"callingnum2": "345626404",
		"switch1": "Germany",
		"switch2": "UK"
	}


#### arrayOfObjects 檔案模式。 

每個檔案都會包含物件的陣列。

	[
	    {
	        "time": "2015-04-29T07:12:20.9100000Z",
	        "callingimsi": "466920403025604",
	        "callingnum1": "678948008",
	        "callingnum2": "567834760",
	        "switch1": "China",
	        "switch2": "Germany"
	    },
	    {
	        "time": "2015-04-29T07:13:21.0220000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789037573",
	        "switch1": "US",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:21.4370000Z",
	        "callingimsi": "466923101048691",
	        "callingnum1": "678901578",
	        "callingnum2": "345626404",
	        "switch1": "Germany",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "789037573",
	        "callingnum2": "789044691",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789044691",
	        "switch1": "US",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:24.2120000Z",
	        "callingimsi": "466922201102759",
	        "callingnum1": "345698602",
	        "callingnum2": "789097900",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:25.6320000Z",
	        "callingimsi": "466923300236137",
	        "callingnum1": "567850552",
	        "callingnum2": "789086133",
	        "switch1": "China",
	        "switch2": "Germany"
	    }
	]

### JsonFormat 範例

如果您的 JSON 檔案含有下列內容：

	{
		"Id": 1,
		"Name": {
			"First": "John",
			"Last": "Doe"
		},
		"Tags": ["Data Factory”, "Azure"]
	}

而且您想要使用下列格式，將它複製到 Azure SQL 資料表︰

識別碼 | Name.First | Name.Middle | Name.Last | 標記
--- | ---------- | ----------- | --------- | ----
1 | John | null | Doe | ["Data Factory”, "Azure"]

JsonFormat 類型的輸入資料集的定義如下：(僅含有相關組件的部分定義)

	"properties": {
		"structure": [
			{"name": "Id", "type": "Int"},
			{"name": "Name.First", "type": "String"},
			{"name": "Name.Middle", "type": "String"},
			{"name": "Name.Last", "type": "String"},
			{"name": "Tags", "type": "string"}
		],
		"typeProperties":
		{
			"folderPath": "mycontainer/myfolder",
			"format":
			{
				"type": "JsonFormat",
				"filePattern": "setOfObjects",
				"encodingName": "UTF-8",
				"nestingSeparator": "."
			}
		}
	}

如果未定義結構，複製活動預設會簡維結構，並複製每個項目。

#### 支援的 JSON 結構
請注意：

- 每個含有名稱/值組集合的物件將會對應到資料 (表格格式) 的某一個資料列。物件可以是巢狀的，而您可以定義預設要在資料集中使用巢狀分隔字元 (.) 來簡維結構的方式。如需範例，請參閱上述的 [JsonFormat 範例](#jsonformat-example)一節。  
- 如果未在 Data Factory 資料集中定義結構，複製活動即會偵測到第一個物件的結構描述，並簡維整個物件。 
- 如果 JSON 輸入具有陣列，複製活動就會將整個陣列值轉換為字串。您可以使用[資料行對應或篩選](#column-mapping-with-translator-rules)，選擇略過它。
- 如果相同層級中有重複的名稱，複製活動將會挑選最後一個。
- 屬性名稱會區分大小寫。名稱相同但大小寫不同的兩個屬性會被視為兩個不同的屬性。 

