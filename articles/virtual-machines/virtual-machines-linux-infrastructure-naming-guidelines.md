---
title: "Azure 基礎結構命名指導方針 - Linux | Microsoft Docs"
description: "了解適合用來在 Azure 基礎結構服務中進行命名的關鍵設計和實作指導方針。"
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 18ee4fb1-8297-49a1-8d3f-097880be67c7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: b86bfcbd8b49dfcf383c48d57f237f2433bf20d6
ms.lasthandoff: 03/03/2017


---
# <a name="azure-infrastructure-naming-guidelines-for-linux-vms"></a>適用於 Linux VM 的 Azure 基礎結構命名指導方針 

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

本文著重於了解各種 Azure 資源的命名慣例做法，以在整個環境中建置一組具邏輯性且可輕鬆識別的資源集合。

## <a name="implementation-guidelines-for-naming-conventions"></a>命名慣例的實作指導方針
决策：

* 哪些是您的 Azure 資源命名慣例？

工作：

* 定義在資源間使用以維護一致性的詞綴。
* 定義必須是全域唯一的儲存體帳戶名稱。
* 記錄要使用並散發給所有相關合作對象的命名慣例，以確保部屬之間的一致性。

## <a name="naming-conventions"></a>命名慣例
在 Azure 中建立任何項目之前，應先建立良好的命名慣例。 命名慣例可確保所有資源都擁有可預測的名稱，有助於降低與這些資源管理相關聯的系統管理負擔。

您可以選擇遵循為整個組織所定義的一組特定命名慣例，或適用於特定 Azure 訂用帳戶或帳戶的命名慣例。 儘管對組織內的個人來說，在使用 Azure 資源時建立隱含規則非常容易，但對於在 Azure 一起工作的小組而言，您需要能夠彈性地調整。

預先針對命名慣例組合取得一致的意見。 有些關於命名慣例的考量會牽涉到規則組合。

## <a name="affixes"></a>詞綴
當您在定義命名慣例時，其中一個決定便是詞綴要位於：

* 名稱開頭 (前置)
* 名稱結尾 (後置)

例如，針對使用 `rg` 詞綴的資源群組，以下是兩個可能的名稱：

* Rg-WebApp (前置)
* WebApp-Rg (後置)

詞綴指的是可說明特定資源的各個層面。 下列表格顯示一些常用範例。

| 層面 | 範例 | 注意事項 |
|:--- |:--- |:--- |
| Environment |dev、stg、prod |取決於每個環境的用途與名稱。 |
| 位置 |usw (West US)、use (East US 2) |取決於資料中心的區域或組織的區域。 |
| Azure 元件、服務或產品 |Rg (適用於資源群組)、VNet (適用於虛擬網路) |取決於資源提供支援的產品。 |
| 角色 |db、app、web |取決於虛擬機器的角色。 |
| 執行個體 |01、02 和 03 等 |適用於有一個以上執行個體的資源。 例如，雲端服務中負載平衡的 Web 伺服器。 |

建立命名慣例時，請確定它們會清楚描述要針對每個資源類型使用哪些詞綴，以及使用的位置 (前置或後置)。

## <a name="dates"></a>日期
從資源名稱來判斷建立日期通常非常重要。 我們建議使用 YYYYMMDD 日期格式。 此格式可確保不僅會記錄完整的日期，而且這兩個名稱只有日期不同的資源會以依字母順序和依時間先後順序的方式來排序。

## <a name="naming-resources"></a>命名資源
在命名慣例中定義每個資源類型，慣例中應包含規則來定義為每個所建立資源指派名稱的方式。 這些規則應套用到所有資源類型，例如：

* 訂用帳戶
* 帳戶
* 儲存體帳戶
* 虛擬網路
* 子網路
* 可用性設定組
* 資源群組
* 虛擬機器
* 端點
* 網路安全性群組
* 角色

若要確保名稱可提供足夠的資訊來判斷其指稱的是哪一個資源，您應使用描述性名稱。

## <a name="computer-names"></a>電腦名稱
當您建立虛擬機器 (VM) 時，Azure 會要求一個由最多 64 個字元組成的 VM 名稱，這會用來做為資源名稱。 Azure 會針對安裝在 VM 中的作業系統使用相同的名稱。 但是，這些名稱不一定相同。

若 VM 是從已經包含作業系統的 .vhd 映像檔所建立，Azure 中的 VM 名稱可能會與 VM 的作業系統電腦名稱不同。 這種情況可能會增加 VM 管理的困難度，因此我們不建議使用。 將 Azure VM 資源的名稱指派為該 VM 作業系統上所指派的相同電腦名稱。

我們建議讓 Azure VM 名稱與基礎作業系統電腦名稱相同。

## <a name="storage-account-names"></a>儲存體帳戶名稱
儲存體帳戶具備負責管理其名稱的特殊規則。 您只能使用小寫字母和數字。 如需詳細資訊，請參閱[建立儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)。 此外，儲存體帳戶名稱 (含 core.windows.net) 應是全域有效的唯一 DNS 名稱。 例如，如果儲存體帳戶名稱為 mystorageaccount，則以下產生的 DNS 名稱應該是唯一的：

* mystorageaccount.blob.core.windows.net
* mystorageaccount.table.core.windows.net
* mystorageaccount.queue.core.windows.net

## <a name="next-steps"></a>後續步驟
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


