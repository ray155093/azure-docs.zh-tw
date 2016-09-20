<properties
   pageTitle="Azure 上的 FreeBSD 簡介 | Microsoft Azure"
   description="了解如何使用 Azure 上的 FreeBSD 虛擬機器"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

#Azure 上的 FreeBSD 簡介
本主題提供在 Azure 中執行 FreeBSD 虛擬機器的概觀。

##概觀
適用於 Microsoft Azure 的 FreeBSD 是由 Microsoft Corporation 所提供。FreeBSD 是一個進階的電腦作業系統，用來為新式伺服器、桌上型電腦及內嵌平台提供技術支援。到目前為止，Azure 中可用的是 FreeBSD 10.3 映像。此映像是以 FreeBSD 10.3 版本為基礎。並且安裝了「Azure VM 客體代理程式 [2\.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4)」。此代理程式會負責 FreeBSD VM 與「Azure 網狀架構」之間一些作業的通訊，例如在第一次使用 VM 時佈建 VM (使用者名稱、密碼、主機名稱等) 及啟用選擇性「VM 擴充功能」的功能等作業。至於未來的 FreeBSD 版本，策略是維持最新狀態，在「FreeBSD 版本工程」小組發行最新版本後不久，便立即提供最新版本。即將推出的版本是 [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html)。

##如何部署 FreeBSD 虛擬機器
使用來自 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) 的映像來部署 FreeBSD 虛擬機器相當簡單。

##適用於 FreeBSD 的 VM 擴充功能
FreeBSD VM 中支援的 VM 擴充功能如下。

• [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

「VMAccess 擴充功能」可以：

- 重設原始 sudo 使用者的密碼
- 建立帶有指定密碼的新 sudo 使用者
- 使用指定的金鑰來設定公開主機金鑰
- 重設在 VM 佈建期間提供的公開主機金鑰 (如果未提供主主機金鑰)
- 開啟 SSH 連接埠 (22)，並且如果 reset\_ssh 設定為 true，則還原 sshd\_config
- 移除現有的使用者
- 檢查磁碟
- 修復新增的磁碟

• [CustomSript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)

「CustomScript 擴充功能」可以：

- 如果提供，可從「Azure 儲存體」或外部公用儲存體 (例如 Github) 下載自訂的指令碼
- 執行進入點指令碼
- 支援內嵌命令
- 自動轉換 Shell 和 Python 指令碼中的 Windows 樣式新行字元
- 自動移除 Shell 和 Python 指令碼中的 BOM
- 保護 commandToExecute 中的機密資料

##驗證：使用者名稱、密碼和 SSH 金鑰
使用 Azure 傳統入口網站來建立 FreeBSD 虛擬機器時，系統會要求您提供使用者名稱、密碼或 SSH 公開金鑰。在 Azure 上部署 FreeBSD 虛擬機器時，使用者名稱的選擇受到下列限制：不允許使用已存在於虛擬機器中的系統帳戶名稱 (UID <100)，例如 'root'。到目前為止，只支援「RSA SSH 金鑰」。多行 SSH 金鑰的開頭必須是 ---- BEGIN SSH2 PUBLIC KEY ---- 且結尾必須是 ---- END SSH2 PUBLIC KEY ----。

##如何取得超級使用者權限
在 Azure 上部署虛擬機器執行個體時所指定的使用者帳戶是特殊權限帳戶。“sudo” 套件已安裝在所發佈的 FreeBSD 映像中。使用此使用者帳戶登入之後，您便能夠以 root 身分使用命令語法來執行命令。
  
    # sudo <COMMAND>

您可以選擇使用 sudo -s 來取得 root shell。

##後續步驟
- 前往 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) 以建立 FreeBSD VM。
- 如果您想要將自己的 FreeBSD 攜至 Azure，請參閱[建立並上傳 FreeBSD VHD 到 Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md)。

<!----HONumber=AcomDC_0907_2016-->