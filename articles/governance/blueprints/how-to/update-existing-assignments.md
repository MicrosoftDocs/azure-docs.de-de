---
title: Aktualisieren einer vorhandenen Zuweisung über das Portal
description: Hier werden die Mechanismen beim Aktualisieren einer vorhandenen Blaupausenzuweisung in Azure Blueprints über das Portal erläutert.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: ddaac8110885d817bc96745965c1af7e522b1919
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007187"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Aktualisieren einer vorhandenen Blaupausenzuweisung

Wenn eine Blaupause zugewiesen ist, kann die Zuweisung aktualisiert werden. Es gibt verschiedene Gründe für das Aktualisieren einer vorhandenen Zuweisung:

- Hinzufügen oder Entfernen einer [Ressourcensperre](../concepts/resource-locking.md)
- Ändern des Wertes von [dynamischen Parametern](../concepts/parameters.md#dynamic-parameters)
- Durchführen eines Upgrades auf eine neuere Blaupausenversion mit dem Status **Veröffentlicht**

## <a name="updating-assignments"></a>Aktualisieren von Zuweisungen

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie auf der Seite links die Option **Zugewiesene Blaupausen**.

1. Wählen Sie in der Liste der Blaupausen die Blaupausenzuweisung aus. Verwenden Sie anschließend die Schaltfläche **Zuweisung aktualisieren**, ODER klicken Sie mit der rechten Maustaste auf die Blaupausenzuweisung, und wählen Sie **Zuweisung aktualisieren** aus.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Screenshot der Seite für Blaupausenzuweisung mit hervorgehobener Schaltfläche „Zuweisung aktualisieren“" border="false":::

1. Die Seite **Blaupause zuweisen** wird geladen, während alle Werte der ursprünglichen Zuweisung bereits eingetragen sind. Sie können die **Version der Blaupausendefinition**, den Zustand von **Zuweisung sperren** sowie die dynamischen Parameter für die Blaupausendefinition ändern. Wählen Sie **Zuweisen** aus, nachdem die Änderungen abgeschlossen sind.

1. Überprüfen Sie auf der Detailseite für die aktualisierte Zuweisung den neuen Status. In diesem Beispiel wurde der Zuweisung **Wird gesperrt** hinzugefügt.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Screenshot einer aktualisierten Blaupausenzuweisung mit geändertem Sperrmodus" border="false":::

1. Einzelheiten zu anderen **Zuweisungsvorgängen** finden Sie in der Dropdownliste. Die Tabelle **Verwaltete Ressourcen** wird durch den ausgewählten Zuweisungsvorgang aktualisiert.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Screenshot einer aktualisierten Blaupausenzuweisung mit Zuweisungsvorgängen und deren Status" border="false":::

## <a name="rules-for-updating-assignments"></a>Regeln für das Aktualisieren von Zuweisungen

Bei der Bereitstellung der aktualisierten Zuweisungen werden einige wichtige Regeln befolgt. Diese Regeln legen fest, was mit bereits bereitgestellten Ressourcen geschieht. Die angeforderte Änderung und der Typ der bereitgestellten oder aktualisierten Artefaktressource bestimmen, welche Maßnahmen ergriffen werden.

- Rollenzuweisungen
  - Wird die Rolle oder der Rollenempfänger (Benutzer, Gruppe oder App) geändert, wird eine neue Rollenzuweisung erstellt. Zuvor bereitgestellte Rollenzuweisungen werden beibehalten.
- Richtlinienzuweisungen
  - Werden die Parameter der Richtlinienzuweisung geändert, wird die vorhandene Zuweisung aktualisiert.
  - Wird die Definition der Richtlinienzuweisung geändert, wird eine neue Richtlinienzuweisung erstellt.
    Zuvor bereitgestellte Richtlinienzuweisungen werden beibehalten.
  - Wird das Artefakt der Richtlinienzuweisung aus der Blaupause entfernt, werden bereitgestellte Richtlinienzuweisung beibehalten.
- Azure Resource Manager-Vorlagen (ARM-Vorlagen)
  - Die Vorlage wird über den Ressourcen-Manager als **PUT** verarbeitet. Da diese Aktion für jeden Ressourcentyp anders behandelt wird, sehen Sie sich die Dokumentation der einzelnen eingeschlossenen Ressourcen an, um die Auswirkung dieser von Blaupausen ausgeführten Aktion zu bestimmen.

## <a name="possible-errors-on-updating-assignments"></a>Mögliche Fehler beim Aktualisieren von Zuweisungen

Beim Aktualisieren von Zuweisungen können Änderungen vorgenommen werden, die bei der Ausführung eine Unterbrechung bewirken. Ein Beispiel hierfür ist das Ändern des Speicherorts einer Ressourcengruppe, nachdem diese bereits bereitgestellt wurde. Jede von [Resource Manager](../../../azure-resource-manager/management/overview.md) unterstützte Änderung kann vorgenommen werden. Jede Änderung, die bei Resource Manager einen Fehler verursacht, bewirkt jedoch ebenso einen Fehler bei der Zuweisung.

Es gibt keine Beschränkung in Bezug darauf, wie oft eine Zuweisung aktualisiert werden kann. Wenn ein Fehler auftritt, identifizieren Sie ihn, und aktualisieren Sie die Zuweisung erneut.  Beispielszenarien für Fehler:

- Falscher Parameter
- Ein bereits vorhandenes Objekt
- Eine von Resource Manager nicht unterstützte Änderung

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../concepts/resource-locking.md) können.
- Beheben Sie Probleme bei der Blaupausenzuweisung mithilfe des [allgemeinen Leitfadens zur Problembehandlung](../troubleshoot/general.md).