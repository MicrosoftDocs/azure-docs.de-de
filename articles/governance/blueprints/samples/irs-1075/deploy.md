---
title: Bereitstellen des IRS 1075-Blaupausenbeispiels
description: Bereitstellungsschritte für das Blaupausenbeispiel „IRS 1075 (Rev.11-2016)“, einschließlich Details zum Blaupausenartefaktparameter.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 6df2781207c623e10daf0681865b9246be1b1f4c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571916"
---
# <a name="deploy-the-irs-1075-blueprint-sample"></a>Bereitstellen des IRS 1075-Blaupausenbeispiels

Führen Sie zum Bereitstellen des Blaupausenbeispiels „IRS 1075 (Rev.11-2016)“ von Azure Blueprints die folgenden Schritte aus:

> [!div class="checklist"]
> - Erstellen einer neuen Blaupause mithilfe des Beispiels
> - Markieren Ihrer Kopie des Beispiels als **Veröffentlicht**
> - Zuweisen Ihrer Kopie der Blaupause zu einem vorhandenen Abonnement

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

## <a name="create-blueprint-from-sample"></a>Erstellen einer Blaupause mithilfe des Beispiels

Implementieren Sie zuerst das Blaupausenbeispiel, indem Sie mithilfe des Beispiels eine neue Blaupause in Ihrer Umgebung erstellen.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Suchen Sie unter _Weitere Beispiele_ nach dem Blaupausenbeispiel **IRS 1075 (Rev.11-2016)** , und wählen Sie **Dieses Beispiel verwenden** aus.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause**: Geben Sie einen Namen für Ihre Kopie des Blaupausenbeispiels „IRS 1075 (Rev.11-2016)“ ein.
   - **Definitionsspeicherort**: Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe aus, in der Sie Ihre Kopie des Beispiels speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Überprüfen Sie die Liste der Artefakte, aus denen das Blaupausenbeispiel besteht. Viele der Artefakte enthalten Parameter, die Sie später definieren. Wählen Sie **Entwurf speichern** aus, nachdem Sie das Blaupausenbeispiel überprüft haben.

## <a name="publish-the-sample-copy"></a>Veröffentlichen der Kopie des Beispiels

Ihre Kopie des Blaupausenbeispiels wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann. Die Kopie des Blaupausenbeispiels kann an Ihre Umgebung und Ihre Anforderungen angepasst werden. Durch diese Änderungen ist sie aber möglicherweise nicht mehr mit den Steuerungen von NIST SP 800-53 konform.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie auf der neuen Seite rechts für Ihre Kopie des Blaupausenbeispiels eine **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** an, etwa „Erste Version aus dem Blaupausenbeispiel „NIST SP 800-53 R4“ veröffentlicht“. Wählen Sie dann unten auf der Seite **Veröffentlichen** aus.

## <a name="assign-the-sample-copy"></a>Zuweisen der Kopie des Beispiels

Nachdem die Kopie des Blaupausenbeispiels erfolgreich **veröffentlicht** wurde, kann sie einem Abonnement innerhalb der Verwaltungsgruppe, in der sie gespeichert wurde, zugewiesen werden. In diesem Schritt werden Parameter angegeben, damit jede Bereitstellung der Kopie des Blaupausenbeispiels eindeutig ist.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite mit der Blaupausendefinition die Option **Blaupause zuweisen** aus.

1. Geben Sie die Parameterwerte für die Blaupausenzuweisung an:

   - Grundlagen

     - **Abonnements**: Wählen Sie mindestens eines der Abonnements in der Verwaltungsgruppe aus, in der Sie die Kopie des Blaupausenbeispiels gespeichert haben. Wenn Sie mehrere Abonnements auswählen, wird für jedes Abonnement eine Zuweisung mit den eingegebenen Parametern erstellt.
     - **Zuweisungsname:** Der Name wird basierend auf dem Namen der Blaupause vorab ausgefüllt.
       Sie können ihn nach Bedarf ändern oder unverändert übernehmen.
     - **Standort**: Wählen Sie eine Region aus, in der die verwaltete Identität erstellt werden soll. Azure Blueprint verwendet diese verwaltete Identität zum Bereitstellen aller Artefakte in der zugewiesenen Blaupause. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version der Blaupausendefinition:** Wählen Sie eine **veröffentlichte** Version Ihrer Kopie des Blaupausenbeispiels aus.

   - Zuweisung sperren

     Wählen Sie die Blaupausensperreinstellung für die Umgebung aus. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../../concepts/resource-locking.md).

   - Verwaltete Identität

     Behalten Sie die Standardeinstellung _Vom System zugewiesen_ für die verwaltete Identität bei.

   - Artefaktparameter

     Die in diesem Abschnitt definierten Parameter gelten für das Artefakt, unter dem sie definiert werden. Diese Parameter sind [dynamische Parameter](../../concepts/parameters.md#dynamic-parameters), da sie während der Zuweisung der Blaupause definiert werden. Eine vollständige Liste der Artefaktparameter und die zugehörigen Beschreibungen finden Sie in der [Tabelle der Artefaktparameter](#artifact-parameters-table).

1. Nachdem Sie alle Parameter eingegeben haben, wählen Sie unten auf der Seite die Option **Zuweisen** aus. Die Blaupausenzuweisung wird erstellt, und die Artefaktbereitstellung wird gestartet. Die Bereitstellung dauert ungefähr eine Stunde. Um den Status der Bereitstellung zu überprüfen, öffnen Sie die Blaupausenzuweisung.

> [!WARNING]
> Der Azure Blueprints-Dienst und die integrierten Blaupausenbeispiele sind **kostenlos**. Azure-Ressourcen werden [nach Produkt abgerechnet](https://azure.microsoft.com/pricing/). Verwenden Sie den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) zum Schätzen der Kosten für die Ausführung der Ressourcen, die mit diesem Blaupausenbeispiel bereitgestellt werden.

## <a name="artifact-parameters-table"></a>Tabelle der Artefaktparameter

Die folgende Tabelle enthält eine Aufstellung der Parameter des Blaupausenartefakts:

|Artefaktname|Artefakttyp|Parametername|BESCHREIBUNG|
|-|-|-|-|
|Überwachen von IRS 1075 (Rev.11-2016)-Steuerungen und Bereitstellen spezifischer VM-Erweiterungen zur Unterstützung der Überwachungsanforderungen|Richtlinienzuweisung|ID des Log Analytics-Arbeitsbereichs, für den die VMs konfiguriert werden sollen|Dies ist die ID (GUID) des Log Analytics-Arbeitsbereichs, für den die virtuellen Computer konfiguriert werden sollen.|
|Überwachen von IRS 1075 (Rev.11-2016)-Steuerungen und Bereitstellen spezifischer VM-Erweiterungen zur Unterstützung der Überwachungsanforderungen|Richtlinienzuweisung|Liste von Ressourcentypen, für die Diagnoseprotokolle aktiviert werden sollen|Liste der Ressourcentypen, die überprüfen sollen, ob die Einstellung für das Diagnoseprotokoll nicht aktiviert ist. Die zulässigen Werte finden Sie unter [Unterstützte Dienste, Schemas und Kategorien für Azure-Diagnoseprotokolle](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas).|
|Überwachen von IRS 1075 (Rev.11-2016)-Steuerungen und Bereitstellen spezifischer VM-Erweiterungen zur Unterstützung der Überwachungsanforderungen|Richtlinienzuweisung|Liste der Benutzer, die aus der Windows-VM-Administratorengruppe ausgeschlossen werden sollen|Eine durch Semikolon getrennte Liste von Mitgliedern, die aus der Gruppe der lokalen Administratoren ausgeschlossen werden sollen. Beispiel: Administrator; myUser1; myUser2|
|Überwachen von IRS 1075 (Rev.11-2016)-Steuerungen und Bereitstellen spezifischer VM-Erweiterungen zur Unterstützung der Überwachungsanforderungen|Richtlinienzuweisung|Liste der Benutzer, die in der Gruppe der Windows-VM-Administratoren enthalten sein sollen|Eine durch Semikolon getrennte Liste von Mitgliedern, die in der Gruppe der lokalen Administratoren enthalten sein sollen. Beispiel: Administrator; myUser1; myUser2|
|Log Analytics-Agent für Linux-VM-Skalierungsgruppen bereitstellen|Richtlinienzuweisung|Log Analytics-Arbeitsbereich für Linux-VM-Skalierungsgruppen (VMSS)|Wenn der Arbeitsbereich außerhalb des Bereichs der Zuweisung liegt, müssen Sie der Prinzipal-ID der Richtlinienzuweisung Berechtigungen vom Typ „Log Analytics-Mitwirkender“ (oder ähnliche Berechtigungen) erteilen.|
|Log Analytics-Agent für Linux-VM-Skalierungsgruppen bereitstellen|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Linux-Betriebssystem zum Hinzufügen zum Bereich|Mit einem leeren Array wird angegeben, dass keine optionalen Parameter verwendet werden: \[\]|
|Bereitstellen des Log Analytics-Agents für Linux-VMs|Richtlinienzuweisung|Log Analytics-Arbeitsbereich für virtuelle Linux-Computer|Wenn der Arbeitsbereich außerhalb des Bereichs der Zuweisung liegt, müssen Sie der Prinzipal-ID der Richtlinienzuweisung Berechtigungen vom Typ „Log Analytics-Mitwirkender“ (oder ähnliche Berechtigungen) erteilen.|
|Bereitstellen des Log Analytics-Agents für Linux-VMs|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Linux-Betriebssystem zum Hinzufügen zum Bereich|Mit einem leeren Array wird angegeben, dass keine optionalen Parameter verwendet werden: \[\]|
|Log Analytics-Agent für Windows-VM-Skalierungsgruppen bereitstellen|Richtlinienzuweisung|Log Analytics-Arbeitsbereich für Windows-VM-Skalierungsgruppen (VMSS)|Wenn der Arbeitsbereich außerhalb des Bereichs der Zuweisung liegt, müssen Sie der Prinzipal-ID der Richtlinienzuweisung Berechtigungen vom Typ „Log Analytics-Mitwirkender“ (oder ähnliche Berechtigungen) erteilen.|
|Log Analytics-Agent für Windows-VM-Skalierungsgruppen bereitstellen|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Windows-Betriebssystem zum Hinzufügen zum Bereich|Mit einem leeren Array wird angegeben, dass keine optionalen Parameter verwendet werden: \[\]|
|Bereitstellen des Log Analytics-Agents für Windows-VMs|Richtlinienzuweisung|Log Analytics-Arbeitsbereich für virtuelle Windows-Computer|Wenn der Arbeitsbereich außerhalb des Bereichs der Zuweisung liegt, müssen Sie der Prinzipal-ID der Richtlinienzuweisung Berechtigungen vom Typ „Log Analytics-Mitwirkender“ (oder ähnliche Berechtigungen) erteilen.|
|Bereitstellen des Log Analytics-Agents für Windows-VMs|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Windows-Betriebssystem zum Hinzufügen zum Bereich|Mit einem leeren Array wird angegeben, dass keine optionalen Parameter verwendet werden: \[\]|
|Bereitstellen von Advanced Threat Protection für Speicherkonten|Richtlinienzuweisung|Wirkung|Informationen zu Richtlinienauswirkungen finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen](../../../policy/concepts/effects.md).|
|Bereitstellen von Überwachung auf SQL-Server-Instanzen|Richtlinienzuweisung|Der Wert für die Aufbewahrungsdauer in Tagen (0 steht für eine unbegrenzte Aufbewahrung).|Aufbewahrung in Tagen (optional, ohne Angabe 180 Tage)|
|Bereitstellen von Überwachung auf SQL-Server-Instanzen|Richtlinienzuweisung|Name der Ressourcengruppe für das Speicherkonto für SQL Server-Überwachung|Die Überwachung schreibt Datenbankereignisse in ein Überwachungsprotokoll in Ihrem Azure Storage-Konto. (Ein Speicherkonto wird in jeder Region erstellt, in der eine SQL Server-Instanz erstellt wird, die von allen Servern in dieser Region gemeinsam genutzt wird.) Wichtig: Damit die Überwachung ordnungsgemäß funktioniert, dürfen Sie die Ressourcengruppe oder die Speicherkonten weder löschen noch umbenennen.|
|Bereitstellen von Diagnoseeinstellungen für Netzwerksicherheitsgruppen|Richtlinienzuweisung|Speicherkontopräfix für die Diagnose von Netzwerksicherheitsgruppen|Dieses Präfix wird mit dem Standort der Netzwerksicherheitsgruppe kombiniert, um den Namen für das erstellte Speicherkonto zu bilden.|
|Bereitstellen von Diagnoseeinstellungen für Netzwerksicherheitsgruppen|Richtlinienzuweisung|Ressourcengruppenname für das Speicherkonto für die Diagnose von Netzwerksicherheitsgruppen (muss vorhanden sein)|Die Ressourcengruppe, in der das Speicherkonto erstellt wird Diese Ressourcengruppe muss bereits vorhanden sein.|

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun die Schritte zur Bereitstellung des Blaupausenbeispiels „IRS 1075 (Rev.11-2016)“ angesehen haben, lesen Sie die folgenden Artikel, um mehr über die Blaupausen- und Steuerungszuordnung zu erfahren:

> [!div class="nextstepaction"]
> [IRS 1075-Blaupause – Übersicht](./index.md)
> [IRS 1075-Blaupause – Steuerungszuordnung](./control-mapping.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
