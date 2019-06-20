---
title: Checkliste zu Leistung und Skalierbarkeit von Azure Storage | Microsoft Docs
description: Eine Checkliste mit bewährten Methoden für die Verwendung mit Azure Storage beim Entwickeln leistungsfähiger Anwendungen.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 904b9b8ba98be5e14b1d769a0e1d8c2d6084e24d
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951179"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Checkliste zu Leistung und Skalierbarkeit von Microsoft Azure Storage
## <a name="overview"></a>Übersicht
Seit der Veröffentlichung der Microsoft Azure Storage-Dienste hat Microsoft eine Reihe bewährter Vorgehensweisen zur effektiven Verwendung dieser Dienste entwickelt. In diesem Artikel werden die wichtigsten davon in einer "Checkliste" zusammengefasst. Dieser Artikel soll Anwendungsentwickler bei der Verwendung bewährter Vorgehensweisen für Azure Storage unterstützen und ihnen helfen, weitere bewährte Vorgehensweisen kennen zu lernen, die sie in Erwägung ziehen sollten. Es wird jedoch nicht jede mögliche Leistungs- und Skalierbarkeitsoptimierung in diesem Artikel abgedeckt; begrenzt anwendbare Methoden oder solche mit geringer Auswirkung sind ausgeschlossen. Insofern sich das Anwendungsverhalten während des Entwurfs vorhersagen lässt, sollten diese Vorgehensweisen von Anfang an berücksichtigt werden, um spätere Leistungsprobleme zu vermeiden.  

Jeder Anwendungsentwickler, der Azure Storage verwendet, sollte sich Zeit zum Lesen dieses Artikels nehmen und überprüfen, ob seine Anwendung den unten aufgeführten bewährten Vorgehensweisen entspricht.  

## <a name="checklist"></a>Checkliste
Dieser Artikel unterteilt die bewährten Vorgehensweisen in Gruppen, die wie folgt anwendbar sind:  

* Alle Azure-Speicherdienste (Blobs, Tabellen, Warteschlangen und Dateien)
* Blobs (in englischer Sprache)
* Tabellen
* Warteschlangen  

| Vorgehensweise | Bereich | Category (Kategorie) | Frage |
| --- | --- | --- | --- |
| &nbsp; | Alle Dienste |Skalierbarkeitsziele |[Ist Ihre Anwendung so konzipiert, dass die Skalierbarkeitsziele nicht überschritten werden?](#subheading1) |
| &nbsp; | Alle Dienste |Skalierbarkeitsziele |[Dient Ihre Namenskonvention einem besseren Lastenausgleich?](#subheading47) |
| &nbsp; | Alle Dienste |Netzwerk |[Haben die clientseitigen Geräte genügend Bandbreite und niedrige Latenz, um die erforderliche Leistung zu erzielen?](#subheading2) |
| &nbsp; | Alle Dienste |Netzwerk |[Haben die clientseitigen Geräte eine ausreichend hohe Verbindungsqualität?](#subheading3) |
| &nbsp; | Alle Dienste |Netzwerk |[Befindet sich die Clientanwendung in der Nähe des Speicherkontos?](#subheading4) |
| &nbsp; | Alle Dienste |Inhaltsverteilung |[Verwenden Sie ein CDN für die Inhaltsverteilung?](#subheading5) |
| &nbsp; | Alle Dienste |Direkter Clientzugriff |[Verwenden Sie SAS und CORS statt eines Proxys, um direkten Zugriff auf den Speicher zu erlauben?](#subheading6) |
| &nbsp; | Alle Dienste |Caching |[Speichert Ihre Anwendung Daten im Cache, die häufig verwendet werden und sich selten ändern?](#subheading7) |
| &nbsp; | Alle Dienste |Caching |[Führt Ihre Anwendung Batchaktualisierungen durch (durch clientseitige Zwischenspeicherung und anschließendes Hochladen in größeren Mengen)?](#subheading8) |
| &nbsp; | Alle Dienste |.NET-Konfiguration |[Haben Sie Ihren Client zur Verwendung einer ausreichenden Anzahl gleichzeitiger Verbindungen konfiguriert?](#subheading9) |
| &nbsp; | Alle Dienste |.NET-Konfiguration |[Haben Sie .NET zur Verwendung einer ausreichenden Anzahl von Threads konfiguriert?](#subheading10) |
| &nbsp; | Alle Dienste |.NET-Konfiguration |[Verwenden Sie .NET 4.5 oder höher mit verbesserter Garbage Collection?](#subheading11) |
| &nbsp; | Alle Dienste |Parallelität |[Haben Sie sichergestellt, dass die Parallelität entsprechend gebunden ist, sodass weder die Clientkapazitäten noch die Skalierbarkeitsziele überlastet werden?](#subheading12) |
| &nbsp; | Alle Dienste |Tools |[Verwenden Sie die aktuellste Version der von Microsoft bereitgestellten Clientbibliotheken und Tools?](#subheading13) |
| &nbsp; | Alle Dienste |Wiederholungsversuche |[Verwenden Sie exponentiell ansteigende Wartezeiten für Wiederholungsversuche, um Fehler und Zeitüberschreitungen zu vermeiden?](#subheading14) |
| &nbsp; | Alle Dienste |Wiederholungsversuche |[Vermeidet Ihre Anwendung Wiederholungsversuche für nicht wiederholbare Fehler?](#subheading15) |
| &nbsp; | Blobs (in englischer Sprache) |Skalierbarkeitsziele |[Greift eine große Anzahl von Clients gleichzeitig auf ein einzelnes Objekt zu?](#subheading46) |
| &nbsp; | Blobs (in englischer Sprache) |Skalierbarkeitsziele |[Bleibt Ihre Anwendung innerhalb der Bandbreiten- oder Vorgangs-Skalierbarkeitsziele für einzelne Blobs?](#subheading16) |
| &nbsp; | Blobs (in englischer Sprache) |Kopieren von Blobs |[Kopieren Sie Blobs effizient?](#subheading17) |
| &nbsp; | Blobs (in englischer Sprache) |Kopieren von Blobs |[Verwenden Sie AzCopy für Massenkopien von Blobs?](#subheading18) |
| &nbsp; | Blobs (in englischer Sprache) |Kopieren von Blobs |[Verwenden Sie Azure Import/Export zum Übertragen von großen Datenmengen?](#subheading19) |
| &nbsp; | Blobs (in englischer Sprache) |Verwenden von Metadaten |[Speichern Sie häufig verwendete Metadaten für Blobs in ihren Metadaten?](#subheading20) |
| &nbsp; | Blobs (in englischer Sprache) |Schnelles Hochladen |[Wenn Sie versuchen, einen Blob schnell hochzuladen, laden Sie dann Blöcke parallel hoch?](#subheading21) |
| &nbsp; | Blobs (in englischer Sprache) |Schnelles Hochladen |[Wenn Sie versuchen, mehrere Blobs schnell hochzuladen, laden Sie dann Blobs parallel hoch?](#subheading22) |
| &nbsp; | Blobs (in englischer Sprache) |Richtiger Blobtyp |[Verwenden Sie jeweils Seiten-Blobs oder Block-Blobs?](#subheading23) |
| &nbsp; | Tabellen |Skalierbarkeitsziele |[Nähern Sie sich den Skalierbarkeitszielen für Entitäten pro Sekunde?](#subheading24) |
| &nbsp; | Tabellen |Konfiguration |[Verwenden Sie JSON für Ihre Tabellenanforderungen?](#subheading25) |
| &nbsp; | Tabellen |Konfiguration |[Haben Sie Nagle deaktiviert, um die Leistung kleiner Anfragen zu verbessern?](#subheading26) |
| &nbsp; | Tabellen |Tabellen und Partitionen |[Haben Sie Ihre Daten richtig partitioniert?](#subheading27) |
| &nbsp; | Tabellen |Hot Partition |[Vermeiden Sie Nur-Anhängen/Nur-Voranstellen-Muster?](#subheading28) |
| &nbsp; | Tabellen |Hot Partition |[Verteilen sich Ihre Einfügungen/Aktualisierungen auf mehrere Partitionen?](#subheading29) |
| &nbsp; | Tabellen |Abfragebereich |[Haben Sie Ihr Schema so konzipiert, dass in den meisten Fällen Punktabfragen verwendet werden können und Tabellenabfragen nur sparsam erfolgen?](#subheading30) |
| &nbsp; | Tabellen |Abfragedichte |[Erfassen Ihre Abfragen normalerweise nur Zeilen für die Rückgabe, die Ihre Anwendung auch verwenden wird?](#subheading31) |
| &nbsp; | Tabellen |Einschränken der zurückgegebenen Daten |[Verwenden Sie Filterung, um die Rückgabe nicht benötigter Entitäten zu vermeiden?](#subheading32) |
| &nbsp; | Tabellen |Einschränken der zurückgegebenen Daten |[Verwenden Sie Projektion, um die Rückgabe nicht benötigter Eigenschaften zu vermeiden?](#subheading33) |
| &nbsp; | Tabellen |Denormalisierung |[Haben Sie Ihre Daten so normalisiert, dass Sie ineffiziente Abfragen oder mehrere Leseanforderungen beim Abrufen von Daten vermeiden?](#subheading34) |
| &nbsp; | Tabellen |Einfügen/Aktualisieren/Löschen |[Verwenden Sie Batchverarbeitung für Anforderungen, die transaktional oder gleichzeitig erfolgen, um Roundtrips zu reduzieren?](#subheading35) |
| &nbsp; | Tabellen |Einfügen/Aktualisieren/Löschen |[Vermeiden Sie das Abrufen von Entitäten, nur um zu bestimmen, ob Einfügen oder Aktualisieren erforderlich ist?](#subheading36) |
| &nbsp; | Tabellen |Einfügen/Aktualisieren/Löschen |[Haben Sie überlegt, Datenreihen, die häufig zusammen abgerufen werden, in einer einzigen Entität als Eigenschaften zu speichern statt in mehreren Entitäten?](#subheading37) |
| &nbsp; | Tabellen |Einfügen/Aktualisieren/Löschen |[Haben Sie überlegt, für Entitäten, die immer zusammen abgerufen werden und in Batches geschrieben werden können (zum Beispiel Zeitreihendaten), Blobs statt Tabellen zu verwenden?](#subheading38) |
| &nbsp; | Warteschlangen |Skalierbarkeitsziele |[Nähern Sie sich den Skalierbarkeitszielen für Nachrichten pro Sekunde?](#subheading39) |
| &nbsp; | Warteschlangen |Konfiguration |[Haben Sie Nagle deaktiviert, um die Leistung kleiner Anfragen zu verbessern?](#subheading40) |
| &nbsp; | Warteschlangen |Nachrichtengröße |[Sind Ihre Nachrichten kompakt, um die Leistung der Warteschlange zu verbessern?](#subheading41) |
| &nbsp; | Warteschlangen |Massenabruf |[Rufen Sie mehrere Nachrichten mit einem einzigen „Get“-Vorgang ab?](#subheading42) |
| &nbsp; | Warteschlangen |Abrufhäufigkeit |[Rufen Sie häufig genug ab, um die gefühlte Latenz der Anwendung zu reduzieren?](#subheading43) |
| &nbsp; | Warteschlangen |Aktualisieren von Nachrichten |[Verwenden Sie UpdateMessage, um den Fortschritt bei der Nachrichtenverarbeitung zu speichern und so nicht die gesamten Nachrichten erneut verarbeiten zu müssen, falls ein Fehler auftritt?](#subheading44) |
| &nbsp; | Warteschlangen |Architecture |[Verwenden Sie Warteschlangen, um die gesamte Anwendung besser skalierbar zu machen, indem Sie Arbeitsauslastungen mit langer Laufzeit aus dem kritischen Pfad heraushalten und unabhängig skalieren?](#subheading45) |

## <a name="allservices"></a>Alle Dienste
In diesem Abschnitt sind bewährte Vorgehensweisen für die Verwendung von allen Azure-Speicherdiensten (Blobs, Tabellen, Warteschlangen und Dateien) aufgelistet.  

### <a name="subheading1"></a>Skalierbarkeitsziele
Azure Storage selbst hat ein Limit von 250 Speicherkonten pro Region und Abonnement. Wenn Sie dieses Limit erreichen, können Sie keine weiteren Speicherkonten in dieser Abonnement-Region-Kombination anlegen.

Jeder Azure-Speicherdienst hat Skalierbarkeitsziele für Kapazität (GB), Transaktionsrate und Bandbreite. Wenn Ihre Anwendung eines der Skalierbarkeitsziele erreicht oder überschreitet, kann es zu erhöhter Transaktionslatenz oder Drosselung kommen. Sobald ein Speicherdienst Ihre Anwendung drosselt, gibt der Dienst für manche Speichertransaktionen den Fehlercode „503 Server Busy“ oder „500 Operation Timeout“ zurück. In diesem Abschnitt werden die allgemeine Vorgehensweise zur Handhabung von Skalierbarkeitszielen sowie speziell die Bandbreiten-Skalierbarkeitsziele behandelt. In späteren Abschnitten zu den einzelnen Speicherdiensten werden die Skalierbarkeitsziele im jeweiligen Dienstkontext erläutert:  

* [Blobbandbreite und Anfragen pro Sekunde](#subheading16)
* [Tabellenentitäten pro Sekunde](#subheading24)
* [Warteschlangennachrichten pro Sekunde](#subheading39)  

#### <a name="sub1bandwidth"></a>Bandbreiten-Skalierbarkeitsziele für alle Dienste
Zum Redaktionszeitpunkt betrugen die Bandbreitenziele in den USA für georedundante Speicherkonten (GRS) 10 Gigabit pro Sekunde (GBit/s) für eingehende Daten (an das Speicherkonto gesendete Daten) und 20 Gigabit pro Sekunde (GBit/s) für ausgehende Daten (vom Speicherkonto aus gesendete Daten). Für lokale redundante Speicherkonten (LRS) liegen die Grenzen höher – 20 GBit/s für eingehende und 30 GBit/s für ausgehende Daten.  Internationale Bandbreitengrenzen können niedriger sein. Informationen dazu finden Sie auf der Seite [Skalierbarkeitsziele](https://msdn.microsoft.com/library/azure/dn249410.aspx).  Weitere Informationen zu den Speicherredundanzoptionen finden Sie unter den Links unter „Nützliche Ressourcen“ weiter unten.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Vorgehensweise bei Annäherung an das Skalierbarkeitsziel
Wenn Sie sich dem Limit der Speicherkonten nähern, die Sie in einer bestimmten Abonnement-Region-Kombination haben können, bewerten Sie Ihre Anwendung und die Nutzung von Speicherkonten und prüfen Sie, ob eine dieser Bedingungen zutrifft.

* Verwenden von Speicherkonten als nicht verwaltete Datenträger und Hinzufügen dieser Datenträger zu Ihren virtuellen Computern. In diesem Szenario empfehlen wir die Verwendung von [verwalteten Datenträgern](../../virtual-machines/windows/managed-disks-overview.md), da sie die Skalierbarkeit der Speicherdatenträger für Sie übernehmen, ohne dass Sie individuelle Speicherkonten erstellen und verwalten müssen.
* Verwenden eines Speicherkonto auf Kundenbasis zum Zwecke der Datenisolierung. In diesem Szenario empfehlen wir, Speichercontainer für jeden Kunden und nicht für ein komplettes Speicherkonto zu verwenden. Azure Storage ermöglicht es Ihnen nun, die rollenbasierte Zugriffskontrolle pro [Containerbasis](storage-auth-aad-rbac-portal.md) festzulegen.
* Verwendung mehrerer Speicherkonten als Shard für eine bessere Skalierbarkeit von ingress/egress/iops/capacity. In diesem Szenario empfehlen wir Ihnen, wenn möglich, die [erhöhten Grenzwerte](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) der Standard-Speicherkonten zu nutzen, um die Anzahl der für Ihren Workload erforderlichen Speicherkonten zu reduzieren.

Wenn sich Ihre Anwendung den Skalierbarkeitszielen für ein Speicherkonto nähert, sollten Sie eine der folgenden Vorgehensweisen wählen:  

* Berücksichtigen Sie die Arbeitsauslastung, aufgrund derer Ihre Anwendung das Skalierbarkeitsziel erreicht oder überschreitet. Können Sie diese anders konzipieren, um weniger Bandbreite bzw. Kapazität oder weniger Transaktionen zu verwenden?
* Wenn eine Anwendung eines der Skalierbarkeitsziele überschreiten muss, sollten Sie mehrere Speicherkonten erstellen und die Anwendungsdaten auf mehrere Speicherkonten aufteilen. Konzipieren Sie in diesem Fall die Anwendung so, dass Sie künftig weitere Speicherkonten für den Lastenausgleich hinzufügen können. Zum Redaktionszeitpunkt konnte jedes Azure-Abonnement über bis zu 100 Speicherkonten verfügen.  Speicherkonten verursachen keine Kosten außer die Nutzung in Bezug auf gespeicherte Daten, durchgeführte Transaktionen und übertragene Daten.
* Wenn Ihre Anwendung das Bandbreitenziel erreicht, versuchen Sie, die Daten im Client zu komprimieren, um die erforderliche Bandbreite zum Senden der Daten an den Speicherdienst zu reduzieren.  Obwohl dies Bandbreite spart und die Netzwerkleistung erhöhen kann, kann es auch negative Auswirkungen haben.  Sie sollten die Leistungsauswirkung in Bezug auf die zusätzliche Verarbeitung beim Komprimieren und Dekomprimieren der Daten im Client beobachten. Außerdem kann das Speichern komprimierter Daten die Fehlerbehebung erschweren, da es schwieriger sein kann, gespeicherte Daten mithilfe von Standardtools anzuzeigen.
* Wenn Ihre Anwendung die Skalierbarkeitsziele erreicht, stellen Sie sicher, dass Sie exponentiell ansteigende Wartezeiten für Wiederholungsversuche verwenden (siehe [Wiederholungsversuche](#subheading14)).  Sie sollten besser sicherstellen, dass Sie sich nicht an die Skalierbarkeitsziele annähern (mit einer der oben angegebenen Methoden), aber dies sorgt ebenfalls dafür, dass die Anwendung keine schnellen Wiederholungen versucht, sodass die Drosselung schlimmer wird.  

#### <a name="useful-resources"></a>Nützliche Ressourcen
Die folgenden Links enthalten zusätzliche Details zu den Skalierbarkeitszielen:

* Weitere Informationen zu Skalierbarkeitszielen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](storage-scalability-targets.md) .
* Weitere Informationen zu Speicherredundanzoptionen finden Sie unter [Azure Storage-Replikation](storage-redundancy.md) und im Blogbeitrag [Azure Storage Redundancy Options and Read Access Geo Redundant Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) (Azure Storage-Redundanzoptionen und Lesezugriff für georedundanten Speicher).
* Aktuelle Informationen zu den Preisdetails für Azure-Dienste finden Sie auf der Seite [Azure-Preise](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Partitionsbenennungskonvention
Azure Storage verwendet ein bereichsbasiertes Partitionierungsschema für Skalierung und Lastausgleich des Systems. Der Partitionsschlüssel wird zur Partitionierung von Daten in Bereiche verwendet, und diese Bereiche werden im Lastausgleich über das System verteilt. Dies bedeutet, dass Benennungskonventionen wie lexikalische Sortierung (zum Beispiel „msftpayroll“, „msftperformance“, „msftemployees“ usw.) oder mit einem Zeitstempel („log20160101“, „log20160102“, „log20160102“ usw.) sich für die Partitionen eignen, die potenziell auf dem gleichen Partitionsserver zusammengestellt werden, bis ein Lastausgleichsvorgang sie in kleinere Bereiche aufteilt. So können z.B. alle Blobs in einem Container als ein einziger Server behandelt werden, bis die Last auf diesen Blobs einen Ausgleich der Partitionsbereiche erfordert. Ebenso kann eine Gruppe leicht geladener Konten, deren Namen lexikalisch sortiert sind, von einem einzigen Server bedient werden, bis die Last auf einem oder allen diesen Konten deren Aufteilung auf mehrere Partitionsserver erfordert. Jeder Lastausgleichsvorgang kann die Latenz der Speicheraufrufe während des Vorgangs beeinflussen. Die Fähigkeit des Systems, eine plötzliche Zunahme des Datenverkehrs auf eine Partition zu bewältigen, wird durch die Skalierbarkeit eines einzelnen Partitionsservers begrenzt, bis der Lastausgleichsvorgang wirksam wird und den Partitionsschlüsselbereich ausgleicht.  

Sie können die Zahl dieser Vorgänge mit einigen bewährten Vorgehensweisen reduzieren.  

* Untersuchen Sie genau die Benennungskonvention, die Sie für Konten, Containers, Blobs, Tabellen und Warteschlangen verwenden. Erwägen Sie, Kontennamen mithilfe einer Hashfunktion, die Ihren Anforderungen am besten gerecht wird, mit einem 3-stelligen Hashpräfix zu versehen.  
* Wenn Sie Ihre Daten mit Zeitstempeln oder numerischen Bezeichnern organisieren, müssen Sie sicherstellen, dass Sie keine Nur-anfügen- oder Nur-voranstellen-Verkehrsmuster verwenden. Diese Muster eignen sich nicht für ein bereichsbasiertes Partitionierungssystem und könnten dazu führen, dass der gesamte Datenverkehr zu einer einzigen Partition geleitet und so das System an einem wirksamen Lastausgleich gehindert wird. Wenn z.B. tägliche Vorgänge ein Blobobjekt mit einem Zeitstempel wie „yyyymmdd“ nutzen, wird der gesamte Verkehr für diesen täglichen Vorgang an ein einziges Objekt geleitet, das von einem einzigen Partitionsserver bedient wird. Prüfen Sie, ob die Grenzwerte pro Blob und pro Partition Ihren Bedürfnissen entsprechen, und erwägen Sie, diesen Vorgang bei Bedarf in mehrere Blobs aufzuteilen. Auch wenn Sie Zeitreihendaten in Ihren Tabellen speichern, könnte der gesamte Verkehr an den letzten Teil des Schlüssel-Namespace weitergeleitet werden. Wenn Sie Zeitstempel oder numerische IDs verwenden müssen, versehen Sie sie mit einem 3-stelligen Hashpräfix, oder stellen Sie bei Zeitstempeln den Sekundenteil als Präfix voran, z.B. „ssyyyymmdd“. Wenn Auflistungs- und Abfragevorgänge routinemäßig ausgeführt werden, wählen Sie eine Hashfunktion, die die Anzahl der Abfragen begrenzt. In anderen Fällen ist möglicherweise ein zufälliges Präfix ausreichend.  
* Weitere Informationen über das in Azure Storage verwendete Partitionierungsschema finden Sie [hier](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)im SOSP-Dokument.

### <a name="networking"></a>Netzwerk
Auch wenn die API-Aufrufe eine Rolle spielen, haben häufig die physischen Netzwerkeinschränkungen deutliche Auswirkungen auf die Leistung. Nachfolgend sind einige der Einschränkungen beschrieben, auf die Benutzer treffen können.  

#### <a name="client-network-capability"></a>Client-Netzwerkkapazität
##### <a name="subheading2"></a>Durchsatz
Bei der Bandbreite liegt das Problem häufig in der Clientkapazität. Während beispielsweise ein einzelnes Speicherkonto 10 GBit/s oder mehr an eingehenden Daten verarbeiten kann (siehe [Bandbreiten-Skalierbarkeitsziele](#sub1bandwidth)), beträgt die Netzwerkgeschwindigkeit in einer „kleinen“ Azure-Workerrolleninstanz nur etwa 100 MBit/s. Größere Azure-Instanzen verfügen über NICs mit höherer Kapazität. Daher sollten Sie erwägen, eine größere Instanz oder mehr VMs zu verwenden, wenn Sie höhere Netzwerkgrenzwerte auf einem einzelnen Computer benötigen. Wenn Sie von einer lokalen Anwendung auf einen Speicherdienst zugreifen, gilt dieselbe Regel: Informieren Sie sich über die Netzwerkkapazität des Clientgeräts und die Netzwerkverbindung zum Azure-Speicherort. Entweder Sie optimieren diese oder Sie entwerfen die Anwendung so, dass sie innerhalb dieser Kapazitätsgrenzen arbeiten.  

##### <a name="subheading3"></a>Verbindungsqualität
Seien Sie sich wie bei jeder Netzwerknutzung bewusst, dass Netzwerkbedingungen, die zu Fehlern und Verlusten von Paketen führen, den effektiven Durchsatz verlangsamen.  Die Verwendung von WireShark oder NetMon kann bei der Diagnose dieses Problems helfen.  

##### <a name="useful-resources"></a>Nützliche Ressourcen
Weitere Informationen zu Größen von virtuellen Computern und zur zugewiesenen Bandbreite finden Sie unter [Größen für virtuelle Computer in Azure (Windows)](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) oder [Größen für virtuelle Computer in Azure (Linux )](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Standort
In jeder verteilten Umgebung wird die beste Leistung erzielt, indem der Client in der Nähe des Servers platziert wird. Zum Zugriff auf den Azure-Speicher mit der niedrigsten Latenz befindet sich der beste Standort für den Client innerhalb derselben Azure-Region. Wenn Sie beispielsweise eine Azure-Website haben, die den Azure-Speicher verwendet, sollten Sie beide innerhalb derselben Region platzieren (z. B. Westeuropa oder Südostasien). Dadurch werden Latenz und Kosten verringert – zum Redaktionszeitpunkt war die Bandbreitennutzung innerhalb einer Region kostenlos.  

Wenn Ihre Clientanwendungen nicht in Azure gehostet werden (beispielsweise Apps für Mobilgeräte oder lokale Enterprise Services), wird die Latenz ebenfalls durch Platzierung des Speicherkontos in einer Region in der Nähe der Geräte, die darauf zugreifen, reduziert. Falls Ihre Clients weit verteilt sind (z. B. einige in Nordamerika und andere in Europa), sollten Sie die Verwendung mehrerer Speicherkonten in Betracht ziehen: eines in Nordamerika und eines in Europa. Dadurch wird die Latenz für Benutzer in beiden Regionen reduziert. Diese Vorgehensweise ist einfacher zu implementieren, wenn die in der Anwendung gespeicherten Daten speziell für bestimmte Benutzer gelten und keine Datenreplikation zwischen den Speicherkonten erforderlich ist.  Für eine verbreitete Inhaltsverteilung wird ein CDN empfohlen. Weitere Details dazu finden Sie im nächsten Abschnitt.  

### <a name="subheading5"></a>Inhaltsverteilung
Manchmal muss eine Anwendung denselben Inhalt für mehrere Benutzer bereitstellen (zum Beispiel ein Produkt-Demovideo auf der Startseite einer Website), die sich entweder in derselben oder in verschiedenen Regionen befinden. In diesem Fall sollten Sie ein Content Delivery Network (CDN) wie das Azure CDN verwenden, und das CDN nutzt den Azure-Speicher als Datenursprung. Im Gegensatz zum Azure-Speicherkonto, das in genau einer Region vorhanden ist und Inhalte nicht mit niedriger Latenz an andere Regionen liefern kann, verwendet das Azure CDN Server in mehreren Rechenzentren weltweit. Darüber hinaus unterstützt ein CDN in der Regel höhere Ausgangsgrenzwerte als ein einzelnes Speicherkonto.  

Weitere Informationen zum Azure CDN finden Sie unter [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Verwenden von SAS und CORS
Wenn Sie Code, z.B. JavaScript, im Webbrowser eines Benutzers oder eine mobile App zum Zugriff auf Daten im Azure Storage autorisieren müssen, besteht die Möglichkeit, eine Anwendung in der Webrolle als Proxy zu verwenden: Das Gerät des Benutzers wird mit der Webrolle authentifiziert, die wiederum den Zugriff auf die Speicherressourcen autorisiert. Auf diese Weise müssen Sie den Speicherkontoschlüssel nicht gegenüber unsicheren Geräten offenbaren. Dies sorgt jedoch für eine deutliche Belastung der Webrolle, da alle zwischen dem Benutzergerät und dem Speicherdienst übertragenen Daten über die Webrolle weitergeleitet werden. Sie können die Verwendung einer Webrolle als Proxy für den Speicherdienst durch die Verwendung von Shared Access Signatures (SAS) vermeiden, manchmal im Zusammenhang mit Cross-Origin Resource Sharing-Headers (CORS). Durch SAS kann das Benutzergerät Anfragen mithilfe eines beschränkten Zugriffstokens direkt an den Speicherdienst stellen. Wenn beispielsweise ein Benutzer ein Foto in die Anwendung hochladen möchte, kann Ihre Webrolle ein SAS-Token generieren und an das Benutzergerät senden, um für 30 Minuten Schreibzugriff auf einen bestimmten Blob oder Container zu gewähren (danach läuft das SAS-Token ab).

Normalerweise lässt der Browser kein JavaScript auf einer Seite zu, die von einer Website auf einer Domäne gehostet wird, um bestimmte Vorgänge wie „PUT“ auf einer anderen Domäne auszuführen. Wenn Sie beispielsweise eine Webrolle unter „contosomarketing.cloudapp.net“ hosten und clientseitiges JavaScript verwenden möchten, um einen Blob in das Speicherkonto unter „contosoproducts.blob.core.windows.net“ hochzuladen, verhindert die Ursprungsrichtlinie des Browsers diesen Vorgang. CORS ist eine Browserfunktion, mit deren Hilfe die Zieldomäne (in diesem Fall das Speicherkonto) dem Browser mitteilen kann, dass sie Anfragen aus der Quelldomäne (in diesem Fall von der Webrolle) vertraut.  

Beide dieser Technologien können dabei helfen, unnötige Lasten (und Engpässe) in der Webanwendung zu vermeiden.  

#### <a name="useful-resources"></a>Nützliche Ressourcen
Weitere Informationen zu SAS finden Sie unter [Shared Access Signatures, Teil 1: Grundlegendes zum SAS-Modell](../storage-dotnet-shared-access-signature-part-1.md).  

Weitere Informationen zu CORS finden Sie unter [Cross-Origin Resource Sharing (CORS)-Support für den Azure-Speicherdienst](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Caching
#### <a name="subheading7"></a>Abrufen von Daten
In der Regel sollte das Abrufen von Daten von einem Dienst nur einmal erfolgen, nicht zweimal. Denken Sie an das Beispiel einer MVC-Webanwendung, die in einer Webrolle ausgeführt wird, die bereits einen Blob von 50 MB aus dem Speicherdienst abgerufen hat, der als Inhalt für einen Benutzer bereitgestellt wird. Die Anwendung kann anschließend entweder denselben Blob bei jeder Benutzeranforderung erneut abrufen oder ihn lokal auf der Festplatte zwischenspeichern und die zwischengespeicherte Version für nachfolgende Benutzeranforderungen erneut verwenden. Außerdem könnte die Anwendung immer, wenn ein Benutzer die Daten anfordert, eine GET-Anforderung mit einem Bedingungsheader für die Änderungszeit ausgeben, um nicht jedes Mal den gesamten Blob abzurufen, wenn er nicht geändert wurde. Dies funktioniert auch für Tabellenentitäten.  

In einigen Fällen können Sie festlegen, dass die Anwendung davon ausgehen kann, dass der Blob nach dem Abrufen für eine bestimmte Zeit gültig bleibt und dass die Anwendung während dieses Zeitraums nicht prüfen muss, ob der Blob geändert wurde.

Konfigurations-, Such- und andere Daten, die immer von der Anwendung verwendet werden, sind hervorragend für die Zwischenspeicherung geeignet.  

Ein Beispiel für das Abrufen der Blobeigenschaften mithilfe von .NET, um das letzte Änderungsdatum zu ermitteln, finden Sie unter [Festlegen und Abrufen von Eigenschaften und Metadaten](../blobs/storage-properties-metadata.md). Informationen zu bedingten Downloads finden Sie unter [Angeben von bedingten Headern für Vorgänge des Blob-Diensts](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Hochladen von Daten in Batches
In einigen Anwendungsszenarien können Sie Daten lokal aggregieren und dann regelmäßig als Batch hochladen, statt die einzelnen Daten sofort hochzuladen. Beispielsweise kann eine Webanwendung eine Protokolldatei der Aktivitäten speichern: Die Anwendung kann entweder die Details jeder Aktivität sofort als Tabellenentität hochladen (was viele Speichervorgänge erforderlich macht) oder die Aktivitätsdetails in einer lokalen Protokolldatei speichern und dann regelmäßig alle Aktivitätsdetails als Datei mit Trennzeichen in den Blob hochladen. Wenn jeder Protokolleintrag eine Größe von 1 KB hat, können Sie Tausende davon in einer einzigen „Put Blob“-Transaktion übertragen (Sie können bis zu 64 MB in einer einzigen Blob-Transaktion hochladen). Wenn der lokale Computer vor dem Upload abstürzt, besteht eine hohe Wahrscheinlichkeit, dass einige Protokolldaten verloren gehen: der Anwendungsentwickler muss bei der Entwicklung die Möglichkeit defekter Clientgeräte oder eines Fehlers beim Hochladen berücksichtigen.  Wenn die Aktivitätsdaten für bestimmte Zeitspannen heruntergeladen werden sollen (nicht nur für eine Aktivität), dann sind Blobs gegenüber Tabellen vorzuziehen.

### <a name="net-configuration"></a>.NET-Konfiguration
Für die Verwendung des .NET Frameworks sind in diesem Abschnitt einige Schnellkonfigurationseinstellungen aufgelistet, die eine deutliche Leistungsoptimierung bewirken.  Bei Verwendung anderer Sprachen sollten Sie sich informieren, ob es ähnliche Konzepte für die jeweilige Sprache gibt.  

#### <a name="subheading9"></a>Erhöhen des Standardverbindungslimits
In .NET erhöht der folgende Code das Standard-Verbindungslimit (in der Regel 2 in einer Clientumgebung oder 10 in einer Serverumgebung) auf 100. In der Regel sollten Sie den Wert auf die ungefähre Anzahl der Threads, die von der Anwendung verwendet werden, setzen.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Sie müssen das Verbindungslimit festlegen, bevor Sie die Verbindungen öffnen.  

Für andere Programmiersprachen erfahren Sie das Verbindungslimit aus der zugehörigen Dokumentation.  

Weitere Informationen finden Sie im Blogbeitrag [Webdienste: Gleichzeitige Verbindungen](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Erhöhen der Mindestanzahl von ThreadPool-Threads bei Verwendung von synchronem Code mit asynchronen Aufgaben
Dieser Code erhöht die Mindestanzahl von ThreadPool-Threads:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Weitere Informationen finden Sie unter [ThreadPool.SetMinThreads-Methode](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Nutzung der .NET 4.5 Garbage Collection
Verwenden Sie .NET 4.5 oder höher, damit die Clientanwendung die Leistungsverbesserungen bei der Server Garbage Collection nutzen kann.

Weitere Informationen finden Sie im Artikel [Eine Übersicht der Leistungsverbesserungen in .NET 4.5](https://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Uneingeschränkte Parallelität
Während Parallelität großartig für die Leistung sein kann, verwenden Sie uneingeschränkte Parallelität (ohne Beschränkung der Anzahl von Threads und/oder parallelen Anforderungen) zum Hochladen oder Herunterladen von Daten mit Vorsicht. Das gilt auch für mehrere Worker zum Zugriff auf mehrere Partitionen (Container, Warteschlangen oder Tabellenpartitionen) im selben Speicherkonto oder für den Zugriff auf mehrere Elemente in derselben Partition. Bei uneingeschränkter Parallelität kann die Kapazität des Clientgeräts oder das Skalierbarkeitsziel des Speicherkontos überschritten werden, sodass es zu höherer Latenz und Drosselung kommt.  

### <a name="subheading13"></a>Speicherclientbibliotheken und Tools
Verwenden Sie immer die neuesten von Microsoft bereitgestellten Clientbibliotheken und Tools. Zum Redaktionszeitpunkt sind Client-Bibliotheken für .NET, Windows Phone, Windows Runtime, Java und C++ sowie Vorschaubibliotheken für andere Sprachen verfügbar. Darüber hinaus hat Microsoft PowerShell-Cmdlets und Befehle der Azure-Befehlszeilenschnittstelle für die Verwendung in Azure Storage entwickelt. Microsoft entwickelt diese Tools aktiv im Hinblick auf die Leistung, hält sie auf dem aktuellen Stand der Dienstversionen und stellt sicher, dass sie viele der bewährten Leistungsmethoden intern abwickeln.  

### <a name="retries"></a>Wiederholungsversuche
#### <a name="subheading14"></a>Drosselung/Server ausgelastet
In einigen Fällen kann der Speicherdienst Ihre Anwendung drosseln oder einfach aufgrund einer vorübergehenden Bedingung nicht in der Lage sein, die Anforderung auszuführen, sodass ein Fehlercode „503 Server Busy“ oder „500 Operation Timeout“ zurückgegeben wird.  Dies kann passieren, wenn Ihre Anwendung sich an die Skalierbarkeitsziele annähert oder wenn das System Ihre partitionierten Daten neu ausgleicht, um einen höheren Durchsatz zu ermöglichen.  In der Regel sollte die Clientanwendung den Vorgang wiederholen, der einen derartigen Fehler verursacht: ein späterer erneuter Versuch kann erfolgreich verlaufen. Wenn der Speicherdienst jedoch Ihre Anwendung drosselt, weil die Skalierbarkeitsziele überschritten wurden, oder wenn der Dienst aus einem anderen Grund nicht in der Lage war, die Anforderung auszuführen, verschlimmern aggressive Wiederholungsversuche meist das Problem. Aus diesem Grund sollten Sie exponentiell ansteigende Wartezeiten verwenden (dieses Verhalten wird standardmäßig von den Clientbibliotheken verwendet). Beispielsweise kann Ihre Anwendung nach 2 Sekunden, dann nach 4 Sekunden, nach 10 Sekunden und nach 30 Sekunden einen Wiederholungsversuch starten und dann komplett aufgeben. Dieses Verhalten führt dazu, dass Ihre Anwendung die Dienstbelastung deutlich reduziert, statt Probleme weiter zu verschärfen.  

Verbindungsfehler können sofort wiederholt werden, da sie kein Ergebnis einer Drosselung sind und nur vorübergehend bestehen sollten.  

#### <a name="subheading15"></a>Nicht wiederholbare Fehler
Die Clientbibliotheken erkennen, welche Fehler Wiederholungsversuche nach sich ziehen und welche nicht. Wenn Sie Ihren eigenen Code für den Speicher-REST-API schreiben, beachten Sie, dass einige Fehler auftreten, die nicht erneut versucht werden dürfen: z. B. die Antwort 400 (Ungültige Anforderung) weist darauf hin, dass die Clientanwendung eine Anforderung gesendet hat, die nicht verarbeitet werden konnte, weil sie ein unerwartetes Format hatte. Das erneute Senden dieser Anforderung führt jedes Mal zur selben Antwort, daher ist es nicht sinnvoll, es wiederholt zu versuchen. Wenn Sie Ihren eigenen Code für die Speicher-REST-API schreiben, beachten Sie, was die Fehlercodes bedeuten und ob sie Wiederholungsversuche nach sich ziehen sollten.  

#### <a name="useful-resources"></a>Nützliche Ressourcen
Weitere Informationen zu Speicherfehlercodes finden Sie unter [Status- und Fehlercodes](https://msdn.microsoft.com/library/azure/dd179382.aspx) auf der Microsoft Azure-Website.  

## <a name="blobs"></a>Blobs (in englischer Sprache)
Zusätzlich zu den zuvor beschriebenen bewährten Vorgehensweisen für [Alle Dienste](#allservices) können die folgenden bewährten Vorgehensweisen speziell für den Blobdienst angewendet werden.  

### <a name="blob-specific-scalability-targets"></a>Blob-spezifische Skalierbarkeitsziele
#### <a name="subheading46"></a>Mehrere Clients greifen gleichzeitig auf ein einzelnes Objekt zu
Wenn eine große Anzahl von Clients gleichzeitig auf ein einzelnes Objekt zugreift, müssen Sie pro Objekt und Speicherkonto Skalierbarkeitsziele berücksichtigen. Die genaue Anzahl von Clients, die auf ein einzelnes Objekt zugreifen können, hängt von verschiedenen Faktoren ab, wie z.B. der Anzahl der Clients, die das Objekt gleichzeitig anfordern, der Größe des Objekts, der Netzwerkbedingungen usw.

Wenn das Objekt über ein CDN verteilt werden kann, wie beispielsweise Bilder oder Videos von einer Website, dann sollten Sie ein CDN verwenden. Klicken Sie [hier](#subheading5).

In anderen Szenarios (z.B. in wissenschaftlichen Simulationen, in denen die Daten vertraulich sind) haben Sie zwei Optionen. Erstens können Sie den Zugriff Ihrer Workload staffeln, sodass über einen bestimmten Zeitraum hinweg auf das Objekt zugegriffen wird (andernfalls würde der Zugriff gleichzeitig erfolgen). Zweitens können Sie das Objekt zeitweise in mehrere Speicherkonten kopieren und so den Gesamt-IOPS pro Objekt und zwischen Speicherkonten vergrößern. Mithilfe beschränkter Tests stellten wir fest, dass bis zu 25 VMs gleichzeitig einen 100-GB-Blob parallel herunterladen können (jede VM hat den Download mithilfe von 32 Threads parallelisiert). Um 100 Clients Zugriff auf das Objekt zu gewähren, kopieren Sie es zunächst in ein zweites Speicherkonto. Lassen Sie dann die ersten 50 VMs auf das erste Blob und die zweiten 50 VMs auf das zweite Blob zugreifen. Die Ergebnisse variieren je nachdem, wie sich Ihre Anwendungen verhalten. Sie sollten dies also bereits in der Entwurfsphase testen. 

#### <a name="subheading16"></a>Bandbreite und Vorgänge pro Blob
Sie können in einem einzelnen Blob bis zu maximal 60 MB/Sekunde lesen oder schreiben (dies entspricht etwa 480 MBit/s), was die Kapazität on vielen clientseitigen Netzwerken (einschließlich der physischen NIC auf dem Clientgerät) übersteigt. Außerdem unterstützt ein einzelner Blob bis zu 500 Anforderungen pro Sekunde. Wenn Sie über mehrere Clients verfügen, die denselben Blob lesen und Sie dieses Limit wahrscheinlich überschreiten, sollten Sie ein CDN für die Blob-Verteilung verwenden.  

Weitere Informationen zum Zieldurchsatz für Blobs finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Kopieren und Verschieben von Blobs
#### <a name="subheading17"></a>Kopieren von Blobs
Mit dem Speicher REST-API-Version 2012-02-12 wurde die nützliche Kopierfunktion von Blobs zwischen Konten eingefügt: Eine Clientanwendung kann den Speicherdienst anweisen, einen Blob aus einer anderen Quelle (ggf. aus einem anderen Speicherkonto) zu kopieren und dann den Dienst den Kopiervorgang asynchron durchführen lassen. Dies kann die für die Anwendung bei der Migration von Daten aus anderen Speicherkonten erforderliche Bandbreite deutlich reduzieren, da Sie die Daten nicht herunter- und wieder hochladen müssen.  

Es sollte jedoch berücksichtigt werden, dass es beim Kopieren zwischen Speicherkonten keine Zeitgarantie gibt, wann der Kopiervorgang abgeschlossen ist. Wenn Ihre Anwendung das Kopieren des Blobs schnell und kontrolliert abschließen soll, kann es besser sein, den Blob zu kopieren, indem Sie ihn auf einen virtuellen Computer herunterladen und dann auf das Ziel hochladen.  Stellen Sie für genaue Vorhersehbarkeit in dieser Situation sicher, dass der Kopiervorgang von einem virtuellen Computer in derselben Azure-Region durchgeführt wird, andernfalls kann sich die Netzwerkleistung auf die Kopierleistung auswirken.  Darüber hinaus können Sie den Fortschritt eines asynchronen Kopiervorgangs programmgesteuert überwachen.  

Kopien im selben Speicherkonto sind normalerweise sehr schnell abgeschlossen.  

Weitere Informationen finden Sie unter [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Verwenden von AzCopy
Das Azure Storage-Team hat ein Befehlszeilentool namens „AzCopy“ veröffentlicht, das die Massenübertragung von vielen Blobs von, an und über Speicherkonten hinweg unterstützen soll.  Dieses Tool ist für dieses Szenario optimiert und kann hohe Übertragungsraten erzielen.  Die Verwendung wird für Massenszenarien beim Hochladen, Herunterladen und Kopieren empfohlen. Weitere Informationen sowie den Download finden Sie unter [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Azure Import/Export-Dienst
Für große Datenmengen (mehr als 1 TB) bietet der Azure-Speicher einen Import-/Export-Dienst, der das Hochladen in und Herunterladen aus dem Blob-Speicher durch den Versand von Festplatten ermöglicht.  Sie können Ihre Daten auf eine Festplatte speichern und zum Hochladen an Microsoft senden oder eine leere Festplatte zum Herunterladen der Daten an Microsoft schicken.  Weitere Informationen finden Sie unter [Verwenden des Microsoft Azure Import/Export-Diensts zum Übertragen von Daten in den Blobspeicher](../storage-import-export-service.md).  Dies kann sehr viel effizienter als das Hochladen/Herunterladen einer solchen Datenmenge über das Netzwerk sein.  

### <a name="subheading20"></a>Verwenden von Metadaten
Der Blobdienst unterstützt Header-Anforderungen, die Metadaten zum Blob enthalten können. Wenn Ihre Anwendung beispielsweise die EXIF-Daten eines Fotos benötigt, kann sie das Foto herunterladen und diese extrahieren. Die Anwendung kann, um Bandbreite zu sparen und die Leistung zu verbessern, die EXIF-Daten in den Blob-Metadaten speichern, wenn die Anwendung das Foto hochgeladen hat. Sie können dann mit einer HEAD-Anforderung die EXIF-Daten in den Metadaten abrufen und so erhebliche Bandbreiten- und Verarbeitungszeit sparen, die jedes Mal zum Extrahieren der EXIF-Daten beim Lesen des Blob benötigt wird. Dies kann in Fällen nützlich sein, wenn Sie nur die Metadaten benötigen und nicht den vollständigen Inhalt eines Blobs.  Pro Blob können nur 8 KB Metadaten gespeichert werden (der Dienst akzeptiert keine Anforderung, mit der mehr als das gespeichert werden soll); wenn die Daten also diese Größe überschreiten, können Sie diese Vorgehensweise nicht anwenden.  

Ein Beispiel für das Abrufen der Blob-Metadaten mithilfe von .NET finden Sie unter [Festlegen und Abrufen von Eigenschaften und Metadaten](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Schnelles Hochladen
Um Blobs schnell hochzuladen, müssen Sie erst die folgende Frage beantworten: Laden Sie einen Blob hoch oder mehrere?  Befolgen Sie die Anleitung unten, um die richtige Methode für Ihr Szenario auszuwählen.  

#### <a name="subheading21"></a>Schnelles Hochladen eines großen Blobs
Um einen großen Blob schnell hochzuladen, sollte Ihre Clientanwendung ihre Blöcke oder Seiten parallel hochladen (beachten Sie dabei die Skalierbarkeitsziele für einzelne Blobs sowie für das gesamte Speicherkonto).  Die offiziell von Microsoft bereitgestellten RTM-Speicherclientbibliotheken (.NET, Java) haben die Möglichkeiten dafür.  Verwenden Sie für die einzelnen Bibliotheken die unten angegebenen Objekte/Eigenschaften, um den Grad an Parallelität festzulegen.  

* .NET: Legen Sie „ParallelOperationThreadCount“ für ein zu verwendendes BlobRequestOptions-Objekt fest.
* Java/Android: Verwenden Sie „BlobRequestOptions.setConcurrentRequestCount()“.
* Node.js: Verwenden Sie „parallelOperationThreadCount“ entweder für die Anforderungsoptionen oder für den Blobdienst.
* C++: Verwenden Sie die Methode „blob_request_options::set_parallelism_factor“.

#### <a name="subheading22"></a>Schnelles Hochladen von mehreren Blobs
Um viele Blobs schnell hochzuladen, laden Sie die Blobs parallel hoch. Dies funktioniert schneller als das Hochladen von jeweils einem Blob, dessen Blöcke parallel hochgeladen werden, da der Hochladevorgang auf mehrere Partitionen des Speicherdiensts verteilt wird. Ein einzelner Blob unterstützt einen Durchsatz von 60 MB/Sekunde (etwa 480 MBit/s). Zum Zeitpunkt der Texterstellung unterstützten US-basierte LRS-Konten bis zu 20 GBit/s für eingehende Daten, was einem viel höheren Durchsatz als bei einem einzelnen Blob entspricht.  [AzCopy](#subheading18) führt das Hochladen standardmäßig parallel aus, was für dieses Szenario ebenfalls empfohlen wird.  

### <a name="subheading23"></a>Auswählen des richtigen Blobtyps
Azure Storage unterstützt zwei Blobtypen: *Seitenblobs* und *Blockblobs*. Je nach Nutzungsszenario wirkt sich die Auswahl des Blobtyps auf die Leistung und Skalierbarkeit der Lösung aus. Block-Blobs sind geeignet, wenn Sie große Datenmengen effizient hochladen möchten: z. B. eine Clientanwendung, die Fotos oder Videos in einen Blob-Speicher hochladen muss. Seiten-Blobs sind für Anwendungen geeignet, die sequentielle Schreibvorgänge auf den Daten ausführen: z. B. werden Azure-VHDs als Seiten-Blobs gespeichert.  

Weitere Informationen finden Sie unter [Grundlagen zu Blockblobs, Anfügeblobs und Seitenblobs](https://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabellen
Zusätzlich zu den zuvor beschriebenen bewährten Vorgehensweisen für [Alle Dienste](#allservices) können die folgenden bewährten Vorgehensweisen speziell für den Tabellendienst angewendet werden.  

### <a name="subheading24"></a>Tabellenspezifische Skalierbarkeitsziele
Zusätzlich zur Bandbreiteneinschränkung des gesamten Speicherkontos haben Tabellen die folgende spezifische Skalierbarkeitsgrenze.  Das System balanciert den ansteigenden Datenverkehr aus, bei plötzlichen Verkehrsspitzen wird jedoch der komplette Durchsatz nicht sofort verfügbar.  Falls Ihr Datenmuster Spitzenlasten enthält, müssen Sie mit Drosselung und/oder Timeouts rechnen, während der Speicherdienst den automatischen Lastenausgleich für die Tabelle ausführt.  Eine langsame Steigerung zeigt in der Regel bessere Ergebnisse, da das System genügend Zeit für den Lastenausgleich hat.  

#### <a name="entities-per-second-account"></a>Entitäten pro Sekunde (Konto)
Die Skalierbarkeitsgrenze für den Tabellenzugriff liegt bei 20.000 Entitäten (je 1 KB) pro Sekunde für ein Konto.  Im Allgemeinen zählt jede Entität, die eingefügt, aktualisiert, gelöscht oder gescannt wird, für dieses Ziel.  Daher zählt eine Batcheinfügung, die 100 Entitäten enthält, als 100 Entitäten.  Eine Abfrage, die 1.000 Entitäten scannt und 5 zurückgibt, zählt als 1.000 Entitäten.  

#### <a name="entities-per-second-partition"></a>Entitäten pro Sekunde (Partition)
Innerhalb einer Partition liegt das Skalierbarkeitsziel für den Tabellenzugriff bei 2.000 Entitäten (je 1 KB) pro Sekunde, mit derselben Zählweise wie im vorherigen Abschnitt beschrieben.  

### <a name="configuration"></a>Konfiguration
In diesem Abschnitt sind verschiedene Schnellkonfigurationseinstellungen aufgelistet, die eine deutliche Leistungsoptimierung im Tabellendienst bewirken.  

#### <a name="subheading25"></a>Verwenden von JSON
Ab Speicherdienstversion 2013-08-15 unterstützt der Tabellendienst die Verwendung von JSON anstelle des XML-basierten AtomPub-Formats für die Übertragung von Tabellendaten. Dies kann die Nutzlast um bis zu 75 % verringern und die Anwendungsleistung deutlich verbessern.

Weitere Informationen finden Sie im Beitrag [Microsoft Azure Tables: Introducing JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) (Microsoft Azure-Tabellen: Einführung in JSON) und [Payload Format for Table Service Operations](https://msdn.microsoft.com/library/azure/dn535600.aspx) (Nutzlastformat für Tabellendienstvorgänge).

#### <a name="subheading26"></a>Deaktivieren von Nagle
Der Nagle-Algorithmus ist in TCP/IP-Netzwerken weit verbreitet, um die Netzwerkleistung zu verbessern. Er ist jedoch nicht unter allen Umständen optimal (z. B. hoch interaktive Umgebungen). Für Azure Storage hat der Nagle-Algorithmus negative Auswirkungen auf Anforderungen an Tabellen- und Warteschlangendienste, daher sollten Sie ihn möglichst deaktivieren.

### <a name="schema"></a>Schema
Wie Sie Ihre Daten präsentieren und abfragen, ist der größte Faktor mit Auswirkung auf die Leistung des Tabellendiensts. Während jede Anwendung anders ist, sind in diesem Abschnitt einige allgemeine bewährte Vorgehensweisen für folgende Bereiche beschrieben:  

* Tabellenentwurf
* Effiziente Abfragen
* Effiziente Datenaktualisierungen  

#### <a name="subheading27"></a>Tabellen und Partitionen
Tabellen sind in Partitionen unterteilt. Jede in einer Partition gespeicherte Entität verwendet denselben Partitionsschlüssel und hat einen eindeutigen Zeilenschlüssel, damit sie innerhalb der Partition identifiziert werden kann. Partitionen bieten Vorteile, führen jedoch auch zu Skalierbarkeitsgrenzen.  

* Vorteile: Sie können die Entitäten innerhalb derselben Partition in einer einzigen, unteilbaren Batchtransaktion aktualisieren, die bis zu 100 separate Speichervorgänge enthalten kann (Gesamtgrößenlimit 4 MB). Bei derselben Menge abzurufender Entitäten können Sie auch die Daten innerhalb einer Partition effizienter abfragen als Daten, die sich über mehrere Partitionen verteilen (lesen Sie für noch mehr Empfehlungen zur Abfrage von Tabellendaten weiter).
* Skalierbarkeitsgrenze: Für den Zugriff auf Entitäten, die in einer einzigen Partition gespeichert sind, kann kein Lastenausgleich erfolgen, da die Partitionen unteilbare Batchtransaktionen unterstützen. Aus diesem Grund ist das Skalierbarkeitsziel für eine einzelne Tabellenpartition niedriger als für den gesamten Tabellendienst.  

Aufgrund dieser Eigenschaften von Tabellen und Partitionen sollten Sie die folgenden Entwurfsprinzipien beachten:  

* Daten, die Ihre Clientanwendung häufig in derselben logischen Arbeitseinheit aktualisiert oder abfragt, sollten sich in derselben Partition befinden.  Der Grund dafür kann sein, dass Ihre Anwendung Schreibvorgänge aggregiert oder dass Sie unteilbare Batchtransaktionen nutzen möchten.  Außerdem können Daten innerhalb einer Partition effizienter mit nur einer Abfrage abgefragt werden als Daten, die sich über mehrere Partitionen verteilen.
* Daten, die Ihre Clientanwendung nicht in derselben logischen Arbeitseinheit einfügt/aktualisiert (Einzelabfrage oder Batchaktualisierung), sollten sich in separaten Partitionen befinden.  Ein wichtiger Hinweis ist hier, dass es keine Begrenzung der Anzahl der Partitionsschlüssel in einer Tabelle gibt, daher sind selbst Millionen von Partitionsschlüsseln kein Problem und wirken sich nicht auf die Leistung aus.  Wenn es sich bei Ihrer Anwendung beispielsweise um eine beliebte Website mit Benutzeranmeldung handelt, kann die Benutzer-ID als Partitionsschlüssel verwendet werden.  

#### <a name="hot-partitions"></a>Hot Partition
Eine Hot Partition ist eine Partition, die einen überproportionalen Anteil des Datenverkehrs an ein Konto empfängt und kein Lastenausgleich erfolgen kann, da es sich um eine einzelne Partition handelt.  Im Allgemeinen wird eine Hot Partition mit einer der folgenden Möglichkeiten erstellt:  

##### <a name="subheading28"></a>Nur-Anhängen/Nur-Voranstellen-Muster
Das Muster „Nur anhängen“ bezieht sich darauf, dass (fast) der gesamte Datenverkehr an einen bestimmten PS abhängig von der aktuellen Zeit ansteigt oder absinkt.  Ein Beispiel ist eine Anwendung, in der das aktuelle Datum als Partitionsschlüssel für Protokolldaten verwendet wird.  Das führt dazu, dass alle Einfügungen in die letzte Partition der Tabelle eingefügt werden und das System keinen Lastenausgleich durchführen kann, da alle Schreibvorgänge am Ende der Tabelle erfolgen.  Wenn das Datenvolumen in dieser Partition das Skalierbarkeitsziel auf Partitionsebene überschreitet, kommt es zu einer Drosselung.  Es ist besser sicherzustellen, dass der Datenverkehr an mehrere Partitionen geleitet wird, um den Lastenausgleich für Anfragen in der Tabelle zu verteilen.  

##### <a name="subheading29"></a>Hoher Datenverkehr
Wenn Ihr Partitionsschema dazu führt, dass in einer einzigen Partition Daten enthalten sind, die deutlich häufiger als in anderen Partitionen verwendet werden, kann ebenfalls eine Drosselung erfolgen, wenn diese Partition das Skalierbarkeitsziel für eine einzelne Partition erreicht.  Sie sollten besser sicherstellen, dass Ihr Partitionsschema so angelegt ist, dass sich keine einzelne Partition an die Skalierbarkeitsziele annähert.  

#### <a name="querying"></a>Abfragen
In diesem Abschnitt werden bewährte Vorgehensweisen für Tabellendienstabfragen erläutert.  

##### <a name="subheading30"></a>Abfragebereich
Es gibt mehrere Möglichkeiten, den Entitätsbereich für die Abfrage anzugeben.  Im Folgenden werden diese einzeln erläutert.  

In der Regel sollten Sie Scans vermeiden (Abfragen, die sich auf mehr als eine Entität beziehen), wenn Sie jedoch scannen müssen, organisieren Sie Ihre Daten so, dass der Scan die benötigten Daten abruft, ohne eine übermäßige Menge an nicht benötigten Entitäten zurückzugeben.  

###### <a name="point-queries"></a>Punktabfragen
Eine Punktabfrage ruft genau eine Entität ab. Dazu wird sowohl der Partitionsschlüssel als auch der Zeilenschlüssel der abzurufenden Entität angegeben. Diese Abfragen sind effizient und Sie sollten diese so oft wie möglich verwenden.  

###### <a name="partition-queries"></a>Partitionsabfragen
Eine Partitionsabfrage ist eine Abfrage, die eine Gruppe von Daten mit einem gemeinsamen Partitionsschlüssel abruft. In der Regel gibt die Abfrage einen Wertebereich für den Zeilenschlüssel oder für eine Entitätseigenschaft zusätzlich zum Partitionsschlüssel an. Diese sind weniger effizient als Punktabfragen und ihr Einsatz sollte sorgfältig überlegt werden.  

###### <a name="table-queries"></a>Tabellenabfragen
Eine Tabellenabfrage ist eine Abfrage, die eine Gruppe von Entitäten ohne gemeinsamen Partitionsschlüssel abruft. Diese Abfragen sind nicht effizient und Sie sollten diese möglichst vermeiden.  

##### <a name="subheading31"></a>Abfragedichte
Ein weiterer Schlüsselfaktor bei der Abfrageeffizienz ist die Anzahl der zurückgegebenen Entitäten im Vergleich zur Anzahl der gescannten Entitäten, um den zurückgegebenen Satz zu suchen. Wenn Ihre Anwendung eine Tabellenabfrage mit einem Filter für einen Eigenschaftswert durchführt, den nur 1 % der Daten teilen, scannt die Abfrage 100 Entitäten für jede zurückgegebene Entität. Die zuvor erläuterten Skalierbarkeitsziele der Tabelle beziehen sich alle auf die Anzahl der durchsuchten und nicht der zurückgegebenen Entitäten: Eine geringe Abfragedichte kann leicht dazu führen, dass der Tabellendienst die Anwendung einschränkt, weil er viele Entitäten durchsuchen muss, um die von Ihnen gesuchte Entität zu finden.  Hinweise, wie Sie dies vermeiden können, finden Sie unten im Abschnitt [Denormalisierung](#subheading34) .  

##### <a name="limiting-the-amount-of-data-returned"></a>Einschränken der Menge der zurückgegebenen Daten
###### <a name="subheading32"></a>Filterung
Wenn Sie wissen, dass eine Abfrage Entitäten zurückgibt, die Sie in der Clientanwendung nicht benötigen, sollten Sie einen Filter verwenden, um die Größe des zurückgegebenen Sets zu reduzieren. Während die nicht an den Client zurückgegebenen Entitäten trotzdem für die Skalierbarkeitsgrenzen zählen, wird sich Ihre Anwendungsleistung aufgrund der reduzierten Netzwerknutzlast und der reduzierten Entitätenanzahl, die von der Clientanwendung verarbeitet werden müssen, verbessern.  Lesen Sie trotzdem oben den Hinweis zur [Abfragedichte](#subheading31)– die Skalierbarkeitsziele beziehen sich auf die Anzahl der gescannten Entitäten, daher kann eine Abfrage, bei der viele Entitäten herausgefiltert werden, trotzdem zur Drosselung führen, auch wenn nur wenige Entitäten zurückgegeben werden.  

###### <a name="subheading33"></a>Projektion
Wenn Ihre Clientanwendung nur bestimmte Eigenschaften der Entitäten in der Tabelle benötigt, können Sie Projektion verwenden, um die Größe des zurückgegebenen Datensatzes einzuschränken. Wie bei der Filterung hilft auch dies bei der Reduzierung der Netzwerklast und der Clientverarbeitung.  

##### <a name="subheading34"></a>Denormalisierung
Im Gegensatz zum Arbeiten mit relationalen Datenbanken führen die bewährten Vorgehensweisen für das effiziente Abfragen von Tabellendaten zur Denormalisierung Ihrer Daten. Das heißt, dieselben Daten werden in mehreren Entitäten dupliziert (eine für jeden Schlüssel, den Sie zum Suchen der Daten verwenden könnten), um die Anzahl der Entitäten zu minimieren, die eine Abfrage scannen muss, um die vom Client benötigten Daten zu finden, statt eine große Anzahl von Entitäten zu scannen, um die für die Anwendung benötigten Daten zu finden.  Beispielsweise kann auf einer E-Commerce-Website eine Bestellung sowohl nach Kunden-ID (zur Abfrage der Kundenbestellungen) als auch nach Datum (zur Abfrage der Bestellungen für ein bestimmtes Datum) gefunden werden.  Bei der Tabellenspeicherung ist es am besten, die Entität zweimal zu speichern (bzw. darauf zu verweisen) – einmal mit Tabellenname, PS und ZS für die Suche nach der Kunden-ID und einmal für die Suche nach dem Datum.  

#### <a name="insertupdatedelete"></a>Einfügen/Aktualisieren/Löschen
In diesem Abschnitt werden bewährte Vorgehensweise für das Ändern von im Tabellendienst gespeicherte Entitäten erläutert.  

##### <a name="subheading35"></a>Batchverarbeitung
Batchtransaktionen werden in Azure-Speicher als Entitätsgruppentransaktionen (ETG) bezeichnet. Alle Vorgänge innerhalb einer ETG müssen sich auf eine einzige Partition in einer einzigen Tabelle beziehen. Wenn möglich, verwenden Sie ETGs für die Durchführung von Einfüge-, Aktualisierungs- und Löschvorgängen in Batches. Dadurch reduziert sich die Anzahl der Roundtrips von Ihrer Clientanwendung zum Server und somit die Anzahl der kostenpflichtigen Transaktionen (eine ETG zählt zu Abrechnungszwecken als eine Transaktion und kann bis zu 100 Speichervorgänge umfassen. Außerdem werden unteilbare Aktualisierungen ermöglicht (alle Vorgänge in einer ETG sind erfolgreich oder schlagen fehl). Umgebungen mit hoher Latenz wie beispielsweise Mobilgeräte profitieren stark von der Verwendung von ETGs.  

##### <a name="subheading36"></a>Upsert
Verwenden Sie wenn möglich den Tabellenvorgang **Upsert** zum Einfügen und Aktualisieren. Es gibt zwei **Upsert**-Typen, die beide effizienter als herkömmliche **Insert**- und **Update**-Vorgänge sind:  

* **InsertOrMerge**: Verwenden Sie diesen Aufruf, wenn Sie eine Teilmenge der Entitätseigenschaften hochladen möchten, aber nicht sicher sind, ob die Entität bereits vorhanden ist. Falls die Entität vorhanden ist, aktualisiert dieser Aufruf die in **Upsert** enthaltenen Eigenschaften und lässt alle vorhandenen Eigenschaften unverändert – falls die Entität nicht vorhanden ist, wird die neue Entität eingefügt. Dies funktioniert ähnlich wie die Projektion in einer Abfrage, da Sie nur die geänderten Eigenschaften hochladen müssen.
* **InsertOrReplace**: Verwenden Sie diesen Aufruf, wenn Sie eine neue Entität hochladen möchten, aber nicht sicher sind, ob diese bereits vorhanden ist. Sie sollten diese Option nur verwenden, wenn Sie wissen, dass die neu hochgeladene Entität komplett richtig ist, da sie die alte Entität vollständig überschreibt. Wenn Sie beispielsweise die Entität aktualisieren möchten, die den aktuellen Standort eines Benutzers speichert, unabhängig davon, ob die Anwendung zuvor Standortdaten für den Benutzer gespeichert hatte, ist die neue Entität komplett, da Sie keine Informationen aus der vorherigen Entität benötigen.

##### <a name="subheading37"></a>Speichern von Datenreihen in einer einzigen Entität
Manchmal speichert eine Anwendung eine Reihe von Daten, die sie häufig in einem Schritt abrufen muss: Beispielsweise kann eine Anwendung die CPU-Auslastung über einen bestimmten Zeitraum erfassen, um ein fortlaufendes Diagramm der Daten aus den letzten 24 Stunden zu plotten. Eine Möglichkeit ist es, eine Tabellenentität pro Stunde anzulegen, sodass jede Entität eine bestimmte Stunde darstellt und die CPU-Auslastung für diese Stunde speichert. Um diese Daten zu plotten, muss die Anwendung die Entitäten abrufen, welche die Daten aus den letzten 24 Stunden enthalten.  

Alternativ kann Ihre Anwendung die CPU-Nutzung für jede Stunde als separate Eigenschaft einer einzelnen Entität speichern: für stündliche Aktualisierungen kann die Anwendung einzeln **InsertOrMerge Upsert** abrufen, um den Wert für die aktuelle Stunde zu aktualisieren. Um die Daten zu plotten, muss die Anwendung nur eine einzige Entität statt 24 abrufen, sodass die Abfrage effizient verläuft (siehe Erläuterungen oben zum [Abfragebereich](#subheading30)).

##### <a name="subheading38"></a>Speichern strukturierter Daten in Blobs
Manchmal hat man das Gefühl, strukturierte Daten sollten immer in Tabellen gespeichert werden, aber Entitätsbereiche werden immer zusammen abgerufen und können als Batch eingefügt werden.  Ein gutes Beispiel dafür ist eine Protokolldatei.  In diesem Fall können Sie Protokolle für mehrere Minuten als Batch zusammenfassen, einfügen und dann immer mehrere Protokollminuten gleichzeitig abrufen.  Aus Leistungsgründen ist es in diesem Fall besser, Blobs statt Tabellen zu verwenden, da Sie damit die Anzahl der geschriebenen/zurückgegebenen Objekte sowie normalerweise auch die Anzahl der gestellten Anfragen deutlich reduzieren können.  

## <a name="queues"></a>Warteschlangen
Zusätzlich zu den zuvor beschriebenen bewährten Vorgehensweisen für [Alle Dienste](#allservices) können die folgenden bewährten Vorgehensweisen speziell für den Warteschlangendienst angewendet werden.  

### <a name="subheading39"></a>Skalierbarkeitsgrenze
Eine einzelne Warteschlange kann ca. 2.000 Nachrichten (1 KB jeweils) pro Sekunde verarbeiten (jede AddMessage, GetMessage und DeleteMessage zählen hier als eine Nachricht). Wenn dies für Ihre Anwendung nicht ausreicht, verwenden Sie mehrere Warteschlangen und verteilen Sie die Nachrichten auf diese.  

Zeigen Sie aktuelle Skalierbarkeitsziele unter [Skalierbarkeits- und Leistungsziele für Azure Storage](storage-scalability-targets.md)an.  

### <a name="subheading40"></a>Deaktivieren von Nagle
Lesen Sie den Abschnitt über den Nagle-Algorithmus in der Tabellenkonfiguration – der Nagle-Algorithmus wirkt sich meist negativ auf die Leistung von Warteschlangenanforderungen aus, daher sollten Sie ihn deaktivieren.  

### <a name="subheading41"></a>Nachrichtengröße
Die Leistung der Warteschlange und der Skalierbarkeit sinkt, wenn die Nachrichtengröße steigt. Fügen Sie einer Nachricht nur die Informationen hinzu, die der Empfänger benötigt.  

### <a name="subheading42"></a>Batchabruf
Sie können bis zu 32 Nachrichten aus einer Warteschlange in einem einzigen Vorgang abrufen. Dies kann die Anzahl der Roundtrips von der Clientanwendung reduzieren, was besonders für Umgebungen mit hoher Latenz wie beispielsweise Mobilgeräte nützlich ist.  

### <a name="subheading43"></a>Abrufintervall für die Warteschlange
Die meisten Anwendungen fragen Nachrichten aus einer Warteschlange ab. Für die Anwendung kann es sich dabei um die größte Quelle für Transaktionen handeln. Wählen Sie das Abrufintervall mit Bedacht aus: Abrufe, die zu häufig stattfinden, können dazu führen, dass die Anwendung die Skalierbarkeitsziele für die Warteschlange erreicht. Bei 200.000 Transaktionen für 0,01 US-Dollar (zum Redaktionszeitpunkt) betragen die Kosten für einen einzelnen Prozessor, der im Monat einen Abruf pro Sekunde macht, jedoch weniger als 15 Cent. Daher sind Kosten üblicherweise kein Auswahlkriterium für das Abrufintervall.  

Aktuelle Datenkosteninformationen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Sie können **UpdateMessage** verwenden, um das Unsichtbarkeits-Zeitlimit zu erhöhen oder die Statusinformationen einer Nachricht zu aktualisieren. Trotz der Möglichkeiten dieser Option sollten Sie beachten, dass jeder **UpdateMessage** -Vorgang für das Skalierbarkeitsziel zählt. Dies kann jedoch sehr viel effizienter sein als ein Workflow, der einen Auftrag von einer Warteschlange zur nächsten verschiebt, wenn der jeweilige Schritt abgeschlossen ist. Mithilfe des Vorgangs **UpdateMessage** kann Ihre Anwendung den Auftragsstatus in der Nachricht speichern und dann weiterarbeiten, statt die Nachricht jedes Mal für den nächsten Schritt erneut in die Warteschlange zu stellen.  

Weitere Informationen finden Sie in diesem Artikel: [ Ändern des Inhalts von Nachrichten in der Warteschlange](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Anwendungsarchitektur
Verwenden Sie Warteschlangen, um die Anwendungsarchitektur skalierbar zu machen. Nachfolgend sind einige Möglichkeiten aufgelistet, wie Sie Warteschlangen verwenden können, um Ihre Anwendung skalierbarer zu gestalten:  

* Sie können Warteschlangen verwenden, um Arbeits-Backlogs zur Verarbeitung zu erstellen und die Arbeitsauslastung in der Anwendung gleichmäßig zu verteilen. Sie können z. B. Benutzeranfragen in die Warteschlange stellen, um prozessorintensive Arbeiten durchzuführen, wie das Ändern der Größe hochgeladener Bilder.
* Sie können Warteschlangen verwenden, um Teile Ihrer Anwendung zu entkoppeln, damit diese unabhängig skaliert werden können. Beispielsweise kann ein Web-Front-End Umfrageergebnisse von Benutzern zur späteren Analyse und Speicherung in eine Warteschlange stellen. Sie können mehr Workerrollen-Instanzen hinzufügen, um die Warteschlangendaten nach Bedarf zu verarbeiten.  

## <a name="conclusion"></a>Zusammenfassung
In diesem Artikel wurden einige der häufigsten bewährten Vorgehensweisen zur Leistungsoptimierung bei der Verwendung des Azure-Speichers erläutert. Wir empfehlen jedem Anwendungsentwickler, seine Anwendung anhand dieser Vorgehensweisen zu überprüfen und ggf. die Empfehlungen umzusetzen, um eine bessere Leistung für ihre Anwendungen zu erzielen, die Azure Storage verwenden.
