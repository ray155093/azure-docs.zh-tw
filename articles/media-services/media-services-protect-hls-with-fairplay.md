<properties 
	pageTitle="使用 Azure 媒體服務串流以 Apple FairPlay 保護的 HLS 內容" 
	description="本主題提供概觀及顯示如何使用 Azure 媒體服務，以 Apple FairPlay 動態加密您的 HTTP 即時資料流 (HLS) 內容。它也會顯示如何使用媒體服務授權傳遞服務，傳遞 FairPlay 授權給用戶端。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="05/11/2016"
	ms.author="juliako"/>

#使用 Azure 媒體服務串流以 Apple FairPlay 保護的 HLS 內容 

Azure 媒體服務可讓您使用下列格式，動態加密您的 HTTP 即時資料流 (HLS) 內容︰

- **AES-128 信封清除金鑰** - 使用 **AES-128 CBC** 模式加密整個區塊。iOS 和 OSX 播放程式原本就支援資料流解密。如需詳細資訊，請參閱[本篇文章](media-services-protect-with-aes128.md)。

- **Apple FairPlay** - 使用 **AES-128 CBC** 模式加密個別的視訊和音訊範例。**FairPlay 串流** (FPS) 已整合至裝置工作系統，在 iOS 和 Apple 電視上具有原生支援。在 OS X 上的 Safari 可讓您使用加密媒體擴充功能 (EME) 介面支援的 FPS。

	>[AZURE.NOTE]
	使用 AMS 傳遞以 FairPlay 加密的 HLS，此做法目前為預覽版本。


下圖顯示「FairPlay 動態加密」工作流程。

![使用 FairPlay 保護](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

本主題示範如何使用 Azure 媒體服務，以 Apple FairPlay 動態加密 HLS 內容。它也會顯示如何使用媒體服務授權傳遞服務，傳遞 FairPlay 授權給用戶端。

	
## 需求和考量

- 以下是使用 AMS 傳遞以 FairPlay 加密的 HLS，以及傳遞 FairPlay 授權時，需要的項目。

	- 一個 Azure 帳戶。如需詳細資訊，請參閱 [Azure 免費試用](/pricing/free-trial/?WT.mc_id=A261C142F)。
	- 媒體服務帳戶。若要建立媒體服務帳號，請參閱[建立帳戶](media-services-create-account.md)。
	- 註冊 [Apple Development Program](https://developer.apple.com/)。
	- Apple 要求內容擁有者必須取得[部署套件](https://developer.apple.com/contact/fps/)。請在要求中說明您已使用 Azure 媒體服務實作 KSM (金鑰安全性模組)，現在想要求最終的 FPS 封裝。最終 FPS 封裝中提供指示來產生憑證和取得 ASK，供您用來設定 FairPlay。 

	- Azure 媒體服務 .NET SDK **3.6.0** 版或更新版本。

- 在 AMS 金鑰傳遞端必須設定下列各項︰
	- **應用程式憑證 (AC)** - 包含私密金鑰的 .pfx 檔案。這個檔案是由客戶建立，而且由同一位客戶使用密碼加密。 
		
	 	當客戶設定金鑰傳遞原則時，他們必須提供該密碼和 base64 格式的 .pfx。

	- **應用程式憑證密碼** - 用來建立 .pfx 檔案的客戶密碼。
	- **應用程式憑證密碼識別碼** - 類似於上傳其他 AMS 金鑰一樣，客戶必須使用 **ContentKeyType.FairPlayPfxPassword** 列舉值來上傳密碼。在結果中，他們會得到 AMS 識別碼，這是他們要在金鑰傳遞原則選項內使用所需之物。
	- **IV** - 16 個位元組的隨機值，必須符合資產傳遞原則中的 iv。客戶會產生 IV，並將它放在兩個位置︰資產傳遞原則和金鑰傳遞原則選項。 
	- **ASK** - 當您使用 Apple 開發人員入口網站產生憑證時，將會收到 ASK (應用程式密碼金鑰)。每個開發小組都會收到一個唯一的 ASK。請儲存一份 ASK，並將它存放在安全的地方。您之後必須將 ASK 設定為 Azure 媒體服務的 FairPlayAsk。 
	-  **ASK ID** - 在客戶將 ASK 上傳到 AMS 時取得。客戶必須使用 **ContentKeyType.FairPlayASk** 列舉值來上傳 ASK。結果，將傳回 AMS 識別碼，設定金鑰傳遞原則選項時應該使用此識別碼。

- FPS 用戶端必須設定下列各項︰
 	- **應用程式憑證 (AC)** - .cer/.der 檔案，包含作業系統用來加密某些承載的公開金鑰。AMS 必須了解它，因為播放程式需要它。金鑰傳遞服務會使用對應的私密金鑰來解密。

- 若要播放 FairPlay 加密的資料流，您需要先取得真正的 ASK，然後產生真正的憑證。該程序會建立所有 3 個部分︰

	-  .der、 
	-  .pfx 和 
	-  .pfx 的密碼。
 
- 支援 HLS 與 **AES-128 CBC** 加密的用戶端︰OS X、Apple TV、iOS 上的 Safari。

##設定 FairPlay 動態加密和授權傳遞服務的步驟

以下是您使用 FairPlay 來保護資產、使用媒體服務授權傳遞服務，以及使用動態加密時，需要執行的一般步驟。

1. 建立資產並將檔案上傳到資產。 
1. 將包含檔案的資產編碼為自適性位元速率 MP4 集。
1. 建立內容金鑰，並將它與編碼的資產產生關聯。  
1. 設定內容金鑰的授權原則。在建立內容金鑰授權原則時，您需要指定下列各項︰ 
	
	- 傳遞方法 (在本案例中為 FairPlay)、 
	- FairPlay 原則選項組態。如需有關如何設定 FairPlay 的詳細資訊，請參閱下列範例中的 ConfigureFairPlayPolicyOptions() 方法。
	
		>[AZURE.NOTE] 在大部分情況下，您只想要設定一次 FairPlay 原則選項，因為您只會有一組憑證和 ASK。
	- 限制 (開放或權杖) 
	- 以及定義如何將金鑰傳遞至用戶端的金鑰傳遞類型的特定資訊。 
	
2. 設定資產傳遞原則。傳遞原則組態包括︰

	- 傳遞通訊協定 (HLS)、 
	- 動態加密類型 (在此案例中為 Common CBC Encryption)、 
	- 授權取得 URL。 
	
	>[AZURE.NOTE]如果您想要傳遞使用 FairPlay + 另一個 DRM 加密的資料流，必須設定個別的傳遞原則
	>
	>- 一個 IAssetDeliveryPolicy 以設定使用 CENC (PlayReady + WideVine) 的 DASH，以及使用 PlayReady 的 Smooth。 
	>- 另一個 IAssetDeliveryPolicy 以設定 HLS 的 FairPlay

1. 若要取得串流 URL，請建立隨選定位器。

##透過播放程式/用戶端應用程式使用 FairPlay 金鑰傳遞

客戶可以使用 iOS SDK 開發播放應用程式。為了能夠播放 FairPlay 內容，客戶必須實作授權交換通訊協定。授權交換通訊協定並非由 Apple 所指定，而是由各個應用程式決定如何傳送金鑰傳遞要求。AMS FairPlay 金鑰傳遞服務會對即將到來的 SPC 視為如下列格式的 www-form-url 已編碼張貼訊息：

	spc=<Base64 encoded SPC>

>[AZURE.NOTE] Azure 媒體播放器不支援現成的 FairPlay 播放。客戶需要從 Apple 開發人員帳戶取得範例播放程式，才能在 MAC OSX 上播放 FairPlay。
 

##.NET 範例


下列範例會示範在 Azure Media Services SDK for .Net 3.6.0 版引進的功能 (使用 Azure 媒體服務來傳遞使用 FairPlay 加密內容的能力)。下列 Nuget 封裝命令可用來安裝封裝：

	PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. 建立新的主控台專案。
1. 使用 NuGet 來安裝和加入 Azure 媒體服務 .NET SDK。
2. 加入其他參考資料：System.Configuration。
2. 新增包含帳戶名稱和金鑰資訊的組態檔：
	
		<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
			  <appSettings>
			
			    <add key="MediaServicesAccountName" value="AccountName"/>
			    <add key="MediaServicesAccountKey" value="AccountKey"/>
			
			    <add key="Issuer" value="http://testacs.com"/>
			    <add key="Audience" value="urn:test"/>
			  </appSettings>
		</configuration>

1. 為您計畫從該處傳遞內容的串流端點至少取得一個串流單元。如需詳細資訊，請參閱[設定串流端點](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal)。

1. 以本章節中所顯示的程式碼覆寫 Program.cs 檔案中的程式碼。
			
		
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Threading;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
		using Newtonsoft.Json;
		using System.Security.Cryptography.X509Certificates;
		
		namespace DynamicEncryptionWithFairPlay
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        private static readonly Uri _sampleIssuer =
		            new Uri(ConfigurationManager.AppSettings["Issuer"]);
		        private static readonly Uri _sampleAudience =
		            new Uri(ConfigurationManager.AppSettings["Audience"]);
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            bool tokenRestriction = false;
		            string tokenTemplateString = null;
		
		            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
		            Console.WriteLine("Uploaded asset: {0}", asset.Id);
		
		            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
		            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
		
		            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
		            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
		            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
		            Console.WriteLine();
		
		            if (tokenRestriction)
		                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
		            else
		                AddOpenAuthorizationPolicy(key);
		
		            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
		            Console.WriteLine();
		
		            CreateAssetDeliveryPolicy(encodedAsset, key);
		            Console.WriteLine("Created asset delivery policy. \n");
		            Console.WriteLine();
		
		            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
		            {
		                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
		                // back into a TokenRestrictionTemplate class instance.
		                TokenRestrictionTemplate tokenTemplate =
		                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
		
		                // Generate a test token based on the the data in the given TokenRestrictionTemplate.
		                // Note, you need to pass the key id Guid because we specified 
		                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
		                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
		                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
		                                                                        DateTime.UtcNow.AddDays(365));
		                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
		                Console.WriteLine();
		            }
		
		            string url = GetStreamingOriginLocator(encodedAsset);
		            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
		
		            Console.ReadLine();
		        }
		
		        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
		        {
		            if (!File.Exists(singleFilePath))
		            {
		                Console.WriteLine("File does not exist.");
		                return null;
		            }
		
		            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
		            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
		
		            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
		
		            Console.WriteLine("Created assetFile {0}", assetFile.Name);
		
		            var policy = _context.AccessPolicies.Create(
		                                    assetName,
		                                    TimeSpan.FromDays(30),
		                                    AccessPermissions.Write | AccessPermissions.List);
		
		            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
		
		            Console.WriteLine("Upload {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            locator.Delete();
		            policy.Delete();
		
		            return inputAsset;
		        }
		
		        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
		        {
		            var encodingPreset = "H264 Multiple Bitrate 720p";
		
		            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
		                                    inputAsset.Name,
		                                    encodingPreset));
		
		            var mediaProcessors =
		                _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
		
		            var latestMediaProcessor =
		                mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
		
		            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
		            encodeTask.InputAssets.Add(inputAsset);
		            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 	AssetCreationOptions.StorageEncrypted);
		
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		            job.Submit();
		            job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		            return job.OutputMediaAssets[0];
		        }
		
		        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
		        {
		            // Create HLS SAMPLE AES encryption content key
		            Guid keyId = Guid.NewGuid();
		            byte[] contentKey = GetRandomBuffer(16);
		
		            IContentKey key = _context.ContentKeys.Create(
		                                    keyId,
		                                    contentKey,
		                                    "ContentKey",
		                                    ContentKeyType.CommonEncryptionCbcs);
		
		            // Associate the key with the asset.
		            asset.ContentKeys.Add(key);
		
		            return key;
		        }
		
		
		        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
		        {
		            // Create ContentKeyAuthorizationPolicy with Open restrictions 
		            // and create authorization policy          
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		                    {
		                        new ContentKeyAuthorizationPolicyRestriction
		                        {
		                            Name = "Open",
		                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
		                            Requirements = null
		                        }
		                    };
		
		
		            // Configure FairPlay policy option.
		            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
		
		            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("",
		                ContentKeyDeliveryType.FairPlay,
		                restrictions,
		                FairPlayConfiguration);
		
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common CBC Content Key with no restrictions").
		                        Result;
		
		            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
		
		            // Associate the content key authorization policy with the content key.
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		        }
		
		        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
		        {
		            string tokenTemplateString = GenerateTokenRequirements();
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		                    {
		                        new ContentKeyAuthorizationPolicyRestriction
		                        {
		                            Name = "Token Authorization Policy",
		                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
		                            Requirements = tokenTemplateString,
		                        }
		                    };
		
		            // Configure FairPlay policy option.
		            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
		
		
		            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
		                       ContentKeyDeliveryType.FairPlay,
		                       restrictions,
		                       FairPlayConfiguration);
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common CBC Content Key with token restrictions").
		                        Result;
		
		            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
		
		            // Associate the content key authorization policy with the content key
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		
		            return tokenTemplateString;
		        }
		
		        private static string ConfigureFairPlayPolicyOptions()
		        {
		            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
		            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
		            byte[] askBytes = Guid.NewGuid().ToByteArray();
		            var askId = Guid.NewGuid();
		            // Key delivery retrieves askKey by askId and uses this key to generate the response.
		            IContentKey askKey = _context.ContentKeys.Create(
		                                    askId,
		                                    askBytes,
		                                    "askKey",
		                                    ContentKeyType.FairPlayASk);
		
		            //Customer password for creating the .pfx file.
		            string pfxPassword = "<customer password for creating the .pfx file>";
		            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
		            var pfxPasswordId = Guid.NewGuid();
		            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
		            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
		                                    pfxPasswordId,
		                                    pfxPasswordBytes,
		                                    "pfxPasswordKey",
		                                    ContentKeyType.FairPlayPfxPassword);
		
		            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
		            byte[] iv = Guid.NewGuid().ToByteArray();
		
		            //Specify the .pfx file created by the customer.
		            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
		
		            string FairPlayConfiguration =
		                Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
		                    appCert,
		                    pfxPassword,
		                    pfxPasswordId,
		                    askId,
		                    iv);
		
		            return FairPlayConfiguration;
		        }
		
		        static private string GenerateTokenRequirements()
		        {
		            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
		
		            template.PrimaryVerificationKey = new SymmetricVerificationKey();
		            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
		            template.Audience = _sampleAudience.ToString();
		            template.Issuer = _sampleIssuer.ToString();
		            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
		
		            return TokenRestrictionTemplateSerializer.Serialize(template);
		        }
		
		        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
		        {
		            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
		
		            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
		
		            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
		
		            // Get the FairPlay license service URL.
		            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
		
					// The reason the below code replaces "https://" with "skd://" is because
					// in the IOS player sample code which you obtained in Apple developer account, 
					// the player only recognizes a Key URL that starts with skd://. 
					// However, if you are using a customized player, 
					// you can choose whatever protocol you want. 
					// For example, "https". 

		            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
		                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
		                {
		                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
		                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
		                };
		
		            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
		                    "AssetDeliveryPolicy",
		                AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
		                AssetDeliveryProtocol.HLS,
		                assetDeliveryPolicyConfiguration);
		
		            // Add AssetDelivery Policy to the asset
		            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
		
		        }
		
		
		        /// <summary>
		        /// Gets the streaming origin locator.
		        /// </summary>
		        /// <param name="assets"></param>
		        /// <returns></returns>
		        static public string GetStreamingOriginLocator(IAsset asset)
		        {
		
		            // Get a reference to the streaming manifest file from the  
		            // collection of files in the asset. 
		
		            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
		                                         EndsWith(".ism")).
		                                         FirstOrDefault();
		
		            // Create a 30-day readonly access policy. 
		            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		                TimeSpan.FromDays(30),
		                AccessPermissions.Read);
		
		            // Create a locator to the streaming content on an origin. 
		            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		                policy,
		                DateTime.UtcNow.AddMinutes(-5));
		
		            // Create a URL to the manifest file. 
		            return originLocator.Path + assetFile.Name;
		        }
		
		        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
		                ((IJob)sender).Name,
		                e.CurrentState,
		                DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
		        }
		
		        static private byte[] GetRandomBuffer(int length)
		        {
		            var returnValue = new byte[length];
		
		            using (var rng =
		                new System.Security.Cryptography.RNGCryptoServiceProvider())
		            {
		                rng.GetBytes(returnValue);
		            }
		
		            return returnValue;
		        }
		    }
		}


##後續步驟：媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0518_2016-->