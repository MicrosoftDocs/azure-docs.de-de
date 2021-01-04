---
title: Blaupausenbeispiel „CIS Microsoft Azure Foundations Benchmark“
description: Hier finden Sie eine Übersicht über das Blaupausenbeispiel „CIS Microsoft Azure Foundations Benchmark“. Dieses Blaupausenbeispiel unterstützt Kunden bei der Bewertung spezifischer Kontrollen.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 6c09e539e192db8422b25079264b9f4e8965d994
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005604"
---
# <a name="cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Blaupausenbeispiel „CIS Microsoft Azure Foundations Benchmark“

Das Blaupausenbeispiel „CIS Microsoft Azure Foundations Benchmark“ bietet mithilfe von [Azure Policy](../../policy/overview.md) Schutzmaßnahmen für Governance, die Ihnen beim Bewerten bestimmter CIS Microsoft Azure Foundations Benchmark-Empfehlungen helfen. Diese Blaupause hilft Kunden bei der Bereitstellung eines Kernsatzes von Richtlinien für jede in Azure bereitgestellte Architektur, die CIS Microsoft Azure Foundations Benchmark-Empfehlungen implementieren muss.

## <a name="recommendation-mapping"></a>Empfehlungszuordnung

In der [Azure Policy-Empfehlungszuordnung](../../policy/samples/cis-azure-1-1-0.md) finden Sie Details zu den in dieser Blaupause enthaltenen Richtliniendefinitionen. Darüber hinaus erfahren Sie, wie diese Richtlinien den **Compliancebereichen** und **Steuerungen** in CIS Microsoft Azure Foundations Benchmark v1.1.0 entsprechen. Bei der Zuweisung zu einer Architektur werden Ressourcen von Azure Policy auf Nichtkonformität mit den zugewiesenen Richtliniendefinitionen überprüft. Weitere Informationen finden Sie unter [Was ist Azure Policy?](../../policy/overview.md).

## <a name="deploy"></a>Bereitstellen

Zum Bereitstellen des Blaupausenbeispiels „CIS Microsoft Azure Foundations Benchmark“ von Azure Blueprints müssen die folgenden Schritte ausgeführt werden:

> [!div class="checklist"]
> - Erstellen einer neuen Blaupause mithilfe des Beispiels
> - Markieren Ihrer Kopie des Beispiels als **Veröffentlicht**
> - Zuweisen Ihrer Kopie der Blaupause zu einem vorhandenen Abonnement

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

### <a name="create-blueprint-from-sample"></a>Erstellen einer Blaupause mithilfe des Beispiels

Implementieren Sie zuerst das Blaupausenbeispiel, indem Sie mithilfe des Beispiels eine neue Blaupause in Ihrer Umgebung erstellen.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Navigieren Sie unter _Weitere Beispiele_ zum Blaupausenbeispiel **CIS Microsoft Azure Foundations Benchmark v1.1.0**, und wählen Sie **Dieses Beispiel verwenden** aus.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause**: Geben Sie einen Namen für Ihre Kopie des Blaupausenbeispiels „CIS Microsoft Azure Foundations Benchmark“ an.
   - **Definitionsspeicherort**: Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe aus, in der Sie Ihre Kopie des Beispiels speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Überprüfen Sie die Liste der Artefakte, die im Blaupausenbeispiel enthalten sind. Viele der Artefakte enthalten Parameter, die Sie später definieren. Wählen Sie **Entwurf speichern** aus, nachdem Sie das Blaupausenbeispiel überprüft haben.

### <a name="publish-the-sample-copy"></a>Veröffentlichen der Kopie des Beispiels

Ihre Kopie des Blaupausenbeispiels wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann. Die Kopie des Blaupausenbeispiels kann Ihrer Umgebung und Ihren Anforderungen angepasst werden. Durch diese Änderungen ist sie aber möglicherweise nicht mehr mit den Empfehlungen zu „CIS Microsoft Azure Foundations Benchmark“ konform.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie auf der neuen Seite rechts für Ihre Kopie des Blaupausenbeispiels eine **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** an, etwa „Erste vom Blaupausenbeispiel ‚CIS Microsoft Azure Foundations Benchmark‘ veröffentlichte Version“. Wählen Sie dann unten auf der Seite **Veröffentlichen** aus.

### <a name="assign-the-sample-copy"></a>Zuweisen der Kopie des Beispiels

Nachdem die Kopie des Blaupausenbeispiels erfolgreich **veröffentlicht** wurde, kann sie einem Abonnement innerhalb der Verwaltungsgruppe, in der sie gespeichert wurde, zugewiesen werden. In diesem Schritt werden Parameter angegeben, damit jede Bereitstellung der Kopie des Blaupausenbeispiels eindeutig ist.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite mit der Blaupausendefinition die Option **Blaupause zuweisen** aus.

1. Geben Sie die Parameterwerte für die Blaupausenzuweisung an:

   - Grundlagen

     - **Abonnements**: Wählen Sie mindestens eines der Abonnements in der Verwaltungsgruppe aus, in der Sie die Kopie des Blaupausenbeispiels gespeichert haben. Wenn Sie mehrere Abonnements auswählen, wird für jedes Abonnement eine Zuweisung mit den eingegebenen Parametern erstellt.
     - **Zuweisungsname:** Der Name wird basierend auf dem Namen der Blaupause vorab ausgefüllt.
       Sie können ihn nach Bedarf ändern oder unverändert übernehmen.
     - **Standort**: Wählen Sie eine Region aus, in der die verwaltete Identität erstellt werden soll. Azure Blueprint verwendet diese verwaltete Identität zum Bereitstellen aller Artefakte in der zugewiesenen Blaupause. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version der Blaupausendefinition:** Wählen Sie eine **veröffentlichte** Version Ihrer Kopie des Blaupausenbeispiels aus.

   - Zuweisung sperren

     Wählen Sie die Blaupausensperreinstellung für die Umgebung aus. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../concepts/resource-locking.md).

   - Verwaltete Identität

     Behalten Sie die Standardeinstellung _Vom System zugewiesen_ für die verwaltete Identität bei.

   - Artefaktparameter

     Die in diesem Abschnitt definierten Parameter gelten für das Artefakt, unter dem sie definiert werden. Diese Parameter sind [dynamische Parameter](../concepts/parameters.md#dynamic-parameters), da sie während der Zuweisung der Blaupause definiert werden. Eine vollständige Liste der Artefaktparameter und die zugehörigen Beschreibungen finden Sie in der [Tabelle der Artefaktparameter](#artifact-parameters-table).

1. Nachdem Sie alle Parameter eingegeben haben, wählen Sie unten auf der Seite die Option **Zuweisen** aus. Die Blaupausenzuweisung wird erstellt, und die Artefaktbereitstellung wird gestartet. Die Bereitstellung dauert ungefähr eine Stunde. Um den Status der Bereitstellung zu überprüfen, öffnen Sie die Blaupausenzuweisung.

> [!WARNING]
> Der Azure Blueprints-Dienst und die integrierten Blaupausenbeispiele sind **kostenlos**. Azure-Ressourcen werden [nach Produkt abgerechnet](https://azure.microsoft.com/pricing/). Verwenden Sie den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) zum Schätzen der Kosten für die Ausführung der Ressourcen, die mit diesem Blaupausenbeispiel bereitgestellt werden.

### <a name="artifact-parameters-table"></a>Tabelle der Artefaktparameter

Die folgende Tabelle enthält eine Aufstellung der Parameter des Blaupausenartefakts:

|Artefaktname|Artefakttyp|Parametername|BESCHREIBUNG|
|-|-|-|-|
|CIS Microsoft Azure Foundations Benchmark 1.1.0-Empfehlungen überwachen und spezifische unterstützende VM-Erweiterungen bereitstellen|Richtlinienzuweisung|Liste der Regionen, in denen Network Watcher aktiviert werden soll|Eine durch Semikolons getrennte Liste von Regionen. Verwenden Sie Get-AzLocation, um eine vollständige Liste der Regionen abzurufen. Beispiel: eastus; eastus2|
|CIS Microsoft Azure Foundations Benchmark 1.1.0-Empfehlungen überwachen und spezifische unterstützende VM-Erweiterungen bereitstellen|Richtlinienzuweisung|Liste der zur Verwendung genehmigten VM-Erweiterungen|Eine durch Semikolons getrennte Liste von Erweiterungen. Verwenden Sie „Get-AzVMExtensionImage“, um eine vollständige Liste der VM-Erweiterungen anzuzeigen. Beispiel: AzureDiskEncryption; IaaSAntimalware|

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).