---
title: Aktivieren der Diagnoseprotokollierung
description: Erfahren Sie, wie Sie die Diagnoseprotokollierung aktivieren und Instrumentierung zu Ihrer Anwendung hinzufügen und wie Sie auf die von Azure protokollierten Informationen zugreifen.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
ms.openlocfilehash: 99a3c9a9c26eebe8dfdf11baf718fd13f7539607
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025275"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service
## <a name="overview"></a>Übersicht
Azure bietet integrierte Diagnosefunktionen zur Unterstützung beim Debuggen einer [App Service-App](overview.md). In diesem Artikel erfahren Sie, wie Sie die Diagnoseprotokollierung aktivieren und Ihrer Anwendung Instrumentierung hinzufügen und wie Sie die von Azure protokollierten Informationen abrufen.

In diesem Artikel werden das [Azure-Portal](https://portal.azure.com) und die Azure-Befehlszeilenschnittstelle (Azure CLI) verwendet, um mit Diagnoseprotokollen zu arbeiten. Informationen zum Arbeiten mit Diagnoseprotokollen in Visual Studio finden Sie unter [Problembehandlung von Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Zusätzlich zu den Protokollierungsanweisungen in diesem Artikel gibt es eine neue, integrierte Protokollierungsfunktion mit Azure Monitor. Weitere Informationen zu dieser Funktion finden Sie im Abschnitt [Senden von Protokollen an Azure Monitor (Vorschauversion)](#send-logs-to-azure-monitor-preview). 
>
>

|type|Plattform|Standort|BESCHREIBUNG|
|-|-|-|-|
| Anwendungsprotokollierung | Windows, Linux | App Service-Dateisystem und/oder Azure Storage-Blobs | Protokolliert Meldungen, die von Ihrem Anwendungscode generiert werden. Die Meldungen können durch das von Ihnen ausgewählte Webframework oder direkt aus Ihrem Anwendungscode mithilfe des Standardprotokollierungsmusters Ihrer Sprache generiert werden. Jede Meldung wird einer der folgenden Kategorien zugewiesen: **Critical (Kritisch)** , **Error (Fehler)** , **Warning (Warnung)** , **Info (Information)** , **Debug (Debuggen)** und **Trace (Ablaufverfolgung)** . Sie können auswählen, wie ausführlich die Protokollierung erfolgen soll, indem Sie den Schweregrad beim Aktivieren der Anwendungsprotokollierung festlegen.|
| Webserverprotokollierung| Windows | App Service-Dateisystem oder Azure Storage-Blobs| Unformatierte HTTP-Anforderungsdaten im [erweiterten W3C-Protokolldateiformat](/windows/desktop/Http/w3c-logging). Jede Protokollmeldung enthält Daten, etwa die HTTP-Methode, den Ressourcen-URI, die Client-IP, den Clientport, den Benutzer-Agent, den Antwortcode usw. |
| Detaillierte Fehlermeldungen| Windows | App Service-Dateisystem | Kopien der *HTM*-Fehlerseiten, die an den Clientbrowser gesendet wurden. Aus Sicherheitsgründen sollten ausführliche Fehlerseiten nicht an Clients in der Produktionsumgebung gesendet werden, aber App Service kann die Fehlerseite bei jedem Auftreten eines Anwendungsfehlers speichern, der HTTP-Code 400 oder höher aufweist. Die Seite kann Informationen enthalten, mit deren Hilfe sich bestimmen lässt, warum der Server den Fehlercode zurückgegeben hat. |
| Ablaufverfolgung fehlgeschlagener Anforderungen | Windows | App Service-Dateisystem | Detaillierte Ablaufverfolgungsinformationen zu fehlgeschlagenen Anforderungen, einschließlich der Ablaufverfolgung von IIS-Komponenten, die zur Verarbeitung der Anforderung verwendet wurden, sowie die in jeder Komponente benötigte Zeit. Dies ist hilfreich, wenn Sie die Leistung der Website verbessern oder einen bestimmten HTTP-Fehler isolieren möchten. Für jede fehlerhafte Anforderung, die die XML-Protokolldatei enthält, wird ein Ordner sowie das XSL-Stylesheet generiert, mit dem die Protokolldatei angezeigt werden kann. |
| Bereitstellungsprotokollierung | Windows, Linux | App Service-Dateisystem | Protokolle zur Veröffentlichung von Inhalten für eine App. Bereitstellungsprotokollierung erfolgt automatisch, und es gibt keine konfigurierbaren Einstellungen für die Bereitstellungsprotokollierung. Sie unterstützt Sie bei der Ermittlung, warum eine Bereitstellung fehlgeschlagen ist. Wenn Sie beispielsweise ein [benutzerdefiniertes Bereitstellungsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) verwenden, können Sie die Bereitstellungsprotokollierung nutzen, um festzustellen, warum das Skript fehlschlägt. |

> [!NOTE]
> App Service bietet ein dediziertes, interaktives Diagnosetool, das Sie bei der Problembehandlung Ihrer Anwendung unterstützt. Weitere Informationen hierzu finden Sie unter [Azure App Service-Pläne – Diagnoseübersicht](overview-diagnostics.md).
>
> Außerdem können Sie andere Azure-Dienste verwenden, um die Protokollierungs- und Überwachungsfunktionen Ihrer App zu verbessern, z.B. [Azure Monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Aktivieren der Anwendungsprotokollierung (Windows)

> [!NOTE]
> Bei der Anwendungsprotokollierung für Blob Storage können nur Speicherkonten in derselben Region wie App Service verwendet werden.

Um Anwendungsprotokollierung für Windows-Apps im [Azure-Portal](https://portal.azure.com) zu aktivieren, navigieren Sie zu Ihrer App und wählen **App Service-Protokolle** aus.

Wählen Sie für **Anwendungsprotokollierung (Dateisystem)** oder **Anwendungsprotokollierung (Blob)** oder beides die Option **Ein** aus. 

Die Option **Dateisystem** ist für das temporäre Debuggen bestimmt und schaltet sich nach 12 Stunden aus. Die Option **Blob** ist für die langfristige Protokollierung vorgesehen und benötigt einen Blobspeichercontainer, in den Protokolle geschrieben werden.  Die Option **Blob** enthält auch zusätzliche Informationen in den Protokollmeldungen, z.B. die ID der ursprünglichen VM-Instanz der Protokollmeldung (`InstanceId`), die Thread-ID (`Tid`) und einen genaueren Zeitstempel ([`EventTickCount`](/dotnet/api/system.datetime.ticks)).

> [!NOTE]
> Zurzeit können nur .NET-Anwendungsprotokolle in den Blobspeicher geschrieben werden. Anwendungsprotokoll für Java, PHP, Node.js, Python können nur im App Service-Dateisystem gespeichert werden (ohne Codeänderungen, um Protokolle in externen Speicher zu schreiben).
>
> Wenn Sie [den Zugriffsschlüssel für Ihr Speicherkonto neu generieren](../storage/common/storage-account-create.md), müssen Sie außerdem die jeweilige Protokollierungskonfiguration zur Verwendung der aktualisierten Zugriffsschlüssel zurücksetzen. Gehen Sie dazu folgendermaßen vor:
>
> 1. Stellen Sie auf der Registerkarte **Konfigurieren** das jeweilige Protokollierungsfeature auf **Aus**. Speichern Sie die Einstellungen.
> 2. Aktivieren Sie die Protokollierung im Speicherkontoblob erneut. Speichern Sie die Einstellungen.
>
>

Wählen Sie die **Ebene** oder die zu protokollierende Detailebene aus. Die folgende Tabelle zeigt die Protokollkategorien der einzelnen Ebenen:

| Ebene | Enthaltene Kategorien |
|-|-|
|**Disabled** | Keine |
|**Fehler** | Fehler, Kritisch |
|**Warning** | Warnung, Fehler, Kritisch|
|**Informationen** | Info, Warnung, Fehler, Kritisch|
|**Ausführlich** | Ablaufverfolgung, Debugging, Info, Warnung, Fehler, Kritisch (alle Kategorien) |

Wenn Sie fertig sind, wählen Sie **Speichern** aus.

## <a name="enable-application-logging-linuxcontainer"></a>Aktivieren der Anwendungsprotokollierung (Linux/Container)

Um Anwendungsprotokollierung für Linux-Apps oder benutzerdefinierte Container-Apps im [Azure-Portal](https://portal.azure.com) zu aktivieren, navigieren Sie zu Ihrer App und wählen **App Service-Protokolle** aus.

Wählen Sie unter **Anwendungsprotokollierung** die Option **Dateisystem** aus.

Geben Sie unter **Kontingent (MB)** das Datenträgerkontingent für die Anwendungsprotokolle an. Legen Sie unter **Aufbewahrungszeitraum (Tage)** die Anzahl der Tage fest, die die Protokolle aufbewahrt werden sollen.

Wenn Sie fertig sind, wählen Sie **Speichern** aus.

## <a name="enable-web-server-logging"></a>Aktivieren der Webserverprotokollierung

Um Webserverprotokollierung für Windows-Apps im [Azure-Portal](https://portal.azure.com) zu aktivieren, navigieren Sie zu Ihrer App und wählen **App Service-Protokolle** aus.

Wählen Sie **Webserverprotokollierung** aus, wählen Sie **Speicher** aus, um Protokolle im Blobspeicher zu speichern, oder **Dateisystem**, um Protokolle im App Service-Dateisystem zu speichern. 

Legen Sie unter **Aufbewahrungszeitraum (Tage)** die Anzahl der Tage fest, die die Protokolle aufbewahrt werden sollen.

> [!NOTE]
> Wenn Sie [den Zugriffsschlüssel für Ihr Speicherkonto neu generieren](../storage/common/storage-account-create.md), müssen Sie die jeweilige Protokollierungskonfiguration zur Verwendung der aktualisierten Schlüssel zurücksetzen. Gehen Sie dazu folgendermaßen vor:
>
> 1. Stellen Sie auf der Registerkarte **Konfigurieren** das jeweilige Protokollierungsfeature auf **Aus**. Speichern Sie die Einstellungen.
> 2. Aktivieren Sie die Protokollierung im Speicherkontoblob erneut. Speichern Sie die Einstellungen.
>
>

Wenn Sie fertig sind, wählen Sie **Speichern** aus.

## <a name="log-detailed-errors"></a>Protokollieren ausführlicher Fehler

Um die Fehlerseite oder die Ablaufverfolgung fehlerhafter Anforderungen für Windows-Apps im [Azure-Portal](https://portal.azure.com) zu aktivieren, navigieren Sie zu Ihrer App und wählen **App Service-Protokolle** aus.

Wählen Sie **Ausführliche Fehlerprotokollierung** oder **Ablaufverfolgung fehlerhafter Anforderungen** aus, wählen Sie **Ein** aus, und wählen Sie dann **Speichern** aus.

Beide Typen von Protokollen werden im App Service-Dateisystem gespeichert. Bis zu 50 Fehler (Dateien/Ordner) werden aufbewahrt. Wenn die Anzahl der HTML-Dateien 50 überschreitet, werden die 26 ältesten Fehler automatisch gelöscht.

## <a name="add-log-messages-in-code"></a>Hinzufügen von Protokollmeldungen im Code

In Ihrem Anwendungscode verwenden Sie die üblichen Protokollierungsfunktionen, um Protokollmeldungen an die Anwendungsprotokolle zu senden. Beispiel:

- ASP.NET-Anwendungen können die Klasse [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) verwenden, um Informationen im Anwendungs-Diagnoseprotokoll aufzuzeichnen. Beispiel:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Standardmäßig verwendet ASP.NET Core den Protokollierungsanbieter [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices). Weitere Informationen finden Sie unter [Protokollierung in ASP.NET Core](/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Streaming von Protokollen

Aktivieren Sie vor dem Streamen von Protokollen in Echtzeit den gewünschten Protokolltyp. Alle Informationen, die in Dateien mit der Erweiterung TXT, LOG oder HTM im Verzeichnis */LogFiles* (D:/home/logfiles) gespeichert werden, werden von App Service gestreamt.

> [!NOTE]
> Einige Protokolltypen puffern die Schreibvorgänge in die Protokolldatei, was zu Störereignissen im Stream führen kann. Beispielsweise kann ein Anwendungsprotokolleintrag für den Besuch einer Seite im Stream vor dem zugehörigen HTTP-Protokolleintrag für die Seitenanforderung angezeigt werden.
>

### <a name="in-azure-portal"></a>Im Azure-Portal

Navigieren Sie zum Streamen von Protokollen im [Azure-Portal](https://portal.azure.com) zu Ihrer App, **und wählen Sie** Protokollstream aus. 

### <a name="in-cloud-shell"></a>In Cloud Shell

Verwenden Sie zum Livestreamen von Protokollen in [Cloud Shell](../cloud-shell/overview.md) den folgenden Befehl:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Um bestimmte Ereignisse wie beispielsweise Fehler zu filtern, verwenden Sie den Parameter **--Filter** . Beispiel:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Um bestimmte Protokolltypen wie HTTP zu filtern, verwenden Sie den Parameter **--Path** . Beispiel:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>In lokalem Terminal

Zum Streamen von Protokollen in der lokalen Konsole installieren Sie die [Azure CLI](/cli/azure/install-azure-cli), und [melden Sie sich bei Ihrem Konto an](/cli/azure/authenticate-azure-cli). Nachdem Sie sich angemeldet haben, befolgen Sie die [Anweisungen für Cloud Shell](#in-cloud-shell).

## <a name="access-log-files"></a>Zugreifen auf Protokolldateien

Wenn Sie die Option „Azure Storage-Blob“ für einen Protokolltyp konfigurieren, benötigen Sie ein Clienttool, das mit Azure Storage funktioniert. Weitere Informationen finden Sie unter [Azure Storage-Clienttools](../storage/common/storage-explorers.md).

Für Protokolle, die im App Service-Dateisystem gespeichert sind, ist die einfachste Möglichkeit, die ZIP-Datei im Browser unter der folgenden Adresse herunterzuladen:

- Linux-/Container-Apps: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows-Apps: `https://<app-name>.scm.azurewebsites.net/api/dump`

Bei Linux-/Container-Apps enthält die ZIP-Datei Konsolenausgabeprotokolle für den Docker-Host und den Docker-Container. Für eine horizontal hochskalierte App enthält die ZIP-Datei einen Satz von Protokollen für jede Instanz. Im App Service-Dateisystem sind diese Protokolldateien der Inhalt des Verzeichnisses */home/LogFiles*.

Für Windows-Apps enthält die ZIP-Datei den Inhalt des Verzeichnisses *D:\Home\LogFiles* im App Service- Dateisystem. Sie hat folgende Struktur:

| Protokolltyp | Verzeichnis | BESCHREIBUNG |
|-|-|-|
| **Anwendungsprotokolle** |*/LogFiles/Application/* | Enthält mindestens eine Textdatei. Das Format der Protokollmeldungen hängt vom verwendeten Protokollierungsanbieter ab. |
| **Ablaufverfolgung für Anforderungsfehler** | */LogFiles/W3SVC#########/* | Enthält XML-Dateien und eine XSL-Datei. Sie können die formatierten XML-Dateien im Browser anzeigen. |
| **Detaillierte Fehlerprotokolle** | */LogFiles/DetailedErrors/* | Enthält HTM-Fehlerdateien. Sie können die HTM-Dateien im Browser anzeigen.<br/>Eine andere Möglichkeit zum Anzeigen der Ablaufverfolgungen für Anforderungsfehler besteht darin, im Portal zur Seite Ihrer App zu navigieren. Wählen Sie im linken Menü **Diagnose und Problembehandlung** aus, und suchen Sie nach **Ablaufverfolgungsprotokolle für Anforderungsfehler**. Klicken Sie dann auf das Symbol, um die gewünschte Ablaufverfolgung zu durchsuchen und anzuzeigen. |
| **Webserverprotokolle** | */LogFiles/http/RawLogs/* | Enthält Textdateien im [erweiterten W3C-Protokolldateiformat](/windows/desktop/Http/w3c-logging). Diese Informationen können in einem Text-Editor oder mit einem Hilfsprogramm wie [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619) gelesen werden.<br/>App Service unterstützt die Felder `s-computername`, `s-ip` oder `cs-version` nicht. |
| **Bereitstellungsprotokolle** | */LogFiles/Git/* und */deployments/* | Enthält Protokolle, die von den internen Bereitstellungsprozessen generiert werden, sowie Protokolle für Git-Bereitstellungen. |

## <a name="send-logs-to-azure-monitor-preview"></a>Senden von Protokollen an Azure Monitor (Vorschauversion)

Mit der neuen [Azure Monitor-Integration](https://aka.ms/appsvcblog-azmon) können Sie [Diagnoseeinstellungen (Vorschau) erstellen](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting), um Protokolle an Speicherkonten, Event Hubs und Log Analytics zu senden. 

> [!div class="mx-imgBorder"]
> ![Diagnoseeinstellungen (Vorschau)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Unterstützte Protokolltypen

In der folgenden Tabelle werden die unterstützten Protokolltypen und Beschreibungen dieser aufgeführt: 

| Protokolltyp | Windows | Windows-Container | Linux | Linux-Container | BESCHREIBUNG |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Ja | Ja | Ja | Standardausgabe und Standardfehler |
| AppServiceHTTPLogs | Ja | Ja | Ja | Ja | Webserverprotokolle |
| AppServiceEnvironmentPlatformLogs | Ja | – | Ja | Ja | App Service-Umgebung: Skalierung, Konfigurationsänderungen und Statusprotokolle|
| AppServiceAuditLogs | Ja | Ja | Ja | Ja | Anmeldeaktivität per FTP und Kudu |
| AppServiceFileAuditLogs | Ja | Ja | Wird noch angekündigt | Wird noch angekündigt | Dateiänderungen am Websiteinhalt; nur für Premium-Tarif und höher verfügbar. |
| AppServiceAppLogs | ASP.NET | ASP.NET | Java SE und Tomcat Blessed Images <sup>1</sup> | Java SE und Tomcat Blessed Images <sup>1</sup> | Anwendungsprotokolle |
| AppServiceIPSecAuditLogs  | Ja | Ja | Ja | Ja | Anforderungen von IP-Regeln |
| AppServicePlatformLogs  | Wird noch angekündigt | Ja | Ja | Ja | Containervorgangsprotokolle |

<sup>1</sup> Fügen Sie für Java SE-Apps den App-Einstellungen „$WEBSITE_AZMON_PREVIEW_ENABLED“ hinzu, und legen Sie sie auf „1“ oder „true“ fest.

## <a name="next-steps"></a><a name="nextsteps"></a> Nächste Schritte
* [Abfrageprotokolle mit Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [How to Monitor Azure App Service (Vorgehensweise: Überwachen von Azure App Service)](web-sites-monitor.md)
* [Troubleshooting Azure App Service in Visual Studio (Problembehandlung für Azure App Service in Visual Studio)](troubleshoot-dotnet-visual-studio.md)
* [Analyze app Logs in HDInsight (Analyse von App-Protokollen in HDInsight)](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
