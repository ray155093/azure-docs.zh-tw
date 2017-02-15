---
title: "x12 追蹤結構描述 | Microsoft Docs"
description: "深入了解 X12 追蹤結構描述"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 48dfc8327b7e2dfb16b0392fd6d44e83bdfb1177


---
# <a name="x12-tracking-schemas"></a>X12 追蹤結構描述
支援的 X12 追蹤結構描述為

* X12 交易集追蹤結構描述
* X12 交易集通知追蹤結構描述
* X12 交換追蹤結構描述
* X12 交換通知追蹤結構描述
* X12 功能群組追蹤結構描述
* X12 功能群組通知追蹤結構描述

## <a name="x12-transaction-set-tracking-schema"></a>X12 交易集追蹤結構描述
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "", 
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| 屬性 | 說明 |
| --- | --- |
| senderPartnerName |選用，字串。  指出 X12 訊息傳送夥伴名稱 |
| receiverPartnerName |選用，字串。  指出 X12 訊息接收夥伴名稱 |
| senderQualifier |必要，字串。  指出傳送夥伴限定詞 |
| senderIdentifier |必要，字串。  指出傳送夥伴識別碼 |
| receiverQualifier |必要，字串。  指出接收夥伴限定詞 |
| receiverQualifier |必要，字串。  指出接收夥伴識別碼 |
| agreementName |選用，字串。  據以解析訊息的 X12 合約名稱 |
| direction |必要，列舉。  指出訊息流程的方向。  允許的值為 receive 或 send |
| interchangeControlNumber |選用，字串。  指出交換控制編號 |
| functionalGroupControlNumber |選用，字串。  指出功能控制編號 |
| transactionSetControlNumber |選用，字串。  指出交易集控制編號 |
| CorrelationMessageId |選用，字串。  指出相互關聯訊息識別碼。  相互關聯識別碼是 {AgreementName}{GroupControlNumber}{TransactionSetControlNumber} 組合 |
| messageType |選用，字串。 指出交易集或文件類型 |
| isMessageFailed |必要，布林值。  指出 X12 訊息是成功或失敗 |
| isTechnicalAcknowledgmentExpected |必要，布林值。  指出 X12 合約中是否設定技術通知 |
| isFunctionalAcknowledgmentExpected |必要，布林值。  指出 X12 合約中是否設定功能通知 |
| needAk2LoopForValidMessages |必要，布林值。  - 有效的訊息是否需要 AK2 迴圈。 - 布林值類型 |
| segmentsCount |選用，整數。  指出 X12 交易集的區段數目 |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 交易集通知追蹤結構描述
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| 屬性 | 說明 |
| --- | --- |
| senderPartnerName |選用，字串。  指出 X12 訊息傳送者的夥伴名稱 |
| receiverPartnerName |選用，字串。  指出 X12 訊息接收者的夥伴名稱 |
| senderQualifier |必要，字串。  指出傳送夥伴限定詞 |
| senderIdentifier |必要，字串。  指出傳送夥伴識別碼 |
| receiverQualifier |必要，字串。  指出接收夥伴限定詞 |
| receiverQualifier |必要，字串。  指出接收夥伴識別碼 |
| agreementName |選用，字串。  據以解析訊息的 X12 合約名稱 |
| direction |必要，列舉。  指出訊息流程的方向。  允許的值為 receive 或 send |
| interchangeControlNumber |選用，字串。  指出功能通知的交換控制編號。 僅針對傳送至夥伴的訊息而接收功能通知的傳送端，才填入值 |
| functionalGroupControlNumber |選用，字串。  指出功能通知的功能群組控制編號。 僅針對傳送至夥伴的訊息而接收功能通知的傳送端，才填入值 |
| isaSegment |選用，字串。  指出訊息的 ISA 區段。 僅針對傳送至夥伴的訊息而接收功能通知的傳送端，才填入值 |
| isaSegment |選用，字串。  指出訊息的 GS 區段。 僅針對傳送至夥伴的訊息而接收功能通知的傳送端，才填入值 |
| respondingfunctionalGroupControlNumber |選用，字串。  指出回應交換控制編號 |
| respondingFunctionalGroupId |選用，字串。 指出回應功能群組識別碼，在通知中對應至 AK101 |
| respondingtransactionSetControlNumber |選用，字串。  指出回應交易集控制編號 |
| respondingTransactionSetId |選用，字串。  指出回應交易集識別碼，在通知中對應至 AK201 |
| StatusCode |必要，布林值。  指出交易集通知狀態碼 |
| segmentsCount |必要，列舉。  指出通知狀態碼。  允許的值為 Accepted、Rejected 或 AcceptedWithErrros |
| processingStatus |必要，列舉。  指出通知的處理狀態。  允許的值為 Received、Generated 或 Sent |
| CorrelationMessageId |選用，字串。  指出相互關聯訊息識別碼。  相互關聯識別碼是 {AgreementName}{GroupControlNumber}{TransactionSetControlNumber} 組合 |
| isMessageFailed |必要，布林值。  指出 X12 訊息是成功或失敗 |
| ak2Segment |選用，字串。 指出 ak2 區段。 ak2 區段指出接收的功能群組內交易集的通知 |
| ak3Segment |選用，字串。 指出 ak3 區段。  ak3 區段報告資料區段中的錯誤 |
| ak5Segment |選用，字串。 指出 ak5 區段。  ak5 區段報告是否接受或拒絕 AK2 區段中識別的交易集，以及原因 |

## <a name="x12-interchange-tracking-schema"></a>X12 交換追蹤結構描述
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| 屬性 | 說明 |
| --- | --- |
| senderPartnerName |選用，字串。  指出 X12 訊息傳送者的夥伴名稱 |
| receiverPartnerName |選用，字串。  指出 X12 訊息接收者的夥伴名稱 |
| senderQualifier |必要，字串。  指出傳送夥伴限定詞 |
| senderIdentifier |必要，字串。  指出傳送夥伴識別碼 |
| receiverQualifier |必要，字串。  指出接收夥伴限定詞 |
| receiverQualifier |必要，字串。  指出接收夥伴識別碼 |
| agreementName |選用，字串。  據以解析訊息的 X12 合約名稱 |
| direction |必要，列舉。  指出訊息流程的方向。  允許的值為 receive 或 send |
| interchangeControlNumber |選用，字串。  指出交換控制編號 |
| isaSegment |選用，字串。  指出訊息 ISA 區段 |
| isTechnicalAcknowledgmentExpected |必要，布林值。  指出 X12 合約中是否設定技術通知 |
| isMessageFailed |必要，布林值。  指出 X12 訊息是成功或失敗 |
| isa09 |選用，字串。  指出 X12 文件交換日期 |
| isa10 |選用，字串。 指出 X12 文件交換時間 |
| isa11 |選用，字串。 指出 X12 交換控制標準識別碼 |
| isa12 |選用，字串。  指出 X12 交換控制版本號碼 |
| isa14 |選用，字串。  指出已要求 X12 通知 |
| isa15 |選用，字串。  指出用於測試或生產的指標 |
| isa16 |選用，字串。 指出元素分隔符號 |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 交換通知追蹤結構描述
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| 屬性 | 說明 |
| --- | --- |
| senderPartnerName |選用，字串。  指出 X12 訊息傳送者的夥伴名稱 |
| receiverPartnerName |選用，字串。  指出 X12 訊息接收者的夥伴名稱 |
| senderQualifier |必要，字串。  指出傳送夥伴限定詞 |
| senderIdentifier |必要，字串。  指出傳送夥伴識別碼 |
| receiverQualifier |必要，字串。  指出接收夥伴限定詞 |
| receiverQualifier |必要，字串。  指出接收夥伴識別碼 |
| agreementName |選用，字串。  據以解析訊息的 X12 合約名稱 |
| direction |必要，列舉。  指出訊息流程的方向。  允許的值為 receive 或 send |
| interchangeControlNumber |選用，字串。  指出技術通知的交換控制編號。 從夥伴收到的技術通知有這個值 |
| isaSegment |選用，字串。  指出技術通知的 ISA 區段。 從夥伴收到的技術通知有這個值 |
| respondingInterchangeControlNumber |選用，字串。  指出技術通知的交換控制編號。 從夥伴收到的技術通知有這個值 |
| isMessageFailed |必要，布林值。  指出 X12 訊息是成功或失敗 |
| StatusCode |必要，列舉。  指出交換通知狀態碼。  允許的值為 Accepted/Rejected/AcceptedWithErrros |
| processingStatus |必要，列舉。  指出通知狀態。  允許的值為 Received/Generated/Sent |
| ta102 |選用，字串。 指出交換日期 |
| ta103 |選用，字串。 指出交換時間 |
| ta105 |選用，字串。 指出交換說明碼 |

## <a name="x12-functional-group-tracking-schema"></a>X12 功能群組追蹤結構描述
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| 屬性 | 說明 |
| --- | --- |
| senderPartnerName |選用，字串。  指出 X12 訊息傳送者的夥伴名稱 |
| receiverPartnerName |選用，字串。  指出 X12 訊息接收者的夥伴名稱 |
| senderQualifier |必要，字串。  指出傳送夥伴限定詞 |
| senderIdentifier |必要，字串。  指出傳送夥伴識別碼 |
| receiverQualifier |必要，字串。  指出接收夥伴限定詞 |
| receiverQualifier |必要，字串。  指出接收夥伴識別碼 |
| agreementName |選用，字串。  據以解析訊息的 X12 合約名稱 |
| direction |必要，列舉。  指出訊息流程的方向。  允許的值為 receive 或 send |
| interchangeControlNumber |選用，字串。 指出交換控制編號 |
| functionalGroupControlNumber |選用 - 功能控制編號 - 字串類型 |
| isaSegment |選用，字串。  指出訊息 GS 區段 |
| isTechnicalAcknowledgmentExpected |必要，布林值。  指出 X12 合約中是否設定技術通知 |
| isFunctionalAcknowledgmentExpected |必要，布林值。  指出 X12 合約中是否設定功能通知 |
| isMessageFailed |必要，布林值。  指出 X12 訊息是成功或失敗 |
| gs01 |選用，字串。 指出功能識別碼 |
| gs02 |選用，字串。 指出應用程式傳送者的代碼 |
| gs03 |選用，字串。 指出應用程式接收者的代碼 |
| gs04 |選用，字串。 指出功能群組日期 |
| gs05 |選用，字串。 指出功能群組時間 |
| gs07 |選用，字串。 指出負責單位代碼 |
| gs08 |選用，字串。 指出版本/版次/產業識別碼 - 字串類型 |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 功能群組通知追蹤結構描述
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| 屬性 | 說明 |
| --- | --- |
| senderPartnerName |選用，字串。  指出 X12 訊息傳送者的夥伴名稱 |
| receiverPartnerName |選用，字串。  指出 X12 訊息接收者的夥伴名稱 |
| senderQualifier |必要，字串。  指出傳送夥伴限定詞 |
| senderIdentifier |必要，字串。  指出傳送夥伴識別碼 |
| receiverQualifier |必要，字串。  指出接收夥伴限定詞 |
| receiverQualifier |必要，字串。  指出接收夥伴識別碼 |
| agreementName |選用，字串。  據以解析訊息的 X12 合約名稱 |
| direction |必要，列舉。  指出訊息流程的方向。  允許的值為 receive 或 send |
| interchangeControlNumber |選用，字串。 指出交換控制編號。 從夥伴收到技術通知時，傳送端會填入此值 |
| functionalGroupControlNumber |選用，字串。 指出技術通知的功能群組控制編號。 從夥伴收到技術通知時，傳送端會填入此值 |
| isaSegment |選用 - 同上，只在特定情況下才會填入交換控制編號。 - 字串類型 |
| isaSegment |選用 - 同上，只在特定情況下才會填入功能群組控制編號。 - 字串類型 |
| respondingfunctionalGroupControlNumber |選用 - 原始功能群組的控制編號 - 字串類型 |
| respondingFunctionalGroupId |選用 - 在 ack 中對應到 AK101 - 功能群組識別碼 - 字串類型 |
| isMessageFailed |必要，布林值。  指出 X12 訊息是成功或失敗 |
| StatusCode |必要，列舉。  指出通知狀態碼。 允許的值為 Accepted/Rejected/AcceptedWithErrros |
| processingStatus |必要，列舉。  指出通知的處理狀態。 允許的值為 Received/Generated/Sent |
| ak903 |選用，字串。 指出收到的交易集數目 |
| ak904 |選用，字串。 指出所識別的功能群組中接受的交易集數目 |
| ak9Segment |選用，字串。  Ak9 區段指出是否接受或拒絕 AK1 區段中識別的功能群組，以及原因 |

## <a name="next-steps"></a>後續步驟
[深入了解監視 B2B 訊息](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[在 OMS 入口網站中追蹤 B2B 訊息](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[深入了解自訂追蹤結構描述](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[深入了解 AS2 追蹤結構描述](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Schema")   
[深入了解企業整合套件](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->


