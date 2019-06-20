---
title: Rollenbasierte Zugriffssteuerung in Azure Automation
description: Die rollenbasierte Zugriffssteuerung (RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure-Ressourcen. Dieser Artikel beschreibt, wie eine rollenbasierte Zugriffssteuerung in Azure Automation eingerichtet wird.
keywords: Automation RBAC, rollenbasierte Zugriffssteuerung, Azure RBAC
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bcbda2464a4607aaa0b1bb96ef8f34c8713cb5f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60738756"
---
# <a name="role-based-access-control-in-azure-automation"></a>Rollenbasierte Zugriffssteuerung in Azure Automation

Die rollenbasierte Zugriffssteuerung (RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure-Ressourcen. Mithilfe der [rollenbasierten Zugriffssteuerung](../role-based-access-control/overview.md) können Sie Aufgaben innerhalb Ihres Teams verteilen sowie Benutzern, Gruppen und Anwendungen nur den Zugriff gewähren, den diese zur Ausführung ihrer Aufgaben benötigen. Rollenbasierter Zugriff kann Benutzern über das Azure-Portal, über Azure-Befehlszeilentools oder über Azure-Verwaltungs-APIs gewährt werden.

## <a name="roles-in-automation-accounts"></a>Rollen in Automation-Konten

Der Zugriff wird in Azure Automation erteilt, indem den Benutzern, Gruppen und Anwendungen im Automation-Konten-Bereich die passende RBAC-Rolle zugewiesen wird. Die folgenden vordefinierten Rollen werden von Automation-Konten unterstützt:

| **Rolle** | **Beschreibung** |
|:--- |:--- |
| Owner (Besitzer) |Die Rolle „Besitzer“ erlaubt den Zugriff auf alle Ressourcen und Aktionen innerhalb eines Automation-Kontos, z.B. den Zugriff auf andere Benutzer, Gruppen und Anwendungen, um das Automation-Konto zu verwalten. |
| Mitwirkender |Die Rolle „Mitwirkender“ erlaubt Ihnen, fast alles zu verwalten. Das Einzige, was Sie nicht können, ist das Ändern der Zugriffsberechtigungen für ein Automation-Konto anderer Benutzer. |
| Leser |Die Rolle „Leser“ erlaubt Ihnen, alle Ressourcen im Automation-Konto zu betrachten, aber Sie können keine Änderungen vornehmen. |
| Operator für Automation |Die Rolle „Operator für Automation“ ermöglicht das Anzeigen des Namens und der Eigenschaften des Runbooks sowie das Erstellen und Verwalten von Aufträgen für alle Runbooks in einem Automation-Konto. Diese Rolle ist hilfreich, wenn Sie Ihre Automation-Konten-Ressourcen wie Anmeldeinformationsobjekte und Runbooks vor Einblicken und Änderungen schützen wollen, es Mitgliedern Ihrer Organisation aber dennoch ermöglichen wollen, diese Runbooks auszuführen. |
|Automation-Auftragsoperator|Die Rolle „Automation-Auftragsoperator“ ermöglicht das Erstellen und Verwalten von Aufträgen für alle Runbooks in einem Automation-Konto.|
|Automation-Runbookoperator|Die Rolle „Automation-Runbookoperator“ ermöglicht das Lesen des Namens und der Eigenschaften eines Runbooks.|
| Log Analytics-Mitwirkender | Die Rolle „Log Analytics-Mitwirkender“ erlaubt Ihnen, alle Überwachungsdaten zu lesen und Überwachungseinstellungen zu bearbeiten. Das Bearbeiten von Überwachungseinstellungen schließt folgende Aufgaben ein: Hinzufügen der VM-Erweiterung zu VMs, Lesen von Speicherkontoschlüsseln zum Konfigurieren von Protokollsammlungen aus Azure Storage, Erstellen und Konfigurieren von Automation-Konten, Hinzufügen von Lösungen, Konfigurieren der Azure-Diagnose für alle Azure-Ressourcen.|
| Log Analytics-Leser | Die Rolle „Log Analytics-Leser“ erlaubt Ihnen, alle Überwachungsdaten anzuzeigen und zu durchsuchen sowie Überwachungseinstellungen anzuzeigen. Dies schließt auch die Anzeige der Konfiguration von Azure-Diagnosen für alle Azure-Ressourcen ein. |
| Überwachungsmitwirkender | Die Rolle „Überwachungsmitwirkender“ erlaubt Ihnen, alle Überwachungsdaten zu lesen und Überwachungseinstellungen zu aktualisieren.|
| Überwachungsleser | Die Rolle „Überwachungsleser“ ermöglicht Ihnen, alle Überwachungsdaten zu lesen. |
| Benutzerzugriffsadministrator |Mit der Rolle „Benutzerzugriffsadministrator“ können Sie den Benutzerzugriff auf Azure Automation-Konten verwalten. |

## <a name="role-permissions"></a>Rollenberechtigungen

Die folgenden Tabellen beschreiben die spezifischen Berechtigungen der einzelnen Rollen. Dazu gehören „Aktionen“, die Berechtigungen erteilen, und „Keine Aktionen“, die sie einschränken.

### <a name="owner"></a>Owner (Besitzer)

Ein Besitzer kann alles verwalten, einschließlich des Zugriffs. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|Aktionen|BESCHREIBUNG|
|---|---|
|Microsoft.Automation/automationAccounts/|Erstellen und Verwalten von Ressourcen aller Typen|

### <a name="contributor"></a>Mitwirkender

Ein Mitwirkender kann alles mit Ausnahme des Zugriffs verwalten. Die folgende Tabelle zeigt, welche Berechtigungen für die Rolle gewährt werden und welche nicht:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Erstellen und Verwalten von Ressourcen aller Typen|
|**Keine Aktionen**||
|Microsoft.Authorization/*/Delete| Löschen von Rollen und Rollenzuweisungen       |
|Microsoft.Authorization/*/Write     |  Erstellen von Rollen und Rollenzuweisungen       |
|Microsoft.Authorization/elevateAccess/Action    | Kein Zugriff auf die Option zum Erstellen eines Benutzerzugriffsadministrators       |

### <a name="reader"></a>Leser

Die Rolle „Leser“ ermöglicht Ihnen, alle Ressourcen im Automation-Konto anzuzeigen, aber Sie können keine Änderungen vornehmen.

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Anzeigen aller Ressourcen in ein Automation-Konto |

### <a name="automation-operator"></a>Operator für Automation

Ein Operator für Automation kann Aufträge erstellen und verwalten sowie Runbooknamen und -Eigenschaften für alle Runbooks in einem Automation-Konto lesen.  Hinweis: Legen Sie diese Rolle nicht fest, wenn Sie den Operatorzugriff auf einzelne Runbooks steuern möchten. Verwenden Sie stattdessen die Rollen „Automation-Auftragsoperator“ und „Automation-Runbookoperator“. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|Microsoft.Authorization/*/read|Lesen von Autorisierungen|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Lesen von Hybrid Runbook Worker-Ressourcen|
|Microsoft.Automation/automationAccounts/jobs/read|Auflisten von Aufträgen des Runbooks|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Fortsetzen eines angehaltenen Auftrags|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Abbrechen eines Auftrags in Bearbeitung|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lesen der Auftragsdatenströme und -ausgabe|
|Microsoft.Automation/automationAccounts/jobs/output/read|Abrufen der Ausgabe eines Auftrags|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Anhalten eines Auftrags in Bearbeitung|
|Microsoft.Automation/automationAccounts/jobs/write|Erstellen von Aufträgen|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Abrufen eines Azure Automation-Auftragszeitplans|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Erstellen eines Azure Automation-Auftragszeitplans|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Abrufen des Arbeitsbereichs, der mit dem Automation-Konto verknüpft ist|
|Microsoft.Automation/automationAccounts/read|Abrufen eines Azure Automation-Kontos|
|Microsoft.Automation/automationAccounts/runbooks/read|Abrufen eines Azure Automation-Runbooks|
|Microsoft.Automation/automationAccounts/schedules/read|Abrufen eines Azure Automation-Zeitplanassets|
|Microsoft.Automation/automationAccounts/schedules/write|Erstellen oder Aktualisieren eines Azure Automation-Zeitplanassets|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Lesen von Rollen und Rollenzuweisungen         |
|Microsoft.Resources/deployments/*      |Erstellen und Verwalten von Ressourcengruppenbereitstellungen         |
|Microsoft.Insights/alertRules/*      | Erstellen und Verwalten von Warnungsregeln        |
|Microsoft.Support/* |Erstellen und Verwalten von Supporttickets|

### <a name="automation-job-operator"></a>Automation-Auftragsoperator

Die Rolle „Automation-Auftragsoperator“ wird im Bereich des Automation-Kontos vergeben. So können mit Operatorberechtigungen Aufträge für alle Runbooks in dem Konto erstellt und verwaltet werden. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|Microsoft.Authorization/*/read|Lesen von Autorisierungen|
|Microsoft.Automation/automationAccounts/jobs/read|Auflisten von Aufträgen des Runbooks|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Fortsetzen eines angehaltenen Auftrags|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Abbrechen eines Auftrags in Bearbeitung|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lesen der Auftragsdatenströme und -ausgabe|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Anhalten eines Auftrags in Bearbeitung|
|Microsoft.Automation/automationAccounts/jobs/write|Erstellen von Aufträgen|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Lesen von Rollen und Rollenzuweisungen       |
|Microsoft.Resources/deployments/*      |Erstellen und Verwalten von Ressourcengruppenbereitstellungen         |
|Microsoft.Insights/alertRules/*      | Erstellen und Verwalten von Warnungsregeln        |
|Microsoft.Support/* |Erstellen und Verwalten von Supporttickets|

### <a name="automation-runbook-operator"></a>Automation-Runbookoperator

Die Rolle „Automation-Runbookoperator“ wird im Runbookbereich vergeben. Ein Automation-Runbookoperator kann den Namen und die Eigenschaften eines Runbooks anzeigen.  Zusammen mit der Rolle „Automation-Auftragsoperator“ ermöglicht diese Rolle dem Operator zudem das Erstellen und Verwalten von Aufträgen für das Runbook. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Auflisten der Runbooks        |
|Microsoft.Authorization/*/read      | Lesen von Autorisierungen        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Lesen von Rollen und Rollenzuweisungen         |
|Microsoft.Resources/deployments/*      | Erstellen und Verwalten von Ressourcengruppenbereitstellungen         |
|Microsoft.Insights/alertRules/*      | Erstellen und Verwalten von Warnungsregeln        |
|Microsoft.Support/*      | Erstellen und Verwalten von Supporttickets        |

### <a name="log-analytics-contributor"></a>Log Analytics-Mitwirkender

Ein Log Analytics-Mitwirkender kann alle Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten. Das Bearbeiten von Überwachungseinstellungen schließt folgende Aufgaben ein: Hinzufügen der VM-Erweiterung zu VMs, Lesen von Speicherkontoschlüsseln zum Konfigurieren von Protokollsammlungen aus Azure Storage, Erstellen und Konfigurieren von Automation-Konten, Hinzufügen von Lösungen, Konfigurieren der Azure-Diagnose für alle Azure-Ressourcen. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|*/Lesen|Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel|
|Microsoft.Automation/automationAccounts/*|Verwalten von Automation-Konten|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Erstellen und Verwalten von VM-Erweiterungen|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Auflisten von klassischen Speicherkontoschlüsseln|
|Microsoft.Compute/virtualMachines/extensions/*|Erstellen und Verwalten von klassischen VM-Erweiterungen|
|Microsoft.Insights/alertRules/*|Lesen/Schreiben/Löschen von Warnungsregeln.|
|Microsoft.Insights/diagnosticSettings/*|Lesen/Schreiben/Löschen von Diagnoseeinstellungen.|
|Microsoft.OperationalInsights/*|Verwalten von Azure Monitor-Protokollen.|
|Microsoft.OperationsManagement/*|Verwalten von Lösungen in Arbeitsbereichen|
|Microsoft.Resources/deployments/*|Erstellen und Verwalten von Ressourcengruppenbereitstellungen|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Erstellen und Verwalten von Ressourcengruppenbereitstellungen|
|Microsoft.Storage/storageAccounts/listKeys/action|Auflisten von Speicherkontoschlüsseln|
|Microsoft.Support/*|Erstellen und Verwalten von Supporttickets|

### <a name="log-analytics-reader"></a>Log Analytics-Leser

Ein Log Analytics-Leser kann alle Überwachungsdaten anzeigen und durchsuchen sowie Überwachungseinstellungen anzeigen. Hierzu zählt auch die Anzeige der Konfiguration von Azure-Diagnosen für alle Azure-Ressourcen. Die folgende Tabelle zeigt, welche Berechtigungen für die Rolle gewährt werden und welche nicht:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|*/Lesen|Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Verwalten von Abfragen in Azure Monitor-Protokollen.|
|Microsoft.OperationalInsights/workspaces/search/action|Durchsuchen von Azure Monitor-Protokolldaten.|
|Microsoft.Support/*|Erstellen und Verwalten von Supporttickets|
|**Keine Aktionen**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Kein Lesezugriff auf freigegebene Zugriffsschlüssel|

### <a name="monitoring-contributor"></a>Überwachungsmitwirkender

Ein Überwachungsmitwirkender kann alle Überwachungsdaten lesen und Überwachungseinstellungen aktualisieren. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|*/Lesen|Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel|
|Microsoft.AlertsManagement/alerts/*|Verwalten von Warnungen|
|Microsoft.AlertsManagement/alertsSummary/*|Verwalten des Warnungsdashboards|
|Microsoft.Insights/AlertRules/*|Verwalten von Warnungsregeln|
|Microsoft.Insights/components/*|Verwalten von Application Insights-Komponenten|
|Microsoft.Insights/DiagnosticSettings/*|Verwalten von Diagnoseeinstellungen|
|Microsoft.Insights/eventtypes/*|Auflisten von Aktivitätsprotokollereignissen (Verwaltungsereignissen) in einem Abonnement. Diese Berechtigung gilt sowohl für den programmgesteuerten als auch für den Portalzugriff auf das Aktivitätsprotokoll.|
|Microsoft.Insights/LogDefinitions/*|Diese Berechtigung ist für Benutzer notwendig, die über das Portal auf Aktivitätsprotokolle zugreifen müssen. Auflisten der Protokollkategorien im Aktivitätsprotokoll.|
|Microsoft.Insights/MetricDefinitions/*|Lesen von Metrikdefinitionen (Liste der verfügbaren Metriktypen für eine Ressource).|
|Microsoft.Insights/Metrics/*|Lesen von Metriken für eine Ressource.|
|Microsoft.Insights/Register/Action|Registriert den Microsoft.Insights-Anbieter.|
|Microsoft.Insights/webtests/*|Verwalten von Application Insights-Webtests|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Verwalten von Azure Monitor-Protokolllösungspaketen.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Verwalten von Azure Monitor-Protokollen: gespeicherte Suchen.|
|Microsoft.OperationalInsights/workspaces/search/action|Durchsuchen von Log Analytics-Arbeitsbereichen.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Auflisten der Schlüssel für einen Log Analytics-Arbeitsbereich.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Verwalten von Azure Monitor-Protokollen: Speichererkenntniskonfigurationen.|
|Microsoft.Support/*|Erstellen und Verwalten von Supporttickets|
|Microsoft.WorkloadMonitor/workloads/*|Verwalten von Workloads|

### <a name="monitoring-reader"></a>Überwachungsleser

Ein Überwachungsleser kann alle Überwachungsdaten lesen. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|*/Lesen|Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel|
|Microsoft.OperationalInsights/workspaces/search/action|Durchsuchen von Log Analytics-Arbeitsbereichen.|
|Microsoft.Support/*|Erstellen und Verwalten von Support-Tickets|

### <a name="user-access-administrator"></a>Benutzerzugriffsadministrator

Ein Benutzerzugriffsadministrator kann den Benutzerzugriff auf Azure-Ressourcen verwalten. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|*/Lesen|Lesen aller Ressourcen|
|Microsoft.Authorization/*|Verwalten der Autorisierung|
|Microsoft.Support/*|Erstellen und Verwalten von Support-Tickets|

## <a name="onboarding"></a>Onboarding

Die folgenden Tabellen zeigen die minimal erforderlichen Berechtigungen, die beim Onboarding virtueller Computer für die Lösungen zur Änderungsnachverfolgung oder Updateverwaltung benötigt werden.

### <a name="onboarding-from-a-virtual-machine"></a>Onboarding von eines virtuellen Computers

|**Aktion**  |**Berechtigung**  |**Mindestumfang**  |
|---------|---------|---------|
|Neue Bereitstellung schreiben      | Microsoft.Resources/deployments/*          |Abonnement          |
|Neue Ressourcengruppe schreiben      | Microsoft.Resources/subscriptions/resourceGroups/write        | Abonnement          |
|Neuen Standardarbeitsbereich erstellen      | Microsoft.OperationalInsights/workspaces/write         | Ressourcengruppe         |
|Neues Konto erstellen      |  Microsoft.Automation/automationAccounts/write        |Ressourcengruppe         |
|Arbeitsbereich mit Konto verknüpfen      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Arbeitsbereich</br>Automation-Konto
|Lösung erstellen      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Ressourcengruppe          |
|MMA-Erweiterung erstellen      | Microsoft.Compute/virtualMachines/write         | Virtual Machine         |
|Gespeicherten Suchvorgang erstellen      | Microsoft.OperationalInsights/workspaces/write          | Arbeitsbereich         |
|Bereichskonfiguration erstellen      | Microsoft.OperationalInsights/workspaces/write          | Arbeitsbereich         |
|Lösung mit Bereichskonfiguration verknüpfen      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Lösung         |
|Statusüberprüfung des Onboardings – Arbeitsbereich lesen      | Microsoft.OperationalInsights/workspaces/read         | Arbeitsbereich         |
|Statusüberprüfung des Onboardings – Eigenschaft des verknüpften Arbeitsbereichs von Konto lesen     | Microsoft.Automation/automationAccounts/read      | Automation-Konto        |
|Statusüberprüfung des Onboardings – Lösung lesen      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Lösung         |
|Statusüberprüfung des Onboardings – VM lesen      | Microsoft.Compute/virtualMachines/read         | Virtual Machine         |
|Statusüberprüfung des Onboardings – Konto lesen      | Microsoft.Automation/automationAccounts/read  |  Automation-Konto   |
| Arbeitsbereichsüberprüfung des Onboardings für die VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Abonnement         |

<sup>1</sup> Diese Berechtigung ist für das Onboarding über die VM-Portal-Benutzeroberfläche erforderlich.

### <a name="onboarding-from-automation-account"></a>Onboarding von Automation-Konto

|**Aktion**  |**Berechtigung** |**Mindestumfang**  |
|---------|---------|---------|
|Neue Bereitstellung erstellen     | Microsoft.Resources/deployments/*        | Abonnement         |
|Erstellen einer neuen Ressourcengruppe     | Microsoft.Resources/subscriptions/resourceGroups/write         | Abonnement        |
|AutomationOnboarding-Blatt – neuen Arbeitsbereich erstellen     |Microsoft.OperationalInsights/workspaces/write           | Ressourcengruppe        |
|AutomationOnboarding-Blatt – verknüpften Arbeitsbereich lesen     | Microsoft.Automation/automationAccounts/read        | Automation-Konto       |
|AutomationOnboarding-Blatt – Lösung lesen     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Lösung        |
|AutomationOnboarding-Blatt – Arbeitsbereich lesen     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Arbeitsbereich        |
|Link für Arbeitsbereich und Konto erstellen     | Microsoft.OperationalInsights/workspaces/write        | Arbeitsbereich        |
|Konto für Shoebox schreiben      | Microsoft.Automation/automationAccounts/write        | Konto        |
|Lösung erstellen      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Ressourcengruppe         |
|Gespeicherten Suchvorgang erstellen/bearbeiten     | Microsoft.OperationalInsights/workspaces/write        | Arbeitsbereich        |
|Bereichskonfiguration erstellen/bearbeiten     | Microsoft.OperationalInsights/workspaces/write        | Arbeitsbereich        |
|Lösung mit Bereichskonfiguration verknüpfen      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Lösung         |
|**Schritt 2 – Integrieren mehrerer VMs**     |         |         |
|VMOnboarding-Blatt – MMA-Erweiterung erstellen     | Microsoft.Compute/virtualMachines/write           | Virtual Machine        |
|Gespeicherten Suchvorgang erstellen/bearbeiten     | Microsoft.OperationalInsights/workspaces/write           | Arbeitsbereich        |
|Bereichskonfiguration erstellen/bearbeiten  | Microsoft.OperationalInsights/workspaces/write   | Arbeitsbereich|

## <a name="update-management"></a>Updateverwaltung

Die Updateverwaltung erstreckt sich über mehrere Dienste, um deren Dienst bereitzustellen. Die folgende Tabelle zeigt die Berechtigungen, die zum Verwalten von Bereitstellungen der Updateverwaltung benötigt werden:

|**Ressource**  |**Rolle**  |**Umfang**  |
|---------|---------|---------|
|Automation-Konto     | Log Analytics-Mitwirkender       | Automation-Konto        |
|Automation-Konto    | Mitwirkender von virtuellen Computern        | Ressourcengruppe für das Konto        |
|Log Analytics-Arbeitsbereich     | Log Analytics-Mitwirkender| Log Analytics-Arbeitsbereich        |
|Log Analytics-Arbeitsbereich |Log Analytics-Leser| Abonnement|
|Lösung     |Log Analytics-Mitwirkender         | Lösung|
|Virtual Machine     | Mitwirkender von virtuellen Computern        | Virtual Machine        |

## <a name="configure-rbac-for-your-automation-account"></a>Konfigurieren der rollenbasierten Zugriffssteuerung für Automation-Konten

Der folgende Abschnitt veranschaulicht das Konfigurieren der rollenbasierten Zugriffssteuerung für Automation-Konten über das [Portal](#configure-rbac-using-the-azure-portal) und [PowerShell](#configure-rbac-using-powershell).

### <a name="configure-rbac-using-the-azure-portal"></a>Konfigurieren der rollenbasierten Zugriffssteuerung über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und öffnen Sie auf der Seite „Automation-Konten“ Ihr Automation-Konto.
2. Klicken Sie oben links auf das Steuerelement **Zugriffssteuerung (IAM)** . Dadurch wird die Seite **Zugriffssteuerung (IAM)** geöffnet. Hier können Sie neue Benutzer, Gruppen und Anwendungen zur Verwaltung Ihres Automation-Kontos hinzufügen. Des Weiteren können Sie vorhandene Rollen anzeigen, die für das Automation-Konto konfiguriert werden können.
3. Klicken Sie auf die Registerkarte **Rollenzuweisungen**.

   ![Zugriffsschaltfläche](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Einen neuen Benutzer hinzufügen und eine Rolle zuweisen

1. Klicken Sie auf der Seite **Zugriffssteuerung (IAM)** auf **+ Rollenzuweisung hinzufügen**, um die Seite **Rollenzuweisung hinzufügen** zu öffnen. Hier können Sie einen Benutzer, eine Gruppe oder eine Anwendung hinzufügen und ihm bzw. ihr Rollen zuweisen.

2. Wählen Sie eine Rolle aus der Liste der verfügbaren Rollen aus. Sie können aber jede der verfügbaren integrierten Rollen, die von Automation-Konten unterstützt werden, oder auch eine eigene, benutzerdefinierte Rolle auswählen.

3. Geben Sie im Feld **Auswählen** den Benutzernamen des Benutzers ein, dem Sie Berechtigungen erteilen möchten. Wählen Sie den Benutzer in der Liste aus, und klicken Sie auf **Speichern**.

   ![Hinzufügen von Benutzern](media/automation-role-based-access-control/automation-04-add-users.png)

   Der Benutzer sollte nun auf der Seite **Benutzer** mit der zugewiesenen Rolle, die Sie ausgewählt haben, angezeigt werden.

   ![Benutzer auflisten](media/automation-role-based-access-control/automation-05-list-users.png)

   Sie können dem Benutzer die Rolle über die Seite **Rollen** zuweisen.
4. Klicken Sie auf der Seite **Zugriffssteuerung (AIM)** auf **Rollen**, um die Seite **Rollen** zu öffnen. Auf dieser Seite werden der Name der Rolle sowie die Anzahl von Benutzern und Gruppen angezeigt, denen diese Rolle zugewiesen wurde.

    ![Rolle über die Seite „Benutzer“ zuweisen](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Die rollenbasierte Zugriffssteuerung kann nur im Automation-Kontobereich eingerichtet werden und nicht bei einer beliebigen Ressource unter dem Automation-Konto.

#### <a name="remove-a-user"></a>Benutzer entfernen

Sie können die Zugriffsberechtigung eines Benutzers, der selbst nicht das Automation-Konto verwaltet oder nicht mehr für die Organisation arbeitet, entfernen. Folgende Schritte müssen Sie durchführen, um einen Benutzer zu entfernen:

1. Wählen Sie auf der Seite **Zugriffssteuerung (AIM)** den Benutzer aus, den Sie entfernen möchten, und klicken Sie dann auf **Entfernen**.
2. Klicken Sie auf der Seite mit den Zuweisungsdetails auf die Schaltfläche **Entfernen**.
3. Klicken Sie auf **Ja** , um die Entfernung zu bestätigen.

   ![Benutzer entfernen](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Konfigurieren der rollenbasierten Zugriffssteuerung über PowerShell

Der rollenbasierte Zugriff kann auch für ein Automation-Konto mit den folgenden [Azure PowerShell-Cmdlets](../role-based-access-control/role-assignments-powershell.md) konfiguriert werden:

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) listet alle in Azure Active Directory verfügbaren RBAC-Rollen auf. Sie können diesen Befehl zusammen mit der **Name** -Eigenschaft verwenden, um alle Aktionen aufzulisten, die von einer bestimmten Rolle durchgeführt werden können.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Im Folgenden sehen Sie die Beispielausgabe:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) listet die Rollenzuweisungen durch Azure AD RBAC für den angegebenen Bereich auf. Ohne einschränkende Parameter gibt dieser Befehl alle Rollenzuweisungen zurück, die in diesem Abonnement erstellt wurden. Verwenden Sie den **ExpandPrincipalGroups** -Parameter, um die Zugriffszuweisungen für den festgelegten Benutzer sowie die Gruppen, denen der Benutzer angehört, aufzulisten.
    **Beispiel:** Verwenden Sie den folgenden Befehl, um alle Benutzer innerhalb eines Automation-Kontos mit ihren Rollen aufzulisten.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Im Folgenden sehen Sie die Beispielausgabe:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

Verwenden Sie [New-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)), um Benutzern, Gruppen und Anwendungen die Zugriffsberechtigung für einen bestimmten Bereich zuzuweisen.
    **Beispiel:** Verwenden Sie den folgenden Befehl, um einem Benutzer die Rolle „Operator für Automation“ im Geltungsbereich des Automation-Kontos zuzuweisen.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Im Folgenden sehen Sie die Beispielausgabe:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Verwenden Sie [Remove-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)), um den Zugriff eines angegebenen Benutzers, einer Gruppe oder einer Anwendung für einen bestimmten Bereich zu entfernen.
    **Beispiel:** Verwenden Sie den folgenden Befehl, um den Benutzer aus der Rolle „Operator für Automation“ im Geltungsbereich des Automation-Kontos zu entfernen.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Ersetzen Sie in den obigen Beispielen die **Anmelde-ID**, die **Abonnement-ID**, den **Namen der Ressourcengruppe** und den **Namen des Automation-Kontos** durch die entsprechenden Werte Ihres Kontos. Wählen Sie **Ja** , wenn Sie zum Bestätigen aufgefordert werden, bevor Sie mit dem Entfernen von Benutzerrollenzuweisungen fortfahren.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Berechtigungen der Rolle „Operator für Automation“ – Automation-Konto

Wenn Benutzer, denen im Automation-Kontobereich die Rolle „Operator für Automation“ zugewiesen wurde, das ihnen zugewiesene Automation-Konto aufrufen, können sie nur die Liste mit den Runbooks, Runbookaufträgen und Zeitplänen sehen, die im Automation-Konto erstellt wurden. Dies gilt nicht für deren Definition. Sie können den Runbookauftrag starten, beenden, unterbrechen, fortsetzen oder planen. Auf andere Automation-Ressourcen wie Konfigurationen, Hybrid Worker-Gruppen oder DSC-Knoten hat der Benutzer keinen Zugriff.

![Kein Zugriff auf Ressourcen](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Konfigurieren der rollenbasierten Zugriffssteuerung für Runbooks

Mit Azure Automation können Sie bestimmten Runbooks die rollenbasierte Zugriffssteuerung zuweisen. Führen Sie das folgende Skript aus, um einen Benutzer einem bestimmten Runbook hinzuzufügen. Das Skript kann von einem Automation-Kontoadministrator oder einem Mandantenadministrator ausgeführt werden.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation Account
$automationAccountName ="<Automation Account Name>" # Name of the Automation Account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation Account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be ran once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Nachdem das Skript ausgeführt wurde, muss der Benutzer sich im Azure-Portal anmelden und **Alle Ressourcen** aufrufen. In der Liste wird das Runbook angezeigt, dem er als **Automation-Runbookoperator** zugewiesen wurde.

![Rollenbasierte Zugriffssteuerung für Runbooks über das Portal](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Berechtigungen der Rolle „Operator für Automation“ – Runbook

Wenn Benutzer, denen im Runbookbereich die Rolle „Operator für Automation“ zugewiesen wurde, ein ihnen zugewiesenes Runbook aufrufen, können sie nur das Runbook starten und die Runbookaufträge ansehen.

![Nur Startzugriff](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den verschiedenen Möglichkeiten, die rollenbasierte Zugriffsteuerung für Azure Automation zu konfigurieren, finden Sie unter [Verwalten der rollenbasierten Zugriffssteuerung mit Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Weitere Informationen zu verschiedenen Methoden zum Starten eines Runbooks finden Sie unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md)
* Weitere Informationen zu verschiedenen Runbooktypen finden Sie unter [Azure Automation-Runbooktypen](automation-runbook-types.md)

