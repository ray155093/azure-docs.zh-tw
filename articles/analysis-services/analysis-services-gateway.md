---
title: "內部部署資料閘道 | Microsoft Docs"
description: "如果 Azure 中的 Analysis Services 伺服器會連接到內部部署資料來源，則需要一個內部部署閘道。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 10/24/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 90584f60864589744888817ea71d0eb0d4d170ff


---
# <a name="on-premises-data-gateway"></a>內部部署資料閘道
內部部署資料閘道的角色如同橋接器，在內部部署資料來源和雲端中的 Azure Analysis Services 伺服器之間提供安全的資料傳輸。

閘道安裝於您網路的電腦上。 您必須為您的 Azure 訂用帳戶中的每部 Azure Analysis Services 伺服器各安裝一個閘道。 例如，如果您的 Azure 訂用帳戶中有兩部伺服器連接到內部部署資料來源，則閘道必須安裝在您網路中不同的兩部電腦上。

## <a name="requirements"></a>需求
**最低需求：**

* .NET 4.5 Framework
* 64 位元版本的 Windows 7 / Windows Server 2008 R2 (或更新版本)

**建議配備：**

* 8 核心 CPU
* 8 GB 記憶體
* 64 位元版本的 Windows 2012 R2 (或更新版本)

**重要考量︰**

* 閘道無法安裝在網域控制站上。
* 一部電腦只能安裝一個閘道。
* 請在電源維持開啟且不會進入睡眠狀態的電腦上安裝閘道。 如果電腦未開啟，Azure Analysis Services 伺服器就無法連線到內部部署資料來源以重新整理資料。
* 請勿將閘道安裝於採用無線網路的電腦上。 效能會因此降低。
* 若要變更已設定閘道的伺服器名稱，必須重新安裝並設定新的閘道。
* 在某些情況下，使用原生提供者 (例如 SQL Server Native Client (SQLNCLI11)) 連接到資料來源的表格式模型可能會傳回錯誤。 若要深入了解，請參閱[資料來源連接](analysis-services-datasource.md)。

## <a name="supported-on-premises-data-sources"></a>支援的內部部署資料來源
預覽版本中，閘道支援 Azure Analysis Services 伺服器與下列內部部署資料來源之間的連接︰

* SQL Server
* SQL 資料倉儲
* APS
* Oracle
* Teradata

## <a name="download"></a>下載
 [下載閘道](https://aka.ms/azureasgateway)

## <a name="install-and-configure"></a>安裝及設定
1. 執行安裝程式。
2. 選擇安裝位置，並接受授權條款。
3. 登入 Azure。
4. 指定 Azure Analysis Server 名稱。 每個閘道只能指定一部伺服器。 按一下 [設定] 即可開始使用。

    ![登入 Azure](./media/analysis-services-gateway/aas-gateway-configure-server.png)

## <a name="how-it-works"></a>運作方式
在您組織的網路電腦上，閘道會執行為一個 Windows 服務：**內部部署資料閘道**。 安裝用於 Azure Analysis Services 的閘道是根據用於其他服務 (例如 Power BI) 的相同閘道，但設定上有些差異。

![運作方式](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

查詢和資料流運作方式如下︰

1. 雲端服務使用內部部署資料來源的加密認證建立查詢。 查詢接著傳送至佇列供閘道處理。
2. 閘道雲端服務會分析該查詢，並將要求推送至 [Azure 服務匯流排](https://azure.microsoft.com/documentation/services/service-bus/)。
3. 內部部署資料閘道會輪詢 Azure 服務匯流排是否有待處理的要求。
4. 閘道收到查詢、解密認證，並使用這些認證連接至資料來源。
5. 閘道將查詢傳送至資料來源執行。
6. 結果會從資料來源傳送回閘道，然後再到雲端服務。

## <a name="windows-service-account"></a>Windows 服務帳戶
內部部署資料閘道設定為使用 *NT SERVICE\PBIEgwService* 做為 Windows 服務的登入認證。 根據預設，它具有「以服務方式登入」的權限 (在您安裝閘道所在機器的環境中)。 此認證不同於連接到內部部署資料來源使用的帳戶或您的 Azure 帳戶。  

如果您因為身分驗證面臨與 Proxy 服務器相關的問題，您可能需要將 Windows 服務帳戶變更為網域使用者或受管理的服務帳戶。

## <a name="ports"></a>連接埠
閘道會建立 Azure 服務匯流排的輸出連接。 閘道會與下列輸出連接埠進行通訊︰TCP 443 (預設)、5671、5672、9350 到 9354。  閘道不需要輸入連接埠。

建議您在防火牆中將您的資料區域 IP 位址加入白名單。 您可以下載 [Microsoft Azure Datacenter IP 清單](https://www.microsoft.com/download/details.aspx?id=41653)。 此清單每週更新。

> [!NOTE]
> Azure Datacenter IP 清單中列出的 IP 位址採用 CIDR 標記法。 例如，10.0.0.0/24 並非 10.0.0.0 到 10.0.0.24。 深入了解 [CIDR 標記法](http://whatismyipaddress.com/cidr)。
>
>

以下是閘道使用的完整網域名稱。

| 網域名稱 | 輸出連接埠 | 說明 |
| --- | --- | --- |
| *.powerbi.com |80 |用於下載安裝程式的 HTTP。 |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |進階訊息佇列通訊協定 (AMQP) |
| *.servicebus.windows.net |443、9350-9354 |透過 TCP 之服務匯流排轉送上的接聽程式 (需要 443 才能取得「存取控制」權杖) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |如果 Power BI 服務無法連接至閘道，則用來測試網際網路連線能力。 |
| *.microsoftonline-p.com |443 |用於驗證 (視設定而定)。 |

### <a name="forcing-https-communication-with-azure-service-bus"></a>強制使用 Azure 服務匯流排進行 HTTPS 通訊
您可以強制閘道使用 HTTPS 取代直接 TCP 來與 Azure 服務匯流排通訊；但這樣會大幅降低效能。 您需要修改 *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* 檔案。 將值從 `AutoDetect` 變更為 `Https`。 這個檔案預設位於 *C:\Program Files\On-premises data gateway*。

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>疑難排解
實際上，將 Azure Analysis Services 連接到內部部署資料來源所用的內部部署資料閘道，就是使用於 Power BI 的閘道。

如果您有閘道安裝和設定的問題，請務必參閱 [Power BI 閘道的疑難排解](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/)。 如果您認為您的防火牆有問題，請參閱防火牆或 Proxy 章節。

如果您認為閘道有 Proxy 相關問題，請參閱[設定 Power BI 閘道的 Proxy 設定](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md)。

## <a name="next-steps"></a>後續步驟
* [ Analysis Services](analysis-services-manage.md)
* [從 Azure Analysis Services 取得資料](analysis-services-connect.md)



<!--HONumber=Nov16_HO3-->


