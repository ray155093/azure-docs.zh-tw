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
因為 App Service 環境對單一客戶是隔離的，所以有某些專門套用到 App Service 環境的組態設定。本文記錄各種可用的 App Service 環境特定自訂。

App Service 環境自訂的儲存方法，是使用在 *hostingEnvironments* ARM 實體的 "Properties" 字典中找到的新屬性 "clusterSettings" 的陣列。

縮寫的 ARM 範本程式碼片段 (如下) 會顯示 "clusterSettings" 屬性︰


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

"clusterSettings" 屬性可以包含在 ARM 範本中，以更新 App Service 環境。

或者使用 [Azure 資源總管](https://resources.azure.com)更新屬性值。在 Azure 資源總管中，瀏覽至 App Service 環境的節點 (訂用帳戶 --> resourceGroups --> 提供者 --> Micrososft.Web --> hostingEnvironments)，然後按一下要更新的特定 App Service 環境。

在右邊的瀏覽器視窗中，按一下上方工具列中的 [讀取/寫入]，允許在資源總管中進行互動式編輯。然後按一下藍色的 [編輯] 按鈕，開放編輯 ARM 範本。將右邊的瀏覽器視窗捲動到底。"clusterSettings" 屬性位在最底部，您可以在此輸入或更新其值。

輸入 (或複製並貼上) 您希望 "clusterSettings" 屬性出現的組態值陣列。然後按一下右邊瀏覽器視窗上方的綠色 [PUT] 按鈕，確認 App Service 環境的變更。

不論更新 App Service 環境的方法為何，變更一經送出，約需 30 分鐘乘以 App Service 環境前端數量的時間，變更才會生效。例如，如果 App Service 環境有四個前端，大約需要兩個小時才能完成組態更新。實行組態變更時，App Service 環境上就不可能進行其他調整作業或組態變更作業。

## 停用 TLS 1.0 ##
客戶的週期性要求，尤其是客戶處理 PCI 規範稽核，就能夠明確停用其應用程式的 TLS 1.0。

下列 *clusterSettings* 項目可停用 TLS 1.0︰

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],



## 開始使用
Azure 快速入門 ARM 範本網站包含具有[建立 App Service 環境](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)基底定義的範本


<!-- LINKS -->

<!-- IMAGES -->
 

<!---HONumber=AcomDC_0413_2016-->