<properties
	pageTitle="安裝 Azure 命令列介面 | Microsoft Azure"
	description="安裝適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (CLI) 以開始使用 Azure 服務"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2016"
	ms.author="danlep"/>
    
# 安裝 Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli-install.md)

快速安裝 Azure 命令列介面 (Azure CLI) 以使用一組開放原始碼的命令介面式命令，用於建立和管理 Microsoft Azure 中的資源。有數個選項可讓您在電腦上安裝這些跨平台工具︰

* **npm 套件** - 執行 npm (適用於 JavaScript 的封裝管理員) 將最新的 Azure CLI 套件安裝在您的 Linux 散發套件或作業系統上。您的電腦上需要有 node.js 和 npm。
* **安裝程式** - 下載安裝程式以輕鬆在 Mac 或 Windows 上安裝。
* **Docker 容器** - 在已就緒可執行的 Docker 容器中開始使用最新的 CLI。您的電腦上需要有 Docker 主機。
    
如需詳細的選項和背景，請參閱 [GitHub](https://github.com/azure/azure-xplat-cli) 上的專案儲存機制。

安裝好 Azure CLI 之後，[使用 Azure 訂用帳戶將其連接](xplat-cli-connect.md)，並從命令列介面 (Bash、終端機及命令提示字元等) 中執行 **azure** 命令以使用 Azure 資源。



## 選項 1。安裝 npm 封裝

若要從 npm 套件安裝 CLI，您的系統上將需要安裝最新的 Node.js 和 npm。接著，請執行下列命令來安裝 [npmjs.com](https://www.npmjs.com) 上發佈的 Azure CLI 套件。(在 Linux 散發套件上，您可能需要使用 **sudo**，才能順利執行 __npm__ 命令)。

	npm install -g azure-cli

> [AZURE.NOTE]如果您需要在 Linux 散發套件或作業系統上安裝或更新 Node.js 和 npm，請參閱 [Nodejs.org](https://nodejs.org/en/download/package-manager/) 上的文件。建議您安裝最新的 Node.js LTS 版本 (4.x)。如果您使用較舊的版本，可能會發生安裝錯誤。

如果您喜歡，請將 npm 套件的最新 Linux [tar 檔案][linux-installer]下載到本機。然後，安裝下載的 npm 套件，如下所示 (在 Linux 散發套件上，您可能需要使用 **sudo**)：

    npm install -g <path to downloaded tar file>

## 選項 2。使用安裝程式

如果您使用 Mac 或 Windows 電腦，則有下列 CLI 安裝程式可供下載︰

* [Mac OS X 安裝程式][mac-installer]

* [Windows MSI][windows-installer]

>[AZURE.TIP]在 Windows 中，您也可以下載 [Web Platform Installer](https://go.microsoft.com/?linkid=9828653) 來安裝 CLI。這個安裝程式可讓您在安裝 CLI 之後，再選擇安裝額外的 Azure SDK 和命令列工具。


## 選項 3。使用 Docker 容器

如果您已將電腦設定為 [Docker](https://docs.docker.com/engine/understanding-docker/) 主機，您可以在 Docker 容器中執行最新的 Azure CLI。請執行：

```
docker run -it microsoft/azure-cli
```


## 執行 Azure CLI 命令
安裝好 Azure CLI 之後，從命令列使用者介面 (Bash、終端機及命令提示字元等) 中執行 **azure** 命令。例如，若要執行 [說明] 命令，請輸入下列命令：

```
azure help
```
> [AZURE.NOTE]在某些 Linux 散發套件上，您可能會收到類似 `/usr/bin/env: ‘node’: No such file or directory` 的錯誤訊息。此錯誤訊息是來自最近安裝在 /usr/bin/nodejs 的 Node.js。若要修正此錯誤，請執行此命令來建立 /usr/bin/node 的符號連結︰

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

若要查看您所安裝的 Azure CLI 版本，請輸入下列命令：

```
azure --version
```

您現在已經準備就緒！ 若要存取所有 CLI 命令以與您自己的資源搭配使用，請[從 Azure CLI 連接到您的 Azure 訂用帳戶](xplat-cli-connect.md)。

>[AZURE.NOTE] 當您第一次使用 Azure CLI 時會看到一則訊息，詢問您是否要允許 Microsoft 收集使用情況資訊。參與為自願性質。如果您選擇參與，只要執行 `azure telemetry --disable` 即可隨時停止參與。只要執行 `azure telemetry --enable` 即可隨時啟用參與。


## 更新 CLI

Microsoft 經常發行 Azure CLI 的更新版本。請使用適用於您作業系統的安裝程式來重新安裝 CLI，或執行最新的 Docker 容器。或者，如果您已安裝最新的 Node.js 和 npm，請輸入下列命令來進行更新 (在 Linux 散發套件上，您可能需要使用 **sudo**)。

```
npm update -g azure-cli
```

## 啟用 TAB 鍵自動完成

針對 Mac 和 Linux 提供 CLI 命令 TAB 鍵自動完成支援。

若要在 zsh 中啟用它，請執行︰

```
echo '. <(azure --completion)' >> .zshrc
```

若要在 bash 中啟用它，請執行︰

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## 後續步驟 

* [從 CLI 連接到您的 Azure 訂用帳戶](xplat-cli-connect.md)來建立和管理 Azure 資源。

* 若要深入了解 Azure CLI、下載來源程式碼、回報問題，或是參與專案，請造訪 [Azure CLI 的 GitHub 儲存機制](https://github.com/azure/azure-xplat-cli)。

* 如果您有關於使用 Azure CLI 或 Azure 方面的問題，請造訪 [Azure 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=azurescripting)。

* 對於 Linux 系統，您也可以安裝 Azure CLI，方法是從[原始碼](http://aka.ms/linux-azure-cli)建置它。如需從原始碼進行建置的詳細資訊，請參閱來源封存中內含的 INSTALL 檔案。

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0928_2016-->