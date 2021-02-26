---
title: 'Schnellstart: Neue Richtlinienzuweisung über das Portal'
description: In dieser Schnellstartanleitung erstellen Sie über das Azure-Portal eine Azure Policy-Zuweisung zum Identifizieren nicht konformer Ressourcen.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: e5cbf31e897b5be404327efa254eb90ead990f5f
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220886"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen

Zum Verständnis der Konformität in Azure müssen Sie zunächst wissen, wie Sie den Status Ihrer Ressourcen ermitteln.
Diese Schnellstartanleitung führt Sie schrittweise durch die Erstellung einer Richtlinienzuweisung zur Identifizierung von virtuellen Computern, die keine verwalteten Datenträger verwenden.

Am Ende dieses Prozesses können Sie erfolgreich virtuelle Computer identifizieren, die keine verwalteten Datenträger verwenden. Sie sind mit der Richtlinienzuweisung _nicht konform_.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-a-policy-assignment"></a>Erstellen einer Richtlinienzuweisung

In dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung und weisen die Richtliniendefinition _Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden_ zu.

1. Starten Sie den Azure Policy-Dienst über das Azure-Portal, indem Sie die Option **Alle Dienste** auswählen und dann nach **Policy** suchen und die entsprechende Option auswählen.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Screenshot: Suchen nach „Policy“ unter „Alle Dienste“" border="false":::

1. Wählen Sie links auf der Seite „Azure Policy“ die Option **Zuweisungen**. Eine Zuweisung ist eine zugewiesene Richtlinie, die innerhalb eines bestimmten Bereichs angewendet werden soll.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Screenshot: Auswählen der Seite „Zuweisungen“ auf der Übersichtsseite für Richtlinien" border="false":::

1. Wählen Sie im oberen Bereich der Seite **Richtlinien – Zuweisungen** die Option **Richtlinie zuweisen**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Screenshot: Auswählen von „Richtlinie zuweisen“ auf der Seite „Zuweisungen“" border="false":::

1. Legen Sie auf der Seite **Richtlinie zuweisen** den **Bereich** fest, indem Sie die Auslassungspunkte und dann entweder eine Verwaltungsgruppe oder ein Abonnement auswählen. Wählen Sie optional eine Ressourcengruppe aus. Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird. Verwenden Sie anschließend unten auf der Seite **Bereich** die Schaltfläche **Auswählen** aus.

   In diesem Beispiel wird das Abonnement **Contoso** verwendet. Ihr Abonnement wird sich davon unterscheiden.

1. Ressourcen können basierend auf dem **Bereich** ausgeschlossen werden. **Ausschlüsse** beginnen auf einer Ebene unterhalb der Ebene des **Bereichs**. **Ausschlüsse** sind optional, lassen Sie sie daher vorerst leer.

1. Wählen Sie die **Richtliniendefinition** mit den Auslassungspunkten, um die Liste der verfügbaren Definitionen zu öffnen. Azure Policy umfasst integrierte Richtliniendefinitionen, die Sie verwenden können. Zu den vielen verfügbaren Definitionen zählen z.B.:

   - Enforce tag and its value (Tag und zugehörigen Wert erzwingen)
   - Apply tag and its value (Tag und zugehörigen Wert anwenden)
   - Tag von der Ressourcengruppe erben, falls nicht vorhanden

   Eine Liste mit einem Teil der verfügbaren integrierten Richtlinien finden Sie unter [Azure Policy-Beispiele](./samples/index.md).

1. Durchsuchen Sie die Liste der Richtliniendefinitionen nach der Definition _Überwachen Sie die virtuellen Computer, die nicht verwaltete Datenträger verwenden_. Wählen Sie diese Richtlinie aus, und verwenden Sie dann die Schaltfläche **Auswählen**.

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Screenshot: Filtern der verfügbaren Definitionen" border="false":::

1. Der **Zuweisungsname** wird automatisch mit dem ausgewählten Richtliniennamen gefüllt, kann aber geändert werden. In diesem Fall wird _Überwachen Sie die virtuellen Computer, die nicht verwaltete Datenträger verwenden_ übernommen. Geben Sie ggf. auch eine **Beschreibung** ein. Die Beschreibung enthält Details zu dieser Richtlinienzuweisung.
   Über **Zugewiesen von** werden die Daten abhängig vom angemeldeten Benutzer automatisch ausgefüllt. Dieses Feld ist optional. Daher können auch benutzerdefinierte Werte eingegeben werden.

1. Behalten Sie für die Richtlinienerzwingung die Einstellung _Aktiviert_ bei. Weitere Informationen finden Sie unter [Richtlinienzuweisung – Erzwingungsmodus](./concepts/assignment-structure.md#enforcement-mode).

1. Wählen Sie unten auf der Seite die Option **Weiter** oder oben die Option **Parameter** aus, um zum nächsten Teil des Assistenten für die Zuweisung zu wechseln.

1. Falls die auf der Registerkarte **Grundlagen** ausgewählte Richtliniendefinition Parameter enthalten hat, werden sie auf dieser Registerkarte konfiguriert. Da für _Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden_ keine Parameter vorhanden sind, wählen Sie unten auf der Seite die Option **Weiter** oder oben die Registerkarte **Bereinigung** aus, um zum nächsten Teil des Assistenten für die Zuweisung zu wechseln.

1. Lassen Sie **Verwaltete Identität erstellen** deaktiviert. Dieses Kontrollkästchen _muss_ aktiviert werden, wenn die Richtlinie oder Initiative eine Richtlinie mit der Auswirkung [deployIfNotExists](./concepts/effects.md#deployifnotexists) oder [modify](./concepts/effects.md#modify) enthält. Da dies bei der für diesen Schnellstart verwendeten Richtlinie nicht der Fall ist, lassen Sie es deaktiviert. Weitere Informationen finden Sie unter [Verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) und [Funktionsweise der Wiederherstellungssicherheit](./how-to/remediate-resources.md#how-remediation-security-works).

1. Wählen Sie unten auf der Seite die Option **Weiter** oder oben die Option **Meldungen zu Complianceverstößen** aus, um zum nächsten Teil des Assistenten für die Zuweisung zu wechseln.

1. Legen Sie **Meldung zu Complianceverstößen** auf _Virtual machines should use a managed disk_ (Für virtuelle Computer muss ein verwalteter Datenträger verwendet werden) fest. Diese benutzerdefinierte Meldung wird bei der Ablehnung einer Ressource oder für nicht konforme Ressourcen bei der regulären Auswertung angezeigt.

1. Wählen Sie unten auf der Seite die Option **Weiter** oder oben die Option **Überprüfen + erstellen** aus, um zum nächsten Teil des Assistenten für die Zuweisung zu wechseln.

1. Überprüfen Sie die ausgewählten Optionen, und wählen Sie anschließend am unteren Rand der Seite die Option **Erstellen** aus.

Sie können nun nicht konforme Ressourcen identifizieren, um den Konformitätszustand Ihrer Umgebung nachzuvollziehen.

## <a name="identify-non-compliant-resources"></a>Identifizieren nicht konformer Ressourcen

Wählen Sie links auf der Seite die Option **Konformität** aus. Suchen Sie dann die von Ihnen erstellte Richtlinienzuweisung _Überwachen Sie die virtuellen Computer, die nicht verwaltete Datenträger verwenden_.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Screenshot: Konformitätsdetails auf der Seite zur Richtlinienkonformität" border="false":::

Falls Ressourcen vorhanden sind, die mit dieser neuen Zuweisung nicht konform sind, werden diese unter **Nicht konforme Ressourcen** angezeigt.

Wenn eine Bedingung für einige Ihrer vorhandenen Ressourcen als erfüllt ausgewertet wird, werden diese Ressourcen als nicht mit der Richtlinie konform markiert. Die folgende Tabelle gibt Aufschluss über das Zusammenspiel zwischen den verschiedenen Richtlinienauswirkungen, der Bedingungsauswertung und dem resultierenden Konformitätszustand. Die Auswertungslogik wird zwar im Azure-Portal nicht angezeigt, Sie sehen aber die Ergebnisse für den Konformitätszustand. Das Ergebnis für den Konformitätszustand ist entweder „konform“ oder „nicht konform“.

| Ressourcenzustand | Wirkung | Richtlinienauswertung | Konformitätszustand |
| --- | --- | --- | --- |
| Neu oder aktualisiert | Audit, Modify, AuditIfNotExist | True | Nicht konform |
| Neu oder aktualisiert | Audit, Modify, AuditIfNotExist | False | Konform |
| Exists | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | True | Nicht konform |
| Exists | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | False | Konform |

> [!NOTE]
> Für die Auswirkungen „DeployIfNotExist“ und „AuditIfNotExist“ muss die IF-Anweisung TRUE und die Existenzbedingung FALSE sein, um nicht konform zu sein. Bei TRUE löst die IF-Bedingung die Auswertung der Existenzbedingung für die zugehörigen Ressourcen aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die erstellte Zuweisung zu entfernen, gehen Sie folgendermaßen vor:

1. Wählen Sie links auf der Seite „Azure Policy“ **Konformität** (oder **Zuweisungen**), und suchen Sie die von Ihnen erstellte Richtlinienzuweisung _Überwachen Sie die virtuellen Computer, die nicht verwaltete Datenträger verwenden_.

1. Klicken Sie mit der rechten Maustaste auf die Richtlinienzuweisung _Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden_, und wählen Sie **Zuweisung löschen**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Screenshot: Verwendung des Kontextmenüs zum Löschen einer Zuweisung von der Konformitätsseite" border="false":::

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einem Bereich eine Richtliniendefinition zugewiesen und deren Konformitätsbericht ausgewertet.
Die Richtliniendefinition überprüft, ob alle Ressourcen im Bereich konform sind, und identifiziert die Ressourcen, die nicht konform sind.

Weitere Informationen zum Zuweisen von Richtlinien, die die Konformität neuer Ressourcen überprüfen, finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Richtlinien](./tutorials/create-and-manage.md)