---
title: Datenquellen für das Sammeln von Windows- und Linux-Leistungsdaten mit dem Log Analytics-Agent in Azure Monitor
description: Leistungsindikatoren werden von Azure Monitor gesammelt, um die Leistung von Windows- und Linux-Agents zu analysieren.  Dieser Artikel beschreibt, wie Sie die Sammlung von Leistungsindikatoren sowohl für Windows- als auch für Linux-Agents konfigurieren, wie die Daten im Arbeitsbereich gespeichert werden und wie sie im Azure-Portal analysiert werden können.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 533d4a83ea73b98e26a57febc077a607bcb25465
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532300"
---
# <a name="collect-windows-and-linux-performance-data-sources-with-log-analytics-agent"></a>Datenquellen für das Sammeln von Windows- und Linux-Leistungsdaten mit dem Log Analytics-Agent
Leistungsindikatoren in Windows und Linux bieten Einblick in die Leistung von Hardwarekomponenten, Betriebssystemen und Anwendungen.  Azure Monitor kann in sehr kurzen Intervallen Leistungsindikatoren von Log Analytics-Agents abrufen, um Analysen in Quasi-Echtzeit zu ermöglichen. Darüber hinaus kann Azure Monitor Leistungsdaten zusammenstellen, um längerfristige Analysen und Berichte zu ermöglichen.

> [!IMPORTANT]
> In diesem Artikel wird das Sammeln von Leistungsdaten mit dem [Log Analytics-Agent](log-analytics-agent.md) beschrieben, einem der von Azure Monitor verwendeten Agents. Andere Agents sammeln andere Daten und werden anders konfiguriert. Eine Liste der verfügbaren Agents und der von ihnen gesammelten Daten finden Sie unter [Übersicht über Azure Monitor-Agents](agents-overview.md).

![Leistungsindikatoren](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurieren von Leistungsindikatoren
Sie konfigurieren Leistungsindikatoren über das [Menü „Daten“ in „Erweiterte Einstellungen“](agent-data-sources.md#configuring-data-sources) für den Log Analytics-Arbeitsbereich.

Wenn Sie die Windows- oder Linux-Leistungsindikatoren zum ersten Mal für einen neuen Arbeitsbereich konfigurieren, haben Sie die Möglichkeit, schnell mehrere allgemeine Indikatoren zu erstellen.  Diese werden in einer Liste aufgeführt, und neben jedem Indikator finden Sie ein Kontrollkästchen.  Stellen Sie sicher, dass alle Leistungsindikatoren aktiviert sind, die Sie anfänglich erstellen möchten, und klicken Sie dann auf **Ausgewählte Leistungsindikatoren hinzufügen**.

Sie können für Windows-Leistungsindikatoren eine bestimmte Instanz für jeden Leistungsindikator auswählen. Bei Linux-Leistungsindikatoren gilt, dass die Instanz eines Indikators, die Sie wählen, für alle untergeordneten Indikatoren des übergeordneten Indikators gilt. Die folgende Tabelle zeigt die allgemeinen Instanzen, die jeweils für die Linux- und Windows-Leistungsindikatoren verfügbar sind.

| Instanzname | BESCHREIBUNG |
| --- | --- |
| \_Gesamt |Gesamtsumme aller Instanzen |
| \* |Alle Instanzen |
| (/&#124;/var) |Stimmt mit den Instanzen mit den Namen „/“ oder „/var“ überein. |

### <a name="windows-performance-counters"></a>Windows-Leistungsindikatoren

![Windows-Leistungsindikatoren konfigurieren](media/data-sources-performance-counters/configure-windows.png)

Gehen Sie folgendermaßen vor, um einen neuen Windows-Leistungsindikator hinzuzufügen, aus dem Daten gesammelt werden sollen. Beachten Sie, dass die V2-Windows-Leistungsindikatoren nicht unterstützt werden.

1. Geben Sie den Namen des Leistungsindikators im Format *Objekt(Instanz)\Indikator* in das Textfeld ein.  Wenn Sie mit der Eingabe beginnen, wird Ihnen eine Liste mit passenden allgemeinen Indikatoren angezeigt.  Sie können einen Indikator aus der Liste auswählen oder selbst einen eingeben.  Sie können auch durch die Angabe von *Objekt\Indikator* alle Instanzen eines bestimmten Leistungsindikators zurückgeben.  

    Wenn SQL Server Leistungsindikatoren von benannten Instanzen erfasst, beginnen alle benannten Instanzindikatoren mit *MSSQL$* , und anschließend folgt der Name der Instanz.  Um beispielsweise den Indikator für die Protokollcache-Trefferrate für alle Datenbanken aus dem Datenbank-Leistungsobjekt für benannte SQL Server-Instanzen INST2 zu sammeln, geben Sie `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio` an.

2. Klicken Sie auf **+** , oder drücken Sie die **EINGABETASTE** um der Liste den Indikator hinzuzufügen.
3. Wenn Sie einen Leistungsindikator hinzufügen, verwendet dieser den Standardwert von 10 Sekunden für das **Stichprobenintervall**.  Sie können diesen Standardwert auf einen höheren Wert von bis zu 1800 Sekunden (30 Minuten) festlegen, wenn Sie die Speicheranforderungen der gesammelten Leistungsdaten reduzieren möchten.
4. Wenn Sie mit dem Hinzufügen von Leistungsindikatoren fertig sind, klicken Sie auf die Schaltfläche **Speichern** am oberen Bildschirmrand, um die Konfiguration zu speichern.

### <a name="linux-performance-counters"></a>Leistungsindikatoren von Linux

![Linux-Leistungsindikatoren konfigurieren](media/data-sources-performance-counters/configure-linux-1.png)

Gehen Sie folgendermaßen vor, um einen neuen Linus-Leistungsindikator hinzuzufügen, aus dem Daten gesammelt werden sollen.

1. Geben Sie den Namen des Leistungsindikators im Format *Objekt(Instanz)\Indikator* in das Textfeld ein.  Wenn Sie mit der Eingabe beginnen, wird Ihnen eine Liste mit passenden allgemeinen Indikatoren angezeigt.  Sie können einen Indikator aus der Liste auswählen oder selbst einen eingeben.  
1. Klicken Sie auf **+** oder drücken Sie die **EINGABETASTE** um den Indikator der Liste der anderen Leistungsindikatoren für das Objekt hinzuzufügen.
1. Alle Leistungsindikatoren für ein Objekt verwenden das gleiche **Stichprobenintervall**.  Der Standardwert ist 10 Sekunden.  Sie können einen höheren Wert von bis zu 1800 Sekunden (30 Minuten) festlegen, wenn Sie die Speicheranforderungen der gesammelten Leistungsdaten reduzieren möchten.
1. Wenn Sie mit dem Hinzufügen von Leistungsindikatoren fertig sind, klicken Sie auf die Schaltfläche **Speichern** am oberen Bildschirmrand, um die Konfiguration zu speichern.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfigurieren von Linux-Leistungsindikatoren in der Konfigurationsdatei
Sie müssen die Linux-Leistungsindikatoren nicht mithilfe des Azure-Portals konfigurieren, sondern können die Konfigurationsdateien auch auf dem Linux-Agent bearbeiten.  Die Leistungsmetriken, die gesammelt werden, werden durch die Konfiguration in **/etc/opt/microsoft/omsagent/\<workspace id\>/conf/omsagent.conf** gesteuert.

Jedes Objekt oder jede Kategorie von Leistungsindikatoren, die gesammelt werden sollen, sollten in der Konfigurationsdatei als einzelnes `<source>` -Element definiert sein. Die Syntax folgt dem unten angegebenen Muster.

```xml
<source>
    type oms_omi  
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```


Die in diesem Element verwendeten Parameter werden in der folgenden Tabelle beschrieben.

| Parameter | BESCHREIBUNG |
|:--|:--|
| object\_name | Der Objektname für die Sammlung. |
| instance\_regex |  Ein *regulärer Ausdruck*, der definiert, welche Instanzen gesammelt werden sollen. Der Wert `.*` gibt alle Instanzen an. Sie können `_Total` angeben, um die Prozessormetriken nur für die Instanz „\_Total“ zu sammeln. Sie können `(crond\|sshd)`angeben, um die Prozessmetriken nur für die „crond“- oder „sshd“-Instanzen zu sammeln. |
| counter\_name\_regex | Ein *regulärer Ausdruck* für die zu sammelnden Leistungsindikatoren (für das Objekt). Geben Sie `.*`an, um alle Indikatoren für das Objekt zu sammeln. Um nur Indikatoren für Speicherauslagerungsbereiche zu sammeln, können Sie beispielsweise `.+Swap.+` angeben |
| interval | Häufigkeit, mit der die Indikatoren des Objekts gesammelt werden. |


Die folgende Tabelle enthält die Objekte und Leistungsindikatoren, die Sie in der Konfigurationsdatei angeben können.  Für bestimmte Anwendungen stehen weitere Indikatoren zur Verfügung. Diese werden unter [Collect performance counters for Linux applications in Azure Monitor (Sammeln von Leistungsindikatoren für Linux-Anwendungen in Azure Monitor)](data-sources-linux-applications.md) beschrieben.

| Objektname | Name des Leistungsindikators |
|:--|:--|
| Logischer Datenträger | % freie Inodes |
| Logischer Datenträger | % freier Speicher |
| Logischer Datenträger | % verwendete Inodes |
| Logischer Datenträger | % verwendeter Speicher |
| Logischer Datenträger | Byte gelesen/s |
| Logischer Datenträger | Lesevorgänge/s |
| Logischer Datenträger | Übertragungen/s |
| Logischer Datenträger | Byte geschrieben/s |
| Logischer Datenträger | Schreibvorgänge/s |
| Logischer Datenträger | Freie Megabytes |
| Logischer Datenträger | Logischer Datenträger Bytes/s |
| Arbeitsspeicher | % verfügbarer Speicher |
| Arbeitsspeicher | % verfügbarer Auslagerungsbereich |
| Arbeitsspeicher | % verwendeter Arbeitsspeicher |
| Arbeitsspeicher | % verwendeter Auslagerungsbereich |
| Arbeitsspeicher | Verfügbarer Speicher in MB |
| Arbeitsspeicher | Verfügbarer Auslagerungsbereich in MB |
| Arbeitsspeicher | Seitenlesevorgänge/s |
| Arbeitsspeicher | Schreibvorgänge/s |
| Arbeitsspeicher | Seiten/s |
| Arbeitsspeicher | Verwendeter Auslagerungsbereich in MB |
| Arbeitsspeicher | Verwendeter Speicher in MB |
| Netzwerk | Summe übertragene Bytes |
| Netzwerk | Summe empfangene Bytes |
| Netzwerk | Summe Bytes |
| Netzwerk | Summe übermittelte Pakete |
| Netzwerk | Summe empfangene Pakete |
| Netzwerk | Summe Rx-Fehler |
| Netzwerk | Summe Tx-Fehler |
| Netzwerk | Summe Konflikte |
| Physikalischer Datenträger | Durchschn. Datenträger s/gelesen |
| Physikalischer Datenträger | Durchschn. Datenträger s/übertragen |
| Physikalischer Datenträger | Durchschn. Datenträger s/geschrieben |
| Physikalischer Datenträger | Physischer Datenträger Bytes/s |
| Prozess | PCT-privilegierte Zeit |
| Prozess | PCT-Benutzerzeit |
| Prozess | Verwendeter Arbeitsspeicher in KB |
| Prozess | Virtueller gemeinsamer Speicher |
| Prozessor | % DPC-Zeit |
| Prozessor | % Leerlaufzeit |
| Prozessor | % Interruptzeit |
| Prozessor | % E/a-Wartezeit |
| Prozessor | % Nice-Zeit |
| Prozessor | % privilegierte Zeit |
| Prozessor | % Prozessorzeit |
| Prozessor | % Benutzerzeit |
| System | Freier physischer Speicher |
| System | Freier Speicherplatz in Auslagerungsdateien |
| System | Freier virtueller Arbeitsspeicher |
| System | Prozesse |
| System | Genutzter Speicherplatz in Auslagerungsdateien |
| System | Betriebszeit |
| System | Benutzer |


Im Folgenden wird die Standardkonfiguration für Leistungsmetriken beschrieben.

```xml
<source>
    type oms_omi
    object_name "Physical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Logical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>

<source>
    type oms_omi
    object_name "Memory"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```

## <a name="data-collection"></a>Datensammlung
Azure Monitor sammelt alle angegebenen Leistungsindikatoren im angegebenen Stichprobenintervall auf allen Agents, auf denen diese Indikatoren installiert sind.  Die Daten werden nicht aggregiert, und die Rohdaten stehen während des durch Ihren Log Analytics-Arbeitsbereich festgelegten Zeitraums in allen Protokollabfrageansichten zur Verfügung.

## <a name="performance-record-properties"></a>Eigenschaften von Leistungsdatensätzen
Leistungsdatensätze weisen den Typ **Perf** auf und besitzen die in der folgenden Tabelle aufgeführten Eigenschaften.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| Computer |Computer, auf dem das Ereignis gesammelt wurde. |
| CounterName |Name des Leistungsindikators. |
| CounterPath |Vollständiger Pfad des Leistungsindikators im Format \\\\\<Computer>\\Objekt(Instanz)\\Leistungsindikator. |
| CounterValue |Numerischer Wert des Leistungsindikators |
| InstanceName |Name der Ereignisinstanz.  Leer, wenn keine Instanz vorhanden ist. |
| ObjectName |Name des Leistungsobjekts. |
| SourceSystem |Typ des Agents, auf dem die Daten gesammelt wurden. <br><br>OpsManager: Windows-Agent (Direktverbindung oder SCOM) <br> Linux: Alle Linux-Agents  <br> AzureStorage – Azure-Diagnose |
| TimeGenerated |Datum und Uhrzeit der Datenstichprobe. |

## <a name="sizing-estimates"></a>Größeneinschätzung
 Bei der Sammlung der Daten von einem bestimmten Leistungsindikator mit einem Intervall von 10 Sekunden können Sie von etwa 1 MB pro Tag und Instanz ausgehen.  Sie können die Speicheranforderungen eines bestimmten Leistungsindikators anhand der folgenden Formel einschätzen.

> 1 MB x (Anzahl der Leistungsindikatoren) x (Anzahl der Agents) x (Anzahl der Instanzen)

## <a name="log-queries-with-performance-records"></a>Protokollabfragen mit Leistungsdatensätzen
Die folgende Tabelle zeigt verschiedene Beispiele für Protokollabfragen, mit denen Leistungsdatensätze abgerufen werden.

| Abfrage | BESCHREIBUNG |
|:--- |:--- |
| Perf |Alle Leistungsdaten. |
| Perf &#124; where Computer == "MyComputer" |Alle Leistungsdaten eines bestimmten Computers. |
| Perf &#124; where CounterName == "Aktuelle Warteschlangenlänge" |Alle Leistungsdaten eines bestimmten Leistungsindikators. |
| Perf &#124; where ObjectName == "Prozessor" and CounterName == "% Prozessorzeit" and InstanceName == "_Total" &#124; summarize AVGCPU = avg(CounterValue) by Computer |Durchschnittliche CPU-Nutzung aller Computer. |
| Perf &#124; where CounterName == "% Prozessorzeit" &#124; summarize AggregatedValue = max(CounterValue) by Computer |Maximale CPU-Nutzung aller Computer. |
| Perf &#124; where ObjectName == "Logischer Datenträger" and CounterName == "Aktuelle Warteschlangenlänge" and Computer == "MyComputerName" &#124; summarize AggregatedValue = avg(CounterValue) by InstanceName |Durchschnittliche aktuelle Länge der Datenträgerwarteschlangen aller Instanzen eines bestimmten Computers |
| Perf &#124; where CounterName == "Übertragungen/s" &#124; summarize AggregatedValue = percentile(CounterValue, 95) by Computer |95. Perzentil der Datenträgerübertragungen pro Sekunde auf allen Computern. |
| Perf &#124; where CounterName == "Prozessorzeit (%)" and InstanceName == "_Total" &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), Computer |Durchschnittliche CPU-Nutzung pro Stunde auf allen Computern |
| Perf &#124; where Computer == "MyComputer" and CounterName startswith_cs "%" and InstanceName == "_Total" &#124; summarize AggregatedValue = percentile(CounterValue, 70) by bin(TimeGenerated, 1h), CounterName | 70. Perzentil pro Stunde jedes prozentualen Indikators für einen bestimmten Computer |
| Perf &#124; where CounterName == "Prozessorzeit (%)" and InstanceName == "_Total" and Computer == "MyComputer" &#124; summarize ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentile(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) by bin(TimeGenerated, 1h), Computer |Durchschnittliche, minimale, maximale und 75.-Perzentil-CPU-Nutzung pro Stunde für einen bestimmten Computer |
| Perf &#124; where ObjectName == "MSSQL$INST2:Databases" and InstanceName == "master" | Alle Leistungsdaten aus dem Datenbank-Leistungsobjekt für die Masterdatenbank von der benannten SQL Server-Instanz INST2.  




## <a name="next-steps"></a>Nächste Schritte
* [Sammeln von Leistungsindikatoren aus Linux-Anwendungen wie](data-sources-linux-applications.md) MySQL und Apache HTTP Server.
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zum Analysieren der aus Datenquellen und Lösungen gesammelten Daten.  
* Exportieren Sie gesammelte Daten nach [Power BI](powerbi.md) , um weitere Möglichkeiten der Visualisierung und Analyse zu nutzen.
