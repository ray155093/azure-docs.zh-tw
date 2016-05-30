<properties
	pageTitle="安裝 Azure 命令列介面 | Microsoft Azure"
	description="安裝適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (CLI) 以開始使用 Azure 服務"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/20/2016"
	ms.author="danlep"/>
    
# 安裝 Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli-install.md)

快速安裝 Azure 命令列介面 (Azure CLI) 以使用一組開放原始碼的命令介面式命令，用於建立和管理 Microsoft Azure 中的資源。您有數個安裝選擇：針對不同的作業系統使用提供的其中一個安裝程式封裝、從 npm 封裝中進行安裝，或將 Azure CLI 安裝為 Docker 主機中的容器。如需詳細的選項和背景，請參閱 [GitHub](https://github.com/azure/azure-xplat-cli) 上的專案儲存機制。


安裝好 Azure CLI 之後，您就能[使用 Azure 訂用帳戶將其連接](xplat-cli-connect.md)，並從命令列介面 (Bash、終端機及命令提示字元等) 中執行 **azure** 命令以使用 Azure 資源。


## 使用安裝程式

我們提供以下安裝程式套件：

* [Windows 安裝程式][windows-installer]

* [OS X 安裝程式][mac-installer]

* [Linux 安裝程式][linux-installer]


## 安裝 npm 封裝

或者，如果您的系統已經安裝最新的 Node.js 和 npm，請執行下列命令來安裝 Azure CLI 封裝(在 Linux 散發套件中，您可能需要使用 **sudo**，才能順利執行 __npm__ 命令)。

	npm install azure-cli -g

> [AZURE.NOTE]如果您需要安裝或更新您作業系統的 Node.js 和 npm，請參閱 [Nodejs.org](https://nodejs.org/en/download/package-manager/) 上的文件。建議您安裝最新的 Node.js LTS 版本 (4.x)。如果您使用較舊的版本，可能會發生安裝錯誤。


## 使用 Docker 容器

在 Docker主機中，執行：

```
docker run -it microsoft/azure-cli
```

## 執行 Azure CLI 命令
安裝好 Azure CLI 之後，您就能從命令列使用者介面 (Bash、終端機及命令提示字元等) 中執行 **azure** 命令。例如，若要執行 [說明] 命令，請輸入下列命令：

```
azure help
```
> [AZURE.NOTE]在一些 Linux 散發套件上，您可能會收到錯誤 (/usr/bin/env: ‘node’: 沒有這類檔案或目錄)，這個錯誤來自將安裝在 /usr/bin/nodejs 的最新 nodejs 安裝。若要修正此錯誤，請執行下列命令來建立 /usr/bin/node 的符號連結：

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

若要查看您所安裝的 Azure CLI 版本，請輸入下列命令：

```
azure --version
```

您現在已經準備就緒！ 若要存取所有 CLI 命令以搭配您的資源使用，請[從 Azure CLI 連接到您的 Azure 訂用帳戶](xplat-cli-connect.md)。

>[AZURE.NOTE] 當您第一次使用 Azure CLI 版本 0.9.20 或更新版本時，會看到一則訊息，詢問您是否要允許 Microsoft 收集您如何使用 CLI 的相關資訊。參與為自願性質。如果您選擇參與，則執行 `azure telemetry --disable` 隨時都可以停止。若要隨時啟用參與，請執行 `azure telemetry --enable`。


## 更新 CLI

Microsoft 經常發行 Azure CLI 的更新版本。使用適用於您作業系統的安裝程式重新安裝 CLI，或者，如果您已安裝最新的 Node.js 和 npm，請輸入下列命令進行更新 (您可能需要在 Linux 散發套件上使用 **sudo**)。

```
npm update -g azure-cli
```

## 後續步驟 

* [從 CLI 連接到您的 Azure 訂用帳戶](xplat-cli-connect.md)來建立和管理 Azure 資源。

* 若要深入了解 Azure CLI、下載來源程式碼、回報問題，或是參與專案，請造訪 [Azure CLI 的 GitHub 儲存機制](https://github.com/azure/azure-xplat-cli)。

* 如果您有關於使用 Azure CLI 或 Azure 方面的問題，請造訪 [Azure 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=azurescripting)。

* 對於 Linux 系統，您也可以安裝 Azure CLI，方法是從[原始碼](http://aka.ms/linux-azure-cli)建置它。如需從原始碼進行建置的詳細資訊，請參閱來源封存中內含的 INSTALL 檔案。

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=windowsazurexplatcli&mode=new
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0518_2016-->