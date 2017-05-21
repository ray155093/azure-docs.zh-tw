---
title: "Azure 受管理的應用程式 DropDown UI 元素 | Microsoft Docs"
description: "描述 Azure 受管理應用程式的 Microsoft.Common.DropDown UI 元素"
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
ms.openlocfilehash: 14809bd7ce5eec140929de88e525df0ca7442869
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---

# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI 元素
下拉式清單中的選取控制項。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>結構描述
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "Foo",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Foo",
        "value": "Bar"
      },
      {
        "label": "Baz",
        "value": "Qux"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>備註
- `constraints.allowedValues` 的標籤是項目的顯示文字，其值為選取時的元素輸出值。
- 如果指定，預設值必須是 `constraints.allowedValues` 中存在的標籤。 如果未指定，則會選取 `constraints.allowedValues` 中的第一個項目。 預設值為 **null**。
- `constraints.allowedValues` 必須包含至少一個項目。
- 此元素不支援 `constraints.required` 屬性。 若要模擬此行為，將包含標籤和 `""` (空字串) 值的項目新增至 `constraints.allowedValues`。

## <a name="sample-output"></a>範例輸出
```json
"Bar"
```

## <a name="next-steps"></a>後續步驟
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

