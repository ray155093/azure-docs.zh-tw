---
title: "Azure Resource Manager 範本函式 - 數值 | Microsoft Docs"
description: "描述 Azure Resource Manager 範本中用來使用數值的函式。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 5844540801a6f0ff593b3f404f6815473c65a52e
ms.contentlocale: zh-tw
ms.lasthandoff: 04/28/2017


---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager 範本的數值函式

資源管理員提供下列函式以使用整數：

* [新增](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [min](#min)
* [max](#max)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>新增
`add(operand1, operand2)`

傳回兩個所提供整數加總後的數字。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- | 
|operand1 |是 |int |要新增的第一個數字。 |
|operand2 |是 |int |要新增的第二個數字。 |

### <a name="examples"></a>範例

下列範例會新增兩個參數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>傳回值

整數，其中包含參數的總和。

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(offset)`

傳回反覆項目迴圈的索引。 

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| Offset |否 |int |要加入到以零為起始之反覆項目值的數字。 |

### <a name="remarks"></a>備註

這個函式一律搭配 **copy** 物件使用。 如果未針對 **offset** 提供任何值，則會傳回目前的反覆項目值。 反覆項目值是從零開始。 如需如何使用 **copyIndex**的完整範例，請參閱 [在 Azure Resource Manager 中建立資源的多個執行個體](resource-group-create-multiple.md)。

### <a name="examples"></a>範例

下列範例顯示複製迴圈以及名稱中所包含的索引值。 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>傳回值

整數，代表目前的反覆項目索引。

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

傳回兩個所提供整數相除後的商。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |被除數。 |
| operand2 |是 |int |除數。 不能為 0。 |

### <a name="examples"></a>範例

下列範例會使用一個參數除以另一個參數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>傳回值

代表除法的整數。

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

將值轉換為浮點數。 您只會在將自訂參數傳遞給應用程式 (例如邏輯應用程式) 時使用這個函式。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |字串或整數 |要轉換為浮點數的值。 |

### <a name="examples"></a>範例

下列範例顯示如何使用 float 將參數傳遞給邏輯應用程式：

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
        "custom1": {
            "value": "[float('3.0')]"
        },
        "custom2": {
            "value": "[float(3)]"
        },
```

### <a name="return-value"></a>傳回值
浮點數。

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

將指定的值轉換成整數。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| valueToConvert |是 |字串或整數 |要轉換成整數的值。 |

### <a name="examples"></a>範例

下列範例會將使用者提供的參數值轉換為整數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[variables('intValue')]"
        }
    }
}
```

### <a name="return-value"></a>傳回值

整數。

<a id="min" />

## <a name="min"></a>Min
`min (arg1)`

傳回整數陣列的最小值，或以逗號分隔的整數清單。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整數的陣列，或以逗號分隔的整數清單 |要用來取得最小值的集合。 |

### <a name="examples"></a>範例

下列範例顯示如何搭配使用 min 與陣列和整數清單：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

### <a name="return-value"></a>傳回值

整數，代表集合中的最小值。

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

傳回整數陣列的最大值，或以逗號分隔的整數清單。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整數的陣列，或以逗號分隔的整數清單 |要用來取得最大值的集合。 |

### <a name="examples"></a>範例

下列範例顯示如何搭配使用 max 與陣列和整數清單：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

### <a name="return-value"></a>傳回值

整數，代表集合中的最大值。

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

傳回兩個所提供整數相除後的餘數。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |被除數。 |
| operand2 |是 |int |除數，不能為 0。 |

### <a name="examples"></a>範例

下列範例傳回的是一個參數除以另一個參數的餘數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>傳回值
代表餘數的整數。

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

傳回兩個所提供整數相乘後的數字。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |要相乘的第一個數字。 |
| operand2 |是 |int |要相乘的第二個數字。 |

### <a name="examples"></a>範例

下列範例會使用一個參數乘以另一個參數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>傳回值

代表乘法的整數。

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

傳回兩個所提供整數相減後的數字。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |減數。 |
| operand2 |是 |int |被減數。 |

### <a name="examples"></a>範例

下列範例會對一個參數減去另一個參數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>傳回值
代表減法的整數。

## <a name="next-steps"></a>後續步驟
* 如需有關 Azure Resource Manager 範本中各區段的說明，請參閱[編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 若要合併多個範本，請參閱[透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。
* 若要依指定的次數重複建立資源類型，請參閱 [在 Azure 資源管理員中建立資源的多個執行個體](resource-group-create-multiple.md)。
* 若要了解如何部署已建立的範本，請參閱[使用 Azure Resource Manager 範本部署應用程式](resource-group-template-deploy.md)。


