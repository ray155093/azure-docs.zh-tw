---
title: 可用的連接器和 API 應用程式清單 | Microsoft Docs
description: 閱讀 Azure App Service 中的連接器和 API 應用程式相關資訊
services: logic-apps
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mandia

---
# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>要於 Logic Apps 中使用的連接器和 API Apps 清單
> [!NOTE]
> 這一版文章適用於邏輯應用程式 2014-12-01-preview 結構描述版本。 如需 Logic Apps 公開上市 (GA) 版本，請參閱 [新的連接器清單](../connectors/apis-list.md)。
> 
> 

深入了解所有由 Microsoft 建立可用於邏輯應用程式中的連接器和 API 應用程式。

如需定價資訊，以及每個服務層隨附項目的清單，請參閱 [Azure App Service 定價](https://azure.microsoft.com/pricing/details/app-service/)。

> [!NOTE]
> 若要在註冊 Azure 帳戶之前開始使用 Logic Apps，請移至 [試用邏輯應用程式](https://tryappservice.azure.com/?appservice=logic)。 您可以在 App Service 中立即建立短期的入門邏輯應用程式。 不需要信用卡；沒有承諾。
> 
> 

## <a name="core-connectors"></a>核心連接器
下表列出所有由 Microsoft 建立且可做為核心連接器使用的可用連接器和 API Apps：

| 名稱 | 說明 |
| --- | --- |
| [Bing 翻譯](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) |使用 Bing，將文字翻譯成其他語言。 |
| [HTTP](app-service-logic-connector-http.md) |HTTP 接聽程式會開啟當做 HTTP 伺服器的端點，並接聽內送的 HTTP 或 HTTPS 要求。 HTTP 動作不需要 API Apps，Logic Apps 內原本就支援。 |
| [Slack](app-service-logic-connector-slack.md) |連接至 Slack，並將訊息張貼至 Slack 通道。 |

## <a name="enterprise-integration-connectors"></a>企業整合連接器
下表列出所有由 Microsoft 建立且可做為企業整合連接器使用的可用連接器和 API Apps：

| 名稱 | 說明 |
| --- | --- |
| [BizTalk 規則](app-service-logic-use-biztalk-rules.md) |使用 BizTalk 規則來定義及控制組織內的商務邏輯。 您可以更新商務原則，毋須重新編譯或重新部署相關聯的應用程式。 |
| [BizTalk XPath 擷取程式](app-service-logic-xpath-extract.md) |根據您選擇的 XPath 來查詢和擷取 XML 內容中的資料。 |
| [DB2 連接器](app-service-logic-connector-db2.md) |連接到內部部署和執行 Windows 作業系統的 Azure 虛擬機器上的 IBM DB2 資料庫。 可將 Web API 和 OData API 作業對應至 Informix 結構化查詢語言命令。 <br/><br/>沒有觸發程序。 動作包含資料表選取、插入、更新、刪除和自訂陳述式<br/><br/>此連接器也包含 Microsoft Client for DRDA，可透過 TCP/IP 網路連接至 Informix 伺服器。 |
| [檔案](app-service-logic-connector-file.md) |使用此連接器，您可以連接到內部部署的檔案系統或網路，完成不同的檔案工作，包括上傳、刪除、列出檔案等等。 |
| [Informix](app-service-logic-connector-informix.md) |連接至內部部署和執行 Windows 作業系統的 Azure 虛擬機器上的 IBM Informix 資料庫。 可將 Web API 和 OData API 作業對應至 Informix 結構化查詢語言命令。<br/><br/>沒有觸發程序。 動作包含資料表選取、插入、更新、刪除和自訂陳述式。<br/><br/>使用內部部署時，可以使用 VPN 或 Azure ExpressRoute。 此連接器也包括 Microsoft Client for DRDA，用來跨 TCP/IP 網路連接至 Informix 伺服器。 |
| [Microsoft SQL Server](app-service-logic-connector-sql.md) |Connects to on-premises SQL Server or an Azure SQL Database. 您可以建立、更新、取得和刪除 SQL 資料庫資料表上的項目。 |
| MQ |連接至內部部署和執行 Windows 作業系統的 Azure 虛擬機器上的 IBM WebSphere MQ Server 第 8 版。 使用內部部署時，可以使用 VPN 或 Azure ExpressRoute。 連接器也包括 Microsoft Client for MQ。<br/><br/>沒有觸發程序。 沒有動作。<br/><br/>**注意**目前無法與 Logic Apps 搭配使用。 |

## <a name="connectors-as-triggers"></a>連接器做為觸發程序
有數個連接器提供 Logic Apps 的觸發程序。 這些觸發程序分成兩種類型：

1. 輪詢觸發程序：這些觸發程序會以指定的頻率輪詢您的服務，以檢查是否有新資料。 有新資料可用時，邏輯應用程式的新執行個體會以該資料做為輸入而執行。 為了防止重複取用相同的資料，觸發程序可能會清除已讀取並傳遞至邏輯應用程式的資料。 例如，檔案、SQL 和 Azure 儲存體都屬於此類連接器。
2. 推送觸發程序：這些觸發程序會接聽端點上的資料，或接聽發生的事件。 然後，觸發邏輯應用程式的新執行個體。 例如，HTTP 接聽程式和 Twitter 都屬於此類連接器。

## <a name="connectors-as-actions"></a>連接器做為動作
連接器也可做為邏輯應用程式內的動作。 動作很適合在邏輯應用程式中查閱可供執行時使用的資料。 例如，在處理訂單時，您可能需要查閱 SQL 資料庫中的資料，以取得客戶的其他資訊。 或者，您可能需要寫入、更新或刪除目的地的資料。 您可以使用連接器所提供的動作來這樣做。 動作對應至 API Apps 中的作業 (由其 Swagger 中繼資料所定義)。

## <a name="create-your-own-connectors-and-api-apps"></a>建立專屬的連接器和 API Apps
[連接器和 API Apps 參考](http://aka.ms/appservicesconnectorreference)  
[Azure App Service API 應用程式觸發程序](../app-service-api/app-service-api-dotnet-triggers.md)  
[邏輯應用程式參考資料](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>連接器和 API Apps 的詳細資訊
[什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)  
[在 Azure App Service 中使用混合連線管理員](app-service-logic-hybrid-connection-manager.md)  
[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)

<!--HONumber=Oct16_HO2-->


