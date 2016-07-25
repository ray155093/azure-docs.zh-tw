<properties
   pageTitle="Red Hat 更新基礎結構 (RHUI) | Microsoft Azure"
   description="了解適用於 Microsoft Azure 中隨選 Red Hat Enterprise Linux 執行個體的 Red Hat 更新基礎結構 (RHUI)"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/11/2016"
   ms.author="borisb"/>

# 適用於 Azure 中隨選 Red Hat Enterprise Linux VM 的 Red Hat 更新基礎結構 (RHUI)

需註冊從隨選 Red Hat Enterprise Linux (RHEL) 映像建立的虛擬機器，以存取部署在 Azure 中的 Red Hat 更新基礎結構 (RHUI)；前述映像可在 Azure Marketplace 中找到。隨選 RHEL 執行個體將有權存取區域 yum 儲存機制，並能夠收到遞增更新。

RHUI 所管理的 yum 儲存機至清單，會於佈建期間設定在您的 RHEL 執行個體中。您不需要進行任何額外設定，只需在 RHEL 執行個體開始執行之後執行 `yum update` 即可取得最新的更新。

## RHUI 概觀
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) 提供調整性高的解決方案，可以針對裝載在 Red Hat 認證雲端提供者上的 Red Hat Enterprise Linux 雲端執行個體，管理 yum 儲存機制內容。根據上游 Pulp 專案，RHUI 可讓雲端提供者在本機建立 Red Hat 所裝載儲存機制內容的鏡像、以其內容建立自定儲存機制，並透過具流量負載平衡的內容傳遞系統，讓儲存機制可供大量使用者使用。

## 可以使用 RHUI 的區域
在所有可使用 RHEL 隨選映像的地區，皆可使用 RHUI。目前包含 [Azure 狀態儀表板](https://azure.microsoft.com/status/)頁面中列出的所有公用區域。從 RHEL 隨選映像佈建之 VM 的 RHUI 存取包含在其價格中。地區/國家的雲端可用性將會更新，因為我們將在未來擴大 RHEL 隨選的可用性。

> [AZURE.NOTE] 僅 [Microsoft Azure Datacenter IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)中的 VM 可存取 Azure 代管的 RHUI。

## 從其他更新儲存機制取得更新

如果您需要從不同的更新儲存機制 (而不是 Azure 代管的 RHUI) 取得更新，您必須在 RHUI 上取消註冊您的執行個體，並以想要的更新基礎結構 (如 Red Hat Satellite 或 Red Hat 客戶入口網站 CDN) 重新註冊它們。您將需要這些服務和註冊的適當 Red Hat 訂用帳戶，以利[在 Azure 中的 Red Hat 雲端存取](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure)。

若要取消註冊 RHUI 並向您的更新基礎結構註冊，請遵循下列步驟。

1.	編輯 /etc/yum.repos.d/rh-cloud.repo，將所有 `enabled=1` 變更為 `enabled=0`。例如：

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	編輯 /etc/yum/pluginconf.d/rhnplugin.conf，將 `enabled=0` 變更為 `enabled=1`。
3.	然後，註冊所需的基礎結構，例如 Red Hat 客戶入口網站。請遵循 Red Hat 解決方案指南的[如何向 Red Hat 客戶入口網站註冊及訂閱系統](https://access.redhat.com/solutions/253273)。

> [AZURE.NOTE] 對 Azure 代管之 RHUI 的存取，包含在 RHEL 隨用隨付 (PAYG) 映像的價格中。從 Azure 代管的 RHUI 取消註冊 PAYG RHEL VM 並不會將虛擬機器轉換成自備授權 (BYOL) 類型的虛擬機器，因此，如果以另一個更新來源註冊相同的 VM，可能會產生雙重費用。
> 
> 如果您一直需要使用非 Azure 代管 RHUI 的更新基礎結構，請考慮建立及部署您自己的 (BYOL 類型) 映像，如[建立及上傳適用於 Azure 的 Red Hat 型虛擬機器](virtual-machines-linux-redhat-create-upload-vhd.md)一文所述。

## 後續步驟
若要從 Azure Marketplace 隨用隨付映像建立 Red Hat Enterprise Linux VM，並利用 Azure 代管 RHUI，請移至 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/)。您將能夠在您的 RHEL 執行個體中使用 `yum update`，不需要任何額外的安裝。

<!---HONumber=AcomDC_0713_2016-->