---
title: 'Azure Storage Premium: Entwerfen für hohe Leistung'
description: Entwerfen Sie Hochleistungsanwendungen mithilfe von verwalteten Azure SSD Premium-Datenträgern. Storage Premium bietet Datenträgerunterstützung für hohe Leistung mit geringer Latenz für E/A-intensive Workloads, die auf virtuellen Azure-Computern ausgeführt werden.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: acdddcd95883d13393838a47281fb888ac2f9274
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500392"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Storage Premium: Entwurf für hohe Leistung

Dieser Artikel bietet Leitlinien zum Erstellen leistungsstarker Anwendungen mit Azure Storage Premium. Sie können die Anweisungen in diesem Dokument kombiniert mit den bewährten Methoden für hohe Leistung befolgen, die für von Ihrer Anwendung verwendeten Technologien gelten. Um die Leitlinien zu veranschaulichen, haben wir SQL Server in Storage Premium im gesamten Dokument als Beispiel verwendet.

Während wir uns in diesem Artikel mit leistungsbezogenen Szenarien auf der Speicherebene beschäftigen, ist es Ihre Aufgabe, die Anwendungsebene zu optimieren. Wenn z. B. Sie eine SharePoint-Farm in Azure Storage Premium hosten, können die SQL Server-Beispiele in diesem Artikel zum Optimieren des Datenbankservers verwenden. Optimieren Sie darüber hinaus den Webserver und Anwendungsserver der SharePoint-Farm, um die beste Leistung zu erzielen.

In diesem Artikel werden häufig gestellte Fragen zum Optimieren der Anwendungsleistung in Azure Storage Premium beantwortet:

* Wie kann die Leistung Ihrer Anwendung gemessen werden?  
* Warum erzielen Sie nicht die erwartete hohe Leistung?  
* Welche Faktoren beeinflussen die Anwendungsleistung in Storage Premium?  
* Wie wirken sich diese Faktoren auf die Leistung Ihrer Anwendung in Storage Premium aus?  
* Wie kann eine Optimierung hinsichtlich IOPS, Bandbreite und Latenz erfolgen?  

Wir stellen diese Leitlinien speziell für Storage Premium bereit, da in Storage Premium ausgeführte Workloads überaus leistungsabhängig sind. Sofern hilfreich, stellen wir Beispiele bereit. Einige dieser Leitlinien können auch für Anwendungen befolgt werden, die auf virtuellen IaaS-Computern mit Storage Standard-Datenträgern ausgeführt werden.

> [!NOTE]
> Manchmal ist ein vermutetes Problem mit der Datenträgerleistung tatsächlich ein Engpass. In solchen Fällen sollten Sie Ihre [Netzwerkleistung](../virtual-network/virtual-network-optimize-network-bandwidth.md) optimieren.
>
> Wenn Sie für Ihren Datenträger einen Benchmarktest durchführen möchten, finden Sie weitere Informationen in diesem Artikel:
>
> * Linux: [Benchmarktests für Ihre Anwendung in Azure Disk Storage](linux/disks-benchmarks.md)
> * Windows: [Benchmarktests für einen Datenträger](windows/disks-benchmarks.md)
>
> Wenn Ihr virtueller Computer den beschleunigten Netzwerkbetrieb unterstützt, stellen Sie sicher, dass dieser aktiviert ist. Wenn er nicht aktiviert ist, können Sie ihn sowohl unter [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) als auch [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) auf bereits bereitgestellten virtuellen Computern aktivieren.

Falls Sie noch nicht mit Storage Premium vertraut sind, lesen Sie zunächst die Artikel [Auswählen eines Azure-Datenträgertyps für IaaS VMs](disks-types.md) und [Skalierbarkeitsziele für Seitenblobspeicher mit Premium-Leistung](../storage/blobs/scalability-targets-premium-page-blobs.md).

## <a name="application-performance-indicators"></a>Anwendungsleistungsindikatoren

Die Leistung einer Anwendung (ob gut oder schlecht) bewerten wir mithilfe von Leistungsindikatoren wie z. B. Geschwindigkeit der Verarbeitung einer Benutzeranforderung, Menge der verarbeiteten Daten pro Anforderung, Anzahl der in einem bestimmten Zeitraum verarbeiteten Anforderungen und Wartezeit des Benutzers auf eine Antwort nach Übermittlung einer Anforderung. Die technische Begriffe für diese Leistungsindikatoren sind IOPS, Durchsatz oder Bandbreite und Latenz (Wartezeit).

In diesem Abschnitt erörtern wir gängige Leistungsindikatoren im Zusammenhang mit Storage Premium. Im folgenden Abschnitt, „Erfassen von Anwendungsanforderungen“, erfahren Sie, wie Sie diese Leistungsindikatoren für Ihre Anwendung messen. Im Anschluss lernen Sie unter „Optimieren der Anwendungsleistung“ die Faktoren, die sich auf diese Leistungsindikatoren auswirken, und Empfehlungen zu ihrer Optimierung kennen.

## <a name="iops"></a>IOPS

Mit der Einheit IOPS (I/O Operations per Second, E/A-Vorgänge/Sek.) wird die Anzahl der Anforderungen angegeben, die Ihre Anwendung pro Sekunde an Speicherdatenträger sendet. Ein E/A-Vorgang kann ein sequenzieller oder zufälliger Lese- oder Schreibvorgang sein. OLTP-Anwendungen (Online Transaction Processing, Onlinetransaktionsverarbeitung) wie die Website eines Onlinehändlers müssen viele gleichzeitige Benutzeranforderungen sofort verarbeiten. Die Benutzeranforderungen sind einfüge- und aktualisierungsintensive Datenbanktransaktionen, die die Anwendung rasch verarbeiten muss. Deshalb benötigen OLTP-Anwendungen eine sehr hohe IOPS-Leistung. Solche Anwendungen verarbeiten Millionen kleiner und zufälliger E/A-Anforderungen. Wenn Sie eine solche Anwendung haben, müssen Sie die Anwendungsinfrastruktur für die IOPS-Optimierung entwerfen. Im Abschnitt *Optimieren der Anwendungsleistung* weiter unten erörtert wir detailliert alle Faktoren, die Sie berücksichtigen müssen, um hohe IOPS-Raten zu erzielen.

Wenn Sie einen Storage Premium-Datenträger an Ihre Hochleistungs-VM anfügen, stellt Ihnen Azure gemäß der Datenträgerspezifikation eine garantierte IOPS-Anzahl bereit. Beispielsweise stellt ein Datenträger vom Typ „P50“ 7500 IOPS bereit. Jeder Typ von Hochleistungs-VM weist außerdem einen bestimmtes IOPS-Limit auf. Bei einer Standard-VM vom Typ GS5 ist das Limit beispielsweise 80.000 IOPS.

## <a name="throughput"></a>Throughput

Durchsatz oder Bandbreite ist die Menge der Daten, die Ihre Anwendung in einem angegebenen Intervall an die Speicherdatenträger überträgt. Wenn Ihre Anwendung E/A-Vorgänge mit hohen E/A-Einheitsgrößen durchführt, benötigt sie hohen Durchsatz. Data Warehouse-Anwendungen führen meist suchintensive Vorgänge, bei denen jeweils auf große Datenmengen zugegriffen wird, und üblicherweise Massenvorgänge durch. Aus diesem Grund erfordern solche Anwendungen einen höheren Durchsatz. Wenn Sie eine solche Anwendung haben, müssen Sie die Anwendungsinfrastruktur für eine Durchsatzoptimierung entwerfen. Im nächsten Abschnitt geht es im Detail um die Faktoren, die Sie optimieren müssen, um dies zu erreichen.

Wenn Sie einen Storage Premium-Datenträger an eine Hochleistungs-VM anfügen, bietet Azure Durchsatz gemäß der Spezifikation dieses Datenträgers. Ein P50-Datenträger stellt z.B. einen Datenträgerdurchsatz von 250 MB pro Sekunde bereit. Jeder Typ von Hochleistungs-VM weist außerdem ein bestimmtes Durchsatzlimit auf. Eine Standard-VM vom Typ GS5 bietet beispielsweise einen maximalen Durchsatz von 2.000 MB pro Sekunde.

Zwischen Durchsatz und IOPS gibt es, wie in der folgenden Formel dargestellt, eine Beziehung.

![Beziehung zwischen IOPS und Durchsatz](linux/media/premium-storage-performance/image1.png)

Aus diesem Grund ist es wichtig, die optimalen Durchsatz- und IOPS-Werte zu bestimmen, die Ihre Anwendung benötigt. Beim Versuch, einen der Faktoren zu optimieren, ist der andere ebenfalls betroffen. Im Abschnitt *Optimieren der Anwendungsleistung* weiter unten werden weitere Details zum Optimieren von IOPS und Durchsatz erläutert.

## <a name="latency"></a>Latency

Latenz ist die Zeit, die eine Anwendung zum Empfangen einer einzelnen Anforderung, deren Übertragung an die Speicherdatenträger und zum Zurücksenden der Antwort an den Client benötigt. Neben IOPS und Durchsatz ist dies ein weiterer wichtiger Messwert für die Leistung einer Anwendung. Die Latenz eines Storage Premium-Datenträgers ist die benötigte Zeit zum Abrufen der Informationen für eine Anforderung und deren Übermittlung zurück an die Anwendung. Storage Premium bietet durchgängig eine niedrige Latenz. Premium-Datenträger bieten für die meisten E/A-Vorgänge Latenzen im einstelligen Millisekundenbereich. Wenn Sie das Hostcache-Einstellung „ReadOnly“ für Storage Premium-Datenträger aktivieren, erhalten Sie bei Lesevorgängen eine wesentlich niedrigere Latenz. Der Datenträgercache wird weiter unten im Abschnitt *Optimieren der Anwendungsleistung* ausführlicher erläutert.

Wenn Sie Ihre Anwendung optimieren, um höhere IOPS- und Durchsatzwerte zu erzielen, wirkt sich dies auf die Latenz der Anwendung aus. Prüfen Sie nach einer Optimierung der Anwendungsleistung stets die Latenz, um unerwartet hohe Latenzen zu vermeiden.

Die folgenden Vorgänge auf Steuerungsebene auf verwalteten Datenträgern können das Verschieben des Datenträgers von einem Speicherort zu einem anderen beinhalten. Dies wird über eine Hintergrundkopie der Daten orchestriert, die bis zum Abschluss mehrere Stunden dauern kann, je nach der Menge der Daten auf den Datenträgern meist weniger als 24 Stunden. Während dieser Zeit können bei Ihrer Anwendung höhere Latenzen bei Lesevorgängen als üblich auftreten, da einige Lesevorgänge an den ursprünglichen Speicherort umgeleitet werden können, sodass es länger dauert, sie abzuschließen. Die Latenz beim Schreiben ist während dieses Zeitraums nicht betroffen.

- Aktualisieren des Speichertyps
- Trennen eines Datenträgers von einer VM und Anfügen an eine andere
- Erstellen eines verwalteten Datenträgers aus einer VHD
- Erstellen eines verwalteten Datenträgers aus einer Momentaufnahme
- Konvertieren von nicht verwalteten Datenträgern in verwaltete Datenträger

## <a name="performance-application-checklist-for-disks"></a>Prüfliste für die Anwendungsleistung für Datenträger

Der erste Schritt beim Entwerfen hochleistungsfähiger Anwendungen in Azure Storage Premium besteht darin, sich mit den Leistungsanforderungen der Anwendung vertraut zu machen. Nach dem Erfassen von Anforderungen können Sie Ihre Anwendung optimieren, um eine optimale Leistung zu erzielen.

Im vorherigen Abschnitt wurden mit IOPS, Durchsatz und Latenz die gängigsten Leistungsindikatoren vorgestellt. Sie müssen bestimmen, welche dieser Leistungsindikatoren für Ihre Anwendung wichtig sind, um Benutzern die gewünschte Funktionalität zu bieten. Eine hohe IOPS-Leistung spielt z. B. bei OLTP-Anwendungen, die Millionen von Transaktionen pro Sekunde verarbeiten, die größte Rolle. Ein hoher Durchsatz ist hingegen für Data Warehouse-Anwendungen von Bedeutung, die pro Sekunde große Datenmengen verarbeiten. Eine überaus niedrige Latenz ist entscheidend für Echtzeitanwendungen wie Websites für das Livestreaming von Video.

Messen Sie als Nächstes die maximalen Leistungsanforderungen der Anwendung während ihrer Lebensdauer. Nutzen Sie als Ausgangspunkt die nachstehende Beispielprüfliste. Zeichnen Sie die maximalen Leistungsanforderungen von Workloads während des Normalbetriebs, zu Spitzenzeiten und außerhalb der Geschäftszeiten auf. Indem Sie Anforderungen für alle Workloadstufen bestimmen, können Sie die Gesamtleistungsanforderungen Ihrer Anwendung feststellen. Der normale Workload einer E-Commerce-Website besteht beispielsweise aus den Transaktionen, die während der meisten Tage eines Jahres verarbeitet werden. Den Spitzenworkload der Website bilden die Transaktionen, die in der Vorweihnachtszeit oder bei Sonderverkaufsaktionen verarbeitet werden. Der Spitzenworkload fällt meist nur in einem begrenzten Zeitraum an, kann allerdings erfordern, dass Ihre Anwendung im Vergleich zum Normalbetrieb um das Zwei- bis Dreifache skaliert werden muss. Finden Sie die Anforderungen für das 50. Perzentil, das 90. Perzentil und 99. Perzentil heraus. Dadurch können Sie Ausreißer in den Leistungsanforderungen herausfiltern und sich auf die Maßnahmen für eine Optimierung entsprechend den richtigen Werten konzentrieren.

## <a name="application-performance-requirements-checklist"></a>Prüfliste für Anforderungen an die Anwendungsleistung

| **Leistungsanforderungen** | **50. Perzentil** | **90. Perzentil** | **99. Perzentil** |
| --- | --- | --- | --- |
| Maximal Transaktionen pro Sekunde | | | |
| % Lesevorgänge | | | |
| % Schreibvorgänge | | | |
| % zufällige Vorgänge | | | |
| % sequenzielle Vorgänge | | | |
| Größe der E/A-Anforderung | | | |
| Durchschnittlicher Durchsatz | | | |
| Maximal Throughput | | | |
| Min. Latency | | | |
| Durchschnittliche Latenz | | | |
| Maximal CPU | | | |
| Durchschnittliche CPU-Nutzung | | | |
| Maximal Arbeitsspeicher | | | |
| Durchschnittliche Arbeitsspeichernutzung | | | |
| Warteschlangenlänge | | | |

> [!NOTE]
> Es empfiehlt sich, diese Zahlen basierend auf dem erwarteten zukünftigen Wachstum Ihrer Anwendung zu skalieren. Es ist stets eine gute Idee, für Wachstum im Voraus zu planen, da es schwieriger sein könnte, die Infrastruktur zur Verbesserung der Leistung später zu ändern.

Wenn Sie eine vorhandene Anwendung haben, die in Storage Premium verschoben werden soll, erstellen Sie für diese Anwendung zunächst die obige Prüfliste. Erstellen Sie anschließend einen Prototyp der Anwendung in Storage Premium, und entwerfen Sie die Anwendung basierend auf den Leitlinien unter *Optimieren der Anwendungsleistung* weiter unten in diesem Dokument. Im nächsten Artikel werden die Tools beschrieben, die Sie verwenden können, um die Leistungsindikatoren zu erfassen.

### <a name="counters-to-measure-application-performance-requirements"></a>Indikatoren zum Messen der Leistungsanforderungen der Anwendung

Die beste Methode zum Messen der Leistungsanforderungen Ihrer Anwendung ist die Verwendung von Leistungsüberwachungstools, die vom Betriebssystem des Servers bereitgestellt werden. Sie können Systemmonitor (PerfMon) für Windows und iostat für Linux verwenden. Diese Tools erfassen Indikatoren für jede im vorherigen Abschnitt erläuterte Messgröße. Sie müssen die Werte dieser Indikatoren erfassen, wenn in der Anwendung Workloads im Normalbetrieb, zu Spitzenzeiten und außerhalb der Geschäftszeiten ausgeführt werden.

Die Leistungsindikatoren im Systemmonitor sind für Prozessor, Arbeitsspeicher und alle logischen und physischen Datenträger Ihres Servers verfügbar. Bei Verwenden von Storage Premium-Datenträgern mit einem virtuellen Computer gelten die Indikatoren für physische Datenträger für jeden Storage Premium-Datenträger. Die Indikatoren für logische Datenträger gelten für jedes Volume, das auf den Storage Premium-Datenträgern erstellt wurde. Sie müssen die Werte für die Datenträger erfassen, die den Workload Ihrer Anwendung hosten. Wenn es eine 1: 1-Zuordnung zwischen logischen und physischen Datenträgern gilt, beziehen Sie sich auf die Leistungsindikatoren für physische Datenträger und andernfalls auf die Leistungsindikatoren für logische Datenträger. Unter Linux erzeugt der Befehl „iostat“ einen Bericht der CPU- und Festplattenauslastung. Der Bericht zur Datenträgerauslastung bietet Statistiken pro physischem Gerät bzw. pro Partition. Wenn Sie einen Datenbankserver mit Daten- und Protokolldateien auf getrennten Datenträgern nutzen, erfassen Sie diese Daten für beide Datenträger. In der folgenden Tabelle werden die Leistungsindikatoren für Datenträger, Prozessor und Arbeitsspeicher beschrieben:

| Leistungsindikator | BESCHREIBUNG | Systemmonitor | iostat |
| --- | --- | --- | --- |
| **IOPS oder Transaktionen pro Sekunde** |Anzahl der an den Speicherdatenträger pro Sekunde erfolgten E/A-Anforderungen. |Lesevorgänge/s <br> Schreibvorgänge/s |tps <br> r/s <br> w/s |
| **Lese- und Schreibvorgänge auf Datenträger** |% der auf dem Datenträger ausgeführten Lese- und Schreibvorgänge. |% Lesezeit <br> % Schreibzeit |r/s <br> w/s |
| **Durchsatz** |Menge der Daten, die pro Sekunde auf dem Datenträger gelesen oder geschrieben wurde. |Byte gelesen/s <br> Byte geschrieben/s |kB_read/s <br> kB_wrtn/s |
| **Latenz** |Zeitaufwand für das Ausführen einer E/A-Anforderung an den Datenträger. |Mittlere Sek./Lesevorgänge <br> Mittlere Sek./Schreibvorgänge |await <br> svctm |
| **E/A-Größe** |Größe der an die Speicherdatenträger gesendeten E/A-Anforderungen. |Mittlere Bytes/Lesevorgang <br> Mittlere Bytes/Schreibvorgang |avgrq-sz |
| **Warteschlangenlänge** |Anzahl der ausstehenden E/A-Anforderungen, die vom Speicherdatenträger gelesen oder auf diesen geschrieben werden sollen. |Aktuelle Warteschlangenlänge |avgqu-sz |
| **Max. Arbeitsspeicher** |Für die reibungslose Ausführung der Anwendung erforderlicher Arbeitsspeicher |Zugesicherte verwendete Bytes (%) |vmstat verwenden |
| **Max. CPU** |Für die reibungslose Ausführung der Anwendung erforderliche CPU-Größe |% Prozessorzeit |%util |

Weitere Informationen zu [iostat](https://linux.die.net/man/1/iostat) und [PerfMon](/windows/win32/perfctrs/performance-counters-portal) (Systemmonitor)



## <a name="optimize-application-performance"></a>Optimieren der Anwendungsleistung

Die wichtigsten Faktoren für die Leistung einer Anwendung, die in Storage Premium ausgeführt wird, sind die Art der E/A-Anforderungen, VM-Größe, Datenträgergröße, Anzahl der Datenträger, die Datenträgerzwischenspeicherung, Multithreading und Warteschlangenlänge. Sie können einige dieser Faktoren mithilfe vom System bereitgestellter Einstellungsmöglichkeiten steuern. Die meisten Anwendungen bieten jedoch möglicherweise keine Option, die E/A-Größe und Warteschlangenlänge direkt zu ändern. In SQL Server können Sie z. B. die E/A-Größe und Warteschlangenlänge nicht festlegen. SQL Server wählt optimale Einstellungen für E/A-Größe und Warteschlangenlänge, um die beste Leistung zu erzielen. Es ist wichtig, die Auswirkungen beider Arten von Faktoren auf die Leistung Ihrer Anwendung zu verstehen, damit Sie entsprechende Ressourcen zum Erfüllen der Leistungsanforderungen bereitstellen können.

Nehmen Sie in diesem gesamten Abschnitt Bezug auf die von Ihnen erstellte Prüfliste mit den Anwendungsanforderungen, um zu bestimmen, was für die Optimierung der Leistung Ihrer Anwendung erforderlich ist. Auf dieser Basis können Sie bestimmen, welche Faktoren in diesem Abschnitt Sie optimieren müssen. Führen Sie zum Überprüfen der Auswirkungen der einzelnen Faktoren auf die Anwendungsleistung Benchmarktools aus. Der am Ende dieses Artikels verlinkte Artikel zu Benchmarks enthält eine Anleitung zum Ausführen gängiger Benchmarktools für virtuelle Windows- und Linux-Computer.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Optimierung von IOPS, Durchsatz und Latenz auf einen Blick

In der folgenden Tabelle sind die Leistungsfaktoren und die erforderlichen Schritte zum Optimieren von IOPS, Durchsatz und Latenz aufgeführt. In den Abschnitten im Anschluss an diese Übersicht werden die einzelnen Faktoren ausführlicher beschrieben.

Weitere Informationen zu VM-Größen und zu den für die einzelnen VM-Typen verfügbaren IOPS, Durchsätzen und Latenzen finden Sie unter [Größen für virtuelle Computer in Azure](sizes.md).

| | **IOPS** | **Durchsatz** | **Latenz** |
| --- | --- | --- | --- |
| **Beispielszenario** |OLTP-Anwendung eines Großunternehmens, die eine sehr hohe Rate von Transaktionen pro Sekunde benötigt. |Data Warehouse-Anwendung eines Großunternehmens, die große Datenmengen verarbeitet. |Nahezu in Echtzeit arbeitende Anwendungen, die Antworten auf Benutzeranforderungen sofort benötigen, wie z. B. Onlinespiele. |
| **Leistungsfaktoren** | &nbsp; | &nbsp; | &nbsp; |
| **E/A-Größe** |Kleinere E/A-Größe führt zu mehr IOPS. |Höhere E/A-Größe führt zu höherem Durchsatz. | &nbsp;|
| **Größe des virtuellen Computers** |Wählen Sie eine VM-Größe, deren IOPS die Anforderung Ihrer Anwendung übersteigt. |Wählen Sie eine VM-Größe aus, deren Durchsatzlimit die Anforderung Ihrer Anwendung übersteigt. |Wählen Sie eine VM-Größe, deren Skalierungslimits die Anforderung Ihrer Anwendung übersteigen. |
| **Datenträgergröße** |Wählen Sie eine Datenträgergröße, deren IOPS die Anforderung Ihrer Anwendung übersteigt. |Wählen Sie eine Datenträgergröße, deren Durchsatzlimit die Anforderung Ihrer Anwendung übersteigt. |Wählen Sie eine Datenträgergröße, deren Skalierungslimits die Anforderung Ihrer Anwendung übersteigen. |
| **Skalierungslimits für virtuelle Computer und Datenträger** |Das IOPS-Limit der gewählten VM-Größe muss größer als die IOPS-Gesamtkapazität von Speicherdatenträgern sein, die angefügt sind. |Das Durchsatzlimit der gewählten VM-Größe muss größer als der Gesamtdurchsatz von Storage Premium-Datenträgern sein, die angefügt sind. |Die Skalierungslimits der gewählten VM-Größe müssen größer als die Gesamtskalierungslimits von Storage Premium-Datenträgern sein, die angefügt sind. |
| **Nutzung des Datenträgercaches** |Aktivieren Sie den „ReadOnly“-Cache auf Storage Premium-Datenträgern mit hohem Aufkommen von Lesevorgängen, um eine höhere IOPS-Leseleistung zu erhalten. | &nbsp; |Aktivieren Sie den „ReadOnly“-Cache auf Storage Premium-Datenträgern mit hohem Aufkommen von Lesevorgängen, um sehr niedrige Leselatenzen zu erzielen. |
| **Datenträgerstriping** |Verwenden Sie mehrere Datenträger, und verbinden Sie sie per Striping, um durch die Kombination ein höheres IOPS- und Durchsatzlimit zu erreichen. Das kombinierte Limit pro VM muss höher als die kombinierten Limits angefügter Premium-Datenträger sein. | &nbsp; | &nbsp; |
| **Stripegröße** |Kleinere Stripegröße für zufällige kleine E/A-Muster in OLTP-Anwendungen. Wählen Sie beispielsweise für eine OLTP-Anwendung mit SQL Server eine Stripegröße von 64 KB. |Höhere Stripegröße für sequenzielle E/A-Muster in Data Warehouse-Anwendungen. Wählen Sie beispielsweise für Data Warehouse-Anwendungen mit SQL Server die Stripegröße 256 KB. | &nbsp; |
| **Multithreading** |Verwenden Sie Multithreading, um eine höhere Anzahl von Anforderungen in Storage Premium zu übertragen, was eine höhere IOPS- und Durchsatzrate zur Folge hat. Legen Sie z. B. für SQL Server einen hohen MAXDOP-Wert fest damit SQL Server mehr CPUs zugewiesen werden. | &nbsp; | &nbsp; |
| **Warteschlangenlänge** |Höhere Warteschlangenlänge führt zu mehr IOPS. |Höhere Warteschlangenlänge führt zu höherem Durchsatz. |Niedrigere Warteschlangenlänge führt zu niedrigeren Latenzen. |

## <a name="nature-of-io-requests"></a>Art der E/A-Anforderungen

Eine E/A-Anforderung ist eine E/A-Vorgangseinheit, die Ihre Anwendung ausführt. Das Bestimmen der Art der E/A-Anforderungen – zufällig oder sequenziell, Lese- oder Schreibvorgang, klein oder groß – hilft beim Ermitteln der Leistungsanforderungen Ihrer Anwendung. Sie müssen sich mit der Art der E/A-Anforderungen vertraut machen, um beim Entwurf der Anwendungsinfrastruktur die richtigen Entscheidungen zu treffen. E/A-Vorgänge müssen gleichmäßig verteilt sein, um die bestmögliche Leistung zu erzielen.

Die E/A-Größe ist einer der wichtigsten Faktoren. Die E/A-Größe ist die Größe des angeforderten E/A-Vorgangs, den Ihre Anwendung generiert hat. Die E/A-Größe hat einen erheblichen Einfluss auf die Leistung, insbesondere auf die IOPS und Bandbreite, die die Anwendung erzielen kann. Die folgende Formel zeigt die Beziehung zwischen IOPS, E/A-Größe und Bandbreite/Durchsatz.  
    ![Eine Abbildung der Gleichung IOPS × E/A-Größe = Durchsatz](media/premium-storage-performance/image1.png)

Einige Anwendungen ermöglichen Ihnen das Ändern der E/A-Größe, andere hingegen nicht. Beispielsweise bestimmt SQL Server selbst die optimale E/A-Größe und bietet Benutzern keine Möglichkeiten, sie zu ändern. Oracle bietet dagegen den Parameter [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815), mit dem Sie die E/A-Anforderungsgröße der Datenbank konfigurieren können.

Wenn Sie eine Anwendung verwenden, die das Ändern der E/A-Größe nicht ermöglicht, befolgen Sie die Leitlinien in diesem Artikel, um die Leistungsindikatoren zu optimieren, die für Ihre Anwendung am relevantesten sind. Beispiel:

* Eine OLTP-Anwendung generiert Millionen kleiner und zufälliger E/A-Anforderungen. Zum Verarbeiten dieser Art von E/A-Anforderungen müssen Sie die Anwendungsinfrastruktur für das Erzielen von mehr IOPS entwerfen.  
* Eine Data Warehouse-Anwendung generiert große und sequenzielle E/A-Anforderungen. Zum Verarbeiten dieser Art von E/A-Anforderungen müssen Sie die Anwendungsinfrastruktur für das Erzielen von mehr Bandbreite bzw. Durchsatz entwerfen.

Wenn Sie eine Anwendung verwenden, die das Ändern der E/A-Größe ermöglicht, sollten Sie neben den anderen Leitlinien für die Leistung diese Faustregel für die E/A-Größe befolgen.

* Kleinere E/A-Größe führt zu mehr IOPS. Beispiel: 8 KB für eine OLTP-Anwendung.  
* Höhere E/A-Größe führt zu mehr Bandbreite/Durchsatz. Beispiel: 1024 KB für eine Data Warehouse-Anwendung.

Es folgt ein Beispiel, wie Sie IOPS bzw. Durchsatz/Bandbreite für Ihre Anwendung berechnen können. Angenommen, eine Anwendung verwendet einen P30-Datenträger. Die maximale IOPS- bzw. Durchsatz-/Bandbreitenrate eines P30-Datenträgers ist 5000 IOPS bzw. 200 MB pro Sekunde. Wenn Ihre Anwendung nun die maximale IOPS-Rate des P30-Datenträgers benötigt und Sie eine kleinere E/A-Größe wie 8 KB verwenden, beträgt die resultierende Bandbreite 40 MB pro Sekunde. Wenn Ihre Anwendung dagegen die maximale Durchsatz-/Bandbreitenkapazität des P30-Datenträgers benötigt und Sie eine höhere E/A-Größe wie 1024 KB verwenden, erzielen Sie weniger als 200 IOPS. Optimieren Sie die E/A-Größe daher so, dass Ihre Anwendung die Anforderungen sowohl an IOPS als auch an Durchsatz/Bandbreite erfüllt. In der folgenden Tabelle sind die verschiedenen E/A-Größen und ihre entsprechenden IOPS- und Durchsatzraten für einen P30-Datenträger zusammengefasst.

| Anwendungsanforderung | E/A-Größe | IOPS | Durchsatz/Bandbreite |
| --- | --- | --- | --- |
| Max. IOPS |8 KB |5\.000 |40 MB pro Sekunde |
| Max. Durchsatz |1024 KB |200 |200 MB pro Sekunde |
| Max. Durchsatz + hohe IOPS-Rate |64 KB |3\.200 |200 MB pro Sekunde |
| Max. IOPS + hoher Durchsatz |32 KB |5\.000 |160 MB pro Sekunde |

Um eine IOPS-Rate und Bandbreite zu erzielen, die höher als der Maximalwert eines einzelnen Storage Premium-Datenträgers ist, verwenden Sie mehrere Premium-Datenträger in einem Stripeset. Fügen Sie z. B. zwei P30-Datenträger zu einem Stripeset zusammen, um in dieser Kombination eine IOPS-Rate von 10.000 oder einen Durchsatz von 400 MB pro Sekunde zu erzielen. Wie im nächsten Abschnitt erläutert, müssen Sie eine VM-Größe wählen, die die IOPS-Rate und den Durchsatz der kombinierten Datenträger unterstützt.

> [!NOTE]
> Wenn Sie entweder die IOPS-Rate oder den Durchsatz erhöhen, sollten Sie sicherstellen, dass Sie dabei nicht das Durchsatz- oder IOPS-Limit des Datenträgers oder der VM überschreiten.

Führen Sie zum Überprüfen der Auswirkungen der E/A-Größe auf die Anwendungsleistung in Ihrer VM und auf Ihren Datenträgern Benchmarktools aus. Erstellen Sie mehrere Testläufe, und wählen Sie für jeden Lauf eine andere E/A-Größe, um die Auswirkungen zu prüfen. Ausführlichere Informationen finden Sie im am Ende dieses Artikels verlinkten Artikel zu Benchmarks.

## <a name="high-scale-vm-sizes"></a>Größen von Hochleistungs-VMs

Beim Entwerfen einer Anwendung ist eine der ersten Aufgaben das Wählen einer VM zum Hosten Ihrer Anwendung. Storage Premium bietet verschiedene Größen von Hochleistungs-VMs zum Ausführen von Anwendungen, die eine höhere Rechenleistung und einen lokalen Datenträger mit hoher E/A-Leistung benötigen. Diese VMs bieten schnellere Prozessoren, ein höheres Arbeitsspeicher/Kern-Verhältnis und ein SSD-Laufwerk (Solid State Drive) als lokalen Datenträger. Beispiele für Hochleistungs-VMs, die Storage Premium unterstützen, sind die VMs der DS- und GS-Serie.

Hochleistungs-VMs stehen in mehreren Größen mit jeweils einer anderen Anzahl von CPU-Kernen, Arbeitsspeicher- und temporären Datenträgergröße und einem anderen Betriebssystem zur Verfügung. Jede VM-Größe weist außerdem eine maximale Anzahl von Datenträgern auf, die Sie an die VM anfügen können. Daher beeinflusst die gewählte VM-Größe die Verarbeitungs-, Arbeitsspeicher- und Speicherkapazität, die für Ihre Anwendung zur Verfügung steht. Sie hat außerdem Einfluss auf die Compute- und Speicherkosten. Nachstehend sind beispielsweise die Spezifikationen der maximalen VM-Größen in der DS- und GS-Serie aufgeführt:

| Größe des virtuellen Computers | CPU-Kerne | Arbeitsspeicher | VM-Datenträgergrößen | Maximal Anzahl Datenträger | Cachegröße | IOPS | Limits für Bandbreite, Cache und E/A |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |Betriebssystem = 1023 GB <br> Lokales SSD = 224 GB |32 |576 GB |50.000 IOPS <br> 512 MB pro Sekunde |4\.000 IOPS und 33 MB pro Sekunde |
| Standard_GS5 |32 |448 GB |Betriebssystem = 1023 GB <br> Lokales SSD = 896 GB |64 |4\.224 GB |80.000 IOPS <br> 2\.000 MB pro Sekunde |5\.000 IOPS und 50 MB pro Sekunde |

Eine vollständige Übersicht mit allen verfügbaren Azure-VM-Größen finden Sie unter [Größen für virtuelle Computer in Azure](sizes.md). Wählen Sie eine VM-Größe, die Ihre Anforderungen an die Anwendungsleistung erfüllen und entsprechend skaliert werden kann. Berücksichtigen Sie darüber hinaus folgende wichtige Aspekte bei der Wahl der VM-Größe.

*Skalierungslimits*  
Die maximalen IOPS-Limits pro VM und Datenträger sind unterschiedlich und voneinander unabhängig. Stellen Sie sicher, dass die Anwendung die IOPS innerhalb der Limits der VM sowie der angefügten Storage Premium-Datenträger unterstützt. Andernfalls wird die Anwendungsleistung gedrosselt.

Beispiel: Angenommen, eine Anwendungsanforderung ist ein IOPS-Wert von maximal 4.000. Um dies zu erreichen, stellen Sie einen P30-Datenträger in einer VM der DS1-Serie bereit. Der P30-Datenträger kann bis zu 5.000 IOPS bieten. Die VM der DS1-Serie ist jedoch auf 3.200 IOPS beschränkt. Daher wird die Anwendungsleistung gemäß dem VM-Limit auf 3.200 IOPS verringert. Um dies zu vermeiden, wählen eine VM- und Datenträgergröße, die beide Anwendungsanforderungen erfüllt.

*Betriebskosten*  
In vielen Fällen ist es möglich, dass die Gesamtbetriebskosten bei der Verwendung von Storage Premium niedriger als bei der Verwendung von Storage Standard sind.

Nehmen wir als Beispiel eine Anwendung, die 16.000 IOPS benötigt. Um diese Leistung zu erzielen, benötigen Sie eine Azure-IaaS-VM vom Typ „Standard\_D14“, die bei Verwenden von 32 Standardspeicher-Datenträgern mit je 1 TB eine maximale IOPS-Rate von 16.000 bietet. Jeder Storage Standard-Datenträger mit 1 TB kann maximal 500 IOPS erzielen. Die geschätzten Kosten für diese VM pro Monat liegen bei 1.570 $. Die monatliche Kosten der 32 Storage Standard-Datenträger betragen 1.638 $. Die geschätzten monatlichen Gesamtkosten betragen 3.208 $.

Wenn Sie jedoch dieselbe Anwendung in Storage Premium hosten, benötigen Sie eine kleinere VM-Größe und weniger Storage Premium-Datenträger, wodurch die Gesamtkosten gesenkt werden. Eine VM vom Typ „Standard\_DS13“ kann mithilfe von vier P30-Datenträgern die Anforderung von 16.000 IOPS erfüllen. Die VM vom Typ DS13 bietet maximal 25.600 IOPS, und jeder P30-Datenträger bietet maximal 5.000 IOPS. Diese Konfiguration kann insgesamt 5.000 x 4 = 20.000 IOPS bieten. Die geschätzten Kosten für diese VM pro Monat liegen bei 1.003 $. Die monatlichen Kosten der vier Storage Premium-Datenträger vom Typ P30 belaufen sich auf 544,34 $. Die geschätzten monatlichen Gesamtkosten betragen 1.544 $.

In der folgenden Tabelle wird die Aufschlüsselung der Kosten für Storage Standard und Storage Premium zusammengefasst.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **VM-Kosten pro Monat** |1\.570,58 $ (Standard\_D14) |1\.003,66 $ (Standard\_DS13) |
| **Datenträgerkosten pro Monat** |1\.638,40 $ (32 1-TB-Festplatten) |544,34 $ (4 P30-Datenträger) |
| **Gesamtkosten pro Monat** |3\.208,98 $ |1\.544,34 $ |

*Linux-Distributionen*  

Mit Azure Storage Premium erhalten Sie unter Windows und Linux das gleiche Maß an Leistung für virtuelle Computer. Wir unterstützen viele Arten von Linux-Distributionen. Eine vollständige Liste finden Sie [hier](linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Es ist wichtig zu beachten, dass verschiedene Distributionen für verschiedene Arten von Workloads besser geeignet sind. Je nach Distribution, in der Ihr Workload ausgeführt wird, sind die Leistungsgrade unterschiedlich. Testen Sie die Linux-Distributionen mit Ihrer Anwendung, und wählen Sie diejenige, die am besten geeignet ist.

Überprüfen Sie bei Ausführen von Linux mit Storage Premium die neuesten Updates hinsichtlich erforderlicher Treiber, um eine hohe Leistung zu gewährleisten.

## <a name="premium-storage-disk-sizes"></a>Größen von Storage Premium-Datenträgern

Azure Storage Premium bietet verschiedene Größen, sodass Sie eine wählen können, die Ihren Anforderungen am besten entspricht. Jede Datenträgergröße hat ein anderes Skalierungslimit hinsichtlich IOPS, Bandbreite und Speicher. Wählen Sie abhängig von den Anwendungsanforderungen und der Größe der Hochleistungs-VM die richtige Storage Premium-Datenträgergröße. Die folgende Tabelle zeigt die verschiedenen Datenträgergrößen und ihre Kapazitäten. Die Datenträgergrößen P4, P6, P15, P60, P70 und P80 werden aktuell nur für verwaltete Datenträger unterstützt.

[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Die zu wählende Anzahl von Datenträgern hängt von der gewählten Datenträgergröße ab. Zum Erfüllen Ihrer Anwendungsanforderung können Sie entweder einen einzelnen P50- oder mehrere P10-Datenträger verwenden. Berücksichtigen Sie bei Ihrer Wahl die nachstehenden Aspekte.

*Skalierungslimits (IOPS und Durchsatz)*  
Die IOPS- und Durchsatzlimits der einzelnen Premium-Datenträgergrößen sind unterschiedlich und unabhängig von den VM-Skalierungslimits. Vergewissern Sie sich, dass die gesamte IOPS- und Durchsatzrate der Datenträger innerhalb der Skalierungslimits der gewählten VM-Größe liegen.

Beispiel: Angenommen, eine Anwendungsanforderung hat einen Durchsatz von maximal 250 MB/s, und Sie verwenden eine VM vom Typ DS4 mit einem einzelnen P30-Datenträger. Die VM vom Typ DS4 kann einen Durchsatz von bis zu 256 MB/s bieten. Ein einzelner P30-Datenträger weist jedoch eine Durchsatzbeschränkung von 200 MB/s auf. Die Anwendung wird daher aufgrund der Beschränkung des Datenträgers auf 200 MB/s beschränkt. Um diese Beschränkung zu umgehen, stellen Sie für die VM mehr als einen Datenträger bereit, oder ändern Sie die Größe Ihrer Datenträger in P40 oder P50.

> [!NOTE]
> Leseanforderungen, die aus dem Cache erfüllt werden, bleiben bei der IOPS- und Durchsatzleistung des Datenträgers unberücksichtigt und unterliegen deshalb keinen Datenträgerlimits. Für den Cache gelten separate IOPS- und Durchsatzlimits pro VM.
>
> Beispielsweise erfolgen Ihre Lese- und Schreibvorgänge anfänglich mit 60 MB/s bzw. 40 MB/s. Mit der Zeit füllt sich der Cache auf, sodass immer mehr Leseanforderungen aus dem Cache erfüllt werden. Dadurch erhalten Sie einen höheren Schreibdurchsatz vom Datenträger.

*Anzahl der Datenträger*  
Bestimmen Sie die benötigte Anzahl der Datenträger, indem Sie die Anwendungsanforderungen prüfen. Für jede VM gilt auch ein Limit für die Anzahl der Datenträger, die an die VM angefügt werden können. In der Regel ist dies die doppelte Anzahl der Kerne. Stellen Sie sicher, dass die gewählte VM-Größe die erforderliche Anzahl von Datenträgern unterstützt.

Wie schon erwähnt, bieten Storage Premium-Datenträger eine höhere Leistung als Storage Standard-Datenträger. Wenn Sie also Ihre Anwendung aus einer Azure IaaS-VM mit Storage Standard zu Storage Premium migrieren, benötigen Sie wahrscheinlich weniger Premium-Datenträger, um für Ihre Anwendung dieselbe oder eine höhere Leistung zu erzielen.

## <a name="disk-caching"></a>Nutzung des Datenträgercaches

Hochleistungs-VMs mit Azure Storage Premium arbeiten mit einer mehrschichtigen Cachetechnologie mit dem Namen BlobCache. BlobCache verwendet für das Zwischenspeichern eine Kombination aus Host-RAM und lokalem SSD-Laufwerk. Dieser Cache ist für die permanenten Storage Premium-Datenträger und die lokalen VM-Datenträger verfügbar. Standardmäßig ist diese Cacheeinstellung auf „Read/Write“ für Betriebssystem-Datenträger und auf und „ReadOnly“ für in Storage Premium gehostete Datenträger festgelegt. Bei aktiviertem Cache auf den Storage Premium-Datenträgern können die Hochleistungs-VMs überaus hohe Leistungsgrade erreichen, die die zugrunde liegende Datenträgerleistung übersteigen.

> [!WARNING]
> Die Zwischenspeicherung von Datenträgern wird nicht für Datenträger mit einer Größe von 4 TiB und höher unterstützt. Wenn mehrere Datenträger an Ihre VM angefügt sind, unterstützt jeder Datenträger mit 4 TiB oder weniger das Zwischenspeichern.
>
> Durch Ändern der Cacheeinstellung eines Azure-Datenträgers wird der Zieldatenträger getrennt und erneut angefügt. Wenn es sich um den Betriebssystemdatenträger handelt, wird der virtuelle Computer neu gestartet. Beenden Sie alle Anwendungen und Dienste, die von dieser Unterbrechung betroffen sein könnten, bevor Sie die Cacheeinstellung des Datenträgers ändern. Wenn diese Empfehlungen nicht befolgt werden, kann dies zu einer Beschädigung der Daten führen.

Weitere Informationen zur Funktionsweise von BlobCache finden Sie im Blogbeitrag [Inside Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

Es ist wichtig, den Cache für die richtige Gruppe von Datenträgern zu aktivieren. Ob Sie den Cache auf einem Premium-Datenträger aktivieren oder nicht, hängt vom Workloadmuster ab, das der Datenträger verarbeiten muss. Die folgende Tabelle zeigt die standardmäßigen Cacheeinstellungen für Betriebssystem und Datenträger.

| **Datenträgertyp** | **Standardeinstellung für den Cache** |
| --- | --- |
| Betriebssystem-Datenträger |ReadWrite |
| Datenträger |ReadOnly |

Es folgen die empfohlenen Cacheeinstellungen für Datenträger:

| **Cacheeinstellung für Datenträger** | **Empfehlung zur Verwendung dieser Einstellung** |
| --- | --- |
| Keine |Konfigurieren Sie „host-cache“ mit „None“ für Datenträger mit hohem oder ausschließlichem Schreibzugriff. |
| ReadOnly |Konfigurieren Sie „host-cache“ mit „ReadOnly“ für Datenträger mit reinem Lese- und Lese-/Schreibzugriff. |
| ReadWrite |Konfigurieren Sie „host-cache“ nur dann mit „ReadWrite“, wenn Ihre Anwendung zwischengespeicherte Daten bei Bedarf ordnungsgemäß auf beständige Datenträger schreiben kann. |

*ReadOnly*  
Durch Konfigurieren des „ReadOnly“-Caches für Storage Premium-Datenträger können Sie für Ihre Anwendung eine niedrige Leselatenz und einen sehr hohe Leserate hinsichtlich IOPS und Durchsatz erzielen. Hierfür gibt es zwei Gründe:

1. Aus dem Cache erfüllte Leseanforderungen, der sich im Arbeitsspeicher der VM und auf dem lokalen SSD-Laufwerk befindet, sind wesentlich schneller als Lesevorgänge vom Datenträger, der sich in Azure Blob Storage befindet.  
1. Storage Premium rechnet die aus dem Cache erfüllten Leseanforderungen nicht zur IOPS- und Durchsatzrate des Datenträgers. Aus diesem Grund kann Ihre Anwendung eine höhere Gesamtrate bei IOPS und Durchsatz erzielen.

*ReadWrite*  
Für die Betriebssystem-Datenträger ist der „ReadWrite“-Cache standardmäßig aktiviert. Wir haben vor Kurzem Unterstützung des „ReadWrite“-Caches auf Datenträgern hinzugefügt. Wenn Sie den „ReadWrite“-Cache nutzen, benötigen Sie eine ordnungsgemäße Möglichkeit zum Schreiben der Daten aus dem Cache auf beständige Datenträger. SQL Server übernimmt beispielsweise selbst das Schreiben von Daten im Cache auf beständige Speicherdatenträger. Das Verwenden eines „ReadWrite“-Caches mit einer Anwendung, die die benötigten Daten nicht beständig speichert, kann zu Datenverlusten führen, sollte die VM abstürzen.

*None*  
Die Option **None** wird aktuell nur für reguläre Datenträger unterstützt. Für Betriebssystemdatenträger wird sie nicht unterstützt. Wenn Sie **None** für einen Betriebssystemdatenträger festlegen, wird die Einstellung intern überschrieben und auf **ReadOnly** festgelegt.

Als Beispiel können Sie diese Leitlinien auf SQL Server in Storage Premium anwenden, indem Sie die folgenden Schritte ausführen:

1. Aktivieren Sie den „ReadOnly“-Cache auf Storage Premium-Datenträgern, die Datendateien hosten.  
   a.  Die schnellen aus dem Cache erfolgenden Lesevorgänge verkürzen die Abfragezeit von SQL Server, da Daten wesentlich schneller aus dem Cache als von den Datenträgern direkt abgerufen werden.  
   b.  Das Erfüllen von Leseanforderungen aus dem Cache bedeutet, dass auf den Premium-Datenträgern zusätzlicher Durchsatz verfügbar ist. SQL Server kann diesen zusätzlichen Durchsatz für das Abrufen von mehr Datenseiten und andere Vorgänge wie Sichern/Wiederherstellen, Batchladevorgänge und Indexneuerstellungen nutzen.  
1. Konfigurieren Sie die Cacheeinstellung „None“ für Storage Premium-Datenträger, die Protokolldateien hosten.  
   a.  Protokolldateien zeichnen sich hauptsächlich durch schreibintensive Vorgänge aus. Aus diesem Grund profitieren sie nicht vom „ReadOnly“-Cache.

## <a name="optimize-performance-on-linux-vms"></a>Optimieren der Leistung auf Linux-VMs

Für alle Premium-SSDs oder Ultra-Datenträger können Sie möglicherweise „Barrieren“ für Dateisysteme auf dem Datenträger deaktivieren, um die Leistung zu verbessern, wenn bekannt ist, dass keine Caches vorhanden sind, bei denen Daten verloren gehen könnten.  Wenn für die Datenträgerzwischenspeicherung in Azure „ReadOnly“ oder „None“ festgelegt ist, können Sie Barrieren deaktivieren.  Wenn für die Zwischenspeicherung jedoch „ReadWrite“ festgelegt ist, sollten Barrieren aktiviert bleiben, um die Dauerhaftigkeit	 des Schreibens sicherzustellen.  Barrieren sind in der Regel standardmäßig aktiviert, aber Sie können Barrieren abhängig vom Dateisystemtyp mithilfe einer der folgenden Methoden deaktivieren:

* Verwenden Sie für **reiserFS** zum Deaktivieren von Barrieren die Bereitstellungsoption „barrier=none“.  Verwenden Sie „barrier=flush“, um Barrieren explizit zu aktivieren.
* Verwenden Sie für **ext3/ext4** zum Deaktivieren von Barrieren die Bereitstellungsoption „barrier=0“.  Verwenden Sie „barrier=1“, um Barrieren explizit zu aktivieren.
* Verwenden Sie für **XFS** zum Deaktivieren von Barrieren die Bereitstellungsoption „nobarrier“.  Verwenden Sie „barrier“, um Barrieren explizit zu aktivieren.  Beachten Sie, dass in neueren Linux-Kernelversionen der Entwurf des XFS-Dateisystems immer die Dauerhaftigkeit gewährleistet und das Deaktivieren von Barrieren keine Auswirkungen hat.  

## <a name="disk-striping"></a>Datenträgerstriping

Wenn an eine Hochleistungs-VM mehrere beständige Storage Premium-Datenträger angefügt werden, können diese Datenträger ein Stripeset bilden, um ihre IOPS-, Bandbreiten- und Speicherkapazitäten zusammenzuführen.

Unter Windows können Sie das Feature „Speicherplätze“ verwenden, um Datenträger in einem Stripeset zu verbinden. Sie müssen für jeden Datenträger in einem Pool eine Spalte konfigurieren. Andernfalls kann die Gesamtleistung des Stripesetvolume aufgrund einer ungleichen Verteilung des Datenverkehrs auf die Datenträger niedriger sein als erwartet.

Wichtig: Im Server-Manager können Sie die Gesamtanzahl der Spalten auf bis zu 8 für ein Stripesetvolume festlegen. Bei Anfügen von mehr als acht Datenträgern nutzen Sie PowerShell, um das Volume zu erstellen. Mithilfe von PowerShell können Sie die Anzahl der Spalten entsprechend der Anzahl der Datenträger festlegen. Wenn beispielsweise ein einzelnes Stripeset 16 Datenträger enthält, geben Sie im PowerShell-Cmdlet *New-VirtualDisk* den Wert „16“ für den *NumberOfColumns*-Parameter an.

Unter Linux können Sie hierfür das Hilfsprogramm MDADM verwenden. Detaillierte Anweisungen für das Erstellen von Stripingdatenträgern unter Linux finden Sie unter [Konfigurieren von Software-RAID unter Linux](/previous-versions/azure/virtual-machines/linux/configure-raid).

*Stripegröße*  
Eine wichtige Konfigurationseinstellung beim Datenträgerstriping ist die Stripegröße. Die Stripe- bzw. Blockgröße ist die kleinste Datenmenge, die eine Anwendung auf einem Stripesetvolume adressieren kann. Die Stripegröße, die Sie konfigurieren, hängt von der Art der Anwendung und ihrem Anforderungsmuster ab. Bei Wahl der falschen Stripegröße ist eine falsche E/A-Abstimmung möglich, durch die sich die Leistung Ihrer Anwendung verschlechtert.

Wenn beispielsweise eine von Ihrer Anwendung generierte E/A-Anweisung größer als die Stripegröße des Datenträgers ist, wird sie vom Speichersystem über Stripe-Einheitsgrenzen hinweg auf mehrere Datenträger geschrieben. Wenn auf diese Daten zugegriffen werden soll, muss zum Erfüllen der Anforderung mehr als eine Stripe-Einheit durchsucht werden. Die kumulative Wirkung eines solchen Verhaltens kann zu beträchtlichen Leistungseinbußen führen. Wenn hingegen die Größe der E/A-Anforderung kleiner als die Stripegröße ist und diese zufälliger Art ist, können sich die E/A-Anforderungen auf demselben Datenträger anhäufen, was zu einem Engpass und schließlich zu einer Verschlechterung der E/A-Leistung führt.

Wählen Sie abhängig vom Workload Ihrer Anwendung eine geeignete Stripegröße aus. Wählen Sie für zufällige kleine E/A-Anforderungen eine kleinere Stripegröße. Wählen Sie hingegen für große sequenzielle E/A-Anforderungen eine größere Stripegröße. Ermitteln Sie Empfehlungen für die Stripegröße für die Anwendung, die Sie in Storage Premium ausführen. Für SQL Server konfigurieren Sie die Stripegröße mit 64 KB für OLTP-Workloads und 256 KB für Data Warehouse-Workloads. Weitere Informationen finden Sie unter [Optimale Verfahren für die Leistung für SQL Server in Azure Virtual Machines](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md#disks-guidance) .

> [!NOTE]
> Bei einer VM der DS-Serie können Sie ein Stripeset mit maximal 32 Storage Premium-Datenträgern und bei einer VM der GS-Serie mit maximal 64 Storage Premium-Datenträgern bilden.

## <a name="multi-threading"></a>Multithreading

Azure hat die Storage Premium-Plattform mit einem hohen Grad an Parallelität ausgelegt. Aus diesem Grund wird mit einer Multithreadanwendung eine wesentlich höhere Leistung als mit einer Singlethreadanwendung erreicht. Eine Multithreadanwendung teilt ihre Aufgaben auf mehrere Threads auf und steigert die Effizienz ihrer Ausführung durch den maximalen Einsatz der VM- und Datenträgerressourcen.

Wenn Ihre Anwendung in einer VM mit einem Kern mithilfe zweier Threads ausgeführt wird, kann die CPU zwischen den beiden Threads umschalten, um für Effizienz zu sorgen. Während ein Thread auf den Abschluss eines Datenträger-E/A-Vorgangs wartet, kann die CPU zum anderen Thread umschalten. Auf diese Weise können zwei Threads mehr erreichen als einem einzelnem Thread möglich ist. Wenn die VM mehr als einen Kern hat, verkürzt sich die Ausführungszeit weiter, da jeder Kern Aufgaben parallel ausführt.

Sie sind ggf. nicht in der Lage, die Weise zu ändern, in der eine Standardanwendung das Singlethreading oder Multithreading implementiert. SQL Server unterstützt beispielsweise das Arbeiten mit mehreren CPUs und Kernen. SQL Server entscheidet jedoch selbst, unter welchen Umständen ein oder mehrere Threads zum Verarbeiten einer Abfrage genutzt werden. SQL Server kann mithilfe von Multithreading Abfragen ausführen und Indizes erstellen. Für eine Abfrage, für die große Tabellen verknüpft und Daten sortiert werden, ehe die Rückgabe an den Benutzer erfolgt, verwendet SQL Server wahrscheinlich mehrere Threads. Ein Benutzer kann nicht jedoch steuern, ob SQL Server eine Abfrage mithilfe eines einzelnen oder mehrerer Threads ausführt.

Es gibt Konfigurationseinstellungen, die Sie ändern können, um dieses Multithreading bzw. die Parallelverarbeitung einer Anwendung zu beeinflussen. Bei SQL Server ist dies die Einstellung „Max. Grad an Parallelität“. Diese auch MAXDOP genannte Einstellung ermöglicht Ihnen das Konfigurieren der maximalen Anzahl von Prozessoren, die SQL Server bei der Parallelverarbeitung verwenden kann. Sie können MAXDOP für einzelne Abfragen oder Indexvorgänge konfigurieren. Dies ist nützlich, wenn Sie bei einer leistungsabhängigen Anwendung die Ressourcen Ihres Systems gleichmäßig auslasten möchten.

Angenommen, Ihre mit SQL Server arbeitende Anwendung führt gleichzeitig eine umfangreiche Abfrage und einen Indexvorgang aus. Wir nehmen einmal an, dass der Indexvorgang Priorität vor der umfangreichen Abfrage haben soll. In einem solchen Fall können Sie den MAXDOP-Wert des Indexvorgangs höher als den MAXDOP-Wert der Abfrage festlegen. Auf diese Weise verfügt SQL Server über eine größere Anzahl von Prozessoren, die für den Indexvorgang genutzt werden kann, im Vergleich zur Anzahl der Prozessoren, die ausschließlich für die umfangreiche Abfrage verwendet werden können. Beachten Sie, dass Sie nicht die Anzahl der Threads steuern können, die SQL Server für jeden Vorgang verwendet. Sie können die maximale Anzahl der Prozessoren steuern, die für das Multithreading reserviert werden.

Lesen Sie die weiteren Informationen zu [Graden an Parallelität](/previous-versions/sql/sql-server-2008-r2/ms188611(v=sql.105)) in SQL Server. Hier erfahren Sie mehr zu Einstellungen, mit denen das Multithreading in Ihrer Anwendung und deren Konfiguration zum Optimieren der Leistung beeinflusst werden können.

## <a name="queue-depth"></a>Warteschlangenlänge

Die Warteschlangenlänge ist die Anzahl ausstehender E/A-Anforderungen im System. Der Wert der Warteschlangenlänge bestimmt, wie viele E/A-Vorgänge Ihre Anwendung in die Schlange stellen kann, die von den Speicherdatenträgern verarbeitet werden sollen. Dieser Wert betrifft alle drei Anwendungsleistungsindikatoren, die wir in diesem Artikel behandelt haben: IOPS, Durchsatz und Latenz.

Warteschlangenlänge und Multithreading stehen in enger Beziehung. Der Wert der Warteschlangenlänge gibt an, wie viel Multithreading von der Anwendung erreicht werden kann. Wenn die Warteschlangenlänge hoch ist, kann die Anwendung mehr Vorgänge gleichzeitig ausführen, also mehr Multithreading unterstützen. Wenn die Warteschlangenlänge niedrig ist, obwohl die Anwendung mit mehreren Threads arbeitet, stehen nicht genügend Anforderungen für die gleichzeitige Ausführung in der Schlange.

Standardanwendungen lassen meist nicht das Ändern der Warteschlangenlänge so, denn bei einer falschen Festlegung ist der Schaden größer als der Nutzen. Anwendungen legen in der Regel den richtigen Wert für die Warteschlangenlänge fest, um sich eine optimale Leistung zu sichern. Allerdings ist es wichtig, dieses Konzept zu verstehen, damit Sie Leistungsprobleme bei Ihrer Anwendung beheben können. Sie können die Auswirkungen der Warteschlangenlänge beobachten, indem Sie in Ihrem System Benchmarktools ausführen.

Einige Programme bieten Einstellungen zum Beeinflussen der Warteschlangenlänge. Die SQL Server-Einstellung „Max. Grad an Parallelität“ (MAXDOP) wurde beispielsweise im vorherigen Abschnitt vorgestellt. MAXDOP ist eine Möglichkeit, Warteschlangenlänge und Multithreading zu beeinflussen, obwohl dadurch der SQL Server-Wert der Warteschlangenlänge nicht direkt geändert wird.

*Hohe Warteschlangenlänge*  
Bei einer hohen Warteschlangenlänge werden mehr Vorgänge auf dem Datenträger in die Schlange gestellt. Der Datenträger kennt die nächste Anforderungen in seiner Warteschlange vorzeitig. Daher kann der Datenträger Vorgänge im Voraus planen und sie in einer optimalen Sequenz verarbeiten. Da die Anwendung mehr Anforderungen zum Datenträger überträgt, kann der Datenträger mehr parallele E/A-Vorgänge verarbeiten. Schlussendlich erzielt die Anwendung dadurch mehr IOPS. Da die Anwendung mehr Anforderungen verarbeitet, erhöht sich auch der Gesamtdurchsatz der Anwendung.

In der Regel erzielt eine Anwendung einen maximalen Durchsatz bei 8-16 und mehr ausstehenden E/A-Vorgängen pro angefügtem Datenträger. Wenn die Warteschlangenlänge den Wert 1 hat, überträgt die Anwendung nicht genügend E/A-Vorgänge an das System, sodass in einem bestimmten Zeitraum weniger davon verarbeitet werden. Das bedeutet weniger Durchsatz.

Beispiel: Wenn in SQL Server der MAXDOP-Wert für eine Abfrage auf 4 festgelegt wird, erkennt SQL Server, dass bis zu vier Kerne verwendet werden können, um die Abfrage auszuführen. SQL Server bestimmt den besten Wert für die Warteschlangenlänge und die Anzahl der Kerne für die Abfrageausführung.

*Optimale Warteschlangenlänge*  
Eine sehr hohe Warteschlangenlänge hat jedoch auch Nachteile. Wenn der Wert zu hoch ist, versucht die Anwendung, eine sehr hohe IOPS-Rate zu erzielen. Außer wenn die Anwendung über beständige Datenträger mit ausreichend bereitgestellten IOPS verfügt, kann sich dies negativ auf die Anwendungslatenz auswirken. Die folgende Formel zeigt die Beziehung zwischen IOPS, Latenz und Warteschlangenlänge.  
    ![Eine Abbildung der Gleichung IOPS × Wartezeit = Warteschlangenlänge](media/premium-storage-performance/image6.png)

Die Warteschlangenlänge darf nicht auf einen beliebig hohen Wert festgelegt werden. Wichtig ist ein optimaler Wert, der der Anwendung genügend IOPS bieten kann, ohne für mehr Latenz zu sorgen. Wenn z. B. die Anwendungslatenz 1 Millisekunde sein muss, ist die erforderliche Warteschlangenlänge zum Erzielen von 5.000 IOPS wie folgt: WL = 5000 x 0,001 = 5.

*Warteschlangenlänge für Stripesetvolume*  
Für ein Stripesetvolume muss die Warteschlangenlänge so hoch sein, dass jeder Datenträger über eine individuelle Spitzenwarteschlangenlänge verfügt. Nehmen wir als Beispiel eine Anwendung mit dem Wert 2 für die Warteschlangenlänge und mit vier Datenträgern im Stripeset. Die beiden E/A-Anforderungen werden an zwei Datenträger gerichtet, während die restlichen inaktiv bleiben. Konfigurieren Sie deshalb die Warteschlangenlänge so, dass alle Datenträger ausgelastet werden. Folgende Formel veranschaulicht, wie Sie die Warteschlangenlänge von Stripesetvolumes bestimmen.  
    ![Eine Abbildung der Gleichung Warteschlangenlänge pro Datenträger × Anzahl der Spalten pro Volume = Warteschlangenlänge des Stripesetvolume](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Drosselung

Azure Storage Premium stellt abhängig von den gewählten VM- und Datenträgergrößen die angegebene IOPS- und Durchsatzrate bereit. Immer wenn die Anwendung versucht, eine IOPS- oder Durchsatzrate über diese Limits der VM oder des Datenträgers hinaus zu nutzen, erfolgt eine Drosselung durch Storage Premium. Dies zeigt sich in Form von Leistungseinbußen in Ihrer Anwendung. Folgen sind höhere Latenz, weniger Durchsatz oder weniger IOPS. Wenn Storage Premium keine Drosselung vornimmt, kann Ihre Anwendung vollständig ausfallen, sobald die zur Verfügung stehenden Ressourcen überschritten werden. Um also Leistungsprobleme aufgrund der Drosselung zu vermeiden, stellen Sie Ihrer Anwendung stets ausreichend Ressourcen zur Verfügung. Berücksichtigen Sie, was zuvor in den Abschnitt „VM-Größen“ und „Datenträgergrößen“ erörtert wurde. Benchmarktests sind die beste Möglichkeit, um herauszufinden, welche Ressourcen Sie zum Hosten der Anwendung benötigen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie für Ihren Datenträger einen Benchmarktest durchführen möchten, finden Sie weitere Informationen in diesem Artikel:

* Linux: [Benchmarktests für Ihre Anwendung in Azure Disk Storage](linux/disks-benchmarks.md)
* Windows: [Benchmarktests für einen Datenträger](windows/disks-benchmarks.md)

Weitere Informationen zu den verfügbaren Datenträgertypen finden Sie unter

* Linux: [Auswählen eines Datenträgertyps](disks-types.md).
* Windows: [Auswählen eines Datenträgertyps](disks-types.md).

Für SQL Server-Benutzer bietet sich das Lesen von Artikeln zu den bewährten Methoden für die Leistung von SQL Server an:

* [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Azure Storage Premium bietet höchste Leistung für SQL Server in Azure-VM](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)