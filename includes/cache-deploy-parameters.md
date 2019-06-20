---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177900"
---
### <a name="cacheskuname"></a>cacheSKUName

Der Tarif der neuen Azure Cache for Redis-Instanz.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

Die Vorlage definiert die Werte, die für diesen Parameter zulässig sind (Basic, Standard oder Premium), und weist einen Standardwert (Basic) zu, wenn kein Wert angegeben wird. "Basic" bietet einen einzelnen Knoten mit mehreren Größen bis zu 53 GB. "Standard" bietet zwei Knoten, Primär/Replikat, mit mehreren Größen bis zu 53 GB und 99,9% SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily

Die SKU-Familie.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

Die Größe der neuen Azure Cache for Redis-Instanz.

Für die Basic- und Standard-Familien:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

Die Cachekapazität des Premium-Werts ist gleich definiert, außer dass die zulässigen Werte von 1 bis 5 statt von 0 bis 6 reichen.

Die Vorlage definiert die ganzzahligen Werte, die für diesen Parameter zulässig sind (0 bis 6 für die Basic- und Standard-Familie; 1 bis 5 für die Premium-Familie). Wenn kein Wert angegeben ist, weist die Vorlage einen Standardwert von 0 für Basic und Standard, 1 für Premium zu.

Diese Werte entsprechen den folgenden Cachegrößen:

| Wert | Basic und Standard.<br>Cachegröße | Premium<br>Cachegröße |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (Standard)                 | –                   |
| 1     | 1 GB                             | 6 GB (Standard)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | –                   |
