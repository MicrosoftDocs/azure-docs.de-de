---
title: Abfragen von Protokollen aus Azure Monitor für VMs
description: Die Lösung „Azure Monitor für VMs“ erfasst Metriken und Protokolldaten. In diesem Artikel werden die Datensätze und einige Beispielabfragen beschrieben.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 118bdcb6929abfc162ff05e91f1621f087b6c50c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186727"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Abfragen von Protokollen aus Azure Monitor für VMs

Azure Monitor für VMs erfasst Leistungs- und Verbindungsmetriken, Inventurdaten von Computern und Prozessen sowie Informationen zum Integritätsstatus und leitet diese an den Log Analytics-Arbeitsbereich in Azure Monitor weiter.  Diese Daten stehen in Azure Monitor für [Abfragen](../log-query/log-query-overview.md) zur Verfügung. Diese Daten können in verschiedenen Szenarios von Nutzen sein, z.B. bei der Migrationsplanung, Kapazitätsanalyse, Ermittlung und Ad-hoc-Behebung von Leistungsproblemen.

## <a name="map-records"></a>Zuordnung von Datensätzen

Zusätzlich zu den Datensätzen, die beim Starten eines Prozesses oder Computers oder beim Onboarding beim Zuordnungsfeature von Azure Monitor for VMs generiert werden, wird pro Stunde ein Datensatz für jeden eindeutigen Computer und jeden eindeutigen Prozess generiert. Die Eigenschaften der Datensätze sind in den folgenden Tabellen aufgeführt. Die Felder und Werte in den ServiceMapComputer_CL-Ereignissen sind Feldern der Computerressource in der ServiceMap ARM-API (Azure Resource Manager) zugeordnet. Die Felder und Werte in den ServiceMapProcess_CL-Ereignissen sind Feldern der Prozessressource in der ServiceMap ARM-API zugeordnet. Das Feld „ResourceName_s“ entspricht dem Namensfeld in der entsprechenden ARM-Ressource. 

Es gibt intern generierte Eigenschaften, mit denen Sie eindeutige Prozessen und Computer identifizieren können:

- Computer: Verwenden Sie *ResourceId* oder *ResourceName_s* zur eindeutigen Identifizierung eines Computers in einem Log Analytics-Arbeitsbereich.
- Prozess: Verwenden Sie *ResourceId* zur eindeutigen Identifizierung eines Prozesses in einem Log Analytics-Arbeitsbereich. *ResourceName_s* ist innerhalb des Kontexts des Computers, auf dem der Prozess ausgeführt wird (MachineResourceName_s), eindeutig. 

Da für einen angegebenen Prozess und Computer in einem angegebenen Zeitraum möglicherweise mehrere Datensätze vorhanden sind, können Abfragen mehrere Datensätze für denselben Computer oder Prozess zurückgeben. Wenn nur der aktuelle Datensatz zurückgegeben werden soll, fügen Sie der Abfrage `| summarize arg_max(TimeGenerated, *) by ResourceId` hinzu.

### <a name="connections-and-ports"></a>Verbindungen und Ports

Mit dem Feature „Verbindungsmetriken“ werden zwei neue Tabellen in Azure Monitor-Protokolle eingeführt: VMConnection und VMBoundPort. Diese Tabellen enthalten Informationen zu den Verbindungen eines Computers (eingehend und ausgehend) und den Serverports, die geöffnet bzw. aktiv sind. Verbindungsmetriken werden ebenfalls über APIs zur Verfügung gestellt, die die Möglichkeit zum Abrufen einer bestimmten Metrik während eines Zeitfensters bereitstellen. TCP-Verbindungen, die durch *Akzeptieren* auf einem lauschenden Socket hergestellt werden, sind eingehend, während solche, die durch *Herstellen einer Verbindung* mit einer bestimmten IP- und Portkombination hergestellt werden, ausgehend sind. Die Richtung einer Verbindung wird durch die Direction-Eigenschaft dargestellt, die auf entweder **inbound** oder **outbound** festgelegt werden kann. 

Die in diesen Tabellen dargestellten Daten wurden aus Daten generiert, die vom Dependency-Agent gemeldet wurden. Jeder Datensatz stellt eine Beobachtung über einen Zeitintervall von einer Minute dar. Die TimeGenerated-Eigenschaft gibt den Anfang des Zeitintervalls an. Jeder Datensatz enthält Informationen zum Identifizieren der jeweiligen Entität – d.h. Verbindung oder Port – sowie der dieser Entität zugeordneten Metriken. Derzeit werden nur Netzwerkaktivitäten gemeldet, die über TCP-over-IPv4 anfallen. 

#### <a name="common-fields-and-conventions"></a>Gemeinsame Felder und Konventionen 

Die folgenden Felder und Konventionen gelten sowohl für VMConnection als auch für VMBoundPort: 

- Computer: Vollqualifizierter Domänenname des berichtenden Computers 
- AgentId: Eindeutiger Bezeichner für einen Computer mit dem Log Analytics-Agent  
- Machine: Name der Azure Resource Manager-Ressource für den Computer, die von ServiceMap bereitgestellt wird. Das Format lautet *m-{GUID}* , wobei *GUID* der gleichen GUID wie AgentId entspricht.  
- Prozess: Name der Azure Resource Manager-Ressource für den Prozess, die von ServiceMap bereitgestellt wird. Das Format lautet *p-{Hexadezimalzeichenfolge}* . Das Feld „Process“ ist innerhalb eines Computerbereichs eindeutig. Zum Erstellen einer eindeutigen Prozess-ID für mehrere Computer können Sie die Felder „Machine“ und „Process“ kombinieren. 
- ProcessName: Name der ausführbaren Datei für den Berichtsprozess
- Alle IP-Adressen sind Zeichenfolgen im kanonischen IPv4-Format, z. B. *13.107.3.160* 

Um Kosten und Komplexität im Zaum zu halten, stellen die Verbindungsdatensätze keine einzelnen physischen Netzwerkverbindungen dar. Mehrere physische Netzwerkverbindungen werden in einer logischen Verbindung gruppiert, die dann in der entsprechenden Tabelle wiedergegeben wird.  Das heißt, dass die Datensätze in der Tabelle *VMConnection* eine logische Gruppierung anstelle der beobachteten einzelnen physischen Verbindungen darstellen. Physische Netzwerkverbindungen, die während eines bestimmten einminütigen Intervalls den gleichen Wert für die folgenden Attribute aufweisen, werden in *VMConnection* zu einem einzelnen logischen Datensatz zusammengefasst. 

| Eigenschaft | BESCHREIBUNG |
|:--|:--|
|Direction |Richtung der Verbindung, der Wert ist *inbound* oder *outbound* |
|Machine |Der vollqualifizierte Domänenname des Computers |
|Prozess |Identität des Prozesses oder Gruppe von Prozessen, die die Verbindung einleitet/akzeptiert |
|SourceIp |IP-Adresse der Quelle |
|DestinationIp |IP-Adresse des Ziels |
|DestinationPort |Portnummer des Ziels |
|Protocol |Für die Verbindung verwendetes Protokoll.  Der Wert ist *tcp*. |

Um dem Einfluss der Gruppierung Rechnung zu tragen, werden Informationen über die Anzahl der gruppierten physischen Verbindungen in den folgenden Eigenschaften des Datensatzes bereitgestellt:

| Eigenschaft | BESCHREIBUNG |
|:--|:--|
|LinksEstablished |Die Anzahl der physischen Netzwerkverbindungen, die während des Berichtszeitraums eingerichtet wurden |
|LinksTerminated |Die Anzahl der physischen Netzwerkverbindungen, die während des Berichtszeitraums beendet wurden |
|LinksFailed |Die Anzahl der physischen Netzwerkverbindungen, die während des Berichtszeitraums für ungültig erklärt wurden. Diese Informationen sind derzeit nur für ausgehende Verbindungen verfügbar. |
|LinksLive |Die Anzahl der physischen Netzwerkverbindungen, die am Ende des Berichtszeitraums offen waren|

#### <a name="metrics"></a>Metriken

Über Metriken zur Verbindungsanzahl hinaus sind in den folgenden Eigenschaften des Datensatzes auch Informationen über das Volumen der gesendeten und empfangenen Daten für eine bestimmte logische Verbindung oder einen bestimmten Netzwerkport enthalten:

| Eigenschaft | BESCHREIBUNG |
|:--|:--|
|BytesSent |Gesamtzahl der Bytes, die während des Berichtszeitraums gesendet wurden |
|BytesReceived |Gesamtzahl der Bytes, die während des Berichtszeitraums empfangen wurden |
|Antworten |Die Anzahl der Antworten, die während des Berichtszeitraums beobachtet wurden. 
|ResponseTimeMax |Die längste Antwortzeit (in Millisekunden), die während des Berichtszeitraums beobachtet wurde. Wenn kein Wert vorliegt, ist die Eigenschaft leer.|
|ResponseTimeMin |Die kürzeste Antwortzeit (in Millisekunden), die während des Berichtszeitraums beobachtet wurde. Wenn kein Wert vorliegt, ist die Eigenschaft leer.|
|ResponseTimeSum |Die Summe aller Antwortzeiten (in Millisekunden), die während des Berichtszeitraums beobachtet wurden. Wenn kein Wert vorliegt, ist die Eigenschaft leer.|

Die dritte Art Daten, die gemeldet werden, sind Antwortzeiten – wie viel Zeit verbringt ein Aufrufer mit Warten auf die Verarbeitung und Antwort durch den Remoteendpunkt für eine über eine Verbindung gesendete Anforderung. Die gemeldete Antwortzeit ist eine Schätzung der tatsächlichen Antwortzeit des zugrundeliegenden Anwendungsprotokolls. Sie wird mit heuristischen Verfahren auf der Grundlage der Beobachtung des Datenflusses zwischen dem Quell- und dem Zielende einer physischen Netzwerkverbindung berechnet. Konzeptionell handelt es sich um die Differenz zwischen dem Zeitpunkt, zu dem das letzte Byte einer Anforderung den Sender verlässt, und dem Zeitpunkt, zu dem das letzte Byte der Antwort wieder bei ihm eintrifft. Diese beiden Zeitstempel werden verwendet, um Anforderungs- und Antwortereignisse für eine bestehende physische Verbindung darzustellen. Die Differenz zwischen ihnen stellt die Antwortzeit für eine einzelne Anforderung dar. 

In dieser ersten Version dieses Features stellen unsere Algorithmen eine Annäherung dar, die abhängig von dem für eine bestimmte Netzwerkverbindung tatsächlich verwendeten Anwendungsprotokoll mehr oder weniger erfolgreich funktionieren kann. Beispielsweise funktioniert der aktuelle Ansatz gut bei Anforderungs-/Antwort-basierten Protokollen wie HTTP(S), er funktioniert aber nicht bei unidirektionalen oder auf Nachrichtenwarteschlangen basierenden Protokollen.

Einige wichtige Punkte sind zu beachten:

1. Wenn ein Prozess Verbindungen an der gleichen IP-Adresse, aber über mehrere Netzwerkschnittstellen annimmt, wird für jede Schnittstelle ein separater Datensatz gemeldet. 
2. Datensätze mit Platzhalter-IP-Adresse enthalten keine Aktivitäten. Sie werden aufgenommen, um die Tatsache darzustellen, dass ein Port auf dem Computer für eingehenden Datenverkehr offen ist.
3. Um Ausführlichkeit und Datenvolumen zu verringern, werden Datensätze mit Platzhalter-IP weggelassen, wenn es einen übereinstimmenden Datensatz (für den gleichen Prozess, Port und das gleiche Protokoll) mit einer bestimmten IP-Adresse gibt. Wenn ein Datensatz mit Platzhalter-IP weggelassen wird, wird die Datensatzeigenschaft „IsWildcardBind“ mit der bestimmten IP-Adresse auf „True“ festgelegt, um anzuzeigen, dass der Port über jede Schnittstelle des berichtenden Computers geöffnet ist.
4. Für Ports, die nur an eine bestimmte Schnittstelle gebunden sind, ist „IsWildcardBind“ auf *FALSE* festgelegt.

#### <a name="naming-and-classification"></a>Benennung und Klassifizierung

Der Einfachheit halber ist die IP-Adresse des Remoteendes einer Verbindung in der Eigenschaft „RemoteIp“ enthalten. Für eingehende Verbindungen ist „RemoteIp“ identisch mit „SourceIp“, während sie für ausgehende Verbindungen mit „DestinationIp“ übereinstimmt. Die Eigenschaft „RemoteDnsCanonicalNames“ stellt die kanonischen DNS-Namen dar, die vom Computer für „RemoteIp“ gemeldet werden. Die Eigenschaften „RemoteDnsQuestions“ und „RemoteClassification“ sind zur zukünftigen Verwendung reserviert. 

#### <a name="geolocation"></a>Geolocation

*VMConnection* enthält außerdem in den folgenden Eigenschaften des Datensatzes Geolocationinformationen für das Remoteende jeder Verbindung: 

| Eigenschaft | BESCHREIBUNG |
|:--|:--|
|RemoteCountry |Der Name des Landes oder der Region, in dem bzw. der „RemoteIp“ gehostet ist.  Beispielsweise *USA* |
|RemoteLatitude |Der Breitengrad der Geolocation. Beispielsweise *47,68* |
|RemoteLongitude |Der Längengrad der Geolocation. Beispielsweise *-122,12* |

#### <a name="malicious-ip"></a>Schädliche IP-Adressen

Jede RemoteIp-Eigenschaft in der Tabelle *VMConnection* wird anhand einer Sammlung von IPs überprüft, die für schädliche Aktivitäten bekannt sind. Wenn die RemoteIp als bösartig identifiziert wurde, werden die folgenden Eigenschaften des Datensatzes aufgefüllt (sie sind leer, wenn die IP nicht als schädlich angesehen wird):

| Eigenschaft | BESCHREIBUNG |
|:--|:--|
|MaliciousIp |Die RemoteIp-Adresse |
|IndicatorThreadType |„Bedrohungsindikator erkannt“ kann einen der folgenden Werte haben: *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos* , *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
|BESCHREIBUNG |Beschreibung der beobachteten Bedrohung. |
|TLPLevel |TLP-Stufe (Ampelprotokoll) ist einer der definierten Werte *White*, *Green*, *Amber*, *Red*. |
|Confidence |Werte sind *0–100*. |
|severity |Werte sind *0–5*, wobei *5* am schwerwiegendsten und *0* überhaupt nicht schwerwiegend ist. Der Standardwert ist *3*.  |
|FirstReportedDateTime |Die Uhrzeit, zu der der Anbieter den Indikator zum ersten Mal gemeldet hat. |
|LastReportedDateTime |Die Uhrzeit, zu der der Indikator zum letzten Mal von Interflow beobachtet wurde. |
|IsActive |Gibt an, dass die Indikatoren deaktiviert sind, mit dem Wert *true* oder *false*. |
|ReportReferenceLink |Links zu Berichten, die im Zusammenhang mit einer bestimmten Beobachtung stehen. |
|AdditionalInformation |Bietet zusätzliche Informationen, falls zutreffend, zur beobachteten Bedrohung. |

### <a name="ports"></a>Ports 

Ports auf einem Computer, die aktiv eingehenden Datenverkehr akzeptieren oder Datenverkehr akzeptieren können und sich während des Berichtszeitraums im Leerlauf befinden, werden in die Tabelle „VMBoundPort“ eingetragen.  

Jeder Datensatz in der Tabelle „VMBoundPort“ wird mit den folgenden Feldern definiert: 

| Eigenschaft | BESCHREIBUNG |
|:--|:--|
|Prozess | Die Identität eines Prozesses (oder von Gruppen von Prozessen) dem der Port zugeordnet ist|
|IP | Die IP-Adresse des Ports (dies kann eine Platzhalter-IP-Adresse sein, z. B. *0.0.0.0*) |
|Port |Die Portnummer |
|Protocol | Das Protokoll,  z. B. *TCP* oder *UDP* (derzeit wird nur *TCP* unterstützt)|
 
Die Identität eines Ports ergibt aus den obigen fünf Feldern und wird in der Eigenschaft „PortId“ gespeichert. Diese Eigenschaft kann dazu verwendet werden, Datensätze für einen bestimmten Port für einen Zeitraum schnell zu finden. 

#### <a name="metrics"></a>Metriken 

Portdatensätze umfassen Metriken, die die zugeordneten Verbindungen darstellen. Derzeit werden die folgenden Metriken gemeldet (die Details zu den einzelnen Metriken finden Sie im vorherigen Abschnitt): 

- BytesSent und BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Einige wichtige Punkte sind zu beachten:

- Wenn ein Prozess Verbindungen an der gleichen IP-Adresse, aber über mehrere Netzwerkschnittstellen annimmt, wird für jede Schnittstelle ein separater Datensatz gemeldet.  
- Datensätze mit Platzhalter-IP-Adresse enthalten keine Aktivitäten. Sie werden aufgenommen, um die Tatsache darzustellen, dass ein Port auf dem Computer für eingehenden Datenverkehr offen ist. 
- Um Ausführlichkeit und Datenvolumen zu verringern, werden Datensätze mit Platzhalter-IP weggelassen, wenn es einen übereinstimmenden Datensatz (für den gleichen Prozess, Port und das gleiche Protokoll) mit einer bestimmten IP-Adresse gibt. Wenn ein Datensatz mit einer Platzhalter-IP ausgelassen wird, wird die Eigenschaft *IsWildcardBind* für den Datensatz mit der spezifischen IP-Adresse auf *TRUE* festgelegt.  Dies gibt an, dass der Port über jede Schnittstelle des berichtenden Computers zur Verfügung gestellt wird. 
- Für Ports, die nur an eine bestimmte Schnittstelle gebunden sind, ist „IsWildcardBind“ auf *FALSE* festgelegt. 

### <a name="vmcomputer-records"></a>VMComputer-Datensätze

Datensätze des Typs *VMComputer* enthalten Bestandsdaten für Server mit dem Dependency-Agent. Die Eigenschaften der Datensätze sind in der folgenden Tabelle aufgeführt:

| Eigenschaft | BESCHREIBUNG |
|:--|:--|
|TenantId | Der eindeutige Bezeichner des Arbeitsbereichs |
|SourceSystem | *Erkenntnisse* | 
|TimeGenerated | Zeitstempel des Datensatzes (UTC) |
|Computer | Der vollqualifizierte Domänenname des Computers | 
|AgentId | Die eindeutige ID des Log Analytics-Agenten |
|Machine | Name der Azure Resource Manager-Ressource für den Computer, die von ServiceMap bereitgestellt wird. Das Format lautet *m-{GUID}* , wobei *GUID* der gleichen GUID wie AgentId entspricht. | 
|DisplayName | `Display name` | 
|FullDisplayName | Vollständiger Anzeigename | 
|HostName | Der Name des Computers ohne Domänennamen |
|BootTime | Startzeit des Computers (UTC) |
|TimeZone | Die normalisierte Zeitzone |
|VirtualizationState | *virtual*, *hypervisor*, *physical* |
|Ipv4Addresses | Array von IPv4-Adressen | 
|Ipv4SubnetMasks | Array von IPv4-Subnetzmasken (in derselben Reihenfolge wie Ipv4Addresses). |
|Ipv4DefaultGateways | Array von IPv4-Gateways | 
|Ipv6Addresses | Array von IPv6-Adressen | 
|MacAddresses | Array von MAC-Adressen | 
|DnsNames | Array von DNS-Namen, die dem Computer zugeordnet sind |
|DependencyAgentVersion | Die Version des Dependency-Agents, der auf dem Computer ausgeführt wird | 
|OperatingSystemFamily | *Linux*, *Windows* |
|OperatingSystemFullName | Der vollständige Name des Betriebssystems | 
|PhysicalMemoryMB | Der physischer Speicher in Megabyte | 
|Cpus | Die Anzahl der Prozessoren | 
|CpuSpeed | Die CPU-Geschwindigkeit in MHz | 
|VirtualMachineType | *hyperv*, *vmware*, *xen* |
|VirtualMachineNativeId | Die VM-ID, die vom entsprechenden Hypervisor zugewiesen wurde | 
|VirtualMachineNativeName | Der Name der VM |
|VirtualMachineHypervisorId | Der eindeutige Bezeichner des Hypervisors, der den virtuellen Computer hostet |
|HypervisorType | *hyperv* |
|HypervisorId | Die eindeutige ID des Hypervisors | 
|HostingProvider | *azure* |
|_ResourceId | Der eindeutige Bezeichner für eine Azure-Ressource |
|AzureSubscriptionId | Der Globally Unique Identifier, der Ihr Abonnement identifiziert | 
|AzureResourceGroup | Der Name der Azure-Ressourcengruppe, deren Mitglied der Computer ist |
|AzureResourceName | Der Name der Azure-Ressource |
|AzureLocation | Der Speicherort der Azure-Ressource |
|AzureUpdateDomain | Der Name der Azure-Updatedomäne |
|AzureFaultDomain | Der Name der Azure-Fehlerdomäne |
|AzureVmId | Der eindeutige Bezeichner des virtuellen Azure-Computers |
|AzureSize | Die Größe der Azure-VM. |
|AzureImagePublisher | Name des Verlegers des virtuellen Azure-Computers |
|AzureImageOffering | Name des Angebotstyps des virtuellen Azure-Computers | 
|AzureImageSku | Die SKU des Azure-VM-Images | 
|AzureImageVersion | Die Version des Azure-VM-Images | 
|AzureCloudServiceName | Der Name des Azure-Clouddiensts |
|AzureCloudServiceDeployment | Bereitstellungs-ID für den Clouddienst |
|AzureCloudServiceRoleName | Name der Clouddienstrolle |
|AzureCloudServiceRoleType | Clouddienst-Rollentyp: *Worker* oder *Web* |
|AzureCloudServiceInstanceId | Rolleninstanz-ID des Clouddiensts |
|AzureVmScaleSetName | Der Name der VM-Skalierungsgruppe |
|AzureVmScaleSetDeployment | ID für Bereitstellung einer VM-Skalierungsgruppe |
|AzureVmScaleSetResourceId | Der eindeutige Bezeichner der Ressourcen der VM-Skalierungsgruppe|
|AzureVmScaleSetInstanceId | Der eindeutige Bezeichner der VM-Skalierungsgruppe |
|AzureServiceFabricClusterId | Der eindeutige Bezeichner des Azure Service Fabric-Clusters | 
|AzureServiceFabricClusterName | Der Name des Azure Service Fabric-Clusters |

### <a name="vmprocess-records"></a>VMProcess-Datensätze

Datensätze des Typs *VMProcess* enthalten Bestandsdaten für über TCP verbundene Prozesse auf Servern mit dem Dependency-Agent. Die Eigenschaften der Datensätze sind in der folgenden Tabelle aufgeführt:

| Eigenschaft | BESCHREIBUNG |
|:--|:--|
|TenantId | Der eindeutige Bezeichner des Arbeitsbereichs |
|SourceSystem | *Erkenntnisse* | 
|TimeGenerated | Zeitstempel des Datensatzes (UTC) |
|Computer | Der vollqualifizierte Domänenname des Computers | 
|AgentId | Die eindeutige ID des Log Analytics-Agenten |
|Machine | Name der Azure Resource Manager-Ressource für den Computer, die von ServiceMap bereitgestellt wird. Das Format lautet *m-{GUID}* , wobei *GUID* der gleichen GUID wie AgentId entspricht. | 
|Prozess | Der eindeutige Bezeichner des Dienstzuordnungsprozesses. Dieser weist das Format *p-{GUID}* auf. 
|ExecutableName | Der Name der ausführbaren Prozessdatei | 
|DisplayName | Anzeigename des Prozesses |
|Role | Prozessrolle: *webserver*, *appServer*, *databaseServer*, *ldapServer*, *smbServer* |
|Group | Prozessgruppenname. Prozesse in derselben Gruppe sind logisch verwandt, z. B. Teil desselben Produkts oder derselben Systemkomponente. |
|StartTime | Die Startzeit des Prozesspools |
|FirstPid | Die erste PID im Prozesspool |
|BESCHREIBUNG | Die Beschreibung des Prozesses |
|CompanyName | Der Name des Unternehmens |
|InternalName | Der interne Name |
|ProductName | Der Name des Produkts |
|ProductVersion | Die Version des Produkts |
|FileVersion | Die Dateiversion |
|ExecutablePath |Der Pfad der ausführbaren Datei |
|CommandLine | Die Befehlszeile |
|WorkingDirectory | Das Arbeitsverzeichnis |
|Dienste | Ein Array an Diensten, unter dem der Prozess ausgeführt wird |
|UserName | Das Konto, unter dem der Prozess ausgeführt wird |
|UserDomain | Die Domäne, unter der der Prozess ausgeführt wird |
|_ResourceId | Der eindeutige Bezeichner für den Prozess innerhalb des Arbeitsbereichs |


## <a name="sample-map-queries"></a>Beispielzuordnungsabfragen

### <a name="list-all-known-machines"></a>Auflisten aller bekannten Computer

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Letzter Neustart des virtuellen Computers

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Zusammenfassung von virtuellen Azure-Computern nach Image, Speicherort und SKU

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Auflisten der physischen Arbeitsspeicherkapazität aller verwalteten Computer

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Auflisten von Computernamen, DNS, IP und Betriebssystem

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Suchen nach allen Prozesse mit "sql" in der Befehlszeile

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Suchen eines Computers (aktuellster Datensatz) anhand des Ressourcennamens

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Suchen eines Computers (aktuellster Datensatz) anhand der IP-Adresse

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Auflisten aller bekannten Prozesse auf einem angegebenen Computer auf

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Auflisten aller Computer, auf denen SQL Server ausgeführt wird

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Auflisten aller eindeutigen Produktversionen von „curl“ im eigenen Rechenzentrum

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Erstellen einer Computergruppe mit allen Computern, auf denen CentOS ausgeführt wird

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Gesendete Bytes und empfangene Trends

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Virtuelle Azure-Computer, die die meisten Bytes übertragen

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Trends zum Linkstatus

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Trend zu Verbindungsfehlern

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Gebundene Ports

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Anzahl der offenen Ports auf Computern

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Bewerten der Prozesse im Arbeitsbereich anhand ihrer offenen Ports

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Aggregieren des Verhaltens für jeden Port

Anschließend kann diese Abfrage verwendet werden, um Ports anhand der Aktivität zu bewerten, z. B. Ports mit dem meisten eingehenden/ausgehenden Datenverkehr oder Ports mit den meisten Verbindungen
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Zusammenfassen der ausgehenden Verbindungen für eine Gruppe von Computern

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="performance-records"></a>Leistungsdatensätze
Datensätze mit einem Typ von *InsightsMetrics* verfügen über Leistungsdaten vom Gastbetriebssystem der VM. Die Eigenschaften der Datensätze sind in der folgenden Tabelle aufgeführt:


| Eigenschaft | BESCHREIBUNG |
|:--|:--|
|TenantId | Dies ist der eindeutige Bezeichner des Arbeitsbereichs. |
|SourceSystem | *Erkenntnisse* | 
|TimeGenerated | Dies ist der Zeitpunkt, zu dem der Wert erfasst wurde (UTC). |
|Computer | Der vollqualifizierte Domänenname des Computers | 
|Origin | *vm.azm.ms* |
|Namespace | Dies ist die Kategorie des Leistungsindikators. | 
|Name | Name des Leistungsindikators. |
|Val | Dies ist der ermittelte Wert. | 
|`Tags` | Dies sind zugehörige Details zum Datensatz. In der folgenden Tabelle finden Sie Tags, die mit unterschiedlichen Datensatztypen verwendet werden.  |
|AgentId | Dies ist der eindeutige Bezeichner für die einzelnen Computer-Agents. |
|type | *InsightsMetrics* |
|_ResourceId_ | Dies ist die Ressourcen-ID der VM. |

In der folgenden Tabelle sind die Leistungsindikatoren aufgeführt, die zurzeit in der *InsightsMetrics*-Tabelle erfasst werden:

| Namespace | Name | BESCHREIBUNG | Einheit | `Tags` |
|:---|:---|:---|:---|:---|
| Computer    | Heartbeat             | Computertakt                        | | |
| Arbeitsspeicher      | AvailableMB           | Arbeitsspeicher: Verfügbare Bytes                    | Megabytes      | memorySizeMB: Gesamtspeichergröße|
| Netzwerk     | WriteBytesPerSecond   | Geschriebene Bytes pro Sekunde (Netzwerk)            | Bytes pro Sekunde | NetworkDeviceId: Geräte-ID<br>bytes: Anzahl der insgesamt gesendeten Bytes |
| Netzwerk     | ReadBytesPerSecond    | Gelesene Bytes pro Sekunde (Netzwerk)             | Bytes pro Sekunde | networkDeviceId: Geräte-ID<br>bytes: Insgesamt empfangene Bytes |
| Prozessor   | UtilizationPercentage | Prozentsatz der Prozessorauslastung          | Percent        | totalCpus: Gesamtanzahl der CPUs |
| Logischer Datenträger | WritesPerSecond       | Schreibvorgänge pro Sekunde (logischer Datenträger)            | Anzahl pro Sekunde | mountId: Einbindungs-ID des Geräts |
| Logischer Datenträger | WriteLatencyMs        | Schreibwartezeit in Millisekunden (logischer Datenträger)    | Millisekunden   | mountId: Einbindungs-ID des Geräts |
| Logischer Datenträger | WriteBytesPerSecond   | Geschriebene Bytes pro Sekunde (logischer Datenträger)       | Bytes pro Sekunde | mountId: Einbindungs-ID des Geräts |
| Logischer Datenträger | TransfersPerSecond    | Übertragungen pro Sekunde (logischer Datenträger)         | Anzahl pro Sekunde | mountId: Einbindungs-ID des Geräts |
| Logischer Datenträger | TransferLatencyMs     | Übertragungswartezeit in Millisekunden(logischer Datenträger) | Millisekunden   | mountId: Einbindungs-ID des Geräts |
| Logischer Datenträger | ReadsPerSecond        | Lesevorgänge pro Sekunde (logischer Datenträger)             | Anzahl pro Sekunde | mountId: Einbindungs-ID des Geräts |
| Logischer Datenträger | ReadLatencyMs         | Lesewartezeit in Millisekunden (logischer Datenträger)     | Millisekunden   | mountId: Einbindungs-ID des Geräts |
| Logischer Datenträger | ReadBytesPerSecond    | Gelesene Bytes pro Sekunde (logischer Datenträger)        | Bytes pro Sekunde | mountId: Einbindungs-ID des Geräts |
| Logischer Datenträger | FreeSpacePercentage   | Prozentsatz verfügbarer Speicherplatz (logischer Datenträger)        | Percent        | mountId: Einbindungs-ID des Geräts |
| Logischer Datenträger | FreeSpaceMB           | Verfügbarer Speicherplatz (logischer Datenträger)             | Megabytes      | mountId: Einbindungs-ID des Geräts<br>diskSizeMB: Gesamtgröße des Datenträgers |
| Logischer Datenträger | Bytes pro Sekunde        | Bytes pro Sekunde (logischer Datenträger)             | Bytes pro Sekunde | mountId: Einbindungs-ID des Geräts |


## <a name="next-steps"></a>Nächste Schritte

* Wenn Sie noch nicht mit dem Schreiben von Protokollabfragen vertraut sind, finden Sie Informationen zur Verwendung von Log Analytics im Azure-Portal zum Schreiben von Protokollabfragen unter [Erste Schritte mit Azure Monitor Log Analytics](../log-query/log-analytics-tutorial.md).

* Erfahren Sie mehr über das [Schreiben von Suchabfragen](../log-query/get-started-queries.md).