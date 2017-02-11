---
title: "在邏輯應用程式中新增 Microsoft Translator | Microsoft Docs"
description: "搭配 REST API 參數來使用 Microsoft Translator 連接器的概觀"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: da782baf-8bf8-4973-8238-e469865f5328
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: f6e7cf0e9e2055df5f8a9fcf9ea49330a91be656


---
# <a name="get-started-with-the-microsoft-translator-connector"></a>開始使用 Microsoft Translator 連接器
連線到 Microsoft Translator 來翻譯文字、偵測語言種類等等。 您可以利用 Microsoft Translator 來： 

* 根據您從 Microsoft Translator 所取得的資料，來建置您的商務流程。 
* 使用動作來翻譯文字、偵測語言種類等等。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，當 Dropbox 中有新檔案建立時，您可以利用 Microsoft Translator 把該檔案中的文字翻譯成另一種語言。

如要在邏輯應用程式中新增作業，請參閱 [建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Microsoft Translator 包含下列動作， 但不包含觸發程序。

| 觸發程序 | 動作 |
| --- | --- |
| None |<ul><li>偵測語言種類</li><li>將文字轉換成語音</li><li>翻譯文字</li><li>取得語言種類</li><li>取得語音的語言種類</li></ul> |

所有連接器都支援 JSON 和 XML 格式的資料。

## <a name="create-a-connection-to-microsoft-translator"></a>建立至 Microsoft Translator 的連線
> [!INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 參考
適用的版本：1.0。

### <a name="detect-language"></a>偵測語言種類
偵測指定文字的原始語言種類。  
```GET: /Detect```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| query |字串 |yes |query |None |要確認其語言種類的文字 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="text-to-speech"></a>將文字轉換成語音
將指定文字轉換成語音，成為 Wave 格式的音訊串流。  
```GET: /Speak```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| query |字串 |yes |query |None |要轉換的文字 |
| 語言 |字串 |yes |query |無 |用來產生語音的語言代碼 (例如：「en-us」) |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="translate-text"></a>翻譯文字
利用 Microsoft Translator 來將文字翻譯成指定語言。  
```GET: /Translate```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| query |字串 |yes |query |None |要翻譯的文字 |
| languageTo |字串 |yes |query |None |目標語言代碼 (例如：「fr」) |
| languageFrom |字串 |no |query |無 |原始語言；如果未提供，Microsoft Translator 會嘗試自動偵測。 (範例：en) |
| category |字串 |no |query |一般 |翻譯類別 (預設值：「general」) |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="get-languages"></a>取得語言種類
擷取 Microsoft Translator 支援的所有語言種類。  
```GET: /TranslatableLanguages```

這個呼叫沒有參數。 

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

### <a name="get-speech-languages"></a>取得語音的語言種類
擷取提供語音合成功能的語言種類。  
```GET: /SpeakLanguages``` 

這個呼叫沒有參數。

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 預設值 |作業失敗。 |

## <a name="object-definitions"></a>物件定義
#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Language：Microsoft Translator 可翻譯語言的語言模型
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| 代碼 |字串 |no |
| 名稱 |字串 |no |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

返回 [API 清單](apis-list.md)。

<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png



<!--HONumber=Nov16_HO3-->


