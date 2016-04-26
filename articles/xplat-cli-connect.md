<properties
	pageTitle="從 CLI 登入 Azure |Microsoft Azure"
	description="從適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (Azure CLI) 連接到您的 Azure 訂用帳戶"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2015"
	ms.author="danlep"/>

# 從 Azure 命令列介面 (Azure CLI) 連接到 Azure 訂用帳戶

Azure CLI 是一組開放原始碼的跨平台命令，可供您運用在 Azure 平台上。本文將描述提供 Azure 帳戶認證以將 Azure CLI 連線至 Azure 訂用帳戶的方式。如果您尚未安裝 CLI，請參閱[安裝 Azure CLI](xplat-cli-install.md)。如果您沒有 Azure 訂用帳戶，則只需要幾分鐘的時間就可以建立[免費帳戶](http://azure.microsoft.com/free/)。

有兩種方法可從 Azure CLI 連接到您的訂用帳戶：

* **使用工作或學校帳戶或 Microsoft 帳戶身分識別登入 Azure** - 使用 CLI 0.9.10 以上版本中的 `azure login` 命令以及任何類型的帳戶身分識別，以透過 Azure Active Directory 進行驗證。CLI (0.9.9 以上版本) 也支援針對已啟用多重要素驗證的帳戶透過 web 入口網站進行互動式驗證。也請使用 `azure login` 命令來驗證 Azure Active Directory 應用程式的服務主體，可用來執行自動化的服務。利用支援的帳戶身分識別登入之後，您可以使用 Azure Resource Manager 模式或 Azure 服務管理模式命令。

* **下載和使用發佈設定檔案** - 這會在您的本機電腦上安裝憑證，只要訂用帳戶和憑證有效，該憑證便可讓您執行管理工作。此方法只允許您使用 Azure 服務管理模式命令。

>[AZURE.NOTE] 如果您使用的是 Azure CLI 0.9.10 版之前的版本，只能搭配公司或學校帳戶使用 `azure login` 命令，無法使用 Microsoft 帳戶身分識別。不過，您可以依意願[從 Microsoft 帳戶識別碼建立工作或學校識別碼](virtual-machines/virtual-machines-windows-create-aad-work-id.md)。

如需不同帳戶身分識別與 Azure 訂用帳戶的背景，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](./active-directory/active-directory-how-subscriptions-associated-directory.md)。

## 使用 azure 登入以互動方式透過 web 入口網站進行驗證

使用 `azure login` 命令 (不含任何引數)，以下列其中一種方式進行互動驗證：

- 需要 Multi-Factor Authentication 的公司或學校帳戶身分識別 (也稱為組織帳戶)，或
- 當您想要存取資源管理員模式命令時的 Microsoft 帳戶身分識別

> [AZURE.NOTE]  在這兩種情況下，驗證和授權都是使用 Azure Active Directory 來執行。如果您使用 Microsoft 帳戶身分識別，登入程序就會存取您的 Azure Active Directory 預設網域(如果您註冊免費 Azure 帳戶，可能不會發覺 Azure Active Directory 為您的帳戶建立了預設網域)。

以互動方式登入很簡單：輸入 `azure login` 並依照提示操作，如下所示：

	azure login                                                                                                                                                                                         
	info:    Executing command login
	info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate. If you're signing in as an Azure AD application, use the --username and --password parameters.

複製上方提供的程式碼，開啟瀏覽器至 http://aka.ms/devicelogin。輸入程式碼後，系統會提示您針對您要使用之身分識別輸入使用者名稱和密碼。完成此程序之後，命令殼層便完成登入程序。會出現類似下面的畫面：

	info:    Added subscription Visual Studio Ultimate with MSDN
	info:    Added subscription Azure Free Trial
	info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
	+
	info:    login command OK

## 使用 azure 登入及組織帳戶的使用者名稱與密碼


在您想要使用不需要多重要素驗證的工作或學校帳戶時，使用 `azure login` 命令與使用者名稱參數，或者使用使用者名稱與密碼進行驗證。下列範例會傳遞組織帳戶的使用者名稱︰

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

在系統提示時輸入您的密碼。

如果這是您第一次使用這些認證來登入，系統會要求您確認是否要快取驗證權杖。如果您先前曾經使用 (如本文稍後所述的) `azure logout` 命令，也會出現此提示。如果要在自動化作業中略過此提示，請使用 `azure login` 參數執行 `-q`。

   

## 使用 azure 登入與服務主體

如果您已建立 Active Directory 應用程式的服務主體，而且服務主體擁有訂用帳戶的權限，您就可以使用 `azure login` 命令來驗證服務主體。根據您的案例，您可以提供服務主體的認證做為 `azure login` 命令的明確參數，或透過 CLI 指令碼或應用程式程式碼。您也可以使用憑證以非互動方式驗證自動化案例的服務主體。如需詳細資料與範例，請參閱[使用 Azure Resource Manager 驗證服務主體](resource-group-authenticate-service-principal.md)。

## 使用發行設定檔方法

如果您只需要使用 Azure 服務管理模式 CLI 命令，您可以使用發佈設定檔連線。

* **若要下載您帳戶的發佈設定檔**，請使用下列命令 (僅供服務管理模式使用)：

		azure account download

    這會開啟您的預設瀏覽器，提示您登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。登入後便會下載 `.publishsettings` 檔案。請記下此檔案的儲存位置。

    > [AZURE.NOTE] 如果您的帳戶與多個 Azure Active Directory 租用戶相關聯，則可能會提示您選取要在其中下載發行設定檔的 Active Directory。

    在使用下載頁面或是造訪 Azure 傳統入口網站選定之後，所選的 Active Directory 會成為傳統入口網站與下載頁面使用的預設值。建立好預設值之後，您會在下載頁面上方看到 [click here to return to the selection page]。請使用提供的連結，返回選取頁面。

* **若要匯入發佈設定檔案**，請執行下列命令：

		azure account import <path to your .publishsettings file>

	>[AZURE.IMPORTANT]匯入發佈設定之後，請刪除 `.publishsettings` 檔案。Azure CLI 已不再需要它，而且它可用於存取您的訂用帳戶，因此造成安全風險。

## 多重訂閱

如果您擁有多個 Azure 訂用帳戶，連接到 Azure 將會針對與您的認證相關聯的所有訂用帳戶授予存取權限。系統會選取一個訂用帳戶做為預設值，以供 Azure CLI 用來執行作業。您可以使用 `azure account list` 命令來檢視訂用帳戶及預設的訂用帳戶。此命令會傳回類似以下的資訊：

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

在上述清單中，[目前] 欄會指出目前的預設訂用帳戶為 Azure-sub-1。若要變更預設訂用帳戶，請使用 `azure account set` 命令，並指定您想要設為預設值的訂用帳戶。例如：

	azure account set Azure-sub-2

此動作會將預設訂用帳戶變更為 Azure-sub-2。

> [AZURE.NOTE] 變更預設的訂用帳戶會立即生效，而且是全域變更；無論您是從相同的命令列執行個體或不同的執行個體執行新的 Azure CLI 命令，它們都會使用新的預設訂用帳戶。

如果您要 Azure CLI 使用非預設的訂用帳戶，但又不想變更目前的預設值，可以使用適用於該命令的 `--subscription` 選項，並提供希望該作業使用的訂用帳戶名稱。

一旦您連接到 Azure 訂用帳戶之後，就可以開始搭配使用 Azure CLI 命令和 Azure 資源。

## CLI 命令模式

Azure CLI 提供兩種命令模式來使用具備不同命令集的 Azure 資源︰

* **Azure Resource Manager 模式** - 適用於以資源管理員部署模型使用 Azure 資源。若要設定此模式，請執行 `azure config mode arm`。

* **Azure 服務管理模式** - 適用於以傳統部署模型使用 Azure 資源。若要設定此模式，請執行 `azure config mode asm`。

最初安裝時，CLI 採用服務管理模式。

>[AZURE.NOTE]Azure Resource Manager 模式與 Azure 服務管理模式是互斥的。亦即，任一模式所建立的資源，將無法由另一種模式來管理。

## CLI 設定的儲存位置

無論您是使用 `azure login` 命令登入或是匯入發佈設定，CLI 設定檔和記錄檔都會儲存在您 `user` 目錄中的 `.azure` 目錄內。您的 `user` 目錄會受到作業系統的保護，但建議您採取額外步驟來加密 `user` 目錄。做法如下：

* 在 Windows 中，修改目錄屬性或使用 BitLocker。
* 在 Mac 中，開啟目錄的 FileVault。
* 在 Ubuntu 中，使用「加密主目錄」功能。其他 Linux 散發套件提供類似的功能。

## 登出

若要登出，請使用下列命令：

	azure logout -u <username>

如果與帳戶關聯的訂用帳戶僅能對 Active Directory 驗證，進行登出時就會從本機設定檔中刪除訂用帳戶資訊。不過，如果也已匯入訂用帳戶的發佈設定檔，則進行登出時只會從本機設定檔中刪除 Active Directory 相關的資訊。
## 後續步驟

* 若要使用 Azure CLI 命令，請參閱 [模Azure Resource Manager 模式中的 Azure CLI 命令](./virtual-machines/azure-cli-arm-commands.md)和 [Azure 服務管理模式中的 Azure CLI 命令](virtual-machines-command-line-tools.md)。

* 若要深入了解 Azure CLI、下載來源程式碼、回報問題，或是參與專案，請造訪 [Azure CLI 的 GitHub 儲存機制](https://github.com/azure/azure-xplat-cli)。

* 如果您在使用 Azure CLI 或 Azure 方面遇到問題，請造訪 [Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home) (英文)。

<!---HONumber=AcomDC_0413_2016-->