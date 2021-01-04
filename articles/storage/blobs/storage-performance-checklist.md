---
title: 'Checkliste zu Leistung und Skalierbarkeit für Blob Storage: Azure Storage'
description: Eine Checkliste mit bewährten Methoden für die Entwicklung leistungsstarker Anwendungen mit Blob Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 14da8b6cb695703f1881b6b0b9858772bde386c5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544750"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Checkliste zu Leistung und Skalierbarkeit für Blob Storage

Microsoft hat eine Reihe bewährter Methoden für die Entwicklung leistungsstarker Anwendungen mit Blob Storage erarbeitet. Diese Checkliste enthält wichtige Methoden, mit denen Entwickler die Leistung optimieren können. Beachten Sie diese Methoden beim Entwerfen Ihrer Anwendung und während des gesamten Prozesses.

Azure Storage verfügt über Skalierbarkeits- und Leistungsziele für die Kapazität, Transaktionsrate und Bandbreite. Weitere Informationen zu Azure Storage-Skalierbarkeitszielen finden Sie unter [Skalierbarkeits- und Leistungsziele für Standardspeicherkonten](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) und [Skalierbarkeits- und Leistungsziele für Blob Storage](scalability-targets.md).

## <a name="checklist"></a>Checkliste

In diesem Artikel werden bewährte Methoden für die Leistung in einer Checkliste zusammengefasst, an der Sie sich beim Entwickeln Ihrer Blob Storage-Anwendung orientieren können.

| Vorgehensweise | Category | Überlegungen zum Entwurf |
| --- | --- | --- |
| &nbsp; |Skalierbarkeitsziele |[Können Sie Ihre Anwendung so entwerfen, dass sie nicht mehr als die maximale Anzahl von Speicherkonten verwendet?](#maximum-number-of-storage-accounts) |
| &nbsp; |Skalierbarkeitsziele |[Vermeiden Sie es, die Kapazitäts- und Transaktionsgrenzwerte zu erreichen?](#capacity-and-transaction-targets) |
| &nbsp; |Skalierbarkeitsziele |[Greift eine große Anzahl von Clients gleichzeitig auf ein einzelnes Blob zu?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Skalierbarkeitsziele |[Hält Ihre Anwendung die Skalierbarkeitsziele für ein einzelnes Blob ein?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Partitionierung |[Dient Ihre Namenskonvention einem besseren Lastenausgleich?](#partitioning) |
| &nbsp; |Netzwerk |[Haben clientseitige Geräte genügend Bandbreite und ist die Wartezeit gering genug, um die erforderliche Leistung zu erzielen?](#throughput) |
| &nbsp; |Netzwerk |[Ist die Qualität der Netzwerkverbindung von clientseitigen Geräten gut?](#link-quality) |
| &nbsp; |Netzwerk |[Befindet sich die Clientanwendung in der gleichen Region wie das Speicherkonto?](#location) |
| &nbsp; |Direkter Clientzugriff |[Verwenden Sie Shared Access Signatures (SAS) und Cross-Origin Resource Sharing (CORS), um den direkten Zugriff auf Azure Storage zu ermöglichen?](#sas-and-cors) |
| &nbsp; |Caching |[Speichert Ihre Anwendung Daten im Cache, die häufig verwendet werden und sich selten ändern?](#reading-data) |
| &nbsp; |Caching |[Führt Ihre Anwendung Batchupdates durch, indem die Updates auf dem zwischengespeichert und dann in größeren Datasets hochgeladen werden?](#uploading-data-in-batches) |
| &nbsp; |.NET-Konfiguration |[Verwenden Sie .NET Core 2.1 oder höher, um eine optimale Leistung zu erzielen?](#use-net-core) |
| &nbsp; |.NET-Konfiguration |[Haben Sie Ihren Client zur Verwendung einer ausreichenden Anzahl gleichzeitiger Verbindungen konfiguriert?](#increase-default-connection-limit) |
| &nbsp; |.NET-Konfiguration |[Für .NET-Anwendungen: Haben Sie .NET für die Verwendung einer ausreichenden Anzahl von Threads konfiguriert?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallelität |[Haben Sie sichergestellt, dass die Parallelität entsprechend begrenzt ist, sodass weder die Clientkapazitäten noch die Skalierbarkeitsziele überschritten werden?](#unbounded-parallelism) |
| &nbsp; |Tools |[Verwenden Sie die aktuellen Versionen der von Microsoft bereitgestellten Clientbibliotheken und -tools?](#client-libraries-and-tools) |
| &nbsp; |Wiederholungsversuche |[Verwenden Sie eine Wiederholungsrichtlinie mit exponentiellem Backoff für Drosselungsfehler und Timeouts?](#timeout-and-server-busy-errors) |
| &nbsp; |Wiederholungsversuche |[Vermeidet Ihre Anwendung Wiederholungsversuche für nicht wiederholbare Fehler?](#non-retryable-errors) |
| &nbsp; |Kopieren von Blobs |[Werden Blobs so effizient wie möglich kopiert?](#blob-copy-apis) |
| &nbsp; |Kopieren von Blobs |[Verwenden Sie für Massenkopiervorgänge die aktuelle Version von AzCopy?](#use-azcopy) |
| &nbsp; |Kopieren von Blobs |[Verwenden Sie die Azure Data Box-Familie zum Importieren großer Datenmengen?](#use-azure-data-box) |
| &nbsp; |Inhaltsverteilung |[Verwenden Sie ein CDN für die Inhaltsverteilung?](#content-distribution) |
| &nbsp; |Verwenden von Metadaten |[Speichern Sie häufig verwendete Metadaten für Blobs in ihren Metadaten?](#use-metadata) |
| &nbsp; |Schnelles Hochladen |[Wenn Sie versuchen, einen Blob schnell hochzuladen, laden Sie dann Blöcke parallel hoch?](#upload-one-large-blob-quickly) |
| &nbsp; |Schnelles Hochladen |[Wenn Sie versuchen, mehrere Blobs schnell hochzuladen, laden Sie dann Blobs parallel hoch?](#upload-many-blobs-quickly) |
| &nbsp; |Blobtyp |[Verwenden Sie jeweils Seiten-Blobs oder Block-Blobs?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Skalierbarkeitsziele

Wenn Ihre Anwendung eines der Skalierbarkeitsziele erreicht oder überschreitet, kann es zu erhöhter Transaktionslatenz oder Drosselung kommen. Wenn Azure Storage Ihre Anwendung drosselt, beginnt der Dienst, die Fehlercodes 503 (Server ausgelastet) oder 500 (Timeout bei Vorgang) zurückzugeben. Diese Fehler zu vermeiden, indem Sie innerhalb der Grenzwerte der Skalierbarkeitsziele bleiben, trägt maßgeblich dazu bei, die Leistung Ihrer Anwendung zu verbessern.

Weitere Informationen zu den Skalierbarkeitszielen für den Warteschlangendienst finden Sie unter [Skalierbarkeits- und Leistungsziele in Azure Storage](../queues/scalability-targets.md#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maximale Anzahl von Speicherkonten

Wenn Sie die maximal zulässige Anzahl von Speicherkonten für eine bestimmte Kombination aus Abonnement und Region fast erreicht haben, sollten Sie Ihr Szenario einschätzen und ermitteln, ob eine der folgenden Bedingungen zutrifft:

- Verwenden Sie Speicherkonten zum Speichern nicht verwalteter Datenträger und zum Hinzufügen dieser Datenträger zu Ihren virtuellen Computern? Für dieses Szenario empfiehlt Microsoft verwaltete Datenträger. Verwaltete Datenträger werden automatisch skaliert, ohne dass einzelne Speicherkonten erstellt und verwaltet werden müssen. Weitere Informationen finden Sie unter [Einführung in verwaltete Azure-Datenträger](../../virtual-machines/managed-disks-overview.md).
- Verwenden Sie ein Speicherkonto pro Kunde, um Daten zu isolieren? Für dieses Szenario empfiehlt Microsoft anstelle eines vollständigen Speicherkontos die Verwendung eines Blobcontainers für jeden Kunden. Azure Storage ermöglicht Ihnen jetzt die Zuweisung von Azure-Rollen pro Container. Weitere Informationen finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](../common/storage-auth-aad-rbac-portal.md).
- Verwenden Sie mehrere Speicherkonten zum horizontalen Partitionieren, um eingehenden und ausgehenden Datenverkehr, E/A-Vorgänge pro Sekunde (IOPS) oder die Kapazität zu erhöhen? In diesem Szenario empfiehlt Microsoft, höhere Grenzwerte für Speicherkonten zu nutzen, um möglichst die Anzahl der Speicherkonten zu verringern, die für Ihre Workload erforderlich sind. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/options/), wenn Sie höhere Grenzwerte für Ihr Speicherkonto benötigen. Weitere Informationen finden Sie unter [Ankündigung größerer Speicherkonten mit höherer Skalierung](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapazitäts- und Transaktionsziele

Wenn sich Ihre Anwendung den Skalierbarkeitszielen für ein Speicherkonto nähert, sollten Sie eine der folgenden Vorgehensweisen wählen:  

- Wenn Ihre Anwendung das Transaktionsziel erreicht, sollten Sie die Verwendung von Blockblob-Speicherkonten in Erwägung ziehen, die für hohe Transaktionsraten und niedrige, gleichbleibende Wartezeiten optimiert sind. Weitere Informationen finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).
- Berücksichtigen Sie die Arbeitsauslastung, aufgrund derer Ihre Anwendung das Skalierbarkeitsziel erreicht oder überschreitet. Können Sie diese anders konzipieren, um weniger Bandbreite bzw. Kapazität oder weniger Transaktionen zu verwenden?
- Wenn Ihre Anwendung eines der Skalierbarkeitsziele überschreiten muss, erstellen Sie mehrere Speicherkonten, und partitionieren Sie Ihre Anwendungsdaten über diese Speicherkonten hinweg. Konzipieren Sie in diesem Fall die Anwendung so, dass Sie künftig weitere Speicherkonten für den Lastenausgleich hinzufügen können. Speicherkonten selbst verursachen abgesehen von den Nutzungskosten für gespeicherte Daten, durchgeführte Transaktionen oder übertragene Daten keine Kosten.
- Wenn sich Ihre Anwendung den Bandbreitenzielen nähert, versuchen Sie, Daten clientseitig zu komprimieren, um die erforderliche Bandbreite zum Senden der Daten an Azure Storage zu reduzieren.
    Die Komprimierung von Daten kann zwar Bandbreite sparen und die Netzwerkleistung verbessern, aber auch negative Auswirkungen auf die Leistung haben. Beobachten Sie, wie sich der zusätzliche Verarbeitungsbedarf für das Komprimieren und Dekomprimieren der Daten auf dem Client auf die Leistung auswirkt. Bedenken Sie, dass das Speichern von komprimierten Daten die Problembehandlung erschweren kann, da es schwieriger sein kann, die Daten mithilfe von Standardtools anzuzeigen.
- Wenn sich Ihre Anwendung den Skalierbarkeitszielen nähert, sollten Sie unbedingt ein exponentielles Backoff für Wiederholungsversuche verwenden. Sie sollten die in diesem Artikel beschriebenen Empfehlungen umsetzen, um zu verhindern, dass die Skalierbarkeitsziele erreicht werden. Wenn Sie ein exponentielles Backoff für Wiederholungsversuche verwenden, kann Ihre Anwendung Vorgänge jedoch nicht schnell wiederholen, wodurch sich die Drosselung verschlechtern kann. Weitere Informationen finden Sie im Abschnitt [Timeoutfehler und Fehler durch ausgelasteten Server](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Mehrere Clients greifen gleichzeitig auf ein einzelnes Blob zu

Wenn eine große Anzahl von Clients gleichzeitig auf ein einzelnes Blob zugreift, müssen Sie Skalierbarkeitsziele pro Blob und pro Speicherkonto berücksichtigen. Die genaue Anzahl von Clients, die auf ein einzelnes Blob zugreifen können, hängt von verschiedenen Faktoren ab, z. B. der Anzahl der Clients, die das Blob gleichzeitig anfordern, der Größe des Blobs und den Netzwerkbedingungen.

Wenn das Blob über ein CDN verteilt werden kann, wie beispielsweise Bilder oder Videos von einer Website, können Sie ein CDN verwenden. Weitere Informationen finden Sie im Abschnitt mit dem Titel [Inhaltsverteilung](#content-distribution).

In anderen Szenarien (z. B. bei wissenschaftlichen Simulationen, in denen die Daten vertraulich sind) haben Sie zwei Möglichkeiten. Erstens können Sie den Workloadzugriff staffeln, sodass über einen bestimmten Zeitraum hinweg auf das Blob zugegriffen wird (andernfalls würde der Zugriff gleichzeitig erfolgen). Zweitens können Sie das Blob zeitweise in mehrere Speicherkonten kopieren und so die Gesamtanzahl der IOPS pro Blob und übergreifend über Speicherkonten erhöhen. Die Ergebnisse variieren je nach dem Anwendungsverhalten. Achten Sie daher darauf, Parallelitätsmuster während des Entwurfs zu testen.

### <a name="bandwidth-and-operations-per-blob"></a>Bandbreite und Vorgänge pro Blob

Ein einzelnes Blob unterstützt bis zu 500 Anforderungen pro Sekunde. Wenn Sie über mehrere Clients verfügen, die dasselbe Blob lesen müssen, und Sie diesen Grenzwert wahrscheinlich überschreiten, sollten Sie die Verwendung eines Blockblob-Speicherkontos in Erwägung ziehen. Ein Blockblob-Speicherkonto unterstützt eine höhere Anforderungsrate oder mehr E/A-Vorgänge pro Sekunde (IOPS).

Sie können auch ein Content Delivery Network (CDN) wie Azure CDN verwenden, um Vorgänge auf das Blob zu verteilen. Weitere Informationen zum Azure CDN finden Sie in der [Übersicht zum Azure CDN](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Partitionierung

Es ist wichtig, zu verstehen, wie Blobdaten von Azure Storage partitioniert werden. So lässt sich die Leistung verbessern. Azure Storage kann Daten in einer einzelnen Partition schneller bereitstellen als Daten, die sich über mehrere Partitionen erstrecken. Wenn Sie Ihren Blobs geeignete Namen geben, können Sie die Effizienz von Leseanforderungen verbessern.

Blob Storage nutzt ein bereichsbasiertes Partitionierungsschema für die Skalierung und den Lastenausgleich. Jedes Blob verfügt über einen Partitionsschlüssel, der aus dem vollständigen Blobnamen (Konto + Container + Blob) besteht. Der Partitionsschlüssel wird zum Partitionieren von Blobdaten in Bereiche verwendet. Anschließend werden die Bereiche per Lastenausgleich auf den Blob Storage verteilt.

Die bereichsbasierte Partitionierung bedeutet Folgendes: Bei Namenskonventionen, die eine lexikalische Reihenfolge verwenden (z. B. *mypayroll*, *myperformance*, *myemployees* usw.), oder bei Zeitstempeln (*log20160101*, *log20160102*, *log20160102* usw.) ist es wahrscheinlicher, dass Partitionen auf demselben Partitionsserver angeordnet werden, es sei denn, eine höhere Auslastung erfordert, dass sie in kleinere Bereiche aufgeteilt werden. Durch die Anordnung von Blobs auf demselben Partitionsserver wird die Leistung verbessert. Daher ist es wichtig, Blobs auf eine Weise zu benennen, in der sie am effektivsten organisiert werden können. So lässt sich die Leistung verbessern.

So können z.B. alle Blobs in einem Container als ein einziger Server behandelt werden, bis die Last auf diesen Blobs einen Ausgleich der Partitionsbereiche erfordert. Ebenso kann eine Gruppe leicht ausgelasteter Konten, deren Namen lexikalisch sortiert sind, mit der Leistung eines einzigen Servers auskommen, bis die Auslastung eines oder aller Konten erfordert, dass sie auf mehrere Partitionsserver verteilt werden.

Jeder Lastausgleichsvorgang kann die Latenz der Speicheraufrufe während des Vorgangs beeinflussen. Die Fähigkeit des Diensts, einen plötzlichen Anstieg des an eine Partition gerichteten Datenverkehrs zu bewältigen, wird durch die Skalierbarkeit eines einzelnen Partitionsservers begrenzt, bis der Lastenausgleich wirksam wird und den Partitionsschlüsselbereich ausgleicht.

Sie können die Zahl dieser Vorgänge mit einigen bewährten Vorgehensweisen reduzieren.  

- Verwenden Sie nach Möglichkeit Blob- oder Blockgrößen über 4 MiB für Standardspeicherkonten und über 256 KiB für Storage Premium-Konten. Durch höhere Blob- oder Blockgrößen werden automatisch Blockblobs mit hohem Durchsatz aktiviert. Blockblobs mit hohem Durchsatz unterstützen eine leistungsfähige Datenerfassung, die von Partitionsnamen unabhängig ist.
- Untersuchen Sie die Namenskonvention, die Sie für Konten, Container, Blobs, Tabellen und Warteschlangen verwenden. Sie sollten überlegen, Konto-, Container- oder Blobnamen mithilfe einer Hashfunktion, die Ihren Anforderungen am besten gerecht wird, mit einem dreistelligen Hashpräfix zu versehen.
- Wenn Sie Ihre Daten mit Zeitstempeln oder numerischen Bezeichnern organisieren, sollten Sie sicherstellen, dass Sie keine Datenverkehrsmuster verwenden, bei denen Daten nur angefügt (oder vorangestellt) werden. Diese Muster sind für ein bereichsbasiertes Partitionierungssystem nicht geeignet. Die Muster können dazu führen, dass der gesamte Datenverkehr an eine einzelne Partition weitergeleitet wird und dass das System daran gehindert wird, einen effektiven Lastenausgleich durchzuführen.

    Beispiel: Wenn Vorgänge täglich ein Blob mit dem Zeitstempel *yyyymmdd* nutzen, wird der gesamte Datenverkehr für diesen täglichen Vorgang an ein einzelnes Blob weitergeleitet, das von einem einzigen Partitionsserver bedient wird. Prüfen Sie, ob die Grenzwerte pro Blob und pro Partition Ihren Bedürfnissen entsprechen, und erwägen Sie, diesen Vorgang bei Bedarf in mehrere Blobs aufzuteilen. Auch wenn Sie Zeitreihendaten in Ihren Tabellen speichern, kann der gesamte Datenverkehr an den letzten Teil des Schlüsselnamespaces weitergeleitet werden. Wenn Sie numerische IDs verwenden, stellen Sie der ID einen dreistelligen Hash als Präfix voran. Wenn Sie Zeitstempel verwenden, stellen Sie dem Zeitstempel den Sekundenwert voran, z. B. *ssyyyymmdd*. Wenn Ihre Anwendung routinemäßig Auflistungs- und Abfragevorgänge ausführt, wählen Sie eine Hashfunktion, die die Anzahl der Abfragen begrenzt. In einigen Fällen ist möglicherweise ein zufälliges Präfix ausreichend.
  
- Weitere Informationen über das in Azure Storage verwendete Partitionierungsschema finden Sie unter [Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf) (Hochverfügbarer Cloud-Speicherdienst mit starker Konsistenz).

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

Verwenden Sie für die breite Verteilung von Blobinhalten ein Content Delivery Network wie Azure CDN. Weitere Informationen zum Azure CDN finden Sie unter [Azure CDN](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS und CORS

Angenommen, Sie müssen im Webbrowser eines Benutzers oder in einer Mobiltelefon-App ausgeführten Code (z. B. JavaScript) für den Zugriff auf Daten in Azure Storage autorisieren. Eine Möglichkeit besteht darin, eine Dienstanwendung zu erstellen, die als Proxy fungiert. Das Gerät des Benutzers wird beim Dienst authentifiziert, der wiederum den Zugriff auf Azure Storage-Ressourcen autorisiert. Auf diese Weise müssen Sie den Speicherkontoschlüssel nicht gegenüber unsicheren Geräten offenbaren. Dieser Ansatz führt für die Dienstanwendung jedoch zu einem erheblichen Mehraufwand, da alle zwischen dem Benutzergerät und Azure Storage übertragenen Daten über die Dienstanwendung gesendet werden müssen.

Mit SAS (Shared Access Signature) können Sie die Verwendung einer Dienstanwendung als Proxy für Azure Storage vermeiden. SAS ermöglicht es dem Benutzergerät, Anforderungen mithilfe eines beschränkten Zugriffstokens direkt an Azure Storage zu senden. Wenn ein Benutzer beispielsweise ein Foto in Ihre Anwendung hochladen möchte, kann die Dienstanwendung eine SAS generieren und an das Gerät des Benutzers senden. Das SAS-Token kann die Berechtigung zum Schreiben in eine Azure Storage Ressource für einen bestimmten Zeitraum erteilen, nach dem das SAS-Token dann abläuft. Weitere Informationen zu SAS finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signatures)](../common/storage-sas-overview.md).  

Normalerweise lässt ein Webbrowser nicht zu, dass JavaScript-Code in einer Seite, die von einer Website in einer Domäne gehostet wird, bestimmte Vorgänge (z. B. Schreibvorgänge) in einer anderen Domäne ausführt. Diese als Richtlinie des gleichen Ursprungs bezeichnete Richtlinie verhindert, dass ein schädliches Skript auf einer Seite Zugriff auf Daten auf einer anderen Webseite erhält. Beim Erstellen einer Lösung in der Cloud kann sich die Richtlinie des gleichen Ursprungs jedoch als Einschränkung erweisen. CORS (Cross-Origin Resource Sharing) ist eine Browserfunktion, mit deren Hilfe die Zieldomäne dem Browser mitteilen kann, dass sie Anforderungen aus der Quelldomäne vertraut.

Angenommen, eine in Azure ausgeführte Webanwendung sendet eine Ressourcenanforderung an ein Azure Storage-Konto. Die Webanwendung ist die Quelldomäne und das Speicherkonto die Zieldomäne. Sie können CORS für alle Azure Storage-Dienste konfigurieren, um dem Webbrowser mitzuteilen, dass Azure Storage Anforderungen aus der Quelldomäne als vertrauenswürdig einstuft. Weitere Informationen zu CORS finden Sie unter [Unterstützung von Cross-Origin Resource Sharing (CORS) für Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Sowohl SAS als auch CORS können Ihnen dabei helfen, eine unnötige Belastung Ihrer Webanwendung zu vermeiden.  

## <a name="caching"></a>Caching

Das Zwischenspeichern hat einen großen Einfluss auf die Leistung. In den folgenden Abschnitten werden bewährte Methoden für das Caching erläutert.

### <a name="reading-data"></a>Lesen von Daten

Im Allgemeinen ist es von Vorteil, Daten nur einmal zu lesen statt zweimal. Stellen Sie sich beispielsweise eine Webanwendung vor, die ein 50-MiB-Blob aus Azure Storage abgerufen hat, um Inhalte für einen Benutzer bereitzustellen. Im Idealfall wird das Blob von der Anwendung lokal auf dem Datenträger zwischengespeichert, um für nachfolgende Benutzeranforderungen dann die zwischengespeicherte Version abzurufen.

Eine Möglichkeit, das Abrufen eines Blobs zu verhindern, wenn es seit der Zwischenspeicherung nicht geändert wurde, besteht darin, den GET-Vorgang mit einem bedingten Header für die Änderungszeit zu qualifizieren. Wenn das Blob das letzte Mal geändert wurde, nachdem es zwischengespeichert wurde, wird es abgerufen und erneut zwischengespeichert. Andernfalls wird das zwischengespeicherte Blob abgerufen, um eine optimale Leistung zu erzielen.

Sie können Ihre Anwendung auch so entwerfen, dass sie davon ausgeht, dass das Blob eine kurze Zeitspanne nach dem Abrufen unverändert bleibt. In diesem Fall muss die Anwendung nicht überprüfen, ob das Blob in diesem Intervall geändert wurde.

Konfigurations-, Such- und andere Daten, die häufig von der Anwendung verwendet werden, sind gut für die Zwischenspeicherung geeignet.  

Weitere Informationen zur Verwendung bedingter Header finden Sie unter [Angeben von bedingten Headern für Vorgänge des Blob-Diensts](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Hochladen von Daten in Batches

In einigen Szenarien können Sie Daten lokal aggregieren und dann regelmäßig als Batch hochladen, statt die einzelnen Daten sofort hochzuladen. Angenommen, eine Webanwendung erfasst Aktivitäten in einer Protokolldatei. Die Anwendung kann entweder Details zu jeder auftretenden Aktivität sofort in eine Tabelle hochladen (was viele Speichervorgänge erforderlich macht) oder die Aktivitätsdetails in einer lokalen Protokolldatei speichern und dann alle Aktivitätsdetails regelmäßig als Datei mit Trennzeichen in ein Blob hochladen. Wenn jeder Protokolleintrag 1 KB groß ist, können Sie Tausende Einträge in einer einzigen Transaktion hochladen. Eine einzelne Transaktion unterstützt das Hochladen eines Blobs mit einer Größe von bis zu 64 MiB. Der Entwickler muss beim Entwurf der Anwendung auch mögliche Clientgeräte- oder Uploadfehler einplanen. Wenn die Aktivitätsdaten für ein Zeitintervall und nicht für eine einzelne Aktivität heruntergeladen werden müssen, ist die Verwendung von Blob Storage der von Table Storage vorzuziehen.

## <a name="net-configuration"></a>.NET-Konfiguration

Für die Verwendung des .NET Frameworks sind in diesem Abschnitt einige Schnellkonfigurationseinstellungen aufgelistet, die eine deutliche Leistungsoptimierung bewirken.  Bei Verwendung anderer Sprachen sollten Sie sich informieren, ob es ähnliche Konzepte für die jeweilige Sprache gibt.  

### <a name="use-net-core"></a>Verwenden von .NET Core

Entwickeln Sie Ihre Azure Storage-Anwendungen mit .NET Core 2.1 oder höher, um die in diesen Versionen verfügbaren Leistungsverbesserungen zu nutzen. Wenn möglich, sollten Sie .NET Core 3.x verwenden.

Weitere Informationen zu den Leistungsverbesserungen in .NET Core finden Sie in den folgenden Blogbeiträgen:

- [Performance Improvements in .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/) (Leistungsverbesserungen in .NET Core 3.0)
- [Performance Improvements in .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/) (Leistungsverbesserungen in .NET Core 2.1)

### <a name="increase-default-connection-limit"></a>Erhöhen des Standardverbindungslimits

In .NET wird das Standardverbindungslimit (in der Regel zwei Verbindungen in einer Clientumgebung oder zehn in einer Serverumgebung) durch den folgenden Code auf 100 erhöht. In der Regel sollten Sie den Wert auf die ungefähre Anzahl der Threads, die von der Anwendung verwendet werden, setzen. Legen Sie das Verbindungslimit fest, bevor Sie Verbindungen öffnen.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Für andere Programmiersprachen erfahren Sie in der zugehörigen Dokumentation, wie Sie das Verbindungslimit festlegen.  

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

## <a name="copying-and-moving-blobs"></a>Kopieren und Verschieben von Blobs

Azure Storage bietet eine Reihe von Lösungen zum Kopieren und Verschieben von Blobs innerhalb eines Speicherkontos, zwischen Speicherkonten und zwischen lokalen Systemen und der Cloud. In diesem Abschnitt werden einige dieser Optionen im Hinblick auf ihre Auswirkungen auf die Leistung beschrieben. Informationen zum effizienten Übertragen von Daten in oder aus Blob Storage finden Sie unter [Auswählen einer Azure-Lösung für die Datenübertragung](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="blob-copy-apis"></a>APIs zum Kopieren von Blobs

Um Blobs übergreifend über Speicherkonten zu kopieren, verwenden Sie den [Put Block From URL](/rest/api/storageservices/put-block-from-url)-Vorgang. Mit diesem Vorgang werden Daten synchron aus einer beliebigen URL-Quelle in ein Blockblob kopiert. Durch die Verwendung des `Put Block from URL`-Vorgangs kann die erforderliche Bandbreite erheblich reduziert werden, wenn Daten übergreifend über Speicherkonten migriert werden. Da der Kopiervorgang auf der Dienstseite erfolgt, müssen Sie die Daten nicht herunterladen und erneut hochladen.

Um Daten innerhalb desselben Speicherkontos zu kopieren, verwenden Sie den [Copy Blob](/rest/api/storageservices/Copy-Blob)-Vorgang. Daten werden im selben Speicherkonto normalerweise schnell kopiert.  

### <a name="use-azcopy"></a>Verwenden von AzCopy

Das Befehlszeilenprogramm AzCopy bietet eine einfache und effiziente Möglichkeit für die Massenübertragung von Blobs in, aus und übergreifend über Speicherkonten. AzCopy ist für dieses Szenario optimiert und kann hohe Übertragungsraten erzielen. AzCopy, Version 10, verwendet den `Put Block From URL`-Vorgang, um Blobdaten übergreifend über Speicherkonten zu kopieren. Weitere Informationen finden Sie unter [Kopieren oder Verschieben von Daten in Azure Storage mit AzCopy v10](../common/storage-use-azcopy-v10.md).  

### <a name="use-azure-data-box"></a>Verwenden von Azure Data Box

Zum Importieren großer Datenmengen in Blob Storage sollten Sie die Azure Data Box-Familie für Offlineübertragungen verwenden. Von Microsoft bereitgestellte Data Box-Geräte bieten eine gute Möglichkeit, große Datenmengen in Azure zu übertragen, wenn Einschränkungen bei der Zeit, der Netzwerkverfügbarkeit oder den Kosten vorliegen. Weitere Informationen finden Sie in der [Azure Data Box-Dokumentation](../../databox/index.yml).

## <a name="content-distribution"></a>Inhaltsverteilung

Manchmal muss eine Anwendung denselben Inhalt für mehrere Benutzer bereitstellen (zum Beispiel ein Produkt-Demovideo auf der Startseite einer Website), die sich entweder in derselben oder in verschiedenen Regionen befinden. Verwenden Sie in diesem Szenario ein Content Delivery Network (CDN) wie Azure CDN, um Blobinhalte geografisch zu verteilen. Im Gegensatz zum Azure-Speicherkonto, das in genau einer Region vorhanden ist und Inhalte nicht mit niedriger Latenz an andere Regionen liefern kann, verwendet das Azure CDN Server in mehreren Rechenzentren weltweit. Darüber hinaus unterstützt ein CDN in der Regel höhere Ausgangsgrenzwerte als ein einzelnes Speicherkonto.  

Weitere Informationen zum Azure CDN finden Sie unter [Azure CDN](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Verwenden von Metadaten

Der Blob-Dienst unterstützt HEAD-Anforderungen, die Blobeigenschaften oder -metadaten enthalten können. Wenn Ihre Anwendung beispielsweise die EXIF (Exchangeable Image File Format)-Daten eines Fotos benötigt, kann sie das Foto abrufen und diese extrahieren. Um Bandbreite zu sparen und die Leistung zu verbessern, kann die Anwendung die EXIF-Daten in den Metadaten des Blobs speichern, wenn das Foto von der Anwendung hochgeladen wird. Anschließend können Sie einfach mithilfe einer HEAD-Anforderung die EXIF-Daten aus den Metadaten abrufen. Wenn Sie nur Metadaten und nicht den vollständigen Blobinhalt abrufen, sparen Sie eine beträchtliche Bandbreite und verringern die Verarbeitungszeit, die zum Extrahieren der EXIF-Daten erforderlich ist. Beachten Sie, dass pro Blob 8 KiB Metadaten gespeichert werden können.  

## <a name="upload-blobs-quickly"></a>Schnelles Hochladen von Blobs

Um BLOBs schnell hochzuladen, bestimmen Sie zunächst, ob Sie eines oder mehrere Blobs hochladen. Befolgen Sie die Anleitung unten, um die richtige Methode für Ihr Szenario auszuwählen.  

### <a name="upload-one-large-blob-quickly"></a>Schnelles Hochladen eines großen Blobs

Um ein einzelnes großes Blob schnell hochzuladen, kann eine Clientanwendung Blöcke oder Seiten parallel hochladen. Beachten Sie dabei die Skalierbarkeitsziele für einzelne Blobs und das Speicherkonto im Ganzen. Die Azure Storage-Clientbibliotheken unterstützen das parallele Hochladen. Beispielsweise können Sie die folgenden Eigenschaften verwenden, um die Anzahl von gleichzeitigen Anforderungen anzugeben, die in .NET oder Java zulässig sind. Clientbibliotheken für andere unterstützte Programmiersprachen bieten ähnliche Optionen.

- Legen Sie für .NET die [BlobRequestOptions.ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)-Eigenschaft fest.
- Rufen Sie für Java/Android die [BlobRequestOptions.setConcurrentRequestCount(final Integer concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount)-Methode auf.

### <a name="upload-many-blobs-quickly"></a>Schnelles Hochladen von mehreren Blobs

Um viele Blobs schnell hochzuladen, laden Sie die Blobs parallel hoch. Das gleichzeitige Hochladen funktioniert schneller als das Hochladen einzelner Blobs mit parallelen Blockuploads, da der Hochladevorgang auf mehrere Partitionen des Speicherdiensts verteilt wird. AzCopy führt Uploads standardmäßig parallel aus, was für dieses Szenario empfohlen wird. Weitere Informationen finden Sie unter [Erste Schritte mit AzCopy](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Auswählen des richtigen Blobtyps

Azure Storage unterstützt Block-, Anfüge- und Seitenblobs. Je nach Nutzungsszenario wirkt sich die Auswahl des Blobtyps auf die Leistung und Skalierbarkeit der Lösung aus.

Blockblobs sind geeignet, wenn Sie große Datenmengen effizient hochladen möchten. Beispielsweise würde eine Clientanwendung Blockblobs nutzen, um Fotos oder Videos in Blob Storage hochzuladen.

Anfügeblobs ähneln Blockblobs insofern, dass sie aus Blöcken bestehen. Wenn Sie ein Anfügeblob ändern, werden Blöcke nur am Ende des Blobs hinzugefügt. Anfügeblobs eignen sich beispielsweise für die Protokollierung, wenn eine Anwendung einem vorhandenen Blob Daten hinzufügen muss.

Seitenblobs sind geeignet, wenn die Anwendung zufällige Schreibvorgänge in den Daten ausführen muss. Beispielsweise werden Datenträger virtueller Azure-Computer als Seitenblobs gespeichert. Weitere Informationen finden Sie unter [Grundlegendes zu Blockblobs, Anfügeblobs und Seitenblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Nächste Schritte

- [Skalierbarkeits- und Leistungsziele für Blob Storage](scalability-targets.md)
- [Skalierbarkeits- und Leistungsziele für Storage Standard-Konten](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Status- und Fehlercodes](/rest/api/storageservices/Status-and-Error-Codes2)