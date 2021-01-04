---
title: Schnelles Erstellen einer .NET-App unter Service Fabric in Azure
description: In diesem Schnellstart erstellen Sie eine .NET-Anwendung für Azure, indem Sie die Service Fabric Reliable Services-Beispielanwendung verwenden.
ms.topic: quickstart
ms.date: 06/26/2019
ms.custom: mvc, devcenter, vs-azure
ms.openlocfilehash: 15e2180e44acaa5ebefb403b2da3755396a45ba4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575889"
---
# <a name="quickstart-deploy-a-net-reliable-services-application-to-service-fabric"></a>Schnellstart: Bereitstellen einer .NET Reliable Services-Anwendung in Service Fabric

Azure Service Fabric ist eine Plattform für verteilte Systeme zum Bereitstellen und Verwalten von skalierbaren und zuverlässigen Microservices und Containern.

In diesem Schnellstart wird gezeigt, wie Sie Ihre erste .NET-Anwendung für Service Fabric bereitstellen. Am Ende verfügen Sie über eine Abstimmungsanwendung mit einem ASP.NET Core-Web-Front-End, mit der Abstimmungsergebnisse im Cluster in einem zustandsbehafteten Back-End-Dienst gespeichert werden.

![Screenshot der Anwendung](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Mithilfe dieser Anwendung erfahren Sie Folgendes:

* Erstellen einer Anwendung mit .NET und Service Fabric
* Verwenden von ASP.NET Core als Web-Front-End
* Speichern von Anwendungsdaten in einem zustandsbehafteten Dienst
* Lokales Debuggen Ihrer Anwendung
* Horizontales Hochskalieren der Anwendung über mehrere Knoten hinweg
* Durchführen eines parallelen Anwendungsupgrades

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie diesen Schnellstart durch:

1. [Installieren Sie Visual Studio 2019](https://www.visualstudio.com/) mit den Workloads **Azure-Entwicklung** und **ASP.NET und Webentwicklung**.
2. [Installation von Git](https://git-scm.com/)
3. [Installieren Sie das Microsoft Azure Service Fabric-SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK).
4. Führen Sie den folgenden Befehl aus, um für Visual Studio die Bereitstellung im lokalen Service Fabric-Cluster zu ermöglichen:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
   ```
    
## <a name="build-a-cluster"></a>Erstellen eines Clusters

Nachdem Sie die Laufzeit, SDKs, Visual Studio-Tools und Docker installiert haben und Docker ausgeführt wird, erstellen Sie einen lokalen Entwicklungscluster mit fünf Knoten.

> [!Note]
> Docker muss beim Erstellen des Clusters ausgeführt werden, damit der Cluster mit aktivierten Containerfunktionen erstellt wird. Wenn Docker nicht ausgeführt wird, müssen Sie den Cluster erneut erstellen, um Containerfunktionen zu aktivieren.
> Die Anweisung, Docker beim Erstellen des Clusters auszuführen, ist zwar für diesen bestimmten Schnellstart nicht erforderlich, ist jedoch als bewährte Methode angegeben.
> Testen Sie, ob Docker ausgeführt wird, indem Sie ein Terminalfenster öffnen und `docker ps` ausführen, um zu sehen, ob ein Fehler auftritt. Wenn in der Antwort kein Fehler angegeben wird, wird Docker ausgeführt, und Sie sind bereit, einen Cluster zu erstellen.
>
> [Einrichten von Windows 10 oder Windows Server für Container](/virtualization/windowscontainers/quick-start/set-up-environment?tabs=Windows-10-Client)

1. Öffnen Sie als Administrator ein neues PowerShell-Fenster mit erhöhten Rechten.
2. Führen Sie den folgenden PowerShell-Befehl aus, um einen Entwicklungscluster zu erstellen:

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
   ```
3. Führen Sie den folgenden Befehl aus, um das lokale Cluster-Manager-Tool zu starten:

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
   ```

>[!NOTE]
> Die Beispielanwendung in dieser Schnellstartanleitung verwendet Features, die unter Windows 7 nicht verfügbar sind.
>

## <a name="download-the-sample"></a>Herunterladen des Beispiels

Führen Sie in einem Befehlsfenster den folgenden Befehl aus, um das Beispiel-App-Repository auf Ihren lokalen Computer zu klonen.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>Lokales Ausführen der Anwendung

Klicken Sie im Menü „Start“ mit der rechten Maustaste auf das Visual Studio-Symbol, und wählen Sie die Option **Als Administrator ausführen**. Um den Debugger an Ihre Dienste anzufügen, müssen Sie Visual Studio als Administrator ausführen.

Öffnen Sie die Visual Studio-Lösung **Voting.sln** aus dem geklonten Repository.

Die Abstimmungsanwendung lauscht standardmäßig an Port 8080.  Der Anwendungsport wird in der Datei */VotingWeb/PackageRoot/ServiceManifest.xml* festgelegt.  Sie können den Anwendungsport ändern, indem Sie das Attribut **Port** im Element **Endpoint** aktualisieren.  Wenn Sie die Anwendung lokal bereitstellen und ausführen möchten, muss der Anwendungsport auf dem Computer geöffnet und verfügbar sein.  Wenn Sie den Anwendungsport ändern, ersetzen Sie im gesamten Artikel „8080“ durch den Wert des neuen Anwendungsports.

Drücken Sie **F5**, um die Anwendung bereitzustellen.

> [!NOTE]
> Das Visual Studio-Ausgabefenster enthält die Meldung „Die Anwendungs-URL ist nicht festgelegt oder keine HTTP-/HTTPS-URL. Der Browser wird daher nicht für die Anwendung geöffnet.“  Diese Meldung deutet nicht auf einen Fehler, sondern darauf hin, dass ein Browser nicht automatisch gestartet wird.

Starten Sie nach Abschluss der Bereitstellung einen Browser, und öffnen Sie `http://localhost:8080`, um das Web-Front-End der Anwendung anzuzeigen.

![Anwendungs-Front-End](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

Sie können jetzt einen Satz mit Abstimmungsoptionen hinzufügen und die Abstimmung freigeben. Die Anwendung führt alle Daten in Ihrem Service Fabric-Cluster aus und speichert sie dort, ohne dass eine separate Datenbank verwendet werden muss.

## <a name="walk-through-the-voting-sample-application"></a>Durchlaufen der Beispielanwendung für die Abstimmung

Die Abstimmungsanwendung besteht aus zwei Diensten:

* Web-Front-End-Dienst (VotingWeb): Ein ASP.NET Core-Web-Front-End-Dienst, der die Webseite bereitstellt und Web-APIs für die Kommunikation mit dem Back-End-Dienst verfügbar macht.
* Back-End-Dienst (VotingData): Ein ASP.NET Core-Web-Dienst, der eine API verfügbar macht, um die Abstimmungsergebnisse in einem zuverlässigen Wörterbuch zu speichern, das dauerhaft auf dem Datenträger vorhanden ist.

![Anwendungsdiagramm](./media/service-fabric-quickstart-dotnet/application-diagram.png)

Beim Abstimmen in der Anwendung treten die folgenden Ereignisse ein:

1. Ein JavaScript sendet die Abstimmungsanforderung als HTTP PUT-Anforderung an die Web-API im Web-Front-End-Dienst.

2. Der Web-Front-End-Dienst nutzt einen Proxy, um eine HTTP PUT-Anforderung zu lokalisieren und an den Back-End-Dienst weiterzuleiten.

3. Der Back-End-Dienst erhält die eingehende Anforderung und speichert das aktualisierte Ergebnis in einem zuverlässigen Wörterbuch, das auf mehreren Knoten im Cluster repliziert und dauerhaft auf dem Datenträger gespeichert wird. Alle Daten der Anwendung werden im Cluster gespeichert, sodass keine Datenbank erforderlich ist.

## <a name="debug-in-visual-studio"></a>Debuggen in Visual Studio

Die Anwendung sollte reibungslos funktionieren. Sie können jedoch den Debugger verwenden, um die Funktion wichtiger Komponenten der Anwendung zu überprüfen. Beim Debuggen der Anwendung in Visual Studio verwenden Sie einen lokalen Service Fabric-Entwicklungscluster. Sie können Ihre Oberfläche für das Debuggen an Ihr Szenario anpassen. In dieser Anwendung werden Daten mithilfe eines zuverlässigen Wörterbuchs in einem Back-End-Dienst gespeichert. Standardmäßig wird die Anwendung von Visual Studio entfernt, wenn Sie den Debugger beenden. Die Entfernung der Anwendung führt dazu, dass auch die Daten im Back-End-Dienst entfernt werden. Um die Daten zwischen den Debugsitzungen beizubehalten, können Sie den **Debugmodus für die Anwendung** als Eigenschaft im Projekt **Voting** in Visual Studio ändern.

Führen Sie die folgenden Schritte aus, um zu ermitteln, was im Code passiert:

1. Öffnen Sie die Datei **/VotingWeb/Controllers/VotesController.cs**, und legen Sie in der **Put**-Methode der Web-API (Zeile 69) einen Breakpoint fest. Sie können in Visual Studio im Projektmappen-Explorer nach der Datei suchen.

2. Öffnen Sie die Datei **/VotingData/Controllers/VoteDataController.cs**, und legen Sie in der **Put**-Methode dieser Web-API (Zeile 54) einen Breakpoint fest.

3. Wechseln Sie zurück in den Browser, und klicken Sie auf eine Abstimmungsoption, oder fügen Sie eine neue Abstimmungsoption hinzu. Der erste Breakpoint befindet sich im API-Controller des Web-Front-Ends.
   * Bei diesem Schritt sendet das JavaScript im Browser eine Anforderung an den Web-API-Controller im Front-End-Dienst.

     ![Front-End-Dienst „Stimme hinzufügen“](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

   * Erstellen Sie zunächst die URL zum Reverseproxy für den Back-End-Dienst **(1)** .
   * Senden Sie anschließend die HTTP-Anforderung PUT an den Reverseproxy **(2)** .
   * Geben Sie zum Schluss die Antwort vom Back-End-Dienst an den Client zurück **(3)** .

4. Drücken Sie **F5**, um fortzufahren.
   - Gewähren Sie bei entsprechender Aufforderung durch den Browser der Gruppe „ServiceFabricAllowedUsers“ Lese- und Ausführungsberechtigungen für den Debugmodus.
   - Sie befinden sich jetzt am Breakpoint im Back-End-Dienst.

     ![Back-End-Dienst „Stimme hinzufügen“](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

   - In der ersten Zeile der Methode **(1)** wird das `StateManager`-Element verwendet, um ein zuverlässiges Wörterbuch mit dem Namen `counts` abzurufen bzw. hinzuzufügen.
   - Für alle Interaktionen mit Werten in einem zuverlässigen Wörterbuch ist eine Transaktion erforderlich. Diese Transaktion wird mithilfe der Anweisung **(2)** erstellt.
   - Aktualisieren Sie in der Transaktion den Wert des relevanten Schlüssels für die Abstimmungsoption, und committen Sie den Vorgang **(3)** . Nachdem die Rückgabe für die Commit-Methode durchgeführt wurde, werden die Daten im Wörterbuch aktualisiert und auf anderen Knoten im Cluster repliziert. Die Daten sind jetzt sicher im Cluster gespeichert, und der Back-End-Dienst kann das Failover auf andere Knoten durchführen, während die Daten weiterhin verfügbar sind.
5. Drücken Sie **F5**, um fortzufahren.

Drücken Sie **UMSCHALT+F5**, um die Debugsitzung zu beenden.

## <a name="perform-a-rolling-application-upgrade"></a>Durchführen eines parallelen Anwendungsupgrades

Beim Bereitstellen von neuen Updates für Ihre Anwendung führt Service Fabric den Rollout des Updates auf sichere Weise durch. Bei parallelen Upgrades kommt es während des Vorgangs nicht zu Ausfallzeiten, und bei Fehlern wird ein automatischer Rollback durchgeführt.

Gehen Sie zum Aktualisieren der Anwendung wie folgt vor:

1. Öffnen Sie die Datei **/VotingWeb/Views/Home/Index.cshtml** in Visual Studio.
2. Ändern Sie die Überschrift auf der Seite, indem Sie den Text ergänzen oder aktualisieren. Ändern Sie beispielsweise die Überschrift in „Service Fabric Voting Sample v2“.
3. Speichern Sie die Datei .
4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Voting**, und wählen Sie **Veröffentlichen**. Das Dialogfeld „Veröffentlichen“ wird angezeigt.
5. Klicken Sie auf die Schaltfläche **Manifestversion**, um die Version des Diensts und der Anwendung zu ändern.
6. Ändern Sie die Version des **Code**-Elements unter **VotingWebPkg** beispielsweise in „2.0.0“, und klicken Sie auf **Speichern**.

    ![Dialogfeld zum Ändern der Version](./media/service-fabric-quickstart-dotnet/change-version.png)
7. Aktivieren Sie im Dialogfeld **Service Fabric-Anwendung veröffentlichen** das Kontrollkästchen **Upgrade der Anwendung ausführen**.
8.  Ändern Sie **Zielprofil** in **PublishProfiles\Local.5Node.xml**, und stellen Sie sicher, dass **Verbindungsendpunkt** auf **Lokaler Cluster** festgelegt ist. 
9. Wählen Sie dann **Upgrade der Anwendung ausführen** aus.

    ![Dialogfeld „Veröffentlichen“ – Einstellung für Upgrade](./media/service-fabric-quickstart-dotnet/upgrade-app.png)

10. Klicken Sie auf **Veröffentlichen**.

    Während das Upgrade durchgeführt wird, können Sie die Anwendung weiterverwenden. Da zwei Instanzen des Diensts im Cluster ausgeführt werden, erhalten einige Anforderungen unter Umständen eine aktualisierte Version der Anwendung, während für andere weiterhin die alte Version verwendet wird.

11. Öffnen Sie Ihren Browser, und navigieren Sie zu der Clusteradresse an Port 19080. Beispiel: `http://localhost:19080/`.
12. Klicken Sie in der Strukturansicht auf den Knoten **Anwendungen** und dann im rechten Bereich auf **Upgrades in Progress** (Laufende Upgrades). Sie sehen, wie das Upgrade die Upgradedomänen in Ihrem Cluster durchläuft und wie sichergestellt wird, dass jede Domäne fehlerfrei ist, bevor mit der nächsten Domäne fortgefahren wird. Eine Upgradedomäne wird in der Statusanzeige grün angezeigt, wenn die Integrität der Domäne überprüft wurde.
    ![Upgradeansicht im Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/upgrading.png)

    Mit Service Fabric werden Upgrades sicher gemacht, indem nach dem Aktualisieren des Diensts auf einem Knoten im Cluster jeweils zwei Minuten gewartet wird. Rechnen Sie damit, dass der gesamte Updatevorgang ca. acht Minuten dauert.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

* Erstellen einer Anwendung mit .NET und Service Fabric
* Verwenden von ASP.NET Core als Web-Front-End
* Speichern von Anwendungsdaten in einem zustandsbehafteten Dienst
* Lokales Debuggen Ihrer Anwendung
* Horizontales Hochskalieren der Anwendung über mehrere Knoten hinweg
* Durchführen eines parallelen Anwendungsupgrades

Weitere Informationen zu Service Fabric und .NET finden Sie im Tutorial zum folgenden Thema:
> [!div class="nextstepaction"]
> [.NET-Anwendung in Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
