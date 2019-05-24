---
title: Erstellen einer Umgebung anhand eines Blaupausenbeispiels
description: Verwenden Sie ein Blaupausenbeispiel, um eine Blaupausendefinition zu erstellen, die zwei Ressourcengruppen einrichtet und eine Rollenzuweisung für jede dieser Ressourcengruppen konfiguriert.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/05/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b0d5d96ff897ac1710206eb49bca785e8809cb7d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798312"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Tutorial: Erstellen einer Umgebung anhand eines Blaupausenbeispiels

Beispielblaupausen enthalten Beispiele für die Aufgaben, die mit Azure Blueprints ausgeführt werden können. Jedes Blaupausenbeispiel dient einer bestimmten Absicht bzw. einem spezifischen Zweck, erstellt aber nicht selbst eine vollständige Umgebung. Jedes Blaupausenbeispiel ist als Ausgangspunkt für die Erkundung von Azure Blueprints vorgesehen und enthält verschiedene Kombinationen von Artefakten, Designs und Parametern.

Im folgenden Tutorial wird die Blaupause **Ressourcengruppen mit RBAC** verwendet, um verschiedene Aspekte des Diensts Azure Blueprints vorzustellen. Folgende Schritte werden erläutert:

> [!div class="checklist"]
> - Erstellen einer neuen Blaupausendefinition anhand des Beispiels
> - Markieren Ihrer Kopie des Beispiels als **Veröffentlicht**
> - Zuweisen Ihrer Kopie der Blaupause zu einem vorhandenen Abonnement
> - Überprüfen der bereitgestellten Ressourcen für die Zuweisung
> - Aufheben der Zuweisung der Blaupause zum Entfernen der Sperren

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-blueprint-definition-from-sample"></a>Erstellen einer Blaupausendefinition anhand des Beispiels

Implementieren Sie zuerst das Blaupausenbeispiel. Beim Importieren wird basierend auf dem Beispiel eine neue Blaupause in Ihrer Umgebung erstellt.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Suchen Sie unter _Weitere Beispiele_ nach dem Blaupausenbeispiel **Ressourcengruppen mit RBAC**, und wählen Sie **Dieses Beispiel verwenden** aus.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause**: Geben Sie einen Namen für Ihre Kopie des Blaupausenbeispiels ein. In diesem Tutorial verwenden Sie den Namen _two-rgs-with-role-assignments_.
   - **Speicherort der Definition**: Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe oder das Abonnement aus, in der bzw. dem Sie Ihre Kopie des Beispiels speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Überprüfen Sie die Liste der Artefakte, aus denen das Blaupausenbeispiel besteht. Dieses Beispiel definiert zwei Ressourcengruppen mit den Anzeigenamen _ProdRG_ und _PreProdRG_. Der endgültige Name und der Speicherort jeder Ressourcengruppe werden während der Blaupausenzuweisung festgelegt. Der Ressourcengruppe _ProdRG_ ist die Rolle _Mitwirkender_ zugewiesen, und der Ressourcengruppe _PreProdRG_ sind die Rollen _Besitzer_ und _Leser_ zugewiesen. Die in der Definition zugewiesenen Rollen sind statisch. Der Benutzer, die App oder die Gruppe, dem bzw. der die Rolle zugewiesen wird, wird jedoch während der Blaupausenzuweisung festgelegt.

1. Wählen Sie **Entwurf speichern** aus, nachdem Sie das Blaupausenbeispiel überprüft haben.

In diesem Schritt wird eine Kopie der Blaupausendefinition in der ausgewählten Verwaltungsgruppe bzw. dem ausgewählten Abonnement erstellt. Die gespeicherte Blaupausendefinition wird wie alle neu erstellten Blaupausen verwaltet. Sie können das Beispiel beliebig oft in Ihrer Verwaltungsgruppe bzw. Ihrem Abonnement speichern. Für jede Kopie muss jedoch ein eindeutiger Name angegeben werden.

Sobald die Portalbenachrichtigung **Blaupausendefinition erfolgreich gespeichert** angezeigt wird, fahren Sie mit dem nächsten Schritt fort.

## <a name="publish-the-sample-copy"></a>Veröffentlichen der Kopie des Beispiels

Ihre Kopie des Blaupausenbeispiels wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann. Die Kopie des Blaupausenbeispiels kann entsprechend Ihrer Umgebung und Ihren Anforderungen angepasst werden. In diesem Tutorial nehmen Sie keine Änderungen vor.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Suchen Sie mithilfe der Filter nach der Blaupausendefinition _two-rgs-with-role-assignments_, und wählen Sie sie aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie im neuen Bereich auf der rechten Seite für Ihre Kopie des Blaupausenbeispiels _1.0_ als **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** ein, z. B. „Erste mit dem Blaupausenbeispiel ‚Ressourcengruppen mit RBAC‘ veröffentlichte Version“. Klicken Sie dann unten auf der Seite auf **Veröffentlichen**.

In diesem Schritt kann die Blaupause einem Abonnement zugewiesen werden. Nach der Veröffentlichung können noch Änderungen vorgenommen werden. Zur Nachverfolgung der Unterschiede zwischen verschiedenen Versionen der gleichen Blaupausendefinition müssen weitere Änderungen mit einem neuen Wert für **Version** veröffentlicht werden.

Sobald die Portalbenachrichtigung **Blaupausendefinition erfolgreich veröffentlicht** angezeigt wird, fahren Sie mit dem nächsten Schritt fort.

## <a name="assign-the-sample-copy"></a>Zuweisen der Kopie des Beispiels

Nachdem die Kopie des Blaupausenbeispiels erfolgreich **veröffentlicht** wurde, kann sie einem Abonnement innerhalb der Verwaltungsgruppe, in der sie gespeichert wurde, zugewiesen werden. In diesem Schritt werden Parameter angegeben, damit jede Bereitstellung der Kopie des Blaupausenbeispiels eindeutig ist.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Suchen Sie mithilfe der Filter nach der Blaupausendefinition _two-rgs-with-role-assignments_, und wählen Sie sie aus.

1. Wählen Sie oben auf der Seite mit der Blaupausendefinition die Option **Blaupause zuweisen** aus.

1. Geben Sie die Parameterwerte für die Blaupausenzuweisung an:

   - Grundlagen

     - **Abonnements**: Wählen Sie mindestens eines der Abonnements in der Verwaltungsgruppe aus, in der Sie die Kopie des Blaupausenbeispiels gespeichert haben. Wenn Sie mehrere Abonnements auswählen, wird für jedes Abonnement eine Zuweisung mit den eingegebenen Parametern erstellt.
     - **Zuweisungsname**: Der Name wird basierend auf dem Namen der Blaupausendefinition vorab aufgefüllt.
     - **Standort**: Wählen Sie eine Region aus, in der die verwaltete Identität erstellt werden soll. Azure Blueprint verwendet diese verwaltete Identität zum Bereitstellen aller Artefakte in der zugewiesenen Blaupause. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../active-directory/managed-identities-azure-resources/overview.md).
       Wählen Sie für dieses Tutorial die Region _USA, Osten 2_ aus.
     - **Version der Blaupausendefinition**: Wählen Sie die Version _1.0_ mit dem Status **Veröffentlicht** Ihrer Kopie der Beispielblaupausendefinition aus.

   - Zuweisung sperren

     Wählen Sie den Blaupausensperrmodus _Schreibgeschützt_ aus. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../concepts/resource-locking.md).

   - Verwaltete Identität

     Übernehmen Sie die Standardoption _Vom System zugewiesen_. Weitere Informationen finden Sie unter [Verwaltete Identitäten](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Artefaktparameter

     Die in diesem Abschnitt definierten Parameter gelten für das Artefakt, unter dem sie definiert werden. Diese Parameter sind [dynamische Parameter](../concepts/parameters.md#dynamic-parameters), da sie während der Zuweisung der Blaupause definiert werden. Legen Sie den Parameterwert für jedes Artefakt auf den Wert fest, der in der Spalte **Wert** definiert ist. Wählen Sie für `{Your ID}` Ihr Azure-Benutzerkonto aus.

     |Artefaktname|Artefakttyp|Parametername|Wert|BESCHREIBUNG|
     |-|-|-|-|-|
     |Ressourcengruppe „ProdRG“|Ressourcengruppe|NAME|ProductionRG|Definiert den Namen der ersten Ressourcengruppe.|
     |Ressourcengruppe „ProdRG“|Ressourcengruppe|Location|USA, Westen 2|Legt den Ort der ersten Ressourcengruppe fest.|
     |Mitwirkender|Rollenzuweisung|Benutzer oder Gruppe|{Ihre ID}|Definiert, welchem Benutzer oder welcher Gruppe die Rollenzuweisung _Mitwirkender_ in der ersten Ressourcengruppe gewährt wird.|
     |Ressourcengruppe „PreProdRG“|Ressourcengruppe|NAME|PreProductionRG|Definiert den Namen der zweiten Ressourcengruppe.|
     |Ressourcengruppe „PreProdRG“|Ressourcengruppe|Location|USA (Westen)|Legt den Ort der zweiten Ressourcengruppe fest.|
     |Owner (Besitzer)|Rollenzuweisung|Benutzer oder Gruppe|{Ihre ID}|Definiert, welchem Benutzer oder welcher Gruppe die Rollenzuweisung _Besitzer_ in der zweiten Ressourcengruppe gewährt wird.|
     |Leser|Rollenzuweisung|Benutzer oder Gruppe|{Ihre ID}|Definiert, welchem Benutzer oder welcher Gruppe die Rollenzuweisung _Leser_ in der zweiten Ressourcengruppe gewährt wird.|

1. Nachdem Sie alle Parameter eingegeben haben, wählen Sie unten auf der Seite die Option **Zuweisen** aus.

In diesem Schritt werden die definierten Ressourcen bereitgestellt und die ausgewählte Option für **Zuweisung sperren** konfiguriert. Das Anwenden von Blaupausensperren kann bis zu 30 Minuten dauern.

Sobald die Portalbenachrichtigung **Blaupausendefinition erfolgreich zugewiesen** angezeigt wird, fahren Sie mit dem nächsten Schritt fort.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Überprüfen der durch die Zuweisung bereitgestellten Ressourcen

Durch die Blaupausenzuweisung werden die in der Blaupausendefinition definierten Artefakte erstellt und nachverfolgt. Der Status der Ressourcen wird auf der Seite mit der Blaupausenzuweisung angezeigt. Sie können den Status auch überprüfen, indem Sie sich die Ressourcen direkt ansehen.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Zugewiesene Blaupausen** aus. Suchen Sie mithilfe der Filter nach der Blaupausenzuweisung _Assignment-two-rgs-with-role-assignments_, und wählen Sie sie aus.

   Auf dieser Seite sehen Sie, dass die Zuweisung erfolgreich war. Zudem wird eine Liste der erstellten Ressourcen mit ihrem Blaupausensperrzustand angezeigt. Wenn die Zuweisung aktualisiert wird, werden in der Dropdownliste **Zuweisungsvorgang** Details zur Bereitstellung jeder Definitionsversion angezeigt. Sie können in der Liste auf jede erstellte Ressource klicken, um die Eigenschaftenseite der jeweiligen Ressource zu öffnen.

1. Wählen Sie die Ressourcengruppe **ProductionRG** aus.

   Wie Sie sehen, wird für den Namen der Ressourcengruppe **ProductionRG** angezeigt und nicht der Artefaktanzeigename _ProdRG_. Dieser Name entspricht dem Wert, der während der Blaupausenzuweisung festgelegt wurde.

1. Wählen Sie links die Seite **Zugriffssteuerung (IAM)** und dann die Registerkarte **Rollenzuweisungen** aus.

   Hier sehen Sie, dass Ihrem Konto die Rolle _Mitwirkender_ für den Bereich _Diese Ressource_ gewährt wurde. Die Blaupausenzuweisung _Assignment-two-rgs-with-role-assignments_ verfügt über die Rolle _Besitzer_, da sie zum Erstellen der Ressourcengruppe verwendet wurde. Diese Berechtigungen werden auch zum Verwalten von Ressourcen mit konfigurierten Blaupausensperren verwendet.

1. Wählen Sie in der Breadcrumb-Leiste im Azure-Portal **Assignment-two-rgs-with-role-assignments** aus, um zur vorherigen Seite zurückzukehren, und wählen Sie dann die Ressourcengruppe **PreProductionRG** aus.

1. Wählen Sie links die Seite **Zugriffssteuerung (IAM)** und dann die Registerkarte **Rollenzuweisungen** aus.

   Hier sehen Sie, dass Ihrem Konto die Rollen _Besitzer_ und _Leser_ für den Bereich _Diese Ressource_ gewährt wurden. Die Blaupausenzuweisung verfügt wie die erste Ressourcengruppe auch über die Rolle _Besitzer_.

1. Wählen Sie die Registerkarte **Ablehnungszuweisungen** aus.

   Durch die Blaupausenzuweisung wurde eine [Ablehnungszuweisung](../../../role-based-access-control/deny-assignments.md) für die bereitgestellte Ressourcengruppe erstellt, um den Blaupausensperrmodus _Schreibgeschützt_ zu erzwingen. Die Ablehnungszuweisung verhindert, dass ein Benutzer mit entsprechenden Berechtigungen auf der Registerkarte _Rollenzuweisungen_ bestimmte Aktionen durchführt. Die Ablehnungszuweisung gilt für _alle Prinzipale_.

1. Wählen Sie die Ablehnungszuweisung und dann links die Seite **Abgelehnte Berechtigungen** aus.

   Die Ablehnungszuweisung verhindert alle Vorgänge mit der Konfiguration **\*** und **Aktion**, erlaubt aber den Lesezugriff, indem **\*/read** über **NotActions** ausgeschlossen wird.

1. Wählen Sie in der Breadcrumb-Leiste im Azure-Portal **PreProductionRG – Zugriffssteuerung (IAM)** aus. Wählen Sie dann links die Seite **Übersicht** aus, und klicken Sie anschließend auf die Schaltfläche **Ressourcengruppe löschen**. Geben Sie den Namen _PreProductionRG_ ein, um den Löschvorgang zu bestätigen, und wählen Sie am unteren Rand des Bereichs die Option **Löschen** aus.

   Die Portalbenachrichtigung **Fehler beim Löschen der Ressourcengruppe „PreProductionRG“** wird angezeigt. Dieser Fehler weist darauf hin, dass Ihr Konto zwar über die Berechtigung zum Löschen der Ressourcengruppe verfügt, der Zugriff aber durch die Blaupausenzuweisung verweigert wird. Wie Sie sich erinnern, haben Sie während der Blaupausenzuweisung den Blaupausensperrmodus _Schreibgeschützt_ ausgewählt. Aufgrund der Blaupausensperre kann die Ressource weder von einem Konto mit der entsprechenden Berechtigung noch vom _Besitzer_ gelöscht werden. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../concepts/resource-locking.md).

Diese Schritte zeigen, dass die Ressourcen gemäß der Definition erstellt wurden und die Blaupausensperren ungewollte Löschungen verhindern (auch durch ein Konto mit der entsprechenden Berechtigung).

## <a name="unassign-the-blueprint"></a>Aufheben der Zuweisung der Blaupause

Im letzten Schritt wird die Zuweisung der Blaupause und der von ihr bereitgestellten Ressourcen aufgehoben.
Durch das Aufheben der Zuweisung werden die bereitgestellten Artefakte nicht entfernt.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Zugewiesene Blaupausen** aus. Suchen Sie mithilfe der Filter nach der Blaupausenzuweisung _Assignment-two-rgs-with-role-assignments_, und wählen Sie sie aus.

1. Klicken Sie oben auf der Seite auf die Schaltfläche **Zuweisung der Blaupause aufheben**. Lesen Sie die Warnung im Bestätigungsdialogfeld, und klicken Sie dann auf **OK**.

   Durch das Entfernen der Blaupausenzuweisung werden auch die Blaupausensperren aufgehoben. Die erstellten Ressourcen können nun wieder von einem Konto mit entsprechenden Berechtigungen gelöscht werden.

1. Wählen Sie im Azure-Menü die Option **Ressourcengruppen** und dann **ProductionRG** aus.

1. Wählen Sie links die Seite **Zugriffssteuerung (IAM)** und dann die Registerkarte **Rollenzuweisungen** aus.

Für die Sicherheit der einzelnen Ressourcengruppen gelten nach wie vor die bereitgestellten Rollenzuweisungen, die Blaupausenzuweisung verfügt jedoch nicht mehr über die Zugriffsrolle _Besitzer_.

Sobald die Portalbenachrichtigung **Blaupausenzuweisung erfolgreich entfernt** angezeigt wird, fahren Sie mit dem nächsten Schritt fort.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie nach Abschluss dieses Tutorials die folgenden Ressourcen:

- Ressourcengruppe _ProductionRG_
- Ressourcengruppe _PreProductionRG_
- Blaupausendefinition _two-rgs-with-role-assignments_

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../concepts/resource-locking.md) können.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../concepts/sequencing-order.md) anpassen können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).
- Beheben Sie Probleme bei der Blaupausenzuweisung mithilfe des [allgemeinen Leitfadens zur Problembehandlung](../troubleshoot/general.md).