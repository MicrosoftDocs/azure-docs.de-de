---
title: Entwickeln von Java Azure Service Fabric-Anwendungen mit Visual Studio Code | Microsoft-Dokumentation
description: In diesem Artikel wird das Erstellen, Bereitstellen und Debuggen von Java Service Fabric-Anwendungen mit Visual Studio Code beschrieben.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 7f60371fb533526ef5bdb154d0c08dface9c0d1f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393955"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Entwickeln von Java Service Fabric-Anwendungen mit Visual Studio Code

Die [Service Fabric Reliable Services-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) erleichtert die Erstellung von Java Service Fabric-Anwendungen unter den Betriebssystemen Windows, Linux und macOS.

In diesem Artikel wird das Erstellen, Bereitstellen und Debuggen einer Java Service Fabric-Anwendung mit Visual Studio Code beschrieben.

> [!IMPORTANT]
> Service Fabric Java-Anwendungen können zwar auf Windows-Computern entwickelt, jedoch nur in Azure Linux-Clustern bereitgestellt werden. Das Debuggen von Java-Anwendungen wird unter Windows nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie VS Code, die Service Fabric Reliable Services-Erweiterung für VS Code sowie alle für Ihre Entwicklungsumgebung erforderlichen Abhängigkeiten bereits installiert haben. Weitere Informationen finden Sie unter [Erste Schritte](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Herunterladen des Beispiels
In diesem Artikel wird die Anwendung Voting im [GitHub-Repository aus dem Schnellstart-Beispiel der Service Fabric Java-Anwendung](https://github.com/Azure-Samples/service-fabric-java-quickstart) verwendet. 

Um das Repository auf Ihren Entwicklungscomputer zu klonen, führen Sie den folgenden Befehl über ein Terminalfenster (Befehlsfenster in Windows) aus:

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Öffnen der Anwendung in VS Code

Öffnen Sie Visual Studio Code.  Klicken Sie in der **Aktivitätsleiste** auf das Explorer-Symbol und dann auf **Ordner öffnen**, oder klicken Sie auf **Datei -> Ordner öffnen**. Navigieren Sie zum Verzeichnis *./service-fabric-java-quickstart/Voting* in dem Ordner, in den Sie das Repository geklont haben, und klicken Sie dann auf **OK**. Der Arbeitsbereich sollte die im folgenden Screenshot gezeigten Dateien enthalten.

![Java-Anwendung Voting im Arbeitsbereich](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Erstellen der Anwendung

1. Drücken Sie (STRG+UMSCHALT+P), um die **Befehlspalette** in VS Code zu öffnen.
2. Suchen Sie den Befehl **Service Fabric: Anwendung erstellen**, und wählen Sie ihn aus. Die Buildausgabe wird an das integrierte Terminal gesendet.

   ![Befehl „Anwendung erstellen“ in VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Bereitstellen der Anwendung im lokalen Cluster
Nachdem Sie die Anwendung erstellt haben, können Sie sie im lokalen Cluster bereitstellen. 

> [!IMPORTANT]
> Die Bereitstellung von Java-Anwendungen im lokalen Cluster wird auf Windows-Computern nicht unterstützt.

1. Wählen Sie in der **Befehlspalette** den Befehl **Service Fabric: Anwendung bereitstellen (Localhost)** aus. Die Ausgabe des Installationsvorgangs wird an das integrierte Terminal gesendet.

   ![Befehl „Anwendung bereitstellen“ in VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Starten Sie nach Abschluss der Bereitstellung einen Browser, und öffnen Sie Service Fabric Explorer: `http://localhost:19080/Explorer`. Es sollte angezeigt werden, dass die Anwendung ausgeführt wird. Dies kann einige Zeit dauern. Haben Sie daher etwas Geduld. 

   ![Anwendung Voting in Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Nachdem Sie sichergestellt haben, dass die Anwendung ausgeführt wird, starten Sie einen Browser. Öffnen Sie folgende Seite: `http://localhost:8080`. Hierbei handelt es sich um das Web-Front-End der Anwendung. Sie können Elemente hinzufügen und zum Abstimmen auf sie klicken.

   ![Anwendung Voting im Browser](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Um eine Anwendung aus einem Cluster zu entfernen, wählen Sie den Befehl **Service Fabric: Anwendung entfernen** aus der **Befehlspalette** aus. Die Ausgabe des Deinstallationsvorgangs wird an das integrierte Terminal gesendet. Mithilfe von Service Fabric Explorer können Sie überprüfen, ob die Anwendung aus dem lokalen Cluster entfernt wurde.

## <a name="debug-the-application"></a>Debuggen der Anwendung
Beim Debuggen von Anwendungen in VS Code muss die Anwendung in einem lokalen Cluster ausgeführt werden. Dem Code können Haltepunkte hinzugefügt werden.

> [!IMPORTANT]
> Das Debuggen von Java-Anwendungen wird auf Windows-Computern nicht unterstützt.

Gehen Sie wie folgt vor, um den VotingDataService und die Anwendung Voting auf das Debuggen vorzubereiten:

1. Aktualisieren Sie die Datei *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh*.
Kommentieren Sie den Befehl in Zeile 6 (mit '#') aus, und fügen Sie den folgenden Befehl am Ende der Datei hinzu:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Aktualisieren Sie die Datei *Voting/VotingApplication/ApplicationManifest.xml*. Legen Sie für die Attribute **MinReplicaSetSize** und **TargetReplicaSetSize** im Element **StatefulService** den Wert „1“ fest:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Klicken Sie in der **Aktivitätsleiste** auf das Symbol zum Debuggen, um die Debuggeransicht in VS Code zu öffnen. Klicken Sie auf das Zahnradsymbol am oberen Rand der Debuggeransicht, und wählen Sie im Dropdownmenü der Umgebung **Java** aus. Die Datei „launch.json“ wird geöffnet. 

   ![Debugsymbol im VS Code-Arbeitsbereich](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. Legen Sie in der Datei „launch.json“ für den Port in der Konfiguration mit dem Namen **Debug (Attach)** (Debuggen (anfügen)) den Wert **8001** fest. Speichern Sie die Datei .

   ![Debugkonfiguration für die Datei „launch.json“](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Stellen Sie die Anwendung für den lokalen Cluster mithilfe des Befehls **Service Fabric: Anwendung bereitstellen (Localhost)** bereit. Stellen Sie sicher, dass die Anwendung in Service Fabric Explorer ausgeführt wird. Ihre Anwendung kann jetzt gedebuggt werden.

Gehen Sie wie folgt vor, um einen Haltepunkt festzulegen:

1. Öffnen Sie in Explorer die Datei */Voting/VotingDataService/src/statefulservice/VotingDataService.java*. Legen Sie für die erste Codezeile im `try`-Block in der `addItem`-Methode (Zeile 80) einen Haltepunkt fest.
   
   ![Festlegen eines Haltepunkts in VotingDataService](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie Haltepunkte für ausführbare Codezeilen festlegen. Für Methodendeklarationen, `try`-Anweisungen oder `catch`-Anweisungen festgelegte Haltepunkte werden vom Debugger beispielsweise übersehen.
2. Klicken Sie in der **Aktivitätsleiste** auf das Debugsymbol, wählen Sie im Debugmenü die Konfiguration **Debug (Attach)** (Debuggen (anfügen)) aus, und klicken Sie auf die Schaltfläche zum Ausführen (grüner Pfeil), um mit dem Debuggen zu beginnen.

   ![Konfiguration „Debug (Attach)“ (Debuggen (anfügen))](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Wechseln Sie in einem Webbrowsers zu `http://localhost:8080`. Geben Sie im Textfeld ein neues Element ein, und klicken Sie auf **+ Hinzufügen**. Ihr Haltepunkt sollte erreicht werden. Mithilfe der Debug-Symbolleiste am oberen Rand von VS Code können Sie die Ausführung fortsetzen, Zeilen überspringen, in Methoden springen oder die aktuelle Methode verlassen. 
   
   ![Erreichen eines Haltepunkts](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Klicken Sie in der Debugsymbolleiste am oberen Rand von VS Code auf das Steckersymbol, um die Debugsitzung zu beenden.
   
   ![Trennen vom Debugger](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Wenn Sie das Debuggen abgeschlossen haben, können Sie die CounterService-Anwendung mit dem Befehl **Service Fabric: Anwendung entfernen** verwenden, um die Voting-Anwendung aus Ihrem lokalen Cluster zu entfernen. 

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Entwickeln und Debuggen von Service Fabric-C#-Anwendungen mit VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
