---
title: Log Analytics-Datensicherheit | Microsoft-Dokumentation
description: Erfahren Sie, wie Log Analytics Ihre Privatsphäre und Daten schützt.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/11/2020
ms.openlocfilehash: 87cdecd29d684c712853970c8246002132d274ac
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094334"
---
# <a name="log-analytics-data-security"></a>Log Analytics-Datensicherheit
Dieses Dokument dient der Bereitstellung von spezifischen Informationen zu Log Analytics, einem Feature von Azure Monitor, in Ergänzung der Informationen im [Azure Trust Center](https://www.microsoft.com/en-us/trust-center?rtc=1).  

In diesem Artikel wird erläutert, wie Daten von Log Analytics gesammelt, verarbeitet und geschützt werden. Sie können Agents verwenden, um die Verbindung mit dem Webdienst herzustellen, mit System Center Operations Manager betriebliche Daten sammeln oder Daten aus Azure-Diagnosen für die Verwendung durch Log Analytics abrufen. 

Der Log Analytics-Dienst verwaltet Ihre Daten in der Cloud sicher mithilfe der folgenden Methoden:

* Trennung von Daten
* Beibehaltung von Daten
* Physische Sicherheit
* Incident Management
* Compliance
* Sicherheitsstandard-Zertifizierungen

Sie können auch zusätzliche Sicherheitsfunktionen verwenden, die in Azure Monitor und Log Analytics integriert sind. Diese Funktionen erfordern mehr Administratorverwaltung. 
* Kundenseitig verwaltete Schlüssel (Sicherheitsschlüssel)
* Privater Azure-Speicher
* Private Link-Netzwerk 
* Von Azure Lockbox festgelegte Zugriffslimits für den Azure-Support

Sollten Sie Fragen, Vorschläge oder Probleme im Zusammenhang mit den folgenden Informationen (einschließlich unserer Sicherheitsrichtlinien) haben, können Sie sich über die [Azure-Supportoptionen](https://azure.microsoft.com/support/options/) mit uns in Verbindung setzen.

## <a name="sending-data-securely-using-tls-12"></a>Sicheres Senden von Daten mit TLS 1.2 

Um die Sicherheit von Daten bei der Übertragung an Log Analytics sicherzustellen, wird dringend empfohlen, den Agent so zu konfigurieren, dass mindestens Transport Layer Security (TLS) 1.2 verwendet wird. Bei älteren Versionen von TLS/Secure Sockets Layer (SSL) wurde ein Sicherheitsrisiko festgestellt. Sie funktionieren aus Gründen der Abwärtskompatibilität zwar noch, werden jedoch **nicht empfohlen**, und die Industrie ist bestrebt, diese älteren Protokolle schnell auszumustern. 

Das [PCI Security Standards Council](https://www.pcisecuritystandards.org/) hat den [30. Juni 2018 als Termin](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) für die Deaktivierung älterer Versionen von TLS/SSL und das Upgrade auf sicherere Protokolle festgelegt. Wenn Azure keine Legacyunterstützung mehr anbietet und Ihre Agents nicht mindestens über TLS 1.2 kommunizieren können, ist das Senden von Daten an Log Analytics nicht möglich. 

Es wird nicht empfohlen, Ihren Agent explizit so einzurichten, dass nur TLS 1.2 verwendet wird, es sei denn, dies ist unbedingt erforderlich. Denn dadurch können Sicherheitsfeatures auf Plattformebene deaktiviert werden, mit deren Hilfe neuere, sicherere Protokolle wie TLS 1.3 automatisch erkannt und genutzt werden können, sobald diese verfügbar sind. 

### <a name="platform-specific-guidance"></a>Plattformspezifische Anleitungen

|Plattform/Sprache | Support | Weitere Informationen |
| --- | --- | --- |
|Linux | Linux-Distributionen greifen zur Unterstützung von TLS 1.2 tendenziell auf [OpenSSL](https://www.openssl.org) zurück.  | Überprüfen Sie anhand des [OpenSSL-Änderungsprotokolls](https://www.openssl.org/news/changelog.html), ob Ihre Version von OpenSSL unterstützt wird.|
| Windows 8.0 bis 10 | Wird unterstützt und ist standardmäßig aktiviert. | Zur Bestätigung, dass Sie weiterhin die [Standardeinstellungen](/windows-server/security/tls/tls-registry-settings) verwenden.  |
| Windows Server 2012 bis 2016 | Wird unterstützt und ist standardmäßig aktiviert. | Zur Bestätigung, dass Sie weiterhin die [Standardeinstellungen](/windows-server/security/tls/tls-registry-settings) verwenden. |
| Windows 7 SP1 und Windows Server 2008 R2 SP1 | Wird unterstützt, ist jedoch standardmäßig deaktiviert. | Details zur Aktivierung finden Sie auf der Seite [Transport Layer Security (TLS) registry settings (Registrierungseinstellungen für Transport Layer Security (TLS))](/windows-server/security/tls/tls-registry-settings).  |

## <a name="data-segregation"></a>Trennung von Daten
Nachdem Ihre Daten vom Log Analytics-Dienst erfasst wurden, werden sie für jede Komponente des Diensts logisch getrennt verwaltet. Sämtliche Daten werden nach Arbeitsbereich gekennzeichnet. Dieser Kennzeichnung wird während des gesamten Datenlebenszyklus beibehalten und auf jeder Ebene des Diensts erzwungen. Die Daten werden in einer dedizierten Datenbank im Speichercluster in der ausgewählten Region gespeichert.

## <a name="data-retention"></a>Beibehaltung von Daten
Die Daten der indizierten Protokollsuche werden gemäß des von Ihnen gewählten Tarifs gespeichert und aufbewahrt. Weitere Informationen finden Sie unter [Log Analytics-Preise](https://azure.microsoft.com/pricing/details/log-analytics/).

Als Bestandteil Ihres [Abonnementvertrags](https://azure.microsoft.com/support/legal/subscription-agreement/) bewahrt Microsoft Ihre Daten gemäß den Vertragsbedingungen auf.  Wenn Kundendaten entfernt werden, werden keine physischen Laufwerke zerstört.  

Die folgende Tabelle enthält einige der verfügbaren Lösungen sowie Beispiele für die Typen der jeweils gesammelten Daten.

| **Lösung** | **Datentypen** |
| --- | --- |
| Kapazität und Leistung |Leistungsdaten und Metadaten |
| Updateverwaltung |Metadaten und Statusdaten |
| Log Management |Benutzerdefinierte Ereignisprotokolle, Windows-Ereignisprotokolle und/oder IIS-Protokolle |
| Change Tracking |Metadaten für Softwarebestand, Windows-Dienste und Linux-Daemons sowie Windows/Linux-Dateimetadaten |
| SQL und Active Directory Assessment |WMI-Daten, Registrierungsdaten, Leistungsdaten und Ergebnisse der dynamischen SQL Server-Verwaltungssichten |

Die folgende Tabelle zeigt Beispiele für Datentypen:

| **Datentyp** | **Fields** |
| --- | --- |
| Warnung |AlertName, AlertDescription, BaseManagedEntityId, ProblemId, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguration |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Ereignis |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Hinweis:** Wenn Sie Ereignisse mit benutzerdefinierten Feldern in das Windows-Ereignisprotokoll schreiben, werden diese per Log Analytics gesammelt. |
| Metadaten |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Leistung |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| State |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Physische Sicherheit
Der Log Analytics-Dienst wird von Microsoft-Mitarbeitern verwaltet. Alle Aktivitäten werden protokolliert und können überwacht werden. Log Analytics wird als Azure-Dienst betrieben und erfüllt sämtliche Compliance- und Sicherheitsanforderungen in Azure. Ausführliche Informationen über die physische Sicherheit der Azure-Ressourcen finden Sie auf Seite 18 des Dokuments [Microsoft Azure Security Overview (Microsoft Azure-Sicherheitsübersicht)](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Physische Zugriffsrechte auf sichere Bereiche werden innerhalb eines Geschäftstags für jeden Benutzer geändert, der keine Verantwortung mehr für den Log Analytics-Dienst trägt, einschließlich der Übertragung und Beendigung. Informieren Sie sich über die globale physische Infrastruktur, die wir in [Microsoft-Rechenzentren](https://azure.microsoft.com/global-infrastructure/) verwenden.

## <a name="incident-management"></a>Incident Management
Log Analytics verfügt über einen Incident-Management-Prozess, dem alle Microsoft-Dienste unterliegen. Zusammenfassung:

* Wir verwenden ein Modell für gemeinsame Verantwortung, bei dem Microsoft einen Teil der Verantwortung für die Sicherheit trägt und der Kunde für den anderen Teil zuständig ist.
* Wir verwalten Azure-Sicherheitsincidents:
  * Wir leiten bei Erkennung eines Vorfalls eine Untersuchung ein.
  * Wir bewerten die Auswirkung und den Schweregrad eines Vorfalls durch ein Teammitglied für Sicherheitsvorfälle auf Abruf. Je nach Situation kann diese Bewertung zu einer weiteren Eskalation an das Security Response-Team führen.
  * Wir diagnostizieren einen Vorfall durch Sicherheitsexperten im Rahmen einer technischen oder forensischen Untersuchung, und wir versuchen dieses Problem zu umgehen, einzudämmen und Lösungsstrategien zu identifizieren. Wenn das Sicherheitsteam davon ausgeht, dass Kundendaten möglicherweise einer rechtswidrigen oder nicht autorisierten Person ausgesetzt worden sind, beginnt die parallele Ausführung des Kundenvorfall-Benachrichtigungsvorgangs.  
  * Stabilisieren und Wiederherstellen nach dem Vorfall. Das Sicherheitsteam erstellt einen Wiederherstellungsplan, um das Problem zu beheben. Schritte zur Schadensbegrenzung, z.B. Isolieren betroffener Systeme, können sofort und parallel zur Diagnose erfolgen. Längerfristige Maßnahmen können geplant werden, die ergriffen werden, nachdem die unmittelbare Gefahr vorüber ist.  
  * Schließen des Vorfalls und Durchführen einer Nachbereitung. Das Sicherheitsteam erstellt eine Nachbereitung mit einer Beschreibung der Details des Vorfalls und der Absicht, Richtlinien, Verfahren und Prozesse zu überarbeiten, um eine Wiederholung des Ereignisses zu verhindern.
* Wir benachrichtigen Kunden über Sicherheitsincidents:
  * Festlegen des Umfangs der betroffenen Kunden und möglichst detaillierte Benachrichtigung aller Betroffenen
  * Erstellen einer Benachrichtigung mit genügend Informationen für Kunden, damit sie ihrerseits eine genauere Untersuchung durchführen und Versprechen halten können, die sie gegenüber ihren Endbenutzern gegeben haben, ohne den Benachrichtigungsvorgang unangemessen zu verzögern.
  * Bestätigung und Meldung des Vorfalls nach Bedarf.
  * Benachrichtigung von Kunden mit einer Vorfallmeldung ohne unangemessene Verzögerung und in Übereinstimmung mit allen gesetzlichen oder vertraglichen Verpflichtungen. Benachrichtigung mindestens eines Administrators des Kunden über Sicherheitsvorfälle mittels von Microsoft ausgewählter Kommunikationsmittel (einschließlich E-Mail).
* Wir führen Team-Bereitschaft und Schulungen durch:
  * Microsoft-Mitarbeiter müssen eine Sicherheits- und Sensibilisierungs-Schulung absolvieren, um verdächtige Sicherheitsprobleme identifizieren und melden zu können.  
  * Operatoren, die mit dem Microsoft Azure-Dienst arbeiten, haben zusätzliche Schulungspflichten hinsichtlich ihres Zugriffs auf vertrauliche Systeme mit Kundendaten.
  * Microsoft Security Response-Mitarbeiter erhalten spezielle Schulungen für ihre Rollen

Im Falle des Verlusts von Kundendaten benachrichtigen wir jeden Kunden innerhalb eines Tages. Ein Verlust von Kundendaten ist jedoch bei dem Dienst noch nie aufgetreten. 

Weitere Informationen über die Reaktion von Microsoft auf Sicherheitsvorfälle finden Sie unter [Microsoft Azure Security Response in the Cloud (Microsoft Azure Security Response in der Cloud)](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Compliance
Das Informationssicherheits- und Governance-Programm des Softwareentwicklungs- und Serviceteams für Log Analytics unterstützt die geschäftlichen Anforderungen und entspricht Gesetzen und Vorschriften, wie unter [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) und [Microsoft Trust Center Compliance](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx) beschrieben. Außerdem wird beschrieben, wie Log Analytics Sicherheitsanforderungen einrichtet, Sicherheitskontrollen identifiziert und Risiken verwaltet und überwacht. Richtlinien, Standards, Verfahren und Leitlinien werden jährlich überprüft.

Jedes Mitglied des Entwicklungsteams erhält eine formale Anwendungssicherheitsschulung. Intern verwenden wir ein Versionskontrollsystem für die Softwareentwicklung. Jedes Softwareprojekt wird durch das Versionskontrollsystem geschützt.

Microsoft hat ein Sicherheits- und Compliance-Team, das alle Microsoft-Dienste überwacht und bewertet. Dieses Team besteht aus Information Security Officers, die nicht mit den Entwicklungsteams, die Log Analytics entwickeln, verbunden sind. Die Sicherheitsbeauftragten verfügen über eine eigene Managementkette und führen unabhängige Beurteilungen von Produkten und Dienstleistungen durch, um Sicherheit und Compliance zu gewährleisten.

Der Microsoft-Vorstand wird in einem Jahresbericht über alle IT-Sicherheitsprogramme bei Microsoft informiert.

Das Softwareentwicklungs- und Serviceteam für Log Analytics arbeitet aktiv mit den Legal- und Compliance-Teams von Microsoft sowie mit anderen Branchenpartnern zusammen, um verschiedenste Zertifizierungen zu erlangen.

## <a name="certifications-and-attestations"></a>Zertifizierungen und Nachweise
Azure Log Analytics erfüllt folgende Anforderungen:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Payment Card Industry (PCI-Compliance) Data Security Standard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) des PCI Security Standards Council
* [Service Organization Controls (SOC) 1 Typ 1 und SOC 2 Typ 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA und HITECH](/compliance/regulatory/offering-hipaa-hitech) für Unternehmen mit einem HIPAA-Geschäftspartnervertrag
* Windows Common Engineering Criteria
* Vertrauenswürdige Datenverarbeitung von Microsoft
* Als Azure-Dienst müssen die Komponenten, die von Log Analytics verwendet werden, die Azure-Konformitätsbestimmungen einhalten. Weitere Informationen erhalten Sie unter [Microsoft Trust Center – Compliance](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> In einigen Zertifizierungen/Nachweisen ist Log Analytics unter dem ehemaligen Namen *Operational Insights* angegeben.
>
>

## <a name="cloud-computing-security-data-flow"></a>Datenfluss beim sicheren Cloud Computing
Das folgende Diagramm zeigt eine Cloudsicherheitsarchitektur sowie den Fluss von Informationen von Ihrem Unternehmen und deren Schutz auf dem Weg zum Log Analytics-Dienst, wo Sie sie letztlich im Azure-Portal anzeigen können. Weitere Informationen zu den einzelnen Schritten finden Sie nach dem Diagramm.

![Abbildung der Datensammlung und Sicherheit in Log Analytics](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Registrieren für Log Analytics und Sammeln von Daten
Damit Ihre Organisation Daten an Log Analytics senden kann, konfigurieren Sie einen Windows- oder Linux-Agent, der auf virtuellen Azure-Computern oder auf virtuellen oder physischen Computern in Ihrer Umgebung oder einem anderen Cloudanbieter ausgeführt wird.  Bei Verwendung von Operations Manager konfigurieren Sie den Operations Manager-Agent über die Verwaltungsgruppe. Benutzer (Sie, andere Einzelbenutzer oder eine Gruppe von Personen) erstellen Log Analytics-Arbeitsbereiche und registrieren Agents mithilfe eines der folgenden Konten:

* [Organisations-ID](../../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft-Konto – Outlook, Office Live, MSN](https://account.microsoft.com/account)

In einem Log Analytics-Arbeitsbereich werden Daten gesammelt, aggregiert, analysiert und präsentiert. Ein Arbeitsbereich wird hauptsächlich zum Partitionieren von Daten verwendet, wobei jeder Arbeitsbereich eindeutig ist. Beispielsweise empfiehlt es sich, Produktionsdaten mit einem Arbeitsbereich zu verwalten und Testdaten mit einem anderen Arbeitsbereich. Arbeitsbereiche helfen Administratoren außerdem dabei, den Benutzerzugriff auf Daten zu steuern. Jedem Arbeitsbereich können mehrere Benutzerkonten zugeordnet werden, und jedes Benutzerkonto kann auf mehrere Log Analytics-Arbeitsbereiche zugreifen. Sie erstellen Arbeitsbereiche auf Grundlage der Rechenzentrumsregion.

Für Operations Manager wird über die Operation Manager-Verwaltungsgruppe eine Verbindung mit dem Log Analytics-Dienst hergestellt. Dann legen Sie fest, welche mit einem Agent verwalteten Systeme in der Verwaltungsgruppe Daten erfassen und an den Dienst senden können. Je nach aktivierter Lösung werden Daten aus diesen Lösungen entweder direkt von einem Operations Manager-Verwaltungsserver oder (aufgrund des Umfangs der Daten, die im mit einem Agent verwalteten System gesammelt werden) direkt vom Agent an den Log Analytics-Dienst gesendet. In Systemen, die nicht über Operations Manager überwacht werden, wird jeweils direkt eine sichere Verbindung mit dem Log Analytics-Dienst hergestellt.

Die gesamte Kommunikation zwischen verbundenen Systemen und dem Log Analytics-Dienst ist verschlüsselt. Das TLS (HTTPS)-Protokoll wird für die Verschlüsselung verwendet.  Das Microsoft SDL-Verfahren stellt sicher, dass Log Analytics nach den neuesten Fortschritten bei kryptografischen Protokollen aktualisiert wird.

Jeder Agent-Typ sammelt Daten für Log Analytics. Die Art der gesammelten Daten ist abhängig von der Art der verwendeten Lösungen. Eine Zusammenfassung der Datensammlung finden Sie unter [Add Log Analytics solutions from the Solutions Gallery (Hinzufügen von Log Analytics-Lösungen aus dem Lösungskatalog)](../insights/solutions.md). Darüber hinaus stehen ausführlichere Sammlungs-Informationen für die meisten Lösungen zur Verfügung. Eine Lösung ist ein Bündel von vordefinierten Ansichten, Protokollsuchabfragen, Datensammlungsregeln und Verarbeitungslogik. Nur Administratoren können Log Analytics zum Importieren einer Lösung verwenden. Nach dem Importieren der Lösung wird diese auf die Operations Manager-Verwaltungsserver (sofern verwendet) und dann in die ausgewählten Agents verschoben. Anschließend sammeln die Agents die Daten.

## <a name="2-send-data-from-agents"></a>2. Senden von Daten von Agents
Sie registrieren alle Agent-Typen mit einem Registrierungsschlüssel. Eine sichere Verbindung zwischen dem Agent und Log Analytics-Dienst wird mithilfe der zertifikatbasierten Authentifizierung und TLS an Port 443 hergestellt. Log Analytics verwendet einen geheimen Speicher zum Generieren und Verwalten von Schlüsseln. Private Schlüssel werden alle 90 Tage rotiert und in Azure gespeichert. Sie werden von Azure-Operatoren verwaltet, die Gesetze und Compliance-Vorschriften strikt einhalten.

In Operations Manager wird über die mit einem Log Analytics-Arbeitsbereich registrierte Verwaltungsgruppe eine sichere HTTPS-Verbindung mit einem Operations Manager-Verwaltungsserver hergestellt.

Für Windows- oder Linux-Agents, die auf virtuellen Computern in Azure ausgeführt werden, wird ein schreibgeschützter Speicherschlüssel verwendet, um Diagnoseereignisse in Azure-Tabellen zu lesen.  

Wenn jeder Agent Daten an eine Operations Manager-Verwaltungsgruppe meldet, die in Log Analytics integriert ist, und der Verwaltungsserver aus einem bestimmten Grund nicht mit dem Dienst kommunizieren kann, werden die gesammelten Daten lokal in einem temporären Cache auf dem Verwaltungsserver gespeichert.   Es wird dann zwei Stunden lang alle acht Minuten versucht, die Daten erneut zu senden.  Bei Daten, bei denen der Verwaltungsserver umgangen wird und die direkt an Log Analytics gesendet werden, entspricht das Verhalten dem des Windows-Agents.  

Zwischengespeicherte Daten des Windows-Agents oder des Agents des Verwaltungsservers sind durch den Anmeldeinformationsspeicher des Betriebssystems geschützt. Wenn der Dienst die Daten innerhalb von zwei Stunden nicht verarbeiten kann, platzieren die Agents die Daten in einer Warteschlange. Wenn die Warteschlange voll ist, beginnt der Agent, Datentypen zu löschen, beginnend mit Leistungsdaten. Die Begrenzung für die Agent-Warteschlange ist ein Registrierungsschlüssel, den Sie bei Bedarf ändern können. Die gesammelten Daten werden komprimiert und unter Umgehung der Datenbanken der Operations Manager-Verwaltungsgruppe an den Dienst gesendet, sodass keine weitere Last hinzugefügt wird. Nach dem Senden werden die gesammelten Daten aus dem Cache entfernt.

Wie oben beschrieben, werden die Daten von dem Verwaltungsserver oder den direkt verbundenen Agents über TLS an Microsoft Azure-Rechenzentren gesendet. Optional können Sie ExpressRoute verwenden, um zusätzliche Sicherheit für die Daten bereitzustellen. ExpressRoute ist eine Möglichkeit, direkt aus Ihrem vorhandenen WAN mit Azure zu verbinden, z.B. ein Multi-Protocol Label Switching-VPN (MPLS), das von einem Netzwerkdienstanbieter bereitgestellt wird. Weitere Informationen finden Sie unter [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Empfangen und Verarbeiten von Daten durch den Log Analytics-Dienst
Der Log Analytics-Dienst stellt sicher, dass eingehende Daten aus einer vertrauenswürdigen Quelle stammen, indem Zertifikate und die Integrität der Daten mittels Azure-Authentifizierung überprüft werden. Die nicht verarbeiteten Rohdaten werden dann in einem Azure Event Hub in der Region gespeichert, in der die Daten schließlich im Ruhezustand gespeichert werden. Die Art der gespeicherten Daten ist abhängig von der Art der importierten und zum Sammeln von Daten verwendeten Lösungen. Der Log Analytics-Dienst verarbeitet dann die Rohdaten und erfasst sie in der Datenbank.

Die Beibehaltungsdauer der gesammelten Daten, die in der Datenbank gespeichert sind, hängt von dem ausgewählten Tarif ab. Beim *Free*-Tarif sind die gesammelten Daten 7 Tage verfügbar. Beim *kostenpflichtigen* Tarif sind die gesammelten Daten standardmäßig 31 Tage verfügbar, aber dieser Zeitraum kann auf 730 Tage verlängert werden. Daten werden im Ruhezustand verschlüsselt in Azure Storage gespeichert, um die Vertraulichkeit der Daten sicherzustellen, und die Daten werden innerhalb der lokalen Region mithilfe von lokal redundantem Speicher (LRS) repliziert. Die letzten zwei Wochen von Daten werden ebenfalls im SSD-basierten Cache gespeichert, und dieser Cache ist verschlüsselt.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Verwenden von Log Analytics für den Datenzugriff
Für den Zugriff auf Ihren Log Analytics-Arbeitsbereich melden Sie sich über das zuvor eingerichtete Unternehmenskonto oder Microsoft-Konto im Azure-Portal an. Der gesamte Datenverkehr zwischen dem Portal und dem Log Analytics-Dienst erfolgt über einen sicheren HTTPS-Kanal. Bei Verwendung des Portals wird eine Sitzungs-ID auf dem Client des Benutzers (Webbrowser) generiert, und die Daten werden in einem lokalen Cache gespeichert, bis die Sitzung beendet wird. Wenn die Sitzung beendet ist, wird der Cache gelöscht. Clientseitige Cookies enthalten keine persönlich identifizierbaren Informationen und werden nicht automatisch entfernt. Sitzungscookies sind „HTTPOnly“ markiert und gesichert. Nach einer vorher festgelegten Zeit im Leerlauf wird die Sitzung im Azure-Portal beendet.


## <a name="additional-security-features"></a>Zusätzliche Sicherheitsfunktionen
Mithilfe dieser zusätzlichen Sicherheitsfunktionen können Sie Ihre Azure Monitor-/Log Analytics-Umgebung noch weiter sichern. Diese Funktionen erfordern mehr Administratorverwaltung. 
- [Kundenseitig verwaltete Schlüssel (Sicherheitsschlüssel)](customer-managed-keys.md): Mithilfe von kundenseitig verwalteten Schlüsseln können Sie Daten verschlüsseln, die an Ihre Log Analytics-Arbeitsbereiche gesendet werden. Dies erfordert die Verwendung von Azure Key Vault. 
- [Privater/kundenseitig verwalteter Speicher](private-storage.md): Verwalten Sie Ihr persönlich verschlüsseltes Speicherkonto, und weisen Sie Log Analytics an, dieses zum Speichern von Überwachungsdaten zu verwenden. 
- [Private Link-Netzwerk](private-link-security.md): Mit Azure Private Link können Sie Azure-PaaS-Dienste (einschließlich Azure Monitor) über private Endpunkte sicher mit Ihrem virtuellen Netzwerk verknüpfen. 
- [Azure Kunden-Lockbox](/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview): Kunden-Lockbox für Microsoft Azure bietet eine Oberfläche, auf der Kunden Anforderungen für den Zugriff auf Kundendaten prüfen und dann genehmigen oder ablehnen können. Das Feature wird in Fällen verwendet, in denen ein Microsoft-Techniker während einer Supportanfrage auf Kundendaten zugreifen muss.


## <a name="next-steps"></a>Nächste Schritte
* Im [Schnellstart zu virtuellen Azure-Computern](../learn/quick-collect-azurevm.md) erfahren Sie, wie Sie mit Log Analytics Daten für Ihre virtuellen Azure-Computer sammeln.  

*  Wenn Sie Daten von physischen oder virtuellen Windows- oder Linux-Computern in Ihrer Umgebung sammeln möchten, finden Sie entsprechende Informationen im [Schnellstart für Linux-Computer](../learn/quick-collect-linux-computer.md) bzw. im [Schnellstart für Windows-Computer](../learn/quick-collect-windows-computer.md).

