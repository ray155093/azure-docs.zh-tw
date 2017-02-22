---
title: "Azure 上的 FreeBSD 簡介 | Microsoft Docs"
description: "了解如何使用 Azure 上的 FreeBSD 虛擬機器"
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2017
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: 71ad04b10bc49500197db6fecdcc0305a1ea0dd2
ms.openlocfilehash: ecb1c385de6c1b12674326afe7d5a0ebf6cd9ad0


---
# <a name="introduction-to-freebsd-on-azure"></a>Azure 上的 FreeBSD 簡介
本主題提供在 Azure 中執行 FreeBSD 虛擬機器的概觀。

## <a name="overview"></a>Overview
適用於 Microsoft Azure 的 FreeBSD 是一套先進的電腦作業系統，可用來為新式伺服器、桌上型電腦及內嵌平台提供技術支援。

Microsoft Corporation 目前在 Azure 上提供已預先設定 [Azure VM 客體代理程式](https://github.com/Azure/WALinuxAgent/)的 FreeBSD 映像。 目前，Microsoft 以映像形式提供下列 FreeBSD 版本：

- FreeBSD 10.3-RELEASE
- FreeBSD 11.0-RELEASE

此代理程式會負責 FreeBSD VM 與 Azure 網狀架構之間作業的通訊，例如在第一次使用 VM 時佈建 VM (使用者名稱、密碼或 SSH 金鑰、主機名稱等)，以及啟用選擇性 VM 延伸模組的功能。

至於未來的 FreeBSD 版本，策略是維持最新狀態，在 FreeBSD 版本工程小組發佈最新版本後不久，便立即提供最新版本。

## <a name="deploying-a-freebsd-virtual-machine"></a>部署 FreeBSD 虛擬機器
使用來自 Azure Marketplace 的映像來部署 FreeBSD 虛擬機器相當簡單：

- [Azure Marketplace 上的 FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [Azure Marketplace 上的 FreeBSD 11.0](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)

## <a name="vm-extensions-for-freebsd"></a>適用於 FreeBSD 的 VM 擴充功能
以下為 FreeBSD VM 中支援的 VM 擴充功能。

### <a name="vmaccess"></a>VMAccess
[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) 擴充功能可以：

* 重設原始 sudo 使用者的密碼。
* 建立新的 sudo 使用者並指定密碼。
* 使用指定的金鑰來設定公開主機金鑰。
* 重設 VM 佈建期間所提供的公開主機金鑰 (如果未提供主機金鑰)。
* 開啟 SSH 連接埠 (22)，而且如果已將 reset_ssh 設定為 true，則還原 sshd_config。
* 移除現有的使用者。
* 檢查磁碟。
* 修復新增的磁碟。

### <a name="customscript"></a>CustomScript
[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) 擴充功能可以：

* 如果提供，可從 Azure 儲存體或外部公用儲存體 (例如 GitHub) 下載自訂的指令碼。
* 執行進入點指令碼。
* 支援內嵌命令。
* 自動轉換 Shell 和 Python 指令碼中的 Windows 樣式新行字元。
* 自動移除 Shell 和 Python 指令碼中的 BOM。
* 保護 CommandToExecute 中的機密資料。

[!NOTE]FreeBSD VM 目前僅支援 CustomScript 1.x 版。  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>驗證：使用者名稱、密碼和 SSH 金鑰
使用 Azure 入口網站來建立 FreeBSD 虛擬機器時，您必須提供使用者名稱、密碼或 SSH 公開金鑰。
在 Azure 上部署 FreeBSD 虛擬機器時所使用的使用者名稱，不可以和虛擬機器中已存在的系統帳戶 (UID <&100;) 名稱相同 (例如 "root")。
目前只支援 RSA SSH 金鑰。 多行 SSH 金鑰的開頭必須為 `---- BEGIN SSH2 PUBLIC KEY ----`，結尾為 `---- END SSH2 PUBLIC KEY ----`。

## <a name="obtaining-superuser-privileges"></a>取得超級使用者權限
在 Azure 上部署虛擬機器執行個體時所指定的使用者帳戶是特殊權限帳戶。 sudo 的封裝已安裝於所發佈的 FreeBSD 映像中。
當您使用此使用者帳戶登入之後，您便能以 root 身分，使用命令語法來執行命令。

    $ sudo <COMMAND>

您可以視需要使用 `sudo -s` 來取得 root shell。

## <a name="known-issues"></a>已知問題
1. 目前 Hyper-V (和 Azure) 上的 FreeBSD 11.0 有一個未解決的問題，此問題會導致在使用 `freebsd-update` 修補作業系統的情況下 VM 發生失敗。 [建議的修補程式](https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=212721)包含在 Azure Marketplace 上的 FreeBSD 映像中。 不過，FreeBSD 小組尚未將它與上游合併，因此執行 `freebsd-update` 會將核心取代成未修補的核心。 建議 Azure 上的使用者在修正程式發佈為 ERRATA 之前，不要安裝 FreeBSD 11.0 的修補程式。

2. [Azure VM 客體代理程式](https://github.com/Azure/WALinuxAgent/) 2.2.2 版有一個[已知問題] (https://github.com/Azure/WALinuxAgent/pull/517)，此問題會導致 Azure 上的 FreeBSD VM 佈建失敗。 建議 Azure 上的 FreeBSD VM 使用者使用 2.2.1 或更舊的版本。 [Azure VM 客體代理程式](https://github.com/Azure/WALinuxAgent/) 2.2.3 版將會包含這項修正。 

## <a name="next-steps"></a>後續步驟
* 前往 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) 以建立 FreeBSD VM。
* 如果您想要將自己的 FreeBSD 攜至 Azure，請參閱[建立並上傳 FreeBSD VHD 到 Azure](./virtual-machines-linux-classic-freebsd-create-upload-vhd.md)。



<!--HONumber=Jan17_HO2-->


