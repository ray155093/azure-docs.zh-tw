---
title: "使用 PowerShell 管理 DNS 區域 | Microsoft Docs"
description: "您可以使用 Azure Powershell 管理 DNS 區域。 如何在 Azure DNS 上更新、刪除及建立 DNS 區域"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: f6403718f106b5021ea5b41e0e91a11bd92d4830

---
# <a name="how-to-manage-dns-zones-using-powershell"></a>如何管理使用 PowerShell 的 DNS 區域

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

本文將說明如何使用 PowerShell 管理 DNS 區域。 為了使用這些步驟，您必須安裝最新版的 Azure Resource Manager PowerShell Cmdlet (1.0 或更新版本)。 如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 。

## <a name="create-a-new-dns-zone"></a>建立新的 DNS 區域

若要建立 DNS 區域，請參閱 [使用 PowerShell 建立 DNS 區域](dns-getstarted-create-dnszone.md)。

## <a name="get-a-dns-zone"></a>取得 DNS 區域

若要擷取 DNS 區域，請使用 `Get-AzureRmDnsZone` Cmdlet。 此作業會傳回對應至 Azure DNS 中現有區域的 DNS 區域物件。 這個物件包含區域的相關資料 (例如記錄集的數目)，但不包含記錄集本身。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
```

## <a name="list-dns-zones"></a>列出 DNS 區域

您可以從 `Get-AzureRmDnsZone` 中省略區域名稱，以列舉資源群組中的所有區域： 此作業會傳回一系列的區域物件。

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

## <a name="update-a-dns-zone"></a>更新 DNS 區域

您可以使用 `Set-AzureRmDnsZone`變更 DNS 區域資源。 這不會更新區域內的任何 DNS 記錄集 (請參閱 [如何管理 DNS 記錄](dns-operations-recordsets.md))。 它只用來更新區域資源本身的屬性。 這在目前限於區域資源的 Azure 資源管理員「標記」。 如需詳細資訊，請參閱 [Etag 和標記](dns-getstarted-create-dnszone.md#tagetag) 。

請使用下列兩種方法之一來更新 DNS 區域：

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>使用區域名稱和資源群組來指定區域

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]
```

### <a name="specify-the-zone-using-a-zone-object"></a>使用 $zone 物件來指定區域

使用 `Get-AzureRmDnsZone`傳回的 $zone 物件來指定區域。 使用 `Set-AzureRmDnsZone` 搭配 $zone 物件時，將會使用 Etag 檢查，以確保不會覆寫並行變更。 您可以使用選擇性的 *-Overwrite* 參數來停用這些檢查。 如需詳細資訊，請參閱 [Etag 和標記](dns-getstarted-create-dnszone.md#tagetag) 。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
<..modify $zone.Tags here...>
Set-AzureRmDnsZone -Zone $zone [-Overwrite]
```

## <a name="delete-a-dns-zone"></a>刪除 DNS 區域

您可以使用 Remove-AzureRmDnsZone cmdlet 刪除 DNS 區域。

在 Azure DNS 中刪除 DNS 區域之前，您必須刪除所有記錄集，但建立區域時在區域的根自動建立的 NS 和 SOA 記錄除外。

請使用下列兩種方法之一來移除 DNS 區域：

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>使用區域名稱和資源群組名稱來指定區域

此作業具有選擇性的 `-Force` 參數，可隱藏要求您確認是否想要移除 DNS 區域的提示。

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-zone-using-a-zone-object"></a>使用 $zone 物件來指定區域

使用 `Get-AzureRmDnsZone`傳回的 $zone 物件來指定區域。 此作業具有選擇性的 `-Force` 參數，可隱藏要求您確認是否想要移除 DNS 區域的提示。 如同 `Set-AzureRmDnsZone`一樣，使用 $zone 物件指定區域可啟用 Etag 檢查，以確保不會刪除並行的變更。

選擇性的 `-Overwrite` 旗標可停用這些檢查。 如需詳細資訊，請參閱 [Etag 和標記](dns-getstarted-create-dnszone.md#tagetag) 。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]
```

區域物件也可以經由管道輸送，而不是當做參數傳遞：

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]
```

## <a name="next-steps"></a>後續步驟

建立 DNS 區域之後，請建立 [記錄集和記錄](dns-getstarted-create-recordset.md) ，以開始解析您的網際網路網域名稱。




<!--HONumber=Nov16_HO3-->


