---
title: "Azure 資料目錄支援的資料來源 | Microsoft Docs"
description: "目前支援的資料來源的規格。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstrauss
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 09/15/2016
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c357d477684444342c74e04a2c5545a76b9ee0e3


---
# <a name="azure-data-catalog-supported-data-sources"></a>Azure 資料目錄支援的資料來源
Azure 資料目錄的使用者可以使用公用 API、Click Once 註冊工具，或手動將資訊直接輸入至資料目錄 Web 入口網站，發佈中繼資料。 下列方格摘要說明目前目錄支援的所有來源，以及針對每個來源的發佈功能。  也列出每個來源可以從我們的入口網站「開啟」經驗啟動的外部資料工具。 文章中的第二個方格會提供每個資料來源連接屬性的更詳細技術規格。

## <a name="list-of-supported-data-sources"></a>支援的資料來源的清單
<table>

    <tr>
       <td><b>資料來源物件</b></td>
       <td><b>API</b></td>
       <td><b>手動輸入</b></td>
       <td><b>註冊工具</b></td>
       <td><b>開啟工具</b></td>
       <td><b>注意事項</b></td>
    </tr>

    <tr>
      <td>Azure 資料湖存放區目錄</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 資料湖存放區檔案</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 儲存體 Blob</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 儲存體目錄</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 儲存體資料表</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>HDFS 目錄</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HDFS 檔案</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive 資料表</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive 檢視</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL 資料表</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL 檢視</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle 資料庫資料表</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle 資料庫檢視</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>其他 (一般資產)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL 資料倉儲資料表</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL 資料倉儲檢視</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 維度</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 量值</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 資料表</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services 報表</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>[瀏覽器]</font></td>
      <td><font size=2>僅限原生模式伺服器。 不支援 SharePoint 模式。</font></td>
    </tr>

    <tr>
      <td>SQL Server 資料表</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server 檢視</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata 資料表</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata 檢視</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana 檢視</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2>僅限計算檢視和分析檢視；不支援屬性檢視。</font></td>
    </tr>

    <tr>
      <td>Db2 資料表</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Db2 檢視</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>檔案系統檔案</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp 目錄</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp 檔案</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 報告</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 結束點</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 檔案</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata 實體集</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata 函式</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql 資料表</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql 檢視</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana 檢視</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Salesforce 物件</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SharePoint 清單 </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

如果您需要其他來源的支援，請使用 [Azure 資料目錄論壇](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)提交功能要求。

<br>
<br>

## <a name="data-source-reference-specification"></a>資料來源參考規格
> [!NOTE]
> 下表中的「DSL 結構」資料行只會列出 Azure 資料目錄所使用之「位址」屬性包的連接屬性 (也就是「位址」屬性包可以包含 Azure 資料目錄持續保留之資料來源的其他連接屬性，但不會加以使用)。
> 
> <table>
> <tr>
> <td><b>來源類型</b></td>
> <td><b>資產類型</b></td>
> <td><b>物件類型</b></td>
> <td><b>DSL 結構<b></td>
> </tr>
> <tr>
> <td>Azure Data Lake Store</td>
> <td>容器</td>
> <td>資料湖</td>
> <td>
> <font size=2>通訊協定：webhdfs
> <br>驗證：{基本、oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Azure Data Lake Store</td>
> <td>資料表</td>
> <td>目錄、檔案</td>
> <td>
> <font size=2>通訊協定：webhdfs
> <br>驗證：{基本、oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Azure 儲存體</td>
> <td>容器</td>
> <td>容器</td>
> <td>
> <font size=2> 通訊協定︰azure-blob
> <br>驗證：{azure-access-key}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帳戶
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 容器 </font>
> </td>
> </tr>
> <tr>
> <td>Azure 儲存體</td>
> <td>資料表</td>
> <td>blob、目錄</td>
> <td>
> <font size=2> 通訊協定︰azure-blob
> <br>驗證：{azure-access-key}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帳戶
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 容器
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name </font>
> </td>
> </tr>
> <tr>
> <td>Azure 儲存體</td>
> <td>容器</td>
> <td>容器</td>
> <td>
> <font size=2> 通訊協定：azure-tables
> <br>驗證：{azure-access-key}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帳戶 </font>
> </td>
> </tr>
> <tr>
> <td>Azure 儲存體</td>
> <td>資料表</td>
> <td>資料表</td>
> <td>
> <font size=2> 通訊協定：azure-tables
> <br>驗證：{azure-access-key}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帳戶
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name </font>
> </td>
> </tr>
> <tr>
> <td>Cosmos</td>
> <td>容器</td>
> <td>虛擬叢集</td>
> <td>
> <font size=2>通訊協定：cosmos
> <br>驗證：{基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Cosmos</td>
> <td>資料表</td>
> <td>串流、串流組、檢視</td>
> <td>
> <font size=2>通訊協定：cosmos
> <br>驗證：{基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>DataZen</td>
> <td>容器</td>
> <td>網站</td>
> <td>
> <font size=2>通訊協定：http
> <br>驗證：{無、基本、oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>DataZen</td>
> <td>報告</td>
> <td>報告、儀表板</td>
> <td>
> <font size=2>通訊協定：http
> <br>驗證：{無、基本、oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Db2</td>
> <td>容器</td>
> <td>資料庫</td>
> <td>
> <font size=2>通訊協定：db2
> <br>驗證：{基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
> </td>
> </tr>
> <tr>
> <td>Db2</td>
> <td>資料表</td>
> <td>資料表、檢視</td>
> <td>
> <font size=2>通訊協定：db2
> <br>驗證：{基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema </font>
> </td>
> </tr>
> <tr>
> <td>檔案系統</td>
> <td>資料表</td>
> <td>檔案</td>
> <td>
> <font size=2>通訊協定：檔案
> <br>驗證：{無、基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path </font>
> </td>
> </tr>
> <tr>
> <td>Ftp</td>
> <td>資料表</td>
> <td>目錄、檔案</td>
> <td>
> <font size=2>通訊協定：ftp
> <br>驗證：{無、基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Hadoop Distributed File System</td>
> <td>容器</td>
> <td>叢集</td>
> <td>
> <font size=2>通訊協定：webhdfs
> <br>驗證：{基本、oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Hadoop Distributed File System</td>
> <td>資料表</td>
> <td>目錄、檔案</td>
> <td>
> <font size=2>通訊協定：webhdfs
> <br>驗證：{基本、oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Hive</td>
> <td>容器</td>
> <td>資料庫</td>
> <td>
> <font size=2> 通訊協定︰hive
> <br>驗證：{hdinsight、基本、使用者名稱、無}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>connectionProperties︰
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
> </td>
> </tr>
> <tr>
> <td>Hive</td>
> <td>資料表</td>
> <td>資料表、檢視</td>
> <td>
> <font size=2> 通訊協定︰hive
> <br>驗證：{hdinsight、基本、使用者名稱、無}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
> <br>connectionProperties︰
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
> </td>
> </tr>
> <tr>
> <td>Http</td>
> <td>容器</td>
> <td>網站</td>
> <td>
> <font size=2>通訊協定：http
> <br>驗證：{無、基本、oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Http</td>
> <td>報告</td>
> <td>報告、儀表板</td>
> <td>
> <font size=2>通訊協定：http
> <br>驗證：{無、基本、oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Http</td>
> <td>資料表</td>
> <td>結束點、檔案</td>
> <td>
> <font size=2>通訊協定：http
> <br>驗證：{無、基本、oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>MySQL</td>
> <td>容器</td>
> <td>資料庫</td>
> <td>
> <font size=2>通訊協定：mysql
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
> </td>
> </tr>
> <tr>
> <td>MySQL</td>
> <td>資料表</td>
> <td>資料表、檢視</td>
> <td>
> <font size=2>通訊協定：mysql
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
> </td>
> </tr>
> <tr>
> <td>Odata</td>
> <td>容器</td>
> <td>實體容器</td>
> <td>
> <font size=2>通訊協定：odata
> <br>驗證：{無、基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Odata</td>
> <td>資料表</td>
> <td>實體集、函式</td>
> <td>
> <font size=2>通訊協定：odata
> <br>驗證：{無、基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; resource </font>
> </td>
> </tr>
> <tr>
> <td>Oracle 資料庫</td>
> <td>容器</td>
> <td>資料庫</td>
> <td>
> <font size=2>通訊協定：oracle
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
> </td>
> </tr>
> <tr>
> <td>Oracle 資料庫</td>
> <td>資料表</td>
> <td>資料表、檢視</td>
> <td>
> <font size=2>通訊協定：oracle
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
> </td>
> </tr>
> <tr>
> <td>Postgresql</td>
> <td>容器</td>
> <td>資料庫</td>
> <td>
> <font size=2>通訊協定：postgresql
> <br>驗證：{基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
> </td>
> </tr>
> <tr>
> <td>Postgresql</td>
> <td>資料表</td>
> <td>資料表、檢視</td>
> <td>
> <font size=2>通訊協定：postgresql
> <br>驗證：{基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
> </td>
> </tr>
> <tr>
> <td>Power BI</td>
> <td>容器</td>
> <td>網站</td>
> <td>
> <font size=2>通訊協定：http
> <br>驗證：{無、基本、oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Power BI</td>
> <td>報告</td>
> <td>報告、儀表板</td>
> <td>
> <font size=2>通訊協定：http
> <br>驗證：{無、基本、oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Power Query</td>
> <td>資料表</td>
> <td>交互式資料</td>
> <td>
> <font size=2> 通訊協定：power-query
> <br>驗證：{oauth}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>Salesforce</td>
> <td>資料表</td>
> <td>Object</td>
> <td>
> <font size=2>通訊協定：salesforce-com
> <br>驗證：{基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; class
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName </font>
> </td>
> </tr>
> <tr>
> <td>SAP Hana</td>
> <td>容器</td>
> <td>伺服器</td>
> <td>
> <font size=2>通訊協定：sap-hana-sql
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server </font>
> </td>
> </tr>
> <tr>
> <td>SAP Hana</td>
> <td>資料表</td>
> <td>檢視</td>
> <td>
> <font size=2>通訊協定：sap-hana-sql
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
> </td>
> </tr>
> <tr>
> <td>SharePoint</td>
> <td>資料表</td>
> <td>列出</td>
> <td>
> <font size=2>通訊協定：sharepoint-list
> <br>驗證：{基本、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
> </td>
> </tr>
> <tr>
> <td>SQL 資料倉儲</td>
> <td>命令</td>
> <td>預存程序</td>
> <td>
> <font size=2>通訊協定：tds
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
> </td>
> </tr>
> <tr>
> <td>SQL 資料倉儲</td>
> <td>TableValuedFunction</td>
> <td>資料表值函數</td>
> <td>
> <font size=2>通訊協定：tds
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
> </td>
> </tr>
> <tr>
> <td>SQL 資料倉儲</td>
> <td>容器</td>
> <td>資料庫</td>
> <td>
> <font size=2>通訊協定：tds
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
> </td>
> </tr>
> <tr>
> <td>SQL 資料倉儲</td>
> <td>資料表</td>
> <td>資料表、檢視</td>
> <td>
> <font size=2>通訊協定：tds
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server</td>
> <td>命令</td>
> <td>預存程序</td>
> <td>
> <font size=2>通訊協定：tds
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server</td>
> <td>TableValuedFunction</td>
> <td>資料表值函數</td>
> <td>
> <font size=2>通訊協定：tds
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server</td>
> <td>容器</td>
> <td>資料庫</td>
> <td>
> <font size=2>通訊協定：tds
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server</td>
> <td>資料表</td>
> <td>資料表、檢視</td>
> <td>
> <font size=2>通訊協定：tds
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Analysis Services 多維度的</td>
> <td>容器</td>
> <td>模型</td>
> <td>
> <font size=2>通訊協定：analysis-services
> <br>驗證：{windows、基本、無}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Analysis Services 多維度的</td>
> <td>KPI</td>
> <td>KPI</td>
> <td>
> <font size=2>通訊協定：analysis-services
> <br>驗證：{windows、基本、無}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Analysis Services 多維度的</td>
> <td>Measure</td>
> <td>Measure</td>
> <td>
> <font size=2>通訊協定：analysis-services
> <br>驗證：{windows、基本、無}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Analysis Services 多維度的</td>
> <td>資料表</td>
> <td>維度</td>
> <td>
> <font size=2>通訊協定：analysis-services
> <br>驗證：{windows、基本、無}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimension} </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Analysis Services 資料表</td>
> <td>容器</td>
> <td>模型</td>
> <td>
> <font size=2>通訊協定：analysis-services
> <br>驗證：{windows、基本、無}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Analysis Services 資料表</td>
> <td>KPI</td>
> <td>KPI</td>
> <td>
> <font size=2>通訊協定：analysis-services
> <br>驗證：{windows、基本、無}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Analysis Services 資料表</td>
> <td>Measure</td>
> <td>Measure</td>
> <td>
> <font size=2>通訊協定：analysis-services
> <br>驗證：{windows、基本、無}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Analysis Services 資料表</td>
> <td>資料表</td>
> <td>資料表</td>
> <td>
> <font size=2>通訊協定：analysis-services
> <br>驗證：{windows、基本、無}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Table} </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Reporting Services</td>
> <td>容器</td>
> <td>伺服器</td>
> <td>
> <font size=2>通訊協定：reporting-services
> <br>驗證：{windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Reporting Services</td>
> <td>報告</td>
> <td>報告</td>
> <td>
> <font size=2>通訊協定：reporting-services
> <br>驗證：{windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
> </td>
> </tr>
> <tr>
> <td>Teradata</td>
> <td>容器</td>
> <td>資料庫</td>
> <td>
> <font size=2>通訊協定：teradata
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
> </td>
> </tr>
> <tr>
> <td>Teradata</td>
> <td>資料表</td>
> <td>資料表、檢視</td>
> <td>
> <font size=2>通訊協定：teradata
> <br>驗證：{通訊協定、windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Master Data Services</td>
> <td>容器</td>
> <td>模型</td>
> <td>
> <font size="2">通訊協定︰mssql-mds
> <br>驗證：{windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version </font>
> </td>
> </tr>
> <tr>
> <td>SQL Server Master Data Services</td>
> <td>資料表</td>
> <td>實體</td>
> <td>
> <font size="2">通訊協定︰mssql-mds
> <br>驗證：{windows}
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; entity </font>
> </td>
> </tr>
> <tr>
> <td>其他 (非上述任何一項)</td>
> <td>\*</td>
> <td>\*</td>
> <td>
> <font size=2>通訊協定：generic-asset
> <br>位址：
> <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
> </td>
> </tr>
> </table> 




<!--HONumber=Nov16_HO3-->


