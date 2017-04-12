---
title: "Azure 中 VM 重新啟動或調整大小的問題 | Microsoft 文件"
description: "針對在 Azure 中重新啟動或調整現有 Linux 虛擬機器的 Resource Manager 部署問題進行疑難排解"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 51f1610c-0290-464a-97d4-c2e485c7ae7f
ms.service: virtual-machines-linux
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.workload: required
ms.date: 01/10/2017
ms.author: delhan
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e49322dbccf4ec1157bc7e3a109175869b53518d
ms.lasthandoff: 04/03/2017


---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-linux-vm-in-azure"></a>針對在 Azure 中重新啟動或調整現有 Linux VM 大小的部署問題進行疑難排解
當您嘗試啟動已停止的 Azure 虛擬機器 (VM)，或調整現有 Azure VM 的大小時，常會遇到的錯誤是配置失敗。 當叢集或區域沒有可用的資源或無法支援所要求的 VM 大小，就會產生此錯誤。

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>收集活動記錄
若要開始進行排解疑難，請收集活動記錄，以識別與問題相關的錯誤。 下列連結提供此程序的更多詳細資訊：

[檢視部署作業](../../azure-resource-manager/resource-manager-deployment-operations.md)

[檢視活動記錄以管理 Azure 資源](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>問題：啟動已停止的 VM 時發生錯誤
您嘗試啟動已停止的 VM，但是發現配置失敗。

### <a name="cause"></a>原因
必須在架設雲端服務的原始叢集上嘗試提出啟動已停止的 VM 要求。 不過，叢集沒有足夠空間可完成要求。

### <a name="resolution"></a>解決方案
* 停止可用性設定組中的所有 VM，然後重新啟動每一部 VM。
  
  1. 按一下 [資源群組] > [您的資源群組] > [資源] > [您的可用性設定組] > [虛擬機器] > [您的虛擬機器] > [停止]。
  2. 所有 VM 都停止後，選取每個已停止的 VM，然後按一下 [開始]。
* 稍後再重試重新啟動要求。

## <a name="issue-error-when-resizing-an-existing-vm"></a>問題：調整現有 VM 的大小時發生錯誤
您嘗試調整現有 VM 的大小，但是發現配置失敗。

### <a name="cause"></a>原因
必須在架設雲端服務的原始叢集上嘗試提出調整 VM 大小的要求。 不過，叢集不支援要求的 VM 大小。

### <a name="resolution"></a>解決方案
* 以較小的 VM 大小重試要求。
* 如果無法變更要求的 VM 的大小︰
  
  1. 停止可用性設定組中的所有 VM。
     
     * 按一下 [資源群組] > [您的資源群組] > [資源] > [您的可用性設定組] > [虛擬機器] > [您的虛擬機器] > [停止]。
  2. 所有 VM 都停止後，將所需 VM 調整為較大的大小。
  3. 選取已調整大小的 VM，按一下 [啟動] ，然後啟動每個已停止的 VM。

## <a name="next-steps"></a>後續步驟
如果您在 Azure 中建立新的 Linux VM 時遇到問題，請參閱[針對在 Azure 中建立新 Linux 虛擬機器的部署問題進行疑難排解](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。


