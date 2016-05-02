<properties
	pageTitle="App Service 環境的自訂設定"
	description="App Service 環境的自訂組態設定"
	services="app-service"
	documentationCenter=""
	authors="stefsch"
	manager="nirma"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="stefsch"/>

# App Service 環境的自訂組態設定

## 概觀 ##
因為 App Service 環境對單一客戶是隔離的，所以有某些專門套用到 App Service 環境的組態設定。本文記錄各種可供 App Service 環境使用的特定自訂。

您可以使用新 **clusterSettings** 屬性的陣列儲存 App Service 環境自訂。這個屬性是在 *hostingEnvironments* Azure Resource Manager 實體的「屬性」字典中找到的。

下列縮寫的 Resource Manager 範本程式碼片段顯示 **clusterSettings** 屬性︰


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

**clusterSettings** 屬性可以包含在 Resource Manager 範本中，以更新 App Service 環境。

## 使用 Azure 資源總管更新 App Service 環境
或者，您可以使用 [Azure 資源總管](https://resources.azure.com)更新 App Service 環境。

1. 在資源總管中，移至 App Service 環境的節點 ([訂用帳戶] > [resourceGroups] > [提供者] > [Micrososft.Web] > [hostingEnvironments])。接著，按一下您想要更新的特定 App Service 環境。

2. 按一下右窗格上方工具列中的 [讀取/寫入]，允許在資源總管中進行互動式編輯。

3. 按一下藍色的 [編輯] 按鈕，開放編輯 Resource Manager 範本。

4. 向下捲動到右窗格底部。**clusterSettings** 屬性位在最底部，您可以在此輸入或更新其值。

5. 輸入 (或複製並貼上) 您希望 **clusterSettings** 屬性出現的組態值陣列。

6. 按一下右窗格上方的綠色 [PUT] 按鈕，確認 App Service 環境的變更。

不過，送出變更後，約需 30 分鐘乘以 App Service 環境中前端數量的時間，變更才會生效。例如，如果 App Service 環境有四個前端，大約需要兩個小時才能完成組態更新。實行組態變更時，App Service 環境上就不會進行其他調整作業或組態變更作業。

## 停用 TLS 1.0 ##
客戶的常見問題是，尤其是處理 PCI 規範稽核的客戶，如何明確停用其應用程式的 TLS 1.0。

透過下列 **clusterSettings** 項目可以停用 TLS 1.0︰

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],



## 開始使用
Azure 快速入門 Resource Manager 範本網站包含具有[建立 App Service 環境](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)基本定義的範本。


<!-- LINKS -->

<!-- IMAGES -->

<!---HONumber=AcomDC_0420_2016-->