---
title: Red Hat Update Infrastructure (RHUI) | Microsoft Docs
description: "了解適用於 Microsoft Azure 中隨選 Red Hat Enterprise Linux 執行個體的 Red Hat Update Infrastructure (RHUI)"
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/01/2016
ms.author: borisb
translationtype: Human Translation
ms.sourcegitcommit: 7ad71094cafb3d401797e8a8b3dd48a9aeeded9e
ms.openlocfilehash: 34c4198c0b1b975caf2c198634bb18e0c4f2ab5b


---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>適用於 Azure 中隨選 Red Hat Enterprise Linux VM 的 Red Hat Update Infrastructure (RHUI)
需註冊從隨選 Red Hat Enterprise Linux (RHEL) 映像建立的虛擬機器，以存取部署在 Azure 中的 Red Hat Update Infrastructure (RHUI)；前述映像可在 Azure Marketplace 中找到。  隨選 RHEL 執行個體能夠存取區域 yum 儲存機制，並且能夠接收累加式更新。

RHUI 所管理的 yum 儲存機至清單，會於佈建期間設定在您的 RHEL 執行個體中。 您不需要進行任何額外設定，只需在 RHEL 執行個體準備好接收最新更新之後執行 `yum update` 即可。

> [!NOTE]
> Azure RHUI 基礎結構最近已更新 (2016 年 9 月)，您現有的 RHEL 執行個體組態必須變更，才能繼續存取 Azure RHUI。 如需詳細資料，請參閱＜RHUI Azure 基礎結構更新＞一節。
> 
> 

## <a name="rhui-azure-infrastructure-update"></a>RHUI Azure 基礎結構更新
自 2016 年 9 月起，Azure 會有一組新的 Red Hat Update Infrastructure (RHUI) 伺服器。 這些伺服器的部署將會透過 [Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)執行，讓任何 VM (不論是哪個區域) 都可以使用單一端點 (rhui-1.micrsoft.com)。 它們也會使用鏈結至已知「憑證授權單位」(Baltimore root) 的 SSL 憑證。 對於一些具有 RHUI 更新伺服器的 ACL 或自訂路由表的客戶來說，讓這項更新自動執行相當危險，因此這項更新可由客戶「選擇加入」。 本頁面提供上架到這些新伺服器的手動步驟，以及以自動化方式 (在驗證個別步驟時) 上架的完整指令碼。 Azure Marketplace 中的新 RHEL PAYG 映像 (日期為 2016 年 9 月的版本或更新版本) 會自動指向新的 Azure RHUI 伺服器，因此不需採取任何其他動作。

### <a name="the-new-azure-rhui-infrastructure-onboarding-timeline"></a>新 Azure RHUI 基礎結構上架時間表
| 日期 | 附註 |
| --- | --- |
| 2016 年 9 月 22 日 |提供 RHUI 伺服器和安裝指示。 使用新 RHEL PAYG Marketplace 映像 (日期為 2016 年 9 月) 部署的 VM 會自動使用新的 RHUI 伺服器，但現有的 VM 則是在「選擇加入」之後才會使用新伺服器 |
| 2016 年 11 月 1 日 |舊版 RHEL PAYG VM 映像 (使用舊的 Azure RHUI 伺服器) 將會自 Azure Marketplace 資源庫中移除 |
| 2017 年 1 月 16 日 |舊的 Azure RHUI 伺服器將被解除委任。 此時請更新所有受影響的 PAYG RHEL VM，以便繼續存取 Azure RHUI |

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>新的 RHUI 內容傳遞伺服器的 IP 為
```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>使用新 Azure RHUI 伺服器的手動更新程序
下載 (透過 curl) 公開金鑰簽章

```bash
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

驗證已下載的金鑰

```bash
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

檢查輸出，驗證 `keyid` 和 `user ID packet`：

```bash
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

安裝公開金鑰

```bash
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

下載、驗證及安裝用戶端 RPM

下載：適用於 RHEL 6

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

適用於 RHEL 7

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

驗證：

```bash
rpm -Kv azureclient.rpm
```

確認輸出中的套件簽章正確

```bash
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

安裝 RPM

```bash
sudo rpm -U azureclient.rpm
```

完成時，請確認您可以從 VM 存取 Azure RHUI

### <a name="all-in-one-script-for-automating-the-above-task"></a>可自動執行上述工作的全方位指令碼
請視需要使用下列指令碼，以自動執行將受影響的 VM 更新到新 Azure RHUI 伺服器的工作。

```sh
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>RHUI 概觀
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) 提供一個可靈活調整的解決方案，可以針對裝載在 Red Hat 認證雲端提供者上的 Red Hat Enterprise Linux 雲端執行個體，管理 yum 儲存機制內容。 根據上游 Pulp 專案，RHUI 可讓雲端提供者在本機建立 Red Hat 所裝載儲存機制內容的鏡像、以其內容建立自定儲存機制，並透過具流量負載平衡的內容傳遞系統，讓儲存機制可供大量使用者使用。

## <a name="regions-where-rhui-is-available"></a>可以使用 RHUI 的區域
在所有可使用 RHEL 隨選映像的地區，皆可使用 RHUI。 目前包含 [Azure 狀態儀表板](https://azure.microsoft.com/status/) 頁面上所列的所有公用區域 (Azure 美國政府和 Azure 德國區域)。 從 RHEL 隨選映像佈建之 VM 的 RHUI 存取包含在其價格中。 額外區域/國家的雲端可用性將會更新，因為我們將在未來擴大 RHEL 隨選的可用性。

> [!NOTE]
> 只有 [Microsoft Azure Datacenter IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)中的 VM 能夠存取 Azure 代管的 RHUI。
> 
> 

## <a name="get-updates-from-another-update-repository"></a>從其他更新儲存機制取得更新
如果您需要從不同的更新儲存機制 (而不是 Azure 代管的 RHUI) 取得更新，您必須在 RHUI 上取消註冊您的執行個體，並以想要的更新基礎結構 (如 Red Hat Satellite 或 Red Hat 客戶入口網站 CDN) 重新註冊它們。 您將需要這些服務的適當 Red Hat 訂用帳戶，而且必須註冊 [Azure 中的 Red Hat 雲端存取](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure)。

若要將 RHUI 取消註冊再向您的更新基礎結構重新註冊，請依照下列步驟。

1. 編輯 /etc/yum.repos.d/rh-cloud.repo，將所有 `enabled=1` 變更為 `enabled=0`。 例如：
   
   ```bash
   sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo
   ```
   
2. 編輯 /etc/yum/pluginconf.d/rhnplugin.conf，將 `enabled=0` 變更為 `enabled=1`。
3. 然後，註冊所需的基礎結構，例如 Red Hat 客戶入口網站。 請依照 Red Hat 解決方案指南中的 [如何向 Red Hat 客戶入口網站註冊及訂閱系統](https://access.redhat.com/solutions/253273)操作。

> [!NOTE]
> 對 Azure 代管之 RHUI 的存取，包含在 RHEL 隨用隨付 (PAYG) 映像的價格中。 從 Azure 代管的 RHUI 取消註冊 PAYG RHEL VM 並不會將虛擬機器轉換成自備授權 (BYOL) 類型的虛擬機器，因此，如果以另一個更新來源註冊相同的 VM，可能會產生雙重費用。 
> 
> 如果您持續需要使用非 Azure 代管 RHUI 的更新基礎結構，請考慮建立及部署您自己的 (BYOL 類型) 映像，如 [建立及上傳適用於 Azure 的 Red Hat 型虛擬機器](virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 一文所述。
> 
> 

## <a name="next-steps"></a>後續步驟
若要從 Azure Marketplace 隨用隨付映像建立 Red Hat Enterprise Linux VM 並利用 Azure 代管的 RHUI，請移至 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/)。 您將能夠在您的 RHEL 執行個體中使用 `yum update` ，而不需要任何額外的安裝。




<!--HONumber=Dec16_HO1-->


