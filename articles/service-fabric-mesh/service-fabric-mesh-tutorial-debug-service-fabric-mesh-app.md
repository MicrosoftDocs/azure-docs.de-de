---
title: Debuggen einer lokal ausgeführten Azure Service Fabric Mesh-Web-App
description: In diesem Tutorial wird eine in Ihrem lokalen Cluster ausgeführte Azure Service Fabric-Mesh-Anwendung debuggt.
author: georgewallace
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 9e3dc16481340c0266cd398d0970e2147648e17f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625448"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Tutorial: Debuggen einer Service Fabric Mesh-Anwendung, die in Ihrem lokalen Entwicklungscluster ausgeführt wird

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen über die Service Fabric Mesh-API werden nicht mehr gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Ausmusterung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Dieses Tutorial ist der zweite Teil einer Reihe und zeigt, wie Sie eine Azure Service Fabric Mesh-App in Ihrem lokalen Entwicklungscluster erstellen und debuggen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Abläufe beim Erstellen einer Azure Service Fabric Mesh-Anwendung
> * Festlegen eines Breakpoints, um einen Aufruf zwischen Diensten zu beobachten

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * [Erstellen einer Service Fabric-App in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Debuggen einer Service Fabric Mesh-App, die in Ihrem lokalen Entwicklungscluster ausgeführt wird
> * [Bereitstellen einer Service Fabric Mesh-App](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Aktualisieren einer Service Fabric Mesh-App](service-fabric-mesh-tutorial-upgrade.md)
> * [Bereinigen von Service Fabric Mesh-Ressourcen](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Stellen Sie sicher, dass Sie [Ihre Entwicklungsumgebung eingerichtet haben](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Dies umfasst auch das Installieren der Service Fabric-Laufzeit, des SDK und die Installation von Docker sowie Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Herunterladen der Aufgabenbeispielanwendung

Falls Sie die Aufgabenbeispielanwendung aus [Teil 1 dieser Tutorialreihe](service-fabric-mesh-tutorial-create-dotnetcore.md) nicht erstellt haben, können Sie sie herunterladen. Führen Sie in einem Befehlsfenster den folgenden Befehl aus, um das Beispiel-App-Repository auf Ihren lokalen Computer zu klonen.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Die Anwendung befindet sich im Verzeichnis „`src\todolistapp`“.

## <a name="build-and-debug-on-your-local-cluster"></a>Erstellen und Debuggen in Ihrem lokalen Cluster

Beim Laden Ihres Projekts wird automatisch ein Docker-Image erstellt und in Ihrem lokalen Cluster bereitgestellt. Dieser Vorgang kann etwas dauern. Den Fortschritt können Sie in Visual Studio im Bereich **Ausgabe** überwachen. Wählen Sie hierzu in der Dropdownliste **Ausgabe anzeigen von:** die Option **Service Fabric-Tools** aus.

Drücken Sie **F5**, um Ihren Dienst zu kompilieren und lokal auszuführen. Wenn das Projekt lokal ausgeführt und debuggt wird, werden von Visual Studio folgende Schritte ausgeführt:

* Sicherstellen, dass Docker für Windows ausgeführt wird und Windows als Containerbetriebssystem festgelegt ist
* Herunterladen ggf. fehlender Docker-Basisimages (kann etwas dauern)
* Erstellen (oder erneutes Erstellen) des Docker-Images zum Hosten Ihres Codeprojekts
* Bereitstellen und Ausführen des Containers im lokalen Service Fabric-Entwicklungscluster
* Ausführen Ihrer Dienste und Anhalten an allen ggf. festgelegten Breakpoints

Wenn die lokale Bereitstellung fertig gestellt wurde und Ihre App von Visual Studio ausgeführt wird, öffnet sich ein Browserfenster mit einer standardmäßigen Beispielwebseite.

## <a name="debugging-tips"></a>Tipps zum Debuggen

Befolgen Sie die Anweisungen im Artikel zum [Optimieren der Visual Studio-Leistung](service-fabric-mesh-howto-optimize-vs.md), um den ersten Debuglauf (F5) erheblich zu beschleunigen.

Derzeit besteht ein Problem, das bewirkt, dass mit dem Aufruf von `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` keine Verbindung mit dem Dienst hergestellt werden kann. Dies kann immer passieren, wenn sich Ihre Host-IP-Adresse ändert. Problembehebung:

1. Entfernen Sie die App aus dem lokalen Cluster (in Visual Studio, **Build** > **Projektmappe bereinigen**).
2. Wählen Sie im Service Fabric-Managers für lokale Cluster die Option **Lokalen Cluster beenden** und dann **Lokalen Cluster starten**.
3. Stellen Sie die App erneut bereit (in Visual Studio, **F5**).

Sollte der Fehler **No Service Fabric local cluster is running** (Es wird kein lokaler Service Fabric-Cluster ausgeführt.) auftreten, vergewissern Sie sich, dass der LCM (Service Fabric Local Cluster Manager) ausgeführt wird, klicken Sie auf der Taskleiste mit der rechten Maustaste auf das LCM-Symbol, und klicken Sie anschließend auf **Start Local Cluster** (Lokalen Cluster starten). Kehren Sie nach dem Start des Clusters zu Visual Studio zurück, und drücken Sie **F5**.

Sollte beim Start der App ein Fehler vom Typ **404** auftreten, sind möglicherweise die Umgebungsvariablen in **service.yaml** nicht korrekt. Vergewissern Sie sich, dass `ApiHostPort` und `ToDoServiceName` ordnungsgemäß festgelegt sind, wie unter [Tutorial: Erstellen, Debuggen und Bereitstellen einer Webanwendung mit mehreren Diensten in Service Fabric Mesh](./service-fabric-mesh-tutorial-create-dotnetcore.md#create-environment-variables) beschrieben.

Vergewissern Sie sich im Falle von Buildfehlern in **service.yaml**, dass zum Einrücken der Zeilen keine Tabulatoren, sondern Leerzeichen verwendet wurden. Darüber hinaus müssen Sie die App vorerst mit dem englischen Gebietsschema erstellen.

### <a name="debug-in-visual-studio"></a>Debuggen in Visual Studio

Beim Debuggen einer Service Fabric Mesh-Anwendung in Visual Studio verwenden Sie einen lokalen Service Fabric-Entwicklungscluster. Debuggen Sie die Methode „OnGet()“, um zu sehen, wie Aufgaben aus dem Back-End-Dienst abgerufen werden.
1. Navigieren Sie im Projekt **WebFrontEnd** zu **Seiten** > **Index.cshtml** > **Index.cshtml.cs**, und legen Sie einen Breakpoint in der Methode **OnGet** (Zeile 17) fest.
2. Öffnen Sie im Projekt **ToDoService** die Datei **TodoController.cs**, und legen Sie einen Breakpoint in der Methode **Get** (Zeile 15) fest.
3. Aktualisieren Sie die Anzeige im Browser. Sie erreichen den ersten Breakpoint in der Web-Front-End-Methode `OnGet()`. Anhand der Variablen `backendUrl` sehen Sie, wie die Umgebungsvariablen, die Sie in der Datei **service.yaml** definiert haben, zu der URL kombiniert werden, die zum Herstellen der Verbindung mit dem Back-End-Dienst verwendet wird.
4. Überspringen Sie durch Drücken von F10 den Aufruf `client.GetAsync(backendUrl).GetAwaiter().GetResult())`, um zum Controller-Breakpoint `Get()` zu gelangen. In dieser Methode sehen Sie, wie die Liste mit den Aufgaben aus der In-Memory-Liste abgerufen wird.
5. Wenn Sie fertig sind, drücken Sie **UMSCHALT+F5**, um das Debuggen Ihres Projekts in Visual Studio zu beenden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Abläufe beim Erstellen einer Azure Service Fabric Mesh-Anwendung
> * Festlegen eines Breakpoints, um einen Aufruf zwischen Diensten zu beobachten

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Bereitstellen einer Service Fabric Mesh-App](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
