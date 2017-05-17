---
title: "Azure Cloud Shell (預覽) 功能 | Microsoft Docs"
description: "Azure Cloud Shell 的功能概觀"
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
ms.openlocfilehash: da78ec5c82a6ea46565a22fce49435d4b4d93a35
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="features--tools"></a>功能和工具
Azure Cloud Shell 是以瀏覽器為基礎的殼層經驗，用於管理和開發 Azure 資源。

Cloud Shell 提供可從瀏覽器存取、預先設定的殼層功能來管理 Azure 資源，您不需要另外自行安裝、版本控制和維護電腦。

Cloud Shell 依照要求而佈建電腦，因此，不會在工作階段之間保存電腦狀態。 由於 Cloud Shell 是針對互動式工作階段而建置，所以殼層會在殼層閒置 10 分鐘後自動終止。

## <a name="tools"></a>工具
|類別   |名稱   |
|---|---|
|Linux 殼層直譯器|Bash<br> sh               |
|Azure 工具            |Azure CLI 2.0 和 1.0     |
|文字編輯器           |vim<br> nano<br> emacs       |
|原始檔控制         |git                    |
|建置工具            |make<br> maven<br> npm<br> pip         |
|容器             |Docker<br> Kubectl<br> DC/OS CLI         |
|資料庫              |MySQL 用戶端<br> PostgreSql 用戶端<br> sqlcmd 公用程式      |
|其他                  |iPython 用戶端 |

## <a name="language-support"></a>語言支援
|語言   |版本   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Node.js    |6.9.4      |
|Python     |2.7 和 3.5|

## <a name="secure-automatic-authentication"></a>安全的自動驗證
Cloud Shell 會安全地自動驗證 Azure CLI 2.0 的帳戶存取。

## <a name="azure-files-persistence"></a>Azure 檔案持續性
因為 Cloud Shell 會依照要求而使用暫時電腦來配置，所以不會在工作階段之間保存 $Home 外面的本機檔案和電腦狀態。
若要在工作階段之間保存檔案，Cloud Shell 會在第一次啟動時逐步引導您連結 Azure 檔案共用。
完成後，Cloud Shell 會自動連結儲存體，供所有未來的工作階段使用。

[深入了解將 Azure 檔案共用連結至 Cloud Shell](persisting-shell-storage.md)。

## <a name="next-steps"></a>後續步驟
[Cloud Shell 快速入門](quickstart.md) 
[了解 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/)
