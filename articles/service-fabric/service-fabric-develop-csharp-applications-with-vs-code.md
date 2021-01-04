---
title: Entwickeln von .NET Core-Anwendungen mit Visual Studio Code
description: In diesem Artikel wird das Erstellen, Bereitstellen und Debuggen von .NET Core Service Fabric-Anwendungen mit Visual Studio Code beschrieben.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 5fbd523a38b3c4860316e45b8b7c03a17de19499
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678340"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Entwickeln von C# Service Fabric-Anwendungen mit Visual Studio Code

Die [Service Fabric Reliable Services-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) erleichtert die Erstellung von .NET Core Service Fabric-Anwendungen unter den Betriebssystemen Windows, Linux und macOS.

In diesem Artikel wird das Erstellen, Bereitstellen und Debuggen einer .NET Core Service Fabric-Anwendung mit Visual Studio Code beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie VS Code, die Service Fabric Reliable Services-Erweiterung für VS Code sowie alle für Ihre Entwicklungsumgebung erforderlichen Abhängigkeiten bereits installiert haben. Weitere Informationen finden Sie unter [Erste Schritte](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Herunterladen des Beispiels
In diesem Artikel wird die CounterService-Anwendung in den [GitHub-Repository-Beispielen für erste Schritte für Service Fabric .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) verwendet. 

Um das Repository auf Ihren Entwicklungscomputer zu klonen, führen Sie den folgenden Befehl über ein Terminalfenster (Befehlsfenster in Windows) aus:

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Öffnen der Anwendung in VS Code

### <a name="windows"></a>Windows
Klicken Sie im Menü „Start“ mit der rechten Maustaste auf das VS Code-Symbol, und klicken Sie auf **Als Administrator ausführen**. Um den Debugger an Ihre Dienste anzufügen, müssen Sie VS Code als Administrator ausführen.

### <a name="linux"></a>Linux
Navigieren Sie mithilfe des Terminals zum Pfad /service-fabric-dotnet-core-getting-started/Services/CounterService aus dem Verzeichnis, in das die Anwendung lokal geklont wurde.

Führen Sie den folgenden Befehl aus, um VS Code als Stammbenutzer zu öffnen, damit der Debugger Ihren Diensten beigefügt werden kann.
```
sudo code . --user-data-dir='.'
```

Die Anwendung wird jetzt in Ihrem VS Code-Arbeitsbereich angezeigt.

![Zählerdienstanwendung im Arbeitsbereich](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Erstellen der Anwendung
1. Drücken Sie (STRG+UMSCHALT+P), um die **Befehlspalette** in VS Code zu öffnen.
2. Suchen Sie den Befehl **Service Fabric: Anwendung erstellen** , und wählen Sie ihn aus. Die Buildausgabe wird an das integrierte Terminal gesendet.

   ![Befehl „Anwendungsbuild“ in VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Bereitstellen der Anwendung im lokalen Cluster
Nachdem Sie die Anwendung erstellt haben, können Sie sie im lokalen Cluster bereitstellen. 

1. Wählen Sie in der **Befehlspalette** den Befehl **Service Fabric: Anwendung bereitstellen (Localhost)** aus. Die Ausgabe des Installationsvorgangs wird an das integrierte Terminal gesendet.

   ![Befehl „Anwendung bereitstellen“ in VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Starten Sie nach Abschluss der Bereitstellung einen Browser, und öffnen Sie Service Fabric Explorer: \//localhost:19080/Explorer. Es sollte angezeigt werden, dass die Anwendung ausgeführt wird. Dies kann einige Zeit dauern. Haben Sie daher etwas Geduld. 

   ![Löschen der Zählerdienstanwendung in Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Nachdem Sie sichergestellt haben, dass die Anwendung ausgeführt wird, starten Sie einen Browser. Öffnen Sie folgende Seite: \//localhost:31002. Hierbei handelt es sich um das Web-Front-End der Anwendung. Aktualisieren Sie die Seite, um den aktuellen Wert des Zählers bei der Erhöhung anzuzeigen.

   ![Zählerdienstanwendung im Browser](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Veröffentlichen der Anwendung in einem Azure Service Fabric-Cluster
Zusammen mit der Bereitstellung der Anwendung im lokalen Cluster können Sie die Anwendung auch in einem Azure Service Fabric-Remotecluster veröffentlichen. 

1. Stellen Sie sicher, dass Sie die Anwendung mithilfe der oben beschriebenen Anweisungen erstellt haben. Aktualisieren Sie die generierte Konfigurationsdatei `Cloud.json` mit den Details des Remoteclusters, in dem Sie die Veröffentlichung durchführen möchten.

2. Wählen Sie in der **Befehlspalette** den Befehl **Service Fabric: Anwendung veröffentlichen** aus. Die Ausgabe des Installationsvorgangs wird an das integrierte Terminal gesendet.

   ![Befehl „Anwendung veröffentlichen“ in VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Starten Sie nach Abschluss der Bereitstellung einen Browser, und öffnen Sie Service Fabric Explorer: `https:<clusterurl>:19080/Explorer`. Es sollte angezeigt werden, dass die Anwendung ausgeführt wird. Dies kann einige Zeit dauern. Haben Sie daher etwas Geduld. 

## <a name="debug-the-application"></a>Debuggen der Anwendung
Beim Debuggen von Anwendungen in VS Code muss die Anwendung in einem lokalen Cluster ausgeführt werden. Dem Code können Haltepunkte hinzugefügt werden.

Gehen Sie wie folgt vor, um einen Haltepunkt festzulegen und zu debuggen:
1. Öffnen Sie im Explorer die Datei */src/CounterServiceApplication/CounterService/CounterService.cs* , und legen Sie einen Haltepunkt in Zeile 62 in der Methode `RunAsync` fest.
3. Klicken Sie in der **Aktivitätsleiste** auf das Symbol zum Debuggen, um die Debuggeransicht in VS Code zu öffnen. Klicken Sie auf das Zahnradsymbol am oberen Rand der Debuggeransicht, und wählen Sie im Dropdownmenü der Umgebung **.NET Core** aus. Die Datei „launch.json“ wird geöffnet. Sie können diese Datei schließen. Jetzt werden die Konfigurationsoptionen im Menü „Debugkonfiguration“ neben der Ausführungsschaltfläche (grüner Pfeil) angezeigt.

   ![Debugsymbol im VS Code-Arbeitsbereich](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Klicken Sie im Menü „Debugkonfiguration“ auf **.NET Core anfügen**.

   ![Screenshot mit ausgewählter Option „.NET Core anfügen“ im Menü „Debugkonfiguration“](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Öffnen Sie Service Fabric Explorer in einem Browser: http:\//localhost:19080/Explorer. Klicken Sie auf **Anwendungen** , und führen Sie einen Drilldown durch, um den primären Knoten zu ermitteln, auf dem der CounterService ausgeführt wird. In der Abbildung unten ist Knoten 0 der primäre Knoten für den CounterService.

   ![Primärer Knoten für CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. Klicken Sie in VS Code auf das Ausführungssymbol (grüner Pfeil) neben der Debugkonfiguration **.NET Core anfügen**. Klicken Sie im Prozessauswahldialogfeld auf den CounterService-Prozess, der auf dem primären Knoten ausgeführt wird, den Sie in Schritt 4 identifiziert haben.

   ![Primärer Prozess](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Der Haltepunkt in der Datei *CounterService.cs* wird sehr schnell erreicht. Sie können dann die Werte der lokalen Variablen untersuchen. Mithilfe der Debugsymbolleiste am oberen Rand von VS Code können Sie die Ausführung fortsetzen, Zeilen überspringen, in Methoden springen oder die aktuelle Methode verlassen. 

   ![Debuggen von Werten](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Klicken Sie in der Debugsymbolleiste am oberen Rand von VS Code auf das Steckersymbol, um die Debugsitzung zu beenden.
   
   ![Trennen vom Debugger](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Wenn Sie das Debuggen abgeschlossen haben, können Sie die CounterService-Anwendung mit dem Befehl **Service Fabric: Anwendung entfernen** aus Ihrem lokalen Cluster entfernen. 

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Develop and debug Java Service Fabric applications with VS Code (Entwickeln und Debuggen von Java Service Fabric-Anwendungen mit VS Code)](./service-fabric-develop-java-applications-with-vs-code.md).



