---
title: Rollen, Berechtigungen und Sicherheit in Azure Monitor
description: Erfahren Sie mehr über die Verwendung der vordefinierten Rollen und Berechtigungen in Azure Monitor, um den Zugriff auf Überwachungsressourcen zu beschränken.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 84ae5f6adfe2a02f62b5d4b1e776d8b5ac1d731b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95975348"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Rollen, Berechtigungen und Sicherheit in Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Viele Teams müssen den Zugriff auf Überwachungsdaten und -einstellungen streng regulieren. Wenn einige Ihrer Teammitglieder beispielsweise ausschließlich an der Überwachung arbeiten (Supporttechniker, DevOps-Techniker) oder wenn Sie einen verwalteten Dienstanbieter verwenden, sollten Sie diesen nur Zugriff auf Überwachungsdaten erteilen und deren Möglichkeit zum Erstellen, Ändern oder Löschen von Ressourcen einschränken. Dieser Artikel beschreibt, wie Sie schnell eine integrierte Azure-Rolle zur Überwachung auf einen Benutzer in Azure anwenden oder Ihre eigene benutzerdefinierte Rolle für einen Benutzer erstellen, der eingeschränkte Überwachungsberechtigungen benötigt. Anschließend werden Sicherheitsaspekte für Ihre Azure Monitor-Ressourcen erörtert, und es wird beschrieben, wie Sie den Zugriff auf die darin enthaltenen Daten beschränken können.

## <a name="built-in-monitoring-roles"></a>Integrierte Überwachungsrollen
Die in Azure Monitor integrierten Rollen unterstützen Sie dabei, den Zugriff auf Ressourcen in einem Abonnement einzuschränken und gleichzeitig den Personen, die für die Überwachung der Infrastruktur verantwortlich sind, das Abrufen und Konfigurieren der benötigten Daten zu ermöglichen. Azure Monitor bietet zwei vorkonfigurierte Rollen: eine für das Lesen von Überwachungsdaten und eine für Mitwirkende an der Überwachung.

### <a name="monitoring-reader"></a>Überwachungsleser
Personen, denen die Überwachungsleserrolle zugewiesen wird, können alle Überwachungsdaten in einem Abonnement anzeigen, aber keine Ressourcen ändern oder Einstellungen im Zusammenhang mit der Ressourcenüberwachung bearbeiten. Diese Rolle eignet sich für Benutzer in einer Organisation, beispielsweise Support- oder Betriebstechniker, die folgende Aufgaben erfüllen müssen:

* Anzeigen von Überwachungsdashboards im Portal und Erstellen ihrer eigenen privaten Überwachungsdashboards.
* Anzeigen von in [Azure-Warnungen](alerts-overview.md) definierten Warnungsregeln
* Abfragen von Metriken über die [Azure Monitor-REST-API](/rest/api/monitor/metrics), [PowerShell-Cmdlets](../samples/powershell-samples.md) oder die [plattformübergreifende Befehlszeilenschnittstelle](../samples/cli-samples.md).
* Abfragen des Aktivitätsprotokolls über das Portal, die Azure Monitor-REST-API, PowerShell-Cmdlets oder die plattformübergreifende Befehlszeilenschnittstelle.
* Anzeigen der [Diagnoseeinstellungen](diagnostic-settings.md) für eine Ressource.
* Anzeigen des [Protokollprofils](./activity-log.md#legacy-collection-methods) für ein Abonnement.
* Anzeigen von Einstellungen für die automatische Skalierung.
* Anzeigen von Warnaktivitäten und -einstellungen.
* Zugreifen auf Application Insights-Daten und Anzeigen von Daten in der AI-Analyse.
* Durchsuchen von Arbeitsbereichsdaten von Log Analytics, einschließlich Nutzungsdaten für den Arbeitsbereich
* Anzeigen von Verwaltungsgruppen in Log Analytics
* Abrufen des Suchschemas im Log Analytics-Arbeitsbereich
* Auflisten von Überwachungspaketen im Log Analytics-Arbeitsbereich
* Abrufen und Ausführen gespeicherter Suchvorgänge im Log Analytics-Arbeitsbereich
* Abrufen der Speicherkonfiguration des Log Analytics-Arbeitsbereichs

> [!NOTE]
> Diese Rolle erteilt keinen Lesezugriff für Protokolldaten, die an einen Event Hub gestreamt oder in einem Speicherkonto gespeichert wurden. [Unten](#security-considerations-for-monitoring-data) finden Sie Informationen zum Konfigurieren des Zugriffs auf diese Ressourcen.
> 
> 

### <a name="monitoring-contributor"></a>Überwachungsmitwirkender
Personen, denen die Rolle für Überwachungsmitwirkende zugewiesen wird, können alle Überwachungsdaten in einem Abonnement anzeigen sowie Überwachungseinstellungen erstellen und ändern, aber keine weiteren Ressourcen ändern. Diese Rolle ist eine Obermenge der Überwachungsleserrolle und eignet sich für Mitglieder des Überwachungsteams einer Organisation oder für Anbieter verwalteter Dienste, die neben den oben genannten Berechtigungen auch folgende Aufgaben ausführen müssen:

* Veröffentlichen von Überwachungsdashboards als freigegebenes Dashboard.
* Festlegen von [Diagnoseeinstellungen](diagnostic-settings.md) für eine Ressource.\*
* Festlegen des [Protokollprofils](./activity-log.md#legacy-collection-methods) für ein Abonnement.\*
* Festlegen der Aktivität und der Einstellungen für Warnungsregeln über [Azure-Warnungen](alerts-overview.md).
* Erstellen von Application Insights-Webtests und -Komponenten.
* Auflisten gemeinsam verwendeter Schlüssel für Log Analytics-Arbeitsbereiche
* Aktivieren oder Deaktivieren von Überwachungspaketen im Log Analytics-Arbeitsbereich
* Erstellen, Löschen und Ausführen gespeicherter Suchvorgänge im Log Analytics-Arbeitsbereich
* Erstellen und Löschen der Speicherkonfiguration des Log Analytics-Arbeitsbereichs

\*Dem Benutzer muss außerdem gesondert die ListKeys-Berechtigung für die Zielressource (Speicherkonto oder Event Hub-Namespace) erteilt werden, um ein Protokollprofil oder eine Diagnoseeinstellung festzulegen.

> [!NOTE]
> Diese Rolle erteilt keinen Lesezugriff für Protokolldaten, die an einen Event Hub gestreamt oder in einem Speicherkonto gespeichert wurden. [Unten](#security-considerations-for-monitoring-data) finden Sie Informationen zum Konfigurieren des Zugriffs auf diese Ressourcen.
> 
> 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>Überwachen von Berechtigungen und benutzerdefinierte Azure-Rollen
Wenn die oben genannten vordefinierten Rollen nicht den genauen Anforderungen Ihres Teams entsprechen, können Sie eine [benutzerdefinierte Azure-Rolle](../../role-based-access-control/custom-roles.md) mit detaillierteren Berechtigungen erstellen. Im Folgenden sind die allgemeinen Azure RBAC-Vorgänge für Azure Monitor mit ihren Beschreibungen aufgeführt.

| Vorgang | BESCHREIBUNG |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Lesen/Schreiben/Löschen einer Aktionsgruppe |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Lesen/Schreiben/Löschen von Aktivitätsprotokollwarnungen |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Lesen/Schreiben/Löschen von Warnungsregeln (aus klassischen Warnungen). |
| Microsoft.Insights/AlertRules/Incidents/Read |Auflisten von Incidents (Verlauf der ausgelösten Warnungsregel) für Warnungsregeln. Dies gilt nur für das Portal. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Lesen/Schreiben/Löschen von Einstellungen für die automatische Skalierung. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Lesen/Schreiben/Löschen von Diagnoseeinstellungen. |
| Microsoft.Insights/EventCategories/Read |Aufzählen aller im Aktivitätsprotokoll möglichen Kategorien Wird vom Azure-Portal verwendet. |
| Microsoft.Insights/eventtypes/digestevents/Read |Diese Berechtigung ist für Benutzer notwendig, die über das Portal auf Aktivitätsprotokolle zugreifen müssen. |
| Microsoft.Insights/eventtypes/values/Read |Auflisten von Aktivitätsprotokollereignissen (Verwaltungsereignissen) in einem Abonnement. Diese Berechtigung gilt sowohl für den programmgesteuerten als auch für den Portalzugriff auf das Aktivitätsprotokoll. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Lesen/Schreiben/Löschen von Diagnoseeinstellungen für Netzwerkflussprotokolle |
| Microsoft.Insights/LogDefinitions/Read |Diese Berechtigung ist für Benutzer notwendig, die über das Portal auf Aktivitätsprotokolle zugreifen müssen. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Lesen/Schreiben/Löschen von Protokollprofilen (Streamen von Aktivitätsprotokollen an ein Ereignishub- oder Speicherkonto) |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Lesen/Schreiben/Löschen von Metrikwarnungen (nahezu in Echtzeit) |
| Microsoft.Insights/MetricDefinitions/Read |Lesen von Metrikdefinitionen (Liste der verfügbaren Metriktypen für eine Ressource). |
| Microsoft.Insights/Metrics/Read |Lesen von Metriken für eine Ressource. |
| Microsoft.Insights/Register/Action |Registrieren des Azure Monitor-Ressourcenanbieters |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Protokollwarnungen für Lesen, Schreiben und Löschen in Azure Monitor. |



> [!NOTE]
> Für den Zugriff auf Warnungen, Diagnoseeinstellungen und Metriken für eine Ressource ist es erforderlich, dass der Benutzer für den Ressourcentyp und den Bereich der jeweiligen Ressource über Lesezugriff verfügt. Für das Erstellen („Schreiben“) einer Diagnoseeinstellung oder eines Protokollprofils, bei der bzw. dem Daten in einem Speicherkonto archiviert oder an Event Hubs gestreamt werden, muss der Benutzer zudem die ListKeys-Berechtigung für die Zielressource besitzen.
> 
> 

Beispielsweise können Sie anhand der oben dargestellten Tabelle wie folgt eine benutzerdefinierte Azure-Rolle für einen „Aktivitätsprotokollleser“ erstellen:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Sicherheitsaspekte für Überwachungsdaten
Überwachungsdaten – besonders Protokolldateien – können vertrauliche Informationen wie IP-Adressen oder Benutzernamen enthalten. Überwachungsdaten aus Azure werden in drei grundlegenden Varianten bereitgestellt:

1. Das Aktivitätsprotokoll, in dem alle Aktionen auf Steuerungsebene in Ihrem Azure-Abonnement beschrieben werden.
2. Diagnoseprotokolle, die von einer Ressource ausgegeben werden.
3. Metriken, die von Ressourcen ausgegeben werden.

Alle drei Datentypen können in einem Speicherkonto gespeichert oder an Event Hub gestreamt werden. Bei beidem handelt es sich um allgemeine Azure-Ressourcen. Da es sich um allgemeine Ressourcen handelt, sind Erstellung, Löschung und der Zugriff darauf privilegierte Vorgänge, die einem Administrator vorbehalten sind. Wir empfehlen, die folgenden Methoden für überwachungsbezogene Ressourcen anzuwenden, um Missbrauch zu verhindern:

* Verwenden Sie ein einzelnes dediziertes Speicherkonto für die Überwachung von Daten. Wenn Sie Überwachungsdaten auf mehrere Speicherkonten aufteilen müssen, nutzen Sie niemals ein Speicherkonto sowohl für Überwachungsdaten als auch für überwachungsfremde Daten, da Lösungen, die nur auf Überwachungsdaten zugreifen müssen (beispielsweise eine Drittanbieter-SIEM-Lösung), dadurch unbeabsichtigterweise Zugriff auf überwachungsfremde Daten erhalten.
* Verwenden Sie aus demselben Grund einen einzelnen dedizierten Service Bus- oder Event Hub-Namespace über alle Diagnoseeinstellungen hinweg.
* Schränken Sie den Zugriff auf überwachungsbezogene Speicherkonten oder Event Hubs ein, indem Sie sie in einer separaten Ressourcengruppe verwalten, und verwenden Sie [Bereiche](../../role-based-access-control/overview.md#scope) für Ihre Überwachungsrollen, um den Zugriff ausschließlich auf diese Ressourcengruppe zu beschränken.
* Erteilen Sie niemals die ListKeys-Berechtigung für Speicherkonten oder Event Hubs im Abonnementbereich, wenn ein Benutzer nur auf Überwachungsdaten zugreifen muss. Erteilen Sie dem Benutzer diese Berechtigungen stattdessen im Ressourcen- oder Ressourcengruppenbereich (wenn Sie eine dedizierte Überwachungsressourcengruppe verwenden).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Einschränken des Zugriffs auf überwachungsbezogene Speicherkonten
Wenn ein Benutzer oder eine Anwendung Zugriff auf Überwachungsdaten in einem Speicherkonto benötigt, sollten Sie für das Speicherkonto, das Überwachungsdaten enthält, [eine Konto-SAS](/rest/api/storageservices/create-account-sas) mit Nur-Lese-Zugriff auf Dienstebene für Blobspeicher erstellen. In PowerShell kann dies wie folgt aussehen:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Sie können das Token an die Entität übergeben, die aus dem jeweiligen Speicherkonto lesen muss, damit sie alle Blobs in diesem Speicherkonto auflisten und lesen kann.

Wenn Sie diese Berechtigung mit Azure RBAC steuern müssen, können Sie dieser Entität alternativ die Berechtigung „Microsoft.Storage/storageAccounts/listkeys/action“ für das jeweilige Speicherkonto erteilen. Dies ist für Benutzer notwendig, die eine Diagnoseeinstellung oder ein Protokollprofil für die Archivierung in einem Speicherkonto festlegen müssen. Beispielsweise könnten Sie die folgende benutzerdefinierte Azure-Rolle für Benutzer oder Anwendungen erstellen, die nur aus einem einzigen Speicherkonto lesen müssen:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> Die ListKeys-Berechtigung ermöglicht dem Benutzer das Auflisten des primären und des sekundären Speicherkontoschlüssels. Diese Schlüssel gewähren dem Benutzer alle signierten Berechtigungen (Lesen, Schreiben, Erstellen von Blobs, Löschen von Blobs usw.) für alle signierten Dienste (Blob, Warteschlange, Tabelle, Datei) in diesem Speicherkonto. Wir empfehlen nach Möglichkeit die Verwendung einer Konto-SAS, wie oben beschriebenen.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Einschränken des Zugriffs auf überwachungsbezogene Event Hubs
Für Event Hubs können Sie einem ähnlichen Muster folgen, jedoch müssen Sie zunächst eine dedizierte Autorisierungsregel für Lauschvorgänge erstellen. Wenn Sie einer Anwendung Zugriff gewähren möchten, die nur auf überwachungsbezogene Event Hubs lauschen muss, gehen Sie wie folgt vor:

1. Erstellen Sie eine SAS-Richtlinie für die Event Hubs, die für das Streamen von Überwachungsdaten mit ausschließlich Lauschansprüchen erstellt haben. Dies kann im Portal erfolgen. Beispielsweise könnten Sie die Richtlinie „MonitoringReadOnly“ nennen. Wenn möglich, sollten Sie diesen Schlüssel direkt an den Consumer übergeben und den nächsten Schritt überspringen.
2. Wenn der Consumer den Schlüssel ad hoc abrufen können muss, gewähren Sie dem Benutzer die ListKeys-Aktion für diesen Event Hub. Dies ist auch für Benutzer notwendig, die eine Diagnoseeinstellung oder ein Protokollprofil für das Streamen in Event Hubs festlegen müssen. Beispielsweise können Sie eine Azure RBAC-Regel erstellen:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Überwachung innerhalb eines gesicherten virtuellen Netzwerks

Azure Monitor benötigt Zugriff auf Ihre Azure-Ressourcen, um die von Ihnen aktivierten Dienste bereitzustellen. Wenn Sie Ihre Azure-Ressourcen überwachen und dennoch vor dem Zugriff auf das öffentliche Internet schützen möchten, können Sie die folgenden Einstellungen aktivieren.

### <a name="secured-storage-accounts"></a>Geschützte Speicherkonten 

Überwachungsdaten werden häufig in ein Speicherkonto geschrieben. Sie sollten sicherstellen, dass die auf ein Speicherkonto kopierten Daten nicht von nicht autorisierten Benutzern eingesehen werden können. Für zusätzliche Sicherheit können Sie den Netzwerkzugriff sperren, um nur Ihren autorisierten Ressourcen und vertrauenswürdigen Microsoft-Diensten den Zugriff auf ein Speicherkonto zu ermöglichen, indem Sie ein Speicherkonto auf die Verwendung durch „ausgewählte Netzwerke“ beschränken.
![Dialogfeld „Einstellungen“ von Azure Storage:](./media/roles-permissions-security/secured-storage-example.png) Azure Monitor gilt als einer dieser vertrauenswürdigen Microsoft-Dienste. Wenn Sie vertrauenswürdigen Microsoft-Diensten den Zugriff auf Ihren geschützten Speicher erlauben, hat Azure Monitor Zugriff auf Ihr geschütztes Speicherkonto und kann unter diesen geschützten Bedingungen Ressourcenprotokolle, Aktivitätsprotokolle und Metriken von Azure Monitor in Ihr Speicherkonto schreiben. Dadurch kann Log Analytics auch Protokolle aus dem geschützten Speicher lesen.   


Weitere Informationen finden Sie unter [Netzwerksicherheitsgruppen und Azure Storage](../../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Nächste Schritte
* [Weitere Informationen zu Azure RBAC und Berechtigungen in Resource Manager](../../role-based-access-control/overview.md)
* [Übersicht über die Überwachung in Microsoft Azure](../overview.md)

