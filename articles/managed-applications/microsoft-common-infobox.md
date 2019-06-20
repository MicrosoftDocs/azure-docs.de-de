---
title: Benutzeroberflächenelement „InfoBox“ in Azure | Microsoft-Dokumentation
description: Hier wird das Benutzeroberflächenelement „Microsoft.Common.InfoBox“ für das Azure-Portal beschrieben.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 2330197b4512dfdd72de3529145103b644594e25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64711220"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Benutzeroberflächenelement „Microsoft.Common.InfoBox“
Ein Steuerelement, das ein Informationsfeld hinzugefügt. Das Feld enthält wichtigen Text oder Warnungen, damit die Benutzer ein besseres Verständnis der Werte erlangen, die sie angeben. Außerdem kann es einen Link zu einer URI bereitstellen, wenn weitere Informationen benötigt werden.

## <a name="ui-sample"></a>Benutzeroberflächenbeispiel
![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>Schema
```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="remarks"></a>Anmerkungen

* Verwenden Sie für `icon` entweder **None**, **Info**, **Warning** oder **Error**.
* Die `uri`-Eigenschaft ist optional.

## <a name="sample-output"></a>Beispielausgabe

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
* Eine Beschreibung der allgemeinen Eigenschaften in Benutzeroberflächenelementen finden Sie unter [CreateUiDefinition-Elemente](create-uidefinition-elements.md).
