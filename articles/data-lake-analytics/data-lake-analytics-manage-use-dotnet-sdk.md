<properties 
   pageTitle="使用 Azure .NET SDK 管理 Azure Data Lake Analytics | Azure" 
   description="了解如何管理資料湖分析工作、資料來源、使用者。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="jgao"/>

# 使用 Azure .NET SDK 管理 Azure Data Lake Analytics

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

瞭解如何使用 Azure .NET SDK 管理 Azure Data Lake Analytics 的帳戶、資料來源、使用者和作業。若要使用其他工具查看管理主題，請按一下上方的索引標籤選取器。

**必要條件**

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。


<!-- ################################ -->
<!-- ################################ -->


## 連接到 Azure Data Lake Analytics

您需要下列 Nuget 封裝：

	Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
	Install-Package Microsoft.Azure.Common 
	Install-Package Microsoft.Azure.Management.ResourceManager -Pre
	Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


下列程式碼範例示範如何連接至 Azure，並在 Azure 訂用帳戶下列出現有的 Data Lake Analytics 帳戶。

	using System;
	using System.Collections.Generic;
	using System.Threading;

	using Microsoft.Rest;
	using Microsoft.Rest.Azure.Authentication;

	using Microsoft.Azure.Management.ResourceManager;
	using Microsoft.Azure.Management.DataLake.Store;
	using Microsoft.Azure.Management.DataLake.Analytics;
	using Microsoft.Azure.Management.DataLake.Analytics.Models;

	namespace ConsoleAcplication1
	{
		class Program
		{

			private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
			private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
			private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

			private static DataLakeAnalyticsAccountManagementClient _adlaClient;

			private static void Main(string[] args)
			{

				var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

				_adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
				_adlaClient.SubscriptionId = SUBSCRIPTIONID;

				var adlaAccounts = ListADLAAccounts();

				Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
				for (int i = 0; i < adlaAccounts.Count; i ++)
				{
					Console.WriteLine(adlaAccounts[i].Name);
				}

				System.Console.WriteLine("Press ENTER to continue");
				System.Console.ReadLine();
			}

			public static ServiceClientCredentials AuthenticateAzure(
			string domainName,
			string nativeClientAppCLIENTID)
			{
				// User login via interactive popup
				SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
				// Use the client ID of an existing AAD "Native Client" application.
				var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
				return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
			}

			public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
			{
				var response = _adlaClient.Account.List();
				var accounts = new List<DataLakeAnalyticsAccount>(response);

				while (response.NextPageLink != null)
				{
					response = _adlaClient.Account.ListNext(response.NextPageLink);
					accounts.AddRange(response);
				}

				return accounts;
			}
		}
	}


## 管理帳戶

您必須擁有資料湖分析帳戶，才能執行任何資料湖分析工作。與 Azure HDInsight 不同的是，分析帳戶未執行工作時，您無需支付該帳戶的費用。您只需支付執行工作時的費用。如需詳細資訊，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

###建立帳戶

您必須有 Azure Resource Management 群組和 Data Lake Store 帳戶，才可以執行下列範例。

下列程式碼示範如何建立資源群組：

	public static async Task<ResourceGroup> CreateResourceGroupAsync(
		ServiceClientCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{

		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential)
		{ SubscriptionId = subscriptionId };
		var resourceGroup = new ResourceGroup { Location = location };
		return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
	}

下列程式碼示範如何建立 Data Lake Store 帳戶：

	var adlsParameters = new DataLakeStoreAccount(location: _location);
	_adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

下列程式碼示範如何建立 Data Lake Analytics 帳戶：

	var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
	var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
	var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
	var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

###列出帳戶

請參閱[連接到 Azure Data Lake Analytics](#connect_to_azure_data_lake_analytics)。

###尋找帳戶

一旦您取得 Data Lake Analytics 帳戶清單物件，就可以使用下列程式碼尋找帳戶︰

	Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
	var myAdlaAccount = adlaAccounts.Find(accountFinder);

###刪除資料湖分析帳戶

下列程式碼片段會刪除 Data Lake Analytics 帳戶：

	_adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## 管理帳戶資料來源

資料湖分析目前支援下列資料來源：

- [Azure 資料湖儲存體](../data-lake-store/data-lake-store-overview.md)
- [Azure 儲存體](../storage/storage-introduction.md)

當您建立分析帳戶時，必須指定 Azure 資料湖儲存體帳戶作為預設的儲存體帳戶。預設的資料湖存放區帳戶是用來儲存工作中繼資料與工作稽核記錄。建立分析帳戶後，就可以新增其他資料湖儲存體帳戶和/或 Azure 儲存體帳戶。

### 尋找預設的資料湖存放區帳戶

請參閱本文中的＜尋找帳戶＞尋找 Data Lake Analytics 帳戶。然後使用下列程式碼：

	string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## 使用 Azure 資源管理員群組

應用程式通常由許多元件組成，例如，Web 應用程式、資料庫、資料庫伺服器、儲存體和協力廠商服務。Azure Resource Manager 可讓您將應用程式中的資源做為群組使用，稱為 Azure 資源群組。您可以透過單一、協調的作業來部署、更新、監視或刪除應用程式的所有資源。您會使用部署的範本，且該範本可以用於不同的環境，例如測試、預備和生產環境。您可以檢視整個群組的彙總成本，為您的組織釐清計費。如需詳細資訊，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。

資料湖分析服務可包含下列元件：

- Azure 資料湖分析帳戶
- 必要的預設 Azure 資料湖儲存體帳戶
- 其他 Azure 資料湖儲存體帳戶
- 其他 Azure 儲存體帳戶

您可以在某個資源管理群組下建立上述所有元件，這樣更容易管理。

![Azure 資料湖分析帳戶與儲存體](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

資料湖分析帳戶和相依的儲存體帳戶必須位於相同的 Azure 資料中心。但資源管理群組可位在不同的資料中心內。

##另請參閱 

- [Microsoft Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 Azure 入口網站開始使用 Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
- [使用 Azure 入口網站監視和疑難排解 Azure Data Lake Analytics 作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0928_2016-->