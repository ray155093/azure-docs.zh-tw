---
title: "Azure 受管理的應用程式 TextBox UI 元素 | Microsoft Docs"
description: "描述 Azure 受管理應用程式的 Microsoft.Common.TextBox UI 元素"
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
ms.openlocfilehash: aa161d2f6611cd7f1ef8fac8eebfde12eedd9fcf
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---

# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI 元素
可以用來編輯未格式化文字的控制項。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>結構描述
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Halp!",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>備註
- 如果將 `constraints.required` 設為 **true**，文字方塊就必須包含值，才能順利通過驗證。 預設值為 **false**。
- `constraints.regex` 是 JavaScript 規則運算式模式。 如果指定，文字方塊的值就必須符合模式，才能順利通過驗證。 預設值為 **null**。
- 當文字方塊的值無法通過驗證時，就會顯示 `constraints.validationMessage` 字串。 如果未指定，則會使用文字方塊的內建的驗證訊息。 預設值為 **null**。
- 當 `constraints.required` 設為 **false** 時，您可指定 `constraints.regex` 的值。 在此案例中，文字方塊不需要值即可順利通過驗證。 如果指定的話，它必須符合規則運算式模式。

## <a name="sample-output"></a>範例輸出

```json
"foobar"
```

## <a name="next-steps"></a>後續步驟
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

