---
title: DNS-Analyse-Lösung in Azure Monitor | Microsoft-Dokumentation
description: Richten Sie die DNS-Analyse-Lösung in Azure Monitor ein, und verwenden Sie sie, um Erkenntnisse über die DNS-Infrastruktur hinsichtlich Sicherheit, Leistung und Betrieb zu sammeln.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 7bdea9239faa4ec66fffa236bea40afd5e628e62
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607142"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Sammeln von Erkenntnissen zu Ihrer DNS-Infrastruktur mit der DNS Analytics-Vorschaulösung

![Symbol für die DNS-Analyse](./media/dns-analytics/dns-analytics-symbol.png)

In diesem Artikel wird beschrieben, wie Sie die Azure DNS-Analyse-Lösung in Azure Monitor einrichten und verwenden, um Erkenntnisse hinsichtlich Sicherheit, Leistung und Betrieb der DNS-Infrastruktur zu sammeln.

DNS Analytics ermöglicht Ihnen Folgendes:

- Identifizieren von Clients, die versuchen, schädliche Domänennamen aufzulösen.
- Identifizieren von veralteten Ressourceneinträgen.
- Identifizieren von häufig abgefragten Domänennamen und DNS-Clients mit hoher Aktivität.
- Anzeigen der Anforderungslast auf DNS-Servern.
- Anzeigen von Fehlern der dynamischen DNS-Registrierung.

Die Lösung erfasst, analysiert und korreliert Windows-DNS-Analyseprotokolle und -Überwachungsprotokolle sowie andere ähnliche Daten von Ihren DNS-Servern.

## <a name="connected-sources"></a>Verbundene Quellen

In der folgenden Tabelle sind die verbundenen Quellen beschrieben, die von der Lösung unterstützt werden:

| **Verbundene Quelle** | **Unterstützung** | **Beschreibung** |
| --- | --- | --- |
| [Windows-Agents](../platform/agent-windows.md) | Ja | Die Lösung erfasst DNS-Informationen von Windows-Agents. |
| [Linux-Agents](../learn/quick-collect-linux-computer.md) | Nein | Die Lösung erfasst keine DNS-Informationen von direkten Linux-Agents. |
| [System Center Operations Manager-Verwaltungsgruppe](../platform/om-agents.md) | Ja | Die Lösung erfasst DNS-Informationen von Agents in einer verbundenen Operations Manager-Verwaltungsgruppe. Es ist keine direkte Verbindung zwischen dem Operations Manager-Agent und Azure Monitor erforderlich. Daten werden von der Verwaltungsgruppe an den Log Analytics-Arbeitsbereich weitergeleitet. |
| [Azure-Speicherkonto](../platform/resource-logs.md#send-to-log-analytics-workspace) | Nein | Azure-Speicher wird von der Lösung nicht verwendet. |

### <a name="data-collection-details"></a>Details zur Datensammlung

Die Lösung sammelt Daten zum DNS-Inventar und zu DNS-Ereignissen von den DNS-Servern, auf denen ein Log Analytics-Agent installiert ist. Diese Daten werden dann in Azure Monitor hochgeladen und im Lösungsdashboard angezeigt. Inventardaten, z.B. die Anzahl der DNS-Server, Zonen und Ressourceneinträge, werden gesammelt, indem die DNS-PowerShell-Cmdlets ausgeführt werden. Die Daten werden einmal alle zwei Tage aktualisiert. Die Ereignisdaten werden nahezu in Echtzeit aus den [Analyse- und Überwachungsprotokollen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) erfasst, die durch die verbesserte DNS-Protokollierung und -Diagnose in Windows Server 2012 R2 bereitgestellt werden.

## <a name="configuration"></a>Konfiguration

Verwenden Sie die folgenden Informationen zum Konfigurieren der Lösung:

- Sie benötigen einen [Windows](../platform/agent-windows.md)- oder [Operations Manager](../platform/om-agents.md)-Agent auf jedem DNS-Server, den Sie überwachen möchten.
- Sie können die DNS Analytics-Lösung im [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace) Ihrem Log Analytics-Arbeitsbereich hinzufügen. Sie können auch den unter [Hinzufügen von Azure Monitor-Lösungen aus dem Lösungskatalog](solutions.md) beschriebenen Prozess verwenden.

Die Lösung beginnt ohne weitere Konfiguration mit dem Sammeln von Daten. Allerdings können Sie die folgende Konfiguration zum Anpassen der Datensammlung verwenden.

### <a name="configure-the-solution"></a>Konfigurieren der Projektmappe

Klicken Sie im Lösungsdashboard auf **Konfiguration**, um die DNS Analytics-Konfigurationsseite zu öffnen. Es gibt zwei Arten von Konfigurationsänderungen, die Sie vornehmen können:

- **Domänennamen auf der Positivliste**. Die Lösung verarbeitet nicht alle Suchabfragen. Sie verwaltet eine Positivliste mit Domänennamensuffixen. Die Suchabfragen, die in Domänennamen aufgelöst werden, die mit Domänennamensuffixen in dieser Positivliste übereinstimmen, werden von der Lösung nicht verarbeitet. Da die in der Positivliste enthalten Domänennamen nicht verarbeitet werden, werden die an Azure Monitor gesendeten Daten optimiert. Die Standardpositivliste enthält gängige öffentliche Domänennamen, z. B. www.google.com und www.facebook.com. Sie können die vollständige Standardliste mittels Scrollen anzeigen.

  Sie können die Liste ändern, um alle Domänennamensuffixe hinzuzufügen, zu denen Sie Suchergebnisse anzeigen möchten. Sie können auch alle Domänennamensuffixe entfernen, zu denen Sie keine Suchergebnisse anzeigen möchten.

- **Schwellenwert für Clients mit hoher Aktivität**. DNS-Clients, die den Schwellenwert für die Anzahl der Suchanforderungen überschreiten, werden auf dem Blatt **DNS-Clients** hervorgehoben. Der Standardschwellenwert beträgt 1.000. Sie können den Schwellenwert bearbeiten.

    ![Domänennamen auf der Positivliste](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Management Packs

Wenn Sie den Microsoft Monitoring Agent für die Verbindung mit Ihrem Log Analytics-Arbeitsbereich verwenden, wird das folgende Management Pack installiert:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)

Wenn Ihre Operations Manager-Verwaltungsgruppe mit Ihrem Log Analytics-Arbeitsbereich verbunden ist, werden beim Hinzufügen dieser Lösung die folgenden Management Packs in Operations Manager installiert. Für diese Management Packs ist keine Konfiguration oder Wartung erforderlich:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

Weitere Informationen zur Aktualisierung von Management Packs finden Sie unter [Herstellen einer Verbindung zwischen Operations Manager und Log Analytics](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Verwenden der DNS Analytics-Lösung

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


Die DNS-Kachel enthält die Anzahl der DNS-Server, auf denen die Daten gesammelt werden. Darüber hinaus enthält sie die Anzahl der Anforderungen von Clients in den letzten 24 Stunden, schädliche Domänen aufzulösen. Wenn Sie auf die Kachel klicken, wird das Lösungsdashboard geöffnet.

![DNS Analytics-Kachel](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Lösungsdashboard

Das Lösungsdashboard zeigt zusammengefasste Informationen für die verschiedenen Funktionen der Lösung. Es enthält auch Links zur detaillierten Ansicht für die forensische Analyse und Diagnose. Standardmäßig werden die Daten der letzten sieben Tage angezeigt. Sie können den Datums- und Uhrzeitbereich mit dem **Steuerelement für die Datums-/Uhrzeitauswahl** ändern, wie in der folgenden Abbildung gezeigt:

![Zeitauswahl-Steuerelement](./media/dns-analytics/dns-time.png)

Im Lösungsdashboard werden die folgenden Blätter angezeigt:

**DNS-Sicherheit**. Gibt die DNS-Clients an, die versuchen, mit schädlichen Domänen zu kommunizieren. Mithilfe von Microsoft-Feeds mit Informationen zu Bedrohungen erkennt DNS Analytics Client-IP-Adressen, die versuchen, auf die schädlichen Domänen zuzugreifen. In vielen Fällen „wählen“ mit Schadsoftware infizierte Geräte das „Befehls- und Steuerungszentrum“ der schädlichen Domäne durch Auflösen des Domänennamens der Schadsoftware an.

![Blatt „DNS-Sicherheit“](./media/dns-analytics/dns-security-blade.png)

Wenn Sie auf eine Client-IP-Adresse in der Liste klicken, wird die Protokollsuche geöffnet und zeigt die Details der Suche für die jeweilige Abfrage an. Im folgenden Beispiel hat DNS Analytics erkannt, dass mit einem [IRCbot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621) kommuniziert wurde:

![Protokollsuchergebnisse mit IRCbot](./media/dns-analytics/ircbot.png)

Mit den Informationen können Sie Folgendes identifizieren:

- Die Client-IP-Adresse, die die Kommunikation initiiert hat.
- Den Domänennamen, der in die schädliche IP-Adresse aufgelöst wird.
- IP-Adressen, in die der Domänenname aufgelöst wird.
- Schädliche IP-Adresse.
- Schweregrad des Problems.
- Grund für die Aufnahme der schädlichen IP-Adresse in die Sperrliste.
- Zeitpunkt der Erkennung.

**Abgefragte Domänen**. Gibt die am häufigsten von den DNS-Clients in Ihrer Umgebung abgefragten Domänennamen an. Sie können die Liste aller abgefragten Domänennamen anzeigen. Sie können auch Details von Suchanforderungen für einen bestimmten Domänennamen in der Protokollsuche anzeigen.

![Blatt „Abgefragte Domänen“](./media/dns-analytics/domains-queried-blade.png)

**DNS-Clients**. Gibt die Clients an, die für die Anzahl von Abfragen im gewählten Zeitraum *den Schwellenwert verletzen*. Sie können die Liste aller DNS-Clients und die Details der von ihnen vorgenommenen Abfragen in der Protokollsuche anzeigen.

![Blatt „DNS-Clients“](./media/dns-analytics/dns-clients-blade.png)

**Dynamische DNS-Registrierungen**. Gibt die Fehler bei der Namensregistrierung an. Alle Registrierungsfehler für [Ressourceneinträge](https://en.wikipedia.org/wiki/List_of_DNS_record_types) von Adressen (Typ A und AAAA) werden zusammen mit den Client-IP-Adressen hervorgehoben, von denen die Registrierungsanforderungen stammen. Mit diesen Informationen können Sie dann die eigentliche Ursache des Registrierungsfehlers mit den folgenden Schritten finden:

1. Suchen Sie die Zone, die für den Namen autoritativ ist, den der Client aktualisieren möchte.

1. Verwenden Sie die Lösung, um die Inventarinformationen dieser Zone zu überprüfen.

1. Stellen Sie sicher, dass die dynamische Aktualisierung für die Zone aktiviert ist.

1. Überprüfen Sie, ob die Zone für die sichere dynamische Aktualisierung konfiguriert ist.

    ![Blatt „Dynamische DNS-Registrierungen“](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Anforderungen zur Namensregistrierung**. Auf der oberen Kachel wird ein Trend erfolgreicher dynamischer DNS-Aktualisierungsanforderungen und derer, bei denen Fehler aufgetreten sind, angezeigt. Auf der unteren Kachel sind die 10 Clients aufgeführt, die die meisten DNS-Aktualisierungsanforderungen, bei denen Fehler aufgetreten sind, an die DNS-Server gesendet haben, sortiert nach der Anzahl von Fehlern.

![Blatt „Anforderungen zur Namensregistrierung“](./media/dns-analytics/name-reg-req-blade.png)

**DDI Analytics-Beispielabfragen**. Enthält eine Liste der am häufigsten verwendeten Suchabfragen, mit denen unformatierte Analysedaten direkt abgerufen werden.


![Beispielabfragen](./media/dns-analytics/queries.png)

Sie können diese Abfragen als Ausgangspunkt für das Erstellen eigener Abfragen für benutzerdefinierte Berichte verwenden. Die Abfragen sind mit der Protokollsucheseite von DNS Analytics verknüpft, wo Ergebnisse angezeigt werden:

- **Liste der DNS-Server**. Zeigt eine Liste aller DNS-Server mit ihren zugeordneten FQDNs, Domänennamen, Gesamtstrukturnamen und Server-IP-Adressen an.
- **Liste der DNS-Zonen**. Zeigt eine Liste aller DNS-Zonen mit den jeweiligen Zonennamen, Angaben zum dynamischen Aktualisierungsstatus, Namenservern und Angaben zum DNSSEC-Signaturstatus an.
- **Nicht verwendete Ressourceneinträge**. Zeigt eine Liste aller nicht verwendeten/veralteten Ressourceneinträge an. Diese Liste enthält den Namen und den Typ des Ressourceneintrags, den zugeordneten DNS-Server, den Zeitpunkt der Generierung und den Zonennamen. Sie können mit dieser Liste die DNS-Ressourceneinträge identifizieren, die nicht mehr verwendet werden. Auf der Basis dieser Informationen können Sie dann diese Einträge von den DNS-Servern entfernen.
- **Abfragelast für DNS-Server**. Zeigt Informationen zur DNS-Last auf den DNS-Servern an. Anhand dieser Informationen können Sie die Kapazität für die Server planen. Sie können zur Registerkarte **Metriken** wechseln, um die Ansicht in eine grafische Visualisierung zu ändern. Mit dieser Ansicht können Sie nachvollziehen, wie die DNS-Last auf die DNS-Server verteilt wird. Sie zeigt Trends der DNS-Abfragerate für jeden Server.

    ![Protokollsuchergebnisse von Abfragen für DNS-Server](./media/dns-analytics/dns-servers-query-load.png)

- **Abfragelast für DNS-Zonen**. Zeigt die Statistik der DNS-Zonenabfragen pro Sekunde für alle Zonen auf den DNS-Servern an, die von der Lösung verwaltet werden. Klicken Sie auf die Registerkarte **Metriken**, um die Ansicht von ausführlichen Datensätzen in eine grafische Visualisierung der Ergebnisse zu ändern.
- **Konfigurationsereignisse**. Zeigt alle DNS-Konfigurationsänderungsereignisse und die zugehörigen Meldungen an. Sie können dann diese Ereignisse basierend auf dem Zeitpunkt des Ereignisses, der Ereignis-ID, des DNS-Servers oder der Aufgabenkategorie filtern. Mit den Daten können Sie Änderungen an bestimmten DNS-Servern zu bestimmten Zeitpunkten überprüfen.
- **DNS-Analyseprotokoll**. Zeigt alle Analyseereignisse auf allen DNS-Servern an, die mit der Lösung verwaltet werden. Sie können dann diese Ereignisse basierend auf dem Zeitpunkt des Ereignisses, der Ereignis-ID, des DNS-Servers, der Client-IP-Adresse, von der die Suchabfrage stammt, und der Aufgabenkategorie des Abfragetyps filtern. Mit Analyseereignissen für DNS-Server können Aktivitäten auf dem DNS-Server überwacht werden. Ein Analyseereignis wird jedes Mal protokolliert, wenn der Server DNS-Informationen sendet oder empfängt.

### <a name="search-by-using-dns-analytics-log-search"></a>Suchen mithilfe der DNS Analytics-Protokollsuche

Auf der Protokollsucheseite können Sie eine Abfrage erstellen. Sie können die Suchergebnisse mit Facetsteuerelementen filtern. Sie können auch erweiterte Abfragen zum Transformieren, Filtern und Berichten für Ihre Ergebnisse erstellen. Beginnen Sie mit folgenden Abfragen:

1. Geben Sie im **Feld für die Suchabfrage**`DnsEvents` ein, um alle DNS-Ereignisse anzuzeigen, die von den DNS-Servern generiert wurden, die von der Lösung verwaltet werden. Die Ergebnisse listen die Protokolldaten für alle Ereignisse im Zusammenhang mit Suchabfragen, dynamischen Registrierungen und Konfigurationsänderungen auf.

    ![DnsEvents-Protokollsuche](./media/dns-analytics/log-search-dnsevents.png)  

    a. Wählen Sie zum Anzeigen der Protokolldaten für Suchabfragen auf der linken Seite im Facetsteuerelement **LookUpQuery** als **Untertyp**-Filter aus. Eine Tabelle mit allen Ereignissen zu Suchabfragen für den ausgewählten Zeitraum wird angezeigt.

    b. Um die Protokolldaten für dynamische Registrierungen anzuzeigen, wählen Sie auf der linken Seite im Facetsteuerelement **DynamicRegistration** als **Untertyp**-Filter aus. Eine Tabelle mit allen dynamischen Registrierungsereignissen für den ausgewählten Zeitraum wird angezeigt.

    c. Um die Protokolldaten für Konfigurationsänderungen anzuzeigen, wählen Sie auf der linken Seite im Facetsteuerelement **ConfigurationChange** als **Untertyp**-Filter aus. Eine Tabelle mit allen Konfigurationsänderungsereignissen für den ausgewählten Zeitraum wird angezeigt.

1. Geben Sie im **Feld für die Suchabfrage**`DnsInventory` ein, um alle DNS-Inventardaten für die DNS-Server anzuzeigen, die von der Lösung verwaltet werden. Die Ergebnisse listen die Protokolldaten für DNS-Server, DNS-Zonen und Ressourceneinträge auf.

    ![DnsInventory-Protokollsuche](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Problembehandlung

Allgemeine Schritte zur Problembehandlung:

1. Fehlende DNS-Lookupdaten: Um dieses Problem zu beheben, setzen Sie die Konfiguration zurück, oder laden Sie die Konfigurationsseite einfach ein Mal im Portal. Ändern Sie zum Zurücksetzen nur eine Einstellung in einen anderen Wert, und ändern Sie sie anschließend erneut in den ursprünglichen Wert. Speichern Sie die Konfiguration dann.

## <a name="suggestions"></a>Vorschläge

Rufen Sie für Feedback die UserVoice-Seite für [Log Analytics](https://aka.ms/dnsanalyticsuservoice) auf, um Ideen zu wünschenswerten DNS Analytics-Features mitzuteilen. 

## <a name="next-steps"></a>Nächste Schritte

[Anfragen von Protokollen](../log-query/log-query-overview.md) zum Anzeigen detaillierter DNS-Protokolldatensätze.
