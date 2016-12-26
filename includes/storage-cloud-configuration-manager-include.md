[適用於 .NET 的 Microsoft Azure Configuration Manager 程式庫](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) 會提供一個類別，可用來剖析組態檔中的連接字串。 [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) 類別可剖析組態設定，無論用戶端應用程式是在桌面、在行動裝置上、在 Azure 虛擬機器中，或在 Azure 雲端服務中執行。

若要參考 CloudConfigurationManager 套件，請新增下列 `using` 指示詞︰

```csharp
using Microsoft.Azure;    //Namespace for CloudConfigurationManager
```

以下範例示範如何從組態檔擷取連接字串：

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

是否使用 Azure Configuration Manager 可由您選擇。 您也可以使用 API，例如 .NET Framework 的 [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) 類別。



<!--HONumber=Dec16_HO2-->


