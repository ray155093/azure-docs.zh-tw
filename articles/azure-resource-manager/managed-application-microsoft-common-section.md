---
title: "Azure 受管理的應用程式 Section UI 元素 | Microsoft Docs"
description: "描述 Azure 受管理應用程式的 Microsoft.Common.Section UI 元素"
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 7111b02432a774e056b5a5e13f93ae7f11fb6848
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI 元素
將標題下方一個或多個元素進行群組的控制項。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>結構描述
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>備註
- `elements` 必須包含至少一個元素，且可以包含 `Microsoft.Common.Section` 以外的所有元素類型。
- 此元素不支援 `toolTip` 屬性。

## <a name="sample-output"></a>範例輸出
若要存取 `elements` 中的元素輸出值，可使用 [basics()](managed-application-createuidefinition-functions.md#basics) 或 [steps()](managed-application-createuidefinition-functions.md#steps) 函式和點標記法︰

```json
basics('section1').element1
```

`Microsoft.Common.Section` 類型的元素本身沒有任何輸出值。

## <a name="next-steps"></a>後續步驟
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

