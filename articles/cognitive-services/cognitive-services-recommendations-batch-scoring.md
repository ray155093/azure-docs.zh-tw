<properties
	pageTitle="在批次中取得建議：機器學習服務的建議 API | Microsoft Azure"
	description="Azure 機器學習服務建議 - 在批次中取得建議"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="luisca"/>

# 以批次方式取得建議

>[AZURE.NOTE] 相較於一次取得一個建議結果，以批次方式取得建議會比較複雜。如果要為單一要求取得建議，請檢查此處的 API：<br> [ 項目對項目建議](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br> [使用者對項目建議](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> 批次計分僅適用建立於 2016 年 7 月 21 日之後的組建


也有些情況是您需要一次取得對多個項目的建議。例如，您可能想要建立建議快取，或分析您所取得的建議類型。

我們所稱的批次計分作業是非同步作業。您需要提交要求，等待作業完成，然後收集結果。以下列出要遵循的步驟，以求更精確地說明︰

1.	如果您還沒有 Azure 儲存體容器，請建立一個。
2.	將描述您每個建議要求的輸入檔上傳至 Azure Blob 儲存體。
3.	開始進行評分的批次作業。
4.	等候非同步作業完成。
5.	完成後，請從 Azure Blob 儲存體收集結果。

我們將逐步解說各個步驟。

## 如果您還沒有 Azure 儲存體容器，請建立一個

如果您還沒有新的儲存體帳戶，請前往 [Azure 管理入口網站](https://portal.azure.com)瀏覽 [新增] > [資料+儲存體] > [儲存體帳戶]，以建立一個新的儲存體帳戶。

有儲存體帳戶之後，您需要建立 Blob 容器，用以儲存批次執行的輸入和輸出。

將描述您每個建議要求的輸入檔上傳至 Azure Blob - 在此先稱為 input.json。有容器之後，您需要上傳描述每個要求的檔案，才能從建議服務執行。給定的批次只能從特定組建的一種要求執行。我們將在下一節說明如何定義此資訊，現在假設我們將在特定組建之外執行項目推薦。因此，輸入檔會包含每個要求的輸入資訊 (在此為種子項目)。

以下是 input.json 檔外觀的範例：

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }
    
如您所見，此檔案是 JSON 檔案，其中每個要求都有傳送建議要求所需的資訊。為您必須完成的要求建立類似的 JSON 檔案，並複製到剛才在 Blob 儲存體建立的容器。

## 開始進行批次作業

下一步是提交新的批次作業。查看 [此處](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/)的 API 參考資料。

API 要求本文必須定義須儲存輸入、輸出及錯誤檔的位置，以及存取位置所需的認證。此外，您必須指定一些適用於整個批次的參數 (要求的建議類型、要使用的模型/組建、每次呼叫的結果數目等等)

以下是要求本文外觀的範例：

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

注意事項：

-	目前 AuthenticationType 應一律設為 PublicOrSas。
-	您必須取得共用存取簽章 (SAS) 權杖，以允許 Recommendations API 讀取和寫入自/至您的 Blob 儲存體帳戶。有關產生 SAS 權杖的詳細資訊請參見[此處](../storage/storage-dotnet-shared-access-signature-part-1.md)
-	目前唯一支援的 *apiName* 是用於項目對項目建議的 “ItemRecommend”。目前的批次不支援使用者對項目的建議。

## 等候非同步作業完成。

當您啟動批次作業時，回應會傳回「作業 - 位置」標頭，此標頭可提供追蹤作業所需的資訊。您可以和追蹤組建作業一樣，使用 [擷取作業狀態 API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da) 來追蹤作業。

## 取得結果

作業完成並假設作業沒有錯誤，您就可以從輸出 Blob 儲存體收集結果。

以下是輸出檔外觀的範例。為求簡單明瞭，我們以有兩個要求的批次為例。

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## 限制

-	每個訂用帳戶一次只能呼叫一個批次作業。
-	批次作業輸入檔不能超過 2 MB。

<!---HONumber=AcomDC_0727_2016-->