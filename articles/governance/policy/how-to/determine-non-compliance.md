---
title: Ermitteln der Ursachen für Nichtkonformität
description: Wenn eine Ressource nicht konform ist, kann das viele mögliche Ursachen haben. Erfahren Sie, wie Sie die Ursache für die Nichtkonformität ermitteln können.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fb7f238bb5c04bb03ee500b1b953895cc88c0596
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298924"
---
# <a name="determine-causes-of-non-compliance"></a>Ermitteln der Ursachen für Nichtkonformität

Wenn festgestellt wird, dass eine Azure-Ressource nicht konform mit einer Richtlinienregel ist, ist es wichtig zu bestimmen, gegen welchen Teil der Regel die Ressource verstößt. Außerdem ist es ebenso nützlich, ermitteln zu können, welche Änderung dazu geführt hat, dass eine ehemals konforme Ressource nun nicht mehr konform ist. Es gibt zwei Möglichkeiten, diese Informationen zu bestimmen:

> [!div class="checklist"]
> - [Konformitätsdetails](#compliance-details)
> - [Änderungsverlauf (Vorschau)](#change-history-preview)

## <a name="compliance-details"></a>Konformitätsdetails

Wenn eine Ressource nicht konform ist, können Sie über die Seite **Richtlinienkonformität** auf die Konformitätsdetails dieser Ressource zugreifen. Der Bereich mit den Kompatibilitätsdetails enthält die folgenden Informationen:

- Angaben zur Ressource wie der Name, der Typ, der Speicherort und die Ressourcen-ID
- Konformitätsstatus und Zeitstempel der letzten Evaluierung für die aktuelle Richtlinienzuweisung
- Eine Liste mit _Gründen_ für die Nichtkonformität der Ressource

> [!IMPORTANT]
> Die Konformitätsdetails einer _nicht konformen_ Ressource enthalten die aktuellen Werte der Eigenschaften der Ressource. Deshalb muss der **Benutzer über Leseberechtigungen** für den **Ressourcentyp** verfügen. Wenn die _nicht konforme_ Ressource z.B. vom Typ **Microsoft.Compute/virtualMachines** ist, muss der Leser über die Berechtigung **Microsoft.Compute/virtualMachines/read** verfügen. Wenn der Benutzer nicht über die erforderliche Berechtigung verfügt, wird ein Zugriffsfehler angezeigt.

Führen Sie die folgenden Schritte durch, um die Konformitätsdetails anzuzeigen:

1. Starten Sie den Azure Policy-Dienst über das Azure-Portal, indem Sie auf **Alle Dienste** klicken und dann nach **Richtlinie** suchen und die entsprechende Option auswählen.

1. Wählen Sie auf der Seite **Übersicht** oder **Konformität** eine Richtlinie mit dem **Konformitätsstatus** _Nicht konform_ aus.

1. Öffnen Sie auf der Seite **Richtlinienkonformität** auf der Registerkarte **Ressourcenkonformität** das Kontextmenü, oder klicken Sie auf die Auslassungspunkte neben einer Ressource mit dem **Konformitätsstatus** _Nicht konform_. Wählen Sie dann **Konformitätsdetails anzeigen** aus.

   ![Option „Konformitätsdetails anzeigen“](../media/determine-non-compliance/view-compliance-details.png)

1. Im Bereich **Konformitätsdetails** werden Informationen der letzten Auswertung der Ressource mit der aktuellen Richtlinienzuweisung angezeigt. In diesem Beispiel enthält das Feld **Microsoft.Sql/servers/version** _12.0_, und die Richtliniendefinition hat _14.0_ erwartet. Wenn es mehrere Gründen für die Nichtkonformität der Ressource gibt, werde diese alle in diesem Bereich aufgeführt.

   ![Bereich „Konformitätsdetails“ und Gründe für fehlende Konformität](../media/determine-non-compliance/compliance-details-pane.png)

   Für **auditIfNotExists**- oder **deployIfNotExists**-Richtliniendefinitionen enthalten die Details die Eigenschaft **details.type** und alle optionalen Eigenschaften. Eine Liste finden Sie unter [„Grundlegendes zu Azure Policy-Auswirkungen“ im Abschnitt „Eigenschaften von „auditIfNotExists“](../concepts/effects.md#auditifnotexists-properties) und unter [„Grundlegendes zu Azure Policy-Auswirkungen“ im Abschnitt „Eigenschaften von „DeployIfNotExists“](../concepts/effects.md#deployifnotexists-properties). **Zuletzt ausgewertete Ressource** ist eine verwandte Ressource aus dem **Detailabschnitt** der Definition.

   Hier sehen Sie einen Beispielausschnitt aus der Definition von **deployIfNotExists**:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![Bereich „Konformitätsdetails“, *ifnotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Der aktuelle Wert zeigt Asteriske an, wenn ein Eigenschaftswert ein _Geheimnis_ ist, um Daten zu schützen.

In den Details wird angegeben, warum eine Ressource nicht konform ist. Allerdings wird nicht angegeben, wann eine Änderung an der Ressource vorgenommen wurde, die dazu geführt hat, dass sie nicht mehr konform ist. Zu diesen Informationen erfahren Sie im Abschnitt [Änderungsverlauf (Vorschau)](#change-history-preview) mehr.

### <a name="compliance-reasons"></a>Konformitätsgründe

In der folgenden Tabelle wird jeder mögliche _Grund_ der entsprechenden [Bedingung](../concepts/definition-structure.md#conditions) in der Richtliniendefinition zugeordnet:

|`Reason` | Bedingung |
|-|-|
|Der aktuelle Wert muss den Zielwert als Schlüssel enthalten. |containsKey oder **nicht** notContainsKey |
|Der aktuelle Wert muss den Zielwert enthalten. |contains oder **nicht** notContains |
|Der aktuelle Wert muss gleich dem Zielwert sein. |equals der **nicht** notEquals |
|Der aktuelle Wert muss kleiner als der Zielwert sein. |less oder **nicht** greaterOrEquals |
|Der aktuelle Wert muss größer oder gleich dem Zielwert sein. |greaterOrEquals oder **nicht** less |
|Der aktuelle Wert muss größer als der Zielwert sein. |greater oder **nicht** lessOrEquals |
|Der aktuelle Wert muss kleiner oder gleich dem Zielwert sein. |lessOrEquals oder **nicht** greater |
|Der aktuelle Wert muss vorhanden sein. |exists |
|Der aktuelle Wert muss im Zielwert enthalten sein. |in der **nicht** notIn |
|Der aktuelle Wert muss dem Zielwert entsprechen. |like oder **nicht** notLike |
|Der aktuelle Wert muss mit dem Zielwert übereinstimmen (Berücksichtigung der Groß-/Kleinschreibung). |match oder **nicht** notMatch |
|Der aktuelle Wert muss mit dem Zielwert übereinstimmen (keine Berücksichtigung der Groß-/Kleinschreibung). |matchInsensitively oder **nicht** notMatchInsensitively |
|Der aktuelle Wert darf den Zielwert nicht als Schlüssel enthalten. |notContainsKeyontainsKey oder **nicht** ContainsKey|
|Der aktuelle Wert darf nicht den Zielwert enthalten. |notContains oder **nicht** contains |
|Der aktuelle Wert darf nicht gleich dem Zielwert sein. |notEquals der **nicht** equals |
|Der aktuelle Wert darf nicht vorhanden sein. |**nicht** exists  |
|Der aktuelle Wert darf nicht im Zielwert enthalten sein. |notIn oder **nicht** in |
|Der aktuelle Wert darf nicht dem Zielwert entsprechen. |notLike oder **nicht** like |
|Der aktuelle Wert darf nicht mit dem Zielwert übereinstimmen (Berücksichtigung der Groß-/Kleinschreibung). |notMatch oder **nicht** match |
|Der aktuelle Wert darf nicht mit dem Zielwert übereinstimmen (keine Berücksichtigung der Groß-/Kleinschreibung). |notMatchInsensitively oder **nicht** matchInsensitively |
|Keine der zugehörigen Ressourcen entspricht den Effektdetails in der Richtliniendefinition. |Eine Ressource des in **then.details.type** definierten Typs, die mit der im **if**-Abschnitt der Richtlinienregel definierten Ressource verwandt ist, ist nicht vorhanden. |

## <a name="compliance-details-for-guest-configuration"></a>Details zur Konformität für die Gastkonfiguration

Für Überwachungsrichtlinien (_audit_) in der Kategorie _Gastkonfiguration_ können auf der VM mehrere Einstellungen ausgewertet werden, und Sie müssen die Details pro Einstellung anzeigen. Wenn Sie beispielsweise eine Überprüfung einer Liste mit installierten Anwendungen durchführen und der Zuweisungsstatus _Nicht konform_ lautet, müssen Sie genau wissen, welche Anwendungen fehlen.

Unter Umständen haben Sie auch keinen Zugriff für die direkte Anmeldung auf der VM, müssen aber melden, warum die VM _nicht konform_ ist. Sie können beispielsweise überprüfen, ob die VMs in die richtige Domäne eingebunden sind und in den Berichtsdetails die aktuelle Domänenmitgliedschaft enthalten.

### <a name="azure-portal"></a>Azure-Portal

1. Starten Sie den Azure Policy-Dienst über das Azure-Portal, indem Sie auf **Alle Dienste** klicken und dann nach **Richtlinie** suchen und die entsprechende Option auswählen.

1. Wählen Sie auf der Seite **Übersicht** oder **Kompatibilität** eine Richtlinienzuweisung für alle Initiativen aus, die für die Gastkonfiguration eine _nicht konforme_ Richtliniendefinition enthalten.

1. Wählen Sie für die Initiative, die _nicht konform_ ist, eine _Überwachungsrichtlinie_ aus.

   ![Anzeigen von Details zur Überwachungsdefinition](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. Auf der Registerkarte **Ressourcenkonformität** sind die folgenden Informationen angegeben:

   - **Name**: Der Name der Zuweisungen für die Gastkonfiguration.
   - **Übergeordnete Ressource**: Der virtuelle Computer mit dem Status _Nicht konform_ für die ausgewählte Zuweisung für die Gastkonfiguration.
   - **Ressourcentyp**: Der vollständige _guestConfigurationAssignments_-Name.
   - **Letzte Auswertung**: Der letzte Zeitpunkt, zu dem der Dienst „Gastkonfiguration“ Azure Policy über den Status des virtuellen Zielcomputers informiert hat.

   ![Anzeigen von Konformitätsdetails](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. Wählen Sie den Namen der Gastkonfigurationszuweisung in der Spalte **Name** aus, um die Seite **Ressourcenkonformität** zu öffnen.

1. Wählen Sie oben auf der Seite die Schaltfläche **Ressource anzeigen**, um die Seite **Gastzuweisung** zu öffnen.

Auf der Seite **Gastzuweisung** werden alle verfügbaren Konformitätsdetails angezeigt. Jede Zeile der Ansicht steht für eine Auswertung, die auf dem virtuellen Computer durchgeführt wurde. In der Spalte **Grund** wird eine Beschreibung angezeigt, mit der der Grund für den Status _Nicht konform_ für die Gastzuweisung angegeben wird. Falls die Überwachung beispielsweise ergibt, dass VMs in eine Domäne eingebunden werden sollten, wird in der Spalte **Grund** Text angezeigt, der auch die aktuelle Domänenmitgliedschaft enthält.

![Anzeigen von Konformitätsdetails](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Sie können Konformitätsdetails auch über Azure PowerShell anzeigen. Stellen Sie zuerst sicher, dass Sie das Modul „Gastkonfiguration“ installiert haben.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Sie können den aktuellen Status aller Gastzuweisungen für eine VM anzeigen, indem Sie den folgenden Befehl verwenden:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Geben Sie nur die untergeordnete Reason-Eigenschaft zurück, um nur den Text von _Grund_ anzuzeigen, mit dem der Grund für den Status _Nicht konform_ angegeben wird.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Sie können auch einen Konformitätsverlauf für Gastzuweisungen des virtuellen Computers ausgeben. Die Ausgabe dieses Befehls enthält die Details aller Berichte für die VM.

> [!NOTE]
> Die Ausgabe kann eine große Datenmenge umfassen. Wir empfehlen Ihnen, die Ausgabe in einer Variablen zu speichern.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Verwenden Sie den Parameter **ShowChanged**, um diese Ansicht zu vereinfachen. Die Ausgabe dieses Befehls enthält nur die Berichte, die nach einer Änderung des Konformitätsstatus erstellt wurden.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Änderungsverlauf (Vorschau)

Im Rahmen einer neuen **öffentlichen Vorschau** sind die letzten 14 Tage des Änderungsverlaufs für alle Azure-Ressourcen verfügbar, die die [Löschung im vollständigen Modus](../../../azure-resource-manager/complete-mode-deletion.md) unterstützen. Der Änderungsverlauf enthält Details dazu, wann eine Änderung erkannt wurde, und eine _Visual Diff_ jeder Änderung. Eine Änderungserkennung wird ausgelöst, wenn Resource Manager-Eigenschaften hinzugefügt, entfernt oder geändert werden.

1. Starten Sie den Azure Policy-Dienst über das Azure-Portal, indem Sie auf **Alle Dienste** klicken und dann nach **Richtlinie** suchen und die entsprechende Option auswählen.

1. Wählen Sie auf der Seite **Übersicht** oder **Konformität** eine Richtlinie mit einem beliebigen **Konformitätsstatus** aus.

1. Wählen Sie auf der Seite **Richtlinienkonformität** auf der Registerkarte **Ressourcenkonformität** eine Ressource aus.

1. Wählen Sie auf der Seite **Ressourcenkonformität** die Registerkarte **Änderungsverlauf (Vorschau)** aus. Eine Liste der erkannten Änderungen, falls vorhanden, wird angezeigt.

   ![Azure Policy-Registerkarte „Änderungsverlauf“ auf der Seite „Ressourcenkonformität“](../media/determine-non-compliance/change-history-tab.png)

1. Wählen Sie eine der erkannten Änderungen aus. Die _Visual Diff_ für die Ressource wird auf der Seite **Änderungsverlauf** angezeigt.

   ![„Visual Diff“ des Azure Policy-Änderungsverlaufs auf der Seite „Änderungsverlauf“](../media/determine-non-compliance/change-history-visual-diff.png)

Die _Visual Diff_ hilft, Änderungen an einer Ressource zu identifizieren. Die erkannten Änderungen müssen nicht zwangsläufig mit dem geänderten Konformitätsstatus der Ressource zusammenhängen.

Die Verlaufsdaten zu Änderungen werden von [Azure Resource Graph](../../resource-graph/overview.md) bereitgestellt. Wie Sie diese Informationen außerhalb des Azure-Portals abfragen, erfahren Sie unter [Get resource changes (Abrufen von Ressourcenänderungen)](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](getting-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).