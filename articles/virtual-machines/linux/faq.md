---
title: "Azure 中 Linux VM 的常見問題集 | Microsoft Docs"
description: "針對以 Resource Manager 模型建立的 Linux 虛擬機器，提供一些相關常見問題的解答。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 5a0092481cb461f26ba463f4c9bbaf114ecb1248
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# Linux 虛擬機器的常見問題
<a id="frequently-asked-question-about-linux-virtual-machines" class="xliff"></a>
本文可解決在 Azure 中使用 Resource Manager 部署模型建立之 Linux 虛擬機器的一些常見問題。 如需本主題的 Windows 版本，請參閱 [Windows 虛擬機器的常見問題](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## 我可以在 Azure VM 上執行什麼？
<a id="what-can-i-run-on-an-azure-vm" class="xliff"></a>
所有的訂閱者都可以在 Azure 虛擬機器上執行伺服器軟體。 如需詳細資訊，請參閱 [經 Azure 背書之配送映像上的 Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## 我可以使用多少的儲存體搭配虛擬機器？
<a id="how-much-storage-can-i-use-with-a-virtual-machine" class="xliff"></a>
每個資料磁碟最多可達 1 TB。 可使用的資料磁碟數量取決於虛擬機器的大小。 如需詳細資訊，請參閱 [虛擬機器的大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

Azure 儲存體帳戶提供作業系統磁碟和任何資料磁碟的儲存空間。 每個磁碟是以分頁 Blob 方式儲存的 .vhd 檔案。 如需定價的詳細資料，請參閱 [儲存體定價詳細資料](https://azure.microsoft.com/pricing/details/storage/)。

## 如何存取我的虛擬機器？
<a id="how-can-i-access-my-virtual-machine" class="xliff"></a>
使用安全殼層 (SSH) 建立遠端連線來登入虛擬機器。 請參閱如何[從 Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或[從 Linux 及 Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 連線的指示。 根據預設，SSH 允許最多 10 個並行連線。 您可以編輯組態檔以增加這個數字。

如果您遇到問題，請參閱 [疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## 我可以使用暫存磁碟 (/dev/sdb1) 儲存資料嗎？
<a id="can-i-use-the-temporary-disk-devsdb1-to-store-data" class="xliff"></a>
請勿使用暫存磁碟 (/dev/sdb1) 來儲存資料。 它只是用於暫時儲存。 您可能會遺失資料且無法復原。

## 我是否可以複製或再製現有的 Azure VM？
<a id="can-i-copy-or-clone-an-existing-azure-vm" class="xliff"></a>
是。 如需相關指示，請參閱 [如何在 Resource Manager 部署模型中建立 Linux 虛擬機器的複本](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## 為什麼我透過 Azure Resource Manager 沒看到加拿大中部和加拿大東部區域？
<a id="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager" class="xliff"></a>
針對現有 Azure 訂用帳戶所建立的虛擬機器，不會自動註冊加拿大中部和加拿大東部這兩個新的區域。 當虛擬機器透過 Azure 入口網站使用 Azure Resource Manager 部署到任何其他區域時，就會自動完成註冊。 將虛擬機器部署到任何其他 Azure 區域之後，新的區域即可供後續的虛擬機器使用。

## 我可以在建立 VM 之後將 NIC 新增至此 VM 嗎？
<a id="can-i-add-a-nic-to-my-vm-after-its-created" class="xliff"></a>
是的，目前可行。 您必須先停止解除配置 VM。 然後您可以新增或移除 NIC (除非它是 VM 上的最後一個 NIC)。 

## 是否有任何電腦名稱需求？
<a id="are-there-any-computer-name-requirements" class="xliff"></a>
是。 電腦名稱的長度最多可以有 64 個字元。 如需命名資源的詳細資訊，請參閱 [基礎結構命名指導方針](infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 。

## 是否有任何資源群組名稱需求？
<a id="are-there-any-resource-group-name-requirements" class="xliff"></a>
是。 資源群組名稱長度最多可以有 90 個字元。 請參閱[基礎結構資源群組指導方針](infrastructure-resource-groups-guidelines.md)，以取得有關資源群組的詳細資訊。

## 建立 VM 時的使用者名稱需求為何？
<a id="what-are-the-username-requirements-when-creating-a-vm" class="xliff"></a>
使用者名稱必須是長度在 1 - 64 之間的字元。

不允許下列使用者名稱︰

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
    </tr>
    <tr>
        <td style="text-align:center">john </td><td style="text-align:center"> owner </td><td style="text-align:center"> root </td><td style="text-align:center"> 伺服器</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> 支援 </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>


## 建立 VM 時的密碼需求為何？
<a id="what-are-the-password-requirements-when-creating-a-vm" class="xliff"></a>
密碼必須是長度在 6 - 72 之間的字元，且符合下列 4 個複雜性需求的其中 3 個：

* 包含小寫字元
* 包含大小字元
* 包含數字
* 包含特殊字元 (Regex match [\W_])

不允許下列密碼︰

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>

