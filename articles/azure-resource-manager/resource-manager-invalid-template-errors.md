---
title: 'Fehler in Azure: ungültige Vorlage | Microsoft-Dokumentation'
description: Beschreibt, wie Fehler aufgrund ungültiger Vorlagen in Azure behoben werden.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: ee4bce38e0fcde93ba0417617ae90dab2eefda67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61061390"
---
# <a name="resolve-errors-for-invalid-template"></a>Beheben von Fehlern aufgrund ungültiger Vorlagen

In diesem Artikel wird beschrieben, wie Fehler aufgrund ungültiger Vorlagen in Azure behoben werden.

## <a name="symptom"></a>Symptom

Wenn Sie eine Vorlage bereitstellen, erhalten Sie eine Fehlermeldung:

```
Code=InvalidTemplate
Message=<varies>
```

Die Fehlermeldung hängt vom Typ des Fehlers ab.

## <a name="cause"></a>Ursache

Dieser Fehler kann aus verschiedenen Arten von Fehlern entstehen. Normalerweise handelt es sich um einen Syntax- oder Strukturfehler in der Vorlage.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Lösung 1 – Syntaxfehler

Wenn Sie eine Fehlermeldung erhalten, die auf eine fehlerhafte Vorlagenüberprüfung hinweist, ist in Ihrer Vorlage möglicherweise ein Syntaxproblem vorhanden.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Solche Fehler können sehr leicht passieren, da Vorlagenausdrücke recht kompliziert sein können. Beispielsweise umfasst die folgende Namenszuweisung für ein Speicherkonto Klammern, drei Funktionen, drei Sätze mit Klammern, einen Satz mit einfachen Anführungszeichen und eine Eigenschaft:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Wenn Sie nicht die passende Syntax bereitstellen, erzeugt die Vorlage einen Wert, der nicht Ihrer Absicht entspricht.

Wenn Sie diese Art von Fehler erhalten, überprüfen Sie sorgfältig die Ausdruckssyntax. Verwenden Sie ggf. einen JSON-Editor, der Sie auf Syntaxfehler aufmerksam machen kann (z.B. [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) oder [Visual Studio Code](resource-manager-vs-code.md)).

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Lösung 2 – falsche Segmentlängen

Ein weiterer Fehler vom Typ „Ungültige Vorlage“ tritt auf, wenn der Ressourcenname nicht im richtigen Format vorliegt.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Eine Ressource auf Stammebene muss im Namen ein Segment weniger aufweisen als im Ressourcentyp. Die einzelnen Segmente sind jeweils durch einen Schrägstrich getrennt. Im folgenden Beispiel weist der Typ zwei Segmente und der Name ein Segment auf, sodass es sich hier um einen **gültigen Namen** handelt.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Im nächsten Beispiel liegt dagegen **kein gültiger Name** vor, da er die gleiche Anzahl von Segmenten besitzt wie der Typ.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Bei untergeordneten Ressourcen besitzen Typ und Name die gleiche Anzahl von Segmenten. Diese Segmentanzahl ist sinnvoll, da der vollständige Name und Typ der untergeordneten Ressource den Namen und den Typ der übergeordneten Ressource enthalten. Aus diesem Grund weist der vollständige Name weiterhin ein Segment weniger auf als der vollständige Typ.

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

Die richtige Angabe der Segmente kann bei Resource Manager-Typen problematisch sein, die ressourcenanbieterübergreifend angewendet werden. Wenn Sie beispielsweise eine Website mit einer Ressourcensperre belegen möchten, benötigen Sie einen Typ mit vier Segmenten. Folglich umfasst der Name drei Segmente:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Lösung 3 – Parameter ungültig

Wenn Sie einen Parameterwert angeben, der nicht einem der zulässigen Werte entspricht, erhalten Sie eine Fehlermeldung der folgenden Art:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Überprüfen Sie die zulässigen Werte in der Vorlage, und geben Sie während der Bereitstellung einen gültigen Wert an. Weitere Informationen zu zulässigen Parameterwerten finden Sie im [Abschnitt „Parameters“ von Azure Resource Manager-Vorlagen](resource-group-authoring-templates.md#parameters).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Lösung 4 – zu viele Zielressourcengruppen

Wenn Sie mehr als fünf Zielressourcengruppen in einer einzelnen Bereitstellung angeben, wird dieser Fehler angezeigt. Sie sollten entweder die Anzahl der Ressourcengruppen in Ihrer Bereitstellung konsolidieren oder einige der Vorlagen als separate Bereitstellungen bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Azure-Ressourcen für mehrere Abonnements oder Ressourcengruppen](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Lösung 5 – Ringabhängigkeit erkannt

Sie erhalten diesen Fehler, wenn Ressourcen auf eine Weise voneinander abhängig sind, die das Starten der Bereitstellung verhindert. Eine Kombination aus Abhängigkeiten bewirkt, dass zwei oder mehr Ressourcen auf andere Ressourcen warten, die sich ebenfalls im Wartezustand befinden. Beispielsweise kann „resource1“ von „resource3“, „resource2“ von „resource1“ und „resource3“ von „resource2“ abhängig sein. Dieses Problem lässt sich normalerweise beheben, indem nicht benötigte Abhängigkeiten entfernt werden.

So beheben Sie eine Ringabhängigkeit

1. Suchen Sie in Ihrer Vorlage nach der Ressource, die in der Ringabhängigkeit angegeben ist. 
2. Sehen Sie sich für diese Ressource die **dependsOn**-Eigenschaft und alle Vorkommen der Funktion **reference** an, um zu ermitteln, von welchen Ressourcen sie abhängig ist. 
3. Überprüfen Sie diese Ressourcen, um zu ermitteln, von welchen Ressourcen sie abhängig sind. Verfolgen Sie die Abhängigkeiten, bis Sie auf eine Ressource stoßen, die von der Originalressource abhängig ist.
5. Untersuchen Sie für die an der Ringabhängigkeit beteiligten Ressourcen sorgfältig alle Vorkommen der **dependsOn**-Eigenschaft, um alle Abhängigkeiten zu identifizieren, die nicht benötigt werden. Entfernen Sie diese Abhängigkeiten. Wenn Sie unsicher sind, ob eine Abhängigkeit erforderlich ist, können Sie versuchen, sie zu entfernen. 
6. Stellen Sie die Vorlage erneut bereit.

Das Entfernen von Werten aus der **dependsOn**-Eigenschaft kann zu Fehlern beim Bereitstellen der Vorlage führen. Fügen Sie die Abhängigkeit wieder in die Vorlage ein, wenn ein Fehler auftritt. 

Falls sich die Ringabhängigkeit mit dieser Vorgehensweise nicht beseitigen lässt, können Sie erwägen, einen Teil Ihrer Bereitstellungslogik in untergeordnete Ressourcen zu verschieben (z.B. Erweiterungen oder Konfigurationseinstellungen). Konfigurieren Sie diese untergeordneten Ressourcen so, dass sie nach den an der Ringabhängigkeit beteiligten Ressourcen bereitgestellt werden. Nehmen wir beispielsweise an, Sie stellen zwei virtuelle Computer bereit, müssen aber Eigenschaften festlegen, die auf den jeweils anderen verweisen. Sie können diese in der folgenden Reihenfolge bereitstellen:

1. VM1
2. VM2
3. Die Erweiterung auf VM1 hängt von VM1 und VM2 ab. Die Erweiterung legt Werte auf VM1 fest, die sie von VM2 abruft.
4. Die Erweiterung auf VM2 hängt von VM1 und VM2 ab. Die Erweiterung legt Werte auf VM2 fest, die sie von VM1 abruft.

Diese Vorgehensweise funktioniert auch für App Service-Apps. Erwägen Sie, Konfigurationswerte in eine untergeordnete Ressource der App-Ressource zu verschieben. Sie können zwei Web-Apps in der folgenden Reihenfolge bereitstellen:

1. webapp1
2. webapp2
3. Die Konfiguration für „webapp1“ ist von „webapp1“ und „webapp2“ abhängig. Sie enthält App-Einstellungen mit Werten aus „webapp2“.
4. Die Konfiguration für „webapp2“ ist von „webapp1“ und „webapp2“ abhängig. Sie enthält App-Einstellungen mit Werten aus „webapp1“.
