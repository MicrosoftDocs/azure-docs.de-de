---
title: Azure Service Fabric mit VS Code – Erste Schritte | Microsoft-Dokumentation
description: Dieser Artikel enthält einen Überblick über die Erstellung von Service Fabric-Anwendungen mit Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: f977a48338f784562ec84355aabb212e5a3dade4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60946569"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric für Visual Studio Code

Die [Service Fabric Reliable Services-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) erleichtert Erstellung und Debugging von Service Fabric-Anwendungen unter den Betriebssystemen Windows, Linux und macOS.

Dieser Artikel enthält einen Überblick über die Anforderungen und die Einrichtung der Erweiterung sowie die Verwendung der verschiedenen Befehle, die von der Erweiterung bereitgestellt werden. 

> [!IMPORTANT]
> Service Fabric Java-Anwendungen können zwar auf Windows-Computern entwickelt, jedoch nur in Azure Linux-Clustern bereitgestellt werden. Das Debuggen von Java-Anwendungen wird unter Windows nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen in allen Umgebungen installiert werden.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git-Client](https://git-scm.com/)
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman-Generatoren: Installieren Sie die entsprechenden Generatoren für Ihre Anwendung

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Die folgenden Voraussetzungen müssen für die Java-Entwicklung installiert werden:

* [Java SDK](https://aka.ms/azure-jdks) (Version 1.8)
* [Gradle](https://gradle.org/install/)
* [Debugger für Java VS Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Wird zum Debuggen von Java-Diensten benötigt. Das Debuggen von Java-Diensten wird nur unter Linux unterstützt. Sie können installieren, indem Sie auf das Symbol „Erweiterungen“ in der **Aktivitätsleiste** in VS Code klicken und die Erweiterung suchen, oder aber über den Visual Studio Code Marketplace.

Die folgenden Voraussetzungen müssen für die .NET Core/C#-Entwicklung installiert werden:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (ab Version 2.0.0)
* [C# für Visual Studio Code (unterstützt von OmniSharp) VS Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) Wird zum Debuggen von C#-Diensten benötigt. Sie können installieren, indem Sie auf „Erweiterungen“ in der **Aktivitätsleiste** in VS Code klicken und die Erweiterung suchen, oder aber über den Visual Studio Code Marketplace.

## <a name="setup"></a>Einrichtung

1. Öffnen Sie Visual Studio Code.
2. Klicken Sie in der **Aktivitätsleiste** am Rand von VS Code auf das Symbol „Debuggen“. Suchen Sie „Service Fabric“. Klicken Sie auf **Installieren** für die Service Fabric Reliable Services-Erweiterung.

## <a name="commands"></a>Befehle
Die Service Fabric Reliable Services-Erweiterung für VS Code bietet viele Befehle, mit denen Entwickler Service Fabric-Projekte erstellen und bereitstellen können. Sie können Befehle über die **Befehlspalette** aufrufen, indem Sie `(Ctrl + Shift + p)` drücken, den Namen des Befehls in die Eingabeleiste eingeben und den gewünschten Befehl in der Liste der Eingabeaufforderungen auswählen. 

* Service Fabric: Anwendung erstellen 
* Service Fabric: Anwendung veröffentlichen 
* Service Fabric: Anwendung bereitstellen 
* Service Fabric: Anwendung entfernen  
* Service Fabric: Anwendung erstellen 
* Service Fabric: Anwendung bereinigen 

### <a name="service-fabric-create-application"></a>Service Fabric: Anwendung erstellen

Der Befehl **Service Fabric: Anwendung erstellen** erstellt eine neue Service Fabric-Anwendung in Ihrem aktuellen Arbeitsbereich. Je nachdem, welche Yeoman-Generatoren auf dem Entwicklungscomputer installiert sind, können Sie mehrere Typen von Service Fabric-Anwendungen erstellen, darunter Java-, C#-, Container- und Gastprojekte. 

1.  Wählen Sie den Befehl **Service Fabric: Dienst hinzufügen** aus.
2.  Wählen Sie den Typ für die neue Service Fabric-Anwendung aus. 
3.  Geben Sie den Namen der Anwendung ein, die Sie erstellen möchten
3.  Wählen Sie den Typ des Diensts aus, den Sie der Service Fabric-Anwendung hinzufügen möchten. 
4.  Folgen Sie den Eingabeaufforderungen, um den Dienst zu benennen. 
5.  Die neue Service Fabric-Anwendung wird im Arbeitsbereich angezeigt.
6.  Öffnen Sie den neuen Anwendungsordner, sodass dieser der Stammordner des Arbeitsbereichs wird. Sie können die Ausführung von Befehlen hier fortsetzen.

### <a name="service-fabric-add-service"></a>Service Fabric: Dienst hinzufügen
Der Befehl **Service Fabric: Dienst hinzufügen** fügt einer vorhandenen Service Fabric-Anwendung einen neuen Dienst hinzu. Die Anwendung, der der Dienst hinzugefügt wird, muss das Stammverzeichnis des Arbeitsbereichs sein. 

1.  Wählen Sie den Befehl **Service Fabric: Dienst hinzufügen** aus.
2.  Wählen Sie den Typ der aktuellen Service Fabric-Anwendung aus. 
3.  Wählen Sie den Typ des Diensts aus, den Sie der Service Fabric-Anwendung hinzufügen möchten. 
4.  Folgen Sie den Eingabeaufforderungen, um den Dienst zu benennen. 
5.  Der neue Dienst wird in Ihrem Projektverzeichnis angezeigt. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Anwendung veröffentlichen
Der Befehl **Service Fabric: Anwendung veröffentlichen** stellt die Service Fabric-Anwendung auf einem Remotecluster bereit. Der Zielcluster kann ein sicherer oder ein unsicherer Cluster sein. Wenn die Parameter nicht in der Datei Cloud.json festgelegt sind, wird die Anwendung im lokalen Cluster bereitgestellt.

1.  Beim ersten Build der Anwendung wird die Datei Cloud.json im Projektverzeichnis generiert.
2.  Geben Sie die Werte für den Cluster, mit dem Sie eine Verbindung herstellen möchten, in die Datei Cloud.json ein.
3.  Wählen Sie den Befehl **Service Fabric: Anwendung veröffentlichen** aus.
4.  Zeigen Sie den Zielcluster mit Service Fabric Explorer an, um zu bestätigen, dass die Anwendung installiert wurde. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Anwendung bereitstellen (Localhost)
Der Befehl **Service Fabric: Anwendung bereitstellen** stellt die Service Fabric-Anwendung im lokalen Cluster bereit. Stellen Sie sicher, dass der lokale Cluster ausgeführt wird, bevor Sie den Befehl verwenden. 

1. Wählen Sie den Befehl **Service Fabric: Anwendung bereitstellen** aus.
2. Zeigen Sie den lokalen Cluster mit Service Fabric Explorer (http:\//localhost:19080/Explorer) an, um zu bestätigen, dass die Anwendung installiert wurde. Dies kann einige Zeit dauern. Haben Sie daher etwas Geduld.
3. Sie können auch den Befehl **Service Fabric: Anwendung veröffentlichen** ohne in der Datei „Cloud.json“ festgelegte Parameter verwenden, um einen lokalen Cluster bereitzustellen.

> [!NOTE]
> Die Bereitstellung von Java-Anwendungen im lokalen Cluster wird auf Windows-Computern nicht unterstützt.

### <a name="service-fabric-remove-application"></a>Service Fabric: Anwendung entfernen
Der Befehl **Service Fabric: Anwendung entfernen** entfernt eine Service Fabric-Anwendung aus dem Cluster, in dem sie zuvor mit der VS Code-Erweiterung bereitgestellt wurde. 

1.  Wählen Sie den Befehl **Service Fabric: Anwendung entfernen** aus.
2.  Zeigen Sie den lokalen Cluster mit Service Fabric Explorer an, um zu bestätigen, dass die Anwendung entfernt wurde. Dies kann einige Zeit dauern. Haben Sie daher etwas Geduld.

### <a name="service-fabric-build-application"></a>Service Fabric: Anwendung erstellen
Der Befehl **Service Fabric: Anwendung erstellen** kann Java- oder C#-Service Fabric-Anwendungen erstellen. 

1.  Stellen Sie sicher, dass Sie sich im Anwendungsstammordner befinden, wenn Sie den Befehl ausführen. Der Befehl weist den Typ der Anwendung (C#- oder Java) aus und erstellt Ihren Anwendungsbuild entsprechend.
2.  Wählen Sie den Befehl **Service Fabric: Anwendung erstellen**, und wählen Sie ihn aus.
3.  Die Ausgabe des Installationsvorgangs wird in das integrierte Terminal geschrieben.

### <a name="service-fabric-clean-application"></a>Service Fabric: Anwendung bereinigen
Der Befehl **Service Fabric: Anwendung bereinigen** löscht alle JAR-Dateien und nativen Bibliotheken, die vom Build generiert wurden. Dies gilt für nur Java-Anwendungen. 

1.  Stellen Sie sicher, dass Sie sich im Anwendungsstammordner befinden, wenn Sie den Befehl ausführen. 
2.  Wählen Sie den Befehl **Service Fabric: Anwendung bereinigen** aus.
3.  Die Ausgabe des Bereinigungsvorgangs wird in das integrierte Terminal geschrieben.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Entwickeln und Debuggen von Service Fabric-C#-Anwendungen mit VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Weitere Informationen finden Sie unter [Develop and debug Java Service Fabric applications with VS Code (Entwickeln und Debuggen von Java Service Fabric-Anwendungen mit VS Code)](./service-fabric-develop-java-applications-with-vs-code.md).
