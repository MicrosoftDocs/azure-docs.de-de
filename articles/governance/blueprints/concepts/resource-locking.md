---
title: Grundlagen von Ressourcensperren
description: Es wird beschrieben, wie Sie die Optionen für das Sperren verwenden, um beim Zuweisen einer Blaupause die Ressourcen zu schützen.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2e281896d45ada8010f24a1f18265a8cdd523d31
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696983"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Grundlegendes zur Ressourcensperre in Azure Blueprint

Die bedarfsabhängige Erstellung konsistenter Umgebungen hat nur dann wirklich Vorteile, wenn ein Mechanismus vorhanden ist, mit dem die Wahrung der Konsistenz sichergestellt werden kann. In diesem Artikel wird beschrieben, wie das Sperren von Ressourcen in Azure Blueprint funktioniert.

## <a name="locking-modes-and-states"></a>Modi und Zustände von Sperren

Der Sperrmodus gilt für die Blaupausenzuweisung und verfügt über drei Optionen: **Nicht sperren**, **Schreibgeschützt** oder **Nicht löschen**. Der Sperrmodus wird konfiguriert, wenn während der Blaupausenzuweisung Artefakte bereitgestellt werden. Um einen anderen Sperrmodus festzulegen, aktualisieren Sie die Blaupausenzuweisung.
Außerhalb von Blaupausen können Sperrmodi jedoch nicht geändert werden.

Für Ressourcen, die von Artefakten in einer Blaupausenzuweisung erstellt wurden, gibt es vier Zustände: **Nicht gesperrt**, **Schreibgeschützt**, **Bearbeiten/Löschen nicht möglich** oder **Löschen nicht möglich**. Jeder Artefakttyp kann sich im Zustand **Nicht gesperrt** befinden. Mithilfe der folgende Tabelle kann der Zustand einer Ressource bestimmt werden:

|Mode|Artefaktressourcentyp|Zustand|BESCHREIBUNG|
|-|-|-|-|
|Nicht sperren|*|Nicht gesperrt|Ressourcen werden nicht durch Blaupausen geschützt. Dieser Zustand wird auch für Ressourcen verwendet, die einem Ressourcengruppenartefakt mit dem Zustand **Schreibgeschützt** oder **Nicht löschen** außerhalb einer Blaupausenzuweisung hinzugefügt wurden.|
|Nur Leseberechtigung|Ressourcengruppe|Bearbeiten/Löschen nicht möglich|Die Ressourcengruppe ist schreibgeschützt, und Tags der Ressourcengruppe können nicht geändert werden. Ressourcen mit dem Zustand **Nicht gesperrt** können dieser Ressourcengruppe hinzugefügt bzw. darin verschoben, geändert oder gelöscht werden.|
|Nur Leseberechtigung|Keine Ressourcengruppe|Nur Leseberechtigung|Die Ressource kann in keiner Weise geändert oder gelöscht werden.|
|Nicht löschen|*|Löschen nicht möglich|Die Ressourcen können geändert, aber nicht gelöscht werden. Ressourcen mit dem Zustand **Nicht gesperrt** können dieser Ressourcengruppe hinzugefügt bzw. darin verschoben, geändert oder gelöscht werden.|

## <a name="overriding-locking-states"></a>Außerkraftsetzen von Zuständen

In der Regel kann es Benutzern mit entsprechender [rollenbasierter Zugriffssteuerung](../../../role-based-access-control/overview.md) für das Abonnement (also beispielsweise mit der Rolle „Besitzer“) gestattet werden, Änderungs- oder Löschvorgänge für beliebige Ressourcen auszuführen. Dies ist jedoch nicht der Fall, wenn Blueprints im Rahmen einer Zuweisungsbereitstellung eine Sperre anwendet. Wenn die Zuweisung mit der Option **Schreibgeschützt** oder **Nicht löschen** festgelegt wurde, ist selbst der Besitzer des Abonnements nicht in der Lage, die blockierte Aktion für die geschützte Ressource auszuführen.

Diese Sicherheitsmaßnahme schützt die Konsistenz der definierten Blaupause und die Umgebung, die damit erstellt werden soll, vor versehentlichen oder programmgesteuerten Lösch- oder Änderungsvorgängen.

## <a name="removing-locking-states"></a>Entfernen von Sperrzuständen

Falls es erforderlich wird, eine durch eine Zuweisung geschützte Ressource zu ändern oder zu löschen, gibt es zwei Möglichkeiten.

- Aktualisieren der Blaupausenzuweisung auf den Sperrmodus **Nicht sperren**
- Löschen der Blaupausenzuweisung

Wenn die Zuweisung entfernt wird, werden auch die von Blueprint erstellten Sperren entfernt. Die Ressource bleibt jedoch zurück und muss auf herkömmliche Weise gelöscht werden.

## <a name="how-blueprint-locks-work"></a>Funktionsweise von Blaupausensperren

Eine Ablehnungsaktion im Rahmen von RBAC-[Ablehnungszuweisungen](../../../role-based-access-control/deny-assignments.md) wird während der Zuweisung einer Blaupause auf Artefaktressourcen angewendet, wenn für die Zuweisung die Option **Schreibgeschützt** oder **Nicht löschen** ausgewählt wurde. Die Ablehnungsaktion wird von der verwalteten Identität der Blaupausenzuweisung hinzugefügt und kann nur von derselben verwalteten Identität aus den Artefaktressourcen entfernt werden. Diese Sicherheitsmaßnahme erzwingt den Sperrmechanismus und verhindert die Aufhebung der Blaupausensperre außerhalb von Blueprints.

> [!IMPORTANT]
> Azure Resource Manager speichert Details zu Rollenzuweisungen bis zu 30 Minuten lang zwischen. Daher sind Ablehnungsaktionen von Ablehnungszuweisungen für Blaupausenressourcen möglicherweise nicht sofort in vollem Umfang wirksam. Während dieser Zeit ist es ggf. möglich, eine Ressource zu löschen, die eigentlich durch Blaupausensperren geschützt werden sollte.

## Exclude a principal from a deny assignment

In manchen Design, oder Security Szenarien kann es notwendig sein bestimmte Accounts von [Ablehnungszuweisungen](../../../role-based-access-control/deny-assignments.md) auszunehmen, die vom Blueprint erstellt werden. Das kann über die REST API realisiert werden, indem man bis zu fünf Werte zum **excludedPrincipals** Array im **locks** Wert hinterlegt, während man das [Assignment erstellt](/rest/api/blueprints/assignments/createorupdate).
Das ist ein Beipiel eines Request Bodies, dass die **excludedPrincipals** enthält:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](sequencing-order.md) anpassen können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).
- Beheben Sie Probleme bei der Blaupausenzuweisung mithilfe des [allgemeinen Leitfadens zur Problembehandlung](../troubleshoot/general.md).
