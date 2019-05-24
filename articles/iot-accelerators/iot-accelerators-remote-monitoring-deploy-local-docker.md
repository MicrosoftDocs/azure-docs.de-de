---
title: Lokales Bereitstellen der Remoteüberwachungslösung – Docker – Azure | Microsoft-Dokumentation
description: In dieser Schrittanleitung wird gezeigt, wie der Solution Accelerator für die Remoteüberwachung zu Test- und Entwicklungszwecken mithilfe von Docker auf Ihrem lokalen Computer bereitgestellt wird.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: db934725fe91a142c43ba66701919e9d5a70a4b2
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967540"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Lokales Bereitstellen des Solution Accelerators für die Remoteüberwachung – Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In diesem Artikel wird gezeigt, wie der Solution Accelerator für die Remoteüberwachung zu Test- und Entwicklungszwecken auf Ihrem lokalen Computer bereitgestellt wird. Sie erfahren, wie Sie die Microservices in lokalen Docker-Containern bereitstellen. Eine lokale Microservices-Bereitstellung verwendet die folgenden Clouddienste: IoT Hub, Cosmos DB, Azure Stream Analytics und Azure Time Series Insights-Dienste in der Cloud.

Wenn Sie den Solution Accelerator für die Remoteüberwachung in einer IDE auf Ihrem lokalen Computer ausführen möchten, finden Sie entsprechende Informationen unter [Lokales Bereitstellen des Solution Accelerators für die Remoteüberwachung – Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die Bereitstellung der vom Solution Accelerator für die Remoteüberwachung benötigten Azure-Dienste benötigen Sie ein aktives Azure-Abonnement.

Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Einrichtung des Computers

Um die lokale Bereitstellung abzuschließen, müssen die folgenden Tools auf dem lokalen Entwicklungscomputer installiert sein:

* [Git-Client](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) – wenn Sie planen, Änderungen an den Microservices vorzunehmen.
* [Node.js v8](https://nodejs.org/) – Diese Software ist eine Voraussetzung für die PCS-CLI, die von den Skripts zum Erstellen von Azure-Ressourcen genutzt wird. Verwenden Sie nicht Node.js v10.

> [!NOTE]
> Diese Tools sind auf vielen Plattformen einschließlich Windows, Linux und iOS verfügbar.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Ausführen der Microservices in Docker

Öffnen Sie eine neue Eingabeaufforderung, um sicherzustellen, dass Sie Zugriff auf die Umgebungsvariablen haben, die mit dem Skript **start.cmd** festgelegt wurden. Unter Windows können Sie überprüfen, ob die Umgebungsvariablen festgelegt sind, indem Sie den folgenden Befehl ausführen:

```cmd
set PCS
```

Mit dem Befehl werden alle mit dem Skript **start.cmd** festgelegten Umgebungsvariablen angezeigt.

Vergewissern Sie sich, dass Docker auf dem lokalen Computer ausgeführt wird.
> [!NOTE]
> Docker muss bei der Ausführung unter Windows [Linux-Container](https://docs.docker.com/docker-for-windows/) ausführen.

Die in den lokalen Docker-Containern ausgeführten Microservices müssen auf die Azure-Clouddienste zugreifen. Sie können die Internetkonnektivität Ihrer Docker-Umgebung mit dem folgenden Befehl testen, um eine Internetadresse innerhalb eines Containers zu pingen:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Navigieren Sie zum Ausführen des Solution Accelerators zum Ordner **services\\scripts\\local** in der Befehlszeilenumgebung, und führen Sie den folgenden Befehl aus:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Stellen Sie sicher, dass Sie [ein lokales Laufwerk](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) für Docker freigeben, bevor Sie `docker-compose up` ausführen.

Wenn Sie diesen Befehl zum ersten Mal ausführen, lädt Docker die Microservice-Images vom Docker-Hub herunter, um die Container lokal zu erstellen. Bei nachfolgenden Ausführungen führt Docker die Container sofort aus.

> [!TIP]
> Microsoft veröffentlicht regelmäßig neue Docker-Images mit neuen Funktionen. Mit den folgenden Befehlen können Sie die lokalen Docker-Container und die entsprechenden Images bereinigen, bevor Sie die neuesten Container und Images abrufen:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Sie können zum Anzeigen der Protokolle aus dem Container eine separate Shell verwenden. Suchen Sie zuerst die Container-ID mithilfe des `docker ps`-Befehls. Verwenden Sie dann `docker logs {container-id} --tail 1000` zum Anzeigen der letzten 1000 Einträge für den angegebenen Container.

### <a name="start-the-stream-analytics-job"></a>Starten des Stream Analytics-Auftrags

Führen Sie folgende Schritte aus, um den Stream Analytics-Auftrag zu starten:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zu der für Ihre Projektmappe erstellten **Ressourcengruppe**. Der Name der Ressourcengruppe ist der Name der Projektmappe, den Sie beim Ausführen des Skripts **start.cmd** ausgewählt haben.
1. Klicken Sie in der Liste der Ressourcen auf den **Stream Analytics-Auftrag**.
1. Klicken Sie auf der Seite **Übersicht** des Stream Analytics-Auftrags auf die Schaltfläche **Starten**. Klicken Sie dann auf **Starten**, um den Auftrag direkt zu starten.

### <a name="connect-to-the-dashboard"></a>Verbinden mit dem Dashboard

Browsen Sie für den Zugriff auf das Dashboard der Lösung für die Remoteüberwachung zu `http://localhost:8080`. Nun können Sie die Webbenutzeroberfläche und die lokalen Microservices verwenden.

## <a name="clean-up"></a>Bereinigen

Um unnötige Gebühren zu vermeiden, entfernen Sie nach Abschluss Ihrer Tests die Clouddienste aus Ihrem Azure-Abonnement. Navigieren Sie zum Entfernen der Dienste zum [Azure-Portal](https://ms.portal.azure.com), und löschen Sie die Ressourcengruppe, die mit dem Skript **start.cmd** erstellt wurde.

Entfernen Sie mit dem `docker-compose down --rmi all`-Befehl die Docker-Images, und geben Sie Speicherplatz auf dem lokalen Computer frei. Sie können auch die lokale Kopie des Remoteüberwachungsrepositorys löschen, die erstellt wurde, als Sie den Quellcode aus GitHub geklont haben.

## <a name="next-steps"></a>Nächste Schritte

Nach Bereitstellung der Remoteüberwachungslösung können Sie sich als Nächstes [mit den Funktionen des Lösungsdashboards vertraut machen](quickstart-remote-monitoring-deploy.md).
