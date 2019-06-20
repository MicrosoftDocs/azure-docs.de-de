---
title: Lokales Bereitstellen der Remoteüberwachungslösung (über Visual Studio-IDE) – Azure | Microsoft-Dokumentation
description: In dieser Schrittanleitung wird gezeigt, wie der Solution Accelerator für die Remoteüberwachung zu Test- und Entwicklungszwecken mithilfe von Visual Studio auf Ihrem lokalen Computer bereitgestellt wird.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 1adf59feca7db4c5903b04c59e1bd23290c1855e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967512"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Lokales Bereitstellen des Solution Accelerators für die Remoteüberwachung – Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In diesem Artikel wird gezeigt, wie der Solution Accelerator für die Remoteüberwachung zu Test- und Entwicklungszwecken auf Ihrem lokalen Computer bereitgestellt wird. Hier wird erläutert, wie Sie die Microservices in Visual Studio ausführen. Eine lokale Microservices-Bereitstellung verwendet die folgenden Clouddienste: IoT Hub, Cosmos DB, Azure Stream Analytics und Azure Time Series Insights-Dienste in der Cloud.

Wenn Sie den Solution Accelerator für die Remoteüberwachung auf Ihrem lokalen Computer in Docker ausführen möchten, finden Sie entsprechende Informationen unter [Lokales Bereitstellen des Solution Accelerators für die Remoteüberwachung – Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die Bereitstellung der vom Solution Accelerator für die Remoteüberwachung benötigten Azure-Dienste benötigen Sie ein aktives Azure-Abonnement.

Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Einrichtung des Computers

Um die lokale Bereitstellung abzuschließen, müssen die folgenden Tools auf dem lokalen Entwicklungscomputer installiert sein:

* [Git-Client](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) – Diese Software ist eine Voraussetzung für die PCS-CLI, die von den Skripts zum Erstellen von Azure-Ressourcen genutzt wird. Verwenden Sie nicht Node.js v10.

> [!NOTE]
> Visual Studio ist für Windows und Mac verfügbar.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Ausführen der Microservices

In diesem Abschnitt führen Sie die Microservices für die Remoteüberwachung aus. Sie führen die Webbenutzeroberfläche nativ, den Gerätesimulationsdienst in Docker und die Microservices in Visual Studio aus.

### <a name="run-the-device-simulation-service"></a>Ausführen des Gerätesimulationsdiensts

Öffnen Sie eine neues Eingabeaufforderungsfenster, um sicherzustellen, dass Sie Zugriff auf die Umgebungsvariablen haben, die in der vorherigen Sitzung mit dem Skript **start.cmd** festgelegt wurden.

Führen Sie den folgenden Befehl aus, um den Docker-Container für den Gerätesimulationsdienst zu starten. Der Dienst simuliert Geräte für die Remoteüberwachungslösung.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Bereitstellen aller anderen Microservices auf dem lokalen Computer

In den folgenden Schritten wird erläutert, wie Sie die Microservices für die Remoteüberwachung in Visual Studio ausführen:

1. Starten Sie Visual Studio.
1. Öffnen Sie die Projektmappe **remote-monitoring.sln** im Ordner **services** in der lokalen Kopie des Repositorys.
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **Eigenschaften**.
1. Wählen Sie **Allgemeine Eigenschaften > Startprojekt** aus.
1. Wählen Sie **Mehrere Startprojekte** aus, und legen Sie **Aktion** für die folgenden Projekte auf **Starten** fest:
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (device-telemetry\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (storage-adapter\WebService)
1. Klicken Sie zum Speichern der Auswahl auf **OK**.
1. Klicken Sie auf **Debuggen > Debuggen starten**, um die Webdienste auf dem lokalen Computer zu erstellen und auszuführen.

Für jeden Webdienst werden eine Eingabeaufforderung und ein Webbrowserfenster geöffnet. An der Eingabeaufforderung wird die Ausgabe von dem ausgeführten Dienst angezeigt, und im Browserfenster können Sie den Status überwachen. Schließen Sie die Eingabeaufforderungen und Webseiten nicht, dadurch werden die Webdienste beendet.

### <a name="start-the-stream-analytics-job"></a>Starten des Stream Analytics-Auftrags

Führen Sie folgende Schritte aus, um den Stream Analytics-Auftrag zu starten:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zu der für Ihre Projektmappe erstellten **Ressourcengruppe**. Der Name der Ressourcengruppe ist der Name der Projektmappe, den Sie beim Ausführen des Skripts **start.cmd** ausgewählt haben.
1. Klicken Sie in der Liste der Ressourcen auf den **Stream Analytics-Auftrag**.
1. Klicken Sie auf der Seite **Übersicht** des Stream Analytics-Auftrags auf die Schaltfläche **Starten**. Klicken Sie dann auf **Starten**, um den Auftrag direkt zu starten.

### <a name="run-the-web-ui"></a>Ausführen der Webbenutzeroberfläche

In diesem Schritt starten Sie die Webbenutzeroberfläche. Öffnen Sie eine neues Eingabeaufforderungsfenster, um sicherzustellen, dass Sie Zugriff auf die Umgebungsvariablen haben, die mit dem Skript **start.cmd** festgelegt wurden. Navigieren Sie in der lokalen Kopie des Repositorys zum Ordner **webui**, und führen Sie die folgenden Befehle aus:

```cmd
npm install
npm start
```

Nach Abschluss des Starts wird im Browser die Seite **http:\//localhost:3000/dashboard** angezeigt. Die Fehler auf dieser Seite sind zu erwarten. Um die Anwendung ohne Fehler anzuzeigen, führen Sie den folgenden Schritt aus.

### <a name="configure-and-run-nginx"></a>Konfigurieren und Ausführen von NGINX

Richten Sie einen Reverseproxyserver ein, um die Webanwendung und die Microservices zu verknüpfen, die auf dem lokalen Computer ausgeführt werden:

* Kopieren Sie die Datei **nginx.conf** aus dem Ordner **webui\scripts\localhost** in Ihrer lokalen Kopie des Repositorys in das Installationsverzeichnis **nginx\conf**.
* Führen Sie **NGINX** aus.

Weitere Informationen zum Ausführen von **NGINX** finden Sie unter [nginx for Windows](https://nginx.org/en/docs/windows.html) (nginx für Windows).

### <a name="connect-to-the-dashboard"></a>Verbinden mit dem Dashboard

Navigieren Sie für den Zugriff auf das Dashboard der Remoteüberwachungslösung in Ihrem Browser zu „http:\//localhost:9000“.

## <a name="clean-up"></a>Bereinigen

Um unnötige Gebühren zu vermeiden, entfernen Sie nach Abschluss Ihrer Tests die Clouddienste aus Ihrem Azure-Abonnement. Navigieren Sie zum Entfernen der Dienste zum [Azure-Portal](https://ms.portal.azure.com), und löschen Sie die Ressourcengruppe, die mit dem Skript **start.cmd** erstellt wurde.

Sie können auch die lokale Kopie des Remoteüberwachungsrepositorys löschen, die erstellt wurde, als Sie den Quellcode aus GitHub geklont haben.

## <a name="next-steps"></a>Nächste Schritte

Nach Bereitstellung der Remoteüberwachungslösung können Sie sich als Nächstes [mit den Funktionen des Lösungsdashboards vertraut machen](quickstart-remote-monitoring-deploy.md).
