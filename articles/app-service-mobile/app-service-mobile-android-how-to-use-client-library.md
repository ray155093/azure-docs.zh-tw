<properties
	pageTitle="如何使用 Android Mobile Apps 的用戶端程式庫"
	description="如何使用適用於 Azure Mobile Apps 的 Android 用戶端 SDK"
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/23/2016"
	ms.author="adrianha"/>


# 如何使用 Mobile Apps 的 Android 用戶端程式庫

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南說明如何使用適用於 Mobile Apps 的 Android 用戶端 SDK 來實作常見案例，例如：

- 查詢資料 (插入、更新和刪除)。
- 驗證。
- 處理錯誤。
- 自訂用戶端。

它還會深入探討大部分行動應用程式中使用的常見用戶端程式碼。

本指南著重於用戶端 Android SDK。若要深入了解 Mobile Apps 的伺服器端 SDK，請參閱[使用 .NET 後端][10]或[如何使用 Node.js 後端 SDK][11]。

## 參考文件

您可以在 [GitHub][12] 找到 Android 用戶端程式庫的 Javadocs API 參考。

## 支援的平台

Azure Mobile Apps 的 Android SDK 支援 API 層級 19 到 24 (KitKat 到 Nougat)。

「伺服器流程」驗證在呈現的 UI 中使用 WebView。如果裝置無法呈現 WebView UI，您需要非本產品能力所及的其他驗證方法。此 SDK 不適用於手錶類型或受到類似限制的裝置。

## 設定和必要條件

完成 [Mobile Apps 快速入門](app-service-mobile-android-get-started.md)教學課程。此工作可確保您已符合開發 Azure Mobile Apps 的所有必要條件。快速入門也可協助您設定帳戶，並建立您的第一個行動應用程式後端。

如果您決定不要完成快速入門教學課程，請完成下列工作︰

- [建立行動應用程式後端][13]以搭配 Android 應用程式使用。
- 在 Android Studio 中，[更新 Gradle 組建檔案](#gradle-build)。
- [啟用網際網路權限](#enable-internet)。

###<a name="gradle-build"></a>更新 Gradle 組建檔案

變更以下兩個 build.gradle 檔案：

1. 將此程式碼加入至 buildscript 標籤內的專案 層級 build.gradle 檔案：

		buildscript {
		    repositories {
		        jcenter()
		    }
		}

2. 將此程式碼加入至 dependencies 標籤內的模組應用程式 層級 build.gradle 檔案：

		compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    目前最新版為 3.1.0。支援的版本列在[這裡][14]。

###<a name="enable-internet"></a>啟用網際網路權限

若要存取 Azure，您的應用程式必須啟用網際網路權限。如果尚未啟用，請將下列這一行程式碼加入至 AndroidManifest.xml 檔案：

	<uses-permission android:name="android.permission.INTERNET" />

## 基本概念的深入探討

本節將探討快速入門應用程式中與使用 Azure Mobile Apps 有關的一些程式碼。

###<a name="data-object"></a>定義用戶端資料類別

若要存取 SQL Azure 資料表的資料，請定義對應至行動應用程式後端中資料表的用戶端資料類別。本主題中的範例採用名為「ToDoItem」的資料表，其中包含下列資料行：

- id
- 文字
- 完成

對應的型別用戶端物件：

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

程式碼位於名為 **ToDoItem.java** 的檔案中。

如果您的 SQL Azure 資料表包含多個資料行，您會將對應的欄位新增至此類別。例如，如果 DTO (資料傳輸物件) 有整數 Priority 資料行，則您可能會新增此欄位，以及其 getter 和 setter 方法：

	private Integer priority;

	/**
	* Returns the item priority
	*/
	public Integer getPriority() {
	    return mPriority;
	}
	
	/**
	* Sets the item priority
	*
	* @param priority
	*            priority to set
	*/
	public final void setPriority(Integer priority) {
	    mPriority = priority;
	}

若要了解如何在您的 Mobile Apps 後端建立其他資料表，請參閱[做法：定義資料表控制器][15] \(.NET 後端) 或[使用動態結構描述定義資料表][16] \(Node.js 後端)。對於 Node.js 後端，您也可以使用 [Azure 入口網站]中的 [簡單資料表] 設定。

###<a name="create-client"></a>做法：建立用戶端內容

下列程式碼將建立用來存取行動應用程式後端的 MobileServiceClient 物件。程式碼會進入在 AndroidManifest.xml 中指定為 MAIN 動作和 LAUNCHER 類別目錄之 Activity 類別的 `onCreate` 方法。在 Quickstart 程式碼中，它會進入 ToDoActivity.java 檔案。

		MobileServiceClient mClient = new MobileServiceClient(
			"MobileAppUrl", // Replace with the Site URL
			this)

在上述程式碼中，以行動應用程式後端的 URL 取代 `MobileAppUrl`，這可在行動應用程式後端的刀鋒視窗中的 [Azure 入口網站]中找到。若要編譯這一行程式碼，您也需要加入下列 import 陳述式：

	import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>作法：建立資料表參考

若要查詢或修改後端中的資料，最簡單的方式就是使用「具型別的程式設計模型」，因為 Java 屬於強型別語言。這個模型會在後端 Azure SQL 中的用戶端物件與資料表之間傳送資料時，使用 [gson][3] 程式庫提供順暢的 JSON 序列化和還原序列化。

若要存取資料表，請先在 [MobileServiceClient][9] 上呼叫 **getTable** 方法，以建立 [MobileServiceTable][8] 物件。此方法有兩個多載：

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

在下列程式碼中，**mClient** 是您的 MobileServiceClient 物件的參考。第一個多載會在類別名稱與資料表名稱相同的情況下使用，而且會使用於 Quickstart：

	MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

第二個多載會在資料表名稱與類型名稱不同時使用：第一個參數為資料表名稱。

	MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>作法：將資料繫結到使用者介面

資料繫結牽涉到三項要件：

- 資料來源
- 畫面配置
- 將這兩個項目連結在一起的配接器。

在範例程式碼中，我們會將 Mobile Apps SQL Azure 資料表 **ToDoItem** 中的資料傳回陣列中。此活動是常見的資料應用模式。資料庫查詢通常會傳回用戶端在清單或陣列中取得的資料列集合。在此範例中，陣列是資料來源。

程式碼會指定一個畫面配置，以定義裝置上出現的資料檢視。這兩個項目會透過配接器繫結在一起；在此程式碼中，配接器會是 **ArrayAdapter&lt;ToDoItem&gt;** 類別的擴充功能。

#### <a name="layout"></a>作法：定義配置

配置可使用數個 XML 程式碼片段來定義。在現有配置下，下列程式碼會顯示我們要以伺服器資料填入的 **ListView**。

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

在上述程式碼中，「listitem」屬性會指定清單中個別資料列的配置識別碼。此程式碼會指定核取方塊及其相關文字，並對清單中的每個項目具現化一次。此配置不會顯示 **id** 欄位，而更複雜的配置將會指定顯示畫面中的其他欄位。此程式碼位於 **row\_list\_to\_do.xml** 檔案中。

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:orientation="horizontal">
	    <CheckBox
	        android:id="@+id/checkToDoItem"
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:text="@string/checkbox_text" />
	</LinearLayout>


#### <a name="adapter"></a>作法：定義配接器

由於我們的檢視資料來源是 **ToDoItem** 的陣列，因此我們將配接器設為 **ArrayAdapter&lt;ToDoItem&gt;** 類別的子類別。這個子類別會為每個使用 **row\_list\_to\_do** 配置的 **ToDoItem** 產生一個檢視。

我們在程式碼中定義了下列類別，這是 **ArrayAdapter&lt;E&gt;** 類別的擴充功能：

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

覆寫配接器的 **getView** 方法。例如：

    @Override
	public View getView(int position, View convertView, ViewGroup parent) {
		View row = convertView;

		final ToDoItem currentItem = getItem(position);

		if (row == null) {
			LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
			row = inflater.inflate(R.layout.row_list_to_do, parent, false);
		}

		row.setTag(currentItem);

		final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
		checkBox.setText(currentItem.getText());
		checkBox.setChecked(false);
		checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


		return row;
	}

我們在「活動」中建立此類別的執行個體，如下所示：

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

ToDoItemAdapter 建構函式的第二個參數是配置的參考。我們現在可以具現化 **ListView**，並對 **ListView** 指派配接器。

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>API 結構

Mobile Apps 資料表作業和自訂 API 呼叫是非同步的。針對涉及查詢、插入、更新和刪除的非同步方法，請使用 [Future] 和 [AsyncTask] 物件。使用 futures 可使得在背景執行緒上執行多項作業變得更容易，而無需處理多個巢狀回呼。

請從 [Azure 入口網站]檢閱 Android 快速入門專案中的 **ToDoActivity.java** 檔案以取得範例。

#### <a name="use-adapter"></a>作法：使用配接器

您現在已可使用資料繫結。下列程式碼說明如何取得資料表中的項目，並以傳回的項目填入本機配接器。

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
		runAsyncTask(task);
    }

請在每次修改 **ToDoItem** 資料表時呼叫配接器。修改是對個別記錄逐一執行的，因此您會處理單一資料列，而不是集合。在插入項目時，請對配接器呼叫 **add** 方法，刪除時則呼叫 **remove** 方法。

##<a name="querying"></a>做法：查詢行動應用程式後端中的資料

本節將說明如何對行動應用程式後端發出查詢，包括下列工作：

- [傳回所有項目]
- [篩選傳回的資料]
- [排序傳回的資料]
- [以分頁方式傳回資料]
- [選取特定資料欄]
- [串連查詢方法](#chaining)

### <a name="showAll"></a>作法：從資料表傳回所有項目

下列查詢會傳回 **ToDoItem** 資料表中的所有項目。

	List<ToDoItem> results = mToDoTable.execute().get();

*results* 變數會以清單形式傳回查詢的結果集。

### <a name="filtering"></a>作法：篩選傳回的資料

下列查詢執行會從 **complete** 等於 **false** 的 **ToDoItem** 資料表傳回所有項目。

	List<ToDoItem> result = mToDoTable.where()
								.field("complete").eq(false)
								.execute().get();

**mToDoTable** 是我們先前建立的行動服務資料表的參考。

在資料表參考上使用 **where** 方法呼叫定義篩選器。在 **where** 方法之後，依序是 **field** 方法以及指定邏輯述詞的方法。可能的述詞方法包括 **eq** (等於)、**ne** (不等於)、**gt** (大於)、**ge** (大於或等於)、**lt** (小於)、**le** (小於或等於)。這些方法可讓您比較數字和字串欄位與特定值。

您可以依日期進行篩選。下列方法可讓您比較整個日期欄位或比較日期的某些部分：**year**、**month**、**day**、**hour**、**minute** 和 **second**。下列範例會為*到期日*為 2013 年的項目新增篩選器。

	mToDoTable.where().year("due").eq(2013).execute().get();

下列方法在字串欄位上支援複雜的篩選器：**startsWith**、**endsWith**、**concat**、**subString**、**indexOf**、**replace**、**toLower**、**toUpper**、**trim** 和 **length**。下列範例會對「text」資料行的開頭為 "PRI0" 的資料表資料列進行篩選。

	mToDoTable.where().startsWith("text", "PRI0").execute().get();

數字欄位支援下列運算子方法：**add**、**sub**、**mul**、**div**、**mod**、**floor**、**ceiling** 和 **round**。下列範例會對 **duration** 為偶數的資料表資料列進行篩選。

	mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

您可以結合述詞與下列邏輯方法：**and**、**or** 和 **not**。下列範例會結合上述的兩個範例。

	mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
				.execute().get();

群組和巢狀邏輯運算子︰

	mToDoTable.where()
				.year("due").eq(2013)
					.and
				(startsWith("text", "PRI0").or().field("duration").gt(10))
				.execute().get();

如需篩選的詳細討論與範例，請參閱[探索功能豐富的 Android 用戶端查詢模型](http://hashtagfail.com/post/46493261719/mobile-services-android-querying)。

### <a name="sorting"></a>作法：排序傳回的資料

下列程式碼會從 **ToDoItems** 資料表傳回依 *text* 欄位遞增排序的所有項目。*mToDoTable* 是您先前建立的後端資料表的參考。

	mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

**orderBy** 方法的第一個參數是一個字串，代表作為排序依據的欄位名稱。第二個參數會使用 **QueryOrder** 列舉，指定要進行遞增還是遞減排序。如果您使用 **where** 方法進行篩選，**where** 方法必須要在 **orderBy** 方法之前叫用。

### <a name="paging"></a>作法：以分頁方式傳回資料

第一個範例將說明如何從資料表中選取前五個項目。查詢會傳回 **ToDoItems** 資料表中的項目。**mToDoTable** 是您先前建立的後端資料表的參考。

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


以下是略過前五個項目，而傳回後續五個項目的查詢：

	mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>作法：選取特定資料欄

下列程式碼說明如何傳回 **ToDoItems** 資料表中的所有項目，但僅顯示 **complete** 和 **text** 欄位。**mToDoTable** 是我們先前建立的後端資料表的參考。

	List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

select 函數的參數是您要傳回之資料表資料行的字串名稱。

**select** 方法必須跟隨在 **where** 和 **orderBy** 等方法之後。而其後可以跟隨 **top** 之類的方法。

### <a name="chaining"></a>作法：串連查詢方法

用來查詢後端資料表的方法是可以串連的。鏈結查詢方法可讓您從排序和分頁的篩選資料列中選取特定資料行。您可以建立複雜的邏輯篩選器。每個查詢方法都會傳回 Query 物件。若要結束這一系列的方法並實際執行查詢，請呼叫 **execute** 方法。例如：

	mToDoTable.where()
        .year("due").eq(2013)
		.and().startsWith("text", "PRI0")
		.or().field("duration").gt(10)
		.orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
		.execute().get();

鏈結的查詢方法必須依下列順序︰

1. 篩選 (**where**) 方法。
2. 排序 (**orderBy**) 方法。
3. 選取 (**select**) 方法。
4. 分頁 (**skip** 和 **top**) 方法。

##<a name="inserting"></a>做法：將資料插入後端

將「ToDoItem」類別的執行個體具現化，並設定其屬性。

	ToDoItem item = new ToDoItem();
	item.text = "Test Program";
	item.complete = false;

然後使用 **insert()** 插入物件︰

	ToDoItem entity = mToDoTable.insert(item).get();

傳回的實體會符合插入後端資料表的資料，包括識別碼和後端上設定的任何其他值。

Mobile Apps 資料表需要名為**識別碼**的主索引鍵資料行。根據預設，此資料行是字串。識別碼資料行的預設值是 GUID。您可以提供其他的唯一值，例如電子郵件地址或使用者名稱。未針對插入的記錄提供字串識別碼值時，後端會產生新的 GUID。

字串識別碼值提供下列優點：

+ 不需要往返存取資料庫就能產生識別碼。
+ 輕鬆合併不同資料表或資料庫的記錄。
+ 識別碼值與應用程式邏輯的整合更理想。

若要支援離線同步處理，則**需要**字串識別碼值。

##<a name="updating"></a>做法︰更新行動應用程式中的資料

若要更新資料表中的資料，請將新物件傳遞至 **update()** 方法。

    mToDoTable.update(item).get();

在此範例中，*item* 是 *ToDoItem* 資料表中某個資料列的參考，其中已有一些變更。具有相同**識別碼**的資料列已更新。

##<a name="deleting"></a>做法：刪除行動應用程式中的資料

下列程式碼說明如何藉由指定資料物件來刪除資料表中的資料。

	mToDoTable.delete(item);

您也可以藉由指定要刪除的資料列的 **id** 欄位來刪除項目。

	String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
   	mToDoTable.delete(myRowId);

##<a name="lookup"></a>作法：查閱特定項目

以 **lookUp()** 方法查閱具有特定**識別碼**欄位的項目︰

	ToDoItem result = mToDoTable
						.lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
						.get();

##<a name="untyped"></a>作法：使用不具類型的資料

不具型別的程式設計模型可讓您精確掌控 JSON 序列化。在某些常見情況下，您可能會想使用不具型別的程式設計模型。例如，如果您的後端資料表包含許多資料行，但您只需要參考其中幾個資料行時。使用具型別的模型時，您必須在資料類別中定義所有行動應用程式資料表的資料行。

用來存取資料的 API 呼叫大多會與型別程式設計呼叫相類似。主要的差別在於，在不具型別的模型中，您會叫用 **MobileServiceJsonTable** 物件的方法，而不是 **MobileServiceTable** 物件。

### <a name="json_instance"></a>作法：建立不具型別的資料表執行個體

和型別模型一樣，首先您必須取得資料表參考，但在此案例中這會是 **MobileServicesJsonTable** 物件。在用戶端的執行個體上呼叫 **getTable** 方法以取得參考：

    private MobileServiceJsonTable mJsonToDoTable;
	//...
    mJsonToDoTable = mClient.getTable("ToDoItem");

在您建立 **MobileServiceJsonTable** 的執行個體後，它就幾乎具有和型別程式設計模型所能使用的一樣的 API。在某些情況下，這些方法會採用不具型別的參數，而非具型別的參數。

### <a name="json_insert"></a>作法：插入不具型別的資料表中

下列程式碼將說明如何執行插入。第一個步驟是建立屬於 [gson][3] 程式庫的 [JsonObject][1]。

	JsonObject jsonItem = new JsonObject();
	jsonItem.addProperty("text", "Wake up");
	jsonItem.addProperty("complete", false);

然後，使用 **insert()** 在資料表中插入不具型別的物件。

    mJsonToDoTable.insert(jsonItem).get();

如果您需要取得所插入之物件的識別碼，請使用 **getAsJsonPrimitive()** 方法。

	jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>作法：在不具型別的資料表中進行刪除

下列程式碼將說明如何刪除執行個體 (在此案例中，即為在前述**插入**範例中建立的同一個 *JsonObject* 執行個體)。程式碼與典型案例相同，但方法具有不同的簽章，因為它會參考 **JsonObject**。

         mToDoTable.delete(jsonItem);

您也可以直接使用 ID 來刪除執行個體：

		 mToDoTable.delete(ID);

### <a name="json_get"></a>作法：從不具型別的資料表傳回所有資料列

下列程式碼將說明如何擷取整個資料表。由於您使用 JSON 資料表，因此可以選擇性地只擷取資料表的某些資料行。

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

具型別的模型可使用的同一組篩選、篩選和分頁方法也可供不具型別的模型使用。

##<a name="custom-api"></a>作法：呼叫自訂 API

自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

從 Android 用戶端呼叫 **invokeApi** 方法，以呼叫自訂 API 端點。下列範例示範如何呼叫名為 **completeAll** 的 API 端點，它會傳回名為 **MarkAllResult** 的集合類別。

	public void completeItem(View view) {

	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}

	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();
	    		}
	    	});
	    }

**invokeApi** 方法是在用戶端上呼叫，可將 POST 要求傳送給新的自訂 API。如有任何錯誤，自訂 API 傳回的結果會顯示在訊息對話方塊中。其他版本的 **invokeApi** 可讓您選擇性地在要求主體中傳送物件、指定 HTTP 方法，並隨著要求一起傳送查詢參數。也會提供不具型別的版本 **invokeApi**。

##<a name="authentication"></a>做法：將驗證新增至您的應用程式

教學課程已詳細說明如何新增這些功能。

App Service 支援使用各種外部識別提供者 (Facebook、Google、Microsoft 帳戶、Twitter 以及 Azure Active Directory) 來[驗證應用程式使用者](app-service-mobile-android-get-started-users.md)。您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以使用經驗證使用者的身分識別，以在後端實作授權規則。

支援兩個驗證流程：伺服器流程和用戶端流程。由於伺服器流程採用識別提供者的 Web 介面，因此所提供的驗證體驗也最為簡單。不需要其他 SDK 就能實作伺服器流程驗證。伺服器流程驗證不會與行動裝置密切整合，因此只建議用於概念證明案例。

用戶端流程依賴識別提供者提供的 SDK，因此可以與裝置特有的功能深入整合，例如單一登入。例如，您可以將 Facebook SDK 整合到行動應用程式。行動用戶端會切換到 Facebook 應用程式，並確認您已登入再切換回行動應用程式。

要在您的應用程式中啟用驗證，必須執行四個步驟：

- 對識別提供者註冊應用程式以進行驗證。
- 設定 App Service 後端。
- 限制只有 App Service 後端上經驗證的使用者會具有資料表權限。
- 將驗證碼新增至您的應用程式。

您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以使用已驗證的使用者 SID 來修改要求。如需詳細資訊，請檢閱[開始使用驗證]和伺服器 SDK 做法文件。

### <a name="caching"></a>作法：將驗證碼新增至您的應用程式

下列程式碼使用 Google 提供者啟動伺服器流程登入程序：

	MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

使用 **getUserId** 方法從 **MobileServiceUser** 取得已登入使用者的識別碼。如需如何使用 Futures 來呼叫非同步登入 API 的範例，請參閱[開始使用驗證]。

### <a name="caching"></a>作法：快取驗證權杖

您必須將使用者 ID 和驗證語彙基元儲存在本機裝置上，才能快取驗證語彙基元。當應用程式下次啟動時，您只需確認這些值仍存在於快取中，即可略過登入程序，並使用這項資料還原用戶端。但這項資料具有敏感性，因此應加密儲存，以確保在手機失竊的狀況下仍保有安全性。

您可以在[快取驗證權杖一節][7]中看到如何快取驗證權杖的完整範例。

當您嘗試使用到期的權杖時，您會收到「401 未授權」的回應。您可以使用篩選器處理驗證錯誤。篩選器會攔截對 App Service 後端提出的要求。篩選器程式碼會測試 401 的回應、觸發登入程序，然後繼續執行產生 401 的要求。

## <a name="adal"></a>做法：使用 Active Directory Authentication Library 驗證使用者

您可以使用 Active Directory Authentication Library (ADAL)，利用 Azure Active Directory 將使用者登入應用程式。使用用戶端流程登入通常會比使用 `loginAsync()` 方法還適合，因為它提供更原生的 UX 風格，並允許其他自訂。

1. 依照[如何設定 App Service 來進行 Active Directory 登入](app-service-mobile-how-to-configure-active-directory-authentication.md)教學課程的說明，設定您的行動應用程式後端來進行 AAD 登入。請務必完成註冊原生用戶端應用程式的選擇性步驟。

2. 安裝 ADAL，方法是修改您的 build.gradle 檔案以納入下列定義：

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. 將下列程式碼新增至您的應用程式，進行下列取代：

* 以您佈建應用程式的租用戶名稱取代 **INSERT-AUTHORITY-HERE**。格式應該是 https://login.windows.net/contoso.onmicrosoft.com。此值可從 [Azure 傳統入口網站] 複製到 Azure Active Directory 的 [網域] 索引標籤以外。

* 以您行動應用程式後端的用戶端識別碼取代 INSERT-RESOURCE-ID-HERE。您可以從入口網站 [Azure Active Directory 設定] 底下的 [進階] 索引標籤取得用戶端識別碼。

* 以您從原生用戶端應用程式中複製的用戶端識別碼取代 INSERT-CLIENT-ID-HERE。

* 使用 HTTPS 配置，以您網站的 _/.auth/login/done_ 端點取代 **INSERT-REDIRECT-URI-HERE**。此值應與 \_https://contoso.azurewebsites.net/.auth/login/done_ 類似。

		private AuthenticationContext mContext;

		private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
		}

		private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
			public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
		};

		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
		}

## 如何：將推播通知新增至您的應用程式

您可以[閱讀概觀][6]，它描述 Microsoft Azure 通知中樞如何支援各種推播通知。在[本教學課程][5]中，每次插入記錄時，就會傳送推播通知到所有裝置。

## 如何：將離線同步處理新增至應用程式

Quickstart 教學課程包含可實作離線同步處理的程式碼。尋找前面加上註解的程式碼：

	// Offline Sync

取消註解下列幾行程式碼，您可以實作離線同步處理，而且可以將類似的程式碼新增至其他 Mobile Apps 程式碼。

##<a name="customizing"></a>作法：自訂用戶端

有幾種方式可以自訂用戶端的預設行為。

### <a name="headers"></a>作法：自訂要求標頭

設定 **ServiceFilter** 以對每個要求新增自訂 HTTP 標頭︰

	private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                	ServiceFilterRequest request,
					NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
	        		request.addHeader("My-Header", "Value");	                }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>作法：自訂序列化

用戶端會假設後端上的資料表名稱、資料行名稱和資料類型，全都與用戶端中定義的資料物件完全相符。有各種原因可能導致伺服器與用戶端的名稱不相符。在您的案例中，您可能會想要執行下列類型的自訂：

- App Service 資料表中使用的資料行名稱不符合您在用戶端中使用的名稱。
- 使用名稱與用戶端中的對應類別不同的 App Service 資料表。
- 開啟自動使用大寫屬性。
- 將複雜屬性新增至物件。

### <a name="columns"></a>作法：對應不同的用戶端和伺服器名稱

假設您的 Java 用戶端程式碼對 **ToDoItem** 物件屬性使用標準 Java 樣式名稱，例如下列屬性：

- mId
- mText
- mComplete
- mDuration

請將用戶端名稱序列化為 JSON 名稱，且這些名稱必須與伺服器上的 **ToDoItem** 資料表的資料行名稱相符。下列程式碼會使用 [gson][3] 程式庫來註解屬性︰

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>做法：在用戶端與後端之間對應不同的資料表名稱

使用 [getTable()][4] 方法的覆寫將用戶端資料表名稱對應至不同的行動服務資料表名稱：

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>作法：自動化資料行名稱對應

您可以使用 [gson][3] API 指定套用至每個資料行的轉換策略。Android 用戶端程式庫會先在背景使用 [gson][3] 來將 Java 物件序列化為 JSON 資料，再將資料傳送至 Azure App Service。下列程式碼使用 **setFieldNamingStrategy()** 方法來設定策略。此範例會刪除每個欄位名稱的起始字元 ("m")，然後將下一個字元轉換為小寫。比方說，它會將「mId」變成「id」。

	client.setGsonBuilder(
	    MobileServiceClient
	    .createMobileServiceGsonBuilder()
	    .setFieldNamingStrategy(new FieldNamingStrategy() {
	        public String translateName(Field field) {
	            String name = field.getName();
	            return Character.toLowerCase(name.charAt(1))
	                + name.substring(2);
	            }
	        });

必須先執行此程式碼才能使用 **MobileServiceClient**。

### <a name="complex"></a>作法：將物件或陣列屬性儲存在資料表中

到目前為止，我們的序列化範例已涉及整數和字串等基本類型。基本類型可輕鬆地序列化為 JSON。如果我們想要新增不會自動序列化為 JSON 的複雜物件，我們需要提供 JSON 序列化方法。若要查看如何提供自訂 JSON 序列化的範例，請檢閱部落格文章[在行動服務 Android 用戶端中使用 gson 程式庫自訂序列化][2]。

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[傳回所有項目]: #showAll
[篩選傳回的資料]: #filtering
[排序傳回的資料]: #sorting
[以分頁方式傳回資料]: #paging
[選取特定資料欄]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure 入口網站]: https://portal.azure.com
[開始使用驗證]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html

<!---HONumber=AcomDC_0928_2016-->