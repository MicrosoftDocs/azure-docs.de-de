---
title: 'Checkliste zu Leistung und Skalierbarkeit für Queue Storage: Azure Storage'
description: Eine Checkliste mit bewährten Methoden für die Entwicklung leistungsstarker Anwendungen mit Queue Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 6e86950581255bd4e3a78b0b4a3f599a24a3cad0
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345753"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Checkliste zu Leistung und Skalierbarkeit für Queue Storage

Microsoft hat eine Reihe bewährter Methoden für die Entwicklung leistungsstarker Anwendungen mit Queue Storage zusammengestellt. Diese Checkliste enthält wichtige Methoden, mit denen Entwickler die Leistung optimieren können. Beachten Sie diese Methoden beim Entwerfen Ihrer Anwendung und während des gesamten Prozesses.

Azure Storage verfügt über Skalierbarkeits- und Leistungsziele für die Kapazität, Transaktionsrate und Bandbreite. Weitere Informationen zu Azure Storage-Skalierbarkeitszielen finden Sie unter [Skalierbarkeits- und Leistungsziele für Standardspeicherkonten](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) und [Skalierbarkeits- und Leistungsziele für Queue Storage](scalability-targets.md).

## <a name="checklist"></a>Checkliste

In diesem Artikel werden bewährte Methoden für die Leistung in einer Checkliste zusammengefasst, an der Sie sich beim Entwickeln Ihrer Queue Storage-Anwendung orientieren können.

| Vorgehensweise | Category | Überlegungen zum Entwurf |
| --- | --- | --- |
| &nbsp; |Skalierbarkeitsziele |[Können Sie Ihre Anwendung so entwerfen, dass sie nicht mehr als die maximale Anzahl von Speicherkonten verwendet?](#maximum-number-of-storage-accounts) |
| &nbsp; |Skalierbarkeitsziele |[Vermeiden Sie es, die Kapazitäts- und Transaktionsgrenzwerte zu erreichen?](#capacity-and-transaction-targets) |
| &nbsp; |Netzwerk |[Haben clientseitige Geräte genügend Bandbreite und ist die Wartezeit gering genug, um die erforderliche Leistung zu erzielen?](#throughput) |
| &nbsp; |Netzwerk |[Ist die Qualität der Netzwerkverbindung von clientseitigen Geräten gut?](#link-quality) |
| &nbsp; |Netzwerk |[Befindet sich die Clientanwendung in der gleichen Region wie das Speicherkonto?](#location) |
| &nbsp; |Direkter Clientzugriff |[Verwenden Sie Shared Access Signatures (SAS) und Cross-Origin Resource Sharing (CORS), um den direkten Zugriff auf Azure Storage zu ermöglichen?](#sas-and-cors) |
| &nbsp; |.NET-Konfiguration |[Verwenden Sie .NET Core 2.1 oder höher, um eine optimale Leistung zu erzielen?](#use-net-core) |
| &nbsp; |.NET-Konfiguration |[Haben Sie Ihren Client zur Verwendung einer ausreichenden Anzahl gleichzeitiger Verbindungen konfiguriert?](#increase-default-connection-limit) |
| &nbsp; |.NET-Konfiguration |[Für .NET-Anwendungen: Haben Sie .NET für die Verwendung einer ausreichenden Anzahl von Threads konfiguriert?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallelität |[Haben Sie sichergestellt, dass die Parallelität entsprechend begrenzt ist, sodass weder die Clientkapazitäten noch die Skalierbarkeitsziele überschritten werden?](#unbounded-parallelism) |
| &nbsp; |Tools |[Verwenden Sie die aktuellen Versionen der von Microsoft bereitgestellten Clientbibliotheken und -tools?](#client-libraries-and-tools) |
| &nbsp; |Wiederholungsversuche |[Verwenden Sie eine Wiederholungsrichtlinie mit exponentiellem Backoff für Drosselungsfehler und Timeouts?](#timeout-and-server-busy-errors) |
| &nbsp; |Wiederholungsversuche |[Vermeidet Ihre Anwendung Wiederholungsversuche für nicht wiederholbare Fehler?](#non-retryable-errors) |
| &nbsp; |Konfiguration |[Haben Sie den Nagle-Algorithmus deaktiviert, um die Leistung kleiner Anforderungen zu verbessern?](#disable-nagle) |
| &nbsp; |Nachrichtengröße |[Sind Ihre Nachrichten kompakt, um die Leistung der Warteschlange zu verbessern?](#message-size) |
| &nbsp; |Massenabruf |[Rufen Sie mehrere Nachrichten mit einem einzigen GET-Vorgang ab?](#batch-retrieval) |
| &nbsp; |Abrufhäufigkeit |[Rufen Sie häufig genug ab, um die gefühlte Latenz der Anwendung zu reduzieren?](#queue-polling-interval) |
| &nbsp; |Aktualisieren von Nachrichten |[Verwenden Sie den Vorgang „UpdateMessage“, um den Fortschritt bei der Nachrichtenverarbeitung zu speichern, sodass bei einem Fehler nicht die gesamte Nachricht erneut verarbeitet werden muss?](#use-update-message) |
| &nbsp; |Aufbau |[Verwenden Sie Warteschlangen, um die gesamte Anwendung besser skalierbar zu machen, indem Sie Arbeitsauslastungen mit langer Laufzeit aus dem kritischen Pfad heraushalten und unabhängig skalieren?](#application-architecture) |

## <a name="scalability-targets"></a>Skalierbarkeitsziele

Wenn Ihre Anwendung eines der Skalierbarkeitsziele erreicht oder überschreitet, kann es zu erhöhter Transaktionslatenz oder Drosselung kommen. Wenn Azure Storage Ihre Anwendung drosselt, beginnt der Dienst, die Fehlercodes 503 (Server ausgelastet) oder 500 (Timeout bei Vorgang) zurückzugeben. Diese Fehler zu vermeiden, indem Sie innerhalb der Grenzwerte der Skalierbarkeitsziele bleiben, trägt maßgeblich dazu bei, die Leistung Ihrer Anwendung zu verbessern.

Weitere Informationen zu den Skalierbarkeitszielen für den Warteschlangendienst finden Sie unter [Skalierbarkeits- und Leistungsziele in Azure Storage](./scalability-targets.md#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maximale Anzahl von Speicherkonten

Wenn Sie die maximal zulässige Anzahl von Speicherkonten für eine bestimmte Kombination aus Abonnement und Region fast erreicht haben: Verwenden Sie mehrere Speicherkonten zur horizontalen Partitionierung, um den ein- und ausgehenden Datenverkehr, die E/A-Vorgänge pro Sekunde (IOPS) oder die Kapazität zu erhöhen? In diesem Szenario empfiehlt Microsoft, höhere Grenzwerte für Speicherkonten zu nutzen, um möglichst die Anzahl der Speicherkonten zu verringern, die für Ihre Workload erforderlich sind. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/options/), wenn Sie höhere Grenzwerte für Ihr Speicherkonto benötigen. Weitere Informationen finden Sie unter [Ankündigung größerer Speicherkonten mit höherer Skalierung](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapazitäts- und Transaktionsziele

Wenn sich Ihre Anwendung den Skalierbarkeitszielen für ein Speicherkonto nähert, sollten Sie eine der folgenden Vorgehensweisen wählen:

- Wenn die Skalierbarkeitsziele für Warteschlangen nicht für Ihre Anwendung ausreichen, sollten Sie mehrere Warteschlangen verwenden, auf die Sie Nachrichten verteilen.
- Berücksichtigen Sie die Arbeitsauslastung, aufgrund derer Ihre Anwendung das Skalierbarkeitsziel erreicht oder überschreitet. Können Sie diese anders konzipieren, um weniger Bandbreite bzw. Kapazität oder weniger Transaktionen zu verwenden?
- Wenn Ihre Anwendung eines der Skalierbarkeitsziele überschreiten muss, erstellen Sie mehrere Speicherkonten, und partitionieren Sie Ihre Anwendungsdaten über diese Speicherkonten hinweg. Konzipieren Sie in diesem Fall die Anwendung so, dass Sie künftig weitere Speicherkonten für den Lastenausgleich hinzufügen können. Speicherkonten selbst verursachen abgesehen von den Nutzungskosten für gespeicherte Daten, durchgeführte Transaktionen oder übertragene Daten keine Kosten.
- Wenn sich Ihre Anwendung den Bandbreitenzielen nähert, versuchen Sie, Daten clientseitig zu komprimieren, um die erforderliche Bandbreite zum Senden der Daten an Azure Storage zu reduzieren. Die Komprimierung von Daten kann zwar Bandbreite sparen und die Netzwerkleistung verbessern, aber auch negative Auswirkungen auf die Leistung haben. Beobachten Sie, wie sich der zusätzliche Verarbeitungsbedarf für das Komprimieren und Dekomprimieren der Daten auf dem Client auf die Leistung auswirkt. Bedenken Sie, dass das Speichern von komprimierten Daten die Problembehandlung erschweren kann, da es schwieriger sein kann, die Daten mithilfe von Standardtools anzuzeigen.
- Wenn sich Ihre Anwendung den Skalierbarkeitszielen nähert, sollten Sie unbedingt ein exponentielles Backoff für Wiederholungsversuche verwenden. Sie sollten die in diesem Artikel beschriebenen Empfehlungen umsetzen, um zu verhindern, dass die Skalierbarkeitsziele erreicht werden. Wenn Sie ein exponentielles Backoff für Wiederholungsversuche verwenden, kann Ihre Anwendung Vorgänge jedoch nicht schnell wiederholen, wodurch sich die Drosselung verschlechtern kann. Weitere Informationen finden Sie im Abschnitt [Timeoutfehler und Fehler durch ausgelasteten Server](#timeout-and-server-busy-errors).

## <a name="networking"></a>Netzwerk

Die physischen Netzwerkeinschränkungen der Anwendung können erhebliche Auswirkungen auf die Leistung haben. In den folgenden Abschnitten werden einige Einschränkungen beschrieben, die Benutzer bemerken können.

### <a name="client-network-capability"></a>Client-Netzwerkkapazität

Wie in den folgenden Abschnitten beschrieben, sind die Bandbreite und die Qualität der Netzwerkverbindung entscheidend für die Anwendungsleistung.

#### <a name="throughput"></a>Throughput

Bei der Bandbreite liegt das Problem häufig in der Clientkapazität. Größere Azure-Instanzen verfügen über NICs mit höherer Kapazität. Daher sollten Sie erwägen, eine größere Instanz oder mehr VMs zu verwenden, wenn Sie höhere Netzwerkgrenzwerte auf einem einzelnen Computer benötigen. Wenn Sie von einer lokalen Anwendung auf Azure Storage zugreifen, gilt dieselbe Regel: Informieren Sie sich über die Netzwerkkapazität des Clientgeräts und die Netzwerkverbindung mit dem Azure Storage-Speicherort, und optimieren Sie diese, oder entwerfen Sie Ihre Anwendung entsprechend diesen Kapazitätsgrenzen.

#### <a name="link-quality"></a>Verbindungsqualität

Bedenken Sie wie bei jeder Netzwerknutzung, dass Netzwerkbedingungen, die zu Fehlern und Paketverlusten führen, den effektiven Durchsatz verringern. Die Verwendung von WireShark oder NetMon kann bei der Diagnose dieses Problems helfen.

### <a name="location"></a>Position

In jeder verteilten Umgebung wird die beste Leistung erzielt, indem der Client in der Nähe des Servers platziert wird. Zum Zugriff auf den Azure-Speicher mit der niedrigsten Latenz befindet sich der beste Standort für den Client innerhalb derselben Azure-Region. Wenn Sie beispielsweise über eine Azure-Web-App verfügen, die Azure Storage verwendet, sollten Sie beide in derselben Region bereitstellen (z. B. „USA, Westen“ oder „Asien, Südosten“). Durch die räumliche Zusammenlegung von Ressourcen werden die Wartezeit und die Kosten verringert, da die Bandbreitennutzung innerhalb einer Region kostenlos ist.

Wenn Clientanwendungen auf Azure Storage zugreifen, aber nicht in Azure gehostet werden (z. B. Apps für mobile Geräte oder lokale Unternehmensdienste), können Sie die Wartezeit reduzieren, indem Sie für das Speicherkonto eine Region in der Nähe dieser Clients verwenden. Wenn Ihre Clients weit verteilt sind (z. B. einige in Nordamerika und andere in Europa), kann es sinnvoll sein, ein Speicherkonto pro Region zu verwenden. Diese Vorgehensweise ist einfacher zu implementieren, wenn die in der Anwendung gespeicherten Daten speziell für bestimmte Benutzer gelten und keine Datenreplikation zwischen den Speicherkonten erforderlich ist.

## <a name="sas-and-cors"></a>SAS und CORS

Angenommen, Sie müssen im Webbrowser eines Benutzers oder in einer Mobiltelefon-App ausgeführten Code (z. B. JavaScript) für den Zugriff auf Daten in Azure Storage autorisieren. Eine Möglichkeit besteht darin, eine Dienstanwendung zu erstellen, die als Proxy fungiert. Das Gerät des Benutzers wird beim Dienst authentifiziert, der wiederum den Zugriff auf Azure Storage-Ressourcen autorisiert. Auf diese Weise müssen Sie den Speicherkontoschlüssel nicht gegenüber unsicheren Geräten offenbaren. Dieser Ansatz führt für die Dienstanwendung jedoch zu einem erheblichen Mehraufwand, da alle zwischen dem Benutzergerät und Azure Storage übertragenen Daten über die Dienstanwendung gesendet werden müssen.

Mit SAS (Shared Access Signature) können Sie die Verwendung einer Dienstanwendung als Proxy für Azure Storage vermeiden. SAS ermöglicht es dem Benutzergerät, Anforderungen mithilfe eines beschränkten Zugriffstokens direkt an Azure Storage zu senden. Wenn ein Benutzer beispielsweise ein Foto in Ihre Anwendung hochladen möchte, kann die Dienstanwendung eine SAS generieren und an das Gerät des Benutzers senden. Das SAS-Token kann die Berechtigung zum Schreiben in eine Azure Storage Ressource für einen bestimmten Zeitraum erteilen, nach dem das SAS-Token dann abläuft. Weitere Informationen zu SAS finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signatures)](../common/storage-sas-overview.md).

Normalerweise lässt ein Webbrowser nicht zu, dass JavaScript-Code in einer Seite, die von einer Website in einer Domäne gehostet wird, bestimmte Vorgänge (z. B. Schreibvorgänge) in einer anderen Domäne ausführt. Diese als Richtlinie des gleichen Ursprungs bezeichnete Richtlinie verhindert, dass ein schädliches Skript auf einer Seite Zugriff auf Daten auf einer anderen Webseite erhält. Beim Erstellen einer Lösung in der Cloud kann sich die Richtlinie des gleichen Ursprungs jedoch als Einschränkung erweisen. CORS (Cross-Origin Resource Sharing) ist eine Browserfunktion, mit deren Hilfe die Zieldomäne dem Browser mitteilen kann, dass sie Anforderungen aus der Quelldomäne vertraut.

Angenommen, eine in Azure ausgeführte Webanwendung sendet eine Ressourcenanforderung an ein Azure Storage-Konto. Die Webanwendung ist die Quelldomäne und das Speicherkonto die Zieldomäne. Sie können CORS für alle Azure Storage-Dienste konfigurieren, um dem Webbrowser mitzuteilen, dass Azure Storage Anforderungen aus der Quelldomäne als vertrauenswürdig einstuft. Weitere Informationen zu CORS finden Sie unter [Unterstützung von Cross-Origin Resource Sharing (CORS) für Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

Sowohl SAS als auch CORS können Ihnen dabei helfen, eine unnötige Belastung Ihrer Webanwendung zu vermeiden.

## <a name="net-configuration"></a>.NET-Konfiguration

Für die Verwendung des .NET Frameworks sind in diesem Abschnitt einige Schnellkonfigurationseinstellungen aufgelistet, die eine deutliche Leistungsoptimierung bewirken. Bei Verwendung anderer Sprachen sollten Sie sich informieren, ob es ähnliche Konzepte für die jeweilige Sprache gibt.

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

Verwenden Sie für optimale Leistung immer die aktuellen Clientbibliotheken und -tools von Microsoft. Azure Storage-Clientbibliotheken sind für viele Programmiersprachen verfügbar. Azure Storage unterstützt auch PowerShell und die Azure-Befehlszeilenschnittstelle (Azure CLI). Microsoft entwickelt diese Clientbibliotheken und -tools aktiv im Hinblick auf die Leistung, hält sie auf dem aktuellen Stand der Dienstversionen und stellt sicher, dass sie viele der bewährten Methoden für die Leistung intern umsetzen. Weitere Informationen finden Sie in der [Azure Storage-Referenzdokumentation](./reference.md).

## <a name="handle-service-errors"></a>Behandeln von Dienstfehlern

Azure Storage gibt einen Fehler zurück, wenn der Dienst eine Anforderung nicht verarbeiten kann. Das Verständnis der Fehler, die Azure Storage in einem bestimmten Szenario zurückgeben kann, ist beim Optimieren der Leistung hilfreich.

### <a name="timeout-and-server-busy-errors"></a>Timeoutfehler und Fehler durch ausgelasteten Server

Azure Storage kann Ihre Anwendung drosseln, wenn sie sich den Skalierbarkeitsgrenzwerten nähert. In einigen Fällen kann Azure Storage eine Anforderung möglicherweise aufgrund vorübergehender Bedingungen nicht verarbeiten. In beiden Fällen kann der Dienst einen Fehler 503 (Server ausgelastet) oder 500 (Timeout) zurückgeben. Diese Fehler können auch auftreten, wenn der Dienst Datenpartitionen ausgleicht, um einen höheren Durchsatz zu ermöglichen. In der Regel wiederholt die Clientanwendung den Vorgang, der einen dieser Fehler verursacht. Wenn Azure Storage Ihre Anwendung drosselt, weil die Skalierbarkeitsziele überschritten wurden, oder der Dienst die Anforderung aus einem anderen Grund nicht ausführen konnte, verschlimmern aggressive Wiederholungsversuche jedoch meist das Problem. Aus diesem Grund wird eine Wiederholungsrichtlinie mit exponentiellem Backoff empfohlen (dies ist das Standardverhalten der Clientbibliotheken). Beispielsweise kann Ihre Anwendung nach 2 Sekunden, dann nach 4 Sekunden, nach 10 Sekunden und nach 30 Sekunden einen Wiederholungsversuch starten und dann komplett aufgeben. So kann die Anwendung die Last des Diensts deutlich reduzieren, anstatt Probleme, die zu einer Drosselung führen können, weiter zu verschärfen.

Verbindungsfehler können sofort wiederholt werden, da sie kein Ergebnis einer Drosselung sind und nur vorübergehend bestehen sollten.

### <a name="non-retryable-errors"></a>Nicht behebbare Fehler

Die Clientbibliotheken berücksichtigen bei Wiederholungsversuchen, welche Fehler behoben werden können und welche nicht. Wenn Sie die Azure Storage-REST-API direkt aufrufen, sollten Sie bei einigen Fehlern jedoch keinen Wiederholungsversuch ausführen. Bei einem Fehler vom Typ 400 (ungültige Anforderung) hat die Clientanwendung beispielsweise eine Anforderung gesendet, die aufgrund eines unerwarteten Formats nicht verarbeitet werden konnte. Das erneute Senden dieser Anforderung führt jedes Mal zur selben Antwort und ist daher nicht sinnvoll. Wenn Sie die Azure Storage-REST-API direkt aufrufen, sollten Sie die potenziellen Fehler kennen und wissen, ob ein Wiederholungsversuch ausgeführt werden sollte.

Weitere Informationen zu Azure Storage-Fehlercodes finden Sie unter [Status- und Fehlercodes](/rest/api/storageservices/status-and-error-codes2).

## <a name="disable-nagle"></a>Deaktivieren des Nagle-Algorithmus

Der Nagle-Algorithmus ist in TCP/IP-Netzwerken weit verbreitet, um die Netzwerkleistung zu verbessern. Er ist jedoch nicht unter allen Umständen optimal (z. B. hoch interaktive Umgebungen). Der Nagle-Algorithmus wirkt sich negativ auf die Leistung von Anforderungen an den Azure-Tabellenspeicherdienst aus und sollte daher möglichst deaktiviert werden.

## <a name="message-size"></a>Nachrichtengröße

Die Leistung der Warteschlange und der Skalierbarkeit sinkt, wenn die Nachrichtengröße steigt. Fügen Sie einer Nachricht nur die Informationen hinzu, die der Empfänger benötigt.

## <a name="batch-retrieval"></a>Batchabruf

Sie können bis zu 32 Nachrichten aus einer Warteschlange in einem einzigen Vorgang abrufen. Der Batchabruf kann die Anzahl von Roundtrips von der Clientanwendung reduzieren, was besonders bei Umgebungen mit langer Wartezeit (z. B. mobile Geräte) nützlich ist.

## <a name="queue-polling-interval"></a>Abrufintervall für die Warteschlange

Die meisten Anwendungen fragen Nachrichten aus einer Warteschlange ab. Für die Anwendung kann es sich dabei um die größte Quelle für Transaktionen handeln. Wählen Sie das Abrufintervall mit Bedacht aus: Abrufe, die zu häufig stattfinden, können dazu führen, dass die Anwendung die Skalierbarkeitsziele für die Warteschlange erreicht. Bei 200.000 Transaktionen für 0,01 US-Dollar (zum Redaktionszeitpunkt) betragen die Kosten für einen einzelnen Prozessor, der im Monat einen Abruf pro Sekunde macht, jedoch weniger als 15 Cent. Daher sind Kosten üblicherweise kein Auswahlkriterium für das Abrufintervall.

Aktuelle Datenkosteninformationen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="use-update-message"></a>Verwenden von „UpdateMessage“

Sie können den **UpdateMessage** -Vorgang verwenden, um das Unsichtbarkeitszeitlimit zu erhöhen oder die Statusinformationen einer Nachricht zu aktualisieren. Die Verwendung von **UpdateMessage** kann effizienter sein als ein Workflow, bei dem ein Auftrag nach Abschluss jedes Auftragsschritts von einer Warteschlange in die nächste verschoben wird. Ihre Anwendung kann den Auftragsstatus in der Nachricht speichern und dann weiterarbeiten, anstatt die Nachricht jedes Mal für den nächsten Schritt erneut in die Warteschlange zu stellen. Beachten Sie, dass jeder **UpdateMessage** -Vorgang auf das Skalierbarkeitsziel angerechnet wird.

## <a name="application-architecture"></a>Anwendungsarchitektur

Verwenden Sie Warteschlangen, um die Anwendungsarchitektur skalierbar zu machen. Nachfolgend sind einige Möglichkeiten aufgelistet, wie Sie Warteschlangen verwenden können, um Ihre Anwendung skalierbarer zu gestalten:

- Sie können Warteschlangen verwenden, um Arbeits-Backlogs zur Verarbeitung zu erstellen und die Arbeitsauslastung in der Anwendung gleichmäßig zu verteilen. Sie können z. B. Benutzeranfragen in die Warteschlange stellen, um prozessorintensive Arbeiten durchzuführen, wie das Ändern der Größe hochgeladener Bilder.
- Sie können Warteschlangen verwenden, um Teile Ihrer Anwendung zu entkoppeln, damit diese unabhängig skaliert werden können. Beispielsweise kann ein Web-Front-End Umfrageergebnisse von Benutzern zur späteren Analyse und Speicherung in eine Warteschlange stellen. Sie können mehr Workerrollen-Instanzen hinzufügen, um die Warteschlangendaten nach Bedarf zu verarbeiten.

## <a name="next-steps"></a>Nächste Schritte

- [Skalierbarkeits- und Leistungsziele für Queue Storage](scalability-targets.md)
- [Skalierbarkeits- und Leistungsziele für Storage Standard-Konten](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Status- und Fehlercodes](/rest/api/storageservices/Status-and-Error-Codes2)
