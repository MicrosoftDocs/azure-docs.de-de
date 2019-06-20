---
title: Benutzeroberflächenelement MultiStorageAccountCombo in Azure | Microsoft-Dokumentation
description: Hier wird das Benutzeroberflächenelement Microsoft.Storage.MultiStorageAccountCombo für das Azure-Portal beschrieben.
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
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 08b65770414e9ee1cb5e478427fe7654b2bb9a78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64725448"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Benutzeroberflächenelement „Microsoft.Storage.MultiStorageAccountCombo“
Eine Gruppe von Steuerelementen zum Erstellen mehrerer Speicherkonten mit Namen, die mit einem allgemeinen Präfix beginnen.

## <a name="ui-sample"></a>Benutzeroberflächenbeispiel
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Anmerkungen
- Der Wert für `defaultValue.prefix` ist mit einer Ganzzahl oder mehreren Ganzzahlen verkettet, um die Sequenz der Speicherkontonamen zu generieren. Beispiel: Wenn `defaultValue.prefix` und **sa** und `count` **2** ist, werden die Speicherkontonamen **sa1** und **sa2** erstellt. Generierte Speicherkontonamen werden automatisch auf Eindeutigkeit überprüft.
- Die Speicherkontonamen werden lexikografisch basierend auf `count` erstellt. Beispiel: Wenn für `count` der Wert 10 angegeben ist, enden die Speicherkontonamen auf zweistelligen Integerwerten (01, 02, 03).
- Der Standardwert für `defaultValue.prefix` lautet **null** und für `defaultValue.type` **Premium_LRS**.
- Alle nicht in `constraints.allowedTypes` angegebenen Typen werden ausgeblendet, und nicht in `constraints.excludedTypes` angegebene Typen werden angezeigt. `constraints.allowedTypes` und `constraints.excludedTypes` sind optional, können jedoch nicht gleichzeitig angegeben werden.
- Zusätzlich zur Erstellung von Speicherkontonamen wird `count` zum Festlegen des entsprechenden Multiplikators für das Element verwendet. Das Element unterstützt einen statischen Wert wie **2** oder einen dynamischen Wert aus einem anderen Element, etwa `[steps('step1').storageAccountCount]`. Der Standardwert ist **1**.

## <a name="sample-output"></a>Beispielausgabe

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
* Eine Beschreibung der allgemeinen Eigenschaften in Benutzeroberflächenelementen finden Sie unter [CreateUiDefinition-Elemente](create-uidefinition-elements.md).
