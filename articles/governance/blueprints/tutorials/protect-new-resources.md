---
title: Schützen neuer Ressourcen mit Blaupausen-Ressourcensperren
description: Hier erfahren Sie, wie Sie mit den Azure Blueprints-Ressourcensperren „Schreibgeschützt“ und „Nicht löschen“ neu bereitgestellte Ressourcen schützen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b885a90728df8cb15c75141b7bce81aec3968359
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779566"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Tutorial: Schützen neuer Ressourcen mit Azure Blueprints-Ressourcensperren

Mit Azure Blueprints-[Ressourcensperren](../concepts/resource-locking.md) können Sie neu bereitgestellte Ressourcen schützen, sodass sie nicht einmal von einem Konto mit der Rolle _Besitzer_ manipuliert werden können. Dieser Schutz kann in der Blaupausendefinition zu Ressourcen hinzugefügt werden, die mithilfe eines Resource Manager-Vorlagenartefakts erstellt wurden.

Folgende Schritte werden beschrieben:

> [!div class="checklist"]
> - Erstellen einer neuen Blaupausendefinition
> - Markieren der Blaupausendefinition als **veröffentlicht**
> - Zuweisen Ihrer Blaupausendefinition zu einem vorhandenen Abonnement
> - Überprüfen der neuen Ressourcengruppe
> - Aufheben der Zuweisung der Blaupause zum Entfernen der Sperren

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-new-blueprint-definition"></a>Erstellen einer neuen Blaupausendefinition

Erstellen Sie zunächst die neue Blaupausendefinition.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Navigieren Sie oben auf der Seite zum Blaupausenbeispiel **Leere Blaupause**, und wählen Sie **Mit leerer Blaupause beginnen** aus.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause**: Geben Sie einen Namen für Ihre Kopie des Blaupausenbeispiels ein. In diesem Tutorial wird der Name _locked-storageaccount_ verwendet.
   - **Blaupausenbeschreibung**: Beschreibt die Blaupausendefinition. Verwenden Sie beispielsweise „Zum Testen der Blaupausen-Ressourcensperre für bereitgestellte Ressourcen“.
   - **Definitionsspeicherort**: Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe oder das Abonnement aus, in der bzw. dem Sie Ihre Blaupausendefinition speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Fügen Sie unter „Abonnement“ die Ressourcengruppe hinzu: Wählen Sie unter **Abonnement** die Zeile **+ Artefakt hinzufügen...** aus.
   Wählen Sie „Ressourcengruppe“ für _Artefakttyp_. Legen Sie für _Anzeigename für Artefakt_ den Namen **RGtoLock** fest.
   Lassen Sie die Felder _Ressourcengruppenname_ und _Speicherort_ leer, aber stellen Sie sicher, dass die Kontrollkästchen aller Eigenschaften aktiviert sind, um sie zu **dynamischen Parametern** zu machen. Klicken Sie auf **Hinzufügen**, um der Blaupause dieses Artefakt hinzuzufügen.

1. Fügen Sie eine Vorlage unter der Ressourcengruppe hinzu: Wählen Sie die Zeile **+ Artefakt hinzufügen** unter dem Eintrag **RGtoLock** aus. Wählen Sie „Azure Resource Manager-Vorlage“ als _Artefakttyp_ aus, legen Sie für _Artefaktanzeigename_ „StorageAccount“ fest, und lassen Sie _Beschreibung_ leer. Fügen Sie auf der Registerkarte **Vorlage** im Editorfeld die folgende Resource Manager-Vorlage ein. Wählen Sie nach dem Einfügen der Vorlage **Hinzufügen** aus, um dieses Artefakt zur Blaupause hinzuzufügen.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

1. Klicken Sie unten auf der Seite auf **Entwurf speichern**.

In diesem Schritt wird die Blaupausendefinition in der ausgewählten Verwaltungsgruppe bzw. dem ausgewählten Abonnement erstellt.

Sobald die Portalbenachrichtigung **Blaupausendefinition erfolgreich gespeichert** angezeigt wird, fahren Sie mit dem nächsten Schritt fort.

## <a name="publish-the-blueprint-definition"></a>Veröffentlichen der Blaupausendefinition

Ihre Blaupausendefinition wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Suchen Sie mithilfe der Filter nach der Blaupausendefinition _locked-storageaccount_, und wählen Sie sie aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie im neuen Bereich auf der rechten Seite _1.0_ für **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** an, etwa „Erste Version veröffentlicht, um per Blaupause bereitgestellte Ressourcen zu sperren“. Wählen Sie dann unten auf der Seite **Veröffentlichen** aus.

In diesem Schritt kann die Blaupause einem Abonnement zugewiesen werden. Nach der Veröffentlichung können noch Änderungen vorgenommen werden. Zur Nachverfolgung der Unterschiede zwischen verschiedenen Versionen der gleichen Blaupausendefinition müssen weitere Änderungen mit einem neuen Wert für **Version** veröffentlicht werden.

Sobald die Portalbenachrichtigung **Blaupausendefinition erfolgreich veröffentlicht** angezeigt wird, fahren Sie mit dem nächsten Schritt fort.

## <a name="assign-the-blueprint-definition"></a>Zuweisen der Blaupausendefinition

Nachdem die Blaupausendefinition erfolgreich **veröffentlicht** wurde, kann sie einem Abonnement innerhalb der Verwaltungsgruppe zugewiesen werden, in der sie gespeichert wurde. In diesem Schritt werden Parameter angegeben, damit jede Bereitstellung der Blaupausendefinition eindeutig ist.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Suchen Sie mithilfe der Filter nach der Blaupausendefinition _locked-storageaccount_, und wählen Sie sie aus.

1. Wählen Sie oben auf der Seite mit der Blaupausendefinition die Option **Blaupause zuweisen** aus.

1. Geben Sie die Parameterwerte für die Blaupausenzuweisung an:

   - Grundlagen

     - **Abonnements**: Wählen Sie mindestens eines der Abonnements in der Verwaltungsgruppe aus, in der Sie die Blaupausendefinition gespeichert haben. Wenn Sie mehrere Abonnements auswählen, wird für jedes Abonnement eine Zuweisung mit den eingegebenen Parametern erstellt.
     - **Zuweisungsname**: Der Name wird basierend auf dem Namen der Blaupausendefinition vorab aufgefüllt. Diese Zuweisung soll die Sperre der neuen Ressourcengruppe darstellen. Ändern Sie daher den Namen der Zuweisung in _assignment-locked-storageaccount-TestingBPLocks_.
     - **Standort**: Wählen Sie eine Region aus, in der die verwaltete Identität erstellt werden soll. Azure Blueprint verwendet diese verwaltete Identität zum Bereitstellen aller Artefakte in der zugewiesenen Blaupause. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../active-directory/managed-identities-azure-resources/overview.md).
       Wählen Sie für dieses Tutorial die Region _USA, Osten 2_ aus.
     - **Version der Blaupausendefinition**: Wählen Sie die Version _1.0_ mit dem Status **Veröffentlicht** der Blaupausendefinition aus.

   - Zuweisung sperren

     Wählen Sie den Blaupausensperrmodus _Schreibgeschützt_ aus. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../concepts/resource-locking.md).

   - Verwaltete Identität

     Übernehmen Sie die Standardoption _Vom System zugewiesen_. Weitere Informationen finden Sie unter [Verwaltete Identitäten](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Artefaktparameter

     Die in diesem Abschnitt definierten Parameter gelten für das Artefakt, unter dem sie definiert werden. Diese Parameter sind [dynamische Parameter](../concepts/parameters.md#dynamic-parameters), da sie während der Zuweisung der Blaupause definiert werden. Legen Sie den Parameterwert für jedes Artefakt auf den Wert fest, der in der Spalte **Wert** definiert ist.

     |Artefaktname|Artefakttyp|Parametername|Wert|BESCHREIBUNG|
     |-|-|-|-|-|
     |Ressourcengruppe „RGtoLock“|Ressourcengruppe|NAME|TestingBPLocks|Definiert den Namen der neuen Ressourcengruppe, auf die Blaupausensperren angewendet werden sollen.|
     |Ressourcengruppe „RGtoLock“|Ressourcengruppe|Location|USA, Westen 2|Definiert den Standort der neuen Ressourcengruppe, auf die Blaupausensperren angewendet werden sollen.|
     |StorageAccount|Resource Manager-Vorlage|storageAccountType (StorageAccount)|Standard_GRS|Auswählen der Speicher-SKU. Der Standardwert lautet _Standard_LRS_.|

1. Nachdem Sie alle Parameter eingegeben haben, wählen Sie unten auf der Seite die Option **Zuweisen** aus.

In diesem Schritt werden die definierten Ressourcen bereitgestellt und die ausgewählte Option für **Zuweisung sperren** konfiguriert. Das Anwenden von Blaupausensperren kann bis zu 30 Minuten dauern.

Sobald die Portalbenachrichtigung **Blaupausendefinition erfolgreich zugewiesen** angezeigt wird, fahren Sie mit dem nächsten Schritt fort.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Überprüfen der durch die Zuweisung bereitgestellten Ressourcen

Die Zuweisung hat die Ressourcengruppe _TestingBPLocks_ und das vom Resource Manager-Vorlagenartefakt bereitgestellte Speicherkonto erstellt. Die neue Ressourcengruppe und der ausgewählte Sperrzustand werden auf der Seite mit den Zuweisungsdetails angezeigt.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Zugewiesene Blaupausen** aus. Suchen Sie mithilfe der Filter nach der Blaupausenzuweisung _assignment-locked-storageaccount-TestingBPLocks_, und wählen Sie sie aus.

   Auf dieser Seite sehen Sie, dass die Zuweisung erfolgreich war und die Ressourcen mit dem neuen Blaupausensperrzustand bereitgestellt wurden. Wenn die Zuweisung aktualisiert wird, werden in der Dropdownliste **Zuweisungsvorgang** Details zur Bereitstellung jeder Definitionsversion angezeigt. Durch Klicken auf die Ressourcengruppe kann direkt die Eigenschaftenseite geöffnet werden.

1. Wählen Sie die Ressourcengruppe **TestingBPLocks** aus.

1. Wählen Sie links die Seite **Zugriffssteuerung (IAM)** und dann die Registerkarte **Rollenzuweisungen** aus.

   Hier sehen Sie, dass der Blaupausenzuweisung _assignment-locked-storageaccount-TestingBPLocks_ die Rolle _Besitzer_ zugewiesen ist, da die Zuweisung zum Bereitstellen und Sperren der Ressourcengruppe verwendet wurde.

1. Wählen Sie die Registerkarte **Ablehnungszuweisungen** aus.

   Durch die Blaupausenzuweisung wurde eine [Ablehnungszuweisung](../../../role-based-access-control/deny-assignments.md) für die bereitgestellte Ressourcengruppe erstellt, um den Blaupausensperrmodus _Schreibgeschützt_ zu erzwingen. Die Ablehnungszuweisung verhindert, dass ein Benutzer mit entsprechenden Berechtigungen auf der Registerkarte _Rollenzuweisungen_ bestimmte Aktionen durchführt. Die Ablehnungszuweisung gilt für _alle Prinzipale_.

   Weitere Informationen zum Ausschließen eines Prinzipals aus einer Ablehnungszuweisung finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Wählen Sie die Ablehnungszuweisung und dann links die Seite **Abgelehnte Berechtigungen** aus.

   Die Ablehnungszuweisung verhindert alle Vorgänge mit der Konfiguration **\*** und **Aktion**, erlaubt aber den Lesezugriff, indem **\*/read** über **NotActions** ausgeschlossen wird.

1. Wählen Sie auf der Breadcrumb-Leiste im Azure-Portal **TestingBPLocks – Zugriffssteuerung (IAM)** aus. Wählen Sie dann links die Seite **Übersicht** aus, und klicken Sie anschließend auf die Schaltfläche **Ressourcengruppe löschen**. Geben Sie den Namen _TestingBPLocks_ ein, um den Löschvorgang zu bestätigen, und wählen Sie am unteren Rand des Bereichs die Option **Löschen** aus.

   Die Portalbenachrichtigung **Fehler beim Löschen der Ressourcengruppe „TestingBPLocks“** wird angezeigt. Dieser Fehler weist darauf hin, dass Ihr Konto zwar über die Berechtigung zum Löschen der Ressourcengruppe verfügt, der Zugriff aber durch die Blaupausenzuweisung verweigert wird. Wie Sie sich erinnern, haben Sie während der Blaupausenzuweisung den Blaupausensperrmodus _Schreibgeschützt_ ausgewählt. Aufgrund der Blaupausensperre kann die Ressource weder von einem Konto mit der entsprechenden Berechtigung noch vom _Besitzer_ gelöscht werden. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../concepts/resource-locking.md).

Diese Schritte zeigen, dass die bereitgestellten Ressourcen nun durch Blaupausensperren geschützt sind, die ungewollte Löschungen verhindern (auch durch ein Konto mit der entsprechenden Berechtigung).

## <a name="unassign-the-blueprint"></a>Aufheben der Zuweisung der Blaupause

Im letzten Schritt wird die Zuweisung der Blaupausendefinition aufgehoben. Durch das Aufheben der Zuweisung werden die verwendeten Artefakte nicht entfernt.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Zugewiesene Blaupausen** aus. Suchen Sie mithilfe der Filter nach der Blaupausenzuweisung _assignment-locked-storageaccount-TestingBPLocks_, und wählen Sie sie aus.

1. Klicken Sie oben auf der Seite auf die Schaltfläche **Zuweisung der Blaupause aufheben**. Lesen Sie die Warnung im Bestätigungsdialogfeld, und klicken Sie dann auf **OK**.

   Durch das Entfernen der Blaupausenzuweisung werden auch die Blaupausensperren aufgehoben. Die erstellten Ressourcen können von einem Konto mit entsprechenden Berechtigungen wieder gelöscht werden.

1. Wählen Sie im Azure-Menü die Option **Ressourcengruppen** und dann **TestingBPLocks** aus.

1. Wählen Sie links die Seite **Zugriffssteuerung (IAM)** und dann die Registerkarte **Rollenzuweisungen** aus.

In den Sicherheitseinstellungen für die Ressourcengruppe sehen Sie, dass die Blaupausenzuweisung nicht mehr über den Zugriff _Besitzer_ verfügt.

Sobald die Portalbenachrichtigung **Blaupausenzuweisung erfolgreich entfernt** angezeigt wird, fahren Sie mit dem nächsten Schritt fort.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie nach Abschluss dieses Tutorials die folgenden Ressourcen:

- Ressourcengruppe _TestingBPLocks_
- Blaupausendefinition _locked-storageaccount_

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../concepts/resource-locking.md) können.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../concepts/sequencing-order.md) anpassen können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).
- Beheben Sie Probleme bei der Blaupausenzuweisung mithilfe des [allgemeinen Leitfadens zur Problembehandlung](../troubleshoot/general.md).