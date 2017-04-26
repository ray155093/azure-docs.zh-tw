---
title: "Azure 執行個體中繼資料服務概觀 | Microsoft Docs"
description: "RESTful 介面，用以取得 VM 的計算、網路和近期維護事件的相關資訊。"
services: virtual-machines-windows, virtual-machines-linux,virtual-machines-scale-sets, cloud-services
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: 
ms.service: azure-instancemetadataservice
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: harijay
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: b5c0268e1a0ebfcb33781678a367090cad865907
ms.lasthandoff: 04/11/2017

---

# <a name="azures-instance-metadata-service---preview"></a>Azure 的執行個體中繼資料服務 -- 預覽

> [!NOTE] 
> 若您同意使用規定即可取得預覽。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款。] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>

執行個體中繼資料服務提供您的執行中虛擬機器執行個體相關資訊。 這項資訊可以用來在 Azure 上管理和設定您的執行個體。 Azure 的執行個體中繼資料服務是 RESTful 端點，可用於透過新的 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN)建立的所有 IaaS VM。 端點可以在已知的非可路由 IP 位址 (169.254.169.254) 取得，該位址只能從 VM 內存取。 此服務會提供關於虛擬機器執行個體、它的網路組態和近期維護事件的重要資料。 如需其他資訊，請參閱[中繼資料類別](#instance-metadata-data-categories)。

### <a name="important-information"></a>重要資訊
此服務目前是**預覽版**，並且會主控關於虛擬機器執行個體和近期維護事件的資訊。 服務會繼續接收更新，且此頁面會反映可用的特定[資料類別](#instance-metadata-data-categories)。


## <a name="service-availability"></a>服務可用性
目前預覽版本適用於 Azure 的**美國中西部**區域。 下表會在服務預覽的區域可用時更新。
若要嘗試執行個體中繼資料服務，請從 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) 或 [Azure 入口網站](http://portal.azure.com)建立 VM，並且遵循範例區段中的範例以存取中繼資料服務。 

執行個體中繼資料服務預覽可用的區域|
------------------------------------------------------------|
美國中西部 |



## <a name="retrieving-instance-metadata"></a>擷取執行個體中繼資料 

執行個體中繼資料適用於使用 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) 建立/管理的執行中 VM。 存取虛擬機器執行個體 (使用下列 URI) 的所有資料類別

```
 curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

所有執行個體中繼資料的預設輸出都是 json 格式 (內容類型 Application/json)

## <a name="usage-examples"></a>使用方式範例
以下是執行個體中繼資料服務的一組範例和使用方式語意

### <a name="versioning"></a>版本控制 
執行個體中繼資料服務已建立版本。 版本是必要項目，且目前預覽版本為 2017-03-01。


```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

當我們新增較新版本時，如果您的指令碼對於特定資料格式有相依性，則因為相容性，仍然可以存取較舊版本。 請注意，服務正式推出之後，就無法使用目前的預覽版本。


### <a name="data-output"></a>資料輸出
根據預設，執行個體中繼資料會傳回格式為 json 的資料 (內容類型 = application/json)。不同節點元素可以傳回可用之不同預設格式的資料，下表是資料格式的快速參考 

元素 | 預設資料格式 | 其他格式
--------|---------------------|--------------
/instance | json | 文字
/scheduledevents | json | 無

舉例來說，針對文字格式在要求 URL 中使用 format=text 

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01&format=text"
```
### <a name="security"></a>安全性
執行個體中繼資料端點只能從非可路由 169.254.169.254 IP 位址上的執行中虛擬機器執行個體內存取。 此外，具有 **X 轉送標頭**的任何要求會被中繼資料服務拒絕。 我們也需要要求包含傳送的 **Metadata:true** 標頭，以確認直接想要實際要求，而不是非預期重新導向的一部分。 
### <a name="error"></a>錯誤
如果找不到資料元素或要求的格式錯誤，則執行個體中繼資料服務會傳回標準 HTTP 錯誤，以下是一些傳回碼的範例 

HTTP 傳回碼 | 原因
----------------|-------
200 | OK
400 | 不正確的要求，遺漏標頭，傳遞 -H Metadata:true
404 | 找不到，要求的元素不存在 
405 | 不支援方法 
429 | 要求太多，目前我們只支援最多每秒 5 個查詢

### <a name="examples"></a>範例
#### <a name="retrieving-the-network-information"></a>擷取網路資訊 

**要求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-03-01"

```
**回應**
> [!NOTE] 
> 回應是 json 字串。 下列輸出會使用類似 [jq](https://stedolan.github.io/jq/) 的公用程式格式化 json。
>

```
{
  "interface": [
    {
      "ipv4": {
        "ipaddress": [
          {
            "ipaddress": "10.0.0.4",
            "publicip": "<>.<>.<>.<>"
          }
        ],
        "subnet": [
          {
            "address": "10.0.0.0",
            "dnsservers": [
              {
                "ipaddress": "10.0.0.2"
              },
              {
                "ipaddress": "10.0.0.3"
              }
            ],
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipaddress": []
      },
      "mac": "000D3A00FA89"
    }
  ]
}
```

#### <a name="retrieving-public-ip-address"></a>擷取公用 IP 位址

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipaddress/0/publicip?api-version=2017-03-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>擷取執行個體的所有中繼資料

**要求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```
**回應**
> [!NOTE]
> 回應是 json 字串。 下列輸出會使用類似 [jq](https://stedolan.github.io/jq/) 的公用程式格式化 json。
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipaddress": [
            {
              "ipaddress": "10.0.0.4",
              "publicip": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.0.0",
              "dnsservers": [
                {
                  "ipaddress": "10.0.0.2"
                },
                {
                  "ipaddress": "10.0.0.3"
                }
              ],
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipaddress": []
        },
        "mac": "000D3A00FA89"
      }
    ]
  }
}

```
#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>擷取 Windows 虛擬機器中的中繼資料

可以透過 Powershell 公用程式 curl 在 Windows 中擷取執行個體中繼資料。 從 Powershell 提示字元中執行下列命令： 

**要求**
```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-03-01 | select -ExpandProperty Content
```
**回應**
>[!NOTE]
> 回應是 json 字串。 下列輸出會以 ConvertTo-Json Powershell 公用程式格式化 json。
> 

```
{
    "compute":  {
                    "location":  "CentralUSEUAP",
                    "name":  "SQLTest",
                    "offer":  "SQL2016SP1-WS2016",
                    "osType":  "Windows",
                    "platformFaultDomain":  "0",
                    "platformUpdateDomain":  "0",
                    "publisher":  "MicrosoftSQLServer",
                    "sku":  "Enterprise",
                    "version":  "13.0.400110",
                    "vmId":  "453945c8-3923-4366-b2d3-ea4c80e9b70e",
                    "vmSize":  "Standard_DS2"
                },
    "network":  {
                    "interface":  [
                                      "@{ipv4=; ipv6=; mac=002248020E1E}"
                                  ]
                }
}
```


## <a name="instance-metadata-data-categories"></a>執行個體中繼資料資料類別
下表有可透過執行個體中繼資料取得之所有資料類別的清單

資料 | 說明
-----|------------
location | 執行 VM 的 Azure 區域 
名稱 | VM 的名稱 
提供項目 | 提供 VM 映像的資訊，這些值只會針對從 Azure 圖像藝廊部署的映像呈現 
publisher | VM 映像的發佈者
sku | VM 映像的特定 SKU  
version | VM 映像的版本 
osType | Linux 或 Windows 
platformUpdateDomain |  VM 執行所在的[更新網域](virtual-machines-windows-manage-availability.md)。 
platformFaultDomain | VM 執行所在的[容錯網域](virtual-machines-windows-manage-availability.md)。
vmId | VM 的唯一識別碼，詳細資訊請參閱[這裡](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
vmSize | [VM 大小](virtual-machines-windows-sizes.md)
ipv4/ipaddress | VM 的本機 IP 位址 
ipv4/publicip | 執行個體的公用 IP 位址 
subnet/address | 子網路位址 
subnet/dnsservers/ipaddress1 | 主要 DNS 伺服器
subnet/dnsservers/ipaddress2 | 次要 DNS 伺服器
subnet/prefix | 子網路首碼，範例 24
ipv6/ipaddress | VM 的 IPv6 位址
mac | VM mac 位址 
scheduledevents | 請參閱 [scheduledevents](virtual-machines-scheduled-events.md)



## <a name="example-scenarios-for-usage"></a>使用方式的範例案例  

### <a name="tracking-vm-running-on-azure"></a>追蹤在 Azure 上執行的 VM 

身為服務提供者，您可能需要追蹤執行軟體的 VM 數目，或者具有需要追蹤 VM 唯一性的代理程式。 若要能夠從執行個體中繼資料服務使用 vmId 欄位取得 VM 唯一識別碼 

**要求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-03-01&format=text"
```
**回應**
```
5c08b38e-4d57-4c23-ac45-aca61037f084

```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>容器的位置，資料分割基礎容錯/更新網域 

對於特定案例，放置不同複本的位置相當重要。 例如 [HDFS 複本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)，或透過 [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) 的容器放置，您需要知道 VM 執行所在的 platformFaultDomain 和 platformUpdateDomain。 您可以直接透過執行個體中繼資料查詢此資料點

**要求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-03-01&format=text" 
```
**回應**
```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>取得支援案例期間 VM 的相關詳細資訊 

身為服務提供者，您可能會收到支援呼叫，而您想要知道更多 VM 的相關資訊。 要求客戶共用計算中繼資料可以為支援專業人員提供基本資訊，以了解 Azure 上的 VM 種類。 

**要求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-03-01"
```
**回應**
> [!NOTE] 
> 回應是 json 字串。 下列輸出會使用類似 [jq](https://stedolan.github.io/jq/) 的公用程式格式化 json。
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }

}
```

## <a name="faq"></a>常見問題集
1. 我收到錯誤 400 不正確的要求，未指定必要的中繼資料標頭。 這代表什麼？
   * 執行個體中繼資料服務需要標頭 Metadata:true 在要求中傳遞。 在 REST 呼叫中傳遞標頭可以允許存取執行個體中繼資料服務。 
2. 為什麼我沒有收到我的 VM 計算資訊？
   * 目前執行個體中繼資料服務僅支援 Azure Resource Manager 建立的執行個體，我們可能會在未來新增雲端服務 VM 的支援。 
3. 我在一陣子之後回過頭來透過 Azure Resource Manager 建立我的虛擬機器。 為什麼我看不到計算中繼資料資訊？
   * 針對在 2016 年 9 月之後建立的 VM，新增[標記](../azure-resource-manager/resource-group-using-tags.md)才會開始看到計算中繼資料。 針對較舊的 VM (在 2016 年 9 月之前建立)，對 VM 新增/移除擴充功能或資料磁碟，以重新整理中繼資料。
4. 為什麼我會收到錯誤 500 - 內部伺服器錯誤？
   * 目前執行個體中繼資料預覽只能在美國西部中部區域使用。 在支援的區域部署您的 VM。  
4. 我要在哪裡共用其他問題/註解？
   * 請在 feedback.azure.com 上傳送您的註解。
    
## <a name="next-steps"></a>後續步驟

深入了解 [scheduledevents] (virtual-machines-scheduled-events.md)

