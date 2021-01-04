---
title: Verwalten von Log Analytics-Arbeitsbereichen in Azure Monitor | Microsoft-Dokumentation
description: Sie können den Zugriff auf Daten, die in einem Log Analytics-Arbeitsbereich in Azure Monitor gespeichert sind, mithilfe von Berechtigungen auf Ressourcen-, Arbeitsbereichs- oder Tabellenebene verwalten. In diesem Artikel wird dies ausführlich erläutert.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: 234ef58920a9f896d3e8ebcc561562ea7ceb2708
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186421"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Verwalten des Zugriffs auf Protokolldaten und Arbeitsbereiche in Azure Monitor

Azure Monitor speichert [Protokolldaten](data-platform-logs.md) in einem Log Analytics-Arbeitsbereich. Bei einem Arbeitsbereich handelt es sich um einen Container, der Daten und Konfigurationsinformationen enthält. Zum Verwalten des Zugriffs auf Protokolldaten führen Sie verschiedene Verwaltungsaufgaben für Ihren Arbeitsbereich durch.

In diesem Artikel erfahren Sie, wie Sie den Zugriff auf Protokolle sowie die Arbeitsbereiche verwalten, in denen sich diese befinden. Außerdem erfahren Sie, wie Sie Zugriff für Folgendes gewähren: 

* Den Arbeitsbereich (mithilfe von Arbeitsbereichsberechtigungen)
* Benutzer, die Zugriff auf Protokolldaten von bestimmten Ressourcen benötigen: mithilfe der rollenbasierten Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC), auch [Ressourcenkontext](design-logs-deployment.md#access-mode) genannt
* Benutzer, die Zugriff auf Protokolldaten in einer bestimmten Tabelle im Arbeitsbereich benötigen (mithilfe von Azure RBAC)

Weitere Informationen zu den Protokollkonzepten bei Azure RBAC und den Zugriffsstrategien finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](design-logs-deployment.md).

## <a name="configure-access-control-mode"></a>Konfigurieren des Zugriffssteuerungsmodus

Sie können den für einen Arbeitsbereich konfigurierten [Zugriffssteuerungsmodus](design-logs-deployment.md) über das Azure-Portal oder mit Azure PowerShell anzeigen.  Diese Einstellung kann mit einer der folgenden unterstützten Methoden geändert werden:

* Azure-Portal

* Azure PowerShell

* Azure Resource Manager-Vorlage

### <a name="from-the-azure-portal"></a>Über das Azure-Portal

Sie können den aktuellen Zugriffssteuerungsmodus für den Arbeitsbereich auf der Seite **Übersicht** für den Arbeitsbereich im Menü **Log Analytics-Arbeitsbereich** anzeigen.

![Anzeigen des Zugriffssteuerungsmodus für den Arbeitsbereich](media/manage-access/view-access-control-mode.png)

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
1. Wählen Sie im Azure-Portal die Option „Log Analytics-Arbeitsbereiche“ und dann Ihren Arbeitsbereich aus.

Sie können diese Einstellung auf der Seite **Eigenschaften** des Arbeitsbereichs ändern. Das Ändern der Einstellung ist deaktiviert, wenn Sie keine Berechtigungen zum Konfigurieren des Arbeitsbereichs besitzen.

![Ändern des Arbeitsbereichzugriffsmodus](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>PowerShell

Verwenden Sie den folgenden Befehl, um den Zugriffssteuerungsmodus für alle Arbeitsbereiche im Abonnement zu überprüfen:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

Die Ausgabe sollte wie folgt aussehen:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Der Wert `False` bedeutet, dass der Arbeitsbereich mit dem Zugriffsmodus für den Arbeitsbereichskontext konfiguriert ist.  Der Wert `True` bedeutet, dass der Arbeitsbereich mit dem Zugriffsmodus für den Ressourcenkontext konfiguriert ist.

> [!NOTE]
> Wenn ein Arbeitsbereich ohne einen booleschen Wert zurückgegeben wird und leer ist, entspricht dies ebenfalls den Ergebnissen eines `False`-Werts.
>

Verwenden Sie das folgende Skript, um den Zugriffssteuerungsmodus für einen bestimmten Arbeitsbereich auf die Berechtigung im Ressourcenkontext festzulegen:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Verwenden Sie das folgende Skript, um den Zugriffssteuerungsmodus für alle Arbeitsbereiche im Abonnement auf die Berechtigung im Ressourcenkontext festzulegen:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Verwenden einer Resource Manager-Vorlage

Um den Zugriffsmodus in einer Azure Resource Manager-Vorlage zu konfigurieren, legen Sie das Featureflag **enableLogAccessUsingOnlyResourcePermissions** für den Arbeitsbereich auf einen der folgenden Werte fest.

* **FALSE**: Legen Sie den Arbeitsbereich auf Berechtigungen im Arbeitsbereichskontext fest. Dies ist die Standardeinstellung, wenn das Flag nicht festgelegt ist.
* **TRUE**: Legen Sie den Arbeitsbereich auf Berechtigungen im Ressourcenkontext fest.

## <a name="manage-access-using-workspace-permissions"></a>Zugriffsverwaltung mithilfe von Arbeitsbereichsberechtigungen

Jedem Arbeitsbereich können mehrere Konten zugeordnet werden, und jedes Konto kann Zugriff auf mehrere Arbeitsbereiche haben. Der Zugriff wird mithilfe der [rollenbasierten Zugriffssteuerung von Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) verwaltet.

Für die folgenden Aktivitäten sind ebenfalls Azure-Berechtigungen erforderlich:

|Aktion |Azure-Berechtigungen erforderlich |Notizen |
|-------|-------------------------|------|
| Hinzufügen und Entfernen von Überwachungslösungen | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Diese Berechtigungen müssen auf der Ressourcengruppen- oder Abonnementebene gewährt werden. |
| Ändern des Tarifs | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Anzeigen von Daten auf den Kacheln der *Backup*- und *Site Recovery*-Lösungen | Administrator/Co-Administrator | Zugriff auf Ressourcen, die mit dem klassischen Bereitstellungsmodell bereitgestellt werden |
| Erstellen eines Arbeitsbereichs im Azure-Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Anzeigen von grundlegenden Arbeitsbereichseigenschaften und Eingeben des Arbeitsbereichsblatts im Portal | `Microsoft.OperationalInsights/workspaces/read` ||
| Abfragen von Protokollen mit einer beliebigen Schnittstelle | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Zugriff auf alle Protokolltypen mithilfe von Abfragen | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Zugriff auf eine bestimmte Protokolltabelle | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Lesen der Arbeitsbereichsschlüssel, um das Senden von Protokollen an den Arbeitsbereich zuzulassen | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Zugriffsverwaltung mithilfe von Azure-Berechtigungen

Führen Sie die Schritte unter [Verwenden von Rollenzuweisungen zum Verwalten Ihrer Azure-Abonnementressourcen](../../role-based-access-control/role-assignments-portal.md) aus, um den Zugriff auf den Log Analytics-Arbeitsbereich mit Azure-Berechtigungen zu gewähren. Beispiele für benutzerdefinierte Rollen finden Sie unter [Beispiele für benutzerdefinierte Rollen](#custom-role-examples).

Azure verfügt über zwei integrierte Benutzerrollen für Log Analytics-Arbeitsbereiche:

* Log Analytics-Leser
* Log Analytics-Mitwirkender

Mitglieder der Rolle *Log Analytics-Leser* können folgende Aktionen ausführen:

* Anzeigen und Durchsuchen aller Überwachungsdaten
* Anzeigen von Überwachungseinstellungen (einschließlich der Konfiguration von Azure-Diagnosen für alle Azure-Ressourcen)

Die Rolle „Log Analytics-Leser“ umfasst die folgenden Azure-Aktionen:

| type    | Berechtigung | BESCHREIBUNG |
| ------- | ---------- | ----------- |
| Aktion | `*/read`   | Anzeigen aller Azure-Ressourcen und der Ressourcenkonfiguration, einschließlich: <br> VM-Erweiterungsstatus <br> Konfiguration von Azure-Diagnosen für Ressourcen <br> Sämtliche Eigenschaften und Einstellungen aller Ressourcen. <br> Für Arbeitsbereiche lässt dies uneingeschränkte Berechtigungen zum Lesen der Arbeitsbereichseinstellungen und Durchführen von Abfragen der Daten zu. Genauere Optionen finden Sie oben. |
| Aktion | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Veraltet: es gibt keinen Grund, diese Berechtigung Benutzern zuzuweisen. |
| Aktion | `Microsoft.OperationalInsights/workspaces/search/action` | Veraltet: es gibt keinen Grund, diese Berechtigung Benutzern zuzuweisen. |
| Aktion | `Microsoft.Support/*` | Öffnen von Supportfällen |
|Keine Aktion | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Verhindert das Lesen des Arbeitsbereichsschlüssels, der zum Verwenden der Datensammlungs-API und zum Installieren von Agents erforderlich ist. Dadurch wird verhindert, dass Benutzer dem Arbeitsbereich neue Ressourcen hinzufügen. |

Mitglieder der Rolle *Log Analytics-Mitwirkender* können folgende Aktionen ausführen:

* Schließt alle Berechtigungen der *Rolle „Log Analytics-Leser“* ein, sodass der Benutzer alle Überwachungsdaten lesen kann.
* Erstellen und Konfigurieren von Automation-Konten
* Hinzufügen und Entfernen von Verwaltungslösungen

    > [!NOTE]
    > Um die letzten beiden Aktionen erfolgreich ausführen zu können, muss diese Berechtigung auf der Ressourcengruppen- oder Abonnementebene gewährt werden.

* Lesen von Speicherkontoschlüsseln
* Konfigurieren der Erfassung von Protokollen aus Azure Storage
* Bearbeiten von Überwachungseinstellungen für Azure-Ressourcen, einschließlich:
  * Hinzufügen der VM-Erweiterung zu virtuellen Computern
  * Konfigurieren von Azure-Diagnosen für alle Azure-Ressourcen

> [!NOTE]
> Durch Hinzufügen einer VM-Erweiterung zu einem virtuellen Computer können Sie die vollständige Kontrolle über einen virtuellen Computer erlangen.

Die Rolle „Log Analytics-Mitwirkender“ umfasst die folgenden Azure-Aktionen:

| Berechtigung | BESCHREIBUNG |
| ---------- | ----------- |
| `*/read`     | Anzeigen aller Ressourcen und der Ressourcenkonfiguration, einschließlich: <br> VM-Erweiterungsstatus <br> Konfiguration von Azure-Diagnosen für Ressourcen <br> Sämtliche Eigenschaften und Einstellungen aller Ressourcen. <br> Für Arbeitsbereiche lässt dies uneingeschränkte Berechtigungen zum Lesen der Arbeitsbereichseinstellung und Durchführen von Abfragen der Daten zu. Genauere Optionen finden Sie oben. |
| `Microsoft.Automation/automationAccounts/*` | Erstellen und Konfigurieren von Azure Automation-Konten (einschließlich Hinzufügen und Bearbeiten von Runbooks) |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Hinzufügen, Aktualisieren und Entfernen von VM-Erweiterungen (einschließlich Microsoft Monitoring Agent und des OMS-Agents für Linux) |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Anzeigen des Speicherkontoschlüssels. Erforderlich, um Log Analytics für das Lesen von Protokollen aus Azure-Speicherkonten zu konfigurieren |
| `Microsoft.Insights/alertRules/*` | Hinzufügen, Aktualisieren und Entfernen von Warnungsregeln |
| `Microsoft.Insights/diagnosticSettings/*` | Hinzufügen, Aktualisieren und Entfernen von Diagnoseeinstellungen für Azure-Ressourcen |
| `Microsoft.OperationalInsights/*` | Hinzufügen, Aktualisieren und Entfernen der Konfiguration für Log Analytics-Arbeitsbereiche. Zum Bearbeiten erweiterter Einstellungen für Arbeitsbereiche benötigt der Benutzer die Berechtigung `Microsoft.OperationalInsights/workspaces/write`. |
| `Microsoft.OperationsManagement/*` | Hinzufügen und Entfernen von Verwaltungslösungen |
| `Microsoft.Resources/deployments/*` | Erstellen und Löschen von Bereitstellungen. Erforderlich für das Hinzufügen und Entfernen von Lösungen, Arbeitsbereichen und Automation-Konten |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Erstellen und Löschen von Bereitstellungen. Erforderlich für das Hinzufügen und Entfernen von Lösungen, Arbeitsbereichen und Automation-Konten |

Wenn Sie Benutzer einer Benutzerrolle hinzufügen oder daraus entfernen möchten, müssen Sie über die Berechtigungen `Microsoft.Authorization/*/Delete` und `Microsoft.Authorization/*/Write` verfügen.

Mit diesen Rollen können Sie Benutzern Zugriff auf verschiedenen Ebenen gewähren:

* Abonnement: Zugriff auf alle Arbeitsbereiche im Abonnement
* Ressourcengruppe: Zugriff auf alle Arbeitsbereiche in der Ressourcengruppe
* Ressource: Nur Zugriff auf den angegebenen Arbeitsbereich

Es empfiehlt sich, Zuweisungen auf der Ressourcenebene (Arbeitsbereich) vorzunehmen, um eine korrekte Zugriffssteuerung zu gewährleisten. Verwenden Sie [benutzerdefinierte Rollen](../../role-based-access-control/custom-roles.md), um Rollen mit spezifischen Berechtigungen zu erstellen.

### <a name="resource-permissions"></a>Ressourcenberechtigungen

Wenn Benutzer Protokolle aus einem Arbeitsbereich mit Zugriff im Ressourcenkontext abfragen, verfügen sie über die folgenden Berechtigungen für die Ressource:

| Berechtigung | BESCHREIBUNG |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Beispiele:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Möglichkeit, alle Protokolldaten für die Ressource anzuzeigen.  |
| `Microsoft.Insights/diagnosticSettings/write` | Die Möglichkeit zum Konfigurieren von Diagnoseeinstellungen, um das Einrichten von Protokollen für diese Ressource zuzulassen. |

Die `/read`-Berechtigung wird in der Regel von einer Rolle erteilt, die _\*/read or_ _\*_ -Berechtigungen enthält, beispielsweise von den integrierten Rollen [Leser](../../role-based-access-control/built-in-roles.md#reader) und [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor). Benutzerdefinierte Rollen, die bestimmte Aktionen umfassen, oder dedizierte integrierte Rollen enthalten diese Berechtigung möglicherweise nicht.

Lesen Sie [Definieren von Zugriffssteuerung pro Tabelle](#table-level-azure-rbac) weiter unten, wenn Sie für verschiedene Tabellen eine unterschiedliche Zugriffssteuerung erstellen möchten.

## <a name="custom-role-examples"></a>Beispiele für benutzerdefinierte Rollen

1. Wenn Sie einem Benutzer Zugriff auf Protokolldaten seiner Ressourcen gewähren möchten, führen Sie die folgenden Schritte aus:

    * Legen Sie den Zugriffssteuerungsmodus für den Arbeitsbereich auf die **Verwendung von Arbeitsbereichs- oder Ressourcenberechtigungen** fest.

    * Erteilen Sie den Benutzern die Berechtigung `*/read` oder `Microsoft.Insights/logs/*/read` für ihre Ressourcen. Wenn den Benutzern bereits die Rolle [Log Analytics-Leser](../../role-based-access-control/built-in-roles.md#reader) für den Arbeitsbereich zugewiesen ist, reicht dies aus.

2. Wenn Sie einem Benutzer Zugriff auf Protokolldaten seiner Ressourcen und das Konfigurieren der Ressourcen zum Senden von Protokollen an den Arbeitsbereich gewähren möchten, führen Sie die folgenden Schritte aus:

    * Legen Sie den Zugriffssteuerungsmodus für den Arbeitsbereich auf die **Verwendung von Arbeitsbereichs- oder Ressourcenberechtigungen** fest.

    * Erteilen Sie den Benutzern die folgenden Berechtigungen für den Arbeitsbereich: `Microsoft.OperationalInsights/workspaces/read` und `Microsoft.OperationalInsights/workspaces/sharedKeys/action`. Mit diesen Berechtigungen können Benutzer keine Abfragen auf Arbeitsbereichsebene ausführen. Sie können den Arbeitsbereich lediglich aufzählen und ihn als Ziel für Diagnoseeinstellungen oder die Agentkonfiguration verwenden.

    * Erteilen Sie den Benutzern die folgenden Berechtigungen für ihre Ressourcen: `Microsoft.Insights/logs/*/read` und `Microsoft.Insights/diagnosticSettings/write`. Wenn den Benutzern bereits die Rolle [Log Analytics-Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor), die Leserrolle oder `*/read`-Berechtigungen für diese Ressource zugewiesen ist, reicht dies aus.

3. Führen Sie die folgenden Schritte aus, um einem Benutzer Zugriff auf Protokolldaten seiner Ressourcen zu erteilen, ohne dass er sicherheitsrelevante Ereignisse lesen oder Daten senden könnte:

    * Legen Sie den Zugriffssteuerungsmodus für den Arbeitsbereich auf die **Verwendung von Arbeitsbereichs- oder Ressourcenberechtigungen** fest.

    * Erteilen Sie den Benutzern die folgenden Berechtigungen für ihre Ressourcen: `Microsoft.Insights/logs/*/read`.

    * Fügen Sie die folgende NonAction hinzu, um das Lesen des SecurityEvent-Typs durch Benutzer zu blockieren: `Microsoft.Insights/logs/SecurityEvent/read`. Die NonAction muss sich in der gleichen benutzerdefinierten Rolle wie die Aktion befinden, die die Leseberechtigung bereitstellt (`Microsoft.Insights/logs/*/read`). Wenn der Benutzer die Leseaktion von einer anderen Rolle erbt, die dieser Ressource, dem Abonnement oder der Ressourcengruppe zugeordnet ist, ist er in der Lage, alle Protokolltypen zu lesen. Dies trifft ebenfalls zu, wenn er `*/read` erbt, beispielsweise aus der Rolle „Leser“ oder „Mitwirkender“.

4. Wenn Sie einem Benutzer Zugriff auf Protokolldaten seiner Ressourcen, das Lesen aller Azure AD-Anmeldeprotokolle sowie das Lesen von Protokolldaten der Updateverwaltungslösung aus dem Arbeitsbereich erteilen möchten, führen Sie die folgenden Schritte aus:

    * Legen Sie den Zugriffssteuerungsmodus für den Arbeitsbereich auf die **Verwendung von Arbeitsbereichs- oder Ressourcenberechtigungen** fest.

    * Erteilen Sie den Benutzern die folgenden Berechtigungen für den Arbeitsbereich: 

        * `Microsoft.OperationalInsights/workspaces/read` ist erforderlich, damit der Benutzer die Arbeitsbereiche auflisten und das Blatt für den Arbeitsbereich im Azure-Portal öffnen kann.
        * `Microsoft.OperationalInsights/workspaces/query/read` ist für jeden Benutzer erforderlich, der Abfragen ausführen kann.
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`, damit Azure AD-Anmeldeprotokolle gelesen werden können.
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`, damit Protokolle der Updateverwaltungslösung gelesen werden können.
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`, damit Protokolle der Updateverwaltungslösung gelesen werden können.
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`, damit Protokolle der Updateverwaltung gelesen werden können.
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` ist erforderlich, damit die Updateverwaltungslösung verwendet werden kann.
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` ist erforderlich, damit die Updateverwaltungslösung verwendet werden kann.

    * Erteilen Sie den Benutzern die folgenden Berechtigungen für ihre Ressourcen: `*/read`, die der Leserrolle zugewiesen ist, oder `Microsoft.Insights/logs/*/read`. 

## <a name="table-level-azure-rbac"></a>Azure RBAC auf Tabellenebene

**Azure RBAC auf Tabellenebene** ermöglicht zusätzlich zu den anderen Berechtigungen eine präzisere Steuerung von Daten in einem Log Analytics-Arbeitsbereich. Mit diesem Steuerelement können Sie bestimmte Datentypen definieren, auf die nur eine bestimmte Gruppe von Benutzern Zugriff besitzt.

Sie implementieren die Tabellenzugriffssteuerung mit [benutzerdefinierten Azure-Rollen](../../role-based-access-control/custom-roles.md), um den Zugriff auf bestimmte [Tabellen](./data-platform-logs.md) im Arbeitsbereich zu gewähren. Diese Rollen gelten für Arbeitsbereiche mit dem [Zugriffssteuerungsmodus](design-logs-deployment.md#access-control-mode) für den Arbeitsbereichskontxt oder Ressourcenkontext, und zwar unabhängig vom [Zugriffsmodus](design-logs-deployment.md#access-mode) des Benutzers.

Erstellen Sie eine [benutzerdefinierte Rolle](../../role-based-access-control/custom-roles.md) mit den folgenden Aktionen, um den Zugriff auf die Tabellenzugriffssteuerung zu definieren.

* Um den Zugriff auf eine Tabelle zu gewähren, fügen Sie sie im Abschnitt **Actions** der Rollendefinition hinzu. Um den Zugriff von den zulässigen **Aktionen** auszuschließen, fügen Sie sie im Abschnitt **NotActions** hinzu.
* Verwenden Sie „Microsoft.OperationalInsights/workspaces/query/*“, um alle Tabellen anzugeben.

Um beispielsweise eine Rolle mit Zugriff auf die Tabellen _Heartbeat_ und _AzureActivity_ zu erstellen, erstellen Sie eine benutzerdefinierte Rolle mit den folgenden Aktionen:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Um eine Rolle mit ausschließlichem Zugriff auf die Tabelle _SecurityBaseline_ zu erstellen, erstellen Sie eine benutzerdefinierte Rolle mit den folgenden Aktionen:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```
In den Beispielen oben wird eine Liste mit Tabellen definiert, die zulässig sind. In diesem Beispiel wird die Definition einer blockierten Liste gezeigt, wenn ein Benutzer auf alle Tabellen mit Ausnahme der Tabelle _SecurityAlert_ zugreifen kann:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
"notActions":  [
    "Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read"
],
```

### <a name="custom-logs"></a>Benutzerdefinierte Protokolle

 Benutzerdefinierte Protokolle werden aus Datenquellen wie etwa benutzerdefinierten Protokollen und der HTTP-Datensammler-API erstellt. Die einfachste Möglichkeit, den Typ des Protokolls zu identifizieren, besteht darin, die unter [Benutzerdefinierte Protokolle im Protokollschema](../log-query/log-analytics-tutorial.md#table-schema) aufgeführten Tabellen zu überprüfen.

 Sie können keinen Zugriff auf einzelne benutzerdefinierte Protokolle gewähren, aber Sie können Zugriff auf alle benutzerdefinierten Protokolle gewähren. Um eine Rolle mit Zugriff auf alle benutzerdefinierten Protokolle zu erstellen, erstellen Sie eine benutzerdefinierte Rolle mit den folgenden Aktionen:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
Als Alternative können Sie den Zugriff auf Protokolle verwalten, indem Sie diese einer Azure-Ressource zuweisen und den Zugriff über das Ressource-Kontext-Paradigma verwalten. Um diese Methode verwenden zu können, müssen Sie die Ressourcen-ID einschließen, indem Sie sie im [x-ms-AzureResourceId](data-collector-api.md#request-headers)-Header angeben, wenn die Daten über die [HTTP-Datensammler-API](data-collector-api.md) in Log Analytics erfasst werden. Die Ressourcen-ID muss gültig sein, und für sie müssen Zugriffsregeln angewendet worden sein. Nachdem die Protokolle erfasst wurden, können Benutzer mit Lesezugriff darauf zugreifen, wie hier erläutert.

Manchmal stammen benutzerdefinierte Protokolle aus Quellen, die nicht direkt einer bestimmten Ressource zugeordnet sind. Erstellen Sie in diesem Fall eine Ressourcengruppe, um nur den Zugriff auf diese Protokolle zu verwalten. Die Ressourcengruppe verursacht keine Kosten, bietet Ihnen aber eine gültige Ressourcen-ID zum Steuern des Zugriffs auf die benutzerdefinierten Protokolle. Wenn beispielsweise eine bestimmte Firewall benutzerdefinierte Protokolle sendet, erstellen Sie eine Ressourcengruppe namens „MyFireWallLogs“, und stellen Sie sicher, dass die API-Anforderungen die Ressourcen-ID von „MyFireWallLogs“ enthalten. So sind die Datensätze des Firewallprotokolls nur für Benutzer zugänglich, denen Zugriff auf „MyFireWallLogs“ gewährt wurde, oder für Benutzer mit Vollzugriff auf den Arbeitsbereich.          

### <a name="considerations"></a>Überlegungen

* Wenn einem Benutzer globale Leseberechtigung mit den Standardrollen „Leser“ oder „Mitwirkender“ erteilt wird, die die Aktion _\*/read_ beinhalten, überschreibt dies die Zugriffssteuerung pro Tabelle und gewährt Zugriff auf alle Protokolldaten.
* Wenn einem Benutzer Zugriff pro Tabelle, aber keine anderen Berechtigungen gewährt werden, kann er über die API auf Protokolldaten zugreifen, nicht aber über das Azure-Portal. Verwenden Sie „Log Analytics-Leser“ als Basisrolle zum Bereitstellen des Zugriffs vom Azure-Portal.
* Administratoren und Eigentümer des Abonnements verfügen über Zugriff auf alle Datentypen, und zwar unabhängig von anderen Berechtigungseinstellungen.
* Besitzer eines Arbeitsbereichs werden wie alle anderen Benutzer bei der Zugriffssteuerung pro Tabelle behandelt.
* Es wird empfohlen, Rollen Sicherheitsgruppen anstelle von einzelnen Benutzern zuzuweisen, um die Anzahl der Zuordnungen zu verringern. Dies unterstützt Sie auch bei der Verwendung vorhandener Gruppenverwaltungstools zum Konfigurieren und Überprüfen des Zugriffs.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Erfassen der Daten von Computern in Ihrem Datencenter oder einer anderen Cloudumgebung finden Sie unter [Collect log data with the Azure Log Analytics agent](./log-analytics-agent.md) (Sammeln von Protokolldaten mit dem Azure Log Analytics-Agent).

* Informationen zum Konfigurieren der Datensammlung von virtuellen Azure-Computern finden Sie unter [Sammeln von Daten über virtuelle Azure-Computer](../learn/quick-collect-azurevm.md).