---
title: "使用 Azure Cloud Shell (預覽) 視窗 | Microsoft Docs"
description: "逐步解說 Azure Cloud Shell 視窗。"
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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4e78dadb7fa036dcb4c5706fe774cbd85d57a746
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="using-the-shell-window"></a>使用殼層視窗
本文件說明如何使用 Azure Cloud Shell 視窗。

## <a name="concurrent-sessions"></a>並行工作階段
Cloud Shell 可透過允許每個工作階段以個別 Bash 處理序的形式存在，來允許跨瀏覽器索引標籤進行多個並行工作階段。
如果要結束工作階段，請務必從每個工作階段視窗結束，因為每個處理序雖然是在相同的電腦上執行，但卻是獨立執行的。

## <a name="restart-cloud-shell"></a>重新啟動 Cloud Shell
![](media/recycle.png)
* 按一下工具列上的 [重新啟動] 圖示，即可重設 Cloud Shell 電腦。

> ![警告] 重新啟動 Cloud Shell 會重設電腦狀態，而所有未由 clouddrive 保存的檔案都將遺失。

## <a name="minimize--maximize-cloud-shell-window"></a>將 Cloud Shell 視窗最大化和最小化
![](media/minmax.png)
* 按一下視窗右上方的 [最小化] 圖示，即可將視窗隱藏。 再按一下 [Cloud Shell] 圖示，即可取消隱藏。
* 按一下 [最大化] 圖示，即可將視窗設成最大高度。 若要將視窗還原到先前的大小，請按一下 [還原]。

## <a name="copy-and-paste"></a>複製和貼上
* Windows：`Ctrl-insert` 和 `Shift-insert` 或在下拉式清單上按一下，即可複製/貼上
  * FireFox/IE 可能無法正確支援剪貼簿權限
* Mac OS：`Cmd-c` 和 `Cmd-v` 或在下拉式清單上按一下，即可複製/貼上

## <a name="resize-cloud-shell-window"></a>調整 Cloud Shell 視窗大小
* 按一下工具列上邊緣並向上或向下拖曳，即可調整 Cloud Shell 視窗大小。

## <a name="scrolling-text-display"></a>捲動文字顯示
* 使用您的滑鼠或觸控板來捲動

## <a name="exit-command"></a>結束命令
執行 `exit` 可終止作用中的工作階段。 此行為預設會在 10 分鐘後發生，不須進行互動。

## <a name="next-steps"></a>後續步驟
[Cloud Shell 快速入門](quickstart.md)  
