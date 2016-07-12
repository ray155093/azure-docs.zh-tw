<properties
   pageTitle="金鑰保存庫開發人員指南 | Microsoft Azure"
   description="開發人員可以使用 Azure 金鑰保存庫來管理 Microsoft Azure 環境中的密碼編譯金鑰。"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/25/2016"
   ms.author="bruceper" />

# Azure 金鑰保存庫開發人員指南
透過金鑰保存庫，您可以從應用程式內安全地存取機密資訊，進而達到以下效果︰

- 不需要自行撰寫程式碼即可保護金鑰和密碼，而且也能輕易地從應用程式加以使用。
- 可以讓客戶擁有及管理他們自己的金鑰，因此您可以致力於提供核心軟體功能。透過這種方式，您的應用程式將不需要對客戶的租用戶金鑰和密碼負起責任或潛在責任。
- 您的應用程式能使用金鑰進行簽署和加密，卻也能在應用程式外部管理金鑰，如此一來此解決方案便適用於位於不同地點的應用程式。

如需 Azure 金鑰保存庫的一般詳細資訊，請參閱[什麼是金鑰保存庫？](key-vault-whatis.md)。

## 影片
此影片示範如何建立專屬金鑰保存庫以及如何從 'Hello Key Vault' 範例應用程式使用它。

影片中所提及資源的連結︰
- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure 金鑰保存庫範例程式碼](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

若要深入了解，您可以遵循 [Key Vault Blog](http://aka.ms/kvblog) (金鑰保存庫部落格)，並加入 [Key Vault Forum (金鑰保存庫論壇)](http://aka.ms/kvforum)。



> [AZURE.VIDEO azure-key-vault-developer-quick-start]



## 建立及管理金鑰保存庫

在程式碼中使用 Azure 金鑰保存庫之前，您可以透過 REST、資源管理員範本、PowerShell 或 CLI 來建立及管理保存庫，如以下文章所述︰

- [使用 REST 建立和管理金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [使用 PowerShell 建立和管理金鑰保存庫](key-vault-get-started.md)
- [使用 CLI 建立和管理金鑰保存庫](key-vault-manage-with-cli.md)
- [建立金鑰保存庫並透過 ARM 範本新增密碼](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] 涉及金鑰保存庫的作業乃透過 AAD 進行驗證，以及透過金鑰保存庫自己的存取原則 (每個保存庫有各自的定義) 進行授權。

## 撰寫金鑰保存庫的程式碼

程式設計人員的金鑰保存庫管理系統由幾個介面組成，並以 REST 作為基礎 ([Key Vault REST API Reference (金鑰保存庫 REST API 參考))](https://msdn.microsoft.com/library/azure/dn903609.aspx)。

已成功取得授權的使用者可以執行下列作業：

- 使用 [[建立]](https://msdn.microsoft.com/library/azure/dn903634.aspx)、[[匯入]](https://msdn.microsoft.com/library/azure/dn903626.aspx)、[[更新]](https://msdn.microsoft.com/library/azure/dn903616.aspx)、[[刪除]](https://msdn.microsoft.com/library/azure/dn903611.aspx) 和其他作業管理密碼編譯金鑰。

- 使用 [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx)、[Update](https://msdn.microsoft.com/library/azure/dn986818.aspx)、[Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) 和其他作業管理密碼。

- 以 [[簽章]](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[[驗證]](https://msdn.microsoft.com/library/azure/dn878082.aspx)、[[包裝金鑰]](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[[解除包裝金鑰]](https://msdn.microsoft.com/library/azure/dn878079.aspx) 和 [[加密]](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[[解密]](https://msdn.microsoft.com/library/azure/dn878097.aspx) 作業使用密碼編譯金鑰。

以下 SDK 適用於操作金鑰保存庫︰

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[.NET SDK 文件](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Node.js SDK 文件](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[.NET SDK 封裝](https://azure.microsoft.com/documentation/api/)|[Node.js SDK 封裝](https://www.npmjs.com/package/azure-keyvault)|


如需搭配使用金鑰保存庫和應用程式的完整範例，請參閱︰

- .NET 範例應用程式 *HelloKeyVault* 和 Azure Web 服務範例。[Azure 金鑰保存庫程式碼範例](http://www.microsoft.com/download/details.aspx?id=45343)
- 幫助您了解如何從 Azure 之 Web 應用程式使用 Azure 金鑰保存庫的教學課程。[從 Web 應用程式使用 Azure 金鑰保存庫](key-vault-use-from-web-application.md)

## 作法

下列文章和案例提供使用 Azure 金鑰保存庫的工作特定指引：

- [如何為 Azure 金鑰保存庫產生並傳輸受 HSM 保護的金鑰](key-vault-hsm-protected-keys.md) - 這將協助您規劃、產生並傳輸專屬受 HSM 保護的金鑰，以搭配 Azure 金鑰保存庫使用。
- [如何在部署期間傳遞安全值 (例如密碼)](../resource-manager-keyvault-parameter.md) - 當您需要在部署期間傳遞安全值 (例如密碼) 作為參數時，可以將該值儲存為 Azure 金鑰保存庫中的密碼，並在其他資源管理員範本中參考該值。
- [如何搭配使用金鑰保存庫與 SQL Server 進行可延伸金鑰管理](https://msdn.microsoft.com/library/dn198405.aspx) - 適用於 Azure 金鑰保存庫的 SQL Server 連接器會啟用 SQL Server 和 SQL-in-a-VM，利用 Azure 金鑰保存庫服務作為可延伸金鑰管理 (EKM) 提供者來保護其針對應用程式連結的加密金鑰；透明資料加密、備份加密和資料行層級加密。
- [如何將憑證從金鑰保存庫部署至 VM](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - 在 Azure 上的 VM 中執行的雲端應用程式需要憑證。現在應如何讓此憑證進入此 VM？
- [如何將金鑰保存庫與適用於 Windows 和 Linux IaaS VM 的 Azure 磁碟加密整合](../azure-security-disk-encryption.md) - 此磁碟加密解決方案與 Azure 金鑰保存庫整合，可幫助您控制和管理您的金鑰保存庫訂用帳戶中的磁碟加密金鑰和密碼，同時確保虛擬機器磁碟中的所有資料會在您的 Azure 儲存體中輕鬆加密。

如需整合金鑰保存庫和 Azure 及搭配使用的詳細工作特定指引，請參閱 [Ryan Jones 金鑰保存庫 ARM 範本範例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。

## 與金鑰保存庫整合

這些文章是關於其他可讓我們與金鑰保存庫整合的案例和服務。

- [Azure 磁碟加密](../azure-security-disk-encryption.md)利用 Windows 的業界標準 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，為 OS 和資料磁碟提供磁碟區加密。此解決方案與 Azure 金鑰保存庫整合，可幫助您控制和管理您的金鑰保存庫訂用帳戶中的磁碟加密金鑰和密碼，同時確保虛擬機器磁碟中的所有資料會在您的 Azure 儲存體中輕鬆加密。


## 支援程式庫

- [Microsoft Azure 金鑰保存庫核心程式庫](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0)提供 `IKey` 和 `IKeyResolver` 介面，以從識別碼尋找金鑰和使用金鑰執行作業。

- [Microsoft Azure 金鑰保存庫延伸模組](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0)提供 Azure 金鑰保存庫的擴充功能。

## 其他金鑰保存庫資源
- [Key Vault Blog (金鑰保存庫部落格)](http://aka.ms/kvblog)
- [Key Vault Forum (金鑰保存庫論壇)](http://aka.ms/kvforum)

<!---HONumber=AcomDC_0629_2016-->