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
ms.date: 08/27/2016
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 0b6516003d93368896ddb7ecbf97cdd01cd75430


---
# <a name="introduction-to-freebsd-on-azure"></a>Azure 上的 FreeBSD 簡介
本主題提供在 Azure 中執行 FreeBSD 虛擬機器的概觀。

## <a name="overview"></a>Overview
適用於 Microsoft Azure 的 FreeBSD 是一套先進的電腦作業系統，可用來為新式伺服器、桌上型電腦及內嵌平台提供技術支援。 FreeBSD 10.3 映像是由 Microsoft Corporation 所提供，且可在 Azure 中使用。 它是以 FreeBSD 10.3 版本為根據，並安裝了 Azure VM 客體代理程式 [2.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4)。 此代理程式會負責 FreeBSD VM 與 Azure 網狀架構之間作業的通訊，例如第一次使用 VM 時佈建 VM (使用者名稱、密碼、主機名稱等)，以及啟用選擇性 VM 擴充功能的功能。
至於未來的 FreeBSD 版本，策略是維持最新狀態，在 FreeBSD 版本工程小組發行最新版本後不久，便立即提供最新版本。 即將推出的版本是 [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html)。

## <a name="deploying-a-freebsd-virtual-machine"></a>部署 FreeBSD 虛擬機器
使用來自 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)的映像來部署 FreeBSD 虛擬機器相當簡單。

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

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>驗證：使用者名稱、密碼和 SSH 金鑰
使用 Azure 入口網站來建立 FreeBSD 虛擬機器時，您必須提供使用者名稱、密碼或 SSH 公開金鑰。
在 Azure 上部署 FreeBSD 虛擬機器時所使用的使用者名稱，不可以和虛擬機器 (例如 "root") 中已存在的系統帳戶 (UID < 100) 名稱相同。
目前只支援 RSA SSH 金鑰。 多行 SSH 金鑰的開頭必須是 "---- BEGIN SSH2 PUBLIC KEY ----" 且結尾必須是 "---- END SSH2 PUBLIC KEY ----"。

## <a name="obtaining-superuser-privileges"></a>取得超級使用者權限
在 Azure 上部署虛擬機器執行個體時所指定的使用者帳戶是特殊權限帳戶。 sudo 的封裝已安裝於所發佈的 FreeBSD 映像中。
當您使用此使用者帳戶登入之後，您便能以 root 身分，使用命令語法來執行命令。

    # sudo <COMMAND>

您可以選擇使用 sudo -s 來取得 root shell。

## <a name="next-steps"></a>後續步驟
* 前往 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) 以建立 FreeBSD VM。
* 如果您想要將自己的 FreeBSD 攜至 Azure，請參閱[建立並上傳 FreeBSD VHD 到 Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。




<!--HONumber=Nov16_HO3-->


