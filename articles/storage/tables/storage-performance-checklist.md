---
title: 'Checkliste zu Leistung und Skalierbarkeit für Table Storage: Azure Storage'
description: Eine Checkliste mit bewährten Methoden für die Entwicklung leistungsstarker Anwendungen mit Table Storage.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 71b1f3cfa1df86b417c468d56f67cd7fe8d71d73
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004703"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Checkliste zu Leistung und Skalierbarkeit für Table Storage

Microsoft hat eine Reihe bewährter Methoden für die Entwicklung leistungsstarker Anwendungen mit Table Storage zusammengestellt. Diese Checkliste enthält wichtige Methoden, mit denen Entwickler die Leistung optimieren können. Beachten Sie diese Methoden beim Entwerfen Ihrer Anwendung und während des gesamten Prozesses.

Azure Storage verfügt über Skalierbarkeits- und Leistungsziele für die Kapazität, Transaktionsrate und Bandbreite. Weitere Informationen zu Azure Storage-Skalierbarkeitszielen finden Sie unter [Skalierbarkeits- und Leistungsziele für Standardspeicherkonten](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) und [Skalierbarkeits- und Leistungsziele für Blob Storage](scalability-targets.md).

## <a name="checklist"></a>Checkliste

In diesem Artikel werden bewährte Methoden für die Leistung in einer Checkliste zusammengefasst, an der Sie sich beim Entwickeln Ihrer Table Storage-Anwendung orientieren können.

| Vorgehensweise | Category | Überlegungen zum Entwurf |
| --- | --- | --- |
| &nbsp; |Skalierbarkeitsziele |[Können Sie Ihre Anwendung so entwerfen, dass sie nicht mehr als die maximale Anzahl von Speicherkonten verwendet?](#maximum-number-of-storage-accounts) |
| &nbsp; |Skalierbarkeitsziele |[Vermeiden Sie es, die Kapazitäts- und Transaktionsgrenzwerte zu erreichen?](#capacity-and-transaction-targets) |
| &nbsp; |Skalierbarkeitsziele |[Nähern Sie sich den Skalierbarkeitszielen für Entitäten pro Sekunde?](#targets-for-data-operations) |
| &nbsp; |Netzwerk |[Haben clientseitige Geräte genügend Bandbreite und ist die Wartezeit gering genug, um die erforderliche Leistung zu erzielen?](#throughput) |
| &nbsp; |Netzwerk |[Ist die Qualität der Netzwerkverbindung von clientseitigen Geräten gut?](#link-quality) |
| &nbsp; |Netzwerk |[Befindet sich die Clientanwendung in der gleichen Region wie das Speicherkonto?](#location) |
| &nbsp; |Direkter Clientzugriff |[Verwenden Sie Shared Access Signatures (SAS) und Cross-Origin Resource Sharing (CORS), um den direkten Zugriff auf Azure Storage zu ermöglichen?](#sas-and-cors) |
| &nbsp; |Batching |[Führt Ihre Anwendung Batchaktualisierungen mithilfe von Entitätsgruppentransaktionen durch?](#batch-transactions) |
| &nbsp; |.NET-Konfiguration |[Verwenden Sie .NET Core 2.1 oder höher, um eine optimale Leistung zu erzielen?](#use-net-core) |
| &nbsp; |.NET-Konfiguration |[Haben Sie Ihren Client zur Verwendung einer ausreichenden Anzahl gleichzeitiger Verbindungen konfiguriert?](#increase-default-connection-limit) |
| &nbsp; |.NET-Konfiguration |[Für .NET-Anwendungen: Haben Sie .NET für die Verwendung einer ausreichenden Anzahl von Threads konfiguriert?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallelität |[Haben Sie sichergestellt, dass die Parallelität entsprechend begrenzt ist, sodass weder die Clientkapazitäten noch die Skalierbarkeitsziele überschritten werden?](#unbounded-parallelism) |
| &nbsp; |Tools |[Verwenden Sie die aktuellen Versionen der von Microsoft bereitgestellten Clientbibliotheken und -tools?](#client-libraries-and-tools) |
| &nbsp; |Wiederholungsversuche |[Verwenden Sie eine Wiederholungsrichtlinie mit exponentiellem Backoff für Drosselungsfehler und Timeouts?](#timeout-and-server-busy-errors) |
| &nbsp; |Wiederholungsversuche |[Vermeidet Ihre Anwendung Wiederholungsversuche für nicht wiederholbare Fehler?](#non-retryable-errors) |
| &nbsp; |Konfiguration |[Verwenden Sie JSON für Ihre Tabellenanforderungen?](#use-json) |
| &nbsp; |Konfiguration |[Haben Sie den Nagle-Algorithmus deaktiviert, um die Leistung kleiner Anforderungen zu verbessern?](#disable-nagle) |
| &nbsp; |Tabellen und Partitionen |[Haben Sie Ihre Daten richtig partitioniert?](#schema) |
| &nbsp; |Heiße Partitionen |[Vermeiden Sie Nur-Anhängen/Nur-Voranstellen-Muster?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Heiße Partitionen |[Verteilen sich Ihre Einfügungen/Aktualisierungen auf mehrere Partitionen?](#high-traffic-data) |
| &nbsp; |Abfragebereich |[Haben Sie Ihr Schema so konzipiert, dass in den meisten Fällen Punktabfragen verwendet werden können und Tabellenabfragen nur sparsam erfolgen?](#query-scope) |
| &nbsp; |Abfragedichte |[Erfassen Ihre Abfragen normalerweise nur Zeilen für die Rückgabe, die Ihre Anwendung auch verwenden wird?](#query-density) |
| &nbsp; |Einschränken der zurückgegebenen Daten |[Verwenden Sie Filterung, um die Rückgabe nicht benötigter Entitäten zu vermeiden?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Einschränken der zurückgegebenen Daten |[Verwenden Sie Projektion, um die Rückgabe nicht benötigter Eigenschaften zu vermeiden?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalisierung |[Haben Sie Ihre Daten so normalisiert, dass Sie ineffiziente Abfragen oder mehrere Leseanforderungen beim Abrufen von Daten vermeiden?](#denormalization) |
| &nbsp; |Einfügen, Aktualisieren und Löschen |[Verwenden Sie Batchverarbeitung für Anforderungen, die transaktional oder gleichzeitig erfolgen, um Roundtrips zu reduzieren?](#batching) |
| &nbsp; |Einfügen, Aktualisieren und Löschen |[Vermeiden Sie das Abrufen von Entitäten, nur um zu bestimmen, ob Einfügen oder Aktualisieren erforderlich ist?](#upsert) |
| &nbsp; |Einfügen, Aktualisieren und Löschen |[Haben Sie überlegt, Datenreihen, die häufig zusammen abgerufen werden, in einer einzigen Entität als Eigenschaften zu speichern statt in mehreren Entitäten?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Einfügen, Aktualisieren und Löschen |[Haben Sie überlegt, für Entitäten, die immer zusammen abgerufen werden und in Batches geschrieben werden können (zum Beispiel Zeitreihendaten), Blobs statt Tabellen zu verwenden?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Skalierbarkeitsziele

Wenn Ihre Anwendung eines der Skalierbarkeitsziele erreicht oder überschreitet, kann es zu erhöhter Transaktionslatenz oder Drosselung kommen. Wenn Azure Storage Ihre Anwendung drosselt, beginnt der Dienst, die Fehlercodes 503 (Server ausgelastet) oder 500 (Timeout bei Vorgang) zurückzugeben. Diese Fehler zu vermeiden, indem Sie innerhalb der Grenzwerte der Skalierbarkeitsziele bleiben, trägt maßgeblich dazu bei, die Leistung Ihrer Anwendung zu verbessern.

Weitere Informationen zu den Skalierbarkeitszielen für den Tabellenspeicherdienst finden Sie unter [Skalierbarkeits- und Leistungsziele für Table Storage](scalability-targets.md).

### <a name="maximum-number-of-storage-accounts"></a>Maximale Anzahl von Speicherkonten

Wenn Sie die maximal zulässige Anzahl von Speicherkonten für eine bestimmte Kombination aus Abonnement und Region fast erreicht haben: Verwenden Sie mehrere Speicherkonten zur horizontalen Partitionierung, um den ein- und ausgehenden Datenverkehr, die E/A-Vorgänge pro Sekunde (IOPS) oder die Kapazität zu erhöhen? In diesem Szenario empfiehlt Microsoft, höhere Grenzwerte für Speicherkonten zu nutzen, um möglichst die Anzahl der Speicherkonten zu verringern, die für Ihre Workload erforderlich sind. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/options/), wenn Sie höhere Grenzwerte für Ihr Speicherkonto benötigen. Weitere Informationen finden Sie unter [Ankündigung größerer Speicherkonten mit höherer Skalierung](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapazitäts- und Transaktionsziele

Wenn sich Ihre Anwendung den Skalierbarkeitszielen für ein Speicherkonto nähert, sollten Sie eine der folgenden Vorgehensweisen wählen:  

- Berücksichtigen Sie die Arbeitsauslastung, aufgrund derer Ihre Anwendung das Skalierbarkeitsziel erreicht oder überschreitet. Können Sie diese anders konzipieren, um weniger Bandbreite bzw. Kapazität oder weniger Transaktionen zu verwenden?
- Wenn Ihre Anwendung eines der Skalierbarkeitsziele überschreiten muss, erstellen Sie mehrere Speicherkonten, und partitionieren Sie Ihre Anwendungsdaten über diese Speicherkonten hinweg. Konzipieren Sie in diesem Fall die Anwendung so, dass Sie künftig weitere Speicherkonten für den Lastenausgleich hinzufügen können. Speicherkonten selbst verursachen abgesehen von den Nutzungskosten für gespeicherte Daten, durchgeführte Transaktionen oder übertragene Daten keine Kosten.
- Wenn sich Ihre Anwendung den Bandbreitenzielen nähert, versuchen Sie, Daten clientseitig zu komprimieren, um die erforderliche Bandbreite zum Senden der Daten an Azure Storage zu reduzieren.
    Die Komprimierung von Daten kann zwar Bandbreite sparen und die Netzwerkleistung verbessern, aber auch negative Auswirkungen auf die Leistung haben. Beobachten Sie, wie sich der zusätzliche Verarbeitungsbedarf für das Komprimieren und Dekomprimieren der Daten auf dem Client auf die Leistung auswirkt. Bedenken Sie, dass das Speichern von komprimierten Daten die Problembehandlung erschweren kann, da es schwieriger sein kann, die Daten mithilfe von Standardtools anzuzeigen.
- Wenn sich Ihre Anwendung den Skalierbarkeitszielen nähert, sollten Sie unbedingt ein exponentielles Backoff für Wiederholungsversuche verwenden. Sie sollten die in diesem Artikel beschriebenen Empfehlungen umsetzen, um zu verhindern, dass die Skalierbarkeitsziele erreicht werden. Wenn Sie ein exponentielles Backoff für Wiederholungsversuche verwenden, kann Ihre Anwendung Vorgänge jedoch nicht schnell wiederholen, wodurch sich die Drosselung verschlechtern kann. Weitere Informationen finden Sie im Abschnitt [Timeoutfehler und Fehler durch ausgelasteten Server](#timeout-and-server-busy-errors).

### <a name="targets-for-data-operations"></a>Ziele für Datenvorgänge

Azure Storage führt einen Lastenausgleich durch, wenn der Datenverkehr in Ihr Speicherkonto zunimmt, aber wenn die Zunahme sehr plötzlich und explosionsartig in Form eines Bursts erfolgt, ist es eventuell nicht möglich, die erforderliche Durchsatzmenge sofort zu erhalten. Während eines solchen Bursts können Drosselung und/oder Timeouts auftreten, weil Azure Storage automatisch einen Lastenausgleich für Ihre Tabelle durchführt. Eine langsame Steigerung zeigt in der Regel bessere Ergebnisse, da das System genügend Zeit für den Lastenausgleich hat.

#### <a name="entities-per-second-storage-account"></a>Entitäten pro Sekunde (Speicherkonto)

Die Skalierbarkeitsgrenze für den Tabellenzugriff liegt bei 20.000 Entitäten (je 1 KB) pro Sekunde für ein Konto. Im Allgemeinen zählt jede Entität, die eingefügt, aktualisiert, gelöscht oder gescannt wird, für dieses Ziel. Daher zählt eine Batcheinfügung, die 100 Entitäten enthält, als 100 Entitäten. Eine Abfrage, die 1.000 Entitäten scannt und 5 zurückgibt, zählt als 1.000 Entitäten.

#### <a name="entities-per-second-partition"></a>Entitäten pro Sekunde (Partition)

Innerhalb einer Partition liegt das Skalierbarkeitsziel für den Tabellenzugriff bei 2.000 Entitäten (je 1 KB) pro Sekunde, mit derselben Zählweise wie im vorherigen Abschnitt beschrieben.

## <a name="networking"></a>Netzwerk

Die physischen Netzwerkeinschränkungen der Anwendung können erhebliche Auswirkungen auf die Leistung haben. In den folgenden Abschnitten werden einige Einschränkungen beschrieben, die Benutzer bemerken können.  

### <a name="client-network-capability"></a>Client-Netzwerkkapazität

Wie in den folgenden Abschnitten beschrieben, sind die Bandbreite und die Qualität der Netzwerkverbindung entscheidend für die Anwendungsleistung.

#### <a name="throughput"></a>Throughput

Bei der Bandbreite liegt das Problem häufig in der Clientkapazität. Größere Azure-Instanzen verfügen über NICs mit höherer Kapazität. Daher sollten Sie erwägen, eine größere Instanz oder mehr VMs zu verwenden, wenn Sie höhere Netzwerkgrenzwerte auf einem einzelnen Computer benötigen. Wenn Sie von einer lokalen Anwendung auf Azure Storage zugreifen, gilt dieselbe Regel: Informieren Sie sich über die Netzwerkkapazität des Clientgeräts und die Netzwerkverbindung mit dem Azure Storage-Speicherort, und optimieren Sie diese, oder entwerfen Sie Ihre Anwendung entsprechend diesen Kapazitätsgrenzen.

#### <a name="link-quality"></a>Verbindungsqualität

Bedenken Sie wie bei jeder Netzwerknutzung, dass Netzwerkbedingungen, die zu Fehlern und Paketverlusten führen, den effektiven Durchsatz verringern.  Die Verwendung von WireShark oder NetMon kann bei der Diagnose dieses Problems helfen.  

### <a name="location"></a>Standort

In jeder verteilten Umgebung wird die beste Leistung erzielt, indem der Client in der Nähe des Servers platziert wird. Zum Zugriff auf den Azure-Speicher mit der niedrigsten Latenz befindet sich der beste Standort für den Client innerhalb derselben Azure-Region. Wenn Sie beispielsweise über eine Azure-Web-App verfügen, die Azure Storage verwendet, sollten Sie beide in derselben Region bereitstellen (z. B. „USA, Westen“ oder „Asien, Südosten“). Durch die räumliche Zusammenlegung von Ressourcen werden die Wartezeit und die Kosten verringert, da die Bandbreitennutzung innerhalb einer Region kostenlos ist.  

Wenn Clientanwendungen auf Azure Storage zugreifen, aber nicht in Azure gehostet werden (z. B. Apps für mobile Geräte oder lokale Unternehmensdienste), können Sie die Wartezeit reduzieren, indem Sie für das Speicherkonto eine Region in der Nähe dieser Clients verwenden. Wenn Ihre Clients weit verteilt sind (z. B. einige in Nordamerika und andere in Europa), kann es sinnvoll sein, ein Speicherkonto pro Region zu verwenden. Diese Vorgehensweise ist einfacher zu implementieren, wenn die in der Anwendung gespeicherten Daten speziell für bestimmte Benutzer gelten und keine Datenreplikation zwischen den Speicherkonten erforderlich ist.

## <a name="sas-and-cors"></a>SAS und CORS

Angenommen, Sie müssen im Webbrowser eines Benutzers oder in einer Mobiltelefon-App ausgeführten Code (z. B. JavaScript) für den Zugriff auf Daten in Azure Storage autorisieren. Eine Möglichkeit besteht darin, eine Dienstanwendung zu erstellen, die als Proxy fungiert. Das Gerät des Benutzers wird beim Dienst authentifiziert, der wiederum den Zugriff auf Azure Storage-Ressourcen autorisiert. Auf diese Weise müssen Sie den Speicherkontoschlüssel nicht gegenüber unsicheren Geräten offenbaren. Dieser Ansatz führt für die Dienstanwendung jedoch zu einem erheblichen Mehraufwand, da alle zwischen dem Benutzergerät und Azure Storage übertragenen Daten über die Dienstanwendung gesendet werden müssen.

Mit SAS (Shared Access Signature) können Sie die Verwendung einer Dienstanwendung als Proxy für Azure Storage vermeiden. SAS ermöglicht es dem Benutzergerät, Anforderungen mithilfe eines beschränkten Zugriffstokens direkt an Azure Storage zu senden. Wenn ein Benutzer beispielsweise ein Foto in Ihre Anwendung hochladen möchte, kann die Dienstanwendung eine SAS generieren und an das Gerät des Benutzers senden. Das SAS-Token kann die Berechtigung zum Schreiben in eine Azure Storage Ressource für einen bestimmten Zeitraum erteilen, nach dem das SAS-Token dann abläuft. Weitere Informationen zu SAS finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signatures)](../common/storage-sas-overview.md).  

Normalerweise lässt ein Webbrowser nicht zu, dass JavaScript-Code in einer Seite, die von einer Website in einer Domäne gehostet wird, bestimmte Vorgänge (z. B. Schreibvorgänge) in einer anderen Domäne ausführt. Diese als Richtlinie des gleichen Ursprungs bezeichnete Richtlinie verhindert, dass ein schädliches Skript auf einer Seite Zugriff auf Daten auf einer anderen Webseite erhält. Beim Erstellen einer Lösung in der Cloud kann sich die Richtlinie des gleichen Ursprungs jedoch als Einschränkung erweisen. CORS (Cross-Origin Resource Sharing) ist eine Browserfunktion, mit deren Hilfe die Zieldomäne dem Browser mitteilen kann, dass sie Anforderungen aus der Quelldomäne vertraut.

Angenommen, eine in Azure ausgeführte Webanwendung sendet eine Ressourcenanforderung an ein Azure Storage-Konto. Die Webanwendung ist die Quelldomäne und das Speicherkonto die Zieldomäne. Sie können CORS für alle Azure Storage-Dienste konfigurieren, um dem Webbrowser mitzuteilen, dass Azure Storage Anforderungen aus der Quelldomäne als vertrauenswürdig einstuft. Weitere Informationen zu CORS finden Sie unter [Unterstützung von Cross-Origin Resource Sharing (CORS) für Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Sowohl SAS als auch CORS können Ihnen dabei helfen, eine unnötige Belastung Ihrer Webanwendung zu vermeiden.  

## <a name="batch-transactions"></a>Batchtransaktionen

Der Tabellenspeicherdienst unterstützt Batchtransaktionen für Entitäten, die sich in derselben Tabelle befinden und der gleichen Partitionsgruppe angehören. Weitere Informationen finden Sie unter [Durchführen von Entitätsgruppentransaktionen](/rest/api/storageservices/performing-entity-group-transactions).

## <a name="net-configuration"></a>.NET-Konfiguration

Für die Verwendung des .NET Frameworks sind in diesem Abschnitt einige Schnellkonfigurationseinstellungen aufgelistet, die eine deutliche Leistungsoptimierung bewirken.  Bei Verwendung anderer Sprachen sollten Sie sich informieren, ob es ähnliche Konzepte für die jeweilige Sprache gibt.  

### <a name="use-net-core"></a>Verwenden von .NET Core

Entwickeln Sie Ihre Azure Storage-Anwendungen mit .NET Core 2.1 oder höher, um die in diesen Versionen verfügbaren Leistungsverbesserungen zu nutzen. Wenn möglich, sollten Sie .NET Core 3.x verwenden.

Weitere Informationen zu den Leistungsverbesserungen in .NET Core finden Sie in den folgenden Blogbeiträgen:

- [Performance Improvements in .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/) (Leistungsverbesserungen in .NET Core 3.0)
- [Performance Improvements in .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/) (Leistungsverbesserungen in .NET Core 2.1)

### <a name="increase-default-connection-limit"></a>Erhöhen des Standardverbindungslimits

In .NET erhöht der folgende Code das Standard-Verbindungslimit (in der Regel 2 in einer Clientumgebung oder 10 in einer Serverumgebung) auf 100. In der Regel sollten Sie den Wert auf die ungefähre Anzahl der Threads, die von der Anwendung verwendet werden, setzen.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Legen Sie das Verbindungslimit fest, bevor Sie Verbindungen öffnen.  

Für andere Programmiersprachen erfahren Sie das Verbindungslimit aus der zugehörigen Dokumentation.  

Weitere Informationen finden Sie im Blogbeitrag [Webdienste: Gleichzeitige Verbindungen](/archive/blogs/darrenj/web-services-concurrent-connections).  

### <a name="increase-minimum-number-of-threads"></a>Erhöhen der Mindestanzahl von Threads

Wenn Sie synchrone Aufrufe zusammen mit asynchronen Aufgaben verwenden, können Sie die Anzahl der Threads im Threadpool erhöhen:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Weitere Informationen finden Sie unter der [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads)-Methode.  

## <a name="unbounded-parallelism"></a>Uneingeschränkte Parallelität

Parallelität kann großartig für die Leistung sein. Bei der Verwendung von uneingeschränkter Parallelität ist jedoch Vorsicht geboten, da in diesem Fall keine Beschränkung für die Anzahl der Threads oder parallelen Anforderungen erzwungen wird. Beschränken Sie parallele Anforderungen zum Hoch- oder Herunterladen von Daten auf den Zugriff auf mehrere Partitionen im selben Speicherkonto oder auf mehrere Elemente in derselben Partition. Bei uneingeschränkter Parallelität können die Kapazität des Clientgeräts oder die Skalierbarkeitsziele des Speicherkontos überschritten werden, sodass es zu längeren Wartezeiten und Drosselung kommt.  

## <a name="client-libraries-and-tools"></a>Clientbibliotheken und -tools

Verwenden Sie für optimale Leistung immer die aktuellen Clientbibliotheken und -tools von Microsoft. Azure Storage-Clientbibliotheken sind für viele Programmiersprachen verfügbar. Azure Storage unterstützt auch PowerShell und die Azure-Befehlszeilenschnittstelle (Azure CLI). Microsoft entwickelt diese Clientbibliotheken und -tools aktiv im Hinblick auf die Leistung, hält sie auf dem aktuellen Stand der Dienstversionen und stellt sicher, dass sie viele der bewährten Methoden für die Leistung intern umsetzen.

## <a name="handle-service-errors"></a>Behandeln von Dienstfehlern

Azure Storage gibt einen Fehler zurück, wenn der Dienst eine Anforderung nicht verarbeiten kann. Das Verständnis der Fehler, die Azure Storage in einem bestimmten Szenario zurückgeben kann, ist beim Optimieren der Leistung hilfreich.

### <a name="timeout-and-server-busy-errors"></a>Timeoutfehler und Fehler durch ausgelasteten Server

Azure Storage kann Ihre Anwendung drosseln, wenn sie sich den Skalierbarkeitsgrenzwerten nähert. In einigen Fällen kann Azure Storage eine Anforderung möglicherweise aufgrund vorübergehender Bedingungen nicht verarbeiten. In beiden Fällen kann der Dienst einen Fehler 503 (Server ausgelastet) oder 500 (Timeout) zurückgeben. Diese Fehler können auch auftreten, wenn der Dienst Datenpartitionen ausgleicht, um einen höheren Durchsatz zu ermöglichen. In der Regel wiederholt die Clientanwendung den Vorgang, der einen dieser Fehler verursacht. Wenn Azure Storage Ihre Anwendung drosselt, weil die Skalierbarkeitsziele überschritten wurden, oder der Dienst die Anforderung aus einem anderen Grund nicht ausführen konnte, verschlimmern aggressive Wiederholungsversuche jedoch meist das Problem. Aus diesem Grund wird eine Wiederholungsrichtlinie mit exponentiellem Backoff empfohlen (dies ist das Standardverhalten der Clientbibliotheken). Beispielsweise kann Ihre Anwendung nach 2 Sekunden, dann nach 4 Sekunden, nach 10 Sekunden und nach 30 Sekunden einen Wiederholungsversuch starten und dann komplett aufgeben. So kann die Anwendung die Last des Diensts deutlich reduzieren, anstatt Probleme, die zu einer Drosselung führen können, weiter zu verschärfen.  

Verbindungsfehler können sofort wiederholt werden, da sie kein Ergebnis einer Drosselung sind und nur vorübergehend bestehen sollten.  

### <a name="non-retryable-errors"></a>Nicht behebbare Fehler

Die Clientbibliotheken berücksichtigen bei Wiederholungsversuchen, welche Fehler behoben werden können und welche nicht. Wenn Sie die Azure Storage-REST-API direkt aufrufen, sollten Sie bei einigen Fehlern jedoch keinen Wiederholungsversuch ausführen. Bei einem Fehler vom Typ 400 (ungültige Anforderung) hat die Clientanwendung beispielsweise eine Anforderung gesendet, die aufgrund eines unerwarteten Formats nicht verarbeitet werden konnte. Das erneute Senden dieser Anforderung führt jedes Mal zur selben Antwort und ist daher nicht sinnvoll. Wenn Sie die Azure Storage-REST-API direkt aufrufen, sollten Sie die potenziellen Fehler kennen und wissen, ob ein Wiederholungsversuch ausgeführt werden sollte.

Weitere Informationen zu Azure Storage-Fehlercodes finden Sie unter [Status- und Fehlercodes](/rest/api/storageservices/status-and-error-codes2).

## <a name="configuration"></a>Konfiguration

In diesem Abschnitt finden Sie verschiedene Schnellkonfigurationseinstellungen, die deutliche Leistungsverbesserungen im Tabellenspeicherdienst bewirken:

### <a name="use-json"></a>Verwenden von JSON

Ab Speicherdienstversion 2013-08-15 unterstützt der Tabellenspeicherdienst die Verwendung von JSON anstelle des XML-basierten AtomPub-Formats für die Übertragung von Tabellendaten. Die Verwendung von JSON kann die Nutzlast um bis zu 75 % verringern und die Anwendungsleistung deutlich verbessern.

Weitere Informationen finden Sie im Eintrag [Microsoft Azure-Tabellen: Einführung in JSON](/archive/blogs/windowsazurestorage/windows-azure-tables-introducing-json) und [Nutzlastformat für Tabellenspeicherdienstvorgänge](/rest/api/storageservices/Payload-Format-for-Table-Service-Operations).

### <a name="disable-nagle"></a>Deaktivieren des Nagle-Algorithmus

Der Nagle-Algorithmus ist in TCP/IP-Netzwerken weit verbreitet, um die Netzwerkleistung zu verbessern. Er ist jedoch nicht unter allen Umständen optimal (z. B. hoch interaktive Umgebungen). Der Nagle-Algorithmus wirkt sich negativ auf die Leistung von Anforderungen an den Azure-Tabellenspeicherdienst aus und sollte daher möglichst deaktiviert werden.

## <a name="schema"></a>Schema

Wie Sie Ihre Daten darstellen und abfragen, ist der wichtigste Einzelfaktor für die Leistung des Tabellenspeicherdiensts. Während jede Anwendung anders ist, sind in diesem Abschnitt einige allgemeine bewährte Vorgehensweisen für folgende Bereiche beschrieben:

- Tabellenentwurf
- Effiziente Abfragen
- Effiziente Datenaktualisierungen

### <a name="tables-and-partitions"></a>Tabellen und Partitionen

Tabellen sind in Partitionen unterteilt. Jede in einer Partition gespeicherte Entität verwendet denselben Partitionsschlüssel und hat einen eindeutigen Zeilenschlüssel, damit sie innerhalb der Partition identifiziert werden kann. Partitionen bieten Vorteile, führen jedoch auch zu Skalierbarkeitsgrenzen.

- Vorteile: Sie können die Entitäten innerhalb derselben Partition in einer einzigen, unteilbaren Batchtransaktion aktualisieren, die bis zu 100 separate Speichervorgänge enthalten kann (Gesamtgrößenlimit 4 MB). Bei derselben Menge abzurufender Entitäten können Sie auch die Daten innerhalb einer Partition effizienter abfragen als Daten, die sich über mehrere Partitionen verteilen (lesen Sie für noch mehr Empfehlungen zur Abfrage von Tabellendaten weiter).
- Skalierbarkeitsgrenze: Für den Zugriff auf Entitäten, die in einer einzigen Partition gespeichert sind, kann kein Lastenausgleich erfolgen, da die Partitionen unteilbare Batchtransaktionen unterstützen. Aus diesem Grund ist das Skalierbarkeitsziel für eine einzelne Tabellenpartition niedriger als das für den gesamten Tabellenspeicherdienst.

Aufgrund dieser Eigenschaften von Tabellen und Partitionen sollten Sie die folgenden Entwurfsprinzipien beachten:

- Platzieren Sie Daten, die Ihre Clientanwendung häufig in derselben logischen Arbeitseinheit aktualisiert oder abfragt, in derselben Partition. Wenn Ihre Anwendung Schreibvorgänge aggregiert oder Sie unteilbare Batchvorgänge durchführen, sollten sich die Daten beispielsweise in derselben Partition befinden. Außerdem können Daten innerhalb einer Partition effizienter mit nur einer Abfrage abgefragt werden als Daten, die sich über mehrere Partitionen verteilen.
- Platzieren Sie Daten, die Ihre Clientanwendung nicht in derselben logischen Arbeitseinheit einfügt/aktualisiert (d. h. mit einer Einzelabfrage oder Batchaktualisierung), in separaten Partitionen. Bedenken Sie, dass die Anzahl von Partitionsschlüsseln in einer Tabelle nicht begrenzt ist. Selbst Millionen von Partitionsschlüsseln sind daher kein Problem und wirken sich nicht auf die Leistung aus. Wenn es sich bei Ihrer Anwendung beispielsweise um eine beliebte Website mit Benutzeranmeldung handelt, kann die Benutzer-ID als Partitionsschlüssel verwendet werden.

#### <a name="hot-partitions"></a>Heiße Partitionen

Eine Hot Partition ist eine Partition, die einen überproportionalen Anteil des Datenverkehrs an ein Konto empfängt und kein Lastenausgleich erfolgen kann, da es sich um eine einzelne Partition handelt. Im Allgemeinen wird eine Hot Partition mit einer der folgenden Möglichkeiten erstellt:

#### <a name="append-only-and-prepend-only-patterns"></a>Nur-Anfügen- und Nur-Voranstellen-Muster

Beim Muster „Nur anfügen“ nimmt (fast) der gesamte Datenverkehr zu einem bestimmten Partitionsschlüssel abhängig von der aktuellen Zeit zu oder ab. Angenommen, Ihre Anwendung verwendet das aktuelle Datum als Partitionsschlüssel für Protokolldaten. Dieser Entwurf führt dazu, dass Daten immer in der letzten Partition der Tabelle eingefügt werden und das System keinen geeigneten Lastenausgleich durchführen kann. Wenn das Datenvolumen in dieser Partition das Skalierbarkeitsziel auf Partitionsebene überschreitet, kommt es zu einer Drosselung. Es ist besser sicherzustellen, dass der Datenverkehr an mehrere Partitionen geleitet wird, um den Lastenausgleich für Anfragen in der Tabelle zu verteilen.

#### <a name="high-traffic-data"></a>Häufig verwendete Daten

Wenn Ihr Partitionsschema dazu führt, dass in einer einzigen Partition Daten enthalten sind, die deutlich häufiger als in anderen Partitionen verwendet werden, kann ebenfalls eine Drosselung erfolgen, wenn diese Partition das Skalierbarkeitsziel für eine einzelne Partition erreicht. Sie sollten besser sicherstellen, dass Ihr Partitionsschema so angelegt ist, dass sich keine einzelne Partition an die Skalierbarkeitsziele annähert.

### <a name="querying"></a>Abfragen

In diesem Abschnitt werden bewährte Methoden für das Abfragen des Tabellenspeicherdiensts erläutert.

#### <a name="query-scope"></a>Abfragebereich

Es gibt mehrere Möglichkeiten, den Entitätsbereich für die Abfrage anzugeben. Für den Abfragebereich sind folgende Optionen verfügbar.

- **Punktabfragen:** Eine Punktabfrage ruft genau eine Entität ab, indem sowohl der Partitionsschlüssel als auch der Zeilenschlüssel der jeweiligen Entität angegeben werden. Diese Abfragen sind effizient und Sie sollten diese so oft wie möglich verwenden.
- **Partitionsabfragen:** Eine Partitionsabfrage ist eine Abfrage, die eine Gruppe von Daten mit einem gemeinsamen Partitionsschlüssel abruft. In der Regel gibt die Abfrage einen Wertebereich für den Zeilenschlüssel oder für eine Entitätseigenschaft zusätzlich zum Partitionsschlüssel an. Diese Abfragen sind weniger effizient als Punktabfragen und sollten nur sparsam verwendet werden.
- **Tabellenabfragen:** Eine Tabellenabfrage ist eine Abfrage, die eine Gruppe von Entitäten ohne gemeinsamen Partitionsschlüssel abruft. Diese Abfragen sind nicht effizient und Sie sollten diese möglichst vermeiden.

In der Regel sollten Sie Scans vermeiden (Abfragen, die sich auf mehr als eine Entität beziehen), wenn Sie jedoch scannen müssen, organisieren Sie Ihre Daten so, dass der Scan die benötigten Daten abruft, ohne eine übermäßige Menge an nicht benötigten Entitäten zurückzugeben.

#### <a name="query-density"></a>Abfragedichte

Ein weiterer Schlüsselfaktor bei der Abfrageeffizienz ist die Anzahl der zurückgegebenen Entitäten im Vergleich zur Anzahl der gescannten Entitäten, um den zurückgegebenen Satz zu suchen. Wenn Ihre Anwendung eine Tabellenabfrage mit einem Filter für einen Eigenschaftswert durchführt, den nur 1 % der Daten teilen, scannt die Abfrage 100 Entitäten für jede zurückgegebene Entität. Die zuvor erläuterten Skalierbarkeitsziele für Tabellen beziehen sich alle auf die Anzahl durchsuchter Entitäten und nicht auf die Anzahl zurückgegebener Entitäten: Eine geringe Abfragedichte kann leicht dazu führen, dass der Tabellenspeicherdienst Ihre Anwendung drosselt, weil er viele Entitäten durchsuchen muss, um die gewünschte Entität abzurufen. Weitere Informationen zum Vermeiden einer Drosselung finden Sie im Abschnitt [Denormalisierung](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Einschränken der Menge der zurückgegebenen Daten

Wenn Sie wissen, dass eine Abfrage Entitäten zurückgibt, die Sie in der Clientanwendung nicht benötigen, sollten Sie einen Filter verwenden, um die Größe der zurückgegebenen Entitätenmenge zu reduzieren. Während die nicht an den Client zurückgegebenen Entitäten trotzdem für die Skalierbarkeitsgrenzen zählen, wird sich Ihre Anwendungsleistung aufgrund der reduzierten Netzwerknutzlast und der reduzierten Entitätenanzahl, die von der Clientanwendung verarbeitet werden müssen, verbessern. Denken Sie daran, dass sich die Skalierbarkeitsziele auf die Anzahl durchsuchter Entitäten beziehen. Eine Abfrage, bei der viele Entitäten herausgefiltert und nur wenige Entitäten zurückgegeben werden, kann daher trotzdem zu einer Drosselung führen. Weitere Informationen zum Erstellen effizienter Abfragen finden Sie im Abschnitt [Abfragedichte](#query-density).

Wenn Ihre Clientanwendung nur bestimmte Eigenschaften der Entitäten in der Tabelle benötigt, können Sie Projektion verwenden, um die Größe des zurückgegebenen Datensatzes einzuschränken. Wie die Filterung trägt auch die Projektion dazu bei, die Netzwerklast und Clientverarbeitung zu reduzieren.

#### <a name="denormalization"></a>Denormalisierung

Im Gegensatz zum Arbeiten mit relationalen Datenbanken führen die bewährten Vorgehensweisen für das effiziente Abfragen von Tabellendaten zur Denormalisierung Ihrer Daten. Das heißt, dieselben Daten werden in mehreren Entitäten dupliziert (eine für jeden Schlüssel, den Sie zum Suchen der Daten verwenden könnten), um die Anzahl der Entitäten zu minimieren, die eine Abfrage scannen muss, um die vom Client benötigten Daten zu finden, statt eine große Anzahl von Entitäten zu scannen, um die für die Anwendung benötigten Daten zu finden. Beispielsweise kann auf einer E-Commerce-Website eine Bestellung sowohl nach Kunden-ID (zur Abfrage der Kundenbestellungen) als auch nach Datum (zur Abfrage der Bestellungen für ein bestimmtes Datum) gefunden werden. Bei der Tabellenspeicherung ist es am besten, die Entität zweimal zu speichern (bzw. darauf zu verweisen) – einmal mit Tabellenname, PS und ZS für die Suche nach der Kunden-ID und einmal für die Suche nach dem Datum.  

### <a name="insert-update-and-delete"></a>Einfügen, Aktualisieren und Löschen

In diesem Abschnitt werden bewährte Methoden für das Ändern von im Tabellenspeicherdienst gespeicherten Entitäten erläutert.  

#### <a name="batching"></a>Batching

Batchtransaktionen werden in Azure Storage als Entitätsgruppentransaktionen bezeichnet. Alle Vorgänge innerhalb einer Entitätsgruppentransaktion müssen in einer einzelnen Partition in einer einzelnen Tabelle ausgeführt werden. Verwenden Sie Entitätsgruppentransaktionen möglichst, um Einfüge-, Aktualisierungs- und Löschvorgänge in Batches durchzuführen. Durch die Verwendung von Entitätsgruppentransaktionen reduziert sich die Anzahl von Roundtrips von Ihrer Clientanwendung zum Server und somit die Anzahl kostenpflichtiger Transaktionen (eine Entitätsgruppentransaktion wird bei der Abrechnung als eine Transaktion gezählt und kann bis zu 100 Speichervorgänge umfassen). Außerdem ermöglicht sie unteilbare Aktualisierungen (alle Vorgänge in einer Entitätsgruppentransaktion sind erfolgreich oder schlagen fehl). Umgebungen mit langen Wartezeiten (z. B. mobile Geräte) profitieren erheblich von der Verwendung von Entitätsgruppentransaktionen.  

#### <a name="upsert"></a>Upsert

Verwenden Sie wenn möglich den Tabellenvorgang **Upsert** zum Einfügen und Aktualisieren. Es gibt zwei **Upsert**-Typen, die beide effizienter als herkömmliche **Insert**- und **Update**-Vorgänge sind:  

- **InsertOrMerge**: Verwenden Sie diesen Vorgang, wenn Sie eine Teilmenge der Entitätseigenschaften hochladen möchten, aber nicht sicher sind, ob die Entität bereits vorhanden ist. Falls die Entität vorhanden ist, aktualisiert dieser Aufruf die in **Upsert** enthaltenen Eigenschaften und lässt alle vorhandenen Eigenschaften unverändert – falls die Entität nicht vorhanden ist, wird die neue Entität eingefügt. Dies funktioniert ähnlich wie die Projektion in einer Abfrage, da Sie nur die geänderten Eigenschaften hochladen müssen.
- **InsertOrReplace**: Verwenden Sie diesen Vorgang, wenn Sie eine neue Entität hochladen möchten, aber nicht sicher sind, ob diese bereits vorhanden ist. Da die neu hochgeladene Entität die alte Entität vollständig überschreibt, sollten Sie diesen Vorgang nur verwenden, wenn Sie wissen, dass die neue Entität ganz korrekt ist. Wenn Sie beispielsweise die Entität aktualisieren möchten, die den aktuellen Standort eines Benutzers speichert, unabhängig davon, ob die Anwendung zuvor Standortdaten für den Benutzer gespeichert hatte, ist die neue Entität komplett, da Sie keine Informationen aus der vorherigen Entität benötigen.

#### <a name="storing-data-series-in-a-single-entity"></a>Speichern von Datenreihen in einer einzigen Entität

Manchmal speichert eine Anwendung eine Reihe von Daten, die sie häufig in einem Schritt abrufen muss: Beispielsweise kann eine Anwendung die CPU-Auslastung über einen bestimmten Zeitraum erfassen, um ein fortlaufendes Diagramm der Daten aus den letzten 24 Stunden zu plotten. Eine Möglichkeit ist es, eine Tabellenentität pro Stunde anzulegen, sodass jede Entität eine bestimmte Stunde darstellt und die CPU-Auslastung für diese Stunde speichert. Um diese Daten zu plotten, muss die Anwendung die Entitäten abrufen, welche die Daten aus den letzten 24 Stunden enthalten.  

Alternativ kann Ihre Anwendung die CPU-Nutzung für jede Stunde als separate Eigenschaft einer einzelnen Entität speichern: für stündliche Aktualisierungen kann die Anwendung einzeln **InsertOrMerge Upsert** abrufen, um den Wert für die aktuelle Stunde zu aktualisieren. Um die Daten darzustellen, muss die Anwendung nicht 24, sondern nur eine einzige Entität abrufen. Die Abfrage ist daher sehr effizient. Weitere Informationen zur Effizienz von Abfragen finden Sie im Abschnitt [Abfragebereich](#query-scope).

#### <a name="storing-structured-data-in-blobs"></a>Speichern strukturierter Daten in Blobs

Wenn Sie Batcheinfügungen durchführen und anschließend Entitätsbereiche zusammen abrufen, sollten Sie ggf. Blobs anstelle von Tabellen verwenden. Ein gutes Beispiel dafür ist eine Protokolldatei. Sie können Protokolle für mehrere Minuten in einem Batch zusammenfassen, einfügen und dann mehrere Protokollminuten gleichzeitig abrufen. In diesem Fall ist die Leistung besser, wenn Sie anstelle von Tabellen Blobs verwenden. Dadurch können Sie die Anzahl von Objekten, in die geschrieben wird oder die gelesen werden, und möglicherweise auch die Anzahl erforderlicher Anforderungen deutlich reduzieren.  

## <a name="next-steps"></a>Nächste Schritte

- [Skalierbarkeits- und Leistungsziele für Table Storage](scalability-targets.md)
- [Skalierbarkeits- und Leistungsziele für Storage Standard-Konten](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Status- und Fehlercodes](/rest/api/storageservices/Status-and-Error-Codes2)