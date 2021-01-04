---
title: 'Tutorial: Problembehandlung mit Azure Monitor'
description: Es wird beschrieben, wie Sie Azure Monitor und Log Analytics für die Überwachung Ihrer App Service-Web-App verwenden können. Mit Azure Monitor wird die Verfügbarkeit erhöht, indem eine umfassende Lösung zum Überwachen Ihrer Umgebungen bereitgestellt wird.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: 049adac5521efd68ae8aa77af2d1007f9dfe0c0e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586991"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Tutorial: Problembehandlung für eine App Service-App mit Azure Monitor

> [!NOTE]
> Die Azure Monitor-Integration in App Service befindet sich in der [Vorschauphase](https://aka.ms/appsvcblog-azmon).
>

In diesem Tutorial wird veranschaulicht, wie Sie mit [Azure Monitor](../azure-monitor/overview.md) die Problembehandlung für eine [App Service](overview.md)-App durchführen. Die Beispiel-App enthält Code, mit dem der Arbeitsspeicher ausgelastet wird und HTTP 500-Fehler verursacht werden, damit Sie das Problem mit Azure Monitor diagnostizieren und beheben können. Nach Abschluss des Vorgangs verfügen Sie über eine Beispiel-App, die über App Service unter Linux mit Integration von [Azure Monitor](../azure-monitor/overview.md) ausgeführt wird.

[Azure Monitor](../azure-monitor/overview.md) maximiert die Verfügbarkeit und Leistung Ihrer Anwendungen und Dienste durch die Bereitstellung einer umfassenden Lösung für das Sammeln, Analysieren und Reagieren auf Telemetriedaten aus Ihren cloudbasierten und lokalen Umgebungen.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Konfigurieren einer Web-App mit Azure Monitor
> * Senden von Konsolenprotokollen an Log Analytics
> * Verwenden von Protokollabfragen zum Identifizieren und Beheben von Web-App-Problemen

Die Schritte in diesem Tutorial können unter macOS, Linux und Windows ausgeführt werden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Um dieses Tutorial abzuschließen, benötigen Sie Folgendes:

- [Azure-Abonnement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
- [Git-Client](https://git-scm.com/)

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Zunächst führen Sie lokal mehrere Befehle aus, um eine Beispiel-App für die Nutzung mit diesem Tutorial einzurichten. Mit den Befehlen wird eine Beispiel-App geklont, es werden Azure-Ressourcen und ein Bereitstellungsbenutzer erstellt, und die App wird in Azure bereitgestellt. Sie werden zum Eingeben des Kennworts aufgefordert, das bei der Erstellung des Bereitstellungsbenutzers angegeben wurde. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure main
```

## <a name="configure-azure-monitor-preview"></a>Konfigurieren von Azure Monitor (Vorschauversion)

### <a name="create-a-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs

Nachdem Sie die Beispiel-App nun in Azure App Service bereitgestellt haben, konfigurieren Sie die Überwachungsfunktion, um bei Problemen die Problembehandlung für die App durchführen zu können. Azure Monitor speichert Protokolldaten in einem Log Analytics-Arbeitsbereich. Bei einem Arbeitsbereich handelt es sich um einen Container, der Daten und Konfigurationsinformationen enthält.

In diesem Schritt erstellen Sie einen Log Analytics-Arbeitsbereich, um Azure Monitor mit Ihrer App zu konfigurieren.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [Bei Azure Monitor Log Analytics zahlen Sie für die Datenerfassung und -aufbewahrung.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Erstellen einer Diagnoseeinstellung

Mit Diagnoseeinstellungen können Metriken für bestimmte Azure-Dienste in Azure Monitor-Protokollen erfasst werden, um dann mit anderen Überwachungsdaten anhand von Protokollabfragen analysiert zu werden. In diesem Tutorial aktivieren Sie den Webserver und die standardmäßigen Ausgabe- bzw. Fehlerprotokolle. Eine umfassende Liste mit den Protokolltypen und Beschreibungen finden Sie unter [Unterstützte Protokolltypen](./troubleshoot-diagnostic-logs.md#supported-log-types).

Sie führen die folgenden Befehle aus, um Diagnoseeinstellungen für AppServiceConsoleLogs (Standardausgabe/-fehler) und AppServiceHTTPLogs (Webserverprotokolle) zu erstellen. Ersetzen Sie _\<app-name>_ und _\<workspace-name>_ durch Ihre eigenen Werte. 

> [!NOTE]
> Die ersten beiden Befehle (`resourceID` und `workspaceID`) sind Variablen, die im Befehl `az monitor diagnostic-settings create` verwendet werden. Weitere Informationen zu diesem Befehl finden Sie unter [Erstellen von Diagnoseeinstellungen mithilfe der Azure CLI](../azure-monitor/platform/diagnostic-settings.md#create-using-azure-cli).
>

```bash
resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'
```

## <a name="troubleshoot-the-app"></a>Durchführen der Problembehandlung für die App

Navigieren Sie zu `http://<app-name>.azurewebsites.net`.

Die Beispiel-App „ImageConverter“ konvertiert Bilder von `JPG` in `PNG`. Für dieses Tutorial wurde absichtlich ein Fehler in den Code eingefügt. Wenn Sie genügend Bilder auswählen, generiert die App bei der Bildkonvertierung einen HTTP 500-Fehler. Stellen Sie sich vor, dass dieses Szenario während der Entwicklungsphase nicht berücksichtigt wurde. Sie nutzen Azure Monitor, um die Problembehandlung für den Fehler durchzuführen.

### <a name="verify-the-app-is-works"></a>Überprüfen der richtigen Funktionsweise der App

Klicken Sie zum Konvertieren von Bildern auf `Tools`, und wählen Sie `Convert to PNG` aus.

![Klicken auf „Tools“ und Auswählen von „Convert to PNG“](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Wählen Sie die ersten beiden Bilder aus, und klicken Sie auf `convert`. Die Konvertierung wird erfolgreich durchgeführt.

![Auswählen der ersten beiden Bilder](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Auslösen des Fehlers für die App

Nachdem Sie die App nun überprüft haben, indem Sie die beiden Bilder erfolgreich konvertiert haben, versuchen wir nun, die ersten fünf Bilder zu konvertieren.

![Konvertieren der ersten fünf Bilder](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Diese Aktion ist nicht erfolgreich, und es wird ein Fehler vom Typ `HTTP 500` generiert, der während der Entwicklung nicht im Testumfang enthalten war.

![Konvertierung führt zu einem HTTP 500-Fehler](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Verwenden der Protokollabfrage zum Anzeigen von Azure Monitor-Protokollen

Wir sehen uns nun an, welche Protokolle im Log Analytics-Arbeitsbereich verfügbar sind. 

Klicken Sie auf diesen [Link für den Log Analytics-Arbeitsbereich](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces), um im Azure-Portal auf Ihren Arbeitsbereich zuzugreifen.

Wählen Sie im Azure-Portal Ihren Log Analytics-Arbeitsbereich aus.

### <a name="log-queries"></a>Protokollabfragen

Mithilfe von Protokollabfragen können Sie die Daten, die in Azure Monitor-Protokollen erfasst werden, in vollem Umfang nutzen. Sie verwenden Protokollabfragen, um die Protokolle sowohl in „AppServiceHTTPLogs“ als auch in „AppServiceConsoleLogs“ zu identifizieren. Weitere Informationen zu Protokollabfragen finden Sie unter [Übersicht über Protokollabfragen in Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="view-appservicehttplogs-with-log-query"></a>Anzeigen von „AppServiceHTTPLogs“ mit Protokollabfrage

Nachdem Sie nun auf die App zugegriffen haben, sehen wir uns die Daten an, die HTTP-Anforderungen zugeordnet sind. Sie befinden sich in `AppServiceHTTPLogs`.

1. Klicken Sie im linken Navigationsbereich auf `Logs`.

![Log Analytics-Arbeitsbereich: Protokolle](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Suchen Sie nach `appservice`, und doppelklicken Sie auf `AppServiceHTTPLogs`.

![Log Analytics-Arbeitsbereich: Tabellen](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Klicken Sie auf `Run`.

![Log Analytics-Arbeitsbereich: App Service-HTTP-Protokolle](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

Mit der Abfrage `AppServiceHTTPLogs` werden alle Anforderungen der letzten 24 Stunden zurückgegeben. Die Spalte `ScStatus` enthält den HTTP-Status. Begrenzen Sie `ScStatus` auf den Wert „500“, und führen Sie die Abfrage wie unten gezeigt aus, um die Fehler vom Typ `HTTP 500` zu diagnostizieren:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>Anzeigen von „AppServiceConsoleLogs“ mit Protokollabfrage

Nachdem Sie die HTTP 500-Fehler bestätigt haben, sehen wir uns nun die Standardausgabe bzw. -fehler der App an. Diese Protokolle befinden sich in „AppServiceConsoleLogs“.

(1) Klicken Sie auf `+`, um eine neue Abfrage zu erstellen. 

(2) Doppelklicken Sie auf die Tabelle `AppServiceConsoleLogs`, und klicken Sie anschließend auf `Run`. 

Da die Konvertierung von fünf Bildern zu Serverfehlern führt, können Sie verfolgen, ob von der App ebenfalls Fehler geschrieben werden, indem Sie nach `ResultDescription` filtern. Dies ist hier dargestellt:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

In der Spalte `ResultDescription` wird der folgende Fehler angezeigt:

<pre>
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
</pre>

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Verknüpfen von „AppServiceHTTPLogs“ und „AppServiceConsoleLogs“

Nachdem Sie nun sowohl HTTP 500-Fehler als auch Standardfehler identifiziert haben, müssen Sie überprüfen, ob eine Korrelation zwischen diesen Meldungen besteht. Als Nächstes verknüpfen Sie die Tabellen anhand des Zeitstempels `TimeGenerated` miteinander.

> [!NOTE]
> Es ist eine Abfrage verfügbar, die Folgendes bewirkt:
>
> - Filtern von „HTTPLogs“ nach 500-Fehlern
> - Abfragen von Konsolenprotokollen
> - Verknüpfen der Tabellen in `TimeGenerated`
>

Führen Sie die folgende Abfrage aus:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

In der Spalte `ResultDescription` wird der folgende Fehler zusammen mit Webserverfehlern angezeigt:

<pre>
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
</pre>

Der Meldungszustandspeicher in Zeile 20 von `process.php` ist voll ausgelastet. Sie haben nun bestätigt, dass von der Anwendung beim Auftreten des HTTP 500-Fehlers ein Fehler generiert wurde. Wir sehen uns den Code an, um das Problem zu identifizieren.

## <a name="identify-the-error"></a>Identifizieren des Fehlers

Öffnen Sie im lokalen Verzeichnis die Datei `process.php`, und sehen Sie sich Zeile 20 an. 

```php
imagepng($imgArray[$x], $filename);
```

Das erste Argument (`$imgArray[$x]`) ist eine Variable mit allen JPG-Dateien (In-Memory), die konvertiert werden müssen. Für `imagepng` werden aber nicht alle Bilder benötigt, sondern nur das jeweils zu konvertierende Bild. Das Vorabladen von Bildern ist nicht erforderlich und führt ggf. zur vollständigen Arbeitsspeicherauslastung, sodass sich HTTP 500-Fehler ergeben. Wir aktualisieren den Code, um Bilder bedarfsabhängig zu laden und zu ermitteln, ob das Problem hierdurch gelöst wird. Als Nächstes verbessern Sie den Code, um das Speicherproblem zu beseitigen.

## <a name="fix-the-app"></a>Problembehebung in der App

### <a name="update-locally-and-redeploy-the-code"></a>Lokales Aktualisieren und erneutes Bereitstellen des Codes

Sie nehmen die folgenden Änderungen an `process.php` vor, um die vollständige Auslastung des Arbeitsspeichers zu vermeiden:

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Führen Sie für Ihre Änderungen in Git einen Commit aus, und übertragen Sie dann die Codeänderungen mithilfe von Push an Azure.

```bash
git commit -am "Load images on-demand in process.php"
git push azure main
```

### <a name="browse-to-the-azure-app"></a>Navigieren zur Azure-App

Navigieren Sie zu `http://<app-name>.azurewebsites.net`. 

Beim Konvertieren von Bildern sollten die HTTP 500-Fehler nun nicht mehr auftreten.

![In Azure App Service ausgeführte PHP-App](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

Löschen Sie die Diagnoseeinstellung mit dem folgenden Befehl:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren einer Web-App mit Azure Monitor
> * Senden von Protokollen an Log Analytics
> * Verwenden von Protokollabfragen zum Identifizieren und Beheben von Web-App-Problemen

## <a name="next-steps"></a><a name="nextsteps"></a> Nächste Schritte
* [Abfrageprotokolle mit Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Troubleshooting Azure App Service in Visual Studio (Problembehandlung für Azure App Service in Visual Studio)](troubleshoot-dotnet-visual-studio.md)
* [Analyze app Logs in HDInsight (Analyse von App-Protokollen in HDInsight)](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)