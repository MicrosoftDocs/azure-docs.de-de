---
title: 'Aktivieren der Diagnoseprotokollierung für Apps: Azure App Service'
description: Erfahren Sie, wie Sie die Diagnoseprotokollierung aktivieren und Instrumentierung zu Ihrer Anwendung hinzufügen und wie Sie auf die von Azure protokollierten Informationen zugreifen.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c21a923f06a768c0a9a0f2843a24583df7a7821d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059652"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service
## <a name="overview"></a>Übersicht
Azure bietet integrierte Diagnosefunktionen zur Unterstützung beim Debuggen einer [App Service-App](https://go.microsoft.com/fwlink/?LinkId=529714). In diesem Artikel erfahren Sie, wie Sie die Diagnoseprotokollierung aktivieren und Ihrer Anwendung Instrumentierung hinzufügen und wie Sie die von Azure protokollierten Informationen abrufen.

In diesem Artikel werden das [Azure-Portal](https://portal.azure.com) und die Azure-Befehlszeilenschnittstelle (Azure CLI) verwendet, um mit Diagnoseprotokollen zu arbeiten. Informationen zum Arbeiten mit Diagnoseprotokollen in Visual Studio finden Sie unter [Problembehandlung von Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Webserver- und Anwendungsdiagnose
App Service bietet Diagnosefunktionen zum Protokollieren von Informationen sowohl über den Webserver als auch über die Webanwendung. Diese sind logisch in **Webserverdiagnose** und **Anwendungsdiagnose** unterteilt.

### <a name="web-server-diagnostics"></a>Webserverdiagnose
Sie können die folgenden Protokollarten aktivieren oder deaktivieren:

* **Detaillierte Fehlerprotokollierung** – Detaillierte Informationen für jede Anforderung, die zum HTTP-Statuscode 400 oder höher führt. Diese können Informationen enthalten, mit deren Hilfe sich bestimmen lässt, warum der Server den Fehlercode zurückgegeben hat. Für jeden Fehler im Dateisystem der App wird eine einzige HTML-Datei generiert, und bis zu 50 Fehler (Dateien) werden beibehalten. Wenn die Anzahl der HTML-Dateien 50 überschreitet, werden die 26 ältesten Dateien automatisch gelöscht.
* **Ablaufverfolgung von Anforderungsfehlern** - Detaillierte Informationen zu fehlgeschlagenen Anforderungen, einschließlich der Verfolgung von IIS-Komponenten, die zur Verarbeitung der Anforderung verwendet wurden, sowie die in jeder Komponente benötigte Zeit. Dies ist hilfreich, wenn Sie die Leistung der Website verbessern oder einen bestimmten HTTP-Fehler isolieren möchten. Für jeden Fehler im Dateisystem der App wird ein einziger Ordner generiert. Die Beibehaltungsrichtlinien für Dateien sind mit denen der vorstehenden detaillierten Fehlerprotokollierung identisch.
* **Webserverprotokollierung** - Informationen über HTTP-Transaktionen im erweiterten [W3C-Protokolldateiformat](/windows/desktop/Http/w3c-logging). Dies ist hilfreich, wenn Sie allgemeine Websitemetriken ermitteln möchten, z.B. die Anzahl der verarbeiteten Anforderungen oder die Anzahl der Anforderungen von einer bestimmten IP-Adresse.

### <a name="application-diagnostics"></a>Anwendungsdiagnose
Mit der Option "Application Diagnostics" können Sie die von einer Webanwendung erzeugten Informationen erfassen. ASP.NET-Anwendungen können die Klasse [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) verwenden, um Informationen im Anwendungs-Diagnoseprotokoll aufzuzeichnen. Beispiel:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Sie können diese Protokolle zur Laufzeit abrufen und bei der Problembehandlung nutzen. Weitere Informationen finden Sie unter [Problembehandlung von Apps in Azure App Service mit Visual Studio](troubleshoot-dotnet-visual-studio.md).

App Service protokolliert auch Bereitstellungsinformationen, wenn Sie Inhalte in einer App veröffentlichen. Dies erfolgt automatisch, und es gibt keine Konfigurationseinstellung für die Bereitstellungsprotokollierung. Anhand der Bereitstellungsprotokollierung können Sie bestimmen, warum eine Bereitstellung fehlgeschlagen ist. Wenn Sie beispielsweise ein benutzerdefiniertes Bereitstellungsskript verwenden, können Sie die Bereitstellungsprotokollierung verwenden, um festzustellen, warum beim Skript ein Fehler aufgetreten ist.

## <a name="enablediag"></a>Aktivieren der Diagnose
Navigieren Sie zur Seite für Ihre App und klicken auf **Einstellungen > Diagnoseprotokolle**, um die Diagnose im [Azure-Portal](https://portal.azure.com) zu aktivieren.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Bereich „Protokolle“](./media/web-sites-enable-diagnostic-log/logspart.png)

Wenn Sie die **Anwendungsdiagnose** aktivieren, können Sie auch die **Ebene** auswählen. Die folgende Tabelle zeigt die Kategorien von Protokollen auf den einzelnen Ebenen:

| Level| Enthaltene Protokollkategorien |
|-|-|
|**Disabled** | Keine |
|**Fehler** | Fehler, Kritisch |
|**Warning** | Warnung, Fehler, Kritisch|
|**Informationen** | Info, Warnung, Fehler, Kritisch|
|**Ausführlich** | Ablaufverfolgung, Debugging, Info, Warnung, Fehler, Kritisch (alle Kategorien) |
|-|-|

Unter **Anwendungsprotokollierung** können Sie zum Debuggen vorübergehend die Dateisystemoption aktivieren. Diese Option wird in 12 Stunden automatisch deaktiviert. Sie können auch die Blob Storage-Option aktivieren, um einen Blobcontainer auszuwählen, in den Protokolle geschrieben werden sollen.

> [!NOTE]
> Zurzeit können nur .NET-Anwendungsprotokolle in den Blobspeicher geschrieben werden. Anwendungsprotokoll für Java, PHP, Node.js, Python können nur im Dateisystem gespeichert werden (ohne Codeänderungen, um Protokolle in externen Speicher zu schreiben).
>
>

Unter **Webserverprotokollierung** können Sie **Speicher** oder **Dateisystem** auswählen. Durch Auswahl von **storage** können Sie ein Speicherkonto festlegen und dann einen Blob-Container hinzufügen, in den die Protokolle geschrieben werden. 

Wenn Sie Protokolle im Dateisystem speichern, können Sie über FTP auf diese Dateien zugreifen oder die Dateien mithilfe der Azure CLI als ZIP-Archiv herunterladen.

Protokolle werden standardmäßig nicht automatisch gelöscht (ausgenommen bei der Option **Anwendungsprotokollierung (Dateisystem)** ). Damit Protokolle automatisch gelöscht werden, legen Sie das Feld **Beibehaltungsdauer (Tage)** fest.

> [!NOTE]
> Wenn Sie [den Zugriffsschlüssel für Ihr Speicherkonto neu generieren](../storage/common/storage-create-storage-account.md), müssen Sie die jeweilige Protokollierungskonfiguration zur Verwendung der aktualisierten Schlüssel zurücksetzen. Gehen Sie dazu folgendermaßen vor:
>
> 1. Stellen Sie auf der Registerkarte **Konfigurieren** das jeweilige Protokollierungsfeature auf **Aus**. Speichern Sie die Einstellungen.
> 2. Aktivieren Sie die Protokollierung im Speicherkontoblob erneut. Speichern Sie die Einstellungen.
>
>

Sie können gleichzeitig eine beliebige Kombination aus Speicherung im Dateisystem und in Blob Storage aktivieren und individuelle Protokollierungsebenen konfigurieren. Sie können beispielsweise Fehler und Warnmeldungen als langfristige Protokollierungslösung im Blob-Speicher protokollieren, während die Dateisystemprotokollierung ausführlich erfolgt.

Beide Speicherorte bieten dieselben grundlegenden Informationen zu protokollierten Ereignissen. **Blob Storage**-Protokolle zeichnen jedoch im Vergleich zur Protokollierung im **Dateisystem** mehr Informationen auf, beispielsweise die Instanz-ID, die Thread-ID und einen ausführlicheren Zeitstempel (Taktformat).

> [!NOTE]
> In **Blob Storage** gespeicherte Informationen können nur über Storage-Clients oder Anwendungen aufgerufen werden, die direkt mit diesen Speichersystemen arbeiten können. Beispielsweise enthält Visual Studio 2013 einen Speicher-Explorer, mit dem Blob Storage erkundet werden kann, und HDInsight kann auf die in Blob Storage gespeicherten Daten zugreifen. Sie können auch mithilfe der [Azure SDKs](https://azure.microsoft.com/downloads/)eine Anwendung schreiben, die auf den Azure-Speicher zugreift.
>

## <a name="download">Vorgehensweise:</a> Herunterladen von Protokollen
Im Dateisystem der App gespeicherte Diagnoseinformationen können direkt über das FTP aufgerufen werden. Sie können auch mithilfe der Azure CLI als ZIP-Archiv heruntergeladen werden.

Protokolle werden in der folgenden Verzeichnisstruktur gespeichert:

* **Anwendungsprotokolle**: /LogFiles/Application/. Dieser Ordner enthält eine oder mehrere Textdateien mit Informationen, die bei der Anwendungsprotokollierung erzeugt wurden.
* **Protokolle für fehlgeschlagene Anforderungen** - /LogFiles/W3SVC#########/. Dieser Ordner enthält eine XSL-Datei und eine oder mehrere XML-Dateien. Vergewissern Sie sich, dass Sie die XSL-Datei in dasselbe Verzeichnis wie die XML-Datei(en) herunterladen, da die XSL-Datei die Funktionalität zum Formatieren und Filtern des Inhalts der XML-Datei(en) zur Anzeige in Internet Explorer zur Verfügung stellt.
* **Detaillierte Fehlerprotokolle**: /LogFiles/DetailedErrors/. Dieser Ordner enthält eine oder mehrere HTM-Dateien, die umfangreiche Informationen zu aufgetretenen HTTP-Fehlern bereitstellen.
* **Webserverprotokolle**: /LogFiles/http/RawLogs. Dieser Ordner enthält eine oder mehrere Textdateien im [erweiterten W3C-Protokolldateiformat](/windows/desktop/Http/w3c-logging).
* **Bereitstellungsprotokolle**: /LogFiles/Git. Dieser Ordner enthält Protokolle, die von den internen Bereitstellungsprozessen von Azure App Service erzeugt werden, sowie Protokolle für Git-Bereitstellungen. Bereitstellungsprotokolle können sich auch unter „D:\home\site\deployments“ befinden.

### <a name="ftp"></a>FTP

Informationen zum Öffnen einer FTP-Verbindung mit dem FTP-Server Ihrer App finden Sie unter [Bereitstellen der App in Azure App Service mithilfe von FTP/S](deploy-ftp.md).

Wenn die Verbindung mit dem FTP/S-Server Ihrer App hergestellt wurde, öffnen Sie den Ordner **LogFiles**, in dem die Protokolldateien gespeichert sind.

### <a name="download-with-azure-cli"></a>Herunterladen mit der Azure-CLI
Zum Herunterladen der Protokolldateien mit der Azure-Befehlszeilenschnittstelle öffnen Sie eine neue Eingabeaufforderung, PowerShell-, Bash- oder Terminalsitzung, und geben Sie den folgenden Befehl ein:

    az webapp log download --resource-group resourcegroupname --name appname

Mit diesem Befehl werden die Protokolle für die App mit dem Namen „appname“ in der Datei **webapp_logs.zip** im aktuellen Verzeichnis gespeichert.

> [!NOTE]
> Wenn Sie die Azure-Befehlszeilenschnittstelle nicht installiert oder nicht zur Verwendung Ihres Azure-Abonnements konfiguriert haben, gehen Sie wie unter [Verwenden der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) beschrieben vor.
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Gewusst wie: Anzeigen von Protokollen in Application Insights
Visual Studio Application Insights bietet Tools zum Filtern und Suchen von Protokollen sowie zu Korrelation der Protokolle mit Anforderungen und anderen Ereignissen.

1. Fügen Sie Ihrem Projekt in Visual Studio Application Insights SDK hinzu.
   * Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights hinzufügen“ aus. Auf der Benutzeroberfläche werden Sie durch Schritte geführt, zu denen auch die Erstellung einer Application Insights-Ressource gehört. [Weitere Informationen](../azure-monitor/app/asp-net.md)
2. Fügen Sie Ihrem Projekt das Ablaufverfolgungs-Listener-Paket hinzu.
   * Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie „NuGet-Pakete verwalten“. Falls die Protokolle automatisch gelöscht werden sollen, wählen Sie `Microsoft.ApplicationInsights.TraceListener` [Weitere Informationen](../azure-monitor/app/asp-net-trace-logs.md)
3. Laden Sie das Projekt hoch, und führen sie es zum Generieren von Protokolldaten aus.
4. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu der neuen Application Insights-Ressource, und öffnen Sie die Option **Suche**. Ihre Protokolldaten sollten zusammen mit der Anforderung, Nutzungsdaten und sonstigen Telemetriedaten angezeigt werden. Einige Telemetrie kann einige Minuten in Anspruch nehmen, bis sie angezeigt wird: Klicken Sie auf "Aktualisieren". [Weitere Informationen](../azure-monitor/app/diagnostic-search.md)

[Erfahren Sie mehr über die Leistungsüberwachung mit Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs">Vorgehensweise:</a> Streaming von Protokollen
Beim Entwickeln einer Anwendung ist es häufig nützlich, Protokollinformationen nahezu in Echtzeit zu sehen. Sie können Protokollierungsinformationen mithilfe der Azure CLI in Ihre Entwicklungsumgebung streamen.

> [!NOTE]
> Einige Protokolltypen puffern die Schreibvorgänge in die Protokolldatei, was zu Störereignissen im Stream führen kann. Beispielsweise kann ein Anwendungsprotokolleintrag für den Besuch einer Seite im Stream vor dem zugehörigen HTTP-Protokolleintrag für die Seitenanforderung angezeigt werden.
>
> [!NOTE]
> Beim Protokollstreaming werden auch Informationen gestreamt, die in eine Textdatei im Ordner **D:\\home\\LogFiles\\** geschrieben werden.
>
>

### <a name="streaming-with-azure-cli"></a>Streamen mit der Azure CLI
Zum Streaming der Protokollinformationen öffnen Sie eine neue Eingabeaufforderung, PowerShell, Bash- oder Terminalsitzung und geben den folgenden Befehl ein:

    az webapp log tail --name appname --resource-group myResourceGroup

Mit diesem Befehl wird eine Verbindung zu der App „appname“ hergestellt, und das Streaming von Informationen in das Fenster wird gestartet, sobald Protokollereignisse in der App auftreten. Alle Informationen, die in Dateien mit der Erweiterung TXT, LOG oder HTM im Verzeichnis „/LogFiles“ (d:/home/logfiles) geschrieben werden, werden an die lokale Konsole gestreamt.

Um bestimmte Ereignisse wie beispielsweise Fehler zu filtern, verwenden Sie den Parameter **--Filter** . Beispiel:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Um bestimmte Protokolltypen wie HTTP zu filtern, verwenden Sie den Parameter **--Path** . Beispiel:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Wenn Sie die Azure-Befehlszeilenschnittstelle nicht installiert oder nicht zur Verwendung Ihres Azure-Abonnements konfiguriert haben, gehen Sie wie unter [Verwenden der Azure-Befehlszeilenschnittstelle](../cli-install-nodejs.md) beschrieben vor.
>
>

## <a name="understandlogs">Vorgehensweise:</a> Verstehen von Diagnoseprotokollen
### <a name="application-diagnostics-logs"></a>Anwendungsdiagnoseprotokolle
Die Anwendungsdiagnose speichert Informationen in einem spezifischen Format für .NET-Anwendungen, je nachdem, ob Sie Protokolle im Dateisystem oder in Blob Storage speichern. 

Die grundlegenden Daten, die gespeichert werden, sind für beide Speichertypen gleich: Datum und Uhrzeit des Ereignisses, Prozess-ID, die das Ereignis erzeugt hat, sowie Ereignistyp (Info, Warnung, Fehler) und Ereignismeldung. Die Verwendung des Dateisystems zum Speichern von Protokollen ist nützlich, wenn Sie zum Beheben eines Problems sofortigen Zugriff auf die Protokolle benötigen, da die Protokolldateien nahezu sofort aktualisiert werden. Blob Storage wird zu Archivierungszwecken eingesetzt, da die Dateien zwischengespeichert und dann nach einem Zeitplan in den Speichercontainer geleert werden.

**Dateisystem**

Jede im Dateisystem protokollierte oder per Streaming empfangene Zeile hat das folgende Format:

    {Date}  PID[{process ID}] {event type/level} {message}

Beispielsweise würde ein Fehlerereignis in etwa wie im folgenden Beispiel aussehen:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Bei der Protokollierung im Dateisystem erhalten Sie nur die grundlegendsten Informationen der drei verfügbaren Methoden, nämlich Zeit, Prozess-ID, Ereignisebene und Meldung.

**Blob Storage**

Bei der Protokollierung in einem Blob-Speicher werden die Daten im CSV-Format (durch Kommas getrennte Werte) gespeichert. Zusätzliche Felder werden protokolliert, um ausführlichere Informationen zum Ereignis bereitzustellen. Die folgenden Eigenschaften werden für jede Zeile in der CSV-Datei angegeben:

| Eigenschaftenname | Wert/Format |
| --- | --- |
| Date |Datum und Zeit des Auftretens des Ereignisses |
| Level |Ereignisebene (z.B. Fehler, Warnung, Information) |
| ApplicationName |Der App-Name |
| InstanceId |Die Instanz der App, in der das Ereignis auftrat |
| EventTickCount |Datum und Zeit des Auftretens des Ereignisses im Tick-Format (höhere Präzision) |
| EventId |Die Ereignis-ID<p><p>Bei fehlender Angabe standardmäßig 0 |
| Pid |Prozess-ID |
| Tid |Thread-ID des Threads, der das Ereignis erzeugt hat |
| `Message` |Meldung zu den Ereignisdetails |

In einem Blob gespeicherte Daten sehen in etwa wie im folgenden Beispiel aus:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Für ASP.NET Core erfolgt die Protokollierung mit dem Anbieter [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices), der zusätzliche Protokolldateien im Blobcontainer ablegt. Weitere Informationen finden Sie unter [Protokollierung in ASP.NET Core](/aspnet/core/fundamentals/logging).
>
>

### <a name="failed-request-traces"></a>Protokolle für fehlgeschlagene Anforderungen
Die Verfolgungsprotokolle für fehlerhafte Anforderungen werden in XML-Dateien mit dem Namen **fr######.xml** gespeichert. Um das Anzeigen der protokollierten Informationen zu erleichtern, wird ein XSL-Stylesheet namens **freb.xsl** im selben Verzeichnis wie die XML-Dateien bereitgestellt. Wenn Sie eine der XML-Dateien in Internet Explorer öffnen, wird das XSL-Stylesheet in Internet Explorer verwendet, um eine formatierte Ansicht mit den Ablaufverfolgungsinformationen ähnlich wie im folgenden Beispiel anzuzeigen:

![Anzeige fehlgeschlagener Anforderungen im Browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> Eine einfache Möglichkeit zum Anzeigen der formatierten Protokolle für fehlgeschlagene Anforderungen besteht darin, im Portal zur Seite Ihrer App zu navigieren. Wählen Sie im linken Menü **Diagnose und Problembehandlung** aus, und suchen Sie nach **Ablaufverfolgungsprotokolle für Anforderungsfehler**. Klicken Sie dann auf das Symbol, um die gewünschte Ablaufverfolgung zu durchsuchen und anzuzeigen.
>

### <a name="detailed-error-logs"></a>Detaillierte Fehlerprotokolle
Detaillierte Fehlerprotokolle sind HTML-Dokumente, die ausführlichere Informationen zu aufgetretenen HTTP-Fehlern bereitstellen. Da es sich um einfache HTML-Dokumente handelt, können sie in einem Webbrowser angezeigt werden.

### <a name="web-server-logs"></a>Webserverprotokolle
Webserverprotokolle werden im [erweiterten W3C-Protokolldateiformat](/windows/desktop/Http/w3c-logging)gespeichert. Die Informationen können in einem Texteditor gelesen oder mit einem Dienstprogramm wie [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619)analysiert werden.

> [!NOTE]
> Die von Azure App Service erzeugten Protokolle unterstützen nicht die Felder **s-computername**, **s-ip** oder **cs-version**.
>
>

## <a name="nextsteps"></a> Nächste Schritte
* [How to Monitor Azure App Service (Vorgehensweise: Überwachen von Azure App Service)](web-sites-monitor.md)
* [Troubleshooting Azure App Service in Visual Studio (Problembehandlung für Azure App Service in Visual Studio)](troubleshoot-dotnet-visual-studio.md)
* [Analyze app Logs in HDInsight (Analyse von App-Protokollen in HDInsight)](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
