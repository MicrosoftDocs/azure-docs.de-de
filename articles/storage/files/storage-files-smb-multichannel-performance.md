---
title: 'SMB Multichannel-Leistung: Azure Files'
description: Informationen zur SMB Multichannel-Leistung.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: ee3d1335de1b2bb3096e88c4d04cd03daaa665f5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "96014099"
---
# <a name="smb-multichannel-performance"></a>SMB Multichannel-Leistung

Azure Files SMB Multichannel (Vorschau) ermöglicht einem SMB 3.x-Client das Einrichten mehrerer Netzwerkverbindungen mit den Premium-Dateifreigaben in einem FileStorage-Konto. Das SMB 3.0-Protokoll hat das SMB Multichannel-Feature in Windows Server 2012- und Windows 8-Clients eingeführt. Aus diesem Grund können alle Azure Files SMB 3.x-Clients, die SMB Multichannel unterstützen, das Feature für Ihre Azure Premium-Dateifreigaben nutzen. Es fallen keine zusätzlichen Kosten für die Aktivierung von SMB Multichannel für ein Speicherkonto an.

## <a name="benefits"></a>Vorteile

Azure Files SMB Multichannel ermöglicht es Clients, mehrere Netzwerkverbindungen zu verwenden, die eine bessere Leistung bereitstellen, während gleichzeitig die Betriebskosten gesenkt werden. Bessere Leistung wird durch Bandbreitenaggregation über mehrere NICs und die Verwendung von RSS-Unterstützung (Receive Side Scaling) für NICs erzielt, um die E/A-Last auf mehrere CPUs zu verteilen.

- **Erhöhter Durchsatz**: Mehrere Verbindungen ermöglichen das parallele Übertragen von Daten über mehrere Pfade. Davon profitieren Workloads erheblich, die größere Dateigrößen mit größeren E/A-Größen verwenden und hohen Durchsatz von einer einzelnen VM oder einer kleineren Gruppe von VMs erfordern. Zu diesen Workloads zählen Medien- und Unterhaltungsdatenflüsse für Inhaltserstellung oder Transcodierung, Genomik und Finanzdienst-Risikoanalyse.
- **Höhere IOPS**: Die NIC-RSS-Funktion ermöglicht eine effektive Lastenverteilung auf mehrere CPUs mit mehreren Verbindungen. Dies ermöglicht eine höhere IOPS-Skalierung und eine effektive Auslastung von VM-CPUs. Dies ist für Workloads mit kleinen E/A-Größen nützlich, z. B. für Datenbankanwendungen.
- **Netzwerkfehlertoleranz**:   Mehrere Verbindungen vermindern das Risiko von Unterbrechungen, da Clients nicht mehr von einer einzelnen Verbindung abhängig sind.
- **Automatische Konfiguration**:   Wenn SMB Multichannel für Clients und Speicherkonten aktiviert ist, ermöglicht dies die dynamische Ermittlung vorhandener Verbindungen und das Erstellen zusätzlicher Verbindungspfade bei Bedarf.
- **Kostenoptimierung:** Workloads können eine höhere Skalierung von einer einzelnen VM oder einer kleinen Gruppe von VMs erreichen, während eine Verbindung mit Premium-Freigaben hergestellt wird. Dies kann die Gesamtbetriebskosten verringern, indem die Anzahl der zum Ausführen und Verwalten einer Workload erforderlichen VMs reduziert wird.

Weitere Informationen zu SMB Multichannel finden Sie in der [Windows-Dokumentation](/azure-stack/hci/manage/manage-smb-multichannel).

Diese Funktion bietet größere Leistungsvorteile für Multithreadanwendungen, in der Regel aber nicht für Singlethreadanwendungen. Weitere Informationen finden Sie im Abschnitt [Leistungsvergleich](#performance-comparison).

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regionale Verfügbarkeit

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Konfiguration

SMB Multichannel funktioniert nur, wenn die Funktion sowohl auf der Clientseite (Ihr Client) als auch auf der Dienstseite (Ihr Azure-Speicherkonto) aktiviert ist.

SMB Multichannel ist auf Windows-Clients standardmäßig aktiviert. Sie können Ihre Konfiguration überprüfen, indem Sie den folgenden PowerShell-Befehl ausführen: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
In Ihrem Azure-Speicherkonto müssen Sie SMB Multichannel aktivieren. Weitere Informationen finden Sie unter [Aktivieren von SMB Multichannel (Vorschau)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>Deaktivieren von SMB Multichannel
In den meisten Szenarien (insbesondere bei Multithreadworkloads) sollte für Clients eine verbesserte Leistung mit SMB Multichannel auftreten. Für einige spezifische Szenarien (z. B. für Singlethreadworkloads oder zu Testzwecken) möchten jedoch möglicherweise SMB Multichannel deaktivieren. Weitere Informationen finden Sie unter [Leistungsvergleich](#performance-comparison).

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Überprüfen, ob SMB Multichannel ordnungsgemäß konfiguriert ist

1. Erstellen Sie eine Premium-Dateifreigabe, oder verwenden Sie eine vorhandene Dateifreigabe.
1. Stellen Sie sicher, dass Ihr Client SMB Multichannel unterstützt (für mindestens einen Netzwerkadapter ist empfangsseitige Skalierung aktiviert). Weitere Informationen finden Sie in der [Windows-Dokumentation](/azure-stack/hci/manage/manage-smb-multichannel).
1. Binden Sie eine Dateifreigabe in Ihren Client ein.
1. Generieren Sie mit Ihrer Anwendung eine Auslastung.
    Ein Kopiertool, z. B. robocopy /MT oder ein beliebiges Leistungstool wie Diskspd zum Lesen/Schreiben von Dateien kann eine Auslastung generieren.
1. Öffnen Sie PowerShell als Administrator, und verwenden Sie den folgenden Befehl: `Get-SmbMultichannelConnection |fl`
1. Suchen Sie nach den Eigenschaften **MaxChannels** und **CurrentChannels**.

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Screenshot: Ergebnisse von get-smbmultichannelconnection." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Leistungsvergleich

Es gibt zwei Kategorien von Lese-/Schreibworkloadmustern: Singlethread und Multithread. Bei den meisten Workloads werden mehrere Dateien verwendet. Es gibt jedoch möglicherweise bestimmte Anwendungsfälle, in denen die Workload eine einzelne Datei in einer Freigabe verwendet. In diesem Abschnitt werden verschiedene Anwendungsfälle und die Leistungsauswirkungen der einzelnen Anwendungsfälle behandelt. Im Allgemeinen sind die meisten Workloads Multithreadanwendungen und verteilen die Workload auf mehrere Dateien, sodass Sie mit SMB Multichannel bedeutende Leistungsverbesserungen bemerken sollten.

- **Multithread/mehreren Dateien**: Abhängig vom Workloadmuster sollte eine beträchtliche Leistungsverbesserung in der Lese- und Schreib-E/A über mehrere Kanäle hinweg auftreten. Die Leistungssteigerungen variieren in Bezug auf IOPS, Durchsatz und Latenz zwischen doppelter und vierfacher Leistung. Für diese Kategorie sollte SMB Multichannel aktiviert werden, um optimale Leistung zu erzielen.
- **Multithread/Einzeldatei**: Für die meisten Anwendungsfälle in dieser Kategorie profitieren Workloads von der Aktivierung von SMB Multichannel. Dies gilt insbesondere dann, wenn die Workload über eine durchschnittliche E/A-Größe > ~16K verfügt. Einige Beispielszenarien, die von SMB Multichannel profitieren, sind die Sicherung oder Wiederherstellung einer einzelnen großen Datei. Eine Ausnahme, bei der Sie SMB Multichannel ggf. deaktivieren möchten, besteht dann, wenn Ihre Workload klein mit hoher E/A ist. In diesem Fall kann es zu einem geringfügigen Leistungsverlust von ungefähr 10 % kommen. Abhängig vom Anwendungsfall sollten Sie die Last auf mehrere Dateien verteilen oder die Funktion deaktivieren. Weitere Informationen finden Sie im Abschnitt [Konfiguration](#configuration).
- **Singlethread/mehrere Dateien oder einzelne Datei**: Bei den meisten Singlethreadworkloads gibt es minimale Leistungsvorteile aufgrund mangelnder Parallelität. In der Regel erfolgt eine geringfügige Leistungsminderung von ungefähr 10 %, wenn SMB Multichannel aktiviert ist. In diesem Fall ist es mit einer Ausnahme ideal, SMB Multichannel zu deaktivieren. Wenn die Singlethreadworkload die Last auf mehrere Dateien verteilen kann und eine durchschnittlich höhere E/A-Größe (> ~16K) verwendet, sollten durch SMB Multichannel geringfügige Leistungsvorteile auftreten.

### <a name="performance-test-configuration"></a>Leistungstestkonfiguration

Für die Diagramme in diesem Artikel wurde die folgende Konfiguration verwendet: Eine einzelne VM des Typs Standard D32s v3 mit einer einzelnen RSS-fähigen NIC mit vier Kanälen. Die Auslastung wurde mithilfe von „diskspd.exe“, Multithread mit E/A-Tiefe 10 und zufälliger E/A mit verschiedenen E/A-Größen generiert.

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Maximale Anzahl NICs|Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="Screenshot: Ergebnisse von get-smbmultichannelconnection." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Mutlithread/mehrere Dateien mit SMB Multichannel

Die Auslastung wurde für 10 Dateien mit verschiedenen E/A-Größen generiert. Die Testergebnisse für horizontales Hochskalieren zeigten deutliche Verbesserungen bei den IOPS- und Durchsatztestergebnissen mit aktiviertem SMB Multichannel. Die Ergebnisse werden in den folgenden Diagrammen dargestellt:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Diagramm der Leistung" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Diagramm der Durchsatzleistung." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- Für eine einzelne NIC wurde für Lesevorgänge eine zwei- bis dreifache Leistungssteigerung beobachtet und für Schreibvorgänge eine zwei- bis vierfache Steigerung in Bezug auf IOPS und Durchsatz.
- SMB Multichannel ermöglichte es IOPS und Durchsatz, VM-Grenzwerte auch mit einer einzelnen NIC und dem Grenzwert von vier Kanälen zu erreichen.
- Da ausgehende Vorgänge (oder Lesevorgänge des Speichers) nicht gemessen werden, konnte der Lesedurchsatz den von der VM veröffentlichten Grenzwert von 16.000 MBit/s (2 GiB/s) überschreiten. Der Test hat >2,7 GiB/s erreicht. Eingehende Vorgänge (oder Schreibvorgänge in den Speicher) unterliegen weiterhin den VM-Grenzwerten.
- Das Verteilen der Last auf mehrere Dateien hat für beträchtliche Verbesserungen gesorgt.

Ein Beispielbefehl, der in diesem Test verwendet wurde, lautet wie folgt: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Multithread-/Einzeldateiworkloads mit SMB Multichannel

Die Auslastung wurde für eine einzelne 128 GiB-Datei generiert. Bei aktiviertem SMB Multichannel zeigte der Hochskalierungstest mit Multithread/Einzeldateien in den meisten Fällen Verbesserungen. Die Ergebnisse werden in den folgenden Diagrammen dargestellt:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="Diagramm der IOPS-Leistung." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Diagramm der Durchsatzleistung einer einzelnen Datei." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- Bei einer einzelnen NIC mit größerer durchschnittlicher E/A-Größe (> ~16K) gab es bedeutende Verbesserungen bei Lese- und Schreibvorgängen.
- Bei geringeren E/A-Größen wurde die Leistung bei Aktivierung von SMB Multichannel um ungefähr 10 % geringfügig beeinträchtigt. Dies kann behoben werden, indem Sie die Last auf mehrere Dateien verteilen oder die Funktion deaktivieren.
- Die Leistung wird weiterhin durch [Grenzwerte für eine einzelne Datei](storage-files-scale-targets.md#file-level-limits) beschränkt.

## <a name="optimizing-performance"></a>Optimieren der Leistung

Die folgenden Tipps können Ihnen helfen, die Leistung zu optimieren:

- Stellen Sie sicher, dass sich Ihr Speicherkonto und Ihr Client in derselben Azure-Region befinden, um Netzwerklatenz zu reduzieren.
- Verwenden Sie Multithreadanwendungen, und verteilen Sie die Last auf mehrere Dateien.
- Die Leistungsvorteile von SMB Multichannel erhöhen sich mit der Anzahl von Dateien, auf die die Last verteilt wird.
- Die Leistung einer Premium-Freigabe wird durch die bereitgestellte Freigabegröße (IOPS/Ausgang/Eingang) und Grenzwerte für eine einzelne Datei beschränkt. Einzelheiten dazu finden Sie unter [Grundlegendes zur Bereitstellung für Premium-Dateifreigaben](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- Die maximale Leistung eines einzelnen VM-Clients ist weiterhin an VM-Grenzwerte gebunden. Beispielsweise kann [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) eine maximale Bandbreite von 16.000 MBit/s (oder 2 GBit/s) unterstützen. Die ausgehenden Vorgänge von der VM (Schreibvorgänge in den Speicher) werden gemessen, eingehende Vorgänge (Lesevorgänge aus dem Speicher) hingegen nicht. Die Leistung der Dateifreigabe hängt unter anderem von den Grenzwerten des Computernetzwerks, den CPUs, dem internen Speicher, der verfügbaren Netzwerkbandbreite, den E/A-Größen und der Parallelität ab.
- Der anfängliche Test dient normalerweise nur dem Aufwärmen. Verwerfen Sie seine Ergebnisse, und wiederholt den Test.
- Wenn die Leistung durch einen einzelnen Client beschränkt ist und die Workload noch immer unter den bereitgestellten Freigabegrenzwerten liegt, kann eine höhere Leistung erzielt werden, indem die Last auf mehrere Clients verteilt wird.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>Beziehung zwischen IOPS, Durchsatz und E/A-Größen

**Durchsatz = E/A-Größe * IOPS**

Höhere E/A-Größen führen zu höherem Durchsatz und weisen höhere Latenzzeiten auf. Dies führt zu einer geringeren Anzahl von Netzwerk-IOPS. Kleinere A/A-Größen führen zu einem höheren IOPS-Wert, bewirken jedoch niedrigeren Netzwerkdurchsatz und Latenzzeiten.

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren von SMB Multichannel für ein FileStorage-Konto (Vorschau)](storage-files-enable-smb-multichannel.md)
- Weitere Informationen zu SMB Multichannel finden Sie in der [Windows-Dokumentation](/azure-stack/hci/manage/manage-smb-multichannel).
