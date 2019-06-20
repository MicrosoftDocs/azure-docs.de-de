---
title: Grundlegendes zum Erstellen von Benutzeroberflächendefinitionen für verwaltete Azure-Anwendungen | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Benutzeroberflächendefinitionen für verwaltete Azure-Anwendungen erstellen.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 3d0a6d97440404904c041369a4631fdd3fb618b4
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257561"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Erstellen einer Benutzeroberfläche im Azure-Portal für die verwaltete Anwendung
In diesem Dokument werden die grundlegenden Konzepte der Datei „createUiDefinition.json“ vorgestellt. Das Azure-Portal verwendet diese Datei zum Generieren der Benutzeroberfläche zum Erstellen einer verwalteten Anwendung.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

Ein CreateUiDefinition-Element enthält immer drei Eigenschaften: 

* handler
* version
* parameters

Bei verwalteten Anwendungen muss für „handler“ immer `Microsoft.Compute.MultiVm` angegeben werden. Die letzte unterstützte Version ist `0.1.2-preview`.

Das Schema der parameters-Eigenschaft hängt von der Kombination aus den angegebenen Werten für „handler“ und „version“ ab. Für verwaltete Anwendungen lauten die unterstützten Eigenschaften `basics`, `steps` und `outputs`. Die Eigenschaften „basics“ und „steps“ enthalten die _Elemente_  (wie Textfelder und Dropdownfelder), die im Azure-Portal angezeigt werden sollen. Mit der outputs-Eigenschaft werden die Ausgabewerte der angegebenen Elemente den Parametern der Azure Resource Manager-Bereitstellungsvorlage zugeordnet.

Die Aufnahme von `$schema` wird empfohlen, ist aber optional. Wenn ein Wert angegeben wird, muss der Wert für `version` der Version im `$schema`-URI entsprechen.

Sie können einen JSON-Editor zum Erstellen Ihrer Benutzeroberflächendefinition verwenden, oder Sie können die Benutzeroberflächendefinitions-Sandbox verwenden, um die Benutzeroberflächendefinition zu erstellen und eine Vorschau davon anzuzeigen. Weitere Informationen zur Sandbox finden Sie unter [Testen Ihrer Portaloberfläche für Azure Managed Applications](test-createuidefinition.md).

## <a name="basics"></a>Grundlagen
„basics“ ist stets der erste Schritt des Assistenten, der erstellt wird, wenn das Azure-Portal die Datei analysiert. Das Portal zeigt nicht nur die in `basics` angegebenen Elemente an, sondern fügt zusätzlich Elemente für Benutzer zum Auswählen des Abonnements, der Ressourcengruppe und des Standort für die Bereitstellung ein. Im Allgemeinen sollten Elemente, die bereitstellungsweite Parameter abfragen (wie den Namen eines Clusters oder Administratoranmeldeinformationen), in diesem Schritt enthalten sein.

Falls das Verhalten eines Elements von dem Abonnement, der Ressourcengruppe oder dem Standort eines Benutzers abhängt, kann dieses Element nicht in „basics“ verwendet werden. **Microsoft.Compute.SizeSelector** ist bei der Ermittlung der Liste verfügbarer Größen beispielsweise vom Abonnement und Standort des Benutzers abhängig. Daher kann **Microsoft.Compute.SizeSelector** nur in „steps“ verwendet werden. Im Allgemeinen können nur Elemente im **Microsoft.Common**-Namespace in „basics“ verwendet werden. Dennoch sind einige Elemente in anderen Namespaces (etwa **Microsoft.Compute.Credentials**) zulässig, die nicht vom Kontext des Benutzers abhängig sind.

## <a name="steps"></a>Schritte
Die steps-Eigenschaft kann null oder mehr zusätzliche steps-Elemente für die Anzeige nach „basics“ enthalten, von denen jeder mindestens ein Element enthält. Ziehen Sie das Hinzufügen von steps-Elementen pro Rolle oder Ebene der bereitgestellten Anwendung in Betracht. Fügen Sie beispielsweise ein steps-Element für Eingaben von den Masterknoten und ein steps-Element für die Workerknoten in einem Cluster hinzu.

## <a name="outputs"></a>Ausgaben
Mit der `outputs`-Eigenschaft ordnet das Azure-Portal Elemente aus `basics` und `steps` den Parametern der Azure Resource Manager-Bereitstellungsvorlage zu. Die Schlüssel dieses Wörterbuchs stellen die Namen der Vorlagenparameter dar, und die Werte sind Eigenschaften der Ausgabeobjekte von den referenzierten Elementen.

Um den Ressourcennamen der verwalteten Anwendung festzulegen, müssen Sie einen Wert mit dem Namen `applicationResourceName` in die Ausgabeeigenschaft eingeben. Wenn Sie diesen Wert nicht festlegen, weist die Anwendung eine GUID für den Namen zu. Sie können ein Textfeld in die Benutzeroberfläche aufnehmen, das einen Namen vom Benutzer anfordert.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>Functions
Ähnlich wie bei Vorlagenfunktionen in Azure Resource Manager (sowohl in Bezug auf Syntax als auch auf Funktionalität) stellt das CreateUiDefinition-Element Funktionen zum Arbeiten mit Ein- und Ausgaben von Elementen sowie Features wie konditionelle Abschnitte bereit.

## <a name="next-steps"></a>Nächste Schritte
Die Datei „createUiDefinition.json“ besitzt selbst ein einfaches Schema. Die tatsächliche Tiefe ergibt sich aus allen unterstützten Elementen und Funktionen. Diese Elemente werden in den folgenden Dokumenten ausführlicher beschrieben:

- [CreateUiDefinition-Elemente](create-uidefinition-elements.md)
- [Funktionen](create-uidefinition-functions.md)

Ein aktuelles JSON-Schema für createUiDefinition ist unter folgendem Link verfügbar: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Ein Beispiel für eine Benutzeroberflächendatei finden Sie unter [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
