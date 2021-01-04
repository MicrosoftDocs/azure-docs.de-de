---
title: Ändern von Azure Service Fabric-Clustereinstellungen
description: Dieser Artikel beschreibt die Fabric-Einstellungen und Fabric-Upgraderichtlinien, die Sie anpassen können.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: c055ad1dad8b9574c8d811284a34619ee3648a10
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095269"
---
# <a name="customize-service-fabric-cluster-settings"></a>Anpassen von Service Fabric-Clustereinstellungen
Dieser Artikel beschreibt die verschiedenen Fabric-Einstellungen, die Sie für Ihren Service Fabric-Cluster anpassen können. Für in Azure gehostete Cluster können Sie Einstellungen über das [Azure-Portal](https://portal.azure.com) oder mithilfe einer Azure Resource Manager-Vorlage anpassen. Weitere Informationen finden Sie unter [Aktualisieren der Konfiguration eines Azure-Clusters](service-fabric-cluster-config-upgrade-azure.md). Für eigenständige Cluster passen Sie die Einstellungen durch Aktualisieren der Datei *ClusterConfig.json* und ein Konfigurationsupgrade in Ihrem Cluster an. Weitere Informationen finden Sie unter [Aktualisieren der Konfiguration eines eigenständigen Clusters](service-fabric-cluster-config-upgrade-windows-server.md).

Es gibt drei verschiedene Upgraderichtlinien:

- **Dynamisch**: Änderungen an einer dynamischen Konfiguration führen weder zu einem Neustart von Service Fabric-Prozessen noch von Diensthostprozessen. 
- **Statisch**: Änderungen an einer statischen Konfiguration führen zum Neustart des Service Fabric-Knotens, um die Änderung zu übernehmen. Dienste auf den Knoten werden neu gestartet.
- **Unzulässig**. Diese Einstellungen können nicht geändert werden. Um diese Einstellungen zu ändern, muss der Cluster gelöscht und ein neuer Cluster angelegt werden. 

In der folgenden Liste sind, zusammengestellt nach Abschnitt, die Fabric-Einstellungen aufgeführt, die Sie anpassen können.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|string, Standardwert „None“|statischen| Dadurch wird das Serverzertifikat nicht überprüft; die Anforderung wird erfolgreich abgeschlossen. Die durch Kommas getrennte Liste der Fingerabdrücke der Remotezertifikate, denen der Reverseproxy vertrauen kann, finden Sie unter „config ServiceCertificateThumbprints“. Den Antragstellernamen und den Ausstellerfingerabdruck des Remotezertifikats, denen der Reverseproxy vertrauen kann, finden Sie unter „config ServiceCommonNameAndIssuer“. Weitere Informationen finden Sie unter [Sichere Reverseproxyverbindung](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, Standardwert 16384 |Dynamisch| Gibt die Größe des Blocks in Bytes an, der zum Lesen des Texts verwendet wird. |
|CrlCheckingFlag|uint, Standardwert 0 x 40000000 |Dynamisch| Flags für die Überprüfung der Anwendungs- bzw. Dienstzertifikatkette, z.B. Zertifikatssperrlistenüberprüfung 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY. Bei Festlegen des Werts auf 0 wird die Zertifikatssperrlistenüberprüfung deaktiviert. Die vollständige Liste der unterstützten Werte wird durch dwFlags von CertGetCertificateChain dokumentiert: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Zeit in Sekunden. Standardwert 120 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an.  Gibt das standardmäßige Anforderungstimeout für die HTTP-Anforderungen an, die im HTTP-App-Gateway verarbeitet werden. |
|ForwardClientCertificate|Boolesch, Standardwert FALSE|Dynamisch|Wenn dieser Parameter auf FALSE gesetzt ist, fordert der Reverseproxy das Clientzertifikat nicht an. Wenn er auf TRUE festgelegt ist, fordert der Reverseproxy das Clientzertifikat beim TLS-Handshake an und leitet die Base64-verschlüsselte PEM-Formatzeichenfolge in einem Header namens „X-Client-Certificate“ an den Dienst weiter. Die Anforderung kann mit dem entsprechenden Statuscode für den Dienst fehlschlagen, nachdem die Zertifikatsdaten überprüft wurden. Wenn dies der Fall ist und der Client kein Zertifikat vorlegt, leitet der Reverseproxy einen leeren Header weiter und lässt den Dienst den Fall bearbeiten. Der Reverseproxy fungiert als transparente Ebene. Weitere Informationen finden Sie unter [Einrichten der Authentifizierung mit Clientzertifikat](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |string, Standardwert „None“ |statischen| Gibt den Typ der Sicherheitsanmeldeinformationen an, die am HTTP-App-Gatewayendpunkt verwendet werden sollen. Gültige Werte sind „None/X509“. |
|GatewayX509CertificateFindType |string, Standardwert „FindByThumbprint“ |Dynamisch| Gibt an, wie nach dem Zertifikat im durch GatewayX509CertificateStoreName angegebenen Speicher gesucht werden soll. Unterstützte Werte: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | string, Standardwert "" |Dynamisch| Suchfilterwert, der zum Suchen des HTTP-App-Gatewayzertifikats verwendet wird. Dieses Zertifikat ist für den HTTPS-Endpunkt konfiguriert und kann bei Bedarf auch von den Diensten zum Überprüfen der Identität der App verwendet werden. Nach FindValue wird zuerst gesucht. Wenn nicht vorhanden, wird nach FindValueSecondary gesucht. |
|GatewayX509CertificateFindValueSecondary | string, Standardwert "" |Dynamisch|Suchfilterwert, der zum Suchen des HTTP-App-Gatewayzertifikats verwendet wird. Dieses Zertifikat ist für den HTTPS-Endpunkt konfiguriert und kann bei Bedarf auch von den Diensten zum Überprüfen der Identität der App verwendet werden. Nach FindValue wird zuerst gesucht. Wenn nicht vorhanden, wird nach FindValueSecondary gesucht.|
|GatewayX509CertificateStoreName |string, Standardwert „My“ |Dynamisch| Name des X.509-Zertifikatspeichers, der das Zertifikat für das HTTP-App-Gateway enthält. |
|HttpRequestConnectTimeout|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(5)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an.  Gibt das Verbindungstimeout für die HTTP-Anforderungen an, die vom HTTP-App-Gateway gesendet werden.  |
|IgnoreCrlOfflineError|Boolesch, Standardwert TRUE|Dynamisch|Gibt an, ob ein Zertifikatsperrlisten-Offlinefehler bei der Überprüfung des Anwendungs- bzw. Dienstzertifikats ignoriert werden soll. |
|isEnabled |Boolesch, Standardwert „false“ |statischen| Aktiviert bzw. deaktiviert HttpApplicationGateway. HttpApplicationGateway ist standardmäßig deaktiviert, und diese Konfiguration muss zur Aktivierung festgelegt werden. |
|NumberOfParallelOperations | Uint, Standardwert 5000 |statischen|Anzahl der Lesevorgänge, die an die HTTP-Serverwarteschlange gesendet werden sollen. Dadurch wird die Anzahl gleichzeitiger Anforderungen gesteuert, die von HttpGateway erfüllt werden können. |
|RemoveServiceResponseHeaders|Zeichenfolge, Standardwert „Date; Server“|statischen|Durch Semikolons/Kommas getrennte Liste mit Antwortheadern, die aus der Dienstantwort entfernt werden, bevor die Weiterleitung an den Client erfolgt. Wenn dieser Wert auf eine leere Zeichenfolge festgelegt ist, werden alle vom Dienst zurückgegebenen Header unverändert übergeben. z.B.: Datum und die Server nicht überschreiben |
|ResolveServiceBackoffInterval |Zeit in Sekunden, Standardwert 5 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an.  Gibt das standardmäßige Backoffintervall an, nach dem ein fehlerhafter Vorgang zum Auflösen von Diensten wiederholt wird. |
|SecureOnlyMode|Boolesch, Standardwert FALSE|Dynamisch| SecureOnlyMode: true: Reverseproxy leitet nur an Dienste weiter, die sichere Endpunkte veröffentlichen. false: Reverseproxy kann Anforderungen an sichere/nicht sichere Endpunkte weiterleiten. Weitere Informationen finden Sie unter [Logik der Reverseproxy-Endpunktauswahl](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|string, Standardwert ""|Dynamisch|Die durch Kommas getrennte Liste der Fingerabdrücke der Remotezertifikate, denen der Reverseproxy vertrauen kann. Weitere Informationen finden Sie unter [Sichere Reverseproxyverbindung](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, Standardwert None|Dynamisch| Antragstellername und Ausstellerfingerabdruck der Remotezertifikate, denen der Reverseproxy vertrauen kann. Weitere Informationen finden Sie unter [Sichere Reverseproxyverbindung](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|MinReplicaSetSize|Ganze Zahl, Standardwert 0|statischen|MinReplicaSetSize für BackupRestoreService |
|PlacementConstraints|Zeichenfolge, Standardwert „“|statischen|    PlacementConstraints für den BackupRestore-Dienst |
|SecretEncryptionCertThumbprint|Zeichenfolge, Standardwert „“|Dynamisch|Fingerabdruck des X509-Verschlüsselungszertifikats für das Geheimnis |
|SecretEncryptionCertX509StoreName|Zeichenfolge, empfohlener Wert ist „My“ (kein Standardwert) |    Dynamisch|    Gibt das Zertifikat für das Ver- und Entschlüsseln von Anmeldeinformationen und den Namen des X.509-Zertifikatspeichers für das Ver- und Entschlüsseln von Speicheranmeldeinformationen für den Sicherungs- und Wiederherstellungsdienst an |
|TargetReplicaSetSize|Ganze Zahl, Standardwert ist „0“|statischen| TargetReplicaSetSize für BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | Boolesch, Standardwert „false“ |Dynamisch|Gibt an, ob benutzerdefinierte Richtlinien für die Upgradesortierung zulässig sind oder nicht. Hiermit wird das 2-Phasen-Upgrade zum Aktivieren dieses Features durchgeführt. Service Fabric 6.5 bietet Unterstützung für die Angabe einer Sortierungsrichtlinie für Upgradedomänen während Cluster- oder Anwendungsupgrades. Die unterstützten Richtlinien sind „Numeric“, „Lexicographical“, „ReverseNumeric“ und „ReverseLexicographical“. Der Standardwert ist „Numeric“. Um dieses Feature verwenden zu können, muss die Einstellung „ClusterManager/ AllowCustomUpgradeSortPolicies“ des Clustermanifests im zweiten Schritt des Konfigurationsupdates auf „True“ festgelegt werden, nachdem der SF 6.5-Code das Softwareupgrade abgeschlossen hat. Dieser Vorgang muss unbedingt in zwei Phasen durchgeführt werden, weil die Codeaktualisierung während des ersten Upgrades mit der Upgradereihenfolge verwechselt werden kann.|
|EnableDefaultServicesUpgrade | Boolesch, Standardwert „false“ |Dynamisch|Aktivieren Sie das Aktualisieren von Standarddiensten während des Anwendungsupgrades. Standarddienstbeschreibungen würden nach dem Upgrade überschrieben werden. |
|FabricUpgradeHealthCheckInterval |Zeit in Sekunden, Standardwert 60 |Dynamisch|Die Häufigkeit der Integritätsstatusprüfungen während eines überwachten Fabric-Upgrades |
|FabricUpgradeStatusPollInterval |Zeit in Sekunden, Standardwert 60 |Dynamisch|Die Häufigkeit des Abrufs des Upgradestatus der Fabric. Dieser Wert bestimmt die Aktualisierungsrate für Aufrufe von „GetFabricUpgradeProgress“ |
|ImageBuilderTimeoutBuffer |Zeit in Sekunden, Standardwert 3 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Die zulässige Zeitspanne für die Rückgabe von Image Builder-Timeoutfehlern an den Client. Wenn dieser Puffer zu klein ist, tritt auf dem Client ein Timeout ein, bevor dies auf dem Server der Fall ist, sodass sich ein generischer Timeoutfehler auf dem Client ergibt. |
|InfrastructureTaskHealthCheckRetryTimeout | Zeit in Sekunden, Standardwert 60 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Die Zeitspanne für Wiederholungen fehlerhafter Integritätsprüfungen während der Nachbearbeitung einer Infrastrukturaufgabe. Durch die Beobachtung einer erfolgreichen Integritätsprüfung wird dieser Timer zurückgesetzt. |
|InfrastructureTaskHealthCheckStableDuration | Zeit in Sekunden, Standardwert 0|Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Die Zeitspanne, die aufeinanderfolgende erfolgreiche Integritätsprüfungen beobachtet werden, bevor die Nachbearbeitung einer Infrastrukturaufgabe erfolgreich abgeschlossen ist. Durch die Beobachtung einer fehlerhaften Integritätsprüfung wird dieser Timer zurückgesetzt. |
|InfrastructureTaskHealthCheckWaitDuration |Zeit in Sekunden, Standardwert 0|Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Die Zeitspanne, die vor dem Starten von Integritätsprüfungen nach der Nachbearbeitung einer Infrastrukturaufgabe gewartet wird. |
|InfrastructureTaskProcessingInterval | Zeit in Sekunden, Standardwert 10 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Verarbeitungsintervall, das von der Infrastrukturaufgabe verwendet wird, die den Zustandsautomaten verarbeitet. |
|MaxCommunicationTimeout |Zeit in Sekunden, Standardwert 600 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das maximale Zeitlimit für die interne Kommunikation zwischen ClusterManager und anderen Systemdiensten (wie Naming Service, Failover-Manager usw.). Dieses Zeitlimit muss kleiner sein als der globale MaxOperationTimeout-Wert (da mehrere Kommunikationen zwischen Systemkomponenten für jeden Clientvorgang möglich sind). |
|MaxDataMigrationTimeout |Zeit in Sekunden, Standardwert 600 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das maximale Zeitlimit für Wiederherstellungsvorgänge nach der Datenmigration nach einer Fabric-Aktualisierung. |
|MaxOperationRetryDelay |Zeit in Sekunden, Standardwert 5|Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Die maximale Verzögerung für interne Wiederholungen, wenn Fehler gefunden werden. |
|MaxOperationTimeout |Zeit in Sekunden, Standardwert MaxValue |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Das maximale globale Zeitlimit für die interne Verarbeitung von Vorgängen für ClusterManager. |
|MaxTimeoutRetryBuffer | Zeit in Sekunden, Standardwert 600 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das maximale Vorgangszeitlimit für die interne Wiederholung aufgrund von Zeitlimits ist `<Original Time out> + <MaxTimeoutRetryBuffer>`. Ein zusätzliches Zeitlimit wird in Schritten von MinOperationTimeout hinzugefügt. |
|MinOperationTimeout | Zeit in Sekunden, Standardwert 60 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das minimale globale Zeitlimit für die interne Verarbeitung von Vorgängen für ClusterManager. |
|MinReplicaSetSize |Ganze Zahl, Standardwert 3 |Nicht zulässig|MinReplicaSetSize für ClusterManager. |
|PlacementConstraints | string, Standardwert "" |Nicht zulässig|PlacementConstraints für ClusterManager. |
|QuorumLossWaitDuration |Zeit in Sekunden, Standardwert MaxValue |Nicht zulässig| Geben Sie die Zeitspanne in Sekunden an. QuorumLossWaitDuration für ClusterManager. |
|ReplicaRestartWaitDuration |Zeit in Sekunden, Standardwert (60,0 \* 30)|Nicht zulässig|Geben Sie die Zeitspanne in Sekunden an. ReplicaRestartWaitDuration für ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Zeit in Sekunden, Standardwert 1200 |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Wenn ReplicaSetCheckTimeout auf den maximalen DWORD-Wert festgelegt ist, wird dies mit dem Wert dieser Konfiguration für Rollbacks überschrieben. Der Wert für Rollforwards wird nie überschrieben. |
|SkipRollbackUpdateDefaultService | Boolesch, Standardwert „false“ |Dynamisch|CM überspringt das Zurücksetzen der aktualisierten Standarddienste während des Rollbacks eines Anwendungsupgrades. |
|StandByReplicaKeepDuration | Zeit in Sekunden, Standardwert (3600,0 \* 2)|Nicht zulässig|Geben Sie die Zeitspanne in Sekunden an. StandByReplicaKeepDuration für ClusterManager. |
|TargetReplicaSetSize |Ganze Zahl, Standardwert 7 |Nicht zulässig|TargetReplicaSetSize für ClusterManager. |
|UpgradeHealthCheckInterval |Zeit in Sekunden, Standardwert 60 |Dynamisch|Die Häufigkeit der Integritätsstatusprüfungen während eines überwachten Anwendungsupgrades |
|UpgradeStatusPollInterval |Zeit in Sekunden, Standardwert 60 |Dynamisch|Die Häufigkeit des Abrufs des Upgradestatus der Anwendung. Dieser Wert bestimmt die Aktualisierungsrate für Aufrufe von „GetApplicationUpgradeProgress“ |
|CompleteClientRequest | Boolesch, Standardwert „false“ |Dynamisch| Schließt die Clientanforderung ab, wenn von CM akzeptiert. |

## <a name="common"></a>Allgemein

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Zeit in Sekunden, Standardwert 1 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Intervall für die Leistungsüberwachung. Mit 0 oder einem negativen Wert wird die Überwachung deaktiviert. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parameter** | **Zulässige Werte** |**Upgraderichtlinie**| **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, Standardwert None|Dynamisch|Gibt an, dass die Richtliniendefragmentierung beim Leeren von Knoten folgt. Für eine bestimmte Metrik gibt 0 an, dass SF versuchen soll, die Knoten gleichmäßig auf UDs und FDs zu defragmentieren. 1 gibt nur an, dass die Knoten defragmentiert werden müssen. |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parameter** | **Zulässige Werte** |**Upgraderichtlinie**| **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, Standardwert None|Dynamisch|Bestimmt die Sammlung von Metriken, die für die Defragmentierung und nicht für den Lastenausgleich verwendet werden soll. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parameter** | **Zulässige Werte** |**Upgraderichtlinie**| **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, Standardwert None|Dynamisch|Bestimmt die Anzahl der freien Knoten, die erforderlich sind, um den Cluster als defragmentiert zu betrachten. Die Angabe erfolgt als Prozent im Bereich [0,0 – 1,0] oder als Anzahl der leeren Knoten als Zahl >= 1,0. |

## <a name="diagnostics"></a>Diagnose

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Boolesch, Standardwert „true“ | Dynamisch | Bewirkt, dass HTTP-Anforderungen, die sich nicht auf den Zustand des Clusters auswirken, aus der Überwachung ausgeschlossen werden. Derzeit werden nur Anforderungen ausgeschlossen, die den Typ „GET“ haben. Dies kann sich aber ändern. |
|AppDiagnosticStoreAccessRequiresImpersonation |Boolesch, Standardwert „true“ | Dynamisch |Gibt an, ob ein Identitätswechsel erforderlich ist, wenn für die Anwendung auf Diagnosespeicher zugegriffen wird. |
|AppEtwTraceDeletionAgeInDays |Ganze Zahl, Standardwert 3 | Dynamisch |Anzahl der Tage, nach denen wir alte ETL-Dateien löschen, die ETW-Ablaufverfolgungen von Anwendungen enthalten. |
|ApplicationLogsFormatVersion |Ganze Zahl, Standardwert 0 | Dynamisch |Version für das Format der Anwendungsprotokolle. Unterstützte Werte sind 0 und 1. Version 1 umfasst mehr Felder aus dem ETW-Ereignisdatensatz als Version 0. |
|AuditHttpRequests |Boolesch, Standardwert „false“ | Dynamisch | Hiermit wird die HTTP-Überwachung aktiviert oder deaktiviert. Der Zweck der Überwachung besteht darin, die Aktivitäten anzeigen zu können, die für den Cluster ausgeführt wurden. Dazu gehört auch, wer die jeweilige Anforderung initiiert hat. Beachten Sie, dass dies eine Protokollierung mit bestmöglichem Versuch ist und dass Verlust der Ablaufverfolgung auftreten kann. HTTP-Anforderungen mit „Benutzer“-Authentifizierung werden nicht festgehalten. |
|CaptureHttpTelemetry|Boolesch, Standardwert „true“ | Dynamisch | Hiermit wird HTTP-Telemetrie aktiviert oder deaktiviert. Mit Telemetrie wird Service Fabric in die Lage versetzt, Telemetriedaten zu erfassen, um zukünftige Arbeit planen und Problembereiche ermitteln zu können. Für Telemetrie werden weder personenbezogene Daten noch der jeweilige Anforderungstext erfasst. Für Telemetriedaten werden alle HTTP-Anforderungen erfasst, sofern dies nicht anders konfiguriert ist. |
|ClusterId |String | Dynamisch |Die eindeutige ID des Clusters. Diese wird generiert, wenn der Cluster erstellt wird. |
|ConsumerInstances |String | Dynamisch |Die Liste der DCA-Consumerinstanzen. |
|DiskFullSafetySpaceInMB |Ganze Zahl, Standardwert 1024 | Dynamisch |Verbleibender Speicherplatz in MB, der vor der Verwendung durch DCA geschützt werden soll. |
|EnableCircularTraceSession |Boolesch, Standardwert „false“ | statischen |Das Flag gibt an, ob zirkuläre Ablaufverfolgungssitzungen verwendet werden sollen. |
|EnablePlatformEventsFileSink |Boolesch, Standardwert „false“ | statischen |Aktivieren/Deaktivieren von Plattformereignissen, die auf den Datenträger geschrieben werden |
|EnableTelemetry |Boolesch, Standardwert „true“ | Dynamisch |Damit werden Telemetriedaten aktiviert oder deaktiviert. |
|FailuresOnlyHttpTelemetry | Boolesch, Standardwert „false“ | Dynamisch | Wenn HTTP-Telemetrieerfassung aktiviert ist, werden nur fehlerhafte Anforderungen erfasst. Hiermit lässt sich die Anzahl der Ereignisse verringern, die für Telemetrie generiert werden. |
|HttpTelemetryCapturePercentage | Ganze Zahl, Standardwert 50 | Dynamisch | Wenn HTTP-Telemetrieerfassung aktiviert ist, wird nur ein zufälliger Prozentsatz der Anforderungen erfasst. Hiermit lässt sich die Anzahl der Ereignisse verringern, die für Telemetrie generiert werden. |
|MaxDiskQuotaInMB |Ganze Zahl, Standardwert 65536 | Dynamisch |Datenträgerkontingent in MB für Windows Fabric-Protokolldateien. |
|ProducerInstances |String | Dynamisch |Die Liste der DCA-Producerinstanzen. |

## <a name="dnsservice"></a>DnsService
| **Parameter** | **Zulässige Werte** |**Upgraderichtlinie**| **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|Boolesch, Standardwert FALSE|statischen|Das Flag zum Aktivieren der Unterstützung für DNS-Abfragen bei partitionierten Diensten. Dieses Feature ist standardmäßig deaktiviert. Weitere Informationen finden Sie unter [DNS-Dienst in Azure Service Fabric](service-fabric-dnsservice.md).|
|InstanceCount|Ganze Zahl, Standardwert -1|statischen|Der Standardwert ist -1. Das bedeutet, dass DnsService auf jedem Knoten ausgeführt wird. Für OneBox muss diese Option auf 1 festgelegt werden, da DnsService den bekannten Port 53 verwendet, damit nicht mehrere Instanzen auf dem gleichen Computer auftreten.|
|isEnabled|Boolesch, Standardwert FALSE|statischen|Aktiviert/deaktiviert DnsService. DnsService ist standardmäßig deaktiviert, und diese Konfiguration muss zur Aktivierung festgelegt werden. |
|PartitionPrefix|Zeichenfolge, Standardwert „--“|statischen|Steuert den Wert der Partitionspräfix-Zeichenfolge in DNS-Abfragen für partitionierte Dienste. Der Wert: <ul><li>Muss RFC-kompatibel sein, da er Teil einer DNS-Abfrage ist.</li><li>Darf keinen Punkt („.“) enthalten, weil Punkte das Verhalten von DNS-Suffixen beeinträchtigen.</li><li>Darf nicht länger als 5 Zeichen sein.</li><li>Darf keine leere Zeichenfolge sein.</li><li>Wenn die PartitionPrefix-Einstellung überschrieben wird, muss auch PartitionSuffix überschrieben werden – und umgekehrt.</li></ul>Weitere Informationen finden Sie unter [DNS-Dienst in Azure Service Fabric](service-fabric-dnsservice.md).|
|PartitionSuffix|string, Standardwert ""|statischen|Steuert den Wert der Partitionssuffix-Zeichenfolge in DNS-Abfragen für partitionierte Dienste. Der Wert: <ul><li>Muss RFC-kompatibel sein, da er Teil einer DNS-Abfrage ist.</li><li>Darf keinen Punkt („.“) enthalten, weil Punkte das Verhalten von DNS-Suffixen beeinträchtigen.</li><li>Darf nicht länger als 5 Zeichen sein.</li><li>Wenn die PartitionPrefix-Einstellung überschrieben wird, muss auch PartitionSuffix überschrieben werden – und umgekehrt.</li></ul>Weitere Informationen finden Sie unter [DNS-Dienst in Azure Service Fabric](service-fabric-dnsservice.md). |
|RetryTransientFabricErrors|Boolesch, Standardwert „true“|statischen|Die Einstellung steuert die Wiederholungsfunktionen beim Aufrufen von Service Fabric-APIs aus DnsService. Wenn diese Option aktiviert ist, werden bis zu drei Mal Wiederholungsversuche ausgeführt, wenn ein vorübergehender Fehler auftritt.|

## <a name="eventstoreservice"></a>EventStoreService

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|MinReplicaSetSize|Ganze Zahl, Standardwert 0|statischen|MinReplicaSetSize für den EventStore-Dienst |
|PlacementConstraints|Zeichenfolge, Standardwert „“|statischen|    PlacementConstraints für den EventStore-Dienst |
|TargetReplicaSetSize|Ganze Zahl, Standardwert ist „0“|statischen| TargetReplicaSetSize für den EventStore-Dienst |

## <a name="fabricclient"></a>FabricClient

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Zeit in Sekunden, Standardwert 2 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Timeoutintervall für Verbindungen für jeden Versuch eines Clients, eine Verbindung mit dem Gateway zu öffnen.|
|HealthOperationTimeout |Zeit in Sekunden, Standardwert 120 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Timeout für eine an den Integritätsdienst gesendete Berichtsnachricht. |
|HealthReportRetrySendInterval |Zeit in Sekunden, Standardwert 30, Mindestwert 1 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Intervall, in dem Berichtskomponenten kumulierte Integritätsberichte erneut an den Integritäts-Manager senden. |
|HealthReportSendInterval |Zeit in Sekunden, Standardwert 30 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Intervall, in dem Berichtskomponenten kumulierte Integritätsberichte an den Integritätsdienst senden. |
|KeepAliveIntervalInSeconds |Ganze Zahl, Standardwert 20 |statischen|Das Intervall, in dem der FabricClient-Transport Keep-Alive-Nachrichten an das Gateway sendet. Bei 0 ist „keepAlive“ deaktiviert. Der Wert muss eine positive Zahl sein. |
|MaxFileSenderThreads |Uint, Standardwert 10 |statischen|Die maximale Anzahl von Dateien, die gleichzeitig übertragen werden. |
|NodeAddresses |string, Standardwert "" |statischen|Eine Sammlung von Adressen (Verbindungszeichenfolgen) auf unterschiedlichen Knoten, die für die Kommunikation mit dem Naming Service verwendet werden können. Anfangs stellt der Client eine Verbindung her, indem er eine der Adressen nach dem Zufallsprinzip auswählt. Wenn mehr als eine Verbindungszeichenfolge angegeben wird und eine Verbindung aufgrund eines Kommunikations- oder Timeoutfehlers nicht hergestellt werden kann, wechselt der Client zur nächsten Adresse in der Reihe. Details zur Semantik von Wiederholungsversuchen finden Sie im Abschnitt zu Wiederholungsversuchen für Naming Service-Adressen. |
|PartitionLocationCacheLimit |Ganze Zahl, Standardwert 100000 |statischen|Anzahl der für die Dienstlösung zwischengespeicherten Partitionen (bei 0 gilt keine Begrenzung). |
|RetryBackoffInterval |Zeit in Sekunden, Standardwert 3 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Backoffintervall vor der Wiederholung des Vorgangs. |
|ServiceChangePollInterval |Zeit in Sekunden, Standardwert 120 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Intervall zwischen aufeinanderfolgenden Abrufen von Dienständerungen vom Client an das Gateway für Benachrichtigungsrückrufe zu registrierten Dienständerungen. |

## <a name="fabrichost"></a>FabricHost

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Ganze Zahl, Standardwert 10 |Dynamisch|Dies ist die maximale Anzahl von Versuchen, die Aktivierung nach einem Fehler zu wiederholen, bevor aufgegeben wird. |
|ActivationMaxRetryInterval |Zeit in Sekunden, Standardwert 300 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Maximales Wiederholungsintervall für die Aktivierung. Bei jedem andauernden Fehler wird das Wiederholungsintervall wie folgt berechnet: Min( ActivationMaxRetryInterval; Anzahl andauernder Fehler * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Zeit in Sekunden, Standardwert 5 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Backoffintervall bei jedem Aktivierungsfehler. Bei jedem andauernden Aktivierungsfehler wiederholt das System die Aktivierung bis zum Wert von MaxActivationFailureCount. Das Wiederholungsintervall bei jedem Versuch ist das Produkt der Anzahl fortlaufender Aktivierungsfehler und des Backoffintervalls für die Aktivierung. |
|EnableRestartManagement |Boolesch, Standardwert „false“ |Dynamisch|Hiermit wird der Neustart des Servers aktiviert. |
|EnableServiceFabricAutomaticUpdates |Boolesch, Standardwert „false“ |Dynamisch|Hiermit werden automatische Fabricupdates über Windows Update aktiviert. |
|EnableServiceFabricBaseUpgrade |Boolesch, Standardwert „false“ |Dynamisch|Hiermit wird das Basisupdate für den Server aktiviert. |
|FailureReportingExpeditedReportingIntervalEnabled | Boolesch, Standardwert „true“ | statischen | Ermöglicht das schnellere Hochladen von Raten in DCA, wenn sich FabricHost im Fehlerberichterstattungsmodus befindet. |
|FailureReportingTimeout | TimeSpan, Standardwert Common::TimeSpan::FromSeconds(60) | statischen |Geben Sie die Zeitspanne in Sekunden an. Timeout für die DCA-Fehlerberichterstattung, wenn für FabricHost in einer frühen Phase ein Startfehler auftritt. | 
|RunDCAOnStartupFailure | Boolesch, Standardwert „true“ | statischen |Bestimmt, ob DCA gestartet werden soll, um Protokolle bei Problemen mit Startproblemen in FabricHost hochzuladen. | 
|StartTimeout |Zeit in Sekunden, Standardwert 300 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Timeout für den Start von fabricactivationmanager. |
|StopTimeout |Zeit in Sekunden, Standardwert 300 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Timeout für die Aktivierung, Deaktivierung und Aktualisierung des gehosteten Diensts. |

## <a name="fabricnode"></a>FabricNode

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |string, Standardwert „FindByThumbprint“ |Dynamisch|Gibt an, wie nach dem Zertifikat im durch ClientAuthX509StoreName angegebenen Speicher gesucht werden soll. Unterstützte Werte: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |string, Standardwert "" | Dynamisch|Suchfilterwert, der zum Suchen des Zertifikats für die standardmäßige FabricClient-Administratorrolle verwendet wird. |
|ClientAuthX509FindValueSecondary |string, Standardwert "" |Dynamisch|Suchfilterwert, der zum Suchen des Zertifikats für die standardmäßige FabricClient-Administratorrolle verwendet wird. |
|ClientAuthX509StoreName |string, Standardwert „My“ |Dynamisch|Der Name des X.509-Zertifikatspeichers, der das Zertifikat für die standardmäßige FabricClient-Administratorrolle enthält. |
|ClusterX509FindType |string, Standardwert „FindByThumbprint“ |Dynamisch|Gibt an, wie nach dem Clusterzertifikat im durch ClusterX509StoreName angegebenen Speicher gesucht werden soll. Unterstützte Werte: „FindByThumbprint“; „FindBySubjectName“ mit „FindBySubjectName“; wenn es mehrere Übereinstimmungen gibt, wird der Datensatz mit dem längsten Ablaufdatum verwendet. |
|ClusterX509FindValue |string, Standardwert "" |Dynamisch|Suchfilterwert, der zum Suchen des Clusterzertifikats verwendet wird. |
|ClusterX509FindValueSecondary |string, Standardwert "" |Dynamisch|Suchfilterwert, der zum Suchen des Clusterzertifikats verwendet wird. |
|ClusterX509StoreName |string, Standardwert „My“ |Dynamisch|Der Name des X.509-Zertifikatspeichers, der das Clusterzertifikat zum Sichern der Kommunikation innerhalb des Clusters enthält. |
|EndApplicationPortRange |Ganze Zahl, Standardwert 0 |statischen|Ende (nicht inklusiv) der Anwendungsports, die vom Hostingsubsystem verwaltet werden. Erforderlich, wenn EndpointFilteringEnabled beim Hosting „true“ ist. |
|ServerAuthX509FindType |string, Standardwert „FindByThumbprint“ |Dynamisch|Gibt an, wie nach dem Serverzertifikat im durch ServerAuthX509StoreName angegebenen Speicher gesucht werden soll. Unterstützte Werte: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |string, Standardwert "" |Dynamisch|Suchfilterwert, der zum Suchen des Serverzertifikats verwendet wird. |
|ServerAuthX509FindValueSecondary |string, Standardwert "" |Dynamisch|Suchfilterwert, der zum Suchen des Serverzertifikats verwendet wird. |
|ServerAuthX509StoreName |string, Standardwert „My“ |Dynamisch|Name des X.509-Zertifikatspeichers, der das Serverzertifikat für den Zugangsdienst enthält. |
|StartApplicationPortRange |Ganze Zahl, Standardwert 0 |statischen|Start der Anwendungsports, die vom Hostingsubsystem verwaltet werden. Erforderlich, wenn EndpointFilteringEnabled beim Hosting „true“ ist. |
|StateTraceInterval |Zeit in Sekunden, Standardwert 300 |statischen|Geben Sie die Zeitspanne in Sekunden an. Das Intervall für die Ablaufverfolgung des Knotenstatus auf allen Knoten und aktiven Knoten in FM/FMM. |
|UserRoleClientX509FindType |string, Standardwert „FindByThumbprint“ |Dynamisch|Gibt an, wie nach dem Zertifikat im durch UserRoleClientX509StoreName angegebenen Speicher gesucht werden soll. Unterstützte Werte: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |string, Standardwert "" |Dynamisch|Suchfilterwert, der zum Suchen des Zertifikats für die standardmäßige FabricClient-Benutzerrolle verwendet wird. |
|UserRoleClientX509FindValueSecondary |string, Standardwert "" |Dynamisch|Suchfilterwert, der zum Suchen des Zertifikats für die standardmäßige FabricClient-Benutzerrolle verwendet wird. |
|UserRoleClientX509StoreName |string, Standardwert „My“ |Dynamisch|Der Name des X.509-Zertifikatspeichers, der das Zertifikat für die standardmäßige FabricClient-Benutzerrolle enthält. |

## <a name="failovermanager"></a>FailoverManager

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Boolesch, Standardwert ist „false“ |Dynamisch|Flag zur Angabe, ob der Knotenzustand bei einem Startknoten entfernt werden darf |
|BuildReplicaTimeLimit|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(3600)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Zeitlimit für das Erstellen eines zustandsbehafteten Replikats, nach dem ein Warnungsintegritätsbericht initiiert wird. |
|ClusterPauseThreshold|Ganze Zahl, Standardwert 1|Dynamisch|Wenn die Anzahl der Knoten im System diesen Wert unterschreitet, werden Platzierung, Lastenausgleich und Failover beendet. |
|CreateInstanceTimeLimit|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(300)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Zeitlimit für das Erstellen einer zustandslosen Instanz, nach dem ein Warnungsintegritätsbericht initiiert wird. |
|ExpectedClusterSize|Ganze Zahl, Standardwert 1|Dynamisch|Beim ersten Start des Clusters wartet der FM darauf, dass sich diese Anzahl von Knoten meldet, bevor andere Dienste (einschließlich der Systemdienste wie Naming Service) platziert werden. Wenn Sie diesen Wert erhöhen, verlängert sich die Startzeit eines Clusters. Es wird aber verhindert, dass die frühen Knoten überlastet werden. Außerdem werden zusätzliche Verschiebungen verhindert, die erforderlich sind, wenn ein größere Anzahl von Knoten online geht. Dieser Wert sollte in der Regel auf einen kleinen Bruchteil der anfänglichen Clustergröße festgelegt werden. |
|ExpectedNodeDeactivationDuration|TimeSpan, Standardwert „Common::TimeSpan::FromSeconds(60,0 \* 30)“|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Dies ist die erwartete Zeitspanne, in der der Abschluss der Deaktivierung eines Knotens erwartet wird. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, Standardwert „Common::TimeSpan::FromSeconds(60,0 \* 30)“|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Dies ist die erwartete Dauer für die Aktualisierung eines Knotens während des Windows Fabric-Upgrades. |
|ExpectedReplicaUpgradeDuration|TimeSpan, Standardwert „Common::TimeSpan::FromSeconds(60,0 \* 30)“|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Dies ist die erwartete Dauer für die Aktualisierung aller Replikate auf einem Knoten während des Anwendungsupgrades. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|Boolesch, Standardwert TRUE|Dynamisch|Wenn auf TRUE festgelegt, dürfen Replikate mit einer Zielgröße der Replikatgruppe von 1 während des Upgrades verschoben werden. |
|MinReplicaSetSize|Ganze Zahl, Standardwert 3|Nicht zulässig|Die Mindestgröße der Replikatgruppe für den FM. Wenn die Anzahl der aktiven FM-Replikate unter diesen Wert sinkt, weist der FM Änderungen am Cluster zurück, bis mindestens die minimale Anzahl der Replikate wiederhergestellt wurde. |
|PlacementConstraints|string, Standardwert ""|Nicht zulässig|Platzierungsbeschränkungen für die Replikate des Failover-Managers. |
|PlacementTimeLimit|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(600)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Zeitlimit für das Erreichen der Zielanzahl von Replikaten, nach dem ein Warnungsintegritätsbericht initiiert wird. |
|QuorumLossWaitDuration |Zeit in Sekunden, Standardwert MaxValue |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Dies ist die maximale Dauer, für die eine Partition in einem Zustand des Quorumverlusts sein darf. Wenn für die Partition nach Ablauf dieses Zeitraums noch ein Quorumsverlust vorliegt, wird die Partition aus dem Quorumsverlusts wiederhergestellt, indem die ausgefallenen Replikate als verloren angesehen werden. Beachten Sie, dass dies möglicherweise Datenverluste verursachen kann. |
|ReconfigurationTimeLimit|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(300)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Zeitlimit für die Neukonfiguration, nach dem ein Warnungsintegritätsbericht initiiert wird. |
|ReplicaRestartWaitDuration|TimeSpan, Standardwert „Common::TimeSpan::FromSeconds(60,0 \* 30)“|Nicht zulässig|Geben Sie die Zeitspanne in Sekunden an. Die ReplicaRestartWaitDuration für FMService. |
| SeedNodeQuorumAdditionalBufferNodes | Ganze Zahl, Standardwert 0 | Dynamisch | Der Puffer der Startknoten, der für die Ausführung erforderlich ist (zusammen mit dem Quorum der Startknoten). FM sollte ein Maximum von (totalNumSeedNodes - (seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) Startknoten ermöglichen, die ausfallen dürfen. |
|StandByReplicaKeepDuration|TimeSpan, Standardwert „Common::TimeSpan::FromSeconds(3600,0 \* 24 \* 7)“|Nicht zulässig|Geben Sie die Zeitspanne in Sekunden an. Dies ist die StandByReplicaKeepDuration für den FMService. |
|TargetReplicaSetSize|Ganze Zahl, Standardwert 7|Nicht zulässig|Dies ist die Zielanzahl der FM-Replikate, die Windows Fabric verwaltet. Eine höhere Anzahl führt zu einer höheren Zuverlässigkeit der FM-Daten bei einer geringen Leistungsverschlechterung. |
|UserMaxStandByReplicaCount |Ganze Zahl, Standardwert 1 |Dynamisch|Die standardmäßige maximale Anzahl von Reservereplikaten, die das System für Benutzerdienste speichert. |
|UserReplicaRestartWaitDuration |Zeit in Sekunden, Standardwert 60,0 \* 30 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Wenn ein dauerhaftes Replikat ausfällt, wartet Windows Fabric für diese Zeitspanne darauf, dass das Replikat wieder aktiviert wird, bevor neue Ersatzreplikate (die eine Kopie des Zustands erfordern würden) erstellt werden. |
|UserStandByReplicaKeepDuration |Zeit in Sekunden, Standardwert 3600,0 \* 24 \* 7 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Wenn ein dauerhaftes Replikat nach einem Ausfall wieder aktiv ist, wurde es möglicherweise bereits ersetzt. Dieser Timer bestimmt, wie lange FM das Reservereplikat beibehält, bevor es verworfen wird. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Ganze Zahl, Standardwert 604800 |statischen| Ungefähr für diese Dauer sollten Aktionen gespeichert werden, die in einem Endzustand sind. Dies hängt auch von StoredActionCleanupIntervalInSeconds ab, da die Bereinigung nur in diesem Intervall erfolgt. 604800 sind 7 Tage. |
|DataLossCheckPollIntervalInSeconds|Ganze Zahl, Standardwert 5|statischen|Dies ist der Zeitraum zwischen den Überprüfungen, die das System während des Wartens auf Datenverluste ausführt. Die Häufigkeit, mit der die Anzahl der Datenverluste pro interner Iteration überprüft wird, ist DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds|Ganze Zahl, Standardwert 25|statischen|Die Gesamtzeit in Sekunden, die das System auf Datenverluste wartet. Dies wird intern verwendet, wenn die StartPartitionDataLossAsync()-API aufgerufen wird. |
|MinReplicaSetSize |Ganze Zahl, Standardwert 0 |statischen|MinReplicaSetSize für FaultAnalysisService. |
|PlacementConstraints | string, Standardwert ""|statischen| PlacementConstraints für FaultAnalysisService. |
|QuorumLossWaitDuration | Zeit in Sekunden, Standardwert MaxValue |statischen|Geben Sie die Zeitspanne in Sekunden an. QuorumLossWaitDuration für FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|Ganze Zahl, Standardwert 600|statischen|Dieser Parameter wird verwendet, wenn die Datenverlust-API aufgerufen wird. Er steuert, wie lange das System wartet, bis ein Replikat gelöscht wird, nachdem intern „remove replica“ für das Replikat aufgerufen wurde. |
|ReplicaRestartWaitDuration |Zeit in Sekunden, Standardwert 60 Minuten|statischen|Geben Sie die Zeitspanne in Sekunden an. ReplicaRestartWaitDuration für FaultAnalysisService. |
|StandByReplicaKeepDuration| Zeit in Sekunden, Standardwert (60 *24* 7) Minuten |statischen|Geben Sie die Zeitspanne in Sekunden an. StandByReplicaKeepDuration für FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Ganze Zahl, Standardwert 3600 |statischen|Gibt an, wie häufig der Speicher bereinigt wird. Nur Aktionen in einem Endzustand und die mindestens vor CompletedActionKeepDurationInSeconds abgeschlossen wurden, werden entfernt. |
|StoredChaosEventCleanupIntervalInSeconds | Ganze Zahl, Standardwert 3600 |statischen|Mit dieser Häufigkeit wird der Speicher auf Bereinigungen überwacht. Wenn die Anzahl der Ereignisse größer als 30000 ist, wird die Bereinigung gestartet. |
|TargetReplicaSetSize |Ganze Zahl, Standardwert 0 |statischen|NOT_PLATFORM_UNIX_START, TargetReplicaSetSize für FaultAnalysisService. |

## <a name="federation"></a>Verbund

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|LeaseDuration |Zeit in Sekunden, Standardwert 30 |Dynamisch|Dauer einer Lease zwischen einem Knoten und seinen Nachbarn. |
|LeaseDurationAcrossFaultDomain |Zeit in Sekunden, Standardwert 30 |Dynamisch|Dauer einer fehlerdomänenübergreifenden Lease zwischen einem Knoten und seinen Nachbarn. |

## <a name="filestoreservice"></a>FileStoreService

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Boolesch, Standardwert TRUE|Dynamisch|Konfiguration, ob der Dateispeicherdienst blockbasierten Dateiupload beim Kopieren des Anwendungspakets akzeptiert. |
|AnonymousAccessEnabled | Boolesch, Standardwert „true“ |statischen|Aktivieren/deaktivieren Sie anonymen Zugriff auf die FileStoreService-Freigaben. |
|CommonName1Ntlmx509CommonName|string, Standardwert ""|statischen| Der allgemeine Name des X509-Zertifikats, der verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für CommonName1NtlmPasswordSecret zu generieren. |
|CommonName1Ntlmx509StoreLocation|string, Standardwert „LocalMachine“|statischen|Der Speicherort des X509-Zertifikats, das verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für CommonName1NtlmPasswordSecret zu generieren. |
|CommonName1Ntlmx509StoreName|string, Standardwert „MY“| statischen|Der Speichername des X509-Zertifikats, das verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für CommonName1NtlmPasswordSecret zu generieren. |
|CommonName2Ntlmx509CommonName|string, Standardwert ""|statischen|Der allgemeine Name des X509-Zertifikats, das verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für CommonName2NtlmPasswordSecret zu generieren. |
|CommonName2Ntlmx509StoreLocation|string, Standardwert „LocalMachine“| statischen|Der Speicherort des X509-Zertifikats, das verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für CommonName2NtlmPasswordSecret zu generieren. |
|CommonName2Ntlmx509StoreName|string, Standardwert „MY“|statischen| Der Speichername des X509-Zertifikats, das verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für CommonName2NtlmPasswordSecret zu generieren. |
|CommonNameNtlmPasswordSecret|SecureString, Standardwert „Common::SecureString("")“| statischen|Der geheime Schlüssel des Kennworts, der als Startwert verwendet wird, um bei Verwendung der NTLM-Authentifizierung das gleiche Kennwort zu generieren. |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, Standardwert Common::TimeSpan::FromMinutes(5)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Zeitintervall zwischen dem Überprüfen des Speicherplatzes für das Berichterstattungs-Integritätsereignis, wenn der Speicherplatz auf dem Datenträger nahezu erschöpft ist. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, Standardwert Common::TimeSpan::FromMinutes(15)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Zeitintervall zwischen dem Überprüfen des Speicherplatzes für das Berichterstattungs-Integritätsereignis, wenn auf dem Datenträger ausreichend Speicherplatz vorhanden ist. |
|EnableImageStoreHealthReporting |Boolesch, Standardwert TRUE    |statischen|Konfiguration zur Bestimmung, ob der Dateispeicherdienst seine Integrität melden sollte. |
|FreeDiskSpaceNotificationSizeInKB|int64, Standardwert 25 \* 1024 |Dynamisch|Die Größe des freien Speicherplatzes, unterhalb dessen es zu einer Integritätswarnung kommen kann. Der minimale Wert dieser Konfiguration und die Konfiguration „FreeDiskSpaceNotificationThresholdPercentage" werden verwendet, um das Senden der Integritätswarnung zu ermitteln. |
|FreeDiskSpaceNotificationThresholdPercentage|double, Standardwert 0,02 |Dynamisch|Der prozentuale Anteil des freien Speicherplatzes, unterhalb dessen es zu einer Integritätswarnung kommen kann. Der minimale Wert dieser Konfiguration und die Konfiguration „FreeDiskSpaceNotificationInMB" werden verwendet, um das Senden der Integritätswarnung zu ermitteln. |
|GenerateV1CommonNameAccount| Boolesch, Standardwert TRUE|statischen|Gibt an, ob ein Konto mit dem V1-Generierungsalgorithmus für Benutzernamen erstellt werden soll. Ab Service Fabric-Version 6.1 wird immer ein Konto mit v2-Generierung erstellt. Das V1-Konto ist erforderlich für Upgrades von/auf Versionen, die die V2-Generierung (vor 6.1) nicht unterstützen.|
|MaxCopyOperationThreads | Uint, Standardwert 0 |Dynamisch| Die maximale Anzahl von parallelen Dateien, die der sekundäre Replikator vom primären Server kopieren kann. 0 = Anzahl von Kernen. |
|MaxFileOperationThreads | Uint, Standardwert 100 |statischen| Die maximale Anzahl von parallelen Threads, die FileOperations (Kopieren/Verschieben) auf dem primären Replikator durchführen dürfen. 0 = Anzahl von Kernen. |
|MaxRequestProcessingThreads | Uint, Standardwert 200 |statischen|Die maximale Anzahl von parallelen Threads, die zum Verarbeiten von Anforderungen auf dem primären Replikator zulässig sind. 0 = Anzahl von Kernen. |
|MaxSecondaryFileCopyFailureThreshold | Uint, Standardwert 25|Dynamisch|Die maximale Anzahl von Wiederholungsversuchen beim Kopieren von Dateien auf den sekundären Replikator, bevor aufgegeben wird. |
|MaxStoreOperations | Uint, Standardwert 4096 |statischen|Die maximale Anzahl von parallelen Speichertransaktionen, die auf dem primären Replikator zulässig sind. 0 = Anzahl von Kernen. |
|NamingOperationTimeout |Zeit in Sekunden, Standardwert 60 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Timeout für die Durchführung des Benennungsvorgangs. |
|PrimaryAccountNTLMPasswordSecret | SecureString, Standardwert ist leer |statischen| Der geheime Schlüssel des Kennworts, der als Startwert verwendet wird, um bei Verwendung der NTLM-Authentifizierung das gleiche Kennwort zu generieren. |
|PrimaryAccountNTLMX509StoreLocation | string, Standardwert „LocalMachine“|statischen| Der Speicherort des X509-Zertifikats, das verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für PrimaryAccountNTLMPasswordSecret zu generieren. |
|PrimaryAccountNTLMX509StoreName | string, Standardwert „MY“|statischen| Der Speichername des X509-Zertifikats, das verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für PrimaryAccountNTLMPasswordSecret zu generieren. |
|PrimaryAccountNTLMX509Thumbprint | string, Standardwert ""|statischen|Der Fingerabdruck des X509-Zertifikats, das verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für PrimaryAccountNTLMPasswordSecret zu generieren. |
|PrimaryAccountType | string, Standardwert "" |statischen|Der primäre AccountType des Prinzipals für die ACL von FileStoreService-Freigaben. |
|PrimaryAccountUserName | string, Standardwert "" |statischen|Der primäre Kontobenutzername des Prinzipals für die ACL von FileStoreService-Freigaben. |
|PrimaryAccountUserPassword | SecureString, Standardwert ist leer |statischen|Das primäre Kontokennwort des Prinzipals für die ACL von FileStoreService-Freigaben. |
|QueryOperationTimeout | Zeit in Sekunden, Standardwert 60 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Timeout für die Durchführung des Abfragevorgangs. |
|SecondaryAccountNTLMPasswordSecret | SecureString, Standardwert ist leer |statischen| Der geheime Schlüssel des Kennworts, der als Startwert verwendet wird, um bei Verwendung der NTLM-Authentifizierung das gleiche Kennwort zu generieren. |
|SecondaryAccountNTLMX509StoreLocation | string, Standardwert „LocalMachine“ |statischen|Der Speicherort des X509-Zertifikats, das verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für SecondaryAccountNTLMPasswordSecret zu generieren. |
|SecondaryAccountNTLMX509StoreName | string, Standardwert „MY“ |statischen|Der Speichername des X509-Zertifikats, das verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für SecondaryAccountNTLMPasswordSecret zu generieren. |
|SecondaryAccountNTLMX509Thumbprint | string, Standardwert ""| statischen|Der Fingerabdruck des X509-Zertifikats, das verwendet wird, um bei Verwendung der NTLM-Authentifizierung HMAC für SecondaryAccountNTLMPasswordSecret zu generieren. |
|SecondaryAccountType | string, Standardwert ""|statischen| Der sekundäre AccountType des Prinzipals für die ACL von FileStoreService-Freigaben. |
|SecondaryAccountUserName | string, Standardwert ""| statischen|Der sekundäre Kontobenutzername des Prinzipals für die ACL von FileStoreService-Freigaben. |
|SecondaryAccountUserPassword | SecureString, Standardwert ist leer |statischen|Das sekundäre Kontokennwort des Prinzipals für die ACL von FileStoreService-Freigaben. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, Standardwert ist 500|Dynamisch|Die Wiederholungsverzögerung beim Kopieren von Dateien (in Millisekunden).|
|UseChunkContentInTransportMessage|Boolesch, Standardwert TRUE|Dynamisch|Das Flag für die Verwendung der neuen Version des in v6.4 eingeführten Uploadprotokolls. Diese Protokollversion verwendet Service Fabric-Transportfunktionen zum Hochladen von Dateien in den Imagespeicher, wodurch eine bessere Leistung erzielt wird als mit dem in vorherigen Versionen verwendeten SMB-Protokoll. |

## <a name="healthmanager"></a>HealthManager

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Boolesch, Standardwert „false“ |statischen|Evaluierungsrichtlinie für die Clusterintegrität, wird für die Integritätsevaluierung pro Anwendungstyp aktiviert. |
|MaxSuggestedNumberOfEntityHealthReports|Ganze Zahl, Standardwert ist 100 |Dynamisch|Die maximale Anzahl von Integritätsberichten, die es für eine Entität geben kann, bevor Bedenken in Bezug auf die Integritätsberichtslogik des Watchdogs wirksam werden. Für jede Integritätsentität wird vorausgesetzt, dass sie eine relativ kleine Anzahl von Integritätsberichten hat. Wenn die Anzahl der Berichte diese Anzahl überschreitet gibt es möglicherweise Probleme mit der Watchdogimplementierung. Eine Entität mit zu vielen Berichten wird durch einen Warnungsintegritätsbericht gekennzeichnet, wenn die Entität ausgewertet wird. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Boolesch, Standardwert „false“ |statischen|Evaluierungsrichtlinie für die Clusterintegrität: Warnungen werden als Fehler behandelt. |
|MaxPercentUnhealthyApplications | Ganze Zahl, Standardwert 0 |statischen|Evaluierungsrichtlinie für die Clusterintegrität: Maximaler Prozentsatz fehlerhafter Anwendungen, die für einen als fehlerfrei geltenden Cluster zulässig sind. |
|MaxPercentUnhealthyNodes | Ganze Zahl, Standardwert 0 |statischen|Evaluierungsrichtlinie für die Clusterintegrität: Maximaler Prozentsatz fehlerhafter Knoten, die für einen als fehlerfrei geltenden Cluster zulässig sind. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|Ganze Zahl, Standardwert 10|statischen|Evaluierungsrichtlinie für die Clusterupgradeintegrität: Maximaler Prozentsatz des Deltas nicht integrer Knoten, die für einen als integer geltenden Cluster zulässig sind. |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|Ganze Zahl, Standardwert 15|statischen|Evaluierungsrichtlinie für die Clusterupgradeintegrität: Maximaler Prozentsatz des Deltas nicht integrer Knoten in einer Upgradedomäne, die für einen als integer geltenden Cluster zulässig sind. |

## <a name="hosting"></a>Hosting

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Ganze Zahl, Standardeinstellung 10 |Dynamisch|Die maximale Anzahl der Wiederholungsversuche nach misslungener Aktivierung, ehe die Versuche eingestellt werden |
|ActivationMaxRetryInterval |Zeit in Sekunden, Standardwert 300 |Dynamisch|Bei jedem fortlaufenden Aktivierungsfehler wiederholt das System die Aktivierung bis zum Wert von „ActivationMaxFailureCount“. „ActivationMaxRetryInterval“ gibt die Wartezeit nach jedem Aktivierungsfehler vor der Wiederholung an |
|ActivationRetryBackoffInterval |Zeit in Sekunden, Standardwert 5 |Dynamisch|Backoffintervall bei jedem Aktivierungsfehler. Bei jedem fortlaufenden Aktivierungsfehler wiederholt das System die Aktivierung bis zum Wert von „MaxActivationFailureCount“. Das Wiederholungsintervall bei jedem Versuch ist das Produkt der Anzahl fortlaufender Aktivierungsfehler und des Backoffintervalls für die Aktivierung. |
|ActivationTimeout| TimeSpan, Standardwert Common::TimeSpan::FromSeconds(180)|Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Das Timeout für die Anwendungsaktivierung, die -deaktivierung und das -upgrade. |
|ApplicationHostCloseTimeout| TimeSpan, Standardwert Common::TimeSpan::FromSeconds(120)|Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Wenn die Beendigung von Fabric in einem selbstaktivierten Prozess erkannt wird, schließt FabricRuntime alle Replikate im Hostprozess des Benutzers (applicationhost). Dies ist das Timeout für den Schließvorgang. |
| CnsNetworkPluginCnmUrlPort | wstring, Standardwert ist L"48080" | statischen | Azure cnm api url port |
| CnsNetworkPluginCnsUrlPort | wstring, Standardwert ist L"10090" | statischen | Azure cns url port |
|ContainerServiceArguments|Zeichenfolge, Standardwert „-H localhost:2375 -H npipe://“|statischen|Service Fabric (SF) verwaltet den Docker-Daemon (außer auf Windows-Clientcomputern wie Win10). Diese Konfiguration ermöglicht Benutzern, benutzerdefinierte Argumente anzugeben, die beim Start an den Docker-Daemon übergeben werden sollen. Wenn Sie benutzerdefinierte Argumente angegeben, übergibt Service Fabric ausschließlich das Argument „--pidfile“ an die Docker-Engine. Benutzer sollten daher das Argument „--pidfile“ nicht in ihren benutzerdefinierten Argumenten angeben. Außerdem sollte mit den benutzerdefinierten Argumenten sichergestellt werden, dass der Docker-Daemon unter Windows an der Standard-Named Pipe (bzw. unter Linux am Unix-Domänensocket) lauscht, damit Service Fabric mit ihm kommunizieren kann.|
|ContainerServiceLogFileMaxSizeInKb|Ganze Zahl, Standardwert 32768|statischen|Maximale Größe der Protokolldatei, die von Docker-Containern generiert wird.  Nur Windows|
|ContainerImageDownloadTimeout|int, Anzahl von Sekunden, Standard ist 1200 (20 Minuten)|Dynamisch|Anzahl von Sekunden, bevor beim Herunterladen von Images ein Timeout auftritt.|
|ContainerImagesToSkip|Zeichenfolge, Imagenamen getrennt durch vertikale Linie, Standardwert: „“|statischen|Der Name von mindestens einem Containerimage, das nicht gelöscht werden soll.  Wird mit dem PruneContainerImages-Parameter verwendet.|
|ContainerServiceLogFileNamePrefix|Zeichenfolge, Standardwert „sfcontainerlogs“|statischen|Dateinamenpräfix für Protokolldateien, die von Docker-Containern generiert werden.  Nur Windows|
|ContainerServiceLogFileRetentionCount|Ganze Zahl, Standardwert 10|statischen|Anzahl der Protokolldateien, die von Docker-Container generiert werden, bevor Protokolldateien überschrieben werden.  Nur Windows|
|CreateFabricRuntimeTimeout|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(120)|Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Der Timeoutwert für den FabricCreateRuntime-Synchronisierungsaufruf. |
|DefaultContainerRepositoryAccountName|string, Standardwert ""|statischen|Verwendung der Standardanmeldeinformationen anstelle der in ApplicationManifest.xml angegebenen Anmeldeinformationen. |
|DefaultContainerRepositoryPassword|string, Standardwert ""|statischen|Verwendung der standardmäßigen Kennwortanmeldeinformationen anstelle der in ApplicationManifest.xml angegebenen Anmeldeinformationen.|
|DefaultContainerRepositoryPasswordType|string, Standardwert ""|statischen|Wenn die Zeichenfolge nicht leer ist, kann der Wert „Encrypted“ oder „SecretsStoreRef“ lauten.|
|DefaultDnsSearchSuffixEmpty|Boolesch, Standardwert FALSE|statischen|Der Dienstname wird standardmäßig an den SF-DNS-Namen für Containerdienste angehängt. Dieses Verhalten wird durch dieses Feature deaktiviert, sodass im Auflösungspfad nichts an den SF-DNS-Namen angehängt wird.|
|DeploymentMaxFailureCount|Ganze Zahl, Standardwert 20| Dynamisch|Die Anwendungsbereitstellung wird DeploymentMaxFailureCount Mal wiederholt, bevor ein Fehler für die Bereitstellung der betreffenden Anwendung auf dem Knoten auftritt.| 
|DeploymentMaxRetryInterval| TimeSpan, Standardwert Common::TimeSpan::FromSeconds(3600)|Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Das maximale Wiederholungsintervall für die Bereitstellung. Bei jedem andauernden Fehler wird das Wiederholungsintervall wie folgt berechnet: Min( DeploymentMaxRetryInterval; Anzahl andauernder Fehler * DeploymentRetryBackoffInterval). |
|DeploymentRetryBackoffInterval| TimeSpan, Standardwert Common::TimeSpan::FromSeconds(10)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Backoffintervall für den Fehler bei der Bereitstellung. Bei jedem Continuous Deployment-Fehler wiederholt das System die Bereitstellung bis zu MaxDeploymentFailureCount Mal. Das Wiederholungsintervall ist das Produkt aus dem Continuous Deployment-Fehler und dem Backoffintervall der Bereitstellung. |
|DisableContainers|Boolesch, Standardwert FALSE|statischen|Konfiguration für das Deaktivieren von Containern – wird anstelle von „DisableContainerServiceStartOnContainerActivatorOpen“ verwendet, der veralteten Konfigurationsoption. |
|DisableDockerRequestRetry|Boolesch, Standardwert FALSE |Dynamisch| Standardmäßig kommuniziert SF mit dem DD (Docker-Daemon) mit dem Timeout „DockerRequestTimeout“ für jede an ihn gesendete HTTP-Anforderung. Reagiert der DD nicht innerhalb dieses Zeitraums, sendet SF die Anforderung erneut, sofern für den übergeordneten Vorgang noch Zeit bleibt.  Bei Hyper-V-Containern benötigt der DD manchmal erheblich mehr Zeit zum Aufrufen oder Deaktivieren des Containers. In diesem Fall fordert der DD aus Sicht von SF ein Timeout an, und SF wiederholt den Vorgang. Manchmal scheint dies den Druck auf den DD zu erhöhen. Diese Konfiguration ermöglicht es, die Wiederholung zu deaktivieren und auf eine Reaktion des DD zu warten. |
|DnsServerListTwoIps | Boolesch, Standardwert ist „false“ | statischen | Durch diese Flags wird der lokale DNS-Server zwei Mal hinzugefügt, um zeitweilig auftretende Probleme zu beheben. |
| DoNotInjectLocalDnsServer | Boolesch, Standardwert FALSE | statischen | Verhindert, dass die Runtime die lokale IP-Adresse als DNS-Server für Container einbindet. |
|EnableActivateNoWindow| Boolesch, Standardwert FALSE|Dynamisch| Der aktivierte Prozess wird im Hintergrund ohne Konsole erstellt. |
|EnableContainerServiceDebugMode|Boolesch, Standardwert TRUE|statischen|Aktivieren/Deaktivieren der Protokollierung für Docker-Container.  Nur Windows|
|EnableDockerHealthCheckIntegration|Boolesch, Standardwert TRUE|statischen|Ermöglicht die Integration von HEALTCHECK-Ereignissen des Docker-Tools in den Service Fabric-Systemintegritätsbericht. |
|EnableProcessDebugging|Boolesch, Standardwert FALSE|Dynamisch| Ermöglicht das Starten von Anwendungshosts unter dem Debugger. |
|EndpointProviderEnabled| Boolesch, Standardwert FALSE|statischen| Ermöglicht die Verwaltung von Endpunktressourcen durch Fabric. Erfordert die Angabe des Start- und Endportbereichs dder Anwendungen in FabricNode. |
|FabricContainerAppsEnabled| Boolesch, Standardwert FALSE|statischen| |
|FirewallPolicyEnabled|Boolesch, Standardwert FALSE|statischen| Ermöglicht das Öffnen von Firewallports für die Endpunktressourcen mit expliziten Ports, die in ServiceManifest angegeben werden. |
|GetCodePackageActivationContextTimeout|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(120)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Der Timeoutwert für die CodePackageActivationContext-Aufrufe. Dies gilt nicht für Ad-hoc-Dienste. |
|GovernOnlyMainMemoryForProcesses|Boolesch, Standardwert FALSE|statischen|Das Standardverhalten der Ressourcengovernance besteht darin, einen in „MemoryInMB“ angegebenen Grenzwert für die vom Prozess verwendete Gesamtspeichermenge (Arbeitsspeicher plus Auslagerungsdateien) festzulegen. Wenn der Grenzwert überschritten wird, empfängt der Prozess eine OutOfMemory-Ausnahme. Wenn dieser Parameter auf „true“ festgelegt wird, wird dieser Grenzwert nur auf die Menge an Arbeitsspeicher angewendet, die von einem Prozess verwendet wird. Wenn dieser Grenzwert überschritten wird und die Einstellung auf „true“ festgelegt ist, lagert das Betriebssystem den Hauptspeicher auf einen Datenträger aus. |
|IPProviderEnabled|Boolesch, Standardwert FALSE|statischen|Ermöglicht die Verwaltung von IP-Adressen. |
|IsDefaultContainerRepositoryPasswordEncrypted|Boolesch, Standardwert FALSE|statischen|Gibt an, ob DefaultContainerRepositoryPassword verschlüsselt ist oder nicht.|
|LinuxExternalExecutablePath|Zeichenfolge, Standardwert „/usr/bin/“ |statischen|Das primäre Verzeichnis der externen ausführbaren Befehle auf dem Knoten|
|NTLMAuthenticationEnabled|Boolesch, Standardwert FALSE|statischen| Ermöglicht die Unterstützung für die Verwendung von NTLM durch die Codepakete, die als andere Benutzer ausgeführt werden, damit die Prozesse computerübergreifend sicher kommunizieren können. |
|NTLMAuthenticationPasswordSecret|SecureString, Standardwert „Common::SecureString("")“|statischen|Eine verschlüsselte Zeichenfolge, der verwendet wird, um das Kennwort für NTLM-Benutzer zu generieren. Muss festgelegt werden, wenn NTLMAuthenticationEnabled auf TRUE festgelegt ist. Wird vom Bereitsteller überprüft. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, Standardwert Common::TimeSpan::FromMinutes(3)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Umgebungsspezifische Einstellungen. Das regelmäßige Intervall, in dem das Hosting auf neue Zertifikate für die FileStoreService-NTLM-Konfiguration scannt. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, Standardwert Common::TimeSpan::FromMinutes(4)|Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Das Timeout für die Konfiguration von NTLM-Benutzern mit allgemeinen Zertifikatnamen. Die NTLM-Benutzer sind für FileStoreService-Freigaben erforderlich. |
|PruneContainerImages|Boolesch, Standardwert FALSE|Dynamisch| Entfernen Sie nicht verwendete Anwendungscontainerimages aus Knoten. Wird die Registrierung eines Anwendungstyps im Service Fabric-Cluster aufgehoben, werden die von dieser Anwendung verwendeten Containerimages auf Knoten entfernt, auf die die Anwendung von Service Fabric heruntergeladen wurde. Die Bereinigung wird stündlich ausgeführt. Daher dauert es unter Umständen bis zu einer Stunde (zuzüglich der Zeit zum Bereinigen des Images), bis Images vom Cluster entfernt werden.<br>Von Service Fabric werden niemals Images heruntergeladen oder gelöscht, die nicht mit einer Anwendung in Zusammenhang stehen.  In keinem Zusammenhang stehende Images, die manuell heruntergeladen wurden, müssen explizit entfernt werden.<br>Images, die nicht gelöscht werden sollen, können im ContainerImagesToSkip-Parameter angegeben werden.| 
|RegisterCodePackageHostTimeout|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(120)|Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Der Timeoutwert für den FabricRegisterCodePackageHost-Synchronisierungsaufruf. Dies gilt für nur Multicode-Paketanwendungshosts wie FWP. |
|RequestTimeout|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(30)|Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Dies stellt das Timeout für die Kommunikation zwischen dem Anwendungshost des Benutzers und dem Fabric-Prozess für verschiedene hostingbezogene Vorgänge dar, z. B. für Factoryregistrierung und Runtimeregistrierung. |
|RunAsPolicyEnabled| Boolesch, Standardwert FALSE|statischen| Ermöglicht das Ausführen von Codepaketen als anderer lokaler Benutzer als der Benutzer, unter dem der Fabric-Prozess ausgeführt wird. Um diese Richtlinie zu aktivieren, muss Fabric als SYSTEM oder als Benutzer mit SeAssignPrimaryTokenPrivilege ausgeführt werden. |
|ServiceFactoryRegistrationTimeout| TimeSpan, Standardwert Common::TimeSpan::FromSeconds(120)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Der Timeoutwert für den Register(Stateless/Stateful)ServiceFactory-Synchronisierungsaufruf. |
|ServiceTypeDisableFailureThreshold |Ganze Zahl, Standardeinstellung 1 |Dynamisch|Dies ist der Schwellenwert für die Anzahl der Fehler. Bei Überschreitung wird FailoverManager (FM) benachrichtigt. FM deaktiviert den Diensttyp auf diesem Knoten und versucht, die Workload auf einem anderen Knoten zu platzieren. |
|ServiceTypeDisableGraceInterval|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(30)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Zeitintervall, nach dem der Diensttyp deaktiviert werden kann. |
|ServiceTypeRegistrationTimeout |Zeit in Sekunden, Standardwert 300 |Dynamisch|Die maximale Zeit, die der Diensttyp bei der Fabric registriert sein darf |
|UseContainerServiceArguments|Boolesch, Standardwert TRUE|statischen|Diese Konfiguration weist das Hosting an, das Übergeben von Argumenten (angegeben in der Konfiguration ContainerServiceArguments) an den Docker-Daemon zu überspringen.|

## <a name="httpgateway"></a>HttpGateway

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, Standardwert 50 |statischen| Anzahl der Lesevorgänge, die an die HTTP-Serverwarteschlange gesendet werden sollen. Dadurch wird die Anzahl gleichzeitiger Anforderungen gesteuert, die von HttpGateway erfüllt werden können. |
|HttpGatewayHealthReportSendInterval |Zeit in Sekunden, Standardwert 30 |statischen|Geben Sie die Zeitspanne in Sekunden an. Das Intervall, in dem das HTTP-Gateway kumulierte Integritätsberichte an den Integritätsdienst sendet. |
|HttpStrictTransportSecurityHeader|Zeichenfolge, Standardwert ist ""|Dynamisch| Geben Sie den HTTP Strict Transport Security-Headerwert an, der in jede vom HttpGateway gesendeten Antwort einbezogen werden soll. Wenn diese Einstellung auf eine leere Zeichenfolge festgelegt wird, wird der Header in der Gatewayantwort nicht einbezogen.|
|isEnabled|Boolesch, Standardwert „false“ |statischen| Aktiviert bzw. deaktiviert „HttpGateway“. „HttpGateway“ ist standardmäßig deaktiviert. |
|MaxEntityBodySize |Uint, Standardwert 4194304 |Dynamisch|Gibt die maximale Größe des Texts an, der von einer HTTP-Anforderung erwartet werden kann. Der Standardwert ist 4 MB. Httpgateway erzeugt für eine Anforderung einen Fehler, wenn der Text größer als dieser Wert ist. Die minimale Blockgröße beim Lesen beträgt 4096 Bytes. Der Wert muss also größer oder gleich 4096. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|Aktiviert |Boolesch, Standardwert „false“ |statischen|Das Flag „Enabled“ für ImageStoreService. Standardwert: FALSE |
|MinReplicaSetSize | Ganze Zahl, Standardwert 3 |statischen|MinReplicaSetSize für ImageStoreService. |
|PlacementConstraints | string, Standardwert "" |statischen| PlacementConstraints für ImageStoreService. |
|QuorumLossWaitDuration | Zeit in Sekunden, Standardwert MaxValue |statischen| Geben Sie die Zeitspanne in Sekunden an. QuorumLossWaitDuration für ImageStoreService. |
|ReplicaRestartWaitDuration | Zeit in Sekunden, Standardwert 60,0 \* 30 |statischen|Geben Sie die Zeitspanne in Sekunden an. ReplicaRestartWaitDuration für ImageStoreService. |
|StandByReplicaKeepDuration | Zeit in Sekunden, Standardwert 3600,0 \* 2 |statischen| Geben Sie die Zeitspanne in Sekunden an. StandByReplicaKeepDuration für ImageStoreService. |
|TargetReplicaSetSize | Ganze Zahl, Standardwert 7 |statischen|TargetReplicaSetSize für ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Ganze Zahl, Standardwert 1 |Dynamisch|Flag, das angibt, ob die Einstellungen für den Arbeitsspeicher automatisch und dynamisch konfiguriert werden sollen. Bei 0 werden die Konfigurationseinstellungen für den Arbeitsspeicher direkt verwendet und nicht basierend auf Systembedingungen geändert. Bei 1 werden die Einstellungen für den Arbeitsspeicher automatisch konfiguriert und können basierend auf Systembedingungen geändert werden. |
|MaximumDestagingWriteOutstandingInKB | Ganze Zahl, Standardwert 0 |Dynamisch|Die Anzahl an KB, die das freigegebene Protokoll dem dedizierten Protokoll voraus sein darf. Verwenden Sie 0, um keine Begrenzung anzugeben.
|SharedLogId |string, Standardwert "" |statischen|Eindeutige GUID für den freigegebenen Protokollcontainer. Verwenden Sie "", wenn Sie den Standardpfad im Fabricdatenstamm nutzen. |
|SharedLogPath |string, Standardwert "" |statischen|Pfad und Dateiname für den Speicherort des freigegebenen Protokollcontainers. Verwenden Sie "", um den Standardpfad im Fabricdatenstamm zu nutzen. |
|SharedLogSizeInMB |Ganze Zahl, Standardwert 8192 |statischen|Die Anzahl an MB, die für den freigegebenen Protokollcontainer reserviert wird. |
|SharedLogThrottleLimitInPercentUsed|Ganze Zahl, Standardwert 0 | statischen | Der Prozentsatz der Nutzung des freigegebenen Protokolls (inklusive Drosselung). Der Wert muss zwischen 0 und 100 liegen. Der Wert 0 bedeutet, dass der Standardprozentwert verwendet wird. Der Wert 100 bedeutet, dass überhaupt keine Drosselung stattfindet. Ein Wert zwischen 1 und 99 gibt den Prozentsatz der Protokollnutzung an, nach dessen Überschreitung eine Drosselung erfolgt. Beispiel: Hat das freigegebene Protokoll eine Größe von 10 GB und lautet der Wert 90, erfolgt die Drosselung, sobald 9 GB davon genutzt werden. Die Verwendung des Standardwerts wird empfohlen.|
|WriteBufferMemoryPoolMaximumInKB | Ganze Zahl, Standardwert 0 |Dynamisch|Die Anzahl an KB, bis zu der der Schreibpuffer-Speicherpool anwachsen kann. Verwenden Sie 0, um keine Begrenzung anzugeben. |
|WriteBufferMemoryPoolMinimumInKB |Ganze Zahl, Standardwert 8388608 |Dynamisch|Die Anzahl an KB, die anfänglich für den Schreibpuffer-Speicherpool reserviert wird. Verwenden Sie 0, um eine Begrenzung anzugeben. Der Standardwert muss mit SharedLogSizeInMB weiter unten konsistent sein. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|isEnabled|Boolesch, Standardwert FALSE|statischen|Flag, das das Vorhandensein und den Status des Diensts für verwaltete Identitätstoken im Cluster steuert. Dies ist eine Voraussetzung für die Verwendung der Funktionen für verwaltete Identitäten von Service Fabric-Anwendungen.|

## <a name="management"></a>Verwaltung

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|TimeSpan, Standardwert Common::TimeSpan::FromMinutes(5)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das zulässige Bereinigungsintervall zum Aufheben der Registrierung des Anwendungstyps während der automatischen Bereinigung des Anwendungstyps.|
|AzureStorageMaxConnections | Ganze Zahl, Standardwert 5000 |Dynamisch|Die maximale Anzahl gleichzeitiger Verbindungen mit Azure Storage. |
|AzureStorageMaxWorkerThreads | Ganze Zahl, Standardwert 25 |Dynamisch|Die maximale Anzahl von parallelen Workerthreads. |
|AzureStorageOperationTimeout | Zeit in Sekunden, Standardwert 6000 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Timeout für den Abschluss des xstore-Vorgangs. |
|CleanupApplicationPackageOnProvisionSuccess|Boolesch, Standardwert ist TRUE |Dynamisch|Aktiviert oder deaktiviert die automatische Bereinigung von Anwendungspaketen nach der erfolgreichen Bereitstellung.
|CleanupUnusedApplicationTypes|Boolesch, Standardwert ist „false“ |Dynamisch|Wenn diese Konfiguration aktiviert wird, ermöglicht sie das automatische Aufheben der Registrierung nicht genutzter Versionen von Anwendungstypen, wobei die letzten drei nicht genutzten Versionen übersprungen werden. Damit wird der vom Imagespeicher verbrauchte Speicherplatz auf dem Datenträger reduziert. Die automatische Bereinigung wird am Ende der erfolgreichen Bereitstellung für den spezifischen Anwendungstyp ausgelöst und auch regelmäßig einmal pro Tag für alle Anwendungstypen ausgeführt. Die Anzahl nicht verwendeter Versionen, die übersprungen werden sollen, kann über den Parameter MaxUnusedAppTypeVersionsToKeep konfiguriert werden. <br/> *Die bewährte Methode ist die Verwendung von `true`.*
|DisableChecksumValidation | Boolesch, Standardwert „false“ |statischen| Diese Konfiguration ermöglicht es, die Prüfsummenüberprüfung während der Anwendungsbereitstellung zu aktivieren oder zu deaktivieren. |
|DisableServerSideCopy | Boolesch, Standardwert „false“ |statischen|Diese Konfiguration aktiviert oder deaktiviert serverseitiges Kopieren des Anwendungspakets in ImageStore während der Anwendungsbereitstellung. |
|ImageCachingEnabled | Boolesch, Standardwert „true“ |statischen|Diese Konfiguration ermöglicht es, das Zwischenspeichern zu aktivieren oder zu deaktivieren. |
|ImageStoreConnectionString |SecureString |statischen|Die Verbindungszeichenfolge für das Stammverzeichnis für ImageStore. |
|ImageStoreMinimumTransferBPS | Ganze Zahl, Standardwert 1024 |Dynamisch|Die minimale Übertragungsrate zwischen dem Cluster und ImageStore. Dieser Wert wird verwendet, um das Timeout zu bestimmen, wenn auf den externen ImageStore zugegriffen wird. Ändern Sie diesen Wert nur, wenn die Latenz zwischen dem Cluster und ImageStore hoch ist, damit der Cluster mehr Zeit für Downloads aus dem externen ImageStore erhält. |
|MaxUnusedAppTypeVersionsToKeep | Ganze Zahl, Standardwert 3 |Dynamisch|Diese Konfiguration definiert die Anzahl der nicht verwendeten Versionen von Anwendungstypen, die bei der Bereinigung übersprungen werden sollen. Dieser Parameter gilt nur, wenn der Parameter CleanupUnusedApplicationTypes aktiviert ist. <br/>*Als allgemeine bewährte Methode wird die Verwendung des Standardwerts (`3`) empfohlen. Werte kleiner als 1 sind nicht gültig.*|


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parameter** | **Zulässige Werte** |**Upgraderichtlinie**| **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, Standardwert None|Dynamisch|Bestimmt die MetricActivityThresholds für die Metriken im Cluster. Der Lastenausgleich funktioniert, wenn maxNodeLoad größer als MetricActivityThresholds ist. Für Defragmentierungsmetriken definiert dieser Wert die Auslastung, bei oder unter der Service Fabric den Knoten als leer betrachtet. |

## <a name="metricbalancingthresholds"></a>MetricActivityThresholds
| **Parameter** | **Zulässige Werte** |**Upgraderichtlinie**| **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, Standardwert None|Dynamisch|Bestimmt die Sammlung der MetricActivityThresholds für die Metriken im Cluster. Der Lastenausgleich funktioniert, wenn maxNodeLoad/minNodeLoad größer als MetricBalancingThresholds ist. Die Defragmentierung funktioniert, wenn maxNodeLoad/minNodeLoad in mindestens einem FD oder UD kleiner als MetricBalancingThresholds ist. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parameter** | **Zulässige Werte** |**Upgraderichtlinie**| **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, Standardwert None|Dynamisch|Bestimmt den Teil der Last, der beim Austauschen auf das Replikat festgelegt wird. Es wird ein Wert zwischen „0“ (Last wird nicht mit Replikat festgelegt) und „1“ (Last wird mit Replikat festgelegt – Standard) benötigt. |

## <a name="namingservice"></a>NamingService

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Ganze Zahl, Standardwert 0 |statischen|Die maximale Anzahl von Einträgen, die im LRU-Dienstbeschreibungscache im Naming Gateway beibehalten werden (bei 0 gilt keine Begrenzung). |
|MaxClientConnections |Ganze Zahl, Standardwert 1000 |Dynamisch|Die maximal zulässige Anzahl von Clientverbindungen pro Gateway. |
|MaxFileOperationTimeout |Zeit in Sekunden, Standardwert 30 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das maximale Timeout, das für den Vorgang des Dateispeicherdiensts zulässig ist. Anforderungen, die einen höheren Timeoutwert angeben, werden zurückgewiesen. |
|MaxIndexedEmptyPartitions |Ganze Zahl, Standardwert 1000 |Dynamisch|Die maximale Anzahl von leeren Partitionen, die im Benachrichtigungscache indiziert bleiben, um Clients zu synchronisieren, die erneut eine Verbindung herstellen. Leere Partitionen oberhalb dieser Anzahl werden in aufsteigender Reihenfolge der Suchversionen aus dem Index entfernt. Clients, die erneut Verbindungen herstellen, können weiterhin synchronisiert werden und versäumte Aktualisierungen leerer Partitionen empfangen. Das Synchronisierungsprotokoll wird jedoch umfangreicher. |
|MaxMessageSize |Ganze Zahl, Standardwert 4\*1024\*1024 |statischen|Die maximale Nachrichtengröße für die Clientknotenkommunikation, wenn Benennungen verwendet werden. DOS-Angriffsbekämpfung, Standardwert 4 MB. |
|MaxNamingServiceHealthReports | Ganze Zahl, Standardwert 10 |Dynamisch|Die maximale Anzahl von langsamen Vorgängen, die der Naming Service-Speicher gleichzeitig als fehlerhaft meldet. Bei 0 werden alle langsamen Vorgänge gesendet. |
|MaxOperationTimeout |Zeit in Sekunden, Standardwert 600 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das maximale Timeout, das für Clientvorgänge zulässig ist. Anforderungen, die einen höheren Timeoutwert angeben, werden zurückgewiesen. |
|MaxOutstandingNotificationsPerClient |Ganze Zahl, Standardwert 1000 |Dynamisch|Die maximale Anzahl von ausstehenden Benachrichtigungen, bevor eine Clientregistrierung erzwungenermaßen vom Gateway geschlossen wird. |
|MinReplicaSetSize | Ganze Zahl, Standardwert 3 |Nicht zulässig| Die Mindestanzahl von Naming Service-Replikaten, in die geschrieben werden muss, um eine Aktualisierung abzuschließen. Wenn weniger Replikate als diese Anzahl im System aktiv sind, verweigert das System für die Zuverlässigkeit Aktualisierungen des Naming Service-Speichers, bis Replikate wiederhergestellt werden. Dieser Wert sollte nie größer als TargetReplicaSetSize sein. |
|PartitionCount |Ganze Zahl, Standardwert 3 |Nicht zulässig|Die Anzahl der zu erstellenden Partitionen des Naming Service-Speichers. Jede Partition besitzt einen einzelnen Partitionsschlüssel, der ihrem Index entspricht. Daher sind Partitionsschlüssel [0; PartitionCount] vorhanden. Durch Erhöhen der Anzahl von Naming Service-Partitionen kann der Naming Service in größerem Maßstab ausgeführt werden, indem die durchschnittliche Menge von Daten verringert wird, die von sichernden Replikatgruppen gespeichert werden. Dies erhöht jedoch die Auslastung von Ressourcen (da Dienstreplikate im Umfang von PartitionCount × ReplicaSetSize beibehalten werden müssen).|
|PlacementConstraints | string, Standardwert "" |Nicht zulässig| Platzierungseinschränkung für den Naming Service. |
|QuorumLossWaitDuration | Zeit in Sekunden, Standardwert MaxValue |Nicht zulässig| Geben Sie die Zeitspanne in Sekunden an. Wenn in einem Naming Service ein Quorumverlust auftritt, wird dieser Timer gestartet. Nach dessen Ablauf betrachtet FM die ausgefallenen Replikate als verloren und versucht, das Quorum wiederherzustellen. Beachten Sie, dass dies zu Datenverlusten führen kann. |
|RepairInterval | Zeit in Sekunden, Standardwert 5 |statischen| Geben Sie die Zeitspanne in Sekunden an. Intervall, in dem die Korrektur von Benennungsinkonsistenzen zwischen dem Autoritätsbesitzer und dem Namensbesitzer gestartet wird. |
|ReplicaRestartWaitDuration | Zeit in Sekunden, Standardwert (60,0 * 30)|Nicht zulässig| Geben Sie die Zeitspanne in Sekunden an. Wenn ein Naming Service-Replikat ausfällt, wird dieser Timer gestartet. Nach Ablauf beginnt der FM damit, die ausgefallenen Replikate zu ersetzen (die Replikate werden noch nicht als verloren betrachtet). |
|ServiceDescriptionCacheLimit | Ganze Zahl, Standardwert 0 |statischen| Die maximale Anzahl von Einträgen, die im LRU-Dienstbeschreibungscache im Naming Service-Speicher beibehalten werden (bei 0 gilt keine Begrenzung). |
|ServiceNotificationTimeout |Zeit in Sekunden, Standardwert 30 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Timeout, das verwendet wird, wenn Dienstbenachrichtigungen an den Client übermittelt werden. |
|StandByReplicaKeepDuration | Zeit in Sekunden, Standardwert 3600,0 * 2 |Nicht zulässig| Geben Sie die Zeitspanne in Sekunden an. Wenn ein Naming Service-Replikat nach einem Ausfall wieder aktiv ist, wurde es möglicherweise bereits ersetzt. Dieser Timer bestimmt, wie lange FM das Reservereplikat beibehält, bevor es verworfen wird. |
|TargetReplicaSetSize |Ganze Zahl, Standardwert 7 |Nicht zulässig|Die Anzahl von Replikatgruppen für jede Partition des Naming Service-Speichers. Das Erhöhen der Anzahl von Replikatgruppen erhöht den Grad an Zuverlässigkeit der Informationen im Naming Service-Speicher. Durch eine Verringerung der Änderung gehen Informationen als Ergebnis von Knotenfehlern verloren. Es entsteht eine erhöhte Last für Windows Fabric, und die Zeitspanne zum Durchführen von Aktualisierungen an den Benennungsdaten wird verlängert.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parameter** | **Zulässige Werte** |**Upgraderichtlinie**| **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, Standardwert None|Dynamisch|Der Knotenkapazitäts-Prozentsatz pro Metrikname. Wird als Puffer verwendet, um freien Speicherplatz auf einem Knoten für Failover bereitzustellen. |

## <a name="nodecapacities"></a>NodeCapacities

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |statischen|Eine Sammlung von Knotenkapazitäten für verschiedene Metriken. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |statischen|Beschreibt die Fehlerdomäne, zu der ein Knoten gehört. Die Fehlerdomäne wird durch einen URI definiert, der die Position des Knotens im Datencenter beschreibt.  Fehlerdomänen-URIs weisen das Format fd:/fd/ gefolgt von einem URI-Pfadsegment auf.|
|UpgradeDomainId |string, Standardwert "" |statischen|Beschreibt die Upgradedomäne, zu der ein Knoten gehört. |

## <a name="nodeproperties"></a>NodeProperties

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |statischen|Eine Sammlung der Zeichenfolgen von Schlüssel-Wert-Paaren für Knoteneigenschaften. |

## <a name="paas"></a>Paas

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ClusterId |string, Standardwert "" |Nicht zulässig|X509-Zertifikatspeicher, der vom Fabric für den Konfigurationsschutz verwendet. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|Counters |String | Dynamisch |Durch Trennzeichen getrennte Liste der zu erfassenden Leistungsindikatoren. |
|isEnabled |Boolesch, Standardwert „true“ | Dynamisch |Das Flag gibt an, ob die Erfassung von Leistungsindikatoren für den lokalen Knoten aktiviert ist. |
|MaxCounterBinaryFileSizeInMB |Ganze Zahl, Standardwert 1 | Dynamisch |Maximale Größe (in MB) für jede Leistungsindikator-Binärdatei. |
|NewCounterBinaryFileCreationIntervalInMinutes |Ganze Zahl, Standardwert 10 | Dynamisch |Maximales Intervall (in Sekunden), nach dem eine neue Leistungsindikator-Binärdatei erstellt wird. |
|SamplingIntervalInSeconds |Ganze Zahl, Standardwert 60 | Dynamisch |Samplingintervall für Leistungsindikatoren, die erfasst werden. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Ganze Zahl, Standardwert 0 | Dynamisch|Bestimmt die Priorität der Affinitätseinschränkung: 0: Stark; 1: Schwach; negativ: Ignorieren. |
|ApplicationCapacityConstraintPriority | Ganze Zahl, Standardwert 0 | Dynamisch|Bestimmt die Priorität der Kapazitätseinschränkung: 0: Stark; 1: Schwach; negativ: Ignorieren. |
|AutoDetectAvailableResources|Boolesch, Standardwert TRUE|statischen|Diese Konfiguration löst eine automatische Erkennung der verfügbaren Ressourcen auf dem Knoten (CPU und Arbeitsspeicher) aus. Wenn diese Konfiguration auf TRUE festgelegt ist, lesen wir reale Kapazitäten aus und korrigieren sie, wenn der Benutzer fehlerhafte Knotenkapazitäten angegeben oder keine Kapazitäten definiert hat. Wenn diese Konfiguration auf FALSE festgelegt ist, geben wir eine Warnung aus, dass der Benutzer fehlerhafte Knotenkapazitäten angegeben hat; korrigieren diese aber nicht. Dies bedeutet, dass der Benutzer die Kapazitäten nutzen möchte, die als größer als die tatsächlichen Kapazitäten des Knotens angegeben wurden, oder wenn Kapazitäten nicht definiert sind; wird von einer unbegrenzten Kapazität ausgegangen. |
|BalancingDelayAfterNewNode | Zeit in Sekunden, Standardwert 120 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Starten Sie keine Aktivitäten zum Lastenausgleich innerhalb dieses Zeitraums nach dem Hinzufügen eines neuen Knotens. |
|BalancingDelayAfterNodeDown | Zeit in Sekunden, Standardwert 120 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Starten Sie keine Aktivitäten zum Lastenausgleich innerhalb dieses Zeitraums nach einem Knotenausfall. |
|BlockNodeInUpgradeConstraintPriority | Ganze Zahl, Standardwert 0 |Dynamisch|Bestimmt die Priorität der Kapazitätseinschränkung: 0: Stark; 1: Schwach; negativ: Ignorieren  |
|CapacityConstraintPriority | Ganze Zahl, Standardwert 0 | Dynamisch|Bestimmt die Priorität der Kapazitätseinschränkung: 0: Stark; 1: Schwach; negativ: Ignorieren. |
|ConsecutiveDroppedMovementsHealthReportLimit | Ganze Zahl, Standardwert 20 | Dynamisch|Definiert die Anzahl der aufeinanderfolgenden Male, die von ResourceBalancer ausgegebene Datenverschiebungen verworfen werden, bevor eine Diagnose durchgeführt wird und Integritätswarnungen ausgegeben werden. Negativ: Unter dieser Bedingung werden keine Warnungen ausgegeben. |
|ConstraintFixPartialDelayAfterNewNode | Zeit in Sekunden, Standardwert 120 |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Beheben Sie keine FaultDomain- und UpgradeDomain-Einschränkungsverletzungen innerhalb dieses Zeitraums nach dem Hinzufügen eines neuen Knotens. |
|ConstraintFixPartialDelayAfterNodeDown | Zeit in Sekunden, Standardwert 120 |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Beheben Sie keine FaultDomain- und UpgradeDomain-Einschränkungsverletzungen innerhalb dieses Zeitraums nach einem Knotenausfall. |
|ConstraintViolationHealthReportLimit | Ganze Zahl, Standardwert 50 |Dynamisch| Definiert, wie häufig ein Einschränkungen verletzendes Replikat dauerhaft nicht korrigiert bleiben muss, bevor eine Diagnose durchgeführt wird und Integritätsberichte ausgegeben werden. |
|DetailedConstraintViolationHealthReportLimit | Ganze Zahl, Standardwert 200 |Dynamisch| Definiert, wie häufig ein Einschränkungen verletzendes Replikat dauerhaft nicht korrigiert bleiben muss, bevor eine Diagnose durchgeführt wird und detaillierte Integritätsberichte ausgegeben werden. |
|DetailedDiagnosticsInfoListLimit | Ganze Zahl, Standardwert 15 |Dynamisch| Definiert die Anzahl von Diagnoseeinträgen (mit detaillierten Informationen) pro Einschränkung, die vor der Kürzung in die Diagnose aufgenommen werden.|
|DetailedNodeListLimit | Ganze Zahl, Standardwert 15 |Dynamisch| Definiert die Anzahl von Knoten pro Einschränkung, die vor der Kürzung in Berichte zu nicht platzierten Replikaten aufgenommen werden. |
|DetailedPartitionListLimit | Ganze Zahl, Standardwert 15 |Dynamisch| Definiert die Anzahl von Partitionen pro Diagnoseeintrag für eine Einschränkung, die vor der Kürzung in die Diagnose aufgenommen werden. |
|DetailedVerboseHealthReportLimit | Ganze Zahl, Standardwert 200 | Dynamisch|Definiert, wie häufig ein nicht platziertes Replikat dauerhaft nicht platziert bleiben muss, bevor detaillierte Integritätsberichte ausgegeben werden. |
|EnforceUserServiceMetricCapacities|Boolesch, Standardwert FALSE | statischen |Aktiviert den Schutz für Fabric-Dienste. Alle Benutzerdienste befinden sich unter einem einzelnen Auftragsobjekt/einer einzelnen cgroup und sind auf die angegebene Menge von Ressourcen beschränkt. Diese muss statisch sein (erfordert einen Neustart von FabricHost), da das Erstellen/Entfernen von Benutzerauftragsobjekten und das Festlegen von Grenzwerten beim Öffnen des Fabric-Hosts erfolgt. |
|FaultDomainConstraintPriority | Ganze Zahl, Standardwert 0 |Dynamisch| Bestimmt die Priorität der Fehlerdomäneneinschränkung: 0: Stark; 1: Schwach; negativ: Ignorieren. |
|GlobalMovementThrottleCountingInterval | Zeit in Sekunden, Standardwert 600 |statischen| Geben Sie die Zeitspanne in Sekunden an. Geben Sie die Länge des letzten Intervalls an, für das Datenverschiebungen pro Domänenreplikat nachverfolgt werden sollen (wird zusammen mit GlobalMovementThrottleThreshold verwendet). Kann auf 0 festgelegt werden, um die globale Drosselung vollständig zu ignorieren. |
|GlobalMovementThrottleThreshold | Uint, Standardwert 1000 |Dynamisch| Maximale Anzahl von zulässigen Datenverschiebungen in der Lastenausgleichsphase im letzten von GlobalMovementThrottleCountingInterval angegebenen Intervall. |
|GlobalMovementThrottleThresholdForBalancing | Uint, Standardwert 0 | Dynamisch|Maximale Anzahl von zulässigen Datenverschiebungen in der Lastenausgleichsphase im letzten von GlobalMovementThrottleCountingInterval angegebenen Intervall. Bei 0 gilt keine Begrenzung. |
|GlobalMovementThrottleThresholdForPlacement | Uint, Standardwert 0 |Dynamisch| Maximale Anzahl von zulässigen Datenverschiebungen in der Platzierungsphase im letzten von GlobalMovementThrottleCountingInterval angegebenen Intervall. Bei 0 gilt keine Begrenzung.|
|GlobalMovementThrottleThresholdPercentage|double, Standardwert 0|Dynamisch|Maximale Anzahl der Gesamtbewegungen, die in Phasen für Lastenausgleich und Platzierung (ausgedrückt als Prozentwert der Gesamtanzahl der Replikate im Cluster) im letzten durch GlobalMovementThrottleCountingInterval angegebenen Intervall zulässig sind. Bei 0 gilt keine Begrenzung. Wenn diese Angabe und GlobalMovementThrottleThreshold festgelegt sind, wird der konservativere Grenzwert verwendet.|
|GlobalMovementThrottleThresholdPercentageForBalancing|double, Standardwert 0|Dynamisch|Maximale Anzahl der Bewegungen, die in der Phase für Lastenausgleich (ausgedrückt als Prozentwert der Gesamtanzahl der Replikate im PLB) im letzten durch GlobalMovementThrottleCountingInterval angegebenen Intervall zulässig sind. Bei 0 gilt keine Begrenzung. Wenn sowohl diese Einstellung als auch „GlobalMovementThrottleThresholdForBalancing“ festgelegt sind, wird der konservativere Grenzwert verwendet.|
|InBuildThrottlingAssociatedMetric | string, Standardwert "" |statischen| Der zugehörige Metrikname für diese Drosselung. |
|InBuildThrottlingEnabled | Boolesch, Standardwert „false“ |Dynamisch| Bestimmen Sie, ob die integrierte Drosselung aktiviert wird. |
|InBuildThrottlingGlobalMaxValue | Ganze Zahl, Standardwert 0 |Dynamisch|Die maximale, global zulässige Anzahl von integrierten Replikaten. |
|InterruptBalancingForAllFailoverUnitUpdates | Boolesch, Standardwert „false“ | Dynamisch|Bestimmt, ob jede Art von Aktualisierung der Failovereinheiten schnelle oder langsame Lastenausgleiche unterbrechen soll. Bei „false“ werden Lastenausgleiche unterbrochen, wenn die Failovereinheit erstellt/gelöscht wird, ihr Replikate fehlen bzw. sie einen geänderten Speicherort für das primäre Replikat oder eine geänderte Anzahl von Replikaten aufweist. Lastenausgleiche werden in anderen Fällen NICHT unterbrochen: wenn die Failovereinheit über zusätzliche Replikate verfügt, ein Replikatflag geändert wurde, nur die Partitionsversion geändert wurde oder in anderen Fällen. |
|MinConstraintCheckInterval | Zeit in Sekunden, Standardwert 1 |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Definiert die minimale Zeitspanne, die vor zwei aufeinanderfolgenden Runden zur Einschränkungsüberprüfung verstreichen muss. |
|MinLoadBalancingInterval | Zeit in Sekunden, Standardwert 5 |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Definiert die minimale Zeitspanne, die vor zwei aufeinanderfolgenden Runden zum Lastenausgleich verstreichen muss. |
|MinPlacementInterval | Zeit in Sekunden, Standardwert 1 |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Definiert die minimale Zeitspanne, die vor zwei aufeinanderfolgenden Platzierungsrunden verstreichen muss. |
|MoveExistingReplicaForPlacement | Boolesch, Standardwert „true“ |Dynamisch|Einstellung, die festlegt, ob vorhandene Replikate während der Platzierung verschoben werden. |
|MovementPerPartitionThrottleCountingInterval | Zeit in Sekunden, Standardwert 600 |statischen| Geben Sie die Zeitspanne in Sekunden an. Geben Sie die Länge des letzten Intervalls an, für das Datenverschiebungen pro Replikat für jede Partition nachverfolgt werden sollen (wird zusammen mit MovementPerPartitionThrottleThreshold verwendet). |
|MovementPerPartitionThrottleThreshold | Uint, Standardwert 50 |Dynamisch| Für eine Partition werden keine Datenverschiebungen im Zusammenhang mit dem Lastenausgleich ausgeführt, wenn die Anzahl der Datenverschiebungen im Zusammenhang mit dem Lastenausgleich für Replikate dieser Partition im letzten von MovementPerPartitionThrottleCountingInterval angegebenen Intervall den Wert von MovementPerFailoverUnitThrottleThreshold erreicht oder überschritten hat. |
|MoveParentToFixAffinityViolation | Boolesch, Standardwert „false“ |Dynamisch| Einstellung, die festlegt, ob übergeordnete Replikate verschoben werden können, um Affinitätseinschränkungen zu beheben.|
|PartiallyPlaceServices | Boolesch, Standardwert „true“ |Dynamisch| Bestimmt, ob alle Dienstreplikate im Cluster „ganz oder gar nicht“ platziert werden, wenn nur beschränkt geeignete Knoten vorhanden sind.|
|PlaceChildWithoutParent | Boolesch, Standardwert „true“ | Dynamisch|Einstellung, die festlegt, ob untergeordnete Dienstreplikate platziert werden können, wenn kein übergeordnetes Replikat ausgeführt wird. |
|PlacementConstraintPriority | Ganze Zahl, Standardwert 0 | Dynamisch|Bestimmt die Priorität der Platzierungseinschränkung: 0: Stark; 1: Schwach; negativ: Ignorieren. |
|PlacementConstraintValidationCacheSize | Ganze Zahl, Standardwert 10000 |Dynamisch| Schränkt die Größe der Tabelle für die schnelle Überprüfung und Zwischenspeicherung von Einschränkungsausdrücken für die Platzierung ein. |
|PlacementSearchTimeout | Zeit in Sekunden, Standardwert 0,5 |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Beim Platzieren von Diensten wird für maximal diese Zeitspanne gesucht, bevor ein Ergebnis zurückgegeben wird. |
|PLBRefreshGap | Zeit in Sekunden, Standardwert 1 |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Definiert die minimale Zeitspanne, die verstreichen muss, bevor PLB den Zustand erneut aktualisiert. |
|PreferredLocationConstraintPriority | Ganze Zahl, Standardwert 2| Dynamisch|Bestimmt die Priorität der Einschränkung für den bevorzugten Speicherort: 0: Stark; 1: Schwach; 2: Optimierung; negativ: Ignorieren |
|PreferredPrimaryDomainsConstraintPriority| Ganze Zahl, Standardwert 1 | Dynamisch| Legt die Priorität der Einschränkung für die bevorzugte primäre Domäne fest: 0: Stark; 1: Schwach; negativ: Ignorieren |
|PreferUpgradedUDs|Boolesch, Standardwert FALSE|Dynamisch|Aktiviert und deaktiviert die Logik, die ein Verschieben zu UDs bevorzugt, für die bereits ein Upgrade durchgeführt wurde. Ab Service Fabric 7.0 lautet der Standardwert für diesen Parameter nicht mehr TRUE sondern FALSE.|
|PreventTransientOvercommit | Boolesch, Standardwert „false“ | Dynamisch|Bestimmt, ob PLB sofort Ressourcen nutzen soll, die von den initiierten Datenverschiebungen freigegeben werden. Standardmäßig kann PLB Datenverschiebungen aus und in einen Knoten initiieren, sodass eine vorübergehende Überlastung entstehen kann. Wenn dieser Parameter auf TRUE festgelegt wird, wird diese Art von Überlastungen verhindert, und eine bedarfsgesteuerte Defragmentierung (placementWithMove) wird deaktiviert. |
|ScaleoutCountConstraintPriority | Ganze Zahl, Standardwert 0 |Dynamisch| Bestimmt die Priorität der Einschränkung für den Umfang der horizontalen Hochskalierung: 0: Stark; 1: Schwach; negativ: Ignorieren. |
|SubclusteringEnabled|Boolesch, Standardwert ist „false“ | Dynamisch |Berücksichtigt das Subclustering beim Berechnen der Standardabweichung für den Ausgleich |
|SubclusteringReportingPolicy| Ganze Zahl, Standardwert 1 |Dynamisch|Definiert, ob und wie Integritätsberichte zum Subclustering gesendet werden: 0: Nicht melden; 1: Warnung; 2: OK |
|SwapPrimaryThrottlingAssociatedMetric | string, Standardwert ""|statischen| Der zugehörige Metrikname für diese Drosselung. |
|SwapPrimaryThrottlingEnabled | Boolesch, Standardwert „false“|Dynamisch| Bestimmen Sie, ob die Drosselung beim Austausch des primären Replikats aktiviert wird. |
|SwapPrimaryThrottlingGlobalMaxValue | Ganze Zahl, Standardwert 0 |Dynamisch| Die maximale, global zulässige Anzahl von primären Replikaten für den Austausch. |
|TraceCRMReasons |Boolesch, Standardwert „true“ |Dynamisch|Gibt an, ob Gründe für von CRM ausgegebene Datenverschiebungen zum Kanal für Betriebsereignisse verfolgt werden sollen. |
|UpgradeDomainConstraintPriority | Ganze Zahl, Standardwert 1| Dynamisch|Bestimmt die Priorität der Einschränkung für die Upgradedomäne: 0: Stark; 1: Schwach; negativ: Ignorieren. |
|UseMoveCostReports | Boolesch, Standardwert „false“ | Dynamisch|Weist LB an, das Kostenelement der Bewertungsfunktion zu ignorieren. Dies führt möglicherweise zu mehr Datenverschiebungen für eine Platzierung mit besserem Lastenausgleich. |
|UseSeparateSecondaryLoad | Boolesch, Standardwert „true“ | Dynamisch|Hierbei handelt es sich um eine Einstellung, die festlegt, ob für sekundäre Replikate eine separate Auslastung verwendet werden soll. |
|UseSeparateSecondaryMoveCost | Boolesch, Standardwert „false“ | Dynamisch|Hierbei handelt es sich um eine Einstellung, die festlegt, ob für sekundäre Replikate separate Verschiebungskosten verwendet werden sollen. |
|ValidatePlacementConstraint | Boolesch, Standardwert „true“ |Dynamisch| Gibt an, ob der PlacementConstraint-Ausdruck für einen Dienst überprüft wird, wenn ServiceDescription für einen Dienst aktualisiert wird. |
|ValidatePrimaryPlacementConstraintOnPromote| Boolesch, Standardwert TRUE |Dynamisch|Gibt an, ob der Ausdruck „PlacementConstraint“ für einen Dienst beim Failover für die primäre Einstellung ausgewertet werden soll oder nicht. |
|VerboseHealthReportLimit | Ganze Zahl, Standardwert 20 | Dynamisch|Definiert, wie häufig ein Replikat nicht platziert werden muss, bevor eine Integritätswarnung dafür gemeldet wird (wenn ausführliche Integritätsberichte aktiviert sind). |
|NodeLoadsOperationalTracingEnabled | Boolesch, Standardwert „true“ |Dynamisch|Konfiguration, die die operative Ablaufverfolgung des Knotenladevorgangs im Ereignisspeicher ermöglicht. |
|NodeLoadsOperationalTracingInterval | TimeSpan, Standardwert ist Common::TimeSpan::FromSeconds(20) | Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Intervall, in dem Knotenladevorgänge in den Ereignisspeicher für jede Dienstdomäne nachverfolgt werden. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Zeit in Sekunden, Standardwert 900 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Die Dauer, die das System wartet, bevor Diensthosts beendet werden, deren Replikate während des Anwendungsupgrades nicht geöffnet werden können.|
|FabricUpgradeMaxReplicaCloseDuration | Zeit in Sekunden, Standardwert 900 |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. Die Dauer, die das System wartet, bevor Diensthosts beendet werden, deren Replikate während des Fabric-Upgrades nicht geöffnet werden können. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(120)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Die Dauer, die das System wartet, bevor Diensthosts beendet werden, deren Replikate nicht geöffnet werden können. Wenn der Wert auf 0 (null) festgelegt ist, werden keine Replikate zum Schließen angewiesen.|
|NodeDeactivationMaxReplicaCloseDuration | Zeit in Sekunden, Standardwert 900 |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Die Dauer, die das System wartet, bevor Diensthosts beendet werden, deren Replikate während der Knotendeaktivierung nicht geöffnet werden können. |
|PeriodicApiSlowTraceInterval | Zeit in Sekunden, Standardwert 5 Minuten |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. PeriodicApiSlowTraceInterval definiert das Intervall, in dem langsame API-Aufrufe vom API-Monitor zurückverfolgt werden. |
|ReplicaChangeRoleFailureRestartThreshold|Ganze Zahl, Standardwert 10|Dynamisch| Eine ganze Zahl. Gibt die Anzahl der API-Fehler bei der Heraufstufung des primären Replikators an, nach der eine automatische Minderungsaktion (Replikatneustart) angewendet wird. |
|ReplicaChangeRoleFailureWarningReportThreshold|Ganze Zahl, Standardwert 2147483647|Dynamisch| Eine ganze Zahl. Gibt die Anzahl der API-Fehler bei der Heraufstufung des primären Replikators an, nach der ein Warnungsintegritätsbericht ausgelöst wird.|
|ServiceApiHealthDuration | Zeit in Sekunden, Standardwert 30 Minuten |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. ServiceApiHealthDuration definiert, wie lange auf die Ausführung einer Dienst-API gewartet wird, bevor sie als fehlerhaft gemeldet wird. |
|ServiceReconfigurationApiHealthDuration | Zeit in Sekunden, Standardwert 30 |Dynamisch| Geben Sie die Zeitspanne in Sekunden an. ServiceReconfigurationApiHealthDuration definiert, wie lange auf die Ausführung einer Dienst-API gewartet wird, bevor sie als fehlerhaft gemeldet wird. Dies gilt für API-Aufrufe, die sich auf die Verfügbarkeit auswirken.|

## <a name="replication"></a>Replikation
| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie**| **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, Standardwert Common::TimeSpan::FromMilliseconds(15)|statischen|Geben Sie die Zeitspanne in Sekunden an. Bestimmt die Zeitdauer, die der Replikator nach dem Empfang eines Vorgangs wartet, bevor er eine Bestätigung sendet. Für andere Vorgänge, die während dieses Zeitraums empfangen werden, werden die Bestätigungen in einer einzelnen Nachricht zurückgesendet. Dadurch wird der Netzwerkverkehr verringert, jedoch möglicherweise auch der Durchsatz des Replikators.|
|MaxCopyQueueSize|uint, Standardwert 1024|statischen|Dies ist der maximale Wert. Er definiert die Anfangsgröße für die Warteschlange, die Replikationsvorgänge verwaltet. Beachten Sie, dass der Wert eine Potenz von 2 sein muss. Wenn während der Laufzeit die Warteschlange auf diese Größe anwächst, werden Vorgänge zwischen den primären und sekundären Replikatoren gedrosselt.|
|MaxPrimaryReplicationQueueMemorySize|uint, Standardwert 0|statischen|Dies ist der maximale Wert für die primäre Replikationswarteschlange in Bytes.|
|MaxPrimaryReplicationQueueSize|uint, Standardwert 1024|statischen|Dies ist die maximale Anzahl von Vorgängen, die in der primären Replikationswarteschlange vorhanden sein können. Beachten Sie, dass der Wert eine Potenz von 2 sein muss.|
|MaxReplicationMessageSize|uint, Standardwert 52428800|statischen|Maximale Nachrichtengröße für Replikationsvorgänge. Der Standardwert ist 50 MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, Standardwert 0|statischen|Dies ist der maximale Wert für die sekundäre Replikationswarteschlange in Bytes.|
|MaxSecondaryReplicationQueueSize|uint, Standardwert 2048|statischen|Dies ist die maximale Anzahl von Vorgängen, die in der sekundären Replikationswarteschlange vorhanden sein können. Beachten Sie, dass der Wert eine Potenz von 2 sein muss.|
|QueueHealthMonitoringInterval|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(30)|statischen|Geben Sie die Zeitspanne in Sekunden an. Dieser Wert bestimmt den Zeitraum, der vom Replikator verwendet wird, um alle Warnungs-/Fehlerintegritätsereignisse in den Warteschlangen des Replikationsvorgangs zu überwachen. Der Wert „0“ deaktiviert die Systemüberwachung. |
|QueueHealthWarningAtUsagePercent|uint, Standardwert 80|statischen|Dieser Wert bestimmt die Verwendung der Replikationswarteschlange (in Prozent), nach der eine Warnung zu hoher Warteschlangenverwendung gemeldet wird. Dies geschieht nach einem Toleranzintervall, das durch QueueHealthMonitoringInterval angegeben wird. Wenn die Warteschlangenverwendung im Toleranzintervall unter diesen Prozentsatz fällt|
|ReplicatorAddress|string, Standardwert „localhost:0“|statischen|Der Endpunkt in Form einer Zeichenfolge – „IP:Port“. Wird vom Windows Fabric-Replikator verwendet, um Verbindungen mit anderen Replikaten herzustellen, um Vorgänge zu senden/zu empfangen.|
|ReplicatorListenAddress|string, Standardwert „localhost:0“|statischen|Der Endpunkt in Form einer Zeichenfolge – „IP:Port“. Wird vom Windows Fabric-Replikator verwendet, um Vorgänge von anderen Replikaten zu empfangen.|
|ReplicatorPublishAddress|string, Standardwert „localhost:0“|statischen|Der Endpunkt in Form einer Zeichenfolge – „IP:Port“. Wird vom Windows Fabric-Replikator verwendet, um Vorgänge an andere Replikate zu senden.|
|RetryInterval|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(5)|statischen|Geben Sie die Zeitspanne in Sekunden an. Wenn ein Vorgang nicht ausgeführt oder abgelehnt wurde, bestimmt dieser Zeitgeber, wie oft der Replikator das Senden des Vorgangs erneut versucht.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie**| **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|isEnabled|Boolesch, Standardwert FALSE |statischen|Steuert, ob der Dienst im Cluster aktiviert ist |

## <a name="runas"></a>RunAs

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|RunAsAccountName |string, Standardwert "" |Dynamisch|Gibt den Namen des RunAs-Kontos an. Dies ist nur für die Kontotypen „DomainUser“ und „ManagedServiceAccount“ erforderlich. Gültige Werte sind „Domäne\Benutzer“ oder „user@domain“. |
|RunAsAccountType|string, Standardwert "" |Dynamisch|Gibt den Typ des RunAs-Kontos an. Dies ist für alle RunAs-Abschnitte erforderlich. Gültige Werte sind DomainUser/NetworkService/ManagedServiceAccount /LocalSystem.|
|RunAsPassword|string, Standardwert "" |Dynamisch|Gibt das Kennwort des RunAs-Kontos an. Dies ist nur für den Kontotyp „DomainUser“ erforderlich. |

## <a name="runas_dca"></a>RunAs_DCA

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|RunAsAccountName |string, Standardwert "" |Dynamisch|Gibt den Namen des RunAs-Kontos an. Dies ist nur für die Kontotypen „DomainUser“ und „ManagedServiceAccount“ erforderlich. Gültige Werte sind „Domäne\Benutzer“ oder „user@domain“. |
|RunAsAccountType|string, Standardwert "" |Dynamisch|Gibt den Typ des RunAs-Kontos an. Dies ist für alle RunAs-Abschnitte erforderlich. Gültige Werte sind LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem. |
|RunAsPassword|string, Standardwert "" |Dynamisch|Gibt das Kennwort des RunAs-Kontos an. Dies ist nur für den Kontotyp „DomainUser“ erforderlich. |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|RunAsAccountName |string, Standardwert "" |Dynamisch|Gibt den Namen des RunAs-Kontos an. Dies ist nur für die Kontotypen „DomainUser“ und „ManagedServiceAccount“ erforderlich. Gültige Werte sind „Domäne\Benutzer“ oder „user@domain“. |
|RunAsAccountType|string, Standardwert "" |Dynamisch|Gibt den Typ des RunAs-Kontos an. Dies ist für alle RunAs-Abschnitte erforderlich. Gültige Werte sind LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem. |
|RunAsPassword|string, Standardwert "" |Dynamisch|Gibt das Kennwort des RunAs-Kontos an. Dies ist nur für den Kontotyp „DomainUser“ erforderlich. |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|RunAsAccountName |string, Standardwert "" |Dynamisch|Gibt den Namen des RunAs-Kontos an. Dies ist nur für die Kontotypen „DomainUser“ und „ManagedServiceAccount“ erforderlich. Gültige Werte sind „Domäne\Benutzer“ oder „user@domain“. |
|RunAsAccountType|string, Standardwert "" |Dynamisch|Gibt den Typ des RunAs-Kontos an. Dies ist für alle RunAs-Abschnitte erforderlich. Gültige Werte sind LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem. |
|RunAsPassword|string, Standardwert "" |Dynamisch|Gibt das Kennwort des RunAs-Kontos an. Dies ist nur für den Kontotyp „DomainUser“ erforderlich. |

## <a name="security"></a>Sicherheit
| **Parameter** | **Zulässige Werte** |**Upgraderichtlinie**| **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|string, Standardwert ""|statischen|Format des AAD-Zertifizierungsendpunkts (Standardwert: Azure Commercial), angegeben für eine nicht standardmäßige Umgebung wie etwa Azure Government (https:\//login.microsoftonline.us/{0}/federationmetadata/2007-06/federationmetadata.xml) |
|AADClientApplication|string, Standardwert ""|statischen|Der Name der nativen Clientanwendung oder -ID, die Fabric-Clients darstellt. |
|AADClusterApplication|string, Standardwert ""|statischen|Web-API-Anwendungsname oder -ID, der bzw. die den Cluster darstellt. |
|AADLoginEndpoint|string, Standardwert ""|statischen|AAD-Anmeldeendpunkt (Standardwert: Azure Commercial), angegeben für eine nicht standardmäßige Umgebung wie etwa Azure Government (https:\//login.microsoftonline.us) |
|AADTenantId|string, Standardwert ""|statischen|Mandanten-ID (GUID) |
|AcceptExpiredPinnedClusterCertificate|Boolesch, Standardwert FALSE|Dynamisch|Flag, das angibt, ob abgelaufene, über Fingerabdruck deklarierte Clusterzertifikate akzeptiert werden sollen. Gilt nur für Clusterzertifikate, um den Cluster funktionsfähig zu halten. |
|AdminClientCertThumbprints|string, Standardwert ""|Dynamisch|Fingerabdrücke von Zertifikaten, die von Clients in der Rolle „Administrator“ verwendet werden. Es handelt sich um eine durch Kommas getrennte Liste. |
|AADTokenEndpointFormat|string, Standardwert ""|statischen|AAD-Tokenendpunkt (Standardwert: Azure Commercial), angegeben für eine nicht standardmäßige Umgebung wie etwa Azure Government (https:\//login.microsoftonline.us/{0}) |
|AdminClientClaims|string, Standardwert ""|Dynamisch|Alle möglichen Ansprüche, die von Administratorclients erwartet werden. Gleiches Format wie ClientClaims. Diese Liste wird intern ClientClaims hinzugefügt. Es besteht daher keine Notwendigkeit, die gleichen Einträge auch ClientClaims hinzuzufügen. |
|AdminClientIdentities|string, Standardwert ""|Dynamisch|Windows-Identitäten der Fabric-Clients in der Rolle „Administrator“. Wird verwendet, um privilegierte Fabric-Vorgänge zu autorisieren. Es handelt sich um eine durch Kommas getrennte Liste. Jeder Eintrag ist ein Domänenkonto- oder Gruppenname. Der Einfachheit halber wird dem Konto, das „fabric.exe“ ausführt, automatisch die Rolle „Administrator“ zugewiesen. Dies gilt auch für die Gruppe ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|Zeichenfolge, Standard ist „/home/sfuser/sfusercerts“ |statischen|Der Ordner, in dem AppRunAsAccountGroup-X509 Zertifikate und private Schlüssel gespeichert sind |
|CertificateExpirySafetyMargin|TimeSpan, Standardwert Common::TimeSpan::FromMinutes(43200)|statischen|Geben Sie die Zeitspanne in Sekunden an. Sicherheitsspanne für den Zertifikatablauf. Der Status des Integritätsberichts des Zertifikats ändert sich aus „OK“ in „Warning“, wenn das Ablaufdatum diese Angabe überschreitet. Der Standardwert sind 30 Tage. |
|CertificateHealthReportingInterval|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(3600 * 8)|statischen|Geben Sie die Zeitspanne in Sekunden an. Gibt das Intervall für Zertifikatintegritätsberichte an. Der Standardwert ist 8 Stunden. Bei Festlegung auf 0 werden Zertifikatintegritätsberichte deaktiviert. |
|ClientCertThumbprints|string, Standardwert ""|Dynamisch|Fingerabdrücke von Zertifikaten, die vom Clients für die Kommunikation mit dem Cluster verwendet werden. Cluster verwenden diese Angabe, um eingehende Verbindungen zu autorisieren. Es handelt sich um eine durch Kommas getrennte Liste. |
|ClientClaimAuthEnabled|Boolesch, Standardwert FALSE|statischen|Gibt an, ob auf Clients anspruchsbasierte Authentifizierung aktiviert ist. Durch Festlegung auf TRUE wird implizit ClientRoleEnabled festlegt. |
|ClientClaims|string, Standardwert ""|Dynamisch|Alle möglichen Ansprüche, die von Clients zum Herstellen einer Verbindung mit dem Gateway erwartet werden. Dies ist eine „OR“-Liste: ClaimsEntry \|\| ClaimsEntry \|\| ClaimsEntry ... jeder ClaimsEntry ist eine „AND“-Liste: ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|ClientIdentities|string, Standardwert ""|Dynamisch|Windows-Identitäten von FabricClient. Naming Gateway verwendet diese Angaben zum Autorisieren eingehender Verbindungen. Es handelt sich um eine durch Kommas getrennte Liste. Jeder Eintrag ist ein Domänenkonto- oder Gruppenname. Der Einfachheit halber ist das Konto, das „fabric.exe“ ausführt, automatisch zulässig. Dies gilt auch für die Gruppen ServiceFabricAllowedUsers und ServiceFabricAdministrators. |
|ClientRoleEnabled|Boolesch, Standardwert FALSE|statischen|Gibt an, ob die Rolle „Client“ aktiviert ist. Bei Festlegung auf TRUE werden Clients anhand ihrer Identitäten Rollen zugewiesen. Für V2 bedeutet die Aktivierung, dass Clients, die nicht in AdminClientCommonNames/AdminClientIdentities enthalten sind, nur schreibgeschützte Vorgänge ausführen können. |
|ClusterCertThumbprints|string, Standardwert ""|Dynamisch|Fingerabdrücke von Zertifikaten, die dem Cluster beitreten dürfen. Eine durch Kommas getrennte Liste. |
|ClusterCredentialType|string, Standardwert „None“|Nicht zulässig|Gibt den Typ der Sicherheitsanmeldeinformationen an, die verwendet werden sollen, um den Cluster zu sichern. Gültige Werte sind „None/X509/Windows“ |
|ClusterIdentities|string, Standardwert ""|Dynamisch|Windows-Identitäten der Clusterknoten, für die Autorisierung der Clustermitgliedschaft verwendet. Es handelt sich um eine durch Kommas getrennte Liste. Jeder Eintrag ist ein Domänenkonto- oder Gruppenname. |
|ClusterSpn|string, Standardwert ""|Nicht zulässig|Der Dienstprinzipalname des Clusters, wenn Fabric als einzelner Domänenbenutzer (gMSA-/Domänenbenutzerkonto) ausgeführt wird. Es handelt sich um den SPN der Leaselistener und der Listener in „fabric.exe“: Verbundlistener, interne Replikationslistener, Dienstlistener zur Laufzeit und Naming Gateway-Listener. Diese Angabe sollte leer bleiben, wenn Fabric als Computerkonten ausgeführt wird. In diesem Fall wird der Computelistener-SPN aus der Listenertransportadresse verbunden. |
|CrlCheckingFlag|uint, Standardwert 0 x 40000000|Dynamisch|Standardflag für die Überprüfung der Zertifikatkette. Kann von einem komponentenspezifischen Flag überschrieben werden, z.B. Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY. Bei Festlegen des Werts auf 0 wird die Zertifikatssperrlistenüberprüfung deaktiviert. Die vollständige Liste der unterstützten Werte wird durch dwFlags von CertGetCertificateChain dokumentiert: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, Standardwert Common::TimeSpan::FromMinutes(15)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Gibt an, wie lange die Zertifikatsperrlistenüberprüfung für ein bestimmtes Zertifikat nach einem Offlinefehler deaktiviert wird, wenn der Zertifikatsperrlisten-Offlinefehler ignoriert werden kann. |
|CrlOfflineHealthReportTtl|TimeSpan, Standardwert Common::TimeSpan::FromMinutes(1440)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. |
|DisableFirewallRuleForDomainProfile| Boolesch, Standardwert TRUE |statischen| Gibt an, ob die Firewallregel das Domänenprofil nicht aktiviert werden soll. |
|DisableFirewallRuleForPrivateProfile| Boolesch, Standardwert TRUE |statischen| Gibt an, ob die Firewallregel das private Profil nicht aktiviert werden soll. | 
|DisableFirewallRuleForPublicProfile| Boolesch, Standardwert TRUE | statischen|Gibt an, ob die Firewallregel das öffentliche Profil nicht aktiviert werden soll. |
| EnforceLinuxMinTlsVersion | Boolesch, Standardwert FALSE | statischen | Wenn auf TRUE festgelegt. Nur TLS-Version 1.2 oder höher wird unterstützt.  Wenn FALSE, Unterstützung früherer TLS-Versionen. Gilt nur für Linux |
| EnforcePrevalidationOnSecurityChanges | Boolesch, Standardwert FALSE| Dynamisch | Flag zum Steuern des Verhaltens beim Clusterupgrade, wenn Änderungen an den Sicherheitseinstellungen erkannt werden. Wenn der Wert auf TRUE festgelegt ist, wird bei einem Clusterupgrade versucht, sicherzustellen, dass mindestens eines der Zertifikate, die mit einer der Darstellungsregeln übereinstimmen, eine entsprechende Validierungsregel bestehen kann. Die Vorvalidierung wird ausgeführt, bevor die neuen Einstellungen auf einen Knoten angewandt werden. Sie wird jedoch nur auf dem Knoten ausgeführt, der zum Zeitpunkt der Initiierung des Upgrades das primäre Replikat des Cluster-Manager-Diensts hostet. Die Standardeinstellung ist derzeit FALSE. Ab Release 7.1 wird die Einstellung für neue Azure Service Fabric-Cluster auf TRUE festgelegt.|
|FabricHostSpn| string, Standardwert "" |statischen| Der Dienstprinzipalname von FabricHost, wenn Fabric als einzelner Domänenbenutzer (gMSA-/Domänenbenutzerkonto) und FabricHost unter dem Computerkonto ausgeführt wird. Es handelt sich um den SPN von IPC-Listenern für FabricHost. Standardmäßig sollte diese Angabe leer bleiben, weil FabricHost unter dem Computerkonto ausgeführt wird |
|IgnoreCrlOfflineError|Boolesch, Standardwert FALSE|Dynamisch|Gibt an, ob ein Zertifikatsperrlisten-Offlinefehler ignoriert werden soll, wenn die Serverseite eingehende Clientzertifikate überprüft. |
|IgnoreSvrCrlOfflineError|Boolesch, Standardwert TRUE|Dynamisch|Gibt an, ob ein Zertifikatsperrlisten-Offlinefehler ignoriert werden soll, wenn die Clientseite eingehende Serverzertifikate überprüft. Der Standardwert ist TRUE. Für Angriffe mit gesperrten Serverzertifikaten ist eine Gefährdung des DNS erforderlich. Dies ist schwieriger als mit gesperrten Clientzertifikaten. |
|ServerAuthCredentialType|string, Standardwert „None“|statischen|Gibt den Typ der Sicherheitsanmeldeinformationen an, die verwendet werden sollen, um die Kommunikation zwischen FabricClient und dem Cluster zu sichern. Gültige Werte sind „None/X509/Windows“ |
|ServerCertThumbprints|string, Standardwert ""|Dynamisch|Fingerabdrücke von Serverzertifikaten, die vom Cluster für die Kommunikation mit Clients verwendet werden. Clients verwenden diese Angabe, um den Cluster zu authentifizieren. Es handelt sich um eine durch Kommas getrennte Liste. |
|SettingsX509StoreName| string, Standardwert „MY“| Dynamisch|Der X509-Zertifikatspeicher, der von Fabric für den Konfigurationsschutz verwendet wird. |
|UseClusterCertForIpcServerTlsSecurity|Boolesch, Standardwert FALSE|statischen|Gibt an, ob ein Clusterzertifikat verwendet werden soll, um die TLS-Transporteinheit auf dem IPC-Server zu schützen |
|X509Folder|string, Standarwert /var/lib/waagent|statischen|Der Ordner, in dem X509 Zertifikate und private Schlüssel gespeichert sind. |
|TLS1_2_CipherList| Zeichenfolge| statischen|Wenn auf eine nicht leere Zeichenfolge festgelegt. Überschreibt die unterstützte Verschlüsselungsliste für TLS 1.2 und niedriger. Weitere Informationen zum Abrufen der unterstützten Verschlüsselungsliste und zum Listenformatbeispiel für eine starke Verschlüsselungsliste für TLS 1.2 finden Sie in der Dokumentation zu „openssl-ciphers“: „ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES-128-GCM-SHA256:ECDHE-ECDSA-AES256-CBC-SHA384:ECDHE-ECDSA-AES128-CBC-SHA256:ECDHE-RSA-AES256-CBC-SHA384:ECDHE-RSA-AES128-CBC-SHA256“ Gilt nur für Linux. |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, Standardwert None|Dynamisch|Dies ist eine Liste von Name-Wert-Paaren. Jeder Name ist der allgemeine Name des Antragstellers oder der DNS-Name von X.509-Zertifikaten, die für Administratorclientvorgänge autorisiert sind. Für einen angegebenen Namen ist der Wert eine durch Kommas getrennte Liste von Zertifikatfingerabdrücken zum Anheften des Ausstellers. Außer bei einer fehlenden Angabe muss sich der direkte Aussteller der Administratorclientzertifikate in der Liste befinden. |

## <a name="securityclientaccess"></a>Security/ClientAccess

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ActivateNode |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Aktivierung eines Knotens. |
|CancelTestCommand |string, Standardwert „Admin“ |Dynamisch| Bricht einen bestimmten TestCommand ab, wenn er aktiv ist. |
|CodePackageControl |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Neustarten von Codepaketen. |
|CreateApplication |string, Standardwert „Admin“ | Dynamisch|Sicherheitskonfiguration für die Anwendungserstellung. |
|CreateComposeDeployment|string, Standardwert „Admin“| Dynamisch|Erstellt eine Compose-Bereitstellung, die durch Compose-Dateien beschrieben wird. |
|CreateGatewayResource|string, Standardwert „Admin“| Dynamisch|Erstellen einer Gatewayressource |
|CreateName |string, Standardwert „Admin“ |Dynamisch|Sicherheitskonfiguration für die Erstellung des Benennungs-URI. |
|CreateNetwork|string, Standardwert „Admin“ |Dynamisch|Erstellt ein Containernetzwerk |
|CreateService |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Diensterstellung. |
|CreateServiceFromTemplate |string, Standardwert „Admin“ |Dynamisch|Sicherheitskonfiguration für die Diensterstellung aus einer Vorlage. |
|CreateVolume|string, Standardwert „Admin“|Dynamisch|Erstellt ein Volume. |
|DeactivateNode |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Deaktivierung eines Knotens. |
|DeactivateNodesBatch |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Deaktivierung mehrerer Knoten. |
|Löschen |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfigurationen für den Imagespeicherclient-Löschvorgang. |
|DeleteApplication |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Anwendungslöschung. |
|DeleteComposeDeployment|string, Standardwert „Admin“| Dynamisch|Löscht die Compose-Bereitstellung. |
|DeleteGatewayResource|string, Standardwert „Admin“| Dynamisch|Löscht eine Gatewayressource |
|DeleteName |string, Standardwert „Admin“ |Dynamisch|Sicherheitskonfiguration für die Löschung des Benennungs-URI. |
|DeleteNetwork|string, Standardwert „Admin“ |Dynamisch|Löscht ein Containernetzwerk |
|DeleteService |string, Standardwert „Admin“ |Dynamisch|Sicherheitskonfiguration für die Dienstlöschung. |
|DeleteVolume|string, Standardwert „Admin“|Dynamisch|Löscht ein Volume.| 
|EnumerateProperties |string, Standardwert „Admin\|\|User“ | Dynamisch|Sicherheitskonfiguration für die Enumeration von Benennungseigenschaften. |
|EnumerateSubnames |string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für die Enumeration von Benennungs-URIs. |
|FileContent |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Imagespeicherclient-Dateiübertragung (außerhalb des Clusters). |
|FileDownload |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Imagespeicherclient-Dateidownloadinitiierung (außerhalb des Clusters). |
|FinishInfrastructureTask |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Beenden von Infrastrukturaufgaben. |
|GetChaosReport | string, Standardwert „Admin\|\|User“ |Dynamisch| Ruft den Status des Chaos innerhalb eines angegebenen Zeitbereichs ab. |
|GetClusterConfiguration | string, Standardwert „Admin\|\|User“ | Dynamisch|Löst GetClusterConfiguration auf einer Partition aus. |
|GetClusterConfigurationUpgradeStatus | string, Standardwert „Admin\|\|User“ |Dynamisch| Löst GetClusterConfigurationUpgradeStatus auf einer Partition aus. |
|GetFabricUpgradeStatus |string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für das Abrufen des Clusterupgradestatus. |
|GetFolderSize |string, Standardwert „Admin“ |Dynamisch|Sicherheitskonfiguration für FileStoreService zum Abrufen der Ordnergröße |
|GetNodeDeactivationStatus |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Überprüfen des Deaktivierungstatus. |
|GetNodeTransitionProgress | string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für das Abrufen des Status eines Knotenübergangsbefehls. |
|GetPartitionDataLossProgress | string, Standardwert „Admin\|\|User“ | Dynamisch|Ruft den Status für einen API-Aufruf zum Auslösen von Datenverlusten ab. |
|GetPartitionQuorumLossProgress | string, Standardwert „Admin\|\|User“ |Dynamisch| Ruft den Status für einen API-Aufruf zum Auslösen von Quorumverlusten ab. |
|GetPartitionRestartProgress | string, Standardwert „Admin\|\|User“ |Dynamisch| Ruft den Status für einen API-Aufruf zum Neustarten einer Partition ab. |
|GetSecrets|string, Standardwert „Admin“|Dynamisch|Ruft Geheimniswerte ab |
|GetServiceDescription |string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für Dienstbenachrichtigungen mit langen Abrufzeiten und das Lesen von Dienstbeschreibungen. |
|GetStagingLocation |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für den Abruf des Imagespeicherclient-Stagingspeicherorts. |
|GetStoreLocation |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für den Abruf des Imagespeicherclient-Speicherorts. |
|GetUpgradeOrchestrationServiceState|string, Standardwert „Admin“| Dynamisch|Schließt GetUpgradeOrchestrationServiceState für eine Partition ein. |
|GetUpgradesPendingApproval |string, Standardwert „Admin“ |Dynamisch| Löst GetUpgradesPendingApproval auf einer Partition aus. |
|GetUpgradeStatus |string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für das Abrufen des Anwendungsupgradestatus. |
|InternalList |string, Standardwert „Admin“ | Dynamisch|Sicherheitskonfiguration für den Imagespeicherclient-Dateiauflistungsvorgang (intern). |
|InvokeContainerApi|Zeichenfolge, Standardwert „Admin“|Dynamisch|Ruft die Container-API auf |
|InvokeInfrastructureCommand |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für Befehle zum Verwalten von Infrastrukturaufgaben. |
|InvokeInfrastructureQuery |string, Standardwert „Admin\|\|User“ | Dynamisch|Sicherheitskonfiguration für das Abfragen von Infrastrukturaufgaben. |
|List |string, Standardwert „Admin\|\|User“ | Dynamisch|Sicherheitskonfiguration für den Imagespeicherclient-Dateiauflistungsvorgang. |
|MoveNextFabricUpgradeDomain |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Fortsetzen von Clusterupgrades mit einer expliziten Upgradedomäne. |
|MoveNextUpgradeDomain |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Fortsetzen von Anwendungsupgrades mit einer expliziten Upgradedomäne. |
|MoveReplicaControl |string, Standardwert „Admin“ | Dynamisch|Verschieben von Replikaten. |
|NameExists |string, Standardwert „Admin\|\|User“ | Dynamisch|Sicherheitskonfiguration für Überprüfungen auf das Vorhandensein von Benennungs-URIs. |
|NodeControl |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Starten, Beenden und Neustarten von Knoten. |
|NodeStateRemoved |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für Berichte zur Entfernung des Knotenstatus. |
|Pingen |string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für Clientpingvorgänge. |
|PredeployPackageToNode |string, Standardwert „Admin“ |Dynamisch| API vor der Bereitstellung. |
|PrefixResolveService |string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für die Dienstpräfixauflösung auf Konfliktbasis. |
|PropertyReadBatch |string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für Lesevorgänge in Benennungseigenschaften. |
|PropertyWriteBatch |string, Standardwert „Admin“ |Dynamisch|Sicherheitskonfigurationen für Schreibvorgänge in Benennungseigenschaften. |
|ProvisionApplicationType |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Anwendungstypbereitstellung. |
|ProvisionFabric |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die MSI- und/oder Clustermanifestbereitstellung. |
|Abfrage |string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für Abfragen. |
|RecoverPartition |string, Standardwert „Admin“ | Dynamisch|Sicherheitskonfiguration für die Wiederherstellung einer Partition. |
|RecoverPartitions |string, Standardwert „Admin“ | Dynamisch|Sicherheitskonfiguration für die Wiederherstellung von Partitionen. |
|RecoverServicePartitions |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Wiederherstellung von Dienstpartitionen. |
|RecoverSystemPartitions |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Wiederherstellung von Systemdienstpartitionen. |
|RemoveNodeDeactivations |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Zurücksetzen der Deaktivierung mehrerer Knoten. |
|ReportFabricUpgradeHealth |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Fortsetzen von Clusterupgrades mit der aktuellen Upgradedomäne. |
|ReportFault |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für Fehlerberichte. |
|ReportHealth |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für Integritätsberichte. |
|ReportUpgradeHealth |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Fortsetzen von Anwendungsupgrades der aktuellen Upgradedomäne. |
|ResetPartitionLoad |string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für das Zurücksetzen der failoverUnit-Auslastung. |
|ResolveNameOwner |string, Standardwert „Admin\|\|User“ | Dynamisch|Sicherheitskonfiguration für die Auflösung des Benennungs-URI-Besitzers. |
|ResolvePartition |string, Standardwert „Admin\|\|User“ | Dynamisch|Sicherheitskonfiguration für die Auflösung von Systemdiensten. |
|ResolveService |string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für die Dienstauflösung auf Konfliktbasis. |
|ResolveSystemService|string, Standardwert „Admin\|\|User“|Dynamisch| Sicherheitskonfiguration für die Auflösung von Systemdiensten. |
|RollbackApplicationUpgrade |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Rollback von Anwendungsupgrades. |
|RollbackFabricUpgrade |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Rollback von Clusterupgrades. |
|ServiceNotifications |string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für Dienstbenachrichtigungen auf Ereignisbasis. |
|GetUpgradeOrchestrationServiceState|string, Standardwert „Admin“| Dynamisch|Schließt SetUpgradeOrchestrationServiceState für eine Partition ein. |
|StartApprovedUpgrades |string, Standardwert „Admin“ |Dynamisch| Löst StartApprovedUpgrades auf einer Partition aus. |
|StartChaos |string, Standardwert „Admin“ |Dynamisch| Startet Chaos, wenn es noch nicht gestartet wurde. |
|StartClusterConfigurationUpgrade |string, Standardwert „Admin“ |Dynamisch| Löst StartClusterConfigurationUpgrade auf einer Partition aus. |
|StartInfrastructureTask |string, Standardwert „Admin“ | Dynamisch|Sicherheitskonfiguration für das Starten von Infrastrukturaufgaben. |
|StartNodeTransition |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Starten eines Knotenübergangs. |
|StartPartitionDataLoss |string, Standardwert „Admin“ |Dynamisch| Löst Datenverluste auf einer Partition aus. |
|StartPartitionQuorumLoss |string, Standardwert „Admin“ |Dynamisch| Löst Quorumverluste auf einer Partition aus. |
|StartPartitionRestart |string, Standardwert „Admin“ |Dynamisch| Startet einige oder alle Replikate einer Partition gleichzeitig neu. |
|StopChaos |string, Standardwert „Admin“ |Dynamisch| Beendet Chaos, falls es gestartet wurde. |
|ToggleVerboseServicePlacementHealthReporting | string, Standardwert „Admin\|\|User“ |Dynamisch| Sicherheitskonfiguration für Umschalten von ausführlichen Integritätsberichten zur Dienstplatzierung. |
|UnprovisionApplicationType |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Aufhebung der Anwendungstypbereitstellung. |
|UnprovisionFabric |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für die Aufhebung der MSI- und/oder Clustermanifestbereitstellung. |
|UnreliableTransportControl |string, Standardwert „Admin“ |Dynamisch| Unzuverlässiger Transport zum Hinzufügen und Entfernen von Verhaltensweisen. |
|UpdateService |string, Standardwert „Admin“ |Dynamisch|Sicherheitskonfiguration für Dienstupdates. |
|UpgradeApplication |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Starten oder Unterbrechen von Anwendungsupgrades. |
|UpgradeComposeDeployment|string, Standardwert „Admin“| Dynamisch|Führt ein Upgrade der Compose-Bereitstellung aus. |
|UpgradeFabric |string, Standardwert „Admin“ |Dynamisch| Sicherheitskonfiguration für das Starten von Clusterupgrades. |
|Upload |string, Standardwert „Admin“ | Dynamisch|Sicherheitskonfiguration für den Imagespeicherclient-Uploadvorgang. |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, Standardwert: None |Dynamisch|X509-Zertifikatspeicher des Zertifikatausstellers für Clientzertifikate; Name = clientIssuerCN; Wert = kommagetrennte Liste der Speicher |

## <a name="securityclientx509names"></a>Security/ClientX509Names

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, Standardwert None|Dynamisch|Dies ist eine Liste von Name-Wert-Paaren. Jeder Name ist der allgemeine Name des Antragstellers oder der DNS-Name von X.509-Zertifikaten, die für Clientvorgänge autorisiert sind. Für einen angegebenen Namen ist der Wert eine durch Kommas getrennte Liste von Zertifikatfingerabdrücken zum Anheften des Ausstellers. Außer bei einer fehlenden Angabe muss sich der direkte Aussteller der Clientzertifikate in der Liste befinden.|

## <a name="securityclustercertificateissuerstores"></a>Security/ClientCertificateIssuerStores

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, Standardwert: None |Dynamisch|X509-Zertifikatspeicher des Zertifikatausstellers für Clusterzertifikate; Name = clusterIssuerCN; Wert = kommagetrennte Liste der Speicher |

## <a name="securityclusterx509names"></a>Security/ClusterX509Names

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, Standardwert None|Dynamisch|Dies ist eine Liste von Name-Wert-Paaren. Jeder Name ist der allgemeine Name des Antragstellers oder der DNS-Name von X.509-Zertifikaten, die für Clustervorgänge autorisiert sind. Für einen angegebenen Namen ist der Wert eine durch Kommas getrennte Liste von Zertifikatfingerabdrücken zum Anheften des Ausstellers. Außer bei einer fehlenden Angabe muss sich der direkte Aussteller der Clusterzertifikate in der Liste befinden.|

## <a name="securityservercertificateissuerstores"></a>Security/ServerCertificateIssuerStores

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, Standardwert: None |Dynamisch|X509-Zertifikatspeicher des Zertifikatausstellers für Serverzertifikate; Name = serverIssuerCN; Wert = kommagetrennte Liste der Speicher |

## <a name="securityserverx509names"></a>Security/ServerX509Names

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, Standardwert None|Dynamisch|Dies ist eine Liste von Name-Wert-Paaren. Jeder Name ist der allgemeine Name des Antragstellers oder der DNS-Name von X.509-Zertifikaten, die für Servervorgänge autorisiert sind. Für einen angegebenen Namen ist der Wert eine durch Kommas getrennte Liste von Zertifikatfingerabdrücken zum Anheften des Ausstellers. Außer bei einer fehlenden Angabe muss sich der direkte Aussteller der Serverzertifikate in der Liste befinden.|

## <a name="setup"></a>Einrichten

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ContainerNetworkName|string, Standardwert ""| statischen |Der zu verwendende Netzwerkname, wenn ein Containernetzwerk eingerichtet wird.|
|ContainerNetworkSetup|Boolescher Wert, Standardwert FALSE (Linux), Standardwert TRUE (Windows)| statischen |Gibt an, ob ein Containernetzwerk eingerichtet werden soll.|
|FabricDataRoot |String | Nicht zulässig |Das Service Fabric-Datenstammverzeichnis. Standardwert für Azure ist „d:\svcfab“. |
|FabricLogRoot |String | Nicht zulässig |Das Service Fabric-Protokollstammverzeichnis. Hier werden SF-Protokolle und Ablaufverfolgungen platziert. |
|NodesToBeRemoved|string, Standardwert ""| Dynamisch |Der Knoten, die als Teil des Konfigurationsupgrades entfernt werden sollen. (Nur für eigenständige Bereitstellungen)|
|ServiceRunAsAccountName |String | Nicht zulässig |Der Kontoname, unter dem der Fabric-Hostdienst ausgeführt werden soll. |
|SkipContainerNetworkResetOnReboot|Boolesch, Standardwert FALSE|Nicht zulässig|Ob das Zurücksetzen des Containernetzwerks beim Neustart übersprungen werden soll.|
|SkipFirewallConfiguration |Boolesch, Standardwert „false“ | Nicht zulässig |Gibt an, ob Firewalleinstellungen vom System festgelegt werden müssen oder nicht. Dies gilt nur, wenn Sie die Windows-Firewall verwenden. Wenn Sie Firewalls von Drittanbietern verwenden, müssen Sie die Ports für das System und die zu verwendenden Anwendungen öffnen. |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|Anbieter |string, Standardwert „DSTS“ |statischen|Durch Trennzeichen getrennte Liste von zu aktivierenden Tokenvalidierungsanbietern (gültige Anbieter sind: DSTS; AAD). Derzeit kann immer nur ein einzelnen Anbieter aktiviert werden. |

## <a name="traceetw"></a>Trace/Etw

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|Ebene |Ganze Zahl, Standardwert 4 | Dynamisch |Die Trace/Etw-Ebene kann die Werte 1, 2, 3 oder 4 annehmen. Zur Unterstützung muss die Trace-Ebene 4 beibehalten werden. |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Zeit in Sekunden, Standardwert 0,015 | statischen | Geben Sie die Zeitspanne in Sekunden an. Bestimmt die Zeitdauer, die der Replikator nach dem Empfang eines Vorgangs wartet, bevor er eine Bestätigung sendet. Für andere Vorgänge, die während dieses Zeitraums empfangen werden, werden die Bestätigungen in einer einzelnen Nachricht zurückgesendet. Dadurch wird der Netzwerkverkehr verringert, jedoch möglicherweise auch der Durchsatz des Replikators. |
|MaxCopyQueueSize |Uint, Standardwert 16384 | statischen |Dies ist der maximale Wert. Er definiert die Anfangsgröße für die Warteschlange, die Replikationsvorgänge verwaltet. Beachten Sie, dass der Wert eine Potenz von 2 sein muss. Wenn während der Laufzeit die Warteschlange auf diese Größe anwächst, werden Vorgänge zwischen den primären und sekundären Replikatoren gedrosselt. |
|MaxPrimaryReplicationQueueMemorySize |Uint, Standardwert 0 | statischen |Dies ist der maximale Wert für die primäre Replikationswarteschlange in Bytes. |
|MaxPrimaryReplicationQueueSize |Uint, Standardwert 8192 | statischen |Dies ist die maximale Anzahl von Vorgängen, die in der primären Replikationswarteschlange vorhanden sein können. Beachten Sie, dass der Wert eine Potenz von 2 sein muss. |
|MaxReplicationMessageSize |Uint, Standardwert 52428800 | statischen | Maximale Nachrichtengröße für Replikationsvorgänge. Der Standardwert ist 50 MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, Standardwert 0 | statischen |Dies ist der maximale Wert für die sekundäre Replikationswarteschlange in Bytes. |
|MaxSecondaryReplicationQueueSize |Uint, Standardwert 16384 | statischen |Dies ist die maximale Anzahl von Vorgängen, die in der sekundären Replikationswarteschlange vorhanden sein können. Beachten Sie, dass der Wert eine Potenz von 2 sein muss. |
|ReplicatorAddress |string, Standardwert „localhost:0“ | statischen | Der Endpunkt in Form einer Zeichenfolge – „IP:Port“. Wird vom Windows Fabric-Replikator verwendet, um Verbindungen mit anderen Replikaten herzustellen, um Vorgänge zu senden/zu empfangen. |

## <a name="transport"></a>Transport
| **Parameter** | **Zulässige Werte** |**Upgraderichtlinie** |**Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(60)|statischen|Geben Sie die Zeitspanne in Sekunden an. Timeout für das Einrichten der Verbindung auf der eingehenden und annehmenden Seite (einschließlich der Sicherheitsaushandlung im sicheren Modus). |
|FrameHeaderErrorCheckingEnabled|Boolesch, Standardwert TRUE|statischen|Standardeinstellung für die Fehlerüberprüfung im Frameheader im ungesicherten Modus; die Komponenteneinstellung überschreibt dies. |
|MessageErrorCheckingEnabled|Boolesch, Standardwert „true“|statischen|Standardeinstellung für die Fehlerüberprüfung im Nachrichtenkopf und -text im ungesicherten Modus; die Komponenteneinstellung überschreibt dies. |
|ResolveOption|Zeichenfolge, Standardwert „unspecified“|statischen|Bestimmt, wie der FQDN aufgelöst wird.  Gültige Werte sind „unspecified/ipv4/ipv6“. |
|SendTimeout|TimeSpan, Standardwert Common::TimeSpan::FromSeconds(300)|Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Zeitüberschreitung beim Senden wegen unterbrochener Verbindung. TCP-Fehlerberichte sind in manchen Umgebungen nicht zuverlässig. Dies muss ggf. entsprechend der verfügbaren Netzwerkbandbreite und Größe der ausgehenden Daten angepasst werden (\*MaxMessageSize\/\*SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Boolesch, Standardwert „true“ |statischen| Automatische Abruf- und Aktualisierungsaktion basierend auf einer Datei für den Zielzustand. |
|AutoupgradeInstallEnabled|Boolesch, Standardwert ist „false“|statischen|Automatische Aktion zum Abrufen, Bereitstellen und Installieren von Codeupgrades basierend auf einer Datei mit Zielzustand.|
|GoalStateExpirationReminderInDays|Ganze Zahl, Standardwert ist 30|statischen|Legt die Anzahl von verbleibenden Tagen fest, nach denen eine Erinnerung zum Zielzustand angezeigt werden soll.|
|MinReplicaSetSize |Ganze Zahl, Standardwert 0 |statischen |MinReplicaSetSize für UpgradeOrchestrationService.|
|PlacementConstraints | string, Standardwert "" |statischen| PlacementConstraints für UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Zeit in Sekunden, Standardwert MaxValue |statischen| Geben Sie die Zeitspanne in Sekunden an. QuorumLossWaitDuration für UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Zeit in Sekunden, Standardwert 60 Minuten|statischen| Geben Sie die Zeitspanne in Sekunden an. ReplicaRestartWaitDuration für UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Zeit in Sekunden, Standardwert 60 *24* 7 Minuten |statischen| Geben Sie die Zeitspanne in Sekunden an. StandByReplicaKeepDuration für UpgradeOrchestrationService. |
|TargetReplicaSetSize |Ganze Zahl, Standardwert 0 |statischen |TargetReplicaSetSize für UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Boolesch, Standardwert „false“ | statischen|Einstellung, damit bei der Codeaktualisierung die Genehmigung durch den Administrator erforderlich ist, bevor fortgefahren werden kann. |

## <a name="upgradeservice"></a>UpgradeService

| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|BaseUrl | string, Standardwert "" |statischen|BaseUrl für UpgradeService. |
|ClusterId | string, Standardwert "" |statischen|ClusterId für UpgradeService. |
|CoordinatorType | string, Standardwert „WUTest“|Nicht zulässig|CoordinatorType für UpgradeService. |
|MinReplicaSetSize | Ganze Zahl, Standardwert 2 |Nicht zulässig| MinReplicaSetSize für UpgradeService. |
|OnlyBaseUpgrade | Boolesch, Standardwert „false“ |Dynamisch|OnlyBaseUpgrade für UpgradeService. |
|PlacementConstraints |string, Standardwert "" |Nicht zulässig|PlacementConstraints für den Aktualisierungsdienst. |
|PollIntervalInSeconds|Timespan, Standardwert ist Common::TimeSpan::FromSeconds(60) |Dynamisch|Geben Sie die Zeitspanne in Sekunden an. Das Intervall zwischen UpgradeService-Abrufen für ARM-Verwaltungsvorgänge. |
|TargetReplicaSetSize | Ganze Zahl, Standardwert 3 |Nicht zulässig| TargetReplicaSetSize für UpgradeService. |
|TestCabFolder | string, Standardwert "" |statischen| TestCabFolder für UpgradeService. |
|X509FindType | string, Standardwert ""|Dynamisch| X509FindType für UpgradeService. |
|X509FindValue | string, Standardwert "" |Dynamisch| X509FindValue für UpgradeService. |
|X509SecondaryFindValue | string, Standardwert "" |Dynamisch| X509SecondaryFindValue für UpgradeService. |
|X509StoreLocation | string, Standardwert "" |Dynamisch| X509StoreLocation für UpgradeService. |
|X509StoreName | string, Standardwert „My“|Dynamisch|X509StoreName für UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities
| **Parameter** | **Zulässige Werte** | **Upgraderichtlinie** | **Anleitung oder Kurzbeschreibung** |
| --- | --- | --- | --- |
|PropertyGroup| UserServiceMetricCapacitiesMap, Standardwert ist „None“ | statischen | Eine Sammlung von Ressourcenkontrollen-Grenzwerten für Benutzerdienste. Sie muss statisch sein, da sie sich auf die Logik der automatischen Erkennung (AutoDetection) auswirkt. |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Aktualisieren der Konfiguration eines Azure-Clusters](service-fabric-cluster-config-upgrade-azure.md) und [Aktualisieren der Konfiguration eines eigenständigen Clusters](service-fabric-cluster-config-upgrade-windows-server.md).
