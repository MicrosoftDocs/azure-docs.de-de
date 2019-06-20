---
title: Funktionsweise von Auswirkungen
description: Die Azure Policy-Definition hat verschiedene Auswirkungen, mit denen festgelegt wird, wie die Konformität verwaltet und gemeldet wird.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 6ad6f9414df17f9edff7565752ef3845e0d3c88e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116202"
---
# <a name="understand-azure-policy-effects"></a>Grundlegendes zu Azure Policy-Auswirkungen

Jede Richtliniendefinition in Azure Policy hat eine einzelne Auswirkung. Diese Auswirkung bestimmt, was geschieht, wenn die Richtlinienregel auf eine Übereinstimmung ausgewertet wird. Die Auswirkungen für eine neue Ressource, eine aktualisierte Ressource oder eine vorhandene Ressource sind hierbei unterschiedlich.

Aktuell werden in einer Richtliniendefinition sechs Auswirkungen unterstützt:

- Anfügen
- Audit
- Auswirkung „AuditIfNotExists“
- Verweigern
- Auswirkung „DeployIfNotExists“
- Deaktiviert

## <a name="order-of-evaluation"></a>Reihenfolge der Auswertung

Anforderungen zum Erstellen oder Aktualisieren einer Ressource über Azure Resource Manager werden von Azure Policy zuerst ausgewertet. Azure Policy erstellt eine Liste aller Zuweisungen, die auf die Ressource zutreffen, und wertet dann die Ressource anhand jeder Definition aus. Azure Policy verarbeitet einige der Auswirkungen, bevor die Anforderung an den geeigneten Ressourcenanbieter übergeben wird. Auf diese Weise wird eine unnötige Verarbeitung durch einen Ressourcenanbieter verhindert, wenn eine Ressource nicht den konfigurierten Governancevorgaben von Azure Policy entspricht.

- Zuerst wird **Deaktiviert** überprüft, um zu ermitteln, ob die Richtlinienregel ausgewertet werden soll.
- Anschließend wird **Anfügen** ausgewertet. Die Anforderung kann durch „append“ geändert werden, deshalb kann eine über „append“ durchgeführte Änderung die Auslösung der Auswirkungen „audit“ oder „deny“ verhindern.
- Anschließend wird **deny** ausgewertet. Durch die Auswertung von „deny“ vor „audit“ wird eine zweimalige Protokollierung einer unerwünschten Ressource verhindert.
- Anschließend wird **audit** ausgewertet, bevor die Anforderung an den Ressourcenanbieter weitergeleitet wird.

Nachdem der Ressourcenanbieter einen Erfolgscode zurückgegeben hat, werden **AuditIfNotExists** und **DeployIfNotExists** ausgewertet, um zu bestimmen, ob eine zusätzliche Konformitätsprotokollierung oder -aktion erforderlich ist.

## <a name="disabled"></a>Deaktiviert

Diese Auswirkung ist in Testsituationen oder nach dem Parametrisieren der Auswirkung durch die Richtliniendefinition hilfreich. Aufgrund dieser Flexibilität kann eine einzelne Zuweisung deaktiviert werden, statt alle Zuweisungen dieser Richtlinie zu deaktivieren.

## <a name="append"></a>Anfügen

„append“ wird verwendet, um der angeforderten Ressource während der Erstellung oder Aktualisierung zusätzliche Felder hinzuzufügen. Ein gängiges Beispiel ist das Hinzufügen von Tags zu Ressourcen (z.B. „costCenter“) oder das Angeben zugelassener IP-Adressen für eine Speicherressource.

### <a name="append-evaluation"></a>Auswertung von „append“

Die Auswertung von „append“ erfolgt vor der Anforderungsverarbeitung durch einen Ressourcenanbieter während der Erstellung oder Aktualisierung einer Ressource. Mit „append“ werden Felder zur Ressource hinzugefügt, wenn die **if**-Bedingung der Richtlinienregel erfüllt ist. Wenn die Auswirkung „append“ einen Wert in der ursprünglichen Anforderung mit einem anderen Wert überschreiben würde, fungiert sie als Auswirkung „deny“ und lehnt die Anforderung ab. Um einen neuen Wert an ein vorhandenes Array anzufügen, verwenden Sie die **[\*]** -Version des Alias.

Wenn eine Richtliniendefinition mit Auswirkung „append“ im Rahmen eines Auswertungszyklus ausgeführt wird, werden keine Änderungen an bereits vorhandenen Ressourcen durchgeführt. Stattdessen werden alle Ressourcen mit Erfüllung der **if**-Bedingung als nicht konform markiert.

### <a name="append-properties"></a>Eigenschaften von „append“

Die Auswirkung „append“ weist nur ein Array **details** auf, das erforderlich ist. Da es sich bei **details** um ein Array handelt, kann es entweder ein einzelnes **field/value**-Paar oder mehrere Wertepaare verwenden. Eine Liste der gültigen Felder finden Sie unter [Struktur von Azure Policy-Definitionen](definition-structure.md#fields).

### <a name="append-examples"></a>Beispiele für „append“

Beispiel 1: Einzelnes **field/value**-Paar zum Anfügen eines Tags.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Beispiel 2: Zwei **field/value**-Paare zum Anfügen einer Gruppe von Tags.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

Beispiel 3: Einzelnes **field/value**-Paar, das einen Nicht- **[\*]** -[Alias](definition-structure.md#aliases) mit einem Arraywert (**value**) verwendet, um IP-Regeln für ein Speicherkonto festzulegen. Wenn der Nicht- **[\*]** -Alias ein Array ist, wird der **value** durch den Effekt als gesamtes Array angefügt. Wenn das Array bereits vorhanden ist, wird durch den Konflikt ein deny-Ereignis ausgelöst.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Beispiel 4: Einzelnes **field/value**-Paar, das einen **[\*]** [-Alias](definition-structure.md#aliases) mit einem Array-**value** verwendet, um IP-Regeln für ein Speicherkonto festzulegen. Durch die Verwendung des **[\*]** -Alias fügt der Effekt den **value** an ein Array an, das möglicherweise bereits vorhanden ist. Wenn das Array noch nicht vorhanden ist, wird es erstellt.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="deny"></a>Verweigern

Mit „deny“ werden Ressourcenanforderungen abgelehnt, welche die über eine Richtliniendefinition festgelegten Standards nicht erfüllen. Für die Anforderung wird anschließend ein Fehler ausgegeben.

### <a name="deny-evaluation"></a>Auswertung von „deny“

Beim Erstellen oder Aktualisieren einer übereinstimmenden Ressource verhindert „deny“, dass die Anforderung an den Ressourcenanbieter gesendet wird. Für die Anforderung wird `403 (Forbidden)` zurückgegeben. Im Portal kann „Verboten“ als Status für die Bereitstellung angezeigt werden, die durch die Richtlinienzuweisung verhindert wurde.

Während der Auswertung vorhandener Ressourcen werden Ressourcen, die einer „deny“-Richtliniendefinition entsprechen, als nicht konform markiert.

### <a name="deny-properties"></a>Eigenschaften von „deny“

Die Auswirkung „deny“ umfasst keine zusätzlichen Eigenschaften zur Verwendung in der **then**-Bedingung der Richtliniendefinition.

### <a name="deny-example"></a>Beispiel für „deny“

Beispiel: Verwendung der Auswirkung „deny“

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Audit

Die Auswirkung „audit“ wird verwendet, um ein Warnungsereignis im Aktivitätsprotokoll zu erstellen, wenn eine nicht konforme Ressource ausgewertet wird. Die Anforderung wird jedoch nicht beendet.

### <a name="audit-evaluation"></a>Auswertung von „audit“

„audit“ ist die letzte Auswirkung, die von Azure Policy während der Erstellung oder Aktualisierung einer Ressource überprüft wird. Azure Policy sendet dann die Ressource an den Ressourcenanbieter. „audit“ funktioniert bei einer Ressourcenanforderung und einem Auswertungszyklus auf gleiche Weise. Azure Policy fügt dem Aktivitätsprotokoll einen `Microsoft.Authorization/policies/audit/action`-Vorgang hinzu und markiert die Ressource als nicht konform.

### <a name="audit-properties"></a>Eigenschaften von „audit“

Die Auswirkung „audit“ umfasst keine zusätzlichen Eigenschaften zur Verwendung in der **then**-Bedingung der Richtliniendefinition.

### <a name="audit-example"></a>Beispiel für „audit“

Beispiel: Verwendung der Auswirkung „audit“

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>Auswirkung „AuditIfNotExists“

Die Auswirkung „AuditIfNotExists“ ermöglicht das Überwachen von Ressourcen, welche die **if**-Bedingung erfüllen, für die aber keine Komponenten in **details** der **then** -Bedingung angegeben sind.

### <a name="auditifnotexists-evaluation"></a>Auswertung von „AuditIfNotExists“

„AuditIfNotExists“ wird ausgeführt, nachdem ein Ressourcenanbieter eine Anforderung zum Erstellen oder Aktualisieren einer Ressource verarbeitet hat und ein Erfolgsstatuscode zurückgegeben wurde. Die Überprüfung findet statt, wenn keine entsprechenden Ressourcen vorhanden sind oder wenn die über **ExistenceCondition** definierten Ressourcen nicht als TRUE ausgewertet werden. Azure Policy fügt dem Aktivitätsprotokoll auf gleiche Weise wie bei der Auswirkung „audit“ einen `Microsoft.Authorization/policies/audit/action`-Vorgang hinzu. Bei Auslösung ist die Ressource, die die **if**-Bedingung erfüllt hat, die Ressource, die als nicht konform markiert wird.

### <a name="auditifnotexists-properties"></a>Eigenschaften von „AuditIfNotExists“

Die **details**-Eigenschaft der Auswirkung „AuditIfNotExists“ umfasst die folgenden Untereigenschaften zur Definition der entsprechenden Ressourcen für den Abgleich.

- **Type** [erforderlich]
  - Gibt den Typ der entsprechenden abzugleichenden Ressource an.
  - Wenn es sich bei **details.type** um einen Ressourcentyp unterhalb der Bedingungsressource **if** handelt, fragt die Richtlinie Ressourcen dieses **Typs** innerhalb des Bereichs der ausgewerteten Ressource ab. Andernfalls erfolgen Abfragen der Richtlinie innerhalb der gleichen Ressourcengruppe wie die ausgewertete Ressource.
- **Name** (optional)
  - Gibt den exakten Namen der Ressource für den Abgleich an und führt dazu, dass die Richtlinie nicht alle Ressourcen des angegebenen Typs, sondern eine bestimmte Ressource abruft.
  - Wenn die Werte der Bedingung für **if.field.type** und **then.details.type** übereinstimmen, wird **Name** _erforderlich_ und muss `[field('name')]` sein. Allerdings sollte stattdessen ein [audit](#audit)-Effekt in Erwägung gezogen werden.
- **ResourceGroupName** (optional)
  - Hierdurch ist es möglich, für den Abgleich eine Ressource aus einer anderen Ressourcengruppe festzulegen.
  - Diese Einstellung ist nicht anwendbar, wenn **type** eine Ressource unterhalb der **if**-Bedingungsressource angibt.
  - Standardmäßig wird die Ressourcengruppe der **if**-Bedingungsressource verwendet.
- **ExistenceScope** (optional)
  - Zulässige Werte sind _Subscription_ und _ResourceGroup_.
  - Legt den Bereich fest, aus dem die entsprechende Ressource für den Abgleich abgerufen werden soll.
  - Diese Einstellung ist nicht anwendbar, wenn **type** eine Ressource unterhalb der **if**-Bedingungsressource angibt.
  - Für _ResourceGroup_ bedeutet dies eine Beschränkung auf die Ressourcengruppe der **if** -Bedingungsressource oder die in **ResourceGroupName** angegebene Ressourcengruppe.
  - Für _Subscription_ wird das gesamte Abonnement nach der entsprechenden Ressource abgefragt.
  - Die Standardeinstellung ist _ResourceGroup_.
- **ExistenceCondition** (optional)
  - Sofern nicht angegeben, erfüllt jede Ressource mit entsprechendem **type** die Bedingung der Auswirkung und löst keine Überwachung aus.
  - Verwendet dieselbe Sprachsyntax wie die Richtlinienregel für die **if**-Bedingung, wird jedoch für jede entsprechende Ressource einzeln ausgeführt.
  - Wenn eine übereinstimmende Ressource als TRUE ausgewertet wird, ist die Auswirkung erfüllt und löst keine Überwachung aus.
  - [field()] kann verwendet werden, um auf Äquivalenz mit Werten in der **if**-Bedingung zu überprüfen.
  - Beispielsweise könnte so überprüft werden, ob die übergeordnete Ressource (in der **if**-Bedingung) sich am selben Ressourcenstandort befindet wie die übereinstimmende Ressource.

### <a name="auditifnotexists-example"></a>Beispiel für „AuditIfNotExists“

Beispiel: Mithilfe einer Auswertung wird ermittelt, ob die Antischadsoftware-Erweiterung auf virtuellen Computern vorhanden ist. Fehlt die Erweiterung, wird eine Überwachung ausgelöst.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>Auswirkung „DeployIfNotExists“

Ähnlich wie „AuditIfNotExists“ führt „DeployIfNotExists“ eine Vorlagenbereitstellung durch, wenn die Bedingung erfüllt ist.

> [!NOTE]
> [Geschachtelte Vorlagen](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) werden mit **deployIfNotExists** unterstützt, [verknüpfte Vorlagen](../../../azure-resource-manager/resource-group-linked-templates.md) werden derzeit jedoch nicht unterstützt.

### <a name="deployifnotexists-evaluation"></a>Auswertung von „DeployIfNotExists“

„DeployIfNotExists“ wird ausgeführt, nachdem ein Ressourcenanbieter eine Anforderung zum Erstellen oder Aktualisieren einer Ressource verarbeitet hat und ein Erfolgsstatuscode zurückgegeben wurde. Eine Vorlagenbereitstellung findet statt, wenn keine entsprechenden Ressourcen vorhanden sind oder wenn die über **ExistenceCondition** definierten Ressourcen nicht als TRUE ausgewertet werden.

Während eines Auswertungszyklus führen Richtliniendefinitionen mit der Auswirkung „DeployIfNotExists“ dazu, dass übereinstimmende Ressourcen als nicht konform markiert werden. Es wird aber keine Aktion für diese Ressource ausgeführt.

### <a name="deployifnotexists-properties"></a>Eigenschaften von „DeployIfNotExists“

Die **details**-Eigenschaft der Auswirkung „AuditIfNotExists“ umfasst die folgenden Untereigenschaften zur Definition der entsprechenden Ressourcen für den Abgleich.

- **Type** [erforderlich]
  - Gibt den Typ der entsprechenden abzugleichenden Ressource an.
  - Zunächst wird versucht, eine Ressource unterhalb der **if**-Bedingungsressource abzurufen, anschließend wird eine Abfrage innerhalb derselben Ressourcengruppe wie der **if**-Bedingungsressource durchgeführt.
- **Name** (optional)
  - Gibt den exakten Namen der Ressource für den Abgleich an und führt dazu, dass die Richtlinie nicht alle Ressourcen des angegebenen Typs, sondern eine bestimmte Ressource abruft.
  - Wenn die Werte der Bedingung für **if.field.type** und **then.details.type** übereinstimmen, wird **Name** _erforderlich_ und muss `[field('name')]` sein.
- **ResourceGroupName** (optional)
  - Hierdurch ist es möglich, für den Abgleich eine Ressource aus einer anderen Ressourcengruppe festzulegen.
  - Diese Einstellung ist nicht anwendbar, wenn **type** eine Ressource unterhalb der **if**-Bedingungsressource angibt.
  - Standardmäßig wird die Ressourcengruppe der **if**-Bedingungsressource verwendet.
  - Wenn eine Vorlagenbereitstellung durchgeführt wird, erfolgt diese in der Ressourcengruppe, die über diesen Wert angegeben wurde.
- **ExistenceScope** (optional)
  - Zulässige Werte sind _Subscription_ und _ResourceGroup_.
  - Legt den Bereich fest, aus dem die entsprechende Ressource für den Abgleich abgerufen werden soll.
  - Diese Einstellung ist nicht anwendbar, wenn **type** eine Ressource unterhalb der **if**-Bedingungsressource angibt.
  - Für _ResourceGroup_ bedeutet dies eine Beschränkung auf die Ressourcengruppe der **if** -Bedingungsressource oder die in **ResourceGroupName** angegebene Ressourcengruppe.
  - Für _Subscription_ wird das gesamte Abonnement nach der entsprechenden Ressource abgefragt.
  - Die Standardeinstellung ist _ResourceGroup_.
- **ExistenceCondition** (optional)
  - Sofern nicht angegeben, erfüllt jede entsprechende Ressource mit **type** die Bedingung der Auswirkung und löst keine Bereitstellung aus.
  - Verwendet dieselbe Sprachsyntax wie die Richtlinienregel für die **if**-Bedingung, wird jedoch für jede entsprechende Ressource einzeln ausgeführt.
  - Wenn eine übereinstimmende Ressource als TRUE ausgewertet wird, ist die Auswirkung erfüllt und löst keine Bereitstellung aus.
  - [field()] kann verwendet werden, um auf Äquivalenz mit Werten in der **if**-Bedingung zu überprüfen.
  - Beispielsweise könnte so überprüft werden, ob die übergeordnete Ressource (in der **if**-Bedingung) sich am selben Ressourcenstandort befindet wie die übereinstimmende Ressource.
- **roleDefinitionIds** [erforderlich]
  - Diese Eigenschaft muss ein Array von Zeichenfolgen enthalten, das mit der Rollen-ID der rollenbasierten Zugriffssteuerung übereinstimmt, auf die das Abonnement zugreifen kann. Weitere Informationen finden Sie unter [Korrigieren nicht konformer Ressourcen](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (optional)
  - Zulässige Werte sind _Subscription_ und _ResourceGroup_.
  - Legt den Typ der auszulösenden Bereitstellung fest. Mit _Subscription_ wird eine [Bereitstellung auf Abonnementebene](../../../azure-resource-manager/deploy-to-subscription.md) und mit _ResourceGroup_ eine Bereitstellung in einer Ressourcengruppe angegeben.
  - Bei der Bereitstellung auf Abonnementebene muss in _Deployment_ eine _location_-Eigenschaft angegeben werden.
  - Die Standardeinstellung ist _ResourceGroup_.
- **Deployment** [erforderlich]
  - Diese Eigenschaft muss die vollständige Vorlagenbereitstellung enthalten, so wie sie an die PUT-API `Microsoft.Resources/deployments` übergeben würde. Weitere Informationen finden Sie im Artikel zur [REST-API für die Bereitstellung](/rest/api/resources/deployments).

  > [!NOTE]
  > Alle Funktionen innerhalb der **Deployment**-Eigenschaft werden als Komponenten der Vorlage – nicht der Richtlinie – ausgewertet. Eine Ausnahme ist die Eigenschaft **parameters**, die Werte von der Richtlinie an die Vorlage übergibt. Der in diesem Abschnitt unter einem Vorlagenparameternamen angegebene Wert für **value** wird verwendet, um diese Wertübergabe durchzuführen (siehe _fullDbName_ im DeployIfNotExists-Beispiel).

### <a name="deployifnotexists-example"></a>Beispiel für „DeployIfNotExists“

Beispiel: Mithilfe einer Auswertung von SQL Server-Datenbanken wird bestimmt, ob „transparentDataEncryption“ aktiviert ist. Falls nicht, wird eine Bereitstellung zur Aktivierung dieser Option durchgeführt.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="layering-policies"></a>Mehrere Richtlinien

Eine Ressource kann durch mehrere Zuweisungen beeinflusst werden. Diese Zuweisungen können für denselben Bereich oder verschiedene Bereiche gelten. Für jede dieser Zuweisungen ist wahrscheinlich auch eine andere Auswirkung definiert. Die Bedingung und die Auswirkung für jede Richtlinie werden unabhängig ausgewertet. Beispiel:

- Richtlinie 1
  - Schränkt den Ressourcenstandort auf „USA, Westen“ ein
  - Abonnement A zugewiesen
  - Auswirkung „deny“
- Richtlinie 2
  - Schränkt den Ressourcenstandort auf „USA, Osten“ ein
  - Ressourcengruppe B im Abonnement A zugewiesen
  - Auswirkung „audit“
  
Dies würde zu folgendem Ergebnis führen:

- Jede Ressource, die sich bereits in Ressourcengruppe B und am Standort „USA, Osten“ befindet, ist gemäß Richtlinie 2 konform und gemäß Richtlinie 1 nicht konform.
- Jede Ressource, die sich bereits in Ressourcengruppe B und nicht am Standort „USA, Osten“ befindet, ist gemäß Richtlinie 2 nicht konform und auch gemäß Richtlinie 1 nicht konform, wenn sie sich nicht am Standort „USA, Westen“ befindet.
- Jede neue Ressource in Abonnement A, die nicht am Standort „USA, Westen“ erstellt wird, wird von Richtlinie 1 abgelehnt.
- Jede neue Ressource in Abonnement A und Ressourcengruppe B am Standort „USA, Westen“ wird erstellt und ist gemäß Richtlinie 2 nicht konform.

Wenn sowohl Richtlinie 1 als auch Richtlinie 2 die Auswirkung „deny“ verwenden, ändert sich die Situation folgendermaßen:

- Jede Ressource, die sich bereits in Ressourcengruppe B und nicht am Standort „USA, Osten“ befindet, ist gemäß Richtlinie 2 nicht konform.
- Jede Ressource, die sich bereits in Ressourcengruppe B und nicht am Standort „USA, Westen“ befindet, ist gemäß Richtlinie 1 nicht konform.
- Jede neue Ressource in Abonnement A, die nicht am Standort „USA, Westen“ erstellt wird, wird von Richtlinie 1 abgelehnt.
- Jede neue Ressource in Ressourcengruppe B von Abonnement A wird abgelehnt.

Jede Zuweisung wird einzeln ausgewertet. Daher ist es nicht möglich, dass eine Ressource aufgrund unterschiedlicher Bereiche „durch das Netz schlüpfen“ kann. Die Anwendung mehrerer oder sich überschneidender Richtlinien führt **kumulativ zur stärksten Einschränkung**. Wenn beispielsweise sowohl Richtlinie 1 als auch Richtlinie 2 die Auswirkung „deny“ verwenden, wird eine Ressource durch sich überschneidende und konfliktverursachende Richtlinien blockiert. Wenn die Ressource dennoch im Zielbereich erstellt werden soll, überprüfen Sie die Ausnahmen für jede Zuweisung, um sicherzustellen, dass die richtigen Richtlinien auf die richtigen Bereiche angewendet werden.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](definition-structure.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](../how-to/programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](../how-to/getting-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).