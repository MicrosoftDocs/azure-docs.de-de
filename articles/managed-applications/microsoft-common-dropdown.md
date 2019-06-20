---
title: Benutzeroberflächenelement DropDown in Azure | Microsoft-Dokumentation
description: Hier wird das Benutzeroberflächenelement Microsoft.Common.DropDown für das Azure-Portal beschrieben.
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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e78fa419b067c0bad48229dcfd8d4e986fc16903
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62117300"
---
# <a name="microsoftcommondropdown-ui-element"></a>Benutzeroberflächenelement „Microsoft.Common.DropDown“
Ein Auswahlsteuerelement mit einer Dropdownliste.

## <a name="ui-sample"></a>Benutzeroberflächenbeispiel
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="remarks"></a>Anmerkungen

- Die Bezeichnung für `constraints.allowedValues` ist der Anzeigetext für ein Element, und sein Wert ist der Ausgabewert des Elements bei der Auswahl.
- Falls angegeben, muss der Standardwert einer in `constraints.allowedValues` vorhandenen Bezeichnung entsprechen. Ohne Angabe wird das erste Element in `constraints.allowedValues` ausgewählt. Der Standardwert lautet **null**.
- `constraints.allowedValues` muss mindestens ein Element aufweisen.
- Fügen Sie zum Emulieren eines nicht benötigten Werts `constraints.allowedValues` ein Element mit einer Bezeichnung und dem Wert `""` (leere Zeichenfolge) hinzu.

## <a name="sample-output"></a>Beispielausgabe
```json
"two"
```

## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
* Eine Beschreibung der allgemeinen Eigenschaften in Benutzeroberflächenelementen finden Sie unter [CreateUiDefinition-Elemente](create-uidefinition-elements.md).
