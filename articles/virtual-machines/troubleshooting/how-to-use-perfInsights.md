---
title: Verwenden von PerfInsights in Microsoft Azure | Microsoft-Dokumentation
description: Lernen Sie, wie Sie Leistungsprobleme bei virtuellen Windows-Computern mit PerfInsights behandeln.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f49ae5139dc92ec1448e5dea05be8c8c216ef91e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002662"
---
# <a name="how-to-use-perfinsights-in-azure"></a>Verwenden von PerfInsights in Azure

[PerfInsights](https://aka.ms/perfinsightsdownload) ist ein Diagnosetool zur Selbsthilfe, das Diagnosedaten sammelt und analysiert. Außerdem erstellt das Tool einen Bericht, der beim Beheben von Leistungsproblemen mit virtuellen Windows-Computern in Azure helfen kann. PerfInsights kann auf virtuellen Computern als eigenständiges Tool oder direkt über das Portal ausgeführt werden. Verwenden Sie die [Leistungsdiagnose für Azure-VMs](performance-diagnostics.md), oder installieren Sie die [Azure-VM-Erweiterung für die Leistungsdiagnose](performance-diagnostics-vm-extension.md).

Wenn bei virtuellen Computern Leistungsprobleme auftreten, führen Sie dieses Tool aus, bevor Sie sich an den Support wenden.

## <a name="supported-troubleshooting-scenarios"></a>Unterstützte Problembehandlungsszenarien

PerfInsights kann verschiedene Arten von Informationen erfassen und analysieren. Allgemeine Szenarien werden in den folgenden Abschnitten beschrieben.

### <a name="quick-performance-analysis"></a>Schnelle Leistungsanalyse

Dieses Szenario erfasst die Datenträgerkonfiguration und andere wichtige Informationen, u.a.:

-   Ereignisprotokolle

-   Netzwerkstatus für alle ein- und ausgehenden Verbindungen

-   Netzwerk- und Firewallkonfigurationseinstellungen

-   Aufgabenliste für alle derzeit auf dem System ausgeführten Anwendungen

-   Microsoft SQL Server-Datenbank-Konfigurationseinstellungen (wenn der virtuelle Computer als Server identifiziert wird, auf dem SQL Server ausgeführt wird)

-   Speicherzuverlässigkeitszähler

-   Wichtige Windows-Hotfixes

-   Installierte Filtertreiber

Dies ist eine passive Sammlung von Informationen, die das System nicht beeinträchtigen sollte. 

>[!Note]
>Dieses Szenario ist automatisch in jedem der folgenden Szenarien enthalten:

### <a name="benchmarking"></a>Benchmarktests

Dieses Szenario führt den [DiskSpd](https://github.com/Microsoft/diskspd)-Vergleichstest (IOPS und MBit/s) für alle Laufwerke aus, die an den virtuellen Computer angefügt sind. 

> [!Note]
> Dieses Szenario kann sich auf das System auswirken und sollte nicht auf einem Liveproduktionssystem ausgeführt werden. Falls erforderlich, führen Sie dieses Szenario in einem dedizierten Wartungsfenster aus, um Probleme zu vermeiden. Eine erhöhte Workload, die durch eine Ablaufverfolgung oder einen Vergleichstest verursacht wird, kann die Leistung Ihres virtuellen Computers beeinträchtigen.
>

### <a name="performance-analysis"></a>Leistungsanalyse

Dieses Szenario führt eine [Leistungsindikator](/windows/win32/perfctrs/performance-counters-portal)-Ablaufverfolgung mithilfe der Indikatoren aus, die in der Datei „RuleEngineConfig.json“ angegeben werden. Wenn der virtuelle Computer als Server identifiziert wird, auf dem SQL Server ausgeführt wird, wird eine Leistungsindikator-Ablaufverfolgung durchgeführt. Hierfür werden die Leistungsindikatoren verwendet, die in der Datei „RuleEngineConfig.json“ enthalten sind. Dieses Szenario enthält auch Leistungsdiagnosedaten.

### <a name="azure-files-analysis"></a>Azure Files-Analyse

Dieses Szenario führt eine besondere Leistungsindikatorerfassung zusammen mit einer Ablaufverfolgung im Netzwerk durch. Die Erfassung enthält alle Indikatoren vom Typ „SMB-Client-Freigaben“ (Server Message Block). Es folgen einige der wichtigsten SMB-Client-Freigaben-Leistungsindikatoren, die Teil der Erfassung sind:

| **Typ**     | **SMB-Client-Freigaben-Indikator** |
|--------------|-------------------------------|
| IOPS         | Datenanforderungen/s             |
|              | Leseanforderungen/s             |
|              | Schreibanforderungen/s            |
| Latency      | Durchschn. Sek./Datenanforderung         |
|              | Durchschn. Sek./Lesevorgang                 |
|              | Durchschn. Sek./Schreibvorgang                |
| E/A-Größe      | Durchschn. Bytes/Datenanforderung       |
|              | Durchschn. Bytes/Lesen               |
|              | Durchschn. Bytes/Schreiben              |
| Throughput   | Datenbytes/s                |
|              | Lesebytes/s                |
|              | Schreibbytes/s               |
| Warteschlangenlänge | Durchschn. Lesewarteschlangen-Länge        |
|              | Durchschn. Schreibwarteschlangen-Länge       |
|              | Durchschn. Datenwarteschlangen-Länge        |

### <a name="advanced-performance-analysis"></a>Erweiterte Leistungsanalyse

Bei der Ausführung einer erweiterten Leistungsanalyse wählen Sie Ablaufverfolgungen aus, die parallel ausgeführt werden sollen. Sie können bei Bedarf alle Ablaufverfolgungen (Leistungsindikator, Xperf, Netzwerk und StorPort) ausführen.  

> [!Note]
> Dieses Szenario kann sich auf das System auswirken und sollte nicht auf einem Liveproduktionssystem ausgeführt werden. Falls erforderlich, führen Sie dieses Szenario in einem dedizierten Wartungsfenster aus, um Probleme zu vermeiden. Eine erhöhte Workload, die durch eine Ablaufverfolgung oder einen Vergleichstest verursacht wird, kann die Leistung Ihres virtuellen Computers beeinträchtigen.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Welche Informationen werden von PerfInsights gesammelt?

Informationen zu virtuellen Windows-Computern, Datenträger- oder Speicherpoolkonfiguration, Leistungsindikatoren, Protokollen und verschiedenen Ablaufverfolgungen werden erfasst. Dies hängt vom verwendeten Leistungsszenario ab. Details finden Sie in der folgenden Tabelle:

| Gesammelte Daten | Schnelle Leistungsanalyse | Benchmarktests | Leistungsanalyse | Azure Files-Analyse | Erweiterte Leistungsanalyse |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|
| Informationen aus Ereignisprotokollen       | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Systeminformationen                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Volumezuordnung                        | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Datenträgerzuordnung                          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Ausgeführte Aufgaben                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Speicherzuverlässigkeitszähler      | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Speicherinformationen               | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| FSUTIL-Ausgabe                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Filtertreiberinformationen                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Netstat-Ausgabe                    | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Netzwerkkonfiguration             | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Firewallkonfiguration            | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| SQL Server-Konfiguration          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Leistungsdiagnose-Ablaufverfolgungen *  | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Leistungsindikator-Ablaufverfolgung **      |                            |                                    | Ja                      |                      | Ja                  |
| SMB-Indikatorablaufverfolgung **              |                            |                                    |                          | Ja                  |                      |
| SQL Server-Indikatorablaufverfolgung **       |                            |                                    | Ja                      |                      | Ja                  |
| XPerf-Ablaufverfolgung                       |                            |                                    |                          |                      | Ja                  |
| StorPort-Ablaufverfolgung                    |                            |                                    |                          |                      | Ja                  |
| Netzwerkablaufverfolgung                     |                            |                                    |                          | Ja                  | Ja                  |
| DiskSpd-Vergleichstest-Ablaufverfolgung **_       |                            | Ja                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-_"></a>Leistungsdiagnose-Ablaufverfolgung (_)

Führt im Hintergrund eine regelbasiertes Engine aus, um Daten zu sammeln und akute Leistungsprobleme zu diagnostizieren. Die folgenden Regeln werden derzeit unterstützt:

- HighCpuUsage-Regel: Erkennt Zeiträume mit hoher CPU-Auslastung und zeigt die stärksten CPU-Ressourcenverbraucher während dieser Zeiträume an.
- HighDiskUsage-Regel: Erkennt Zeiträume mit hoher Datenträgerauslastung auf physischen Datenträgern und zeigt die stärksten Datenträger-Ressourcenverbraucher während dieser Zeiträume an.
- HighResolutionDiskMetric-Regel: Zeigt IOPS, Durchsatz und E/A-Latenzmetriken pro 50 Millisekunden für jeden physischen Datenträger an. Mit der Lösung lassen sich schnell Datenträgerdrosselungs-Perioden identifizieren.
- HighMemoryUsage-Regel: Erkennt Perioden mit hoher Arbeitsspeicherauslastung und zeigt die stärksten Arbeitsspeicher-Ressourcenverbraucher während dieser Periode an.

> [!NOTE] 
> Derzeit werden Windows-Versionen unterstützt, die .NET Framework 4.5 oder höher beinhalten.

### <a name="performance-counter-trace-"></a>Leistungsindikator - Ablaufverfolgung (\*\*)

Die Methode erfasst die folgenden Leistungsindikatoren:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk und \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures und \Server\Pool Paged Failures
- Ausgewählte Indikatoren unter \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments,  \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Processor Network Interface Card Activity und \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Für SQL Server-Instanzen
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats und \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Für Azure Files
\SMB Client Shares

### <a name="diskspd-benchmark-trace-_"></a>DiskSpd-Vergleichstest-Ablaufverfolgung (**_)
DiskSpd-E/A-Workloadtests (Betriebssystemdatenträger [Schreibzugriff] und Poollaufwerke [Lese-/Schreibzugriff])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Ausführen des Tools PerfInsights auf Ihrem virtuellen Computer

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Was muss ich vor dem Ausführen des Tools wissen? 

#### <a name="tool-requirements"></a>Toolanforderungen

-  Dieses Tool muss auf dem virtuellen Computer ausgeführt werden, auf dem das Leistungsproblem besteht. 

-  Die folgenden Betriebssysteme werden unterstützt: _ Windows Server 2019
   * Windows Server 2016
   * Windows Server 2012 R2
   * Windows Server 2012
   * Windows Server 2008 R2
   * Windows 10
   * Windows 8.1
   * Windows 8

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Mögliche Probleme bei Ausführung des Tools auf Produktions-VMs

-  Im Benchmarktestszenario oder im Szenario „Erweiterte Leistungsanalyse“, das für die Verwendung von XPerf oder DiskSpd konfiguriert ist, kann das Tool die Leistung des virtuellen Computers beeinträchtigen. Diese Szenarien sollten nicht in einer Liveproduktionsumgebung ausgeführt werden.

-  Vergewissern Sie sich bei Verwendung des Benchmarktestszenarios oder des Szenarios „Erweiterte Leistungsanalyse“, das für die Verwendung von DiskSpd konfiguriert ist, dass die E/A-Workload nicht durch eine andere Hintergrundaktivität beeinträchtigt wird.

-  Standardmäßig verwendet das Tool das temporäre Speicherlaufwerk zum Sammeln von Daten. Wenn die Ablaufverfolgung für einen längeren Zeitraum aktiviert bleibt, könnte die Menge der gesammelten Daten relevant sein. Dies kann den verfügbaren Speicher auf dem temporären Datenträger reduzieren und sich damit auf jede Anwendung auswirken, die von diesem Laufwerk abhängig ist.

### <a name="how-do-i-run-perfinsights"></a>Wie führe ich PerfInsights aus? 

Sie können PerfInsights auf einem virtuellen Computer ausführen, indem Sie die [Azure-VM-Erweiterung für die Leistungsdiagnose](performance-diagnostics-vm-extension.md) installieren. Sie können PerfInsights auch als eigenständiges Tool ausführen. 

**Installieren und Ausführen von PerfInsights über das Azure-Portal**

Weitere Informationen zu dieser Option finden Sie unter [Azure-VM-Erweiterung für die Leistungsdiagnose unter Windows](performance-diagnostics-vm-extension.md#install-the-extension).  

**Ausführen von PerfInsights im eigenständigen Modus**

Gehen Sie wie folgt vor, um das Tool PerfInsights auszuführen:


1. Laden Sie [PerfInsights.zip](https://aka.ms/perfinsightsdownload) herunter.

2. Entsperren Sie die Datei „PerfInsights.zip“. Klicken Sie zu diesem Zweck mit der rechten Maustaste auf die Datei „PerfInsights.zip“, und wählen Sie **Eigenschaften**. Wählen Sie auf der Registerkarte **Allgemein** die Option **Entsperren**, und klicken Sie auf **OK**. So stellen Sie sicher, dass das Tool ohne zusätzliche Sicherheitsabfragen ausgeführt wird.  

    ![Screenshot der PerfInsights-Eigenschaften, auf dem die Option „Entsperren“ hervorgehoben ist](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Extrahieren Sie die komprimierte Datei „PerfInsights.zip“ auf das temporäre Laufwerk (standardmäßig in der Regel Laufwerk „D“). 

4.  Öffnen Sie eine Windows-Eingabeaufforderung als Administrator, und führen Sie dann „PerfInsights.exe“ aus, um die verfügbaren Befehlszeilenparameter anzuzeigen.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Screenshot: Ausgabe der PerfInsights-Befehlszeile](media/how-to-use-perfInsights/pi-commandline.png)
    
    Die grundlegende Syntax für die Ausführung von PerfInsights-Szenarien lautet:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Sie können das unten angegebene Beispiel verwenden, um das Leistungsanalyseszenario fünf Minuten lang auszuführen:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Sie können das folgende Beispiel verwenden, um das erweiterte Szenario mit Xperf und Leistungsindikator-Ablaufverfolgungen fünf Minuten lang auszuführen:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Mit dem folgenden Beispiel können Sie das Leistungsanalyseszenario fünf Minuten lang ausführen und die resultierende ZIP-Datei in das Speicherkonto hochladen:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Sie können alle verfügbaren Szenarien und Optionen mit dem Befehl **/list** nachschlagen:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Vor dem Ausführen eines Szenarios wird der Benutzer von PerfInsights aufgefordert, dem Freigeben von Diagnoseinformationen und den Lizenzbedingungen zuzustimmen. Verwenden Sie die Option **/AcceptDisclaimerAndShareDiagnostics**, um diese Aufforderungen zu überspringen. 
    >
    >Wenn Sie über ein aktives Supportticket bei Microsoft verfügen und PerfInsights gemäß der Aufforderung durch den Supporttechniker ausführen, mit dem Sie zusammenarbeiten, sollten Sie die Nummer des Supporttickets mit der Option **/sr** angeben.
    >
    >Standardmäßig versucht PerfInsights, selbst ein Update auf die neueste verfügbare Version durchzuführen. Verwenden Sie den Parameter **/SkipAutoUpdate** oder **/sau**, um die automatische Aktualisierung zu überspringen.  
    >
    >Wenn der Switch **/d** (Dauer) nicht angegeben ist, werden Sie von PerfInsights zum Reproduzieren des Problems aufgefordert, während die Szenarien vmslow, azurefiles und das erweiterte Szenario ausgeführt werden. 

Wenn die Ablaufverfolgungen oder Vorgänge abgeschlossen sind, wird eine neue Datei im selben Ordner wie PerfInsights angezeigt. Der Name der Datei lautet **PerformanceDiagnostics\_jjjj-MM-tt\_hh-mm-ss-fff.zip**. Sie können diese Datei zur Analyse an den Support-Agent senden oder den Bericht in der ZIP-Datei öffnen, um Ergebnisse und Empfehlungen anzuzeigen.

## <a name="review-the-diagnostics-report"></a>Überprüfen des Diagnoseberichts

In der Datei **PerformanceDiagnostics\_jjjj-MM-tt\_hh-mm-ss-fff.zip** finden Sie einen HTML-Bericht, der die Ergebnisse von PerfInsights detailliert beschreibt. Um den Bericht anzuzeigen, erweitern Sie die Datei **PerformanceDiagnostics\_jjjj-MM-tt\_hh-mm-ss-fff.zip**, und öffnen Sie dann die Datei **PerfInsights Report.html**.

Wählen Sie die Registerkarte **Ergebnisse**.

![Screenshot: Registerkarte „Übersicht“ des PerfInsights-Berichts](media/how-to-use-perfInsights/pi-finding-tab.png)
![Screenshot: Registerkarte „Speicher“ des PerfInsights-Berichts](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> Bei Ergebnissen, die als „Hoch“ eingestuft werden, handelt es sich um bekannte Probleme, die zu Leistungsproblemen führen können. Ergebnisse vom Typ „Mittel“ sind suboptimale Konfigurationen, die nicht unbedingt Leistungsprobleme verursachen. Ergebnisse, die als „Niedrig“ eingestuft werden, dienen lediglich zur Information.

Sehen Sie sich die Empfehlungen und Links zu allen hohen und mittleren Ergebnissen an. Dort erhalten Sie Informationen zu ihren möglichen Auswirkungen auf die Leistung sowie zu bewährten Vorgehensweisen für leistungsoptimierte Konfigurationen.

### <a name="storage-tab"></a>Registerkarte „Speicher“

Im Abschnitt **Ergebnisse** werden verschiedene Ergebnisse und Empfehlungen zum Speicher angezeigt.

Die Abschnitte **Datenträgerzuordnung** und **Volumezuordnung** beschreiben, wie logische Volumes und physische Datenträger miteinander verknüpft werden.

In der Perspektive für physische Datenträger (Datenträgerzuordnung) zeigt die Tabelle alle logischen Volumes, die auf dem Datenträger ausgeführt werden. Im folgenden Beispiel führt **PhysicalDrive2** zwei logische Volumes aus, die auf mehreren Partitionen („J“ und „H“) erstellt werden:

![Screenshot der Datenträger-Registerkarte](media/how-to-use-perfInsights/pi-disk-tab.png)

In der Volume-Perspektive (Volumezuordnung) zeigen die Tabellen alle physischen Datenträger unter den einzelnen logischen Volumes an. Beachten Sie, dass Sie für RAID-Datenträger und dynamische Datenträger ein logisches Volume auf mehreren physischen Datenträgern ausführen können. Im folgenden Beispiel ist *C:\\mount* ein Bereitstellungspunkt, der als *SpannedDisk* auf den physischen Datenträgern 2 und 3 konfiguriert ist:

![Screenshot der Registerkarte „Volume“](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>Registerkarte „SQL“

Wenn der virtuelle Zielcomputer SQL Server-Instanzen hostet, sehen Sie im Bericht eine zusätzliche Registerkarte mit dem Namen **SQL**:

![Screenshot der Registerkarte „SQL“](media/how-to-use-perfInsights/pi-sql-tab.png)

Dieser Abschnitt enthält die Registerkarte **Ergebnisse** und zusätzliche Registerkarten für jede SQL Server-Instanz, die auf dem virtuellen Computer gehostet wird.

Die Registerkarte **Ergebnisse** enthält eine Liste mit allen SQL-bezogenen Leistungsproblemen, die gefunden wurden, und die dazugehörigen Empfehlungen.

Im folgenden Beispiel wird **PhysicalDrive0** (auf Laufwerk „C“ ausgeführt) angezeigt. Das liegt daran, dass sich sowohl die Datei **modeldev** als auch die Datei **modellog** auf Laufwerk „C“ befindet und sie unterschiedlichen Typs sind (z.B. Datendatei und Transaktionsprotokoll).

![Screenshot der Protokollinformationen](media/how-to-use-perfInsights/pi-log-info.png)

Die Registerkarten für bestimmte SQL Server-Instanzen enthalten einen allgemeinen Abschnitt, in dem grundlegende Informationen zur ausgewählten Instanz angezeigt werden. Die Registerkarten enthalten außerdem zusätzliche Abschnitte für erweiterte Informationen, einschließlich Einstellungen, Konfigurationen und Benutzeroptionen.

### <a name="diagnostic-tab"></a>Registerkarte „Diagnose“
Die Registerkarte **Diagnose** enthält Informationen zu den entsprechenden Consumern von CPU, Datenträger und Arbeitsspeicher auf dem Computer mit den höchsten Werten für die Dauer der PerfInsights-Ausführung. Hier finden Sie auch weitere Informationen zu kritischen Patches, die im System ggf. fehlen, Aufgabenlisten und wichtigen Systemereignissen. 

## <a name="references-to-the-external-tools-used"></a>Verweise auf die verwendeten externen Tools

### <a name="diskspd"></a>DiskSpd

DiskSpd ist ein Speicherladegenerator- und Leistungstesttool von Microsoft. Weitere Informationen finden Sie unter [DiskSpd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

XPerf ist ein Befehlszeilentool zum Erfassen von Ablaufverfolgungen aus dem Windows Performance Toolkit. Weitere Informationen finden Sie unter [Windows Performance Toolkit – Xperf](/archive/blogs/ntdebugging/windows-performance-toolkit-xperf).

## <a name="next-steps"></a>Nächste Schritte

Sie können Diagnoseprotokolle und Berichte an den Microsoft-Support zur weiteren Prüfung hochladen. Sie werden vom Support unter Umständen aufgefordert, zur Unterstützung bei der Problembehandlung die Ausgabe zu senden, die von PerfInsights generiert wird.

Der folgende Screenshot zeigt eine Nachricht, die der an Sie gesendeten Nachricht ähnelt:

![Screenshot der Beispielnachricht vom Microsoft-Support](media/how-to-use-perfInsights/pi-support-email.png)

Befolgen Sie die Anweisungen in der Nachricht, um auf den Arbeitsbereich für die Dateiübertragung zuzugreifen. Zur Erhöhung der Sicherheit müssen Sie das Kennwort bei der ersten Verwendung ändern.

Nach der Anmeldung sehen Sie ein Dialogfeld zum Hochladen der von PerfInsights erfassten Datei **PerformanceDiagnostics\_jjjj-MM-tt\_hh-mm-ss-fff.zip**.
