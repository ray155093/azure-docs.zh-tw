<properties
   pageTitle="調整 Web 服務 | Microsoft Azure"
   description="了解如何藉由增加並行要求和新增新端點的方式調整 Web 服務。"
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="azure 機器學習服務, web 服務, 作業化, 調整, 端點, 並行要求"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="07/06/2016"
   ms.author="neerajkh"/>

# 調整 Web 服務

>[AZURE.NOTE] 本主題描述適用於傳統 Web 服務的技巧。

根據預設，系統將每個發佈的 Web 服務設定為支援 20 個並行要求，而且最多可達 200 個並行要求。雖然 Azure 傳統入口網站提供設定此值的方法，但是 Azure Machine Learning 會自動最佳化此設定，為您的 Web 服務提供最佳的效能，而忽略入口網站的值。

如果您打算以超過最大同時呼叫數目值 200 可支援的負載來呼叫 API，則應該在相同的 Web 服務上建立多個端點，並將負載隨機分配給全體。

## 在相同 Web 服務新增新的端點

調整 Web 服務的規模大小是常見的工作，目的是支援 200 個以上的並行要求、透過多個端點提高可用性，或是為不同 Web 服務的取用者提供不同端點。您可以透過 [Azure 傳統入口網站](https://manage.windowsazure.com/)為同一個 Web 服務新增其他端點以增加規模，如下圖所示︰

請記住，如果您未以對應的叫用率叫用 API  
，就不適合使用非常高的並行處理計數。如果您將相對低的負載放在為高負載設定的 API，可能會看見延遲有零星的逾時及 (或) 突增情況。

通常在需要低度延遲的情況下，才會使用同步 API。這裡所說的延遲意味著 API 完成一個要求所花費的時間，而未計入任何網路延遲的時間。假設您有一個會延遲 50 毫秒的 API。其節流層級為 [高] 且 [最大同時呼叫數目] 為 20，您必須每秒呼叫這個 API 20 * 1000 / 50 = 400 次，才能完全耗盡可用的容量。再進一步延伸，假設會延遲 50 毫秒，而最大同時呼叫數目為 200 時，將可讓您每秒呼叫 API 4000 次。

移至 [Azure 傳統入口網站](https://manage.windowsazure.com/)，按一下左邊的機器學習服務圖示，選取用於發佈 Web 服務的工作空間，按一下所需的 Web 服務，按一下面板下方的 [新增端點]，然後提供新端點的名稱、說明及所需的並行要求。

若要新增新端點，請參閱[建立端點](machine-learning-create-endpoint.md)。

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_0720_2016-->