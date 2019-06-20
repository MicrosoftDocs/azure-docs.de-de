---
title: Aktualisieren einer vorhandenen Zuweisung über das Portal
description: Hier werden die Mechanismen beim Aktualisieren einer vorhandenen Zuweisung in Azure Blueprints über das Portal erläutert.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c75bd8c3831bad0c8217f16315843cbe3824fe4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "63766589"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Aktualisieren einer vorhandenen Blaupausenzuweisung

Wenn eine Blaupause zugewiesen ist, kann die Zuweisung aktualisiert werden. Es gibt verschiedene Gründe für das Aktualisieren einer vorhandenen Zuweisung:

- Hinzufügen oder Entfernen einer [Ressourcensperre](../concepts/resource-locking.md)
- Ändern des Wertes von [dynamischen Parametern](../concepts/parameters.md#dynamic-parameters)
- Aktualisieren der Zuweisung auf eine neuere **veröffentlichte** Version der Blaupause

## <a name="updating-assignments"></a>Aktualisieren von Zuweisungen

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie auf der Seite links die Option **Zugewiesene Blaupausen**.

1. Klicken Sie in der Liste der Blaupausen auf die Blaupausenzuweisung. Klicken Sie anschließend auf die Schaltfläche **Zuweisung aktualisieren**, ODER klicken Sie mit der rechten Maustaste auf die Blaupausenzuweisung, und wählen Sie **Zuweisung aktualisieren** aus.

   ![Aktualisieren einer vorhandenen Blaupausenzuweisung](../media/update-existing-assignments/update-assignment.png)

1. Die Seite **Blaupause zuweisen** wird geladen, und alle Werte der ursprünglichen Zuweisung sind bereits eingetragen. Sie können die **Version der Blaupausendefinition**, den Zustand von **Zuweisung sperren** sowie die dynamischen Parameter für die Blaupausendefinition ändern. Klicken Sie nach dem Vornehmen der Änderungen auf **Zuweisen**.

1. Überprüfen Sie auf der Detailseite für die aktualisierte Zuweisung den neuen Status. In diesem Beispiel wurde der Zuweisung **Wird gesperrt** hinzugefügt.

   ![Vorhandene Blaupausenzuweisung nach der Aktualisierung – geänderter Sperrmodus](../media/update-existing-assignments/updated-assignment.png)

1. Einzelheiten zu anderen **Zuweisungsvorgängen** finden Sie in der Dropdownliste. Die Tabelle **Verwaltete Ressourcen** wird durch den ausgewählten Zuweisungsvorgang aktualisiert.

   ![Zuweisungsvorgänge einer Blaupausenzuweisung](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regeln für das Aktualisieren von Zuweisungen

Bei der Bereitstellung der aktualisierten Zuweisungen werden einige wichtige Regeln befolgt. Diese Regeln legen fest, was mit bereits bereitgestellten Ressourcen geschieht. Die angeforderte Änderung und der Typ der bereitgestellten oder aktualisierten Artefaktressource bestimmen, welche Maßnahmen ergriffen werden.

- Rollenzuweisungen
  - Wird die Rolle oder der Rollenempfänger (Benutzer, Gruppe oder App) geändert, wird eine neue Rollenzuweisung erstellt. Zuvor bereitgestellte Rollenzuweisungen werden beibehalten.
- Richtlinienzuweisungen
  - Werden die Parameter der Richtlinienzuweisung geändert, wird die vorhandene Zuweisung aktualisiert.
  - Wird die Definition der Richtlinienzuweisung geändert, wird eine neue Richtlinienzuweisung erstellt. Zuvor bereitgestellte Richtlinienzuweisungen werden beibehalten.
  - Wird das Artefakt der Richtlinienzuweisung aus der Blaupause entfernt, werden bereitgestellte Richtlinienzuweisung beibehalten.
- Azure-Ressourcen-Manager-Vorlagen
  - Die Vorlage wird über den Ressourcen-Manager als **PUT** verarbeitet. Da diese Aktion für jeden Ressourcentyp anders behandelt wird, sehen Sie sich die Dokumentation der einzelnen eingeschlossenen Ressourcen an, um die Auswirkung dieser von Blaupausen ausgeführten Aktion zu bestimmen.

## <a name="possible-errors-on-updating-assignments"></a>Mögliche Fehler beim Aktualisieren von Zuweisungen

Beim Aktualisieren von Zuweisungen können Änderungen vorgenommen werden, die bei der Ausführung eine Unterbrechung bewirken. Ein Beispiel hierfür ist das Ändern des Speicherorts einer Ressourcengruppe, nachdem diese bereits bereitgestellt wurde. Jede von [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) unterstützte Änderung kann vorgenommen werden. Jede Änderung, die bei Azure Resource Manager einen Fehler verursacht, bewirkt jedoch ebenso das Fehlschlagen der Zuweisung.

Es gibt keine Beschränkung in Bezug darauf, wie oft eine Zuweisung aktualisiert werden kann. Wenn ein Fehler auftritt, identifizieren Sie ihn, und aktualisieren Sie die Zuweisung erneut.  Beispielszenarien für Fehler:

- Falscher Parameter
- Ein bereits vorhandenes Objekt
- Eine Änderung, die von Azure Resource Manager nicht unterstützt wird

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../concepts/resource-locking.md) können.
- Beheben Sie Probleme bei der Blaupausenzuweisung mithilfe des [allgemeinen Leitfadens zur Problembehandlung](../troubleshoot/general.md).