---
title: Debuggen von Anwendungen in Visual Studio
description: Verbessern Sie die Zuverlässigkeit und Leistung Ihrer Dienste, indem Sie sie in Visual Studio in einem lokalen Entwicklungscluster entwickeln und debuggen.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b7d08d610c883240abedc66c55abba64a74c8e3
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576314"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Debuggen der Service Fabric-Anwendung mithilfe von Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Debuggen einer lokalen Service Fabric-Anwendung
Sie können Zeit und Geld sparen, indem Sie Ihre Azure Service Fabric-Anwendung in einem Cluster für die Entwicklung auf einem lokalen Computer bereitstellen und debuggen. Visual Studio 2019 oder 2015 kann die Anwendung im lokalen Cluster bereitstellen und den Debugger automatisch mit allen Instanzen der Anwendung verbinden. Visual Studio muss als Administrator ausgeführt werden, um eine Verbindung mit dem Debugger herstellen zu können.

1. Um einen lokalen Cluster für die Entwicklung zu erstellen, folgen Sie den Schritten unter [Einrichten der Service Fabric-Entwicklungsumgebung](service-fabric-get-started.md).
2. Drücken Sie **F5** oder klicken Sie auf **Debuggen** > **Debugging starten**.
   
    ![Screenshot, der das Menü „Debuggen“ zeigt.][startdebugging]
3. Legen Sie Haltepunkte im Code fest und durchlaufen Sie die Anwendung mit den Befehlen im Menü **Debuggen** .
   
   > [!NOTE]
   > Visual Studio wird an alle Instanzen der Anwendung angefügt. Wenn Sie den Code durchlaufen, können Haltepunkte von mehreren Prozessen gleichzeitig erreicht werden, sodass es zu gleichzeitigen Sitzungen kommt. Versuchen Sie, die Haltepunkte nach dem Erreichen zu deaktivieren, indem Sie jeden Haltepunkt von der Thread-ID abhängig machen oder Diagnoseereignisse verwenden.
   > 
   > 
4. Das Fenster **Diagnoseereignisse** wird automatisch geöffnet, sodass Sie die Diagnoseereignisse in Echtzeit sehen können.
   
    ![Diagnoseereignisse in Echtzeit anzeigen][diagnosticevents]
5. Sie können das Fenster **Diagnoseereignisse** auch im Cloud-Explorer öffnen.  Klicken Sie unter **Service Fabric** mit der rechten Maustaste auf einen beliebigen Knoten, und wählen Sie **Streamingablaufverfolgungen anzeigen** aus.
   
    ![Fenster mit Diagnoseereignissen öffnen][viewdiagnosticevents]
   
    Wenn Sie Ihre Ablaufverfolgungen nach einem bestimmten Dienst oder einer bestimmten Anwendung filtern möchten, müssen Sie das Streaming von Ablaufverfolgungen in diesem Dienst oder dieser Anwendung aktivieren.
6. Sie können die Diagnoseereignisse in der automatisch generierten Datei **ServiceEventSource.cs** anzeigen und im Anwendungscode aufrufen.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Im Fenster **Diagnoseereignisse** können Ereignisse in Echtzeit gefiltert, angehalten und geprüft werden.  Der Filter ist eine einfache Zeichenfolgensuche in der Ereignismeldung und ihrem Inhalt.
   
    ![Ereignisse in Echtzeit filtern, anhalten und fortsetzen oder prüfen][diagnosticeventsactions]
8. Das Debuggen von Diensten ist vergleichbar mit dem Debuggen jeder beliebigen anderen Anwendung. Um das Debuggen zu vereinfachen, legen Sie in der Regel Haltepunkte in Visual Studio fest. Obwohl Reliable Collections in mehreren Knoten repliziert werden, wird dennoch IEnumerable implementiert. Diese Implementierung bedeutet, dass Sie beim Debuggen die Ergebnisansicht in Visual Studio verwenden können, um den darin gespeicherten Inhalt anzuzeigen. Legen Sie hierfür an einer beliebigen Stelle im Code einen Haltepunkt fest.
   
    ![Anwendung debuggen][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Ausführen eines Skripts beim Debuggen
In bestimmten Szenarien müssen Sie möglicherweise beim Start einer Debugsitzung ein Skript ausführen (wenn Sie z. B. keine Standarddienste verwenden).

In Visual Studio können Sie die Datei **Start-Service.ps1** im Ordner **Scripts** des Service Fabric-Anwendungsprojekts (.sfproj) hinzufügen. Dieses Skript wird aufgerufen, nachdem die Anwendung im lokalen Cluster erstellt wurde.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Debuggen einer Service Fabric-Remoteanwendung
Wenn Ihre Service Fabric-Anwendungen auf einem Service Fabric-Cluster in Azure ausgeführt werden, können Sie das Remotedebuggen hierfür direkt aus Visual Studio durchführen.

> [!NOTE]
> Die Funktion benötigt [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) und [Azure SDK für .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> Das Remotedebuggen ist für Entwicklungs-/Testszenarios vorgesehen und sollte aufgrund der Auswirkung auf ausgeführte Anwendungen nicht in Produktionsumgebungen verwendet werden.

1. Navigieren Sie im **Cloud-Explorer** zu Ihrem Cluster. Klicken Sie mit der rechten Maustaste, und wählen Sie **Debuggen aktivieren**.
   
    ![Aktivieren des Remotedebuggens][enableremotedebugging]
   
    Durch diese Aktion wird der Vorgang zum Aktivieren der Erweiterung zum Remotedebuggen auf Ihren Clusterknoten und für die benötigten Netzwerkkonfigurationen gestartet.
2. Klicken Sie im **Cloud-Explorer** mit der rechten Maustaste auf den Clusterknoten, und wählen Sie **Debugger anfügen** aus.
   
    ![Debugger anfügen][attachdebugger]
3. Wählen Sie im Dialogfeld **An den Prozess anfügen** den Prozess, den Sie debuggen möchten, und klicken Sie auf **Anfügen**.
   
    ![Prozess wählen][chooseprocess]
   
    Der Name des Prozesses, an den Sie anhängen möchten, ist derselbe Name wie Ihr Dienstprojekt-Assemblyname.
   
    Der Debugger wird an alle den Prozess ausführenden Knoten angehängt.
   
   * Falls Sie einen zustandslosen Dienst debuggen, sind alle Instanzen des Diensts auf allen Knoten Teil der Debugsitzung.
   * Falls Sie einen zustandsbehafteten Dienst debuggen, ist nur das primäre Replikat der jeweiligen Partition aktiv und wird somit vom Debugger behandelt. Falls das primäre Replikat während der Debugsitzung verschoben wird, ist die Verarbeitung dieses Replikats immer noch Teil der Debugsitzung.
   * Um nur relevante Partitionen oder Instanzen eines bestimmten Diensts abzufangen, können Sie bedingte Haltepunkte verwenden, damit nur eine bestimmte Partition oder Instanz unterbrochen wird.
     
     ![Bedingter Haltepunkt][conditionalbreakpoint]
     
     > [!NOTE]
     > Das Debuggen eines Service Fabric-Clusters mit mehreren Instanzen desselben Namens des ausführbaren Diensts wird derzeit nicht unterstützt.
     > 
     > 
4. Nach dem Debuggen Ihrer Anwendung können Sie die Erweiterung für das Remotedebuggen deaktivieren, indem Sie im **Cloud-Explorer** mit der rechten Maustaste auf den Cluster klicken und **Debuggen deaktivieren** auswählen.
   
    ![Deaktivieren des Remotedebuggen][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Streaming von Ablaufverfolgungen aus einem Remoteclusterknoten
Sie können Ablaufverfolgungen von einem Remoteclusterknoten aus auch direkt zu Visual Studio streamen. Diese Funktion ermöglicht es Ihnen, auf einem Service Fabric-Cluster erstellte ETW-Ablaufverfolgungsereignisse zu streamen.

> [!NOTE]
> Die Funktion benötigt [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) und [Azure SDK für .NET 2.9](https://azure.microsoft.com/downloads/).
> Diese Funktion unterstützt nur in Azure ausgeführte Cluster.
> 
> 

<!-- -->
> [!WARNING]
> Das Streaming von Ablaufverfolgungen ist für Entwicklungs-/Testszenarios vorgesehen und sollte aufgrund der Auswirkung auf ausgeführte Anwendungen nicht in Produktionsumgebungen verwendet werden.
> In einem Produktionsszenario sollten Sie sich auf das Weiterleiten von Ereignissen mithilfe von Azure-Diagnose verlassen.

1. Navigieren Sie im **Cloud-Explorer** zu Ihrem Cluster. Klicken Sie mit der rechten Maustaste, und wählen Sie **Streamingablaufverfolgungen aktivieren**.
   
    ![Aktivieren des Remotestreaming von Ablaufverfolgungen][enablestreamingtraces]
   
    Mit dieser Aktion wird der Vorgang zum Aktivieren der Erweiterung zum Streamen von Ablaufverfolgungen auf Ihren Clusterknoten sowie der benötigten Netzwerkkonfigurationen gestartet.
2. Erweitern Sie im **Cloud-Explorer** das **Knoten**-Element, klicken Sie mit der rechten Maustaste auf den Knoten, von dem aus Sie Ablaufverfolgungen streamen möchten, und wählen Sie **Streamingablaufverfolgungen anzeigen** aus.
   
    ![Remotestreaming von Ablaufverfolgungen anzeigen][viewremotestreamingtraces]
   
    Wiederholen Sie Schritt 2 für beliebig viele Knoten, von denen Sie Ablaufverfolgungen anzeigen möchten. Der Datenstrom jedes Knotens wird in einem dedizierten Fenster angezeigt.
   
    Sie können jetzt die von Service Fabric und Ihren Diensten ausgegebenen Ablaufverfolgungen sehen. Falls Sie die Ereignisse filtern möchten, damit nur eine bestimmte Anwendung angezeigt wird, geben Sie einfach den Namen der Anwendung in den Filter ein.
   
    ![Streaming von Ablaufverfolgungen anzeigen][viewingstreamingtraces]
3. Sobald Sie mit dem Streamen von Ablaufverfolgungen von Ihrem Cluster fertig sind, können Sie das Remotestreaming von Ablaufverfolgungen deaktivieren, indem Sie im **Cloud-Explorer** mit der rechten Maustaste auf Ihren Cluster klicken und **Streamingablaufverfolgungen deaktivieren** auswählen.
   
    ![Deaktivieren des Remotestreamingsvon Ablaufverfolgungen][disablestreamingtraces]

## <a name="next-steps"></a>Nächste Schritte
* [Testen eines Service Fabric-Diensts](service-fabric-testability-overview.md)
* [Verwalten von Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
