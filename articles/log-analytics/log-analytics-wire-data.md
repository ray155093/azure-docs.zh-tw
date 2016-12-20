---
title: "Log Analytics 中的連線資料方案 | Microsoft Docs"
description: "網路資料是來自具有 OMS 代理程式 (包括 Operations Manager 和 Windows 連線的代理程式) 的電腦的網路和效能彙總資料。 網路資料結合記錄資料可協助您將資料相互關聯。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 15858f7b7436536e6bae7fcfd6a50c722d2d04a2
ms.openlocfilehash: be00cb9b1e8ba5d9d8368695ca8d448d466e8f47


---
# <a name="wire-data-solution-in-log-analytics"></a>Log Analytics 中的連線資料方案
網路資料是來自具有 OMS 代理程式 (包括 Operations Manager 和 Windows 連線的代理程式) 的電腦的網路和效能彙總資料。 網路資料結合記錄資料可協助您將資料相互關聯。 IT 基礎結構中安裝的 OMS 代理程式會監視往返於這些電腦傳送的網路資料 (屬於 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model) 中的網路層級 2-3)，包括使用的各種通訊協定和連接埠。

> [!NOTE]
> 目前無法將連線資料方案加入至工作區。 已啟用連線資料方案的客戶可以繼續使用連線資料方案。
>
>

根據預設，OMS 會從 Windows 內建的計數器收集記錄的資料，包括 CPU、記憶體、磁碟和網路效能資料。 針對每個代理程式，都是即時收集網路和其他資料，包括電腦使用的子網路和應用程式層級通訊協定。 您可以在 [設定] 頁面的 [記錄檔] 索引標籤上加入其他效能計數器。

如果您曾經使用 [sFlow](http://www.sflow.org/) 或其他軟體來搭配 [Cisco NetFlow 通訊協定](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html)，則會很熟悉從網路資料傳回的統計資料和資料。

幾種內建的記錄搜尋查詢包括︰

* 提供連線資料的代理程式
* 提供連線資料的代理程式的 IP 位址
* 依 IP 位址的輸出通訊
* 應用程式通訊協定傳送的位元組數目
* 應用程式服務傳送的位元組數目
* 不同通訊協定接收的位元組數目
* IP 傳送及接收的位元組總數
* 與 10.0.0.0/8 子網路上的代理程式通訊的 IP 位址
* 可靠測量的連線平均延遲
* 起始或接收網路流量的電腦處理程序
* 處理程序的網路流量

當您使用連線資料來搜尋時，您可以篩選和分組資料，以檢視最常用的前幾個代理程式和通訊協定的相關資訊。 或者，您可以調查某些電腦何時彼此通訊 (IP 位址/MAC 位址)、持續時間，以及已傳送的資料量 -- 基本上，就是在檢視以搜尋為基礎的網路流量的相關元資料。

不過，因為是檢視元資料，在深入的疑難排解中不見得實用。 OMS 中的連線資料不是完整擷取的網路資料。 因此，不適用於封包層級的深入疑難排解。
相較於其他收集方法，使用代理程式的優點是您不必安裝應用裝置、重新設定網路交換器，或執行複雜的設定。 連線資料是以代理程式為基礎 -- 在電腦上安裝代理程式，它就會監視自己的網路流量。 另一個優點是您想要監視雲端提供者、主機服務提供者或 Microsoft Azure 中執行的工作負載，而其中使用者未擁有網狀架構層級。

相反地，如果您未在網路基礎結構中的所有電腦上安裝代理程式，則無法完整看到網路上發生的情形。

## <a name="installing-and-configuring-the-solution"></a>安裝和設定方案
請使用下列資訊來安裝和設定方案。

* 連線資料方案會從執行 Windows Server 2012 R2、Windows 8.1 和更新版本作業系統的電腦取得資料。
* 您想要取得連線資料的來源電腦上需要有 Microsoft.NET Framework 4.0 或更新版本。
* 使用 [從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)所述的程序，將連線資料方案加入您的 OMS 工作區。  不需要進一步的組態。
* 如果您想要檢視特定方案的連線資料，必須先將此方案加入至您的 OMS 工作區。

## <a name="wire-data-data-collection-details"></a>「連線資料」資料收集詳細資訊
連線資料會使用您已啟用的代理程式，來收集有關網路流量的中繼資料。

下表顯示連線資料的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows (2012 R2/8.1 或更新版本) |![是](./media/log-analytics-wire-data/oms-bullet-green.png) |![是](./media/log-analytics-wire-data/oms-bullet-green.png) |![否](./media/log-analytics-wire-data/oms-bullet-red.png) |![否](./media/log-analytics-wire-data/oms-bullet-red.png) |![否](./media/log-analytics-wire-data/oms-bullet-red.png) |每隔 1 分鐘 |

## <a name="combining-wire-data-with-other-solution-data"></a>結合連線資料和其他方案資料
如上所示從內建查詢傳回的資料本身就可能很有趣。 不過，連線資料要結合來自其他 OMS 方案的資訊，才會彰顯其實用性。 比方說，您可以使用「安全性和稽核」方案所收集的安全性事件資料，再結合連線資料，以找出具名處理程序不尋常的網路登入嘗試。  在此範例中，您將在搜尋查詢中使用 IN 和 DISTINCT 運算子來聯結資料點。

需求︰若要使用下列範例，您需要安裝安全性和稽核方案。 不過，您可以使用來自其他方案的資料並結合連線資料，一樣可達到類似的結果。

### <a name="to-combine-wire-data-with-security-events"></a>結合連線資料和安全性事件
1. 在 [概觀] 頁面上，按一下 [連線資料]  圖格。
2. 在 [常用 WireData 查詢] 清單中，按一下 [依處理程序的網路流量 (位元組)]，查看傳回的處理程序清單。
    ![連線資料查詢](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. 如果處理程序清單太長而不易檢視，您可以將搜尋查詢修改成類似於︰

    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    下列範例顯示一個稱為 DancingPigs.exe 的處理程序，它看起來很可疑。
    ![連線資料搜尋結果](./media/log-analytics-wire-data/oms-wiredata-02.png)
4. 使用清單中傳回的資料，按一下具名的處理程序。 在此範例中，已按一下 DancingPigs.exe。 如下所示的結果描述各種通訊協定上的網路流量，例如輸出通訊。
    ![連線資料結果顯示有名稱的程序](./media/log-analytics-wire-data/oms-wiredata-03.png)
5. 因為已安裝安全性和稽核方案，您可以使用 IN 和 DISTINCT 搜尋查詢運算子來修改搜尋查詢，以探查具有相同 ProcessName 欄位值的安全性事件。 當連線資料和其他方案記錄檔的值有相同格式時，您就可以這樣做。 將搜尋查詢修改成類似於︰

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    

    ![wiredata results showing combined data](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. 在上述結果中，您會看到出現帳戶資訊。 現在，您可以使用類似下列的查詢，顯示安全性和稽核資料，以了解處理程序使用帳戶的頻率︰        

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```

    ![wiredata results showing account data](./media/log-analytics-wire-data/oms-wiredata-05.png)

## <a name="next-steps"></a>後續步驟
* [搜尋記錄檔](log-analytics-log-searches.md) 以檢視詳細的連線資料搜尋記錄。
* 請參閱 Dan 的 [Using Wire Data in Operations Management Suite Log Search (在 Operations Management Suite 記錄檔搜尋中使用連線資料)](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) 部落格文章，其中提供有關資料收集頻率及如何修改 Operations Manager 代理程式收集屬性的其他資訊。



<!--HONumber=Nov16_HO3-->


