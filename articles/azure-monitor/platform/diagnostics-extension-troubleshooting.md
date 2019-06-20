---
title: Problembehandlung bei der Azure-Diagnoseerweiterung
description: Behandeln Sie Probleme bei der Verwendung der Azure-Diagnose in Azure Virtual Machines, Service Fabric und Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: robb
ms.openlocfilehash: 99ac4ffc288773e52183d371ef2c20f6153bc0f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65471782"
---
# <a name="azure-diagnostics-troubleshooting"></a>Problembehandlung mit Azure-Diagnose
Dieser Artikel enthält Informationen zur Problembehandlung, die für die Verwendung der Azure-Diagnose relevant sind. Weitere Informationen zur Azure-Diagnose finden Sie unter [Überblick über Azure-Diagnose](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Logische Komponenten
**Startprogramm für Diagnose-Plug-In (DiagnosticsPluginLauncher.exe)** : Startet die Azure-Diagnoseerweiterung. Dient als Prozess für den Einstiegspunkt.

**Diagnose-Plug-In (DiagnosticsPlugin.exe)** : Dient zum Konfigurieren, Starten und Verwalten der Lebensdauer des Überwachungs-Agent. Dies ist der Hauptprozess, der vom Startprogramm gestartet wird.

**Überwachungs-Agent (MonAgent\*.exe-Prozesse)** : Überwacht, erfasst und überträgt die Diagnosedaten.  

## <a name="logartifact-paths"></a>Protokoll-/Artefaktpfade
Hier sind die Pfade zu einigen wichtigen Protokollen und Artefakten angegeben. Wir verweisen im weiteren Verlauf des Dokuments immer wieder auf diese Informationen.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefakt | `Path` |
| --- | --- |
| **Azure-Diagnosekonfigurationsdatei** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Protokolldateien** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Lokaler Speicher für Diagnosedaten** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Konfigurationsdatei für Monitoring Agent** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Paket mit Azure-Diagnoseerweiterung** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Pfad des Hilfsprogramms für die Protokollsammlung** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost-Protokolldatei** | C:\Resources\Directory\<Clouddienstbereitstellungs-ID>.\<Rollenname>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<Sequenznummer>.log |

### <a name="virtual-machines"></a>Virtuelle Computer
| Artefakt | `Path` |
| --- | --- |
| **Azure-Diagnosekonfigurationsdatei** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Protokolldateien** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Lokaler Speicher für Diagnosedaten** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Konfigurationsdatei für Monitoring Agent** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Statusdatei** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Paket mit Azure-Diagnoseerweiterung** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Pfad des Hilfsprogramms für die Protokollsammlung** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **MonAgentHost-Protokolldatei** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<Diagnoseversion>\WAD0107\Configuration\MonAgentHost.<Sequenznummer>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Metrikdaten werden nicht im Azure-Portal angezeigt
Bei der Azure-Diagnose werden Metrikdaten bereitgestellt, die im Azure-Portal angezeigt werden können. Falls Sie Probleme beim Anzeigen der Daten im Portal haben, können Sie die Tabelle „WADMetrics\*“ im Azure-Diagnose-Speicherkonto überprüfen, um festzustellen, ob die entsprechenden Metrikdatensätze vorhanden sind.

Hier ist der **PartitionKey** der Tabelle die Ressourcen-ID, der virtuelle Computer oder die VM-Skalierungsgruppe. **RowKey** ist der Metrikname (auch als Leistungsindikatorname bezeichnet).

Wenn die Ressourcen-ID falsch ist, sollten Sie unter **Diagnose** **Konfiguration** > **Metriken** > **ResourceId** überprüfen, ob die Ressourcen-ID richtig festgelegt ist.

Wenn keine Daten für die spezifische Metrik vorhanden sind, sollten Sie unter **Diagnosekonfiguration** > **PerformanceCounter** überprüfen, ob die Metrik (Leistungsindikator) vorhanden ist. Die folgenden Leistungsindikatoren sind standardmäßig aktiviert:
- \Processor(_Total)\%Prozessorzeit
- \Memory\Verfügbare Bytes
- \ASP.NET-Anwendungen(__Insgesamt__)\Anforderungen/Sek.
- \ASP.NET-Anwendungen(__Insgesamt__)\Fehler gesamt/Sek.
- \ASP.NET\Anforderungen in Warteschlange
- \ASP.NET\Zurückgewiesene Anforderungen
- \Prozessor(w3wp)\% Prozessorzeit
- \Prozess(w3wp)\Private Bytes
- \Prozess(WaIISHost)\% Prozessorzeit
- \Prozess(WaIISHost)\Private Bytes
- \Prozess(WaWorkerHost)\% Prozessorzeit
- \Prozess(WaWorkerHost)\Private Bytes
- \Arbeitsspeicher\Seitenfehler/Sek.
- \..NET CLR-Speicher(_Global_)\% Zeit in GC
- \Logischer Datenträger(C:)\Auf den Datenträger geschriebene Bytes/s
- \Logischer Datenträger(C:)\Vom Datenträger gelesene Bytes/s
- \Logischer Datenträger(D:)\Auf den Datenträger geschriebene Bytes/s
- \Logischer Datenträger(D:)\Vom Datenträger gelesene Bytes/s

Wenn die Konfiguration richtig festgelegt ist und die Metrikdaten trotzdem nicht angezeigt werden, können Sie sich als Hilfe bei der Problembehandlung an die folgenden Richtlinien halten.


## <a name="azure-diagnostics-is-not-starting"></a>Die Azure-Diagnose wird nicht gestartet.
Informationen dazu, warum die Azure-Diagnose nicht gestartet wurde, finden Sie in den Dateien **DiagnosticsPluginLauncher.log** und **DiagnosticsPlugin.log** am zuvor angegebenen Speicherort der Protokolldateien.

Die Angabe `Monitoring Agent not reporting success after launch` in diesen Protokollen bedeutet, dass beim Starten von „MonAgentHost.exe“ ein Fehler aufgetreten ist. Sehen Sie sich die Protokolle an dem Speicherort an, der im vorherigen Abschnitt für `MonAgentHost log file` angegeben ist.

Die letzte Zeile der Protokolldateien enthält den Exitcode.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Wenn Sie einen **negativen** Exitcode finden, hilft Ihnen die [Exitcode-Tabelle](#azure-diagnostics-plugin-exit-codes) im Abschnitt [Referenzen](#references) weiter.

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnosedaten werden nicht in Azure Storage protokolliert
Ermitteln Sie, ob keine oder nur einige Daten angezeigt werden.

### <a name="diagnostics-infrastructure-logs"></a>Diagnoseinfrastrukturprotokolle
Bei der Diagnose werden alle Fehler in Diagnoseinfrastrukturprotokollen protokolliert. Stellen Sie sicher, dass Sie die [Erfassung von Diagnoseinfrastrukturprotokollen in Ihrer Konfiguration](#how-to-check-diagnostics-extension-configuration) aktiviert haben. Anschließend können Sie schnell nach allen relevanten Fehlern suchen, die unter Ihrem konfigurierten Speicherkonto in der Tabelle `DiagnosticInfrastructureLogsTable` angezeigt werden.

### <a name="no-data-is-appearing"></a>Es werden keine Daten angezeigt
Die häufigste Ursache dafür, dass keine Ereignisdaten angezeigt werden, ist die fehlerhafte Definition der Speicherkontoinformationen.

Lösung: Korrigieren Sie die Diagnostics-Konfiguration, und installieren Sie Diagnostics erneut.

Wenn das Speicherkonto richtig konfiguriert wurde, sollten Sie den Remotezugriff auf den Computer durchführen und überprüfen, ob *DiagnosticsPlugin.exe* und *MonAgentCore.exe* ausgeführt werden. Wenn nicht, sollten Sie die Schritte unter [Die Azure-Diagnose wird nicht gestartet](#azure-diagnostics-is-not-starting) ausführen.

Wenn die Prozesse ausgeführt werden, können Sie zu [Lokale Erfassung von Daten](#is-data-getting-captured-locally) navigieren und die angegebene Anleitung befolgen.

Gehen Sie wie folgt vor, wenn das Problem dadurch nicht behoben wird:

1. Deinstallieren des Agents
2. Löschen Sie das Verzeichnis C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics.
3. Installieren Sie den Agent neu.


### <a name="part-of-the-data-is-missing"></a>Ein Teil der Daten fehlt
Wenn Sie nicht alle Daten erhalten, sondern nur einige, bedeutet dies, dass die Pipeline für die Datensammlung bzw. -übertragung richtig eingerichtet ist. Mit den Informationen in den folgenden Unterabschnitten können Sie die Ursache des Problems eingrenzen:

#### <a name="is-the-collection-configured"></a>Konfiguration der Sammlung
Die Diagnosekonfiguration enthält eine Anleitung zum Sammeln von Daten eines bestimmten Typs. [Überprüfen Sie Ihre Konfiguration](#how-to-check-diagnostics-extension-configuration), um sicherzustellen, dass Sie nur nach den für die Sammlung konfigurierten Daten suchen.

#### <a name="is-the-host-generating-data"></a>Generierung von Daten durch den Host
- **Leistungsindikatoren**: Öffnen Sie den Systemmonitor, und überprüfen Sie den Leistungsindikator.

- **Ablaufverfolgungsprotokolle**:  Führen Sie den Remotezugriff auf die VM durch, und fügen Sie der Konfigurationsdatei der App einen TextWriterTraceListener hinzu.  Informationen zum Einrichten des Textlisteners finden Sie unter https://msdn.microsoft.com/library/sk36c28t.aspx.  Stellen Sie sicher, dass für das `<trace>`-Element `<trace autoflush="true">` festgelegt ist.<br />
Wenn Sie nicht sehen, dass Ablaufverfolgungsprotokolle generiert werden, helfen Ihnen die Informationen unter „Weitere Informationen zu fehlenden Ablaufverfolgungsprotokollen“ weiter.

- **ETW-Ablaufverfolgungen**: Führen Sie den Remotezugriff auf die VM durch, und installieren Sie PerfView.  Führen Sie in PerfView Folgendes aus: **File** > **User Command** > **Listen etwprovider1** > **etwprovider2** (Datei > Benutzerbefehl > Lauschen etwprovider1 > etwprovider2) usw. Beim Befehl **Listen** (Lauschen) wird die Groß-/Kleinschreibung beachtet, und die kommagetrennte Liste mit ETW-Anbietern darf keine Leerstellen enthalten. Falls der Befehl nicht ausgeführt werden kann, können Sie im PerfView-Tool unten rechts die Schaltfläche **Log** (Protokoll) wählen, um anzuzeigen, was ausgeführt werden sollte und wie das Ergebnis lautet.  Es wird ein neues Fenster angezeigt, wenn die Eingabe korrekt ist. Nach einigen Sekunden werden die ersten Ereignisablaufverfolgungen für Windows angezeigt.

- **Ereignisprotokolle**: Führen Sie den Remotezugriff auf die VM durch. Öffnen Sie `Event Viewer`, und stellen Sie anschließend sicher, dass die Ereignisse vorhanden sind.

#### <a name="is-data-getting-captured-locally"></a>Lokale Erfassung von Daten
Stellen Sie als Nächstes sicher, dass die Daten lokal erfasst werden.
Die Daten werden lokal in `*.tsf`-Dateien im lokalen Speicher für die Diagnosedaten gespeichert. Verschiedene Arten von Protokollen werden in unterschiedlichen `.tsf`-Dateien erfasst. Die Namen ähneln den Tabellennamen in Azure Storage.

`Performance Counters` werden beispielsweise in `PerformanceCountersTable.tsf` gesammelt. Ereignisprotokolle werden in `WindowsEventLogsTable.tsf` gesammelt. Nutzen Sie die Anleitung im Abschnitt [Extraktion von lokalen Protokollen](#local-log-extraction), um die lokalen Sammlungsdateien zu öffnen, und vergewissern Sie sich, dass die Erfassung auf dem Datenträger erfolgt.

Wenn Sie nicht erkennen können, dass Protokolle lokal erfasst werden, und bereits überprüft haben, dass der Host Daten generiert, liegt vermutlich ein Konfigurationsproblem vor. Überprüfen Sie die Konfigurationseinstellungen sorgfältig.

Überprüfen Sie auch die Konfiguration, die für die MonitoringAgent-Datei „MaConfig.xml“ generiert wurde. Stellen Sie sicher, dass ein Abschnitt vorhanden ist, in dem die relevante Protokollquelle beschrieben wird. Vergewissern Sie sich anschließend, dass diese bei der Übersetzung zwischen der Diagnosekonfiguration und der Monitoring Agent-Konfiguration nicht verloren geht.

#### <a name="is-data-getting-transferred"></a>Übertragung von Daten
Führen Sie die folgenden Schritte aus, wenn Sie sich davon überzeugt haben, dass Daten zwar lokal erfasst werden, in Ihrem Speicherkonto aber immer noch nicht angezeigt werden:

- Vergewissern Sie sich, dass Sie ein richtiges Speicherkonto angegeben und für das jeweilige Speicherkonto keinen Rollover für Schlüssel durchgeführt haben. Bei Azure Cloud Services kommt es manchmal vor, dass Benutzer `useDevelopmentStorage=true` nicht aktualisieren.

- Stellen Sie sicher, dass das bereitgestellte Speicherkonto korrekt ist. Vergewissern Sie sich, dass keine Netzwerkeinschränkungen vorhanden sind, die verhindern, dass die Komponenten öffentliche Speicherendpunkte erreichen. Eine Möglichkeit besteht hierbei darin, den Remotezugriff auf den Computer durchzuführen und dann selbst Daten in dasselbe Speicherkonto zu schreiben.

- Abschließend können Sie sich ansehen, welche Fehler vom Monitoring Agent gemeldet werden. Der Monitoring Agent schreibt seine Protokolle in der Datei `maeventtable.tsf`, die sich im lokalen Speicher für Diagnosedaten befindet. Befolgen Sie die Anleitung im Abschnitt [Extraktion von lokalen Protokollen](#local-log-extraction), um diese Datei zu öffnen. Versuchen Sie anschließend zu ermitteln, ob `errors` vorhanden sind, bei denen es um das Lesen von lokalen Dateien bzw. das Schreiben in den Speicher geht.

### <a name="capturing-and-archiving-logs"></a>Erfassen und Archivieren von Protokollen
Wenn Sie sich ggf. an den Support wenden möchten, werden Sie als Erstes vermutlich gebeten, Protokolle auf Ihrem Computer zu erfassen. Sie können Zeit sparen, indem Sie dies vorab selbst durchführen. Führen Sie das Hilfsprogramm `CollectGuestLogs.exe` unter dem Pfad des Hilfsprogramms für die Protokollsammlung aus. Es wird eine ZIP-Datei mit allen relevanten Azure-Protokollen in demselben Ordner generiert.

## <a name="diagnostics-data-tables-not-found"></a>Tabellen mit Diagnosedaten nicht gefunden
Die Tabellen im Azure-Speicher, die ETW-Ereignisse enthalten, werden anhand des folgenden Codes benannt:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Beispiel:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Mit diesem Code werden vier Tabellen generiert:

| Ereignis | Tabellenname |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

## <a name="references"></a>Referenzen

### <a name="how-to-check-diagnostics-extension-configuration"></a>Überprüfen der Konfiguration der Diagnoseerweiterung
Die einfachste Möglichkeit zum Überprüfen Ihrer Erweiterungskonfiguration ist das Navigieren zum [Azure-Ressourcen-Explorer](http://resources.azure.com) und dann zu dem virtuellen Computer oder Clouddienst, unter dem sich die betreffende Azure-Diagnoseerweiterung (IaaSDiagnostics/PaaDiagnostics) befindet.

Greifen Sie alternativ dazu über Remotedesktop auf den Computer zu, und sehen Sie sich die Datei für die Azure-Diagnosekonfiguration an, die im Abschnitt zum Protokollartefaktpfad beschrieben ist.

Suchen Sie jeweils nach **Microsoft.Azure.Diagnostics** und dann nach dem Feld **xmlCfg** oder **WadCfg**.

Wenn Sie auf einem virtuellen Computer suchen und das Feld **WadCfg** vorhanden ist, bedeutet dies, dass die Konfiguration im JSON-Format vorliegt. Wenn das Feld **xmlCfg** vorhanden ist, bedeutet dies, dass die Konfigurationsdatei im XML-Format vorliegt und Base64-codiert ist. Sie müssen sie [decodieren](https://www.bing.com/search?q=base64+decoder), um die von der Diagnose geladenen XML-Daten anzuzeigen.

Wenn Sie bei der Clouddienstrolle die Konfiguration vom Datenträger auswählen, sind die Daten Base64-codiert. Sie müssen sie also [decodieren](https://www.bing.com/search?q=base64+decoder), um die XML-Daten anzuzeigen, die von der Diagnose geladen wurden.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure-Diagnose-Plug-In – Exitcodes
Das Plug-In gibt die folgenden Exitcodes zurück:

| Exitcode | BESCHREIBUNG |
| --- | --- |
| 0 |Erfolgreich. |
| -1 |Allgemeiner Fehler. |
| -2 |Die RCF-Datei konnte nicht geladen werden.<p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer falsch manuell aufgerufen wird. |
| -3 |Die Diagnosekonfigurationsdatei kann nicht geladen werden.<p><p>Lösung: Der Grund hierfür ist, dass eine Konfigurationsdatei die Schemaüberprüfung nicht bestanden hat. Gelöst werden kann der Fehler durch Bereitstellung einer Konfigurationsdatei, die dem Schema entspricht. |
| -4 |Es wird bereits eine andere Instanz des Überwachungs-Agents der Diagnose unter Verwendung des lokalen Ressourcenverzeichnisses ausgeführt.<p><p>Lösung: Geben Sie für **LocalResourceDirectory** einen anderen Wert an. |
| -6 |Das Startprogramm für das Gast-Agent-Plug-In hat versucht, die Diagnose mit einer ungültigen Befehlszeile zu starten.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer falsch manuell aufgerufen wird. |
| -10 |Das Diagnose-Plug-In wurde mit einem Ausnahmefehler beendet. |
| -11 |Der Gast-Agent konnte den für den Start und die Überwachung des Überwachungs-Agents zuständigen Prozess nicht erstellen.<p><p>Lösung: Überprüfen Sie, ob genügend Systemressourcen zum Starten neuer Prozesse verfügbar sind.<p> |
| -101 |Ungültige Argumente beim Aufrufen des Diagnose-Plug-Ins.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer falsch manuell aufgerufen wird. |
| -102 |Der Plug-In-Prozess kann sich nicht selbst initialisieren.<p><p>Lösung: Überprüfen Sie, ob genügend Systemressourcen zum Starten neuer Prozesse verfügbar sind. |
| -103 |Der Plug-In-Prozess kann sich nicht selbst initialisieren. Insbesondere kann das Protokollierungsobjekt nicht erstellt werden.<p><p>Lösung: Überprüfen Sie, ob genügend Systemressourcen zum Starten neuer Prozesse verfügbar sind. |
| -104 |Die vom Gast-Agent bereitgestellte RCF-Datei konnte nicht geladen werden.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer falsch manuell aufgerufen wird. |
| -105 |Das Diagnose-Plug-In kann die Diagnosekonfigurationsdatei nicht öffnen.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Diagnose-Plug-In auf dem virtuellen Computer falsch manuell aufgerufen wird. |
| -106 |Die Diagnosekonfigurationsdatei kann nicht gelesen werden.<p><p>Der Grund hierfür ist, dass eine Konfigurationsdatei die Schemaüberprüfung nicht bestanden hat. <br><br>Lösung: Stellen Sie eine Konfigurationsdatei bereit, die die Anforderungen des Schemas erfüllt. Weitere Informationen finden Sie unter [Überprüfen der Konfiguration der Diagnoseerweiterung](#how-to-check-diagnostics-extension-configuration). |
| -107 |Das an den Überwachungs-Agent übergebene Ressourcenverzeichnis ist ungültig.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn der Überwachungs-Agent auf dem virtuellen Computer falsch manuell aufgerufen wird.</p> |
| -108 |Die Diagnosekonfigurationsdatei kann nicht in die Konfigurationsdatei des Überwachungs-Agents konvertiert werden.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Diagnose-Plug-In manuell mit einer ungültigen Konfigurationsdatei aufgerufen wird. |
| -110 |Allgemeiner Fehler in der Diagnosekonfiguration.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Diagnose-Plug-In manuell mit einer ungültigen Konfigurationsdatei aufgerufen wird. |
| -111 |Der Überwachungs-Agent kann nicht gestartet werden.<p><p>Lösung: Überprüfen Sie, ob genügend Systemressourcen verfügbar sind. |
| -112 |Allgemeiner Fehler |

### <a name="local-log-extraction"></a>Extraktion von lokalen Protokollen
Der Überwachungs-Agent sammelt Protokolle und Artefakte in Form von `.tsf`-Dateien. Die `.tsf`-Datei ist nicht lesbar, aber Sie können sie wie folgt in eine `.csv`-Datei konvertieren:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Eine neue Datei mit dem Namen `<relevantLogFile>.csv` wird unter demselben Pfad wie die entsprechende `.tsf`-Datei erstellt.

>[!NOTE]
> Sie müssen dieses Hilfsprogramm nur für die TSF-Hauptdatei (z.B. „PerformanceCountersTable.tsf“) ausführen. Die dazugehörigen Dateien (z.B. „PerformanceCountersTables_\*\*001.tsf“, „PerformanceCountersTables_\*\*002.tsf“ usw.) werden automatisch verarbeitet.

### <a name="more-about-missing-trace-logs"></a>Weitere Informationen zu fehlenden Ablaufverfolgungsprotokollen

>[!NOTE]
> Die folgenden Informationen gelten hauptsächlich für Azure Cloud Services, sofern Sie nicht das DiagnosticsMonitorTraceListener-Element für eine Anwendung konfiguriert haben, die auf Ihrer IaaS-VM ausgeführt wird.

- Stellen Sie sicher, dass das **DiagnosticMonitorTraceListener**-Element in „web.config“ oder „app.config“ konfiguriert wurde.  In Clouddienstprojekten ist es standardmäßig konfiguriert. Einige Kunden kommentieren das Element aber aus, und dies führt dazu, dass die Überwachungsanweisungen von der Diagnose nicht gesammelt werden.

- Falls die Protokolle nicht mit der **OnStart**- oder **Run**-Methode geschrieben werden, sollten Sie sicherstellen, dass das **DiagnosticMonitorTraceListener**-Element in „app.config“ enthalten ist.  Standardmäßig ist es in der Datei „web.config“ enthalten, aber dies gilt nur für Code, der in „w3wp.exe“ ausgeführt wird. Sie benötigen es in „app.config“, um Ablaufverfolgungen zu erfassen, die in „WaIISHost.exe“ ausgeführt werden.

- Vergewissern Sie sich, dass Sie **Diagnostics.Trace.TraceXXX** anstelle von **Diagnostics.Debug.WriteXXX** verwenden. Die Debuganweisungen werden aus einem Versionsbuild entfernt.

- Stellen Sie sicher, dass der kompilierte Code über die **Diagnostics.Trace-Zeilen** verfügt (Reflector, ildasm oder ILSpy für die Überprüfung verwenden). **Diagnostics.Trace**-Befehle werden aus der kompilierten Binärdatei entfernt, sofern Sie nicht das Symbol für die bedingte TRACE-Kompilierung verwenden. Dies ist ein häufiges Problem, das auftritt, wenn Sie msbuild zum Erstellen eines Projekts verwenden.   

## <a name="known-issues-and-mitigations"></a>Bekannte Probleme und Lösungen
Die folgende Liste enthält bekannte Probleme und Lösungen:

**1. .NET 4.5-Abhängigkeit**

Die Windows Azure-Diagnoseerweiterung verfügt über eine Laufzeitabhängigkeit von .NET 4.5 Framework oder höher. Zum Zeitpunkt der Abfassung dieses Artikels ist für alle Computer, die für Azure Cloud Services bereitgestellt werden, sowie für alle Images, die auf virtuellen Azure-Computern basieren, .NET 4.5 oder höher installiert.

Es kann trotzdem zu einer Situation kommen, in der Sie versuchen, die Windows Azure-Diagnoseerweiterung auf einem Computer auszuführen, der nicht über .NET 4.5 oder höher verfügt. Dies passiert, wenn Sie Ihren Computer aus einem alten Image oder einer alten Momentaufnahme erstellen oder einen eigenen benutzerdefinierten Datenträger verwenden.

Hierbei tritt im Allgemeinen der Exitcode **255** auf, wenn **DiagnosticsPluginLauncher.exe** ausgeführt wird. Fehler aufgrund des folgenden Ausnahmefehlers:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Lösung:** Installieren Sie .NET 4.5 oder höher auf Ihrem Computer.

**2. Daten von Leistungsindikatoren sind im Speicher verfügbar, werden aber nicht im Portal angezeigt**

Auf der Portaloberfläche auf den virtuellen Computern werden bestimmte Leistungsindikatoren standardmäßig angezeigt. Überprüfen Sie Folgendes, wenn die Leistungsindikatoren nicht angezeigt werden und Sie sicher sind, dass die Daten generiert werden, weil sie im Speicher verfügbar sind:

- Haben die Daten im Speicher englische Indikatornamen? Wenn die Indikatornamen nicht auf Englisch sind, können sie vom Portalmetrikdiagramm nicht erkannt werden. **Vorbeugende Maßnahme**: Ändern Sie die Sprache des Computers für Systemkonten in Englisch. Wählen Sie hierzu **Systemsteuerung** > **Region** > **Verwaltung** > **Einstellungen kopieren**. Deaktivieren Sie als Nächstes die Option **Willkommensseite und Systemkonten**, damit die benutzerdefinierte Sprache nicht auf das Systemkonto angewendet wird.

- Wenn Sie in den Namen Ihrer Leistungsindikatoren Platzhalter (\*) verwenden, ist es für das Portal nicht möglich, den konfigurierten und erfassten Indikator zu korrelieren, wenn die Leistungsindikatoren an die Azure Storage-Senke gesendet werden. **Vorbeugende Maßnahme**: Um sicherzustellen, dass Sie Platzhalter verwenden können und das Portal zudem den (\*) erweitert, leiten Sie Ihre Leistungsindikatoren an die [„Azure Monitor“-Senke](diagnostics-extension-schema.md#diagnostics-extension-111) weiter.

