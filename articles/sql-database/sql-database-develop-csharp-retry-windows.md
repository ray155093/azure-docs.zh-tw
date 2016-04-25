<properties
	pageTitle="利用 C# 重試邏輯，適用於 SQL Database | Microsoft Azure"
	description="C# 範例中包括重試邏輯，可與 Azure SQL Database 穩健互動。"
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="annemill"/>


# 程式碼範例：C# 中用於連接到 SQL Database 的重試邏輯



[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]



本主題提供可示範自訂重試邏輯的 C# 程式碼範例。重試邏輯的設計目的在於順暢處理暫時錯誤，或如果程式等候幾秒並重試後通常會消失的暫時性錯誤。


用來連接到您本機 Microsoft SQL Server 的 ADO.NET 類別也可以連接到 Azure SQL Database。不過，ADO.NET 類別本身無法提供在生產環境使用所需的所有穩定性和可靠性。用戶端程式可能會遇到暫時性錯誤，用戶端程式應該會從失敗中無訊息且正常自行復原並繼續。


暫時性錯誤的一些範例：


- 透過網際網路的連線受限於短暫的網路中斷，之後可以重新建立連線。
- 雲端運算牽涉到負載平衡，它可以短暫封鎖對連線或查詢的嘗試。


## 答：識別暫時性錯誤


您的程式必須區分暫時性錯誤與持續性錯誤。如果您的程式對於目標資料庫名稱拼字錯誤，「找不到這類資料庫」錯誤將會持續並且每次重新執行該程式將會再次出現。

分類為暫時性錯誤的錯誤號碼清單可自此取得：

- [SQL Database 用戶端程式的錯誤訊息](sql-database-develop-error-messages.md#bkmk_connection_errors)
  - 請參閱＜暫時性錯誤、連線中斷錯誤＞一節。


本主題稍後的 C# 程式碼範例會在名為 **TransientErrorNumbers** 的數字欄位中列出暫時性錯誤。



## B.程式碼範例


目前主題中的 C# 程式碼範例包含可處理暫時性錯誤的自訂偵測和重試邏輯。範例假設已安裝 .NET Framework 4.5.1 或更新版本。


程式碼範例會遵循幾個基本指導方針或適用的建議，而不論用來與 Azure SQL Database 互動的技術為何。您可以在此處看到一般建議：


- [連接到 SQL Database：連結、最佳作法和設計方針](sql-database-connect-central-recommendations.md)


C# 程式碼範例包含一個名為 Program.cs 的檔案。下一節會提供它的程式碼。


### B.1 擷取與編譯程式碼範例


您可以使用下列步驟編譯此範例：


1. 在[免費的 Visual Studio Community 版本](https://www.visualstudio.com/products/visual-studio-community-vs)中，透過 C# 主控台應用程式範本建立新的專案。
  - [檔案] > [新增] > [專案] > [已安裝] > [範本] > [Visual C#] > [Windows] > [傳統桌面] > [主控台應用程式]
  - 將專案命名為 **RetryAdo2**。
2. 開啟 [方案總管] 窗格。
  - 查看專案的名稱。
  - 查看 Program.cs 檔案的名稱。
3. 開啟 Program.cs 檔案。
4. 使用下列程式碼區塊中的程式碼，取代 Program.cs 檔案的所有內容。
5. 按一下 [建置] 功能表 > [建置解決方案]。


#### 要貼上的 C# 原始程式碼


將此程式碼到貼到您的 **Program.cs** 檔案中。


接著，您必須編輯伺服器名稱、密碼等項目的字串。您可以在名為 **GetSqlConnectionStringBuilder** 的方法中找到這些字串。



```
using System;   // C#
using G = System.Collections.Generic;
using S = System.Data.SqlClient;
using T = System.Threading;
   
namespace RetryAdo2
{
   public class Program
   {
      static public int Main(string[] args)
      {
         bool succeeded = false;
         int totalNumberOfTimesToTry = 4;
         int retryIntervalSeconds = 10;
   
         for (int tries = 1;
            tries <= totalNumberOfTimesToTry;
            tries++)
         {
            try
            {
               if (tries > 1)
               {
                  Console.WriteLine
                     ("Transient error encountered. Will begin attempt number {0} of {1} max...",
                     tries, totalNumberOfTimesToTry
                     );
                  T.Thread.Sleep(1000 * retryIntervalSeconds);
                  retryIntervalSeconds = Convert.ToInt32
                     (retryIntervalSeconds * 1.5);
               }
               AccessDatabase();
               succeeded = true;
               break;
            }
   
            catch (S.SqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred.", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (TestSqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred. (TESTING.)", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (Exception Exc)
            {
               Console.WriteLine(Exc);
               succeeded = false;
               break;
            }
         }
   
         if (succeeded == true)
         {
            return 0;
         }
         else
         {
            Console.WriteLine("ERROR: Unable to access the database!");
            return 1;
         }
      }
   
      /// <summary>
      /// Connects to the database, reads,
      /// prints results to the console.
      /// </summary>
      static public void AccessDatabase()
      {
         //throw new TestSqlException(4060); //(7654321);  // Uncomment for testing.
   
         using (var sqlConnection = new S.SqlConnection
                  (GetSqlConnectionString()))
         {
            using (var dbCommand = sqlConnection.CreateCommand())
            {
               dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";
   
               sqlConnection.Open();
               var dataReader = dbCommand.ExecuteReader();
   
               while (dataReader.Read())
               {
                  Console.WriteLine("{0}\t{1}",
                     dataReader.GetString(0),
                     dataReader.GetString(1));
               }
            }
         }
      }
   
      /// <summary>
      /// You must edit the four 'my' string values.
      /// </summary>
      /// <returns>An ADO.NET connection string.</returns>
      static private string GetSqlConnectionString()
      {
         // Prepare the connection string to Azure SQL Database.
         var sqlConnectionSB = new S.SqlConnectionStringBuilder();
   
         // Change these values to your values.
         sqlConnectionSB.DataSource = "tcp:myazuresqldbserver.database.windows.net,1433"; //["Server"]
         sqlConnectionSB.InitialCatalog = "MyDatabase"; //["Database"]
   
         sqlConnectionSB.UserID = "MyLogin";  // "@yourservername"  as suffix sometimes.
         sqlConnectionSB.Password = "MyPassword";
         sqlConnectionSB.IntegratedSecurity = false;
   
         // Adjust these values if you like. (ADO.NET 4.5.1 or later.)
         sqlConnectionSB.ConnectRetryCount = 3;
         sqlConnectionSB.ConnectRetryInterval = 10;  // Seconds.
   
         // Leave these values as they are.
         sqlConnectionSB.IntegratedSecurity = false;
         sqlConnectionSB.Encrypt = true;
         sqlConnectionSB.ConnectTimeout = 30;
   
         return sqlConnectionSB.ToString();
      }
   
      static public G.List<int> TransientErrorNumbers =
         new G.List<int> { 4060, 40197, 40501, 40613,
         49918, 49919, 49920, 11001 };
   }
   
   /// <summary>
   /// For testing retry logic, you can have method
   /// AccessDatabase start by throwing a new
   /// TestSqlException with a Number that does
   /// or does not match a transient error number
   /// present in TransientErrorNumbers.
   /// </summary>
   internal class TestSqlException : ApplicationException
   {
      internal TestSqlException(int testErrorNumber)
      { this.Number = testErrorNumber; }
   
      internal int Number
      { get; set; }
   }
}
```



## C.執行程式


**RetryAdo2.exe** 可執行檔不會輸入任何參數。執行 .exe：

1. 開啟主控台視窗至您編譯 RetryAdo2.exe 二進位檔的位置。
2. 執行 RetryAdo2.exe，不含輸入參數。



```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```



## D.重試邏輯的測試方式

有許多種方式可讓您模擬暫時性錯誤，以便測試您的重試邏輯。


### D.1 擲回測試例外狀況

程式碼範例包含：

- 小型的第二個類別，名為 **TestSqlException**，其屬性名為 **Number**。
- `//throw new TestSqlException(4060);`，您可以將它取消註解。

如果您取消註解擲回，並且重新編譯，那麼下次執行的 **RetryAdo2.exe** 輸出會類似下列內容。



```
[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>> RetryAdo2.exe
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 2 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 3 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 4 of 4 max...
4060: transient occurred. (TESTING.)
ERROR: Unable to access the database!

[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>>
```



#### 使用持續性錯誤重新測試


若要證明程式碼正確地處理持續性錯誤，請重新執行先前的測試，但不要使用類似 4060 的真正暫時性錯誤的數字。請改為使用無意義的數字 7654321。此程式應該將這視為持續性錯誤，並且應該略過任何重試。



### D.2 中斷網路連線

1. 中斷用戶端電腦的網路連線。
  - 若為桌上型電腦，請拔除網路線。
  - 若為膝上型電腦，請按功能鍵組合來關閉網路介面卡。
2. 啟動 RetryAdo2.exe，並等候主控台顯示第一個暫時性錯誤，可能是 11001。
3. 在 RetryAdo2.exe 繼續執行時，重新連接網路。
4. 觀看主控台報告後續的重試成功。


### D.3 暫時拼錯伺服器名稱

1. 暫時將 40615 當作另一個錯誤號碼加入 **TransientErrorNumbers** 中，並重新編譯。
2. 在 `new S.SqlConnectionStringBuilder()` 這行設定中斷點。
3. 使用「編輯後繼續」功能刻意拼錯伺服器名稱，這在下面幾行。
  - 讓程式執行，並返回您的中斷點。
  - 會發生錯誤 40615。
4. 修正拼字錯誤。
5. 讓程式執行並成功完成。
6. 移除 40615，然後重新編譯。


## E.相關連結

- [SQL Database 的用戶端快速入門程式碼範例](sql-database-develop-quick-start-client-code-samples.md)
- [試用 SQL Database：透過 SQL Database Library for .NET 使用 C#; 建立 SQL Database](sql-database-get-started-csharp.md)

<!---HONumber=AcomDC_0413_2016-->