---
title: Verwalten von Log Analytics-Arbeitsbereichen in Azure Monitor | Microsoft-Dokumentation
description: Sie können Log Analytics-Arbeitsbereiche in Azure Monitor verwalten, indem Sie verschiedene Verwaltungsaufgaben für Benutzer, Konten, Arbeitsbereiche und Azure-Konten verwenden.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: 27db27d79a05f24461e63242c0395cfd81315432
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60782768"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Verwalten von Protokolldaten und Arbeitsbereichen in Azure Monitor
In Azure Monitor werden Protokolldaten in einem Log Analytics-Arbeitsbereich gespeichert, bei dem es sich im Wesentlichen um einen Container handelt, der Daten und Konfigurationsinformationen enthält. Zum Verwalten des Zugriffs auf Protokolldaten führen Sie verschiedene Verwaltungsaufgaben für Arbeitsbereiche durch. Sie oder andere Mitglieder Ihrer Organisation können mehrere Arbeitsbereiche nutzen, um unterschiedliche Mengen von Daten zu verwalten, die in Ihrer gesamten IT-Infrastruktur oder Teilen davon erfasst werden.

In diesem Artikel wird erläutert, wie der Zugriff auf Protokolle verwaltet wird und wie die Arbeitsbereiche verwaltet werden, die diese enthalten. 

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs
Zum Erstellen eines Log Analytics-Arbeitsbereichs benötigen Sie Folgendes:

1. Ein Azure-Abonnement
2. Einen Namen für den Arbeitsbereich
3. Zuordnung des Arbeitsbereichs zu einem Ihrer Abonnements und einer Ihrer Ressourcengruppen
4. Ausgewählten geografischen Standort

Weitere Informationen zum Erstellen eines Arbeitsbereichs finden Sie in den folgenden Artikeln:

- [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../learn/quick-create-workspace.md)
- [Erstellen eines Log Analytics-Arbeitsbereichs mit der Azure CLI 2.0](../learn/quick-create-workspace-cli.md)
- [Erstellen eines Log Analytics-Arbeitsbereichs mit Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Bestimmen der benötigten Anzahl von Arbeitsbereichen
Ein Log Analytics-Arbeitsbereich ist eine Azure-Ressource. Es handelt sich hierbei um einen Container, in dem Daten gesammelt, aggregiert, analysiert und in Azure Monitor angezeigt werden. Sie können mehrere Arbeitsbereiche pro Azure-Abonnement verwenden und über den Zugriff auf mehr als einen Arbeitsbereich mit einfacher Abfragemöglichkeit verfügen. In diesem Abschnitt wird beschrieben, wann es hilfreich sein kann, mehr als einen Arbeitsbereich zu erstellen.

Ein Log Analytics-Arbeitsbereich bietet Folgendes:

* Einen geografischen Standort für die Speicherung von Daten.
* Datenisolation zum Definieren unterschiedlicher Benutzerzugriffsrechte im arbeitsbereichbezogenen Modus. Nicht relevant beim Arbeiten im ressourcenbezogenen Modus.
* Bereich für die Konfiguration von Einstellungen wie [Tarif](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [Aufbewahrung](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) und [Datenobergrenzen](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Kosten im Zusammenhang mit der Datenerfassung und -Aufbewahrung fallen für die Arbeitsbereichsressource an.

Im Hinblick auf die Nutzung wird empfohlen, möglichst wenige Arbeitsbereiche zu erstellen. Dadurch werden Verwaltung und Abfragen einfacher und schneller. Auf der Grundlage der obigen Merkmale können Sie jedoch in folgenden Szenarien mehrere Arbeitsbereiche erstellen:

* Sie sind ein globales Unternehmen und müssen Protokolldaten aus Gründen der Datensouveränität bzw. aus Compliancegründen in bestimmten Regionen speichern.
* Sie nutzen Azure und möchten Gebühren für ausgehende Datenübertragungen vermeiden, indem Sie einen Arbeitsbereich in derselben Region wie die verwalteten Azure-Ressourcen nutzen.
* Sie sind ein Dienstanbieter mit Verwaltung und müssen die Log Analytics-Daten für jeden Kunden, den Sie verwalten, von den Daten der anderen Kunden isolieren.
* Sie verwalten mehrere Kunden und möchten, dass jeder Kunde, jede Abteilung und jeder Geschäftsbereich seine eigenen Daten sieht, aber keine Daten von anderen, und es besteht kein Bedarf für eine konsolidierte kunden-/abteilungs-/geschäftsgruppenübergreifende Ansicht.

Wenn Sie Windows-Agents zum Sammeln von Daten verwenden, können Sie [jeden Agent so konfigurieren, dass er Informationen zu mindestens einem Arbeitsbereich meldet](../../azure-monitor/platform/agent-windows.md).

Bei Verwendung von System Center Operations Manager kann jede Operations Manager-Verwaltungsgruppe mit nur einem Arbeitsbereich verbunden werden. Sie können den Microsoft Monitoring Agent auf Computern installieren, die mit Operations Manager verwaltet werden, und den Agent so einrichten, dass er sowohl Daten an Operations Manager als auch an einen anderen Log Analytics-Arbeitsbereich liefert.

Sobald die Arbeitsbereichsarchitektur definiert ist, sollten Sie diese Richtlinie für Azure-Ressourcen mit [Azure Policy](../../governance/policy/overview.md) durchsetzen. Dies kann eine integrierte Definition bereitstellen, die automatisch für alle Azure-Ressourcen gilt. Sie können beispielsweise eine Richtlinie festlegen, um sicherzustellen, dass alle Ihre Azure-Ressourcen in einer bestimmten Region alle ihre Diagnoseprotokolle an einen bestimmten Arbeitsbereich senden.

## <a name="view-workspace-details"></a>Anzeigen von Arbeitsbereichsdetails
Sie analysieren die Daten in Ihrem Log Analytics-Arbeitsbereich über das Menü **Azure Monitor** im Azure-Portal, die Arbeitsbereiche erstellen und verwalten Sie jedoch im Menü **Log Analytics-Arbeitsbereiche**.
 

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics-Arbeitsbereiche** aus.  

    ![Azure-Portal](media/manage-access/azure-portal-01.png)  

3. Wählen Sie Ihren Arbeitsbereich in der Liste aus.

4. Auf der Seite für den Arbeitsbereich werden Details zum Arbeitsbereich, zu den ersten Schritten und zur Konfiguration sowie Links zu weiteren Informationen angezeigt.  

    ![Informationen zum Arbeitsbereich](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Arbeitsbereichsberechtigungen und Bereich
Die Daten, auf die ein Benutzer Zugriff besitzt, werden durch mehrere Faktoren bestimmt, die in der folgenden Tabelle aufgeführt sind. Jeder dieser Faktoren wird in den folgenden Abschnitten beschrieben.

| Faktor | BESCHREIBUNG |
|:---|:---|
| [Zugriffsmodus](#access-modes) | Die Methode, die der Benutzer verwendet, um auf den Arbeitsbereich zuzugreifen.  Definiert den Bereich der verfügbaren Daten und den Zugriffssteuerungsmodus, der angewendet wird. |
| [Zugriffssteuerungsmodus](#access-control-mode) | Eine Einstellung für den Arbeitsbereich, die definiert, ob Berechtigungen auf der Arbeitsbereich- oder Ressourcenebene angewendet werden. |
| [Berechtigungen](#manage-accounts-and-users) | Berechtigungen, die auf einzelne Benutzer oder Gruppen von Benutzern für den Arbeitsbereich oder die Ressource angewendet werden. Definiert, auf welche Daten der Benutzer zugreifen kann. |
| [RBAC auf Tabellenebene](#table-level-rbac) | Optionale präzise Berechtigungen, die für alle Benutzer unabhängig von deren Zugriffs- oder Zugriffssteuerungsmodus gelten. Definiert die Datentypen, auf die ein Benutzer zugreifen kann. |



## <a name="access-modes"></a>Zugriffsmodi
Der _Zugriffsmodus_ bezieht sich darauf, wie ein Benutzer auf einen Log Analytics-Arbeitsbereich zugreift. Er definiert den Umfang der Daten, auf die zugegriffen werden kann. 

**Arbeitsbereichbezogen**: In diesem Modus kann ein Benutzer alle Protokolle im Arbeitsbereich anzeigen, für die er Berechtigungen besitzt. Abfragen in diesem Modus beziehen sich auf alle Daten in allen Tabellen im Arbeitsbereich. Dies ist der Zugriffsmodus, der verwendet wird, wenn auf Protokolle mit dem Arbeitsbereich als Bereich zugegriffen wird, z.B. wenn Sie **Protokolle** aus dem Menü **Azure Monitor** im Azure-Portal auswählen.

**Ressourcenbezogen**: Wenn Sie auf den Arbeitsbereich für eine bestimmte Ressource zugreifen (z.B. wenn Sie **Protokolle** aus einem Ressourcenmenü im Azure-Portal auswählen), können Sie Protokolle nur für diese Ressource in allen Tabellen anzeigen, auf die Sie Zugriff besitzen. Abfragen in diesem Modus beziehen sich auf nur Daten, die dieser Ressource zugeordnet sind. Dieser Modus ermöglicht auch differenzierte rollenbasierte Zugriffssteuerung (RBAC). 

> [!NOTE]
> Protokolle sind für ressourcenbezogene Abfragen nur dann verfügbar, wenn sie der jeweiligen Ressource ordnungsgemäß zugeordnet wurden. Zurzeit gelten für die folgenden Ressourcen Einschränkungen: 
> - Computer außerhalb von Azure
> - Service Fabric
> - Application Insights
> - Container
>
> Sie können testen, ob Protokolle ordnungsgemäß ihrer Ressource zugeordnet wurden, indem Sie eine Abfrage ausführen und die Datensätze überprüfen, an denen Sie interessiert sind. Wenn sich die richtige Ressourcen-ID in der Eigenschaft [_ResourceId](log-standard-properties.md#_resourceid) befindet, stehen Daten für ressourcenbezogene Abfragen zur Verfügung.

### <a name="comparing-access-modes"></a>Vergleichen von Zugriffsmodi

Die Zugriffsmodi werden in der folgenden Tabelle zusammengefasst:

| | Arbeitsbereichbezogen | Ressourcenbezogen |
|:---|:---|:---|
| Für wen ist das jeweilige Modell vorgesehen? | Zentraladministration. Administratoren, die die Datensammlung konfigurieren müssen, und Benutzer, die Zugriff auf eine Vielzahl von Ressourcen benötigen. Zurzeit auch erforderlich für Benutzer, die auf Protokolle für Ressourcen außerhalb von Azure zugreifen müssen. | Anwendungsteams. Administratoren von Azure-Ressourcen, die überwacht werden. |
| Was ist für einen Benutzer erforderlich, um Protokolle anzuzeigen? | Berechtigungen für den Arbeitsbereich. Weitere Informationen finden Sie unter **Arbeitsbereichberechtigungen** in [Verwalten von Konten und Benutzern](#manage-accounts-and-users). | Lesezugriff auf die Ressource. Weitere Informationen finden Sie unter **Ressourcenberechtigungen** in [Verwalten von Konten und Benutzern](#manage-accounts-and-users). Berechtigungen können vererbt (z.B. aus der enthaltenden Ressourcengruppe) oder der Ressource direkt zugeordnet werden. Die Berechtigung für die Protokolle für die Ressource wird automatisch zugewiesen. |
| Welchen Geltungsbereich haben Berechtigungen? | Den Arbeitsbereich. Benutzer mit Zugriff auf den Arbeitsbereich können alle Protokolle in diesem Arbeitsbereich aus Tabellen abfragen, für die sie über Berechtigungen verfügen. Siehe [Tabellenzugriffssteuerung](#table-level-rbac). | Die Azure-Ressource. Der Benutzer kann Protokolle für Ressourcen aus jedem Arbeitsbereich abfragen, auf die er Zugriff besitzt, aber nicht Protokolle für andere Ressourcen. |
| Wie kann der Benutzer auf Protokolle zugreifen? | Starten Sie **Protokolle** aus dem Menü **Azure Monitor** oder über **Log Analytics-Arbeitsbereiche**. | Starten Sie **Protokolle** aus dem Menü für die Azure-Ressource. |


## <a name="access-control-mode"></a>Zugriffssteuerungsmodus
Der _Zugriffssteuerungsmodus_ ist eine Einstellung für jeden Arbeitsbereich, die definiert, wie Berechtigungen für diesen Arbeitsbereich bestimmt werden.

**Arbeitsbereichsberechtigungen erforderlich**:  Dieser Steuerungsmodus ermöglicht keine differenzierte rollenbasierte Zugriffssteuerung (RBAC). Damit ein Benutzer auf den Arbeitsbereich zugreifen kann, müssen ihm Berechtigungen für den Arbeitsbereich oder für bestimmte Tabellen gewährt werden. 

Wenn ein Benutzer auf den Arbeitsbereich im arbeitsbereichbezogenen Modus zugreift, besitzt er Zugriff auf alle Daten und alle Tabellen, für die ihm Zugriff erteilt wurde. Wenn ein Benutzer auf den Arbeitsbereich im ressourcenbezogenen Modus zugreift, besitzt er nur Zugriff auf Daten für diese Ressource in Tabellen, für die ihm Zugriff erteilt wurde.

Dies ist die Standardeinstellung für alle Arbeitsbereiche, die vor März 2019 erstellt wurden.

**Ressourcen- oder Arbeitsbereichsberechtigungen verwenden**: Dieser Steuerungsmodus ermöglicht differenzierte rollenbasierte Zugriffssteuerung (RBAC). Benutzern wird der Zugriff nur auf Daten gewährt, die mit Ressourcen verknüpft sind, die sie über Azure-Berechtigungen anzeigen können, für die sie über die Berechtigung `read` verfügen. 

Wenn ein Benutzer auf den Arbeitsbereich im arbeitsbereichbezogenen Modus zugreift, gelten Arbeitsbereichberechtigungen. Wenn ein Benutzer im ressourcenbezogenen Modus auf den Arbeitsbereich zugreift, werden nur die Ressourcenberechtigungen überprüft, und die Arbeitsbereichsberechtigungen werden ignoriert. Aktivieren Sie RBAC für einen Benutzer, indem Sie ihn aus den Arbeitsbereichberechtigungen entfernen und zulassen, dass seine Ressourcenberechtigungen erkannt werden.

Dies ist die Standardeinstellung für alle Arbeitsbereiche, die nach März 2019 erstellt werden.

> [!NOTE]
> Wenn ein Benutzer nur Ressourcenberechtigungen für den Arbeitsbereich besitzt, kann er nur im [ressourcenbezogenen Modus](#access-modes) auf den Arbeitsbereich zugreifen.


### <a name="define-access-control-mode-in-azure-portal"></a>Definieren des Zugriffssteuerungsmodus im Azure-Portal
Sie können den aktuellen Zugriffssteuerungsmodus für den Arbeitsbereich auf der Seite **Übersicht** für den Arbeitsbereich im Menü **Log Analytics-Arbeitsbereich** anzeigen.

![Anzeigen des Zugriffssteuerungsmodus für den Arbeitsbereich](media/manage-access/view-access-control-mode.png)

Sie können diese Einstellung auf der Seite **Eigenschaften** für den Arbeitsbereich ändern. Das Ändern der Einstellung ist deaktiviert, wenn Sie keine Berechtigungen zum Konfigurieren des Arbeitsbereichs besitzen.

![Ändern des Arbeitsbereichzugriffsmodus](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Definieren des Zugriffssteuerungsmodus in PowerShell

Verwenden Sie den folgenden Befehl, um den Zugriffssteuerungsmodus für alle Arbeitsbereiche im Abonnement zu überprüfen:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Verwenden Sie das folgende Skript, um den Zugriffssteuerungsmodus für einen bestimmten Arbeitsbereich festzulegen:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Verwenden Sie das folgende Skript, um den Zugriffssteuerungsmodus für alle Arbeitsbereiche im Abonnement festzulegen:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Definieren des Zugriffsmodus in einer Resource Manager-Vorlage
Um den Zugriffsmodus in einer Azure Resource Manager-Vorlage zu konfigurieren, legen Sie das Featureflag **enableLogAccessUsingOnlyResourcePermissions** für den Arbeitsbereich auf einen der folgenden Werte fest.

- **FALSE**: Legen Sie den Arbeitsbereich auf arbeitsbereichbezogene Berechtigungen fest. Dies ist die Standardeinstellung, wenn das Flag nicht festgelegt ist.
- **TRUE**: Legen Sie den Arbeitsbereich auf ressourcenbezogene Berechtigungen fest.


## <a name="manage-accounts-and-users"></a>Verwalten von Konten und Benutzern
Die Berechtigungen für den Arbeitsbereich, die auf einen bestimmten Benutzer angewendet werden, werden durch seinen Zugriffsmodus und den [Zugriffssteuerungsmodus](#access-control-mode) des Arbeitsbereichs definiert. **Arbeitsbereichberechtigungen** werden angewendet, wenn ein Benutzer auf einen Arbeitsbereich mithilfe von **Arbeitsbereichbezogen** im [arbeitsbereichbezogenen Modus](#access-modes) zugreift. **Ressourcenberechtigungen** werden angewendet, wenn ein Benutzer auf einen Arbeitsbereich mit **Ressourcen- oder Arbeitsbereichsberechtigungen verwenden** im [Zugriffssteuerungsmodus](#access-control-mode) mit dem [resourcenbezogenen Modus](#access-modes) zugreift.

### <a name="workspace-permissions"></a>Arbeitsbereichberechtigungen
Jedem Arbeitsbereich können mehrere Konten zugeordnet werden, und jedes Konto kann Zugriff auf mehrere Arbeitsbereiche haben. Der Zugriff wird über die [rollenbasierte Zugriffssteuerung in Azure](../../role-based-access-control/role-assignments-portal.md) verwaltet. 


Für die folgenden Aktivitäten sind ebenfalls Azure-Berechtigungen erforderlich:

| Aktion                                                          | Azure-Berechtigungen erforderlich | Notizen |
|-----------------------------------------------------------------|--------------------------|-------|
| Hinzufügen und Entfernen von Überwachungslösungen                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Diese Berechtigungen müssen auf der Ressourcengruppen- oder Abonnementebene gewährt werden. |
| Ändern des Tarifs                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Anzeigen von Daten auf den Kacheln der *Backup*- und *Site Recovery*-Lösungen | Administrator/Co-Administrator | Zugriff auf Ressourcen, die mit dem klassischen Bereitstellungsmodell bereitgestellt werden |
| Erstellen eines Arbeitsbereichs im Azure-Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Verwalten des Zugriffs auf den Log Analytics-Arbeitsbereich mit Azure-Berechtigungen 
Führen Sie die Schritte unter [Verwenden von Rollenzuweisungen zum Verwalten Ihrer Azure-Abonnementressourcen](../../role-based-access-control/role-assignments-portal.md) aus, um den Zugriff auf den Log Analytics-Arbeitsbereich mit Azure-Berechtigungen zu gewähren.

Azure verfügt über zwei integrierte Benutzerrollen für Log Analytics-Arbeitsbereiche:
- Log Analytics-Leser
- Log Analytics-Mitwirkender

Mitglieder der Rolle *Log Analytics-Leser* können folgende Aktionen ausführen:
- Anzeigen und Durchsuchen aller Überwachungsdaten 
- Anzeigen von Überwachungseinstellungen (einschließlich der Konfiguration von Azure-Diagnosen für alle Azure-Ressourcen)

Die Rolle „Log Analytics-Leser“ umfasst die folgenden Azure-Aktionen:

| Type    | Berechtigung | BESCHREIBUNG |
| ------- | ---------- | ----------- |
| Aktion | `*/read`   | Anzeigen aller Azure-Ressourcen und der Ressourcenkonfiguration, einschließlich: <br> VM-Erweiterungsstatus <br> Konfiguration von Azure-Diagnosen für Ressourcen <br> Sämtliche Eigenschaften und Einstellungen aller Ressourcen |
| Aktion | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Ausführen von Protokollsuchabfragen (v2) |
| Aktion | `Microsoft.OperationalInsights/workspaces/search/action` | Ausführen von Protokollsuchabfragen (v1) |
| Aktion | `Microsoft.Support/*` | Öffnen von Supportfällen |
|Keine Aktion | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Verhindert das Lesen des Arbeitsbereichsschlüssels, der zum Verwenden der Datensammlungs-API und zum Installieren von Agents erforderlich ist. Dadurch wird verhindert, dass Benutzer dem Arbeitsbereich neue Ressourcen hinzufügen. |


Mitglieder der Rolle *Log Analytics-Mitwirkender* können folgende Aktionen ausführen:
- Lesen aller Überwachungsdaten (wie dies die Rolle „Log Analytics-Leser“ ermöglicht)  
- Erstellen und Konfigurieren von Automation-Konten  
- Hinzufügen und Entfernen von Verwaltungslösungen    
    > [!NOTE] 
    > Um die letzten beiden Aktionen erfolgreich ausführen zu können, muss diese Berechtigung auf der Ressourcengruppen- oder Abonnementebene gewährt werden.  

- Lesen von Speicherkontoschlüsseln   
- Konfigurieren der Sammlung von Protokollen aus Azure Storage  
- Bearbeiten von Überwachungseinstellungen für Azure-Ressourcen, einschließlich:
  - Hinzufügen der VM-Erweiterung zu virtuellen Computern
  - Konfigurieren von Azure-Diagnosen für alle Azure-Ressourcen

> [!NOTE] 
> Durch Hinzufügen einer VM-Erweiterung zu einem virtuellen Computer können Sie die vollständige Kontrolle über einen virtuellen Computer erlangen.

Die Rolle „Log Analytics-Mitwirkender“ umfasst die folgenden Azure-Aktionen:

| Berechtigung | BESCHREIBUNG |
| ---------- | ----------- |
| `*/read`     | Anzeigen aller Ressourcen und der Ressourcenkonfiguration, einschließlich: <br> VM-Erweiterungsstatus <br> Konfiguration von Azure-Diagnosen für Ressourcen <br> Sämtliche Eigenschaften und Einstellungen aller Ressourcen |
| `Microsoft.Automation/automationAccounts/*` | Erstellen und Konfigurieren von Azure Automation-Konten (einschließlich Hinzufügen und Bearbeiten von Runbooks) |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Hinzufügen, Aktualisieren und Entfernen von VM-Erweiterungen (einschließlich Microsoft Monitoring Agent und des OMS-Agents für Linux) |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Anzeigen des Speicherkontoschlüssels. Erforderlich, um Log Analytics für das Lesen von Protokollen aus Azure-Speicherkonten zu konfigurieren |
| `Microsoft.Insights/alertRules/*` | Hinzufügen, Aktualisieren und Entfernen von Warnungsregeln |
| `Microsoft.Insights/diagnosticSettings/*` | Hinzufügen, Aktualisieren und Entfernen von Diagnoseeinstellungen für Azure-Ressourcen |
| `Microsoft.OperationalInsights/*` | Hinzufügen, Aktualisieren und Entfernen der Konfiguration für Log Analytics-Arbeitsbereiche |
| `Microsoft.OperationsManagement/*` | Hinzufügen und Entfernen von Verwaltungslösungen |
| `Microsoft.Resources/deployments/*` | Erstellen und Löschen von Bereitstellungen. Erforderlich für das Hinzufügen und Entfernen von Lösungen, Arbeitsbereichen und Automation-Konten |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Erstellen und Löschen von Bereitstellungen. Erforderlich für das Hinzufügen und Entfernen von Lösungen, Arbeitsbereichen und Automation-Konten |

Wenn Sie Benutzer einer Benutzerrolle hinzufügen oder daraus entfernen möchten, müssen Sie über die Berechtigungen `Microsoft.Authorization/*/Delete` und `Microsoft.Authorization/*/Write` verfügen.

Mit diesen Rollen können Sie Benutzern Zugriff auf verschiedenen Ebenen gewähren:
- Abonnement: Zugriff auf alle Arbeitsbereiche im Abonnement
- Ressourcengruppe: Zugriff auf alle Arbeitsbereiche in der Ressourcengruppe
- Ressource: Nur Zugriff auf den angegebenen Arbeitsbereich

Sie sollten Zuweisungen auf der Ressourcenebene (Arbeitsbereich) vornehmen, um eine ordnungsgemäße Zugriffssteuerung zu gewährleisten.  Verwenden Sie [benutzerdefinierte Rollen](../../role-based-access-control/custom-roles.md), um Rollen mit spezifischen Berechtigungen zu erstellen.

### <a name="resource-permissions"></a>Ressourcenberechtigungen 
Wenn Benutzer Protokolle aus einem Arbeitsbereich mit ressourcenbezogenem Zugriff abfragen, verfügen sie über die folgenden Berechtigungen für die Ressource:

| Berechtigung | BESCHREIBUNG |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Beispiele:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Möglichkeit, alle Protokolldaten für die Ressource anzuzeigen.  |


Diese Berechtigung wird in der Regel von einer Rolle erteilt, die _\*/read- oder_ _\*_ -Berechtigungen enthält, beispielsweise von den integrierten Rollen [Leser](../../role-based-access-control/built-in-roles.md#reader) und [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor). Beachten Sie, dass benutzerdefinierte Rollen, die bestimmte Aktionen umfassen, oder dedizierte integrierte Rollen diese Berechtigung ggf. nicht enthalten können.

Lesen Sie [Definieren von Zugriffssteuerung pro Tabelle](#table-level-rbac) weiter unten, wenn Sie für verschiedene Tabellen eine unterschiedliche Zugriffssteuerung erstellen möchten.


## <a name="table-level-rbac"></a>RBAC auf Tabellenebene
**RBAC auf Tabellenebene** ermöglicht zusätzlich zu den anderen Berechtigungen eine präzisere Steuerung von Daten in einem Log Analytics-Arbeitsbereich. Mit diesem Steuerelement können Sie bestimmte Datentypen definieren, auf die nur eine bestimmte Gruppe von Benutzern Zugriff besitzt.

Sie implementieren die Tabellenzugriffssteuerung mit [benutzerdefinierten Azure-Rollen](../../role-based-access-control/custom-roles.md), um den Zugriff auf bestimmte [Tabellen](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized) im Arbeitsbereich entweder zu gewähren oder zu verweigern. Diese Rollen gelten für Arbeitsbereiche mit arbeitsbereichbezogenen oder ressourcenbezogenen [Zugriffssteuerungsmodi](#access-control-mode), und zwar unabhängig vom [Zugriffsmodus](#access-modes) des Benutzers.

Erstellen Sie eine [benutzerdefinierte Rolle](../../role-based-access-control/custom-roles.md) mit den folgenden Aktionen, um den Zugriff auf die Tabellenzugriffssteuerung zu definieren.

- Um den Zugriff auf eine Tabelle zu gewähren, fügen Sie sie im Abschnitt **Actions** der Rollendefinition hinzu.
- Um den Zugriff auf eine Tabelle zu verweigern, fügen Sie sie im Abschnitt **NotActions** der Rollendefinition hinzu.
- Verwenden Sie das Zeichen „*“, um alle Tabellen anzugeben.

Um beispielsweise eine Rolle mit Zugriff auf die Tabellen _Heartbeat_ und _AzureActivity_ zu erstellen, erstellen Sie eine benutzerdefinierte Rolle mit den folgenden Aktionen:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Um eine Rolle mit Zugriff nur auf die Tabelle _SecurityBaseline_ und keine weiteren Tabellen zu erstellen, erstellen Sie eine benutzerdefinierte Rolle mit den folgenden Aktionen:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Benutzerdefinierte Protokolle
 Benutzerdefinierte Protokolle werden von Datenquellen wie etwa benutzerdefinierten Protokollen und der HTTP-Datensammler-API erstellt. Die einfachste Möglichkeit, den Typ des Protokolls zu identifizieren, besteht darin, die unter [Benutzerdefinierte Protokolle im Protokollschema](../log-query/get-started-portal.md#understand-the-schema) aufgeführten Tabellen zu überprüfen.

 Sie können derzeit den Zugriff auf einzelne benutzerdefinierte Protokolle nicht gewähren oder verweigern, aber Sie können den Zugriff auf alle benutzerdefinierten Protokolle gewähren oder verweigern. Um eine Rolle mit Zugriff auf alle benutzerdefinierten Protokolle zu erstellen, erstellen Sie eine benutzerdefinierte Rolle mit den folgenden Aktionen:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Überlegungen

- Wenn einem Benutzer globale Leseberechtigung mit den Standardrollen „Leser“ oder „Mitwirkender“ erteilt wird, die die Aktion _\*/read_ beinhalten, überschreibt dies die Zugriffssteuerung pro Tabelle und gewährt Zugriff auf alle Protokolldaten.
- Wenn einem Benutzer Zugriff pro Tabelle, aber keine anderen Berechtigungen gewährt werden, kann er über die API auf Protokolldaten zugreifen, nicht aber über das Azure-Portal. Verwenden Sie „Log Analytics-Leser“ als Basisrolle zum Bereitstellen von Zugriff auf das Azure-Portal.
- Administratoren für das Abonnement verfügen über Zugriff auf alle Datentypen, und zwar unabhängig von anderen Berechtigungseinstellungen.
- Besitzer eines Arbeitsbereichs werden wie alle anderen Benutzer bei der Zugriffssteuerung pro Tabelle behandelt.
- Sie sollten Rollen Sicherheitsgruppen anstelle von einzelnen Benutzern zuweisen, um die Anzahl der Zuordnungen zu verringern. Dies unterstützt Sie auch bei der Verwendung vorhandener Gruppenverwaltungstools zum Konfigurieren und Überprüfen des Zugriffs.




## <a name="next-steps"></a>Nächste Schritte
* Informationen zum Erfassen der Daten von Computern in Ihrem Datencenter oder einer anderen Cloudumgebung finden Sie unter [Collect log data with the Azure Log Analytics agent](../../azure-monitor/platform/log-analytics-agent.md) (Sammeln von Protokolldaten mit dem Azure Log Analytics-Agent).
* Informationen zum Konfigurieren der Datensammlung von virtuellen Azure-Computern finden Sie unter [Sammeln von Daten über virtuelle Azure-Computer](../../azure-monitor/learn/quick-collect-azurevm.md).  

