---
title: "啟用或停用 Azure VM 監視"
description: "說明如何啟用或停用 Azure VM 監視"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 6ce366d2-bd4c-4fef-a8f5-a3ae2374abcc
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ffc9a84a3a89b37f772826c612f4fa9abadc1a0e
ms.lasthandoff: 04/03/2017


---
# <a name="enable-or-disable-azure-vm-monitoring"></a>啟用或停用 Azure VM 監視
本節說明如何在 Azure 上執行的虛擬機器上啟用或停用監視。 如果從 [Azure 入口網站](https://portal.azure.com) 部署，而且預設以 1 分鐘的期間提供監視圖形，則預設會在 Azure 虛擬機器上啟用監視。 您可以使用入口網站或適用於 Mac、Linux 和 Windows (Azure CLI) 的 Azure 命令列介面啟用或停用監視。 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>透過 Azure 入口網站啟用/停用監視
您可以啟用 Azure VM 的監視，在 1 分鐘的期間提供您的執行個體相關資料。 (儲存體變更適用)。 接著，詳細的診斷資料可用於入口網站圖形中的 VM，或透過 API 取得。 根據預設，Azure 入口網站會啟用監視，但是您可以將它關閉，如下所述。 您可以在 VM 正在執行或處於停止狀態時啟用監視。

* 開啟 Azure 入口網站，位址是 **[https://portal.azure.com](https://portal.azure.com)**
* 在左側導覽中，按一下 [虛擬機器]。
* 在 [虛擬機器] 清單中，選取執行中或已停止的執行個體。 虛擬機器刀鋒視窗隨即開啟。
* 按一下 [所有設定]。
* 按一下 [診斷]。
* 將狀態變更為 [開啟] 或 [關閉]。 您也可以在此刀鋒視窗中挑選您要為虛擬機器啟用的監視層級詳細資料。

[Azure.Note] 當您建立新的虛擬機器時，[診斷開啟] 開關是預設值

![透過 Azure 入口網站啟用/停用監視。][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>使用 Azure CLI 啟用/停用監視
啟用 Azure VM 的監視。

* 建立名為 PrivateConfig.json 且包含下列內容的檔案。
        { "storageAccountName":"the storage account to receive data", "storageAccountKey":"the key of the account" }
* 執行下列 Azure CLI 命令。
  
        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] 若有提供，您可以從 2.0 版變更為較新的版本。 

如需有關設定監視度量和範例的更多詳細資料，請瀏覽文件 - **[使用 Linux 診斷擴充功能監視 Linux VM 的效能和診斷資料](classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

<!--Image references-->
[1]: ./media/vm-monitoring/portal-enable-disable.png



