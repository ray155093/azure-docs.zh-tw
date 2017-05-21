---
title: "Azure 受管理的應用程式 UserNameTextBox UI 元素 | Microsoft Docs"
description: "描述 Azure 受管理應用程式的 Microsoft.Compute.UserNameTextBox UI 元素"
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
ms.openlocfilehash: 1379c1c48d883fe74d49be16e081219a52f611da
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI 元素
使用 Windows 和 Linux 使用者名稱內建驗證的文字方塊控制項。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>結構描述
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>備註
- 如果將 `constraints.required` 設為 **true**，文字方塊就必須包含值，才能順利通過驗證。 預設值為 **true**。
- 必須指定 `osPlatform`，且可以是 **Windows** 或 **Linux**。
- `constraints.regex` 是 JavaScript 規則運算式模式。 如果指定，文字方塊的值就必須符合模式，才能順利通過驗證。 預設值為 **null**。
- 當文字方塊的值無法通過 `constraints.regex` 驗證時，就會顯示 `constraints.validationMessage` 字串。 如果未指定，則會使用文字方塊的內建的驗證訊息。 預設值為 **null**。
- 這個元素具有根據 `osPlatform` 指定值的內建驗證。 內建驗證可以與自訂規則運算式搭配使用。
如果指定了 `constraints.regex` 值，就會觸發內建和自訂驗證。

## <a name="sample-output"></a>範例輸出
```json
"tabrezm"
```

## <a name="next-steps"></a>後續步驟
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

