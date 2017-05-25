---
title: "在 Azure Cloud Shell (預覽) 中保存檔案 | Microsoft Docs"
description: "逐步解說 Azure Cloud Shell 如何保存檔案。"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 540cd10066e055e2dc132445b9adba5a4112d63a
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017

---

# <a name="persisting-files-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中保存檔案
在初始啟動時，Azure Cloud Shell 會要求您的訂用帳戶，以便為您建立 LRS 儲存體帳戶和 Azure 檔案共用。

![](media/storage-prompt.png)

### <a name="three-resources-will-be-created-on-your-behalf-in-a-supported-region-nearest-to-you"></a>在距離您最近的支援區域中，會為您建立三個資源：
1. 名稱如下的資源群組：`cloud-shell-storage-<region>`
2. 名稱如下的儲存體帳戶：`cs-uniqueGuid`
3. 名稱如下的檔案共用：`cs-<user>-<domain>-com-uniqueGuid`

此檔案共用會在您的 $Home 目錄下掛接為 `clouddrive`。 此檔案共用也會用來儲存為您建立的 5-GB 映像，以便自動更新並保存您的 $Home 目錄。 這是一次性的動作，後續的工作階段會自動掛接。

### <a name="cloud-shell-persists-files-with-both-methods-below"></a>Cloud Shell 會使用下列兩種方法來保存檔案：
1. 建立 $Home 目錄的磁碟映像，以保存 $Home 中的檔案。 此磁碟映像會在您指定的檔案共用中儲存為 `acc_<User>.img` (位於 `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`)

2. 在您的 $Home 目錄中，將指定的檔案共用掛接為 `clouddrive`，以便直接與檔案共用互動。 
`/Home/<User>/clouddrive` 對應至 `fileshare.storage.windows.net/fileshare`。
 
> [!Note]
> $Home 目錄中的所有檔案 (例如 SSH 金鑰) 會都保存於已掛接檔案共用中儲存的使用者磁碟映像中。 在 $Home 目錄和已掛接的檔案共用中保存資訊時，請套用最佳作法。

## <a name="using-clouddrive"></a>使用 clouddrive
Cloud Shell 可讓使用者執行名為 `clouddrive` 的命令，以手動更新掛接至 Cloud Shell 的檔案共用。
![](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>掛接新的 clouddrive

### <a name="pre-requisites-for-manual-mounting"></a>手動掛接的先決條件
Cloud Shell 在第一次啟動時會為您建立儲存體帳戶和檔案共用，不過您也可以使用 `clouddrive mount` 命令來更新檔案共用。

如果要掛接現有的檔案共用，儲存體帳戶必須為：
1. 可支援檔案共用的 LRS 或 GRS。
2. 位於您的指派區域。 上架時，您的指派區域會列在名為 `cloud-shell-storage-<region>` 的資源群組中。

### <a name="supported-storage-regions"></a>支援的儲存體區域
Azure 檔案服務必須位於與所掛接電腦的相同區域中。 Cloud Shell 電腦存在於下列區域：
|領域|區域|
|---|---|
|美洲|美國東部、美國中南部、美國西部|
|歐洲|北歐、西歐|
|亞太地區|印度中部、東南亞|

### <a name="mount-command"></a>掛接命令

> [!NOTE]
> 如果掛接新的檔案共用，將會為您的 $Home 目錄建立一個新的使用者映像，因為先前的 $Home 映像會保留在先前的檔案共用中。

1. 執行 `clouddrive mount` 搭配下列參數 <br>

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

若要查看詳細資料，請執行 `clouddrive mount -h`： <br>
![](media/mount-h.png)

## <a name="unmount-clouddrive"></a>卸載 clouddrive
您可以隨時將掛接至 Cloud Shell 的檔案共用卸載。 不過，Cloud Shell 需要有掛接的檔案共用。因此，移除檔案共用之後，在下一個工作階段會提示您建立及掛接新的檔案共用。

若要從 Cloud Shell 卸離檔案共用：
1. 執行 `clouddrive unmount`
2. 了解並確認提示

除非手動刪除，否則您的檔案共用將會繼續存在。 Cloud Shell 在後續的工作階段中將不再搜尋此檔案共用。

若要查看詳細資料，請執行 `clouddrive mount -h`： <br>
![](media/unmount-h.png)

> [!WARNING]
> 這個命令將不會刪除任何資源。 不過，手動刪除對應至 Cloud Shell 的資源群組、儲存體帳戶或檔案共用，將會清除您的 $Home 目錄磁碟映像及檔案共用中的任何檔案。 此動作無法復原。

## <a name="list-clouddrive"></a>列出 clouddrive
若要探索已掛接為 `clouddrive` 的檔案共用：
1. 執行 `df` 

clouddrive 檔案路徑會在 URL 中顯示您的儲存體帳戶名稱和檔案共用。

`//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

## <a name="transfer-local-files-to-cloud-shell"></a>將本機檔案傳輸至 Cloud Shell
`clouddrive` 目錄會同步處理至 Azure 入口網站儲存體刀鋒視窗。 使用此方法對檔案共用雙向傳輸本機檔案。 從 Cloud Shell 中更新的檔案，會在刀鋒視窗更新時反映在檔案儲存體 GUI 中。

### <a name="download-files"></a>下載檔案
![](media/download.gif)
1. 瀏覽至掛接的檔案共用
2. 在入口網站中選取目標檔案
3. 點擊 [下載]

### <a name="upload-files"></a>上傳檔案
![](media/upload.gif)
1. 瀏覽至掛接的檔案共用
2. 選取 [上傳]
3. 選取您想要上傳的檔案
4. 確認上傳

您現在應會看到 Cloud Shell 的 clouddrive 目錄中可存取的檔案。

## <a name="next-steps"></a>後續步驟
[Cloud Shell 快速入門](quickstart.md) 
[了解 Azure 檔案儲存體](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) 
[了解儲存體標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) 
