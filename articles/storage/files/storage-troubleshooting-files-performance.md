---
title: Azure Files-Leistung – Handbuch zur Problembehandlung
description: Bekannte Leistungsprobleme mit Azure Premium-Dateifreigaben (Vorschauversion) und entsprechende Problemumgehungen.
services: storage
author: gunjanj
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 5ae0bb736a7cc0bbc38df5905abc5d8a71f60eb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190052"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Troubleshooting bei Azure Files-Leistungsproblemen

In diesem Artikel sind einige allgemeine Probleme aufgeführt, die es im Zusammenhang mit Premium-Azure-Dateifreigaben (Vorschauversion) geben kann. Er enthält mögliche Ursachen und Problemumgehungen, wenn diese Probleme auftreten.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Hohe Latenz, niedriger Durchsatz und allgemeine Leistungsprobleme

### <a name="cause-1-share-experiencing-throttling"></a>Ursache 1: Freigabe, in der Drosselung auftritt

Das standardmäßige Kontingent für eine Freigabe ist 100 GiB, wodurch 100 IOPS-Grundwerte bereitgestellt werden (mit der Möglichkeit, für eine Stunde auf bis zu 300 zu erweitern). Weitere Informationen zu einer Bereitstellung und deren Beziehung zu IOPS finden Sie im Planungshandbuch im Abschnitt [Bereitgestellte Freigaben](storage-files-planning.md#provisioned-shares).

Um zu prüfen, ob Ihre Dateifreigabe gedrosselt wird, können Sie Azure-Metriken im Portal nutzen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Alle Dienste** aus, und suchen Sie dann nach **Metriken**.

1. Klicken Sie auf **Metriken**.

1. Wählen Sie Ihr Speicherkonto als die Ressource aus.

1. Wählen Sie **Datei** als Metriknamespace aus.

1. Wählen Sie **Transaktionen** als Metrik aus.

1. Fügen Sie einen Filter für **Antworttyp** hinzu, und überprüfen Sie, ob es Antworten gibt, die den Antwortcode **SuccessWithThrottling** enthalten.

![Metriken-Optionen für Premium-Dateifreigaben](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Lösung

- Erhöhen Sie die für die Dateifreigabe bereitgestellte Kapazität, indem Sie ein höheres Kontingent für Ihre Freigabe angeben.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Ursache 2: Hohe Arbeitsauslastung bezüglich Metadaten/Namespace

Sind die meisten Ihrer Anforderungen metadatenorientiert (etwa Datei erstellen/Datei öffnen/Datei schließen/Infos abfragen/Verzeichnis abfragen), ist die Latenz gravierender im Vergleich zu Lese-/Schreibvorgängen.

Um zu prüfen, ob die meisten Ihrer Anforderungen metadatenorientiert sind, können Sie die gleichen Schritte wie zuvor ausführen. Mit dem einzigen Unterschied, dass Sie statt eines Filters für **Antworttyp** einen Filter für **API-Name** hinzufügen.

![Filter für API-Namen in Ihren Metriken](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Problemumgehung

- Überprüfen Sie, ob die Anwendung so geändert werden kann, dass sich die Anzahl von Metadatenvorgängen verringert.

### <a name="cause-3-single-threaded-application"></a>Ursache 3: Singlethread-Anwendung

Wenn die Anwendung, die vom Kunden verwendet wird, eine Singlethread-Anwendung ist, kann sich eine Situation ergeben, in der der IOPS/Durchsatzwert deutlich niedriger ist als derjenige, der entsprechend Ihrer bereitgestellten Freigabegröße maximal möglich wäre.

### <a name="solution"></a>Lösung

- Erhöhen Sie die Parallelität Ihrer Anwendung, indem Sie die Anzahl von Threads erhöhen.
- Wechseln Sie zu Anwendungen, in denen Parallelität möglich ist. Für Kopiervorgänge könnten Kunden z. B. „AzCopy“ oder „RoboCopy“ von Windows-Clients oder den **parallel**-Befehl auf Linux-Clients verwenden.

## <a name="very-high-latency-for-requests"></a>Sehr hohe Latenz für Anforderungen

### <a name="cause"></a>Ursache

Der virtuelle Client-Computer kann sich in einer anderen Region befinden als die Premium-Dateifreigabe.

### <a name="solution"></a>Lösung

- Führen Sie die Anwendung auf einem virtuellen Computer aus, der sich in derselben Region befindet wie die Premium-Dateifreigabe.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Client kann den maximalen Durchsatz des Netzwerks nicht erreichen

Eine mögliche Ursache ist, dass SMB-Multikanalübertragung nicht unterstützt wird. Derzeit unterstützen Azure-Dateifreigaben nur Einkanalübertragung, sodass es nur eine Verbindung vom virtuellen Clientcomputer mit dem Server gibt. Diese einzelne Verbindung ist an einen einzelnen Kern auf dem virtuellen Clientcomputer gebunden, weshalb der maximal erreichbare Durchsatz für den virtuellen Computer durch den einzelnen Kern beschränkt ist.

### <a name="workaround"></a>Problemumgehung

- Durch Bereitstellen eines virtuellen Computers mit einem größeren Kern lässt sich der Durchsatz möglicherweise erhöhen.
- Durch Ausführen der Clientanwendung auf mehreren virtuellen Computern wird der Durchsatz erhöht.
- Verwenden Sie nach Möglichkeit REST-APIs.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Durchsatz auf Linux-Clients ist deutlich geringer im Vergleich zu Windows-Clients

### <a name="cause"></a>Ursache

Dies ist ein bekanntes Problem mit der Implementierung des SMB-Clients unter Linux.

### <a name="workaround"></a>Problemumgehung

- Verteilen Sie die Last auf mehrere virtuelle Computer.
- Verwenden Sie auf einem virtuellen Computer mehrere Bereitstellungspunkte mit der **nosharesock**-Option, und verteilen Sie die Last auf diese Bereitstellungspunkte.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Hohe Latenzen bei metadatenorientierten hohen Arbeitslasten, die sich aus umfangreichen Öffnen-/Schließen-Vorgängen ergeben.

### <a name="cause"></a>Ursache

Es werden keine Verzeichnis-Leasedauern unterstützt.

### <a name="workaround"></a>Problemumgehung

- Vermeiden Sie nach Möglichkeit, dass Öffnen-/Schließen-Vorgänge im selben Verzeichnis innerhalb kurzer Zeit verarbeitet werden müssen.
- Erhöhen Sie für virtuelle Linux-Computer das Cachetimeout für Verzeichniseinträge, indem Sie **actimeo=<sec>** als eine Bereitstellungsoption angeben. Der Standardwert für die Option beträgt eine Sekunde, sodass ein höherer Wert, etwa drei oder fünf Sekunden, nützlich sein kann.
- Aktualisieren Sie bei virtuellen Linux-Computern den Kernel auf 4.20 oder höher.

## <a name="low-iops-on-centosrhel"></a>Niedriger IOPS unter CentOS/RHEL

### <a name="cause"></a>Ursache

Eine E/A-Tiefe von größer als eins wird unter CentOS/RHEL nicht unterstützt.

### <a name="workaround"></a>Problemumgehung

- Führen Sie ein Upgrade auf CentOS 8/RHEL 8 durch.
- Wechseln Sie zu Ubuntu.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Unregelmäßiges/Sägezahn-Muster für IOPS

### <a name="cause"></a>Ursache

Eine Client-Anwendung überschreitet durchgängig den IOPS-Grundwert. Derzeit gibt es keine dienstseitige Glättung der Anforderungslast, d. h., wenn der Client den IOPS-Grundwert überschreitet, wird er von dem Dienst gedrosselt. Diese Drosselung kann dazu führen, dass sich für den Client ein unregelmäßiges/sägezahnartiges IOPS-Muster ergibt. In diesem Fall kann der durchschnittliche IOPS, der vom Client erreicht wird, niedriger sein der IOPS-Grundwert.

### <a name="workaround"></a>Problemumgehung

- Verringern Sie die Anforderungslast von der Clientanwendung, damit sich keine Drosselung für die Freigabe ergibt.
- Erhöhen Sie das Kontingent für die Dateifreigabe, sodass die Freigabe nicht gedrosselt wird.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Übermäßig viele DirectoryOpen/DirectoryClose-Aufrufe

### <a name="cause"></a>Ursache

Wenn sich die Anzahl von DirectoryOpen/DirectoryClose Aufrufen unter den häufigsten API-Aufrufen befindet und Sie nicht davon ausgehen, dass der Client so viele Aufrufe ausführt, kann die Problemursache die Antivirensoftware sein, die auf dem virtuellen Azure-Client installiert ist.

### <a name="workaround"></a>Problemumgehung

- Eine Behebung dieses Problems ist im [Plattformupdate für Windows aus April](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform) verfügbar.

## <a name="file-creation-is-slower-than-expected"></a>Dateierstellung ist langsamer als erwartet

### <a name="cause"></a>Ursache

Workloads, die sich aus der Erstellung einer großen Anzahl von Dateien ergeben, sehen keinen wesentlichen Unterschied zwischen der Leistung von Premium-Dateifreigaben und derjenigen von Standarddateifreigaben.

### <a name="workaround"></a>Problemumgehung

- None (Keine):

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Niedrige Leistung von Windows 8.1 oder Server 2012 R2

### <a name="cause"></a>Ursache

Zugriffe auf Azure-Dateien haben für E/A-intensive Workloads eine höhere Latenz als erwartet.

### <a name="workaround"></a>Problemumgehung

- Installieren Sie den verfügbaren [Hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).