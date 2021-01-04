---
title: Bereitstellen des Blaupausenbeispiels „ISO 27001“
description: Bereitstellungsschritte für das eigenständige Blaupausenbeispiel „ISO 27001“, einschließlich Details zum Blaupausenartefaktparameter.
ms.date: 11/02/2020
ms.topic: sample
ms.openlocfilehash: 75403710ecbfb0cc7046804b45c2da042531e98b
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420281"
---
# <a name="deploy-the-iso-27001-blueprint-sample"></a>Bereitstellen des ISO 27001-Blaupausenbeispiels

Führen Sie zum Bereitstellen des ISO 27001-Blaupausenbeispiels von Azure Blueprints die folgenden Schritte aus:

> [!div class="checklist"]
> - Erstellen einer neuen Blaupause mithilfe des Beispiels
> - Markieren Ihrer Kopie des Beispiels als **Veröffentlicht**
> - Zuweisen Ihrer Kopie der Blaupause zu einem vorhandenen Abonnement

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

## <a name="create-blueprint-from-sample"></a>Erstellen einer Blaupause mithilfe des Beispiels

Implementieren Sie zuerst das Blaupausenbeispiel, indem Sie mithilfe des Beispiels eine neue Blaupause in Ihrer Umgebung erstellen.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen** , und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Suchen Sie unter _Weitere Beispiele_ nach dem Blaupausenbeispiel **ISO 27001** , und wählen Sie **Dieses Beispiel verwenden** aus.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause** : Geben Sie einen Namen für Ihre Kopie des ISO 27001-Blaupausenbeispiels ein.
   - **Definitionsspeicherort** : Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe aus, in der Sie Ihre Kopie des Beispiels speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Überprüfen Sie die Liste der Artefakte, aus denen das Blaupausenbeispiel besteht. Viele der Artefakte enthalten Parameter, die Sie später definieren. Wählen Sie **Entwurf speichern** aus, nachdem Sie das Blaupausenbeispiel überprüft haben.

## <a name="publish-the-sample-copy"></a>Veröffentlichen der Kopie des Beispiels

Ihre Kopie des Blaupausenbeispiels wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann. Die Kopie des Blaupausenbeispiels kann an die Umgebung und an Ihre Anforderungen angepasst werden. Durch diese Änderungen ist sie aber möglicherweise nicht mehr mit der Norm ISO 27001 konform.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen** , und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie auf der neuen Seite rechts für Ihre Kopie des Blaupausenbeispiels eine **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** ein, z. B. „Erste mit dem Blaupausenbeispiel ‚ISO 27001‘ veröffentlichte Version“. Wählen Sie dann unten auf der Seite **Veröffentlichen** aus.

## <a name="assign-the-sample-copy"></a>Zuweisen der Kopie des Beispiels

Nachdem die Kopie des Blaupausenbeispiels erfolgreich **veröffentlicht** wurde, kann sie einem Abonnement innerhalb der Verwaltungsgruppe, in der sie gespeichert wurde, zugewiesen werden. In diesem Schritt werden Parameter angegeben, damit jede Bereitstellung der Kopie des Blaupausenbeispiels eindeutig ist.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen** , und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite mit der Blaupausendefinition die Option **Blaupause zuweisen** aus.

1. Geben Sie die Parameterwerte für die Blaupausenzuweisung an:

   - Grundlagen

     - **Abonnements** : Wählen Sie mindestens eines der Abonnements in der Verwaltungsgruppe aus, in der Sie die Kopie des Blaupausenbeispiels gespeichert haben. Wenn Sie mehrere Abonnements auswählen, wird für jedes Abonnement eine Zuweisung mit den eingegebenen Parametern erstellt.
     - **Zuweisungsname:** Der Name wird basierend auf dem Namen der Blaupause vorab ausgefüllt.
       Sie können ihn nach Bedarf ändern oder unverändert übernehmen.
     - **Standort** : Wählen Sie eine Region aus, in der die verwaltete Identität erstellt werden soll. Azure Blueprint verwendet diese verwaltete Identität zum Bereitstellen aller Artefakte in der zugewiesenen Blaupause. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version der Blaupausendefinition:** Wählen Sie eine **veröffentlichte** Version Ihrer Kopie des Blaupausenbeispiels aus.

   - Zuweisung sperren

     Wählen Sie die Blaupausensperreinstellung für die Umgebung aus. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../../concepts/resource-locking.md).

   - Verwaltete Identität

     Behalten Sie die Standardeinstellung _Vom System zugewiesen_ für die verwaltete Identität bei.

   - Blaupausenparameter

     Die in diesem Abschnitt definierten Parameter werden in vielen der Artefakte in der Blaupausendefinition verwendet, um Konsistenz zu gewährleisten.

     - **Allowed location for resources and resource groups** (Zulässiger Speicherort für Ressourcen und Ressourcengruppen): Wert, der die zulässigen Standorte für Ressourcengruppen und Ressourcen angibt.

   - Artefaktparameter

     Die in diesem Abschnitt definierten Parameter gelten für das Artefakt, unter dem sie definiert werden. Diese Parameter sind [dynamische Parameter](../../concepts/parameters.md#dynamic-parameters), da sie während der Zuweisung der Blaupause definiert werden. Eine vollständige Liste der Artefaktparameter und die zugehörigen Beschreibungen finden Sie in der [Tabelle der Artefaktparameter](#artifact-parameters-table).

1. Nachdem Sie alle Parameter eingegeben haben, wählen Sie unten auf der Seite die Option **Zuweisen** aus. Die Blaupausenzuweisung wird erstellt, und die Artefaktbereitstellung wird gestartet. Die Bereitstellung dauert ungefähr eine Stunde. Um den Status der Bereitstellung zu überprüfen, öffnen Sie die Blaupausenzuweisung.

> [!WARNING]
> Der Azure Blueprints-Dienst und die integrierten Blaupausenbeispiele sind **kostenlos**. Azure-Ressourcen werden [nach Produkt abgerechnet](https://azure.microsoft.com/pricing/). Verwenden Sie den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) zum Schätzen der Kosten für die Ausführung der Ressourcen, die mit diesem Blaupausenbeispiel bereitgestellt werden.

## <a name="artifact-parameters-table"></a>Tabelle der Artefaktparameter

Die folgende Tabelle enthält eine Aufstellung der Parameter des Blaupausenartefakts:

|Artefaktname|Artefakttyp|Parametername|BESCHREIBUNG|
|-|-|-|-|
|\[Vorschau\]: Log Analytics-Agent für Linux-VM-Skalierungsgruppen bereitstellen|Richtlinienzuweisung|Log Analytics-Arbeitsbereich für Linux-VM-Skalierungsgruppen (VMSS)|Wenn der Arbeitsbereich außerhalb des Bereichs der Zuweisung liegt, müssen Sie der Prinzipal-ID der Richtlinienzuweisung Berechtigungen vom Typ „Log Analytics-Mitwirkender“ (oder ähnliche Berechtigungen) erteilen.|
|\[Vorschau\]: Log Analytics-Agent für Linux-VM-Skalierungsgruppen bereitstellen|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Linux-Betriebssystem zum Hinzufügen zum Bereich|Mit einem leeren Array wird angegeben, dass keine optionalen Parameter verwendet werden: \[\]|
|\[Vorschau\]: Bereitstellen des Log Analytics-Agents für Linux-VMs|Richtlinienzuweisung|Log Analytics-Arbeitsbereich für virtuelle Linux-Computer|Wenn der Arbeitsbereich außerhalb des Bereichs der Zuweisung liegt, müssen Sie der Prinzipal-ID der Richtlinienzuweisung Berechtigungen vom Typ „Log Analytics-Mitwirkender“ (oder ähnliche Berechtigungen) erteilen.|
|\[Vorschau\]: Bereitstellen des Log Analytics-Agents für Linux-VMs|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Linux-Betriebssystem zum Hinzufügen zum Bereich|Mit einem leeren Array wird angegeben, dass keine optionalen Parameter verwendet werden: \[\]|
|\[Vorschau\]: Log Analytics-Agent für Windows-VM-Skalierungsgruppen bereitstellen|Richtlinienzuweisung|Log Analytics-Arbeitsbereich für Windows-VM-Skalierungsgruppen (VMSS)|Wenn der Arbeitsbereich außerhalb des Bereichs der Zuweisung liegt, müssen Sie der Prinzipal-ID der Richtlinienzuweisung Berechtigungen vom Typ „Log Analytics-Mitwirkender“ (oder ähnliche Berechtigungen) erteilen.|
|\[Vorschau\]: Log Analytics-Agent für Windows-VM-Skalierungsgruppen bereitstellen|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Windows-Betriebssystem zum Hinzufügen zum Bereich|Mit einem leeren Array wird angegeben, dass keine optionalen Parameter verwendet werden: \[\]|
|\[Vorschau\]: Bereitstellen des Log Analytics-Agents für Windows-VMs|Richtlinienzuweisung|Log Analytics-Arbeitsbereich für virtuelle Windows-Computer|Wenn der Arbeitsbereich außerhalb des Bereichs der Zuweisung liegt, müssen Sie der Prinzipal-ID der Richtlinienzuweisung Berechtigungen vom Typ „Log Analytics-Mitwirkender“ (oder ähnliche Berechtigungen) erteilen.|
|\[Vorschau\]: Bereitstellen des Log Analytics-Agents für Windows-VMs|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Windows-Betriebssystem zum Hinzufügen zum Bereich|Mit einem leeren Array wird angegeben, dass keine optionalen Parameter verwendet werden: \[\]|
|Allowed storage account SKUs (Zulässige Speicherkonto-SKUs)|Richtlinienzuweisung|Liste der zulässigen Speicher-SKUs|Die Liste der SKUs, die für Speicherkonten angegeben werden können|
|Allowed virtual machine SKUs (Zulässige VM-SKUs)|Richtlinienzuweisung|Liste der zulässigen VM-SKUs|Die Liste der SKUs, die für virtuelle Computer angegeben werden können|
|Blueprint Initiative für ISO 27001|Richtlinienzuweisung|Liste von Ressourcentypen, für die Diagnoseprotokolle aktiviert werden sollen|Liste der Ressourcentypen, die überprüfen sollen, ob die Einstellung für das Diagnoseprotokoll nicht aktiviert ist. Die zulässigen Werte finden Sie unter [Unterstützte Dienste, Schemas und Kategorien für Azure-Diagnoseprotokolle](../../../../azure-monitor/platform/resource-logs-schema.md#service-specific-schemas).|

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Schritten zum Bereitstellen des ISO 27001-Blaupausenbeispiels vertraut gemacht haben, finden Sie nun in den folgenden Artikeln Informationen zur Architektur und Steuerungszuordnung:

> [!div class="nextstepaction"]
> [Übersicht über das ISO 27001-Blaupausenbeispiel](./index.md)
> [Control mapping of the ISO 27001 blueprint sample (Steuerungszuordnung des ISO 27001-Blaupausenbeispiels)](./control-mapping.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
