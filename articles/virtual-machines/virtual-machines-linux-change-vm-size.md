<properties
   pageTitle="如何重新調整 Linux VM 的大小 | Microsoft Azure"
   description="如何藉由變更 VM 的大小來相應增加或相應減少 Linux 虛擬機器。"
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# 如何重新調整 Linux VM 的大小

## 概觀 

部屬虛擬機器 (VM) 之後，您可以藉由變更 [VM 大小][vm-sizes]來相應增加或減少 VM。在某些情況下，您必須先解除配置 VM。如果新的大小無法在裝載 VM 的硬體叢集上取得，可能有這樣的情況。

本文說明如何使用 [Azure CLI][azure-cli] 重新調整 Linux VM 的大小。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。


## 重新調整 Linux VM 的大小 

若要重新調整 VM 的大小，請執行下列步驟。

1. 執行下列 CLI 命令。這個命令會列出裝載 VM 的硬體叢集上的可用 VM 大小。

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. 如果有列出所需的大小，請執行以下命令來調整 VM 的大小。

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    在此程序中 VM 會重新啟動。重新啟動之後，您現有的作業系統和資料磁碟將會重新對應。暫存磁碟的所有項目將會遺失。

    使用 `--enable-boot-diagnostics` 選項可讓[開機診斷][boot-diagnostics]記錄任何與啟動相關的錯誤。

3. 另一方面，如果沒有列出所需的大小，請執行以下命令以將 VM 解除配置、重新調整大小，然後再將它重新啟動。

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] 將 VM 解除配置也會釋出指派給該 VM 的任何動態 IP 位址。不會影響作業系統和資料磁碟。
   
## 後續步驟

如需提高延展性，可執行多個 VM 執行個體並相應放大。如需詳細資訊，請參閱[在虛擬機器調整集中自動調整 Linux 機器][scale-set]。

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/zh-TW/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md
[vm-sizes]: virtual-machines-linux-sizes.md

<!---HONumber=AcomDC_0518_2016-->