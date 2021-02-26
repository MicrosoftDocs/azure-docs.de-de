---
title: Entwerfen und Implementieren einer Oracle-Datenbank in Azure | Microsoft-Dokumentation
description: Entwerfen und implementieren Sie eine Oracle-Datenbank in Ihrer Azure-Umgebung.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 0b6f4e652ca8fef7bee4165bcd0673be2fa11eac
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98890763"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Entwerfen und Implementieren einer Oracle-Datenbank in Azure

## <a name="assumptions"></a>Annahmen

- Sie planen die Migration einer Oracle-Datenbank von einem lokalen Standort zu Azure.
- Sie verfügen über das [Diagnostics Pack](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) oder [Automatic Workload Repository](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) für die Oracle Database-Datenbank, die Sie migrieren möchten.
- Sie sind mit den verschiedenen Metriken in Oracle vertraut.
- Sie besitzen Grundkenntnisse über Anwendungsleistung und Plattformnutzung.

## <a name="goals"></a>Ziele

- Sie möchten wissen, wie Sie Ihre Oracle-Bereitstellung in Azure optimieren können.
- Sie möchten die Optionen zur Leistungsoptimierung einer Oracle-Datenbank in einer Azure-Umgebung untersuchen.
- Sie haben klare Erwartungen an die Grenzen der physischen Optimierung mittels Architektur und der Vorteile bzw. der logischen Optimierung von Datenbankcode (SQL) und des gesamten Datenbankentwurfs.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Die Unterschiede zwischen einer lokalen und einer Azure-Implementierung 

Im Folgenden sind einige wichtige Aspekte aufgeführt, die Sie beachten müssen, wenn Sie lokale Anwendungen zu Azure migrieren. 

Ein wichtiger Unterschied besteht darin, dass in einer Azure-Implementierung Ressourcen wie virtuelle Computer, Datenträger und virtuelle Netzwerke für andere Clients freigegeben werden. Darüber hinaus können Ressourcen je nach Anforderungen gedrosselt werden. Anstatt sich auf das Vermeiden von Ausfällen zu konzentrieren (MTBF), ist Azure eher darauf ausgerichtet, Ausfälle zu überstehen (MTTR).

Die folgende Tabelle enthält einige der Unterschiede zwischen einer lokalen Implementierung und einer Azure-Implementierung von Oracle-Datenbanken.


|  | Lokale Implementierung | Azure-Implementierung |
| --- | --- | --- |
| **Netzwerk** |LAN/WAN  |SDN (Software-Defined Networking)|
| **Sicherheitsgruppe** |Tools für IP/Port-Einschränkungen |[Netzwerksicherheitsgruppe (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Resilienz** |MTBF (Mean Time Between Failure, mittlere Betriebsdauer zwischen Ausfällen) |MTTR (Mean Time To Recover, mittlere Reparaturzeit)|
| **Geplante Wartung** |Patchen/Upgrades|[Verfügbarkeitsgruppen](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (Patchen/Upgrades werden von Azure verwaltet) |
| **Ressource** |Dediziert  |Für andere Clients freigegeben|
| **Regionen** |Rechenzentren |[Regionspaare](../../regions.md#region-pairs)|
| **Storage** |SAN-/Physische Datenträger |[Von Azure verwalteter Speicher](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Skalieren** |Vertikale Skalierung |Horizontale Skalierung|


### <a name="requirements"></a>Requirements (Anforderungen)

- Bestimmen Sie die tatsächliche CPU-Auslastung. Da Oracle nach Anzahl der Kerne lizenziert wird, kann das Bestimmen des Bedarfs an virtuellen CPUs ein wesentlicher Faktor für Kosteneinsparungen sein. 
- Bestimmen Sie Größe, Sicherungsspeicher und Wachstumsrate der Datenbank.
- Bestimmen Sie die E/A-Anforderungen, die Sie anhand der Oracle-Berichte „Statspack“ oder „AWR“ oder anderer Speicherüberwachungstools auf Betriebssystemebene schätzen können.

## <a name="configuration-options"></a>Konfigurationsoptionen

Es gibt vier mögliche Bereiche, die Sie optimieren können, um die Leistung in einer Azure-Umgebung zu verbessern:

- Größe des virtuellen Computers
- Netzwerkdurchsatz
- Datenträgertypen und -konfigurationen
- Cacheeinstellungen von Datenträgern

### <a name="generate-an-awr-report"></a>Generieren eines AWR-Berichts

Wenn Sie über eine Oracle Enterprise Edition-Datenbank verfügen und die Migration zu Azure planen, haben Sie mehrere Optionen. Wenn Sie das [Diagnostics Pack](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) für Ihre Oracle-Instanzen haben, können Sie den Oracle AWR-Bericht ausführen, um die Metriken (IOPS, Mbit/s, GiB/s) abzurufen. Für Datenbanken ohne Diagnostics Pack-Lizenz oder für eine Standard Edition-Datenbank können die gleichen wichtigen Metriken mithilfe eines Statspack-Berichts gesammelt werden, nachdem manuelle Momentaufnahmen erstellt wurden.  Der Hauptunterschied zwischen diesen beiden Berichtsmethoden besteht darin, dass AWR automatisch gesammelt wird und mehr Informationen zur Datenbank liefert als die vorherige Berichtsoption Statspack.

Sie können den AWR-Bericht auch für reguläre und maximale Workloads ausführen und die Ergebnisse nachfolgend vergleichen. Um die Workload genauer zu erfassen, sollten Sie einen Bericht mit einem erweiterten Zeitfenster von einer Woche im Vergleich zu 24 Stunden erwägen. Außerdem sollten Sie bedenken, dass AWR Durchschnittswerte als Teil seiner Berechnungen im Bericht verwendet.  Für die Migration eines Rechenzentrums empfehlen wir, Berichte für die Dimensionierung der Produktionssysteme zu sammeln und die verbleibenden Datenbankkopien, die für Benutzertests, Tests, Entwicklung usw. verwendet werden, prozentual abzuschätzen (Benutzerakzeptanztests entsprechend der Produktion, Test und Entwicklung mit 50 % der Produktionsdimensionierung usw.)

Standardmäßig speichert das AWR-Repository die Daten 8 Tage und erstellt Momentaufnahmen in stündlichen Abständen.  Zum Ausführen eines AWR-Berichts über die Befehlszeile können Sie Folgendes in einem Terminal ausführen:

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Die wichtigsten Metriken

Der Bericht fragt die folgenden Informationen ab:
- Berichtstyp: HTML oder TEXT (HTML in 12.1 bietet mehr Informationen als das TEXT-Format).
- Die Anzahl der Tage mit anzuzeigenden Momentaufnahmen (bei einstündigen Intervallen würde ein einwöchiger Bericht 168 unterschiedliche Momentaufnahmen-IDs aufweisen)
- Die erste Momentaufnahmen-ID für das Zeitfenster des Berichts.
- Die letzte Momentaufnahmen-ID für das Zeitfenster des Berichts.
- Der Name des Berichts, der durch das AWR-Skript erstellt werden soll.

Bei Ausführung von AWR in einem Real Application Cluster (RAC) lautet der Befehlszeilenbericht awrgrpt.sql statt awrrpt.sql.  Der Bericht „g“ erstellt einen Bericht für alle Knoten in der RAC-Datenbank in einem einzigen Bericht, anstatt einen auf jedem RAC-Knoten ausführen zu müssen.

Folgende Metriken können Sie aus dem AWR-Bericht abrufen:

- Datenbank-, Instanz- und Hostname
- Datenbankversion (Unterstützbarkeit durch Oracle)
- CPU/Kerne
- SGA/PGA (und Ratgeber im Falle einer Unterdimensionierung)
- Gesamter Speicher in GB
- Auslastung der CPU in %
- Datenbank-CPUs
- IOPS (Lesen/Schreiben)
- MBit/s (Lesen/Schreiben)
- Netzwerkdurchsatz
- Netzwerklatenzrate (niedrig/hoch)
- Wichtigste Warteereignisse 
- Parametereinstellungen für Datenbank
- Ob Datenbank zu RAC gehört, Exadata, unter Verwendung erweiterter Features oder Konfigurationen

### <a name="virtual-machine-size"></a>Größe des virtuellen Computers

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Schätzen der Größe der VM basierend auf CPU-, Speicher- und E/A-Auslastung gemäß AWR-Bericht

Sie können sich unter anderem die fünf wichtigsten zeitgesteuerten Vordergrundereignisse ansehen, die auf die Engpässe im System hinweisen.

In der folgenden Abbildung steht die Protokolldateisynchronisierung z.B. ganz oben. Die Anzahl von Wartevorgängen wird angezeigt, die erforderlich sind, bevor LGWR den Protokollpuffer in die Redo-Protokolldatei schreibt. Diese Ergebnisse zeigen, dass ein leistungsfähigerer Speicher oder leistungsfähigere Datenträger erforderlich sind. Darüber hinaus werden auch die Anzahl von CPUs (Kernen) und die Speichermenge im Diagramm angezeigt.

![Screenshot: Protokolldateisynchronisierung oben in der Tabelle](./media/oracle-design/cpu_memory_info.png)

Die folgenden Diagramme zeigen das gesamte E/A-Volumen der Lese- und Schreibvorgänge. Während der Berichtsausführung wurden 59 GB gelesen und 247,3 GB geschrieben.

![Screenshot: Gesamtes E/A-Volumen der Lese- und Schreibvorgänge](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Auswählen einer VM

Basierend auf den Informationen aus dem AWR-Bericht wählen Sie im nächsten Schritt eine VM mit ähnlicher Größe aus, die Ihren Anforderungen entspricht. Eine Liste der verfügbaren VMs finden Sie im Artikel [Memory optimized](../../sizes-memory.md) (Optimierter Speicher).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Optimieren der VM-Größe mit ähnlicher, auf der ACU basierender VM-Serie

Sobald Sie die VM ausgewählt haben, sollten Sie auf die Azure Compute-Einheit (Azure Compute Unit, ACU) für die VM achten. Sie können sich anhand des ACU-Werts auch für eine andere VM entscheiden, die Ihre Anforderungen möglicherweise besser erfüllt. Weitere Informationen finden Sie unter [Azure-Compute-Einheit (ACU)](../../acu.md).

![Screenshot der Seite der ACUs](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Netzwerkdurchsatz

Die folgende Abbildung zeigt die Beziehung zwischen Durchsatz und IOPS:

![Screenshot des Durchsatzes](./media/oracle-design/throughput.png)

Der gesamte Netzwerkdurchsatz wird anhand der folgenden Informationen geschätzt:
- SQL*Net-Datenverkehr
- MBit/s × Anzahl von Servern (ausgehender Datenstrom wie z.B. Oracle Data Guard)
- Andere Faktoren wie z.B. Anwendungsreplikation

![Screenshot des SQL*Net-Durchsatzes](./media/oracle-design/sqlnet_info.png)

Je nach Ihren Anforderungen an die Netzwerkbandbreite können Sie aus verschiedenen Gatewaytypen wählen. Dazu gehören Basic, VpnGw und Azure ExpressRoute. Weitere Informationen finden Sie auf der Seite [VPN Gateway – Preise](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Empfehlungen**

- Die Netzwerklatenz ist höher als bei einer lokalen Bereitstellung. Eine Verringerung der Netzwerkroundtrips kann die Leistung deutlich verbessern.
- Zur Reduzierung von Roundtrips sollten Anwendungen, die ein hohes Transaktionsaufkommen aufweisen oder kommunikationsintensiv sind, auf demselben virtuellen Computer konsolidiert werden.
- Verwenden Sie Virtual Machines mit [beschleunigtem Netzwerkbetrieb](../../../virtual-network/create-vm-accelerated-networking-cli.md), um eine bessere Netzwerkleistung zu erzielen.
- Erwägen Sie für bestimmte Linux-Distributionen die Aktivierung der [TRIM/UNMAP-Unterstützung](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Installieren Sie [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) auf einem separaten virtuellen Computer.
- Große Seiten sind unter Linux nicht standardmäßig aktiviert. Erwägen Sie das Aktivieren großer Seiten, und legen Sie `use_large_pages = ONLY` für die Oracle Database fest. Dies kann helfen, die Leistung zu steigern. Weitere Informationen finden Sie [hier](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Datenträgertypen und -konfigurationen

- *Standard-Betriebssystemdatenträger*: Diese Datenträgertypen bieten persistente Daten und Zwischenspeicherung. Sie sind für den Betriebssystemzugriff beim Systemstart optimiert und nicht für Transaktionsworkloads oder Data Warehouse-Workloads (analytisch) ausgelegt.

- *Verwaltete Datenträger*: Azure verwaltet die Speicherkonten, die Sie für Ihre VM-Datenträger verwenden. Sie geben den Datenträgertyp (für Oracle-Workloads meist SSD Premium) und die Größe des Datenträgers an, den Sie benötigen. Azure erstellt und verwaltet den Datenträger für Sie.  Ein verwalteter SSD Premium-Datenträger ist nur für speicheroptimierte und speziell entworfene VM-Serien verfügbar. Nachdem Sie eine bestimmte VM-Größe ausgewählt haben, zeigt das Menü nur die verfügbaren Storage Premium-SKUs an, die auf dieser VM-Größe basieren.

![Screenshot der Seite „Verwaltete Datenträger“](./media/oracle-design/premium_disk01.png)

Nachdem Sie Ihren Speicher auf einer VM konfiguriert haben, sollten Sie vor dem Erstellen einer Datenbank einen Auslastungstest für die Datenträger ausführen. Wenn Sie die E/A-Rate im Hinblick auf Latenz und Durchsatz kennen, können Sie damit besser bestimmen, ob die VMs den erwarteten Durchsatz mit Latenzzielen unterstützen.

Es gibt eine Reihe von Tools für den Auslastungstest einer Anwendung wie z. B. Oracle Orion, Sysbench, SLOB und Fio.

Führen Sie den Auslastungstest erneut aus, nachdem Sie eine Oracle-Datenbank bereitgestellt haben. Starten Sie Ihre reguläre und maximale Workload, und die Ergebnisse zeigen Ihnen die Baseline Ihrer Umgebung.  Seien Sie beim Workloadtest realistisch, da es wenig sinnvoll ist, eine Workload auszuführen, die nichts mit dem zu tun hat, was Sie tatsächlich auf der VM ausführen werden.

Da Oracle in vielen Fällen eine E/A-intensive Datenbank ist, sollte die Größe des Speichers eher anhand der IOPS-Rate als anhand der Speichergröße bestimmt werden. Wenn z.B. die erforderliche IOPS-Rate 5.000 beträgt, Sie aber nur 200 GB benötigen, erhalten Sie möglicherweise immer noch den Premium-Datenträger der P30-Klasse, obwohl er mehr als 200 GB Speicher bietet.

Die IOPS-Rate kann aus dem AWR-Bericht abgerufen werden. Sie wird durch das Redo-Protokoll sowie die Rate physischer Lese- und Schreibvorgänge bestimmt.  Vergewissern Sie sich stets, dass die gewählte VM-Serie die E/A-Anforderungen der Workload auch bewältigen kann.  Wenn die VM einen niedrigeren E/A-Grenzwert als der Speicher hat, wird der maximale Grenzwert von der VM festgelegt.

![Screenshot der AWR-Berichtsseite](./media/oracle-design/awr_report.png)

Beispiel: Die Größe des Redo-Protokolls beträgt 12.200.000 Bytes pro Sekunde, was 11,63 MBit/s entspricht.
Die IOPS-Rate beträgt 12.200.000 : 2.358 = 5.174.

Sobald Sie eine genaue Vorstellung von den E/A-Anforderungen haben, können Sie eine Kombination von Laufwerken auswählen, die für diese Anforderungen am besten geeignet ist.

**Empfehlungen**

- Verteilen Sie die E/A-Workload für den DATA-Tabellenbereich mit verwaltetem Speicher oder Oracle ASM auf mehrere Datenträger.
- Reduzieren Sie die E/A mithilfe der erweiterten Oracle-Komprimierung (für Daten und Indizes).
- Legen Sie Redo-Protokolle sowie TEMP- und UNDO-Tabellenbereiche auf separaten Datenträgern ab.
- Speichern Sie keine Anwendungsdateien auf dem standardmäßigen Betriebssystemdatenträger (/dev/sda). Diese Datenträger sind für schnelle VM-Startzeiten optimiert und erbringen für Ihre Anwendung möglicherweise keine gute Leistung.
- Wenn Sie VMs der M-Serie in Storage Premium verwenden, aktivieren Sie [Schreibbeschleunigung](../../how-to-enable-write-accelerator.md) für Datenträger mit Wiederholungsprotokollen.
- Erwägen Sie das Verschieben von Redo-Protokollen mit hoher Latenzzeit auf Ultra-Datenträger.

### <a name="disk-cache-settings"></a>Cacheeinstellungen von Datenträgern

Es gibt drei Optionen für die Hostzwischenspeicherung, aber bei einer Oracle-Datenbank wird für eine Datenbank-Workload nur ReadOnly-Caching empfohlen.  ReadWrite kann erhebliche Schwachstellen in einer Datendatei verursachen, denn das Ziel eines Schreibvorgangs in einer Datenbank ist es, den Datensatz in der Datendatei zu speichern und nicht die Informationen zwischenzuspeichern.

Im Gegensatz zu einem Dateisystem oder einer Anwendung lautet die Empfehlung für die Hostzwischenspeicherung bei einer Datenbank *ReadOnly*: Alle Anforderungen werden für künftige Lesevorgänge zwischengespeichert. Alle Schreibvorgänge werden weiterhin auf Datenträger geschrieben.

**Empfehlungen**

Zur Maximierung des Durchsatzes empfehlen wir, nach Möglichkeit mit **ReadOnly** für die Hostzwischenspeicherung zu beginnen. Beachten Sie bei Storage Premium, dass Sie die „Barrieren“ deaktivieren müssen, wenn Sie das Dateisystem mit der Option **ReadOnly** bereitstellen. Aktualisieren Sie die Datei „/etc/fstab“ mit der UUID auf die Datenträger.

![Screenshot: Seite der verwalteten Datenträger mit den Optionen „Schreibgeschützt“ und „Kein“](./media/oracle-design/premium_disk02.png)

- Verwenden Sie für Betriebssystemdatenträger die Standardzwischenspeicherung **Lesen/Schreiben** und für Oracle-Workload-VMs SSD Premium-Datenträger.  Stellen Sie außerdem sicher, dass sich das für die Auslagerung genutzte Volume ebenfalls auf SSD Premium-Datenträgern befindet.
- Verwenden Sie für alle DATAFILES zur Zwischenspeicherung **ReadOnly**. ReadOnly-Zwischenspeichern ist nur für verwaltete Premium-Datenträger, P30 und höher, verfügbar.  Es gilt eine Obergrenze von 4.095 GiB für ein Volume, das mit ReadOnly-Zwischenspeichern verwendet werden kann.  Bei jeder größeren Zuteilung wird die Hostzwischenspeicherung standardmäßig deaktiviert.

Wenn die Workloads zwischen Tag und Nacht stark variieren und die E/A-Workload dies unterstützen kann, kann SSD Premium (P1-P20) mit Bursting die erforderliche Leistung bei nächtlichen Batchlasten oder begrenzten E/A-Anforderungen bieten.  

## <a name="security"></a>Sicherheit

Nachdem Sie Ihre Azure-Umgebung eingerichtet und konfiguriert haben, besteht der nächste Schritt im Sichern des Netzwerks. Hier sind einige Empfehlungen dafür:

- *NSG-Richtlinie*: NSG kann von einem Subnetz oder einer NIC definiert werden. Für Anwendungsfirewalls etwa vereinfacht die Zugriffssteuerung auf Subnetzebene die Sicherheitsimplementierung und Routingerzwingung.

- *Jumpbox:* Damit der Zugriff noch sicherer wird, sollten Administratoren keine direkte Verbindung zum Anwendungsdienst oder zur Datenbank herstellen. Eine Jumpbox wird als Medium zwischen dem Administratorcomputer und Azure-Ressourcen verwendet.
![Screenshot der Jumpbox-Topologieseite](./media/oracle-design/jumpbox.png)

    Der Administratorcomputer sollte nur IP-beschränkten Zugriff auf die Jumpbox bieten. Die Jumpbox sollte dann Zugriff auf Anwendung und Datenbank haben.

- *Privates Netzwerk* (Subnetze): Sie sollten den Anwendungsdienst und die Datenbank in separaten Subnetzen installieren, damit eine bessere Steuerung über die NSG-Richtlinie festgelegt werden kann.


## <a name="additional-reading"></a>Zusätzliche Lektüre

- [Konfigurieren von Oracle ASM](configure-oracle-asm.md)
- [Konfigurieren von Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurieren von Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Sichern und Wiederherstellen einer Oracle-Datenbank](./oracle-overview.md)

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erstellen von hoch verfügbaren virtuellen Computern](../../linux/create-cli-complete.md)
- [Erkunden der Azure CLI-Beispiele für die Bereitstellung virtueller Computer](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
