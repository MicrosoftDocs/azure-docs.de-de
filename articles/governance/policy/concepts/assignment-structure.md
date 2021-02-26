---
title: Details zur Richtlinienzuweisungsstruktur
description: Beschreibt die Definition der Richtlinienzuweisung, die von Azure Policy verwendet wird, um Richtliniendefinitionen und -parameter zur Bewertung mit Ressourcen in Beziehung zu setzen.
ms.date: 01/29/2021
ms.topic: conceptual
ms.openlocfilehash: 12acbe368c9ccd6fa5654d3394e0fecb286984bf
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219565"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy-Zuweisungsstruktur

Richtlinienzuweisungen werden von Azure Policy zur Definition verwendet, welche Ressourcen welchen Richtlinien oder Initiativen zugewiesen werden. Die Richtlinienzuweisung kann die Werte von Parametern für diese Gruppe von Ressourcen zum Zeitpunkt der Zuweisung bestimmen, wodurch es möglich wird, Richtliniendefinitionen wiederzuverwenden, die dieselben Ressourceneigenschaften mit unterschiedlichen Anforderungen an die Compliance ansprechen.

Eine Richtlinienzuweisung wird mithilfe von JSON erstellt. Die Richtlinienzuweisung enthält Elemente für Folgendes:

- Anzeigename
- description
- metadata
- Erzwingungsmodus
- Ausgeschlossene Bereiche
- Richtliniendefinition
- Meldungen zu Complianceverstößen
- parameters

Der folgende JSON-Code zeigt z. B. eine Richtlinienzuweisung im _DoNotEnforce_-Modus mit dynamischen Parametern an:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Alle Azure Policy-Beispiele sind unter [Azure Policy-Beispiele](../samples/index.md) verfügbar.

## <a name="display-name-and-description"></a>Anzeigename und Beschreibung

Sie verwenden **displayName** und **description**, um die Richtlinienzuweisung zu identifizieren und den Kontext für ihre Verwendung mit einem bestimmten Satz von Ressourcen bereitzustellen. **displayName** hat eine maximale Länge von _128_ Zeichen, und **description** hat eine maximale Länge von _512_ Zeichen.

## <a name="enforcement-mode"></a>Erzwingungsmodus

Mit der **enforcementMode**-Eigenschaft können Kunden das Ergebnis einer Richtlinie für vorhandene Ressourcen testen, ohne dass die Richtlinienauswirkung initiiert oder Einträge im [Azure-Aktivitätsprotokoll](../../../azure-monitor/platform/platform-logs-overview.md) ausgelöst werden. Dieses Szenario wird allgemein als „Was-wäre-wenn“ bezeichnet und richtet sich nach sicheren Bereitstellungsverfahren. **enforcementMode** unterscheidet sich vom Effekt [Disabled](./effects.md#disabled), da dieser Effekt die Ressourcenauswertung gänzlich verhindert.

Diese Eigenschaft weist die folgenden Werte auf:

|Mode |JSON-Wert |type |Manuelle Behebung |Aktivitätsprotokolleintrag |BESCHREIBUNG |
|-|-|-|-|-|-|
|Aktiviert |Standard |Zeichenfolge |Ja |Ja |Die Richtlinienauswirkung wird während der Erstellung oder Aktualisierung von Ressourcen erzwungen. |
|Disabled |DoNotEnforce |Zeichenfolge |Ja |Nein | Die Richtlinienauswirkung wird nicht während der Erstellung oder Aktualisierung von Ressourcen erzwungen. |

Wenn **enforcementMode** nicht in einer Richtlinie oder Initiativendefinition angegeben ist, wird der Wert _Default_ verwendet. [Aufgaben zur Bereinigung](../how-to/remediate-resources.md) können für [deployIfNotExists](./effects.md#deployifnotexists)-Richtlinien gestartet werden, auch wenn **enforcementMode** auf _DoNotEnforce_ festgelegt ist.

## <a name="excluded-scopes"></a>Ausgeschlossene Bereiche

Der **scope** der Zuweisung umfasst alle untergeordneten Ressourcencontainer und untergeordneten Ressourcen. Wenn die Definition für untergeordnete Ressourcencontainer oder untergeordnete Ressourcen nicht angewendet werden soll, können diese durch Festlegung von **notScopes** von der Auswertung _ausgeschlossen_ werden. Diese Eigenschaft ist ein Array, mit dem ein oder mehrerer Ressourcencontainer oder Ressourcen von der Auswertung ausgeschlossen werden können. **notScopes** können nach dem Erstellen der anfänglichen Zuweisung hinzugefügt oder aktualisiert werden.

> [!NOTE]
> Eine _ausgeschlossene_ Ressource unterscheidet sich von einer _ausgenommenen_ Ressource. Weitere Informationen finden Sie in der [Übersicht zu Bereichen in Azure Policy](./scope.md).

## <a name="policy-definition-id"></a>Richtliniendefinitions-ID

Dieses Feld muss der vollständige Pfadname einer Richtlinien- oder einer Initiativendefinition sein.
`policyDefinitionId` ist eine Zeichenfolge und kein Array. Es wird empfohlen, stattdessen eine [Initiative](./initiative-definition-structure.md) zu verwenden, wenn häufig mehrere Richtlinien gleichzeitig zugewiesen werden.

## <a name="non-compliance-messages"></a>Meldungen zu Complianceverstößen

Legen Sie `nonComplianceMessages` in der Zuweisungsdefinition fest, um eine benutzerdefinierte Meldung festzulegen, in der beschrieben wird, weshalb eine Ressource nicht der Richtlinien- oder Initiativendefinition entspricht. Dieser Knoten ist ein Array von `message`-Einträgen. Diese benutzerdefinierte Meldung wird zusätzlich zur Standardfehlermeldung für Nichtkonformität verwendet und ist optional.

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Wenn die Zuweisung für eine Initiative erfolgt, können für jede Richtliniendefinition in der Initiative verschiedene Meldungen konfiguriert werden. Für die Meldungen wird der `policyDefinitionReferenceId`-Wert verwendet, der in der Initiativendefinition konfiguriert wurde. Einzelheiten finden Sie in den [Definitionseigenschaften](./initiative-definition-structure.md#policy-definition-properties).

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>Parameter

Dieses Segment der Richtlinienzuweisung stellt die Werte für die Parameter bereit, die in der [Richtliniendefinition oder Initiativendefinition](./definition-structure.md#parameters) definiert sind. Dieses Design ermöglicht es, eine Richtlinien- oder Initiativendefinition mit unterschiedlichen Ressourcen wiederzuverwenden, aber auf unterschiedliche Geschäftswerte oder -ergebnisse zu prüfen.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

In diesem Beispiel sind die Parameter, die zuvor in der Richtliniendefinition definiert wurden, `prefix` und `suffix`. Diese spezielle Richtlinienzuweisung legt `prefix` auf **DeptA** und `suffix` auf **-LC** fest. Dieselbe Richtliniendefinition kann mit einem anderen Satz von Parametern für eine andere Abteilung wiederverwendet werden, wodurch die Duplizierung und Komplexität von Richtliniendefinitionen reduziert und gleichzeitig Flexibilität bereitgestellt wird.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Struktur von Richtliniendefinitionen](./definition-structure.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](../how-to/programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](../how-to/get-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).