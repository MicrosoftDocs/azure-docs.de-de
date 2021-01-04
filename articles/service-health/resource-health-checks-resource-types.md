---
title: Durch Azure Resource Health unterstützte Ressourcentypen | Microsoft-Dokumentation
description: Durch Azure Resource Health unterstützte Ressourcentypen
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 79f979b55366d29ca8cf22a1e65ebdcc557ef594
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899497"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Ressourcentypen und Integritätsprüfungen in Azure Resource Health
Es folgt eine vollständige Auflistung nach Ressourcentypen aller von Resource Health ausgeführten Überprüfungen.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der Server betriebsbereit?</li><li>Ist für den Server genügend Arbeitsspeicher verfügbar?</li><li>Wird der Server gestartet?</li><li>Wird der Server wiederhergestellt?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der API Management-Dienst aktiv und wird ausgeführt?</li></ul>|

## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist die Azure Spring Cloud-Instanz verfügbar?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Wird das Batch-Konto ausgeführt?</li><li>Wurde das Poolkontingent für dieses Batch-Konto überschritten?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/Redis
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Sind alle Cacheknoten funktionstüchtig?</li><li>Kann der Cache vom Rechenzentrum aus erreicht werden?</li><li>Hat der Cache die maximale Anzahl von Verbindungen erreicht?</li><li> Hat der Cache seinen verfügbaren Arbeitsspeicher ausgschöpft? </li><li>Tritt im Cache eine große Anzahl von Seitenfehlern auf?</li><li>Ist der Cache stark ausgelastet?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Ausgeführte Überprüfungen|
|---|
|<ul> <li>Können CDN-Konfigurationsvorgänge auf das zusätzliche Portal zugreifen?</li><li>Treten bei den CDN-Endpunkten laufend Übermittlungsprobleme auf?</li><li>Können Benutzer die Konfiguration ihrer CDN-Ressourcen ändern?</li><li>Werden Konfigurationsänderungen mit der erwarteten Rate weitergegeben?</li><li>Können Benutzer die CDN-Konfiguration mithilfe von Azure-Portal, PowerShell oder API verwalten?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der Hostserver funktionstüchtig?</li><li>Ist das Starten des Hostbetriebssystems abgeschlossen?</li><li>Ist der VM-Container bereitgestellt und hochgefahren?</li><li>Besteht Netzwerkkonnektivität zwischen Host und Speicherkonto?</li><li>Ist das Starten des Gastbetriebssystems abgeschlossen?</li><li>Gibt es eine laufende geplante Wartung?</li><li>Wurde die Hosthardware heruntergestuft und ein baldiger Ausfall vorhergesagt?</li></ul>|

## <a name="microsoftclassiccomputedomainnames"></a>Microsoft.classiccompute/domainnames
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist die Produktionsslotbereitstellung in allen Rolleninstanzen fehlerfrei?</li><li>Ist die Rolle in allen VM-Instanzen fehlerfrei?</li><li>Wie ist der Integritätsstatus der einzelnen virtuellen Computer innerhalb einer Rolle eines Clouddiensts?</li><li>Hat sich der VM-Status aufgrund eines von der Plattform oder vom Kunden initiierten Vorgangs geändert?</li><li>Ist das Starten des Gastbetriebssystems abgeschlossen?</li><li>Gibt es eine laufende geplante Wartung?</li><li>Wurde die Hosthardware heruntergestuft und ein baldiger Ausfall vorhergesagt?</li><li>[Weitere Informationen zu ausgeführten Überprüfungen](../cloud-services/resource-health-for-cloud-services.md)</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Kann das Konto vom Rechenzentrum aus erreicht werden?</li><li>Ist der Cognitive Services-Ressourcenanbieter verfügbar?</li><li>Sind die Cognitive Services in der entsprechenden Region verfügbar?</li><li>Können Lesevorgänge in dem Speicherkonto durchgeführt werden, auf dem die Ressourcenmetadaten gespeichert sind?</li><li>Wurde das API-Aufrufkontingent erreicht?</li><li>Wurde die API-Aufruflesebeschränkung erreicht?</li></ul>|

## <a name="microsoftcomputehostgroupshosts"></a>Microsoft.compute/hostgroups/hosts
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der Host aktiv und wird ausgeführt?</li><li>Wurde die Hosthardware heruntergestuft?</li><li>Wurde die Zuordnung des Hosts aufgehoben?</li><li>Ist der Hosthardwaredienst auf unterschiedlicher Hardware verfügbar?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Hostet der Server diese VM funktionstüchtig?</li><li>Ist das Starten des Hostbetriebssystems abgeschlossen?</li><li>Ist der VM-Container bereitgestellt und hochgefahren?</li><li>Besteht Netzwerkkonnektivität zwischen Host und Speicherkonto?</li><li>Ist das Starten des Gastbetriebssystems abgeschlossen?</li><li>Gibt es eine laufende geplante Wartung?</li><li>Wurde die Hosthardware heruntergestuft und ein baldiger Ausfall vorhergesagt?</li></ul>|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der Cluster aktiv und wird ausgeführt?</li><li>Sind Kerndienste im Cluster verfügbar?</li><li>Sind alle Clusterknoten bereit?</li><li>Ist der Dienstprinzipal aktuell und gültig?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/factories
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Sind Fehler bei der Ausführung der Pipeline aufgetreten?</li><li>Hostet der Cluster die Data Factory fehlerfrei?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Sind bei Benutzern Probleme beim Übermitteln oder Auflisten ihrer Data Lake Analytics-Aufträge aufgetreten?</li><li>Können Data Lake Analytics-Aufträge aufgrund von Systemfehlern nicht abgeschlossen werden?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Sind bei Benutzern Probleme beim Hochladen von Daten in Data Lake Store aufgetreten?</li><li>Sind bei Benutzern Probleme beim Herunterladen von Daten aus Data Lake Store aufgetreten?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist ein Fehler bei der Bereitstellung des Datenbankmigrationsdiensts aufgetreten?</li><li>Wurde die Datenbankmigrationsdienst-Instanz aufgrund von Inaktivität oder einer Benutzeranforderung beendet?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Wird das Data Share-Konto ausgeführt?</li><li>Ist der Cluster, der Data Share hostet, verfügbar?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der Server aufgrund von Wartungsarbeiten nicht verfügbar?</li><li>Ist der Server aufgrund einer Neukonfiguration nicht verfügbar?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der Server aufgrund von Wartungsarbeiten nicht verfügbar?</li><li>Ist der Server aufgrund einer Neukonfiguration nicht verfügbar?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der Server aufgrund von Wartungsarbeiten nicht verfügbar?</li><li>Ist der Server aufgrund einer Neukonfiguration nicht verfügbar?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Wird IoT Hub ausgeführt?</li></ul>|

## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/DigitalTwinsInstances
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist die Azure Digital Twins-Instanz aktiv und wird ausgeführt?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Wurden Datenbank- oder Sammlungsanforderungen nicht ausgeführt, weil der Azure Cosmos DB-Dienst nicht verfügbar war?</li><li>Wurden Dokumentanforderungen nicht ausgeführt, weil der Azure Cosmos DB-Dienst nicht verfügbar war?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/namespaces
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Treten im Events Hub-Namespace benutzergenerierte Fehler auf?</li><li>Wird für den Event Hubs-Namespace zurzeit ein Upgrade durchgeführt?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Sind Kerndienste für den HDInsight-Cluster verfügbar?</li><li>Kann der HDInsight-Cluster auf den Schlüssel für die BYOK-Verschlüsselung ruhender Daten zugreifen?</li></ul>|

## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist die IoT Central-Anwendung verfügbar?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Sind an den Schlüsseltresor gerichtete Anforderungen aufgrund von Problemen mit der Azure Key Vault-Plattform nicht erfolgreich?</li><li>Werden an den Schlüsseltresor gerichtete Anforderungen aufgrund von übermäßig vielen Anforderungen des Kunden gedrosselt?</li></ul>|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/clusters
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Treten beim Cluster niedrige Erfolgsraten bei der Erfassung auf?</li><li>Treten beim Cluster hohe Latenzen bei der Erfassung auf?</li><li>Treten beim Cluster viele Abfragefehler auf?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Wird der Webdienst ausgeführt?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Wird der Mediendienst ausgeführt?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist die Leistung der Application Gateway-Instanz beeinträchtigt?</li><li>Ist das Application Gateway verfügbar?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der VPN-Tunnel verbunden?</li><li>Treten Konfigurationskonflikte in der Verbindung auf?</li><li>Sind die vorinstallierten Schlüssel ordnungsgemäß konfiguriert?</li><li>Ist das lokale VPN-Gerät erreichbar?</li><li>Treten Konflikte in der IPSec-/IKE-Sicherheitsrichtlinie auf?</li><li>Ist die S2S-VPN-Verbindung ordnungsgemäß bereitgestellt oder in einem fehlerhaften Zustand?</li><li>Ist die VNET-zu-VNET-Verbindung ordnungsgemäß bereitgestellt oder in einem fehlerhaften Zustand?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist die ExpressRoute-Verbindung integer?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Antworten Front Door-Back-Ends mit Fehlern auf Integritätstests?</li><li>Wurden Konfigurationsänderungen verzögert?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.network/LoadBalancers
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Sind die Endpunkte des Lastenausgleichs verfügbar?</li></ul>|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.network/trafficmanagerprofiles
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Gibt es Probleme, die sich auf das Traffic Manager-Profil auswirken?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist das VPN-Gateway vom Internet aus erreichbar?</li><li>Ist das VPN-Gateway im Standbymodus?</li><li>Wird der VPN-Dienst auf dem Gateway ausgeführt?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Können Laufzeitvorgänge wie Registrierung, Installation oder Senden im Namespace ausgeführt werden?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Gibt es Indizierungsverzögerungen für den Arbeitsbereich?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Wird die Kapazitätsressource ausgeführt?</li><li>Werden alle Workloads ausgeführt?</li></ul>

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Können Diagnosevorgänge auf dem Cluster ausgeführt werden?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Treten bei Kunden benutzergenerierte Service Bus-Fehler auf?</li><li>Treten bei Benutzern nach dem Upgrade eines Service Bus-Namespace vermehrt vorübergehende Fehler auf?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft.ServiceFabric/clusters
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der Service Fabric Cluster aktiv und wird ausgeführt?</li><li>Kann der Service Fabric-Cluster über Azure Resource Manager verwaltet werden?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.SQL/managedInstances/databases
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Wird die Datenbank ausgeführt?</li></ul>|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.SQL/servers/databases
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Wurden Anmeldungen bei der Datenbank durchgeführt?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Sind Anforderungen zum Lesen von Daten aus dem Speicherkonto aufgrund von Problemen mit der Azure Storage-Plattform nicht erfolgreich?</li><li>Sind Anforderungen zum Schreiben von Daten in das Speicherkonto aufgrund von Problemen mit der Azure Storage-Plattform nicht erfolgreich?</li><li>Ist der Speichercluster, in dem sich das Speicherkonto befindet, nicht verfügbar?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Sind alle Hosts funktionstüchtig, auf denen der Auftrag ausgeführt wird?</li><li>Konnte der Auftrag nicht gestartet werden?</li><li>Gibt es laufende Laufzeitupgrades?</li><li>Ist der Auftrag in einem erwarteten Zustand (z.B. vom Kunden ausgeführt oder beendet)?</li><li>Sind bei dem Auftrag Arbeitsspeichermangel-Ausnahmefehler aufgetreten?</li><li>Gibt es laufende geplante Computeupdates?</li><li>Ist der Ausführungs-Manager (Steuerungsplan) verfügbar?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der Hostserver funktionstüchtig?</li><li>Werden die Internetinformationsdienste ausgeführt?</li><li>Wird der Load Balancer ausgeführt?</li><li>Kann der App Service-Plan vom Rechenzentrum aus erreicht werden?</li><li>Ist das Speicherkonto verfügbar, das den Inhalt der Websites für die Serverfarm hostet?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Ausgeführte Überprüfungen|
|---|
|<ul><li>Ist der Hostserver funktionstüchtig?</li><li>Wir der Internet Information Server ausgeführt?</li><li>Wird der Load Balancer ausgeführt?</li><li>Kann die Web-App vom Rechenzentrum aus erreicht werden?</li><li>Ist das Speicherkonto verfügbar, das den Inhalt der Website hostet?</li></ul>|

## <a name="next-steps"></a>Nächste Schritte
-  Weitere Informationen finden Sie unter [Dienstintegrität](service-health-overview.md) und [Übersicht über Azure Resource Health](resource-health-overview.md). 
-  [Azure Resource Health – FAQ](resource-health-faq.md)
- Richten Sie Warnungen ein, damit Sie über Integritätsprobleme benachrichtigt werden. Weitere Informationen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen zu Dienstbenachrichtigungen](./alerts-activity-log-service-notifications-portal.md).