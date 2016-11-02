<properties
    pageTitle="利用串流分析來建置 IoT 解決方案 | Microsoft Azure"
    description="串流分析收費亭案例 IoT 解決方案的入門教學課程"
    keywords="iot 解決方案, 視窗函數"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="build-an-iot-solution-by-using-stream-analytics"></a>利用串流分析來建置 IoT 解決方案

## <a name="introduction"></a>簡介

在本教學課程中，您將學習如何利用 Azure 串流分析來獲得您資料的即時深入解析。 開發人員可以很容易地結合資料串流，例如點選流、記錄和裝置產生的事件，並且包含歷程記錄或參考資料，以衍生商務深入解析。 Azure 串流分析是由 Microsoft Azure 代管、可完全管理的即時串流計算服務，它提供內建的備援、低延遲及延展性功能，可讓您在幾分鐘之內就立刻上手。

完成本教學課程之後，您將能夠：

-   熟悉 Azure 串流分析入口網站。
-   設定及部署串流工作。
-   使用串流分析查詢語言來表達真實世界的問題，並加以解決。
-   有自信地使用串流分析來為客戶開發串流解決方案。
-   利用監視和記錄的經驗來排解問題。

## <a name="prerequisites"></a>必要條件

您需要下列必要條件來完成本教學課程：

-   最新版的 [Azure PowerShell](../powershell-install-configure.md)
-   Visual Studio 2015 或，或是免費的 [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   [Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)
-   電腦的系統管理員權限
-   從 Microsoft 下載中心下載 [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip)
-   選擇性： [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction:-“hello,-toll!”"></a>案例簡介：收費站，你好！


收費站是常見的設施。 您可以在世界各地的許多快速道路、橋樑及隧道中看到它們。 每個收費站都有多個收費亭。 在人工收費亭中，您需要停車來向服務員付費。 在自動收費亭中，位於每個收費亭最上方的感應器會在您通過收費亭時掃描黏貼在您車輛擋風玻璃上的 RFID 卡。 我們可以輕易地把車輛通過這些收費站的情況，想像成其中能執行許多有趣行動的事件串流。

![收費亭中車輛的圖片](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>傳入的資料

本教學課程搭配兩個資料串流來教學。 安裝在收費亭入口和出口處的感應器會產生第一個串流。 第二個串流是擁有車輛登記資料的靜態查詢資料集。

### <a name="entry-data-stream"></a>入口資料流

入口資料流包含車輛進入收費站的相關資訊。


| TollID | EntryTime               | LicensePlate | State | Make   | 模型   | VehicleType | VehicleWeight | Toll | Tag       |
|--------|-------------------------|--------------|-------|--------|---------|-------------|---------------|------|-----------|
| 1      | 2014-09-10 12:01:00.000 | JNB 7001     | NY    | Honda  | CRV     | 1           | 0             | 7    |           |
| 1      | 2014-09-10 12:02:00.000 | YXZ 1001     | NY    | Toyota | Camry   | 1           | 0             | 4    | 123456789 |
| 3      | 2014-09-10 12:02:00.000 | ABC 1004     | CT    | Ford   | Taurus  | 1           | 0             | 5    | 456789123 |
| 2      | 2014-09-10 12:03:00.000 | XYZ 1003     | CT    | Toyota | Corolla | 1           | 0             | 4    |           |
| 1      | 2014-09-10 12:03:00.000 | BNJ 1007     | NY    | Honda  | CRV     | 1           | 0             | 5    | 789123456 |
| 2      | 2014-09-10 12:05:00.000 | CDE 1007     | NJ    | Toyota | 4x4     | 1           | 0             | 6    | 321987654 |


以下是每個資料欄的簡短說明：

| 資料欄 | 說明 |
|--------------|--------------------------------------------------------------------|
| TollID       | 唯一識別收費亭的收費亭識別碼            |
| EntryTime    | 車輛進入收費亭的日期及時間 (國際標準時間) |
| LicensePlate | 車輛的車牌號碼                            |
| State        | 美國的某個洲                                           |
| Make         | 車輛的製造商                                 |
| 模型        | 車輛的型號                                 |
| VehicleType  | 1 代表載客車或 2 代表商用車       |
| WeightType   | 車輛的重量，單位為噸；0 代表客車                   |
| Toll         | 通行費，單位為美元                                              |
| Tag          | 車輛上可用來自動付費的 e-Tag，空白則代表手動付費 |


### <a name="exit-data-stream"></a>出口資料流

出口資料流包含車輛離開收費站的相關資訊。


| **TollId** | **ExitTime**                 | **LicensePlate** |
|------------|------------------------------|------------------|
| 1          | 2014-09-10T12:03:00.0000000Z | JNB 7001         |
| 1          | 2014-09-10T12:03:00.0000000Z | YXZ 1001         |
| 3          | 2014-09-10T12:04:00.0000000Z | ABC 1004         |
| 2          | 2014-09-10T12:07:00.0000000Z | XYZ 1003         |
| 1          | 2014-09-10T12:08:00.0000000Z | BNJ 1007         |
| 2          | 2014-09-10T12:07:00.0000000Z | CDE 1007         |

以下是每個資料欄的簡短說明：


| 資料欄 | 說明 |
|--------------|-----------------------------------------------------------------|
| TollID       | 唯一識別收費亭的收費亭識別碼         |
| ExitTime     | 車輛離開收費亭的日期及時間 (國際標準時間) |
| LicensePlate | 車輛的車牌號碼                         |

### <a name="commercial-vehicle-registration-data"></a>商用車的登記資料

本教學課程將使用商用車登記資料庫的靜態快照。


| LicensePlate | RegistrationId | 已過期 |
|--------------|----------------|---------|
| SVT 6023     | 285429838      | 1       |
| XLZ 3463     | 362715656      | 0       |
| BAC 1005     | 876133137      | 1       |
| RIV 8632     | 992711956      | 0       |
| SNY 7188     | 592133890      | 0       |
| ELH 9896     | 678427724      | 1       |

以下是每個資料欄的簡短說明：


| 資料欄 | 說明 |
|--------------|-----------------------------------------------------------------|
| LicensePlate | 車輛的車牌號碼                         |
| RegistrationId     | 車輛的登記識別碼 |
| 已過期 | 車輛的登記狀態：0 代表車輛登記仍有效，1 代表車輛登記已過期                 |


## <a name="set-up-the-environment-for-azure-stream-analytics"></a>設定 Azure 串流分析的環境

若要完成此教學課程，您需要 Microsoft Azure 訂用帳戶。 Microsoft 能讓您免費試用 Microsoft Azure 服務。

如果您沒有 Azure 帳戶，您可以 [要求獲得免費試用版](http://azure.microsoft.com/pricing/free-trial/)。

> [AZURE.NOTE] 若要註冊免費試用，您必須擁有可接收簡訊的行動裝置，以及有效的信用卡。

請務必依照這篇文章結尾處＜清理您的 Azure 帳戶＞一節中的步驟來進行，讓您能充分利用您 $200 美元的免費 Azure 點數。

## <a name="provision-azure-resources-required-for-the-tutorial"></a>佈建本教學課程所需的 Azure 資源

本教學課程將需要 2 個事件中樞，來接收 *entry* 及 *exit* 資料流。 Azure SQL Database 會輸出串流分析工作的結果。 Azure 儲存體會儲存車輛登記的相關參考資料。

您可以使用 GitHub 上 TollApp 資料夾中的 Setup.ps1 指令碼來建立所有必要的資源。 為了節省時間，我們建議您執行這個指令碼。 如果您想要深入了解如何在 Azure 入口網站中設定這些資源，請參閱＜在 Azure 入口網站中設定教學課程資源＞附錄。

請下載並儲存支援的 [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) 資料夾和檔案。

請_以系統管理員的身分_開啟 [Microsoft Azure PowerShell]。 如果您沒有 Azure PowerShell，請依照 [安裝和設定 Azure PowerShell](../powershell-install-configure.md) 中的指示來安裝。

因為 Windows 會自動封鎖 .ps1、.dll 及 .exe 檔案，所以您需要先設定執行原則，才能執行指令碼。 請確認您是「以系統管理員的身分」 執行 Azure PowerShell 視窗。 請執行 **Set-ExecutionPolicy unrestricted**。 在出現提示時按下 Y 鍵 。

!["Set-ExecutionPolicy unrestricted" 正在 Azure PowerShell 視窗中執行的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

請執行 **Get-ExecutionPolicy** 來確認命令已正確執行。

!["Get-ExecutionPolicy" 正在 Azure PowerShell 視窗中執行的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

請前往擁有指令碼和產生器應用程式的目錄。

!["cd .\TollApp\TollApp" 正在 Azure PowerShell 視窗中執行的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

請輸入 **.\\Setup.ps1** 來設定您的 Azure 帳戶、建立及設定所有必要的資源，然後開始產生事件。 指令碼會隨機挑選一個區域來建立您的資源。 若要明確指定區域，您可以傳遞 **-location** 參數，如以下範例所示：

**.\\Setup.ps1 -location “Central US”**

![Azure 登入頁面的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

指令碼將會開啟 Microsoft Azure 的 [登入]  頁面。 請輸入您的帳戶認證。

> [AZURE.NOTE] 如果您的帳戶能夠存取多個訂用帳戶，系統將會要求您輸入您要用於本教學課程的訂用帳戶名稱。

指令碼可能需要幾分鐘的時間來執行。 完成之後，輸出結果應該看起來像下方的螢幕擷取畫面。

![Azure PowerShell 視窗中指令碼輸出的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

您也會看到類似下方螢幕擷取畫面的另一個視窗。 這個應用程式會把事件傳送至 Azure 事件中樞，您需要它來執行教學課程。 所以在您完成本教學課程之前，請不要停止這個應用程式，或是關閉這個視窗。

![「正在傳送事件中樞資料」的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

現在，您應該能夠在 Azure 入口網站中看到所有已建立的資源。 請前往 <https://manage.windowsazure.com>，並使用您的帳戶認證登入。

### <a name="azure-event-hubs"></a>Azure 事件中心

請按一下 Azure 入口網站左側的 [服務匯流排]  ，來查看上一節中指令碼所建立的事件中樞。

![服務匯流排](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

您將在自己的訂用帳戶中看到所有可用的命名空間。 請按一下開頭為 *tolldata* 的項目 (在我們的範例中是 tolldata4637388511)。 按一下 [事件中樞] 索引標籤。

![Azure 入口網站中的事件中樞](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

您會在這個命名空間中看到 2 個已建立的事件中樞，分別名為 *entry* 和 *exit*。

![Azure 入口網站中 "entry" 和 "exit" 事件中樞的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Azure 儲存體容器

1. 請按一下 Azure 入口網站左側的 [儲存體]  ，來查看在教學課程中使用的 Azure 儲存體容器。

    ![儲存體功能表項目](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

2. 請按一下開頭為 *tolldata* 的項目 (在我們的範例中是 tolldata4637388511)。 請按一下 [容器] 索引標籤來查看已建立的容器。

    ![Azure 入口網站中的 [容器] 索引標籤](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

3. 請按一下 [tolldata]  容器來查看已上傳，且擁有車輛登記資料的 JSON 檔案。

    ![容器中 registration.json 檔案的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Azure SQL Database

1. 請按一下 Azure 入口網站左側的 [SQL DATABASES]  ，來查看將在教學課程中使用的 SQL Database。

    ![SQL Database](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

2. 按一下 [tolldatadb] 。

    ![所建立 SQL Database 的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

3. 請複製伺服器名稱，但不用複製連接埠號碼 (例如 [伺服器名稱].database.windows.net)。

## <a name="connect-to-the-database-from-visual-studio"></a>從 Visual Studio 連線至資料庫

使用 Visual Studio 來存取輸出資料庫中的查詢結果。

如何從 Visual Studio 連線到 SQL Database (目的地)：

1. 開啟 Visual Studio，然後按一下 [工具] > [連接到資料庫]。

2. 如果畫面出現提示，請按一下 [Microsoft SQL Server]  來做為資料來源。

    ![[變更資料來源] 對話方塊](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3. 在 [伺服器名稱] 欄位中，貼上您在上一節從 Azure 入口網站複製的名稱 (也就是 [伺服器名稱].database.windows.net)。

4. 按一下 [使用 SQL Server 驗證] 。

5. 在 [使用者名稱] 欄位中輸入 **tolladmin**，以及在 [密碼] 欄位中輸入 **123toll!** 。

6. 按一下 [選取或輸入資料庫名稱]，然後選取 [TollDataDB] 做為資料庫。

    ![[新增連線] 對話方塊](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7. 按一下 [確定] 。

8. 開啟 [伺服器總管]。

    ![Server Explorer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9. 在 TollDataDB 資料庫中看到 4 個資料表。

    ![TollDataDB 資料庫中的資料表](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator:-tollapp-sample-project"></a>事件產生器：TollApp 範例專案

PowerShell 指令碼會自動利用 TollApp 範例應用程式來開始傳送事件， 因此您不需要執行任何額外的步驟。

然而，如果您對實作的細節有興趣，可以在 GitHub 的 [samples/TollApp](https://aka.ms/azure-stream-analytics-toll-source)中找到 TollApp 應用程式的原始程式碼。

![Visual Studio 中所顯示範例程式碼的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>建立串流分析工作

1. 請在 Azure 入口網站中開啟 [串流分析]，然後按一下頁面左下角的 [新增]  來建立新的分析工作。

    ![New button](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

2. 按一下 [快速建立] 。 然後選取指令碼所建立的其他資源所在的相同區域。

3. 對於 [區域監視儲存體帳戶] 設定，請選取 [建立新的儲存體帳戶]，並給它一個唯一的名稱。 Azure 串流分析會使用此帳戶來儲存您未來所有工作的監視資訊。

4. 按一下頁面底部的 [建立串流分析作業]  。

    ![[建立串流分析工作] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>定義輸入來源

1. 按一下入口網站中已建立的分析工作。

    ![入口網站中分析工作的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

2. 開啟 [輸入]  索引標籤以定義來源資料。

    ![[輸入] 索引標籤](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

3. 按一下 [加入輸入] 。

    ![[加入輸入] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

4. 按一下第一頁的 [資料流]  。

    ![[資料流] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

5. 按一下精靈第二頁上的 [事件中樞]  。

    ![[事件中樞] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

6. 輸入 **EntryStream** 做為 [輸入別名]。

7. 按一下 [事件中樞]  下拉式清單，然後選取開頭為「TollData」的項目 (例如 TollData9518658221)。

8. 選取 [entry] 做為事件中樞名稱，並選取 [all]**all** 做為事件中樞原則名稱。

    您的設定將看起來會像是：

    ![[事件中樞] 設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

9. 在下一個頁面上，選取 [JSON] 做為 [事件序列化格式]，並選取 [UTF8] 做為 [編碼] 格式。

    ![[序列化] 設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

10. 請按一下頁面底部的 [確定]  來完成精靈。

    ![Azure 入口網站中 EntryStream 輸入的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

    現在您已經建立了入口資料流，您將可以依照相同步驟建立出口資料流。 在精靈的第三個頁面上，請務必輸入如下面螢幕擷取畫面中所示的值。

    ![出口資料流的設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

    您已經定義了兩個輸入串流：

    ![Azure 入口網站中定義的輸入資料流](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

    接下來，您將為包含車輛登記資料的 Blob 檔案新增參考資料輸入。

11. 按一下 [加入輸入]，然後按一下 [參考資料]。

    ![已選取 [參考資料] 的 [加入輸入] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

12. 請在下一個頁面選取開頭為 **tolldata**的儲存體帳戶。 容器名稱應該是 **tolldata**，且 [路徑格式] 底下的 Blob 名稱應該是 **registration.json**。 這個檔案名稱會區分大小寫，且應該是小寫。

    ![[部落格儲存體] 設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

13. 請在下一頁選取如以下螢幕擷取畫面中顯示的值，然後按一下 [確定]  來完成精靈。

    ![選取 JSON 做為 [事件序列化格式] 和選取 UTF8 做為 [編碼] 格式的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

現在，所有輸入都已經定義了。

![三個已定義輸入的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>定義輸出

1. 請前往 [輸出] 索引標籤，然後按一下 [新增輸出]。

    ![[輸出] 索引標籤和 [加入輸出] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

2. 按一下 [SQL Database] 。

3. 請選取在文章的＜從 Visual Studio 連線至資料庫＞一節中使用的伺服器名稱。 資料庫名稱應該是 **TollDataDB**。

4. 在 [使用者名稱] 欄位中輸入 **tolladmin**，在 [密碼] 欄位中輸入 **123toll!** ，然後在 [資料表] 欄位中輸入 **TollDataRefJoin**。

    ![SQL Database 設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Azure 串流分析查詢

[查詢]  索引標籤包含會轉換傳入資料的 SQL 查詢。

![已新增到 [查詢] 索引標籤的查詢](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

本教學課程會嘗試回答和幾個通行費資料相關的業務問題，並建構可在 Azure 串流分析中使用的串流分析查詢來提供相關的答案。

在您開始第一個串流分析工作之前，讓我們先來探索幾個案例和查詢語法。

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Azure 串流分析查詢語言簡介
-----------------------------------------------------

假設您需要計算進入某個收費亭的車輛數目。 由於這是連續的事件串流，因此您必須定義一個「時段」。 我們來將問題修改為「每三分鐘有多少車輛進入收費亭」。 這通常稱為輪轉計數。

我們來看看回答這個問題的 Azure 串流分析查詢：

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

如您所見，Azure 串流分析會使用類似 SQL 的查詢語言，並新增幾個擴充功能來指定和時間有關的查詢層面。

如需詳細資料，請參閱 MSDN 中的[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)，以及查詢中所用的[時段](https://msdn.microsoft.com/library/azure/dn835019.aspx)建構。

## <a name="testing-azure-stream-analytics-queries"></a>測試 Azure 串流分析查詢

您已經將第一個 Azure 串流分析查詢編寫完成，現在該利用位於下列路徑中您 TollApp 資料夾中的範例資料檔案來測試查詢了：

**..\\TollApp\\TollApp\\Data**

這個資料夾包含下列檔案：

- Entry.json
- Exit.json
- registration.json

## <a name="question-1:-number-of-vehicles-entering-a-toll-booth"></a>問題 1：進入某個收費亭的車輛數目

1. 開啟 Azure 入口網站，然後移至您建立的 Azure 串流分析工作。 按一下 [查詢]  索引標籤，然後貼上在上一節中複製的查詢。

    ![已張貼到 [查詢] 索引標籤的查詢](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

2. 如要針對範例資料來驗證這個查詢，請按一下 [測試] 按鈕。 請在隨後開啟的對話方塊中移至 Entry.json，這個檔案包含來自 **EntryTime** 事件串流的範例資料。

    ![Entry.json 檔案的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

3. 請驗證查詢的輸出與下列預期的結果相同：

    ![測試結果](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2:-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>問題 2：回報每輛車通過收費亭的總時數

車輛通過收費亭所需的平均時間有助於評估程序效率和客戶經驗。

若要尋找總時間，您需要聯結 EntryTime 串流和 ExitTime 串流。 您將會聯結 TollId 和 LicencePlate 資料欄的串流。 **JOIN** 運算子需要您指定彈性時間，來說明已聯結事件之間可接受的時間差。 您將使用 **DATEDIFF** 函式來指定事件之間的時間差不能超過 15 分鐘。 我們也會將 **DATEDIFF** 函式套用到入口和出口時間，以便計算車輛經過收費亭的實際時間。 請注意在 **SELECT** 陳述式中 (而非在 **JOIN** 中) 使用 **DATEDIFF** 時，其使用方式的差異。

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. 若要測試此查詢，請更新您工作中 [查詢]  索引標籤上的查詢：

    ![[查詢] 索引標籤中已更新的查詢](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

2. 請按一下 [測試]  ，並指定 EntryTime 和 ExitTime 的範例輸入檔。

    ![所選輸入檔案的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

3. 選取核取方塊來測試查詢並檢視輸出：

    ![測試輸出](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3:-report-all-commercial-vehicles-with-expired-registration"></a>問題 3：回報所有車輛登記已過期的商用車

Azure 串流分析可使用靜態的資料快照，來與時間資料流聯結。 若要示範此功能，請使用下面的範例問題。

如果商用車已經向收費公司登記，就可以直接通過收費亭，不用停車接受檢查。 您將使用商用車登記查詢資料表，來辨識所有車輛登記已過期的商用車。

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

若要使用參考資料測試查詢，您需要定義參考資料的輸入來源，而且您已經這樣做了。

1. 若要測試此查詢，請把查詢貼到 [查詢] 索引標籤，然後按一下 [測試]，並指定 2 個輸入來源：

    ![所選輸入檔案的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

2. 請檢視查詢的輸出：

    ![查詢輸出的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>啟動串流分析工作


現在可以完成設定並啟動工作。 儲存問題 3 的查詢，這會產生符合我們 **TollDataRefJoin** 輸出資料表結構描述的輸出。

請前往工作 [儀表板]，然後按一下 [啟動]。

![工作儀表板中 [開始] 按鈕的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

在開啟的對話方塊上，把 [開始輸出] 時間變更為 [自訂時間]。 將小時變更為目前時間的前一個小時。 此變更可確保在您於教學課程開始時開始產生事件之後，來自事件中樞的所有事件都會被處理。 現在，請按一下核取記號來啟動工作。

![自訂時間的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

啟動工作可能需要幾分鐘的時間， 您可以在串流分析最上層的頁面看到狀態。

![工作狀態的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>在 Visual Studio 中查看結果

1. 開啟 Visual Studio 伺服器總管，然後用滑鼠右鍵按一下 [TollDataRefJoin]  資料表。
2. 按一下 [顯示資料表資料]  可查看您工作的輸出。

    ![伺服器總管中 [顯示資料表資料] 的選取項目](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>相應放大 Azure 串流分析工作

Azure 串流分析是設計成能進行彈性調整，以便於處理大量資料。 Azure 串流分析查詢可以使用 **PARTITION BY** 子句，來告訴系統此步驟將會相應放大。 **PartitionId** 是系統新增的特殊資料欄，且它與輸入 (事件中樞) 的分割識別碼相符。

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. 請停止目前的工作，然後更新 [查詢] 索引標籤中的查詢，再開啟 [調整] 索引標籤。

     會定義工作能夠接收之計算能力的量。

2. 請將滑桿移動到 6。

    ![選取 6 個串流處理單位的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

3. 前往 [輸出] 索引標籤，然後把 SQL 資料表名稱變更為 **TollDataTumblingCountPartitioned**。

如果您現在啟動工作，Azure 串流分析將能夠把工作分散到更多的計算資源上，並達到更高的輸送量。 請注意，TollApp 應用程式也會同時傳送已依照 TollId 來分割的事件。

## <a name="monitor"></a>監視

[監視]  索引標籤包含執行中工作的相關統計資料。

![執行中工作相關統計資料的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

您可以從 [儀表板] 索引標籤來存取 [操作記錄]。

![[操作記錄] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![您可以看到工作狀態的操作記錄的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

如要查看特定事件的其他相關資訊，請按一下該事件，然後按一下 [詳細資料]  按鈕。

![所選事件相關詳細資料的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>結論

本教學課程向您介紹了 Azure 串流分析服務。 課程中示範了如何為串流分析工作設定輸入和輸出。 課程中還利用付費資料案例，來解釋在資料空間會不斷變化時所引發的常見問題類型，以及如何在 Azure 串流分析中利用類似 SQL 的簡單查詢來解決這些問題。 課程說明了要用來處理時間資料的 SQL 延伸模組建構。 課程示範了如何聯結不同的資料流，以及如何利用靜態參考資料來豐富資料流的內容，以及如何相應放大查詢來達到更高的輸送量。

雖然本教學課程提供了詳盡的簡介，但也絕對不算是完整的說明。 您可以使用 SAQL 語言在 [一般串流分析使用模式的查詢範例](stream-analytics-stream-analytics-query-patterns.md)中找到更多查詢模式。
若要深入了解 Azure 串流分析，請參閱 [線上文件](https://azure.microsoft.com/documentation/services/stream-analytics/) 。

## <a name="clean-up-your-azure-account"></a>清理您的 Azure 帳戶

1. 從 Azure 入口網站停止串流分析工作。

    Setup.ps1 指令碼會建立 2 個事件中樞，以及 1 個 SQL Database。 下列指示將協助您在本教學課程結束時清理資源。

2. 請在 PowerShell 視窗中輸入 **.\\Cleanup.ps1**，來啟動會刪除本教學課程所使用資源的指令碼。

    > [AZURE.NOTE] 資源是依照名稱來識別。 請確認您在確認刪除之前，已仔細檢閱每個項目。

    ![清理程序的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)



<!--HONumber=Oct16_HO2-->


