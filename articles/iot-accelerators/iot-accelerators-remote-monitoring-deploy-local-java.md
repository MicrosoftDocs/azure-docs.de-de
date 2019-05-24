---
title: Lokales Bereitstellen der Remoteüberwachungslösung (über IntelliJ-IDE) – Azure | Microsoft-Dokumentation
description: In dieser Schrittanleitung wird gezeigt, wie der Solution Accelerator für die Remoteüberwachung zu Test- und Entwicklungszwecken mithilfe von IntelliJ auf Ihrem lokalen Computer bereitgestellt wird.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 2b55fea69fe1affb6cab5d360f1e8355c3bb720d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "66015435"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Lokales Bereitstellen des Solution Accelerators für die Remoteüberwachung – IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In diesem Artikel wird gezeigt, wie der Solution Accelerator für die Remoteüberwachung zu Test- und Entwicklungszwecken auf Ihrem lokalen Computer bereitgestellt wird. Sie erfahren, wie Sie die Microservices in IntelliJ ausführen. Eine lokale Microservices-Bereitstellung verwendet die folgenden Clouddienste: IoT Hub, Cosmos DB, Azure Stream Analytics und Azure Time Series Insights-Dienste in der Cloud.

Wenn Sie den Solution Accelerator für die Remoteüberwachung auf Ihrem lokalen Computer in Docker ausführen möchten, finden Sie entsprechende Informationen unter [Lokales Bereitstellen des Solution Accelerators für die Remoteüberwachung – Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die Bereitstellung der vom Solution Accelerator für die Remoteüberwachung benötigten Azure-Dienste benötigen Sie ein aktives Azure-Abonnement.

Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Einrichtung des Computers

Um die lokale Bereitstellung abzuschließen, müssen die folgenden Tools auf dem lokalen Entwicklungscomputer installiert sein:

* [Git-Client](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [IntelliJ-Plug-In Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ-Plug-In SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ-Plug-In SBT Executor](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) – Diese Software ist eine Voraussetzung für die PCS-CLI, die von den Skripts zum Erstellen von Azure-Ressourcen genutzt wird. Verwenden Sie nicht Node.js v10.

> [!NOTE]
> Die IntelliJ-IDE ist für Windows und Mac verfügbar.

## <a name="download-the-source-code"></a>Herunterladen des Quellcodes

Die Quellcoderepositorys für die Remoteüberwachung umfassen den Quellcode und die Docker-Konfigurationsdateien, die Sie zum Ausführen der Docker-Images für die Microservices benötigen.

Um eine lokale Version des Repositorys zu klonen und zu erstellen, navigieren Sie über Ihre Befehlszeilenumgebung zu einem geeigneten Ordner auf dem lokalen Computer. Führen Sie dann einen der folgenden Befehlssätze aus, um das Java-Repository zu klonen:

Um die neueste Version der Java-Microserviceimplementierungen herunterzuladen, führen Sie Folgendes aus:


```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Diese Befehle laden den Quellcode für alle Microservices zusätzlich zu den Skripts herunter, mit denen Sie die Microservices lokal ausführen. Obwohl Sie den Quellcode zum Ausführen der Microservices in Docker nicht benötigen, ist er hilfreich, wenn Sie den Solution Accelerator später ändern und Ihre Änderungen lokal testen möchten.

## <a name="deploy-the-azure-services"></a>Bereitstellen von Azure-Diensten

In diesem Artikel erfahren Sie zwar, wie Sie die Microservices lokal ausführen, doch sie hängen von Azure-Diensten ab, die in der Cloud ausgeführt werden. Stellen Sie die Azure-Dienste mit dem nachfolgenden Skript bereit. Bei den nachstehenden Skriptbeispielen wird davon ausgegangen, dass Sie das Java-Repository auf einem Windows-Computer verwenden. Wenn Sie in einer anderen Umgebung arbeiten, passen Sie die Pfade, Dateierweiterungen und Pfadtrennzeichen entsprechend an.

### <a name="create-new-azure-resources"></a>Erstellen neuer Azure-Ressourcen

Wenn Sie die erforderlichen Azure-Ressourcen noch nicht erstellt haben, führen Sie die folgenden Schritte aus:

1. Navigieren Sie in Ihrer Befehlszeilenumgebung zum Ordner **\services\scripts\local\launch** in Ihrer geklonten Kopie des Repositorys.

1. Führen Sie die folgenden Befehle aus, um das CLI-Tool **pcs** zu installieren und sich bei Ihrem Azure-Konto anzumelden:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Führen Sie das Skript **start.cmd** aus. Das Skript fordert Sie zur Eingabe der folgenden Informationen auf:
   * Ein Lösungsname.
   * Das zu verwendende Azure-Abonnement.
   * Der Speicherort des zu verwendenden Azure-Rechenzentrums.

     Das Skript erstellt die Ressourcengruppe in Azure mit dem Namen Ihrer Projektmappe. Diese Ressourcengruppe enthält die Azure-Ressourcen, die der Solution Accelerator verwendet. Sie können diese Ressourcengruppe löschen, wenn Sie die entsprechenden Ressourcen nicht mehr benötigen.

     Mit dem Skript werden auf dem lokalen Computer zudem mehrere Umgebungsvariablen mit dem Präfix **PCS** hinzugefügt. Diese Umgebungsvariablen stellen die Details für die Remoteüberwachung bereit, damit sie aus einer Azure Key Vault-Ressource gelesen werden können. Diese Key Vault-Ressource ist die Quelle, aus der die Remoteüberwachung ihre Konfigurationswerte lesen wird.

     > [!TIP]
     > Nach Abschluss des Skripts werden außerdem die Umgebungsvariablen in der Datei **\<Ihr Stammordner\>\\.pcs\\\<Lösungsname\>.env** gespeichert. Sie können sie für zukünftige Bereitstellungen des Solution Accelerators verwenden. Beachten Sie, dass alle auf dem lokalen Computer festgelegten Umgebungsvariablen die Werte in der Datei **services\\scripts\\local\\.env** überschreiben, wenn Sie **docker-compose** ausführen.

1. Beenden Sie die Befehlszeilenumgebung.

### <a name="use-existing-azure-resources"></a>Verwenden vorhandener Azure-Ressourcen

Wenn Sie die erforderlichen Azure-Ressourcen bereits erstellt haben, erstellen Sie die entsprechenden Umgebungsvariablen auf dem lokalen Computer.
Legen Sie die Umgebungsvariablen für Folgendes fest:
* **PCS_KEYVAULT_NAME** – Der Name der Azure Key Vault-Ressource
* **PCS_AAD_APPID** – Die AAD-Anwendungs-ID
* **PCS_AAD_APPSECRET** – Das AAD-Anwendungsgeheimnis

Konfigurationswerte werden aus dieser Azure Key Vault-Ressource gelesen. Diese Umgebungsvariablen sind möglicherweise in der Datei **\<Ihr Stammordner\>\\.pcs\\\<Lösungsname\>.env** aus der Bereitstellung gespeichert. Beachten Sie, dass die auf dem lokalen Computer festgelegten Umgebungsvariablen die Werte in der Datei **services\\scripts\\local\\.env** überschreiben, wenn Sie **docker-compose** ausführen.

Ein Teil der vom Microservice benötigten Konfiguration wird in einer **Key Vault**-Instanz gespeichert, die bei der ersten Bereitstellung erstellt wurde. Die entsprechenden Variablen in Key Vault sollten nach Bedarf geändert werden.

## <a name="run-the-microservices"></a>Ausführen der Microservices

In diesem Abschnitt führen Sie die Microservices für die Remoteüberwachung aus. Sie führen die Webbenutzeroberfläche nativ, die Dienste für Gerätesimulation, Authentifizierung und ASA-Manager in Docker und die Microservices in IntelliJ aus.

### <a name="run-the-device-simulation-service"></a>Ausführen des Gerätesimulationsdiensts

Öffnen Sie eine neues Eingabeaufforderungsfenster, um sicherzustellen, dass Sie Zugriff auf die Umgebungsvariablen haben, die in der vorherigen Sitzung mit dem Skript **start.cmd** festgelegt wurden.

Führen Sie den folgenden Befehl aus, um den Docker-Container für den Gerätesimulationsdienst zu starten. Der Dienst simuliert Geräte für die Remoteüberwachungslösung.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Ausführen des Authentifizierungsdiensts

Öffnen Sie ein neues Eingabeaufforderungsfenster, und führen Sie den folgenden Befehl aus, um den Docker-Container für den Authentifizierungsdienst zu starten. Der Dienst ermöglicht es, die Benutzer zu verwalten, die für den Zugriff auf Azure-IoT-Lösungen autorisiert sind.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>Ausführen des ASA-Manager-Diensts

Öffnen Sie ein neues Eingabeaufforderungsfenster, und führen Sie den folgenden Befehl aus, um den Docker-Container für den ASA-Manager-Dienst zu starten. Der Dienst ermöglicht die Verwaltung von Azure Stream Analytics-Aufträgen (ASA), einschließlich des Festlegens der Konfiguration und des Startens, Beendens und Überwachens ihres Status.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Bereitstellen aller anderen Microservices auf dem lokalen Computer

In den folgenden Schritten wird erläutert, wie Sie die Microservices für die Remoteüberwachung in IntelliJ ausführen:

#### <a name="import-project"></a>Importieren des Projekts

1. Starten der IntelliJ-IDE
1. Wählen Sie **Projekt importieren** und dann **azure-iot-pcs-remote-monitoring-java\services\build.sbt** aus.

#### <a name="create-run-configurations"></a>Erstellen von Laufzeitkonfigurationen

1. Wählen Sie **Ausführen > Konfigurationen bearbeiten** aus.
1. Wählen Sie **Neue Konfiguration hinzufügen > sbt task (SBT-Aufgabe)** aus. 
1. Geben Sie einen **Namen** und dann **Tasks** als Ausführung ein. 
1. Wählen Sie das **Arbeitsverzeichnis** basierend auf dem auszuführenden Dienst aus.
1. Klicken Sie auf **Anwenden > OK**, um Ihre Auswahl zu speichern.
1. Erstellen Sie die Laufzeitkonfiguration für die folgenden Dienste:
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

Die folgende Abbildung zeigt als Beispiel das Hinzufügen einer Konfiguration für einen Dienst:

[![Add-Configuration](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Erstellen einer Kombinationskonfiguration

1. Wählen Sie zum gemeinsamen Ausführen aller Dienste **Neue Konfiguration hinzufügen > Kombination** aus.
1. Geben Sie einen **Namen** ein, und **fügen Sie SBT-Aufgaben hinzu**.
1. Klicken Sie auf **Anwenden > OK**, um Ihre Auswahl zu speichern.

Die folgende Abbildung zeigt als Beispiel das Hinzufügen aller SBT-Aufgaben zu einer einzigen Konfiguration:

[![Add-All-Services](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Klicken Sie auf **Ausführen**, um die Webdienste auf dem lokalen Computer zu erstellen und auszuführen.

Für jeden Webdienst werden eine Eingabeaufforderung und ein Webbrowserfenster geöffnet. An der Eingabeaufforderung wird die Ausgabe von dem ausgeführten Dienst angezeigt, und im Browserfenster können Sie den Status überwachen. Schließen Sie die Eingabeaufforderungen und Webseiten nicht, dadurch werden die Webdienste beendet.


Um auf den Status der Dienste zuzugreifen, können Sie zu den folgenden URLs navigieren:
* IoT-Hub-Manager: [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Gerätetelemetrie: [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* Konfiguration: [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* Speicheradapter: [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


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
