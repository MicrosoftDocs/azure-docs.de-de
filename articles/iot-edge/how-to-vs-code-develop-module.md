---
title: Entwickeln und Debuggen von Modulen für Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden von Visual Studio Code zum Entwickeln, Erstellen und Debuggen eines Moduls für Azure IoT Edge mithilfe von C#, Python, Node.js, Java oder C
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: ebc12e6d64d015267497497bebc22c8586adf999
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995912"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Verwenden von Visual Studio Code zum Entwickeln und Debuggen von Modulen für Azure IoT Edge

Sie können Ihre Geschäftslogik in Azure IoT Edge-Module umwandeln. In diesem Artikel wird veranschaulicht, wie Sie Visual Studio Code als wichtigstes Tool zum Entwickeln und Debuggen von Modulen verwenden.

Es gibt zwei Möglichkeiten zum Debuggen von Modulen, die in C#, Node.js oder Java geschrieben wurden, in Visual Studio Code: Sie können entweder einen Prozess in einem Modulcontainer anfügen oder den Modulcode im Debugmodus starten. Zum Debuggen von Modulen, die in Python oder C geschrieben wurden, können Sie nur einen Prozess in Linux-amd64-Containern anfügen.

Wenn Sie mit den Debugfunktionen von Visual Studio Code nicht vertraut sind, informieren Sie sich über [Debuggen](https://code.visualstudio.com/Docs/editor/debugging).

Dieser Artikel enthält Anweisungen zum Entwickeln und Debuggen von Modulen in mehreren Sprachen für mehrere Architekturen. Derzeit bietet Visual Studio Code Unterstützung für Module, die in C#, C, Python, Node.js und Java geschrieben wurden. Die unterstützten Gerätearchitekturen sind X64 und ARM32. Weitere Informationen zu unterstützten Betriebssystemen, Sprachen und Architekturen finden Sie unter [Sprach- und Architekturunterstützung](module-development.md#language-and-architecture-support).

>[!NOTE]
>Die Unterstützung für das Entwickeln und Debuggen von Linux-ARM64-Geräten befindet sich in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Weitere Informationen finden Sie unter [Develop and debug ARM64 IoT Edge modules in Visual Studio Code (preview) (Entwickeln und Debuggen von ARM64 IoT Edge-Modulen in Visual Studio Code (Vorschauversion))](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Voraussetzungen

Sie können einen Computer oder virtuellen Computer unter Windows, macOS oder Linux als Entwicklungscomputer verwenden. Auf Windows-Computern können Sie entweder Windows- oder Linux-Module entwickeln. Verwenden Sie zum Entwickeln von Windows-Modulen einen Windows-Computer, auf dem die Version 1809/Build 17763 oder höher ausgeführt wird. Verwenden Sie zum Entwickeln von Linux-Modulen einen Windows-Computer, der die [Anforderungen für Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) erfüllt.

Installieren Sie zuerst [Visual Studio Code](https://code.visualstudio.com/), und fügen Sie dann die folgenden Erweiterungen hinzu:

- [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker-Erweiterung](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Spezifische Visual Studio-Erweiterungen für die Sprache, die in der Sie entwickeln:
  - C#, einschließlich Azure Functions: [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Python-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java-Erweiterungspaket für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Ferner müssen Sie einige zusätzliche, sprachspezifische Tools installieren, um Ihr Modul zu entwickeln:

- C#, einschließlich Azure Functions: [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) und [Pip](https://pip.pypa.io/en/stable/installing/#installation) zum Installieren von Python-Paketen (normalerweise in Ihrer Python-Installation enthalten).

- Node.js: [Node.js](https://nodejs.org). Sie sollten außerdem [Yeoman](https://www.npmjs.com/package/yo) und den [Azure IoT Edge Node.js-Modulgenerator](https://www.npmjs.com/package/generator-azure-iot-edge-module) installieren.

- Java: [Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) und [Maven](https://maven.apache.org/). Sie müssen [die `JAVA_HOME`-Umgebungsvariable so festlegen](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/), dass sie auf Ihre JDK-Installation verweist.

Um Ihr Modulimage zu erstellen und bereitzustellen, müssen Sie Docker das Modulimage erstellen lassen und benötigen eine Containerregistrierung, die das Modulimage aufnimmt:

- [Docker Community Edition](https://docs.docker.com/install/) auf Ihrem Entwicklungscomputer.

- [Azure Container Registry](../container-registry/index.yml) oder [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Sie können anstelle einer Cloudregistrierung auch eine lokale Docker-Registrierung für Prototyp- bzw. Testzwecke verwenden.

Sofern Sie Ihr Modul nicht in C entwickeln, benötigen Sie außerdem das Python-basierte [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/), um Ihre lokale Entwicklungsumgebung für das Debuggen, Ausführen und Testen Ihrer IoT Edge-Lösung einzurichten. Wenn dies noch nicht erfolgt ist, installieren Sie [Python (2.7/3.6/3.7 oder höher) und Pip](https://www.python.org/), und installieren Sie anschließend **iotedgehubdev**, indem Sie diesen Befehl in Ihrem Terminal ausführen.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> „iotedgehubdev“ verwendet derzeit eine docker-py-Bibliothek, die nicht mit Python 3.8 kompatibel ist.
>
> Wenn Sie über mehrere Versionen von Python verfügen, einschließlich Python 2.7 (z. B. für Ubuntu oder macOS), sollten Sie sicherstellen, dass Sie das richtige `pip`- oder `pip3`-Programm zum Installieren von **iotedgehubdev** verwenden.

Zum Testen Ihres Moduls auf einem Gerät benötigen Sie einen aktiven IoT Hub mit mindestens einem IoT Edge-Gerät. Um Ihren Computer als IoT Edge-Gerät zu verwenden, führen Sie die Schritte in der Schnellstartanleitung für [Linux](quickstart-linux.md) oder [Windows](quickstart.md) aus. Wenn Sie IoT Edge-Daemon auf Ihrem Entwicklungscomputer ausführen, müssen Sie möglicherweise EdgeHub und EdgeAgent beenden, bevor Sie mit nächsten Schritt fortfahren.

## <a name="create-a-new-solution-template"></a>Erstellen einer neuen Lösungsvorlage

Die folgenden Schritte veranschaulichen, wie Sie ein IoT Edge-Modul in Ihrer bevorzugten Entwicklungssprache (einschließlich des in C# geschriebenen Azure Functions) mithilfe von Visual Studio Code und der Azure IoT-Tools erstellen. Als Erstes erstellen Sie eine Projektmappe, dann generieren Sie das erste Modul in dieser Projektmappe. Jede Projektmappe kann mehrere Module enthalten.

1. Wählen Sie **Ansicht** > **Befehlspalette** aus.

1. Geben Sie in der Befehlspalette den folgenden Befehl ein, und führen Sie ihn aus: **Azure IoT Edge: Neue IoT Edge-Projektmappe** ein.

   ![Ausführen einer neuen IoT Edge-Projektmappe](./media/how-to-develop-csharp-module/new-solution.png)

1. Navigieren Sie zu dem Ordner, in dem Sie die neue Projektmappe erstellen möchten, und wählen Sie dann **Ordner auswählen** aus.

1. Geben Sie einen Namen für die Lösung ein.

1. Wählen Sie als erstes Modul in der Projektmappe eine Modulvorlage für Ihre bevorzugte Entwicklungssprache aus.

1. Geben Sie einen Namen für Ihr Modul ein. Wählen Sie einen Namen, der in der Containerregistrierung eindeutig ist.

1. Geben Sie den Namen des Imagerepositorys des Moduls an. Visual Studio Code füllt den Modulnamen automatisch mit **localhost:5000/<Name Ihres Moduls\>** auf. Ersetzen Sie ihn durch Ihre eigenen Registrierungsinformationen. Wenn Sie eine lokale Docker-Registrierung zum Testen verwenden, können Sie **localhost** nutzen. Nutzen Sie den Anmeldeserver aus Ihren Registrierungseinstellungen, wenn Sie Azure Container Registry verwenden. Der Anmeldeserver hat das Format **_\<registry name\>_ .azurecr.io**. Ersetzen Sie nur den Teil **localhost:5000** der Zeichenfolge, damit das endgültige Ergebnis folgendes Format aufweist: **\<*registry name*\>.azurecr.io/ _\<your module name\>_**.

   ![Bereitstellen eines Docker-Imagerepositorys](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code verwendet die angegebenen Informationen, erstellt eine IoT Edge-Projektmappe, und lädt diese in einem neuen Fenster.

Es gibt vier Elemente in der Projektmappe:

- Einen Ordner **.vscode**, der Debugkonfigurationen enthält.

- Einen Ordner **modules**, der Unterordner für jedes Modul enthält.  Innerhalb der Ordner für die einzelnen Module befindet sich eine Datei namens **module.json**, die die Erstellung und Bereitstellung der Module steuert.  Diese Datei müsste bearbeitet werden, um die Containerregistrierung für die Modulbereitstellung von „localhost“ in eine Remoteregistrierung zu ändern. Zu diesem Zeitpunkt verfügen Sie über nur ein Modul.  Sie können aber über die Befehlspalette weitere hinzufügen. Verwenden Sie dazu den Befehl **Azure IoT Edge: Add IoT Edge Module** (Azure IoT Edge: IoT Edge-Modul hinzufügen) aus.

- Eine **.env**-Datei, in der Ihre Umgebungsvariablen aufgeführt werden. Wenn Azure Container Registry Ihre Registrierung ist, enthält sie einen Azure Container Registry-Benutzernamen und ein Kennwort.

  > [!NOTE]
  > Die Umgebungsdatei wird nur erstellt, wenn Sie ein Imagerepository für das Modul angeben. Wenn Sie die „localhost“-Standardeinstellungen zum lokalen Testen und Debuggen akzeptiert haben, müssen Sie keine Umgebungsvariablen deklarieren.

- Eine Datei **deployment.template.json**, in der Ihr neues Modul zusammen mit dem Beispielmodul **SimulatedTemperatureSensor**, mit dem Daten zum Testen simuliert werden, aufgeführt ist. Weitere Informationen zur Funktionsweise von Bereitstellungsmanifesten finden Sie unter [Bereitstellen von Modulen und Einrichten von Routen mithilfe von Bereitstellungsmanifesten](module-composition.md).

Der [Quellcode „SimulatedTemperatureSensor.csproj“](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor) veranschaulicht die Funktionsweise des Moduls für simulierte Temperatur.

## <a name="add-additional-modules"></a>Hinzufügen zusätzlicher Module

Um Ihrer Projektmappe zusätzliche Module hinzuzufügen, führen Sie den Befehl **Azure IoT Edge: IoT Edge-Modul hinzufügen** aus der Befehlspalette aus. Sie können aber auch mit der rechten Maustaste auf den Ordner **Module** oder auf die `deployment.template.json`-Datei in der Exploreransicht von Visual Studio Code klicken und dann **Zu IoT Edge-Module hinzufügen** auswählen.

## <a name="develop-your-module"></a>Entwickeln Ihres Moduls

Der Modulstandardcode, der zum Lieferumfang der Projektmappe gehört, befindet sich an diesem Speicherort:

- Azure Function (C#): **Module > *&lt;Name Ihres Moduls&gt;*  >  *&lt;Name Ihres Moduls&gt;* .cs**
- C#: **Module > *&lt;Name Ihres Moduls&gt;* > Program.cs**
- Python: **Module > *&lt;Name Ihres Moduls&gt;* > main.py**
- Node.js: **Module > *&lt;Name Ihres Moduls&gt;* > app.js**
- Java: **Module > *&lt;Name Ihres Moduls&gt;* > src > main > java > com > edgemodulemodules > App.java**
- C: **Module > *&lt;Name Ihres Moduls&gt;* > main.c**

Das Modul und die Datei „deployment.template.json“ werden so eingerichtet, dass Sie die Projektmappe erstellen, in Ihre Containerregistrierung verschieben und zum Starten des Tests für ein Gerät bereitstellen können, ohne Code ändern zu müssen. Das Modul ist so konzipiert, dass es einfach eine Eingabe aus einer Quelle akzeptiert (in diesem Fall das Daten simulierende „SimulatedTemperatureSensor“-Modul) und an den IoT Hub sendet.

Wenn Sie bereit sind, die Vorlage mit Ihrem eigenen Code anzupassen, erstellen Sie mit den [Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md) Module, die die wesentlichen Anforderungen für IoT-Lösungen wie Sicherheit, Geräteverwaltung und Zuverlässigkeit berücksichtigen.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Debuggen eines Moduls ohne einen Container (C#, Node.js, Java)

Wenn Sie in C#, Node.js oder Java entwickeln, ist für Ihr Modul die Verwendung eines **ModuleClient**-Objekts im Standardcode des Moduls erforderlich, damit es gestartet und ausgeführt werden und Nachrichten weiterleiten kann. Sie verwenden außerdem den Standardeingabekanal **input1** für Aktionen, wenn das Modul Nachrichten empfängt.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Einrichten des IoT Edge-Simulators für eine IoT Edge-Projektmappe

Auf dem Entwicklungscomputer müssen Sie zum Ausführen der IoT Edge-Projektmappe nicht den IoT Edge-Sicherheits-Daemon installieren, sondern können den IoT Edge-Simulator starten.

1. Klicken Sie im Geräte-Explorer auf der linken Seite mit der rechten Maustaste auf Ihre IoT Edge-Geräte-ID, und wählen Sie dann **IoT Edge Simulator einrichten** aus, um den Simulator ohne die Geräteverbindungszeichenfolge zu starten.
1. Sie können sehen, dass der IoT Edge-Simulator erfolgreich eingerichtet wurde, indem Sie die Verlaufsdetails im integrierten Terminal lesen.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Einrichten des IoT Edge-Simulators für die Einzelmodul-App

Um den Simulator einzurichten und zu starten, führen Sie den Befehl **Azure IoT Edge: Edge Hub-Simulator für Einzelmodul starten** in der Visual Studio Code-Befehlspalette aus. Wenn Sie aufgefordert werden, verwenden Sie den Wert **input1** aus dem Modulstandardcode (oder den entsprechenden Wert aus Ihrem Code) als Eingabenamen für Ihre Anwendung. Der Befehl öffnet das **iotedgehubdev**-CLI und startet anschließend den IoT Edge-Simulator und einen Modulcontainer eines Testhilfsprogramms. Die Ausgaben werden unten im integrierten Terminal angezeigt, wenn der Simulator im Einzelmodulmodus erfolgreich gestartet wurde. Angezeigt wird auch ein `curl`-Befehl, über den das Senden von Nachrichten erfolgen kann. Er wird später noch benötigt.

   ![Einrichten des IoT Edge-Simulators für die Einzelmodul-App](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Sie können die Docker Explorer-Ansicht in Visual Studio Code verwenden, um den Ausführungsstatus des Moduls zu sehen.

   ![Modulstatus im Simulator](media/how-to-develop-csharp-module/simulator-status.png)

   Der **EdgeHubDev**-Container ist das Kernstück des lokalen IoT Edge-Simulators. Er kann auf dem Entwicklungscomputer ohne IoT Edge-Sicherheits-Daemon ausgeführt werden und stellt Umgebungseinstellungen für Ihre native Modul-App oder Modulcontainer bereit. Der **input**-Container macht REST APIs verfügbar, um die Übermittlung von Nachrichten an den Zieleingabekanal Ihres Moduls zu unterstützen.

### <a name="debug-module-in-launch-mode"></a>Modul im Startmodus debuggen

1. Bereiten Sie Ihre Umgebung entsprechend den Anforderungen Ihrer Entwicklungssprache für das Debuggen vor, legen Sie einen Haltepunkt in Ihrem Modul fest, und wählen Sie die zu verwendende Debugkonfiguration aus:
   - **C#**
     - Führen Sie im integrierten Terminal von Visual Studio Code einen Verzeichniswechsel zum Ordner **_&lt;Name Ihres Moduls&gt;_* _ durch, und führen Sie dann den folgenden Befehl zum Erstellen der .NET Core-Anwendung aus.

       ```cmd
       dotnet build
       ```

     - Öffnen Sie die Datei `Program.cs`, und fügen Sie einen Haltepunkt hinzu.

     - Navigieren Sie zur Visual Studio Code-Debugansicht, indem Sie _*Ansicht > Debuggen** auswählen. Wählen Sie in der Dropdownliste die Debugkonfiguration **_ &lt;Name Ihres Moduls&gt;_ Local Debug (.NET Core)** aus.

        > [!NOTE]
        > Wenn Ihr .NET Core-`TargetFramework` nicht mit Ihrem Programmpfad in `launch.json` übereinstimmt, müssen Sie den Programmpfad in `launch.json` manuell aktualisieren, damit er zum `TargetFramework` in Ihrer CSPROJ-Datei passt, so dass Visual Studio Code dieses Programm erfolgreich starten kann.

   - **Node.js**
     - Führen Sie im integrierten Terminal von Visual Studio Code einen Verzeichniswechsel zum Ordner **_&lt;Name Ihres Moduls&gt;_* _ durch, und führen Sie dann den folgenden Befehl zum Installieren von Node-Paketen aus.

       ```cmd
       npm install
       ```

     - Öffnen Sie die Datei `app.js`, und fügen Sie einen Haltepunkt hinzu.

     - Navigieren Sie zur Visual Studio Code-Debugansicht, indem Sie _*Ansicht > Debuggen** auswählen. Wählen Sie in der Dropdownliste die Debugkonfiguration **_ &lt;Name Ihres Moduls&gt;_ Local Debug (Node.js)** aus.
   - **Java**
     - Öffnen Sie die Datei `App.java`, und fügen Sie einen Haltepunkt hinzu.

     - Navigieren Sie zur Visual Studio Code-Debugansicht, indem Sie **Ansicht > Debuggen** auswählen. Wählen Sie aus der Dropdownliste die Debugkonfiguration **_&lt;Name Ihres Moduls&gt;_ Local Debug (Java)** aus.

1. Klicken Sie auf **Debuggen starten**, oder drücken Sie **F5**, um die Debugsitzung zu starten.

1. Führen Sie im integrierten Visual Studio Code-Terminal den folgenden Befehl aus, um die Nachricht **Hallo Welt** an Ihr Modul zu senden. Dies ist der Befehl, der in vorherigen Schritten gezeigt wurde, wenn der IoT Edge-Simulator installiert wurde.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Wenn Sie Windows verwenden, achten Sie darauf, dass die Shell Ihres integrierten Terminals von Visual Studio Code **Git Bash** oder **WSL Bash** ist. Den `curl`-Befehl können Sie nicht aus einer PowerShell oder Eingabeaufforderung ausführen.
   > [!TIP]
   > Sie können auch [PostMan](https://www.getpostman.com/) oder andere API-Tools (anstelle von `curl`) zum Senden von Nachrichten verwenden.

1. In der Visual Studio Code-Debugansicht werden die Variablen im linken Bereich angezeigt.

1. Um Ihre Debugsitzung zu beenden, wählen Sie die Schaltfläche „Stopp“ aus, oder drücken Sie **UMSCHALT + F5**, und führen Sie dann **Azure IoT Edge: IoT Edge-Simulator beenden** in der Befehlspalette aus, um den Simulator zu beenden und zu bereinigen.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Debuggen im Anfügemodus mit IoT Edge Simulator (C#, Node.js, Java, Azure Functions)

Ihre Standardprojektmappe enthält zwei Module: ein Modul mit simuliertem Temperatursensor und ein Pipemodul. Der simulierte Temperatursensor sendet Nachrichten an das Pipemodul; anschließend werden die Nachrichten an den IoT Hub geleitet. In dem von Ihnen erstellten Modulordner gibt es mehrere Docker-Dateien für unterschiedliche Containertypen. Verwenden Sie eine der Dateien mit der Erweiterung **.debug**, um Ihr Modul zum Testen zu erstellen.

Aktuell wird Debuggen im Anfügemodus nur in folgender Weise unterstützt:

- C#-Module, einschließlich der für Azure Functions, unterstützen das Debuggen in Linux-amd64-Containern
- Node.js-Module unterstützen das Debuggen in Linux-amd64- und -arm32v7-Containern und in Windows-amd64-Containern
- Java-Module unterstützen das Debuggen in Linux-amd64- und -arm32v7-Containern

> [!TIP]
> Sie können zwischen den Optionen für die Standardplattform für ihre IoT Edge-Projektmappe umschalten, indem Sie in der Visual Studio Code-Statusleiste auf das Element klicken.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Einrichten des IoT Edge-Simulators für eine IoT Edge-Projektmappe

Auf dem Entwicklungscomputer brauchen Sie zum Ausführen der IoT Edge-Projektmappe nicht den IoT Edge-Sicherheits-Daemon zu installieren, sondern können den IoT Edge-Simulator starten.

1. Klicken Sie im Geräte-Explorer auf der linken Seite mit der rechten Maustaste auf Ihre IoT Edge-Geräte-ID, und wählen Sie dann **IoT Edge Simulator einrichten** aus, um den Simulator ohne die Geräteverbindungszeichenfolge zu starten.

1. Sie können sehen, dass der IoT Edge-Simulator erfolgreich eingerichtet wurde, indem Sie die Verlaufsdetails im integrierten Terminal lesen.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Erstellen und Ausführen eines Containers zum Debuggen und Debuggen im Modus „Anfügen“

1. Öffnen Sie Ihre Moduldatei (`Program.cs`, `app.js`, `App.java` oder `<your module name>.cs`), und fügen Sie einen Haltepunkt hinzu.

1. Klicken Sie in der Visual Studio Code-Exploreransicht mit der rechten Maustaste auf die `deployment.debug.template.json`-Datei für Ihre Projektmappe, und wählen Sie dann **IoT Edge-Projektmappe im Simulator erstellen und ausführen** aus. Im gleichen Fenster werden die Protokolle aller Modulcontainer angezeigt. Alternativ können Sie zur Docker-Ansicht navigieren, um den Containerstatus anzuzeigen.

   ![Beachten der Variablen](media/how-to-vs-code-develop-module/view-log.png)

1. Navigieren Sie zur Visual Studio Code-Debugansicht, und wählen Sie die Debugkonfigurationsdatei für Ihr Modul aus. Der Name der Debugoption sollte ähnlich lauten wie **_&lt;Name Ihres Moduls&gt;_ -Remotedebuggen**

1. Wählen Sie **Debuggen starten** aus, oder drücken Sie **F5**. Wählen Sie den Prozess zum Anfügen aus.

1. In der Visual Studio Code-Debugansicht werden die Variablen im linken Bereich angezeigt.

1. Um die Debugsitzung zu beenden, wählen Sie zunächst die Schaltfläche „Stopp“ aus, oder drücken Sie **UMSCHALT + F5**, und wählen Sie dann **Azure IoT Edge: IoT Edge-Simulator beenden** in der Befehlspalette aus.

> [!NOTE]
> Im obigen Beispiel wird veranschaulicht, wie Sie IoT Edge-Module in Containern debuggen. Dabei wurden den Containereinstellungen Ihres Moduls verfügbar gemachte Ports für `createOptions` hinzugefügt. Nachdem Sie das Debuggen Ihrer Module abgeschlossen haben, empfehlen wir Ihnen, diese verfügbar gemachten Ports für IoT Edge-Module, die für die Produktion bereit sind, zu entfernen.
>
> Für Module, die in C# geschrieben wurden, einschließlich Azure Functions, basiert dieses Beispiel auf der Debugversion von `Dockerfile.amd64.debug`, die den .NET Core-Befehlszeilendebugger (VSDBG) beim Erstellen in Ihr Containerimage einbindet. Nach dem Debuggen Ihrer C#-Module sollten Sie direkt das Dockerfile ohne VSDBG für produktionsbereite IoT Edge-Module verwenden.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Debuggen eines Moduls mit der IoT Edge-Runtime

In jedem Modulordner gibt es mehrere Docker-Dateien für unterschiedliche Containertypen. Verwenden Sie eine der Dateien mit der Erweiterung **.debug**, um Ihr Modul zum Testen zu erstellen.

Beim Debuggen von Modulen über diese Methode werden Ihre Module auf der obersten Ebene der IoT Edge-Runtime ausgeführt. Das IoT Edge-Gerät und Ihr Visual Studio Code können sich auf demselben Computer befinden. Meist befindet sich jedoch Visual Studio Code auf dem Entwicklungscomputer, und die IoT Edge-Runtime und die Module werden auf einem anderen physischen Computer ausgeführt. Für das Debuggen in Visual Studio Code müssen Sie folgende Aktionen ausführen:

- Richten Sie Ihr IoT Edge-Gerät ein, erstellen Sie IoT Edge-Module mit der Dockerfile **.debug**, und stellen Sie sie dann auf dem IoT Edge-Gerät bereit.
- Machen Sie die IP-Adresse und den Port des Moduls verfügbar, damit der Debugger angefügt werden kann.
- Aktualisieren Sie die Datei `launch.json`, damit Visual Studio Code an den Prozess im Container des Remotecomputers angefügt werden kann. Diese Datei befindet sich in Ihrem Arbeitsbereich im Ordner `.vscode` und wird bei jedem Hinzufügen ein neues Moduls, das Debuggen unterstützt, aktualisiert.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Erstellen und Bereitstellen Ihres Moduls auf dem IoT Edge-Gerät

1. Öffnen Sie in Visual Studio Code die `deployment.debug.template.json`-Datei, die die Debugversion Ihres Modulimages mit den korrekt festgelegten Werten für `createOptions` enthält.

1. Wenn Sie Ihr Modul in Python bereitstellen, führen Sie diese Schritte aus, bevor Sie fortfahren:
   - Öffnen Sie die Datei `main.py`, und fügen Sie hinter dem Importabschnitt diesen Code ein:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Fügen Sie in den Rückruf, den Sie debuggen möchten, die folgende einzelne Codezeile ein:

      ```python
      ptvsd.break_into_debugger()
      ```

     Wenn Sie beispielsweise die `receive_message_listener`-Funktion debuggen möchten, würden Sie diese Codezeile wie unten dargestellt einfügen:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. In der Visual Studio Code-Befehlspalette:
   1. Führen Sie den Befehl **Azure IoT Edge: IoT Edge-Projektmappe erstellen und pushen** aus.

   1. Wählen Sie die `deployment.debug.template.json`-Datei für Ihre Projektmappe aus.

1. Führen Sie im Abschnitt **Azure IoT Hub-Geräte** der Visual Studio Code-Exploreransicht Folgendes aus:
   1. Klicken Sie mit der rechten Maustaste auf eine IoT Edge-Geräte-ID, und wählen Sie dann **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen) aus.

      > [!TIP]
      > Vergewissern Sie sich, dass es sich bei dem ausgewählten Gerät um ein IoT Edge-Gerät handelt. Wählen Sie dazu das Gerät aus, um die Liste mit den Modulen zu erweitern, und vergewissern Sie sich, dass **$edgeHub** und **$edgeAgent** vorhanden sind. Jedes IoT Edge-Gerät verfügt über diese beiden Module.

   1. Navigieren Sie zum Ordner **config** Ihrer Projektmappe, wählen Sie die Datei `deployment.debug.amd64.json` und dann **Edge-Bereitstellungsmanifest auswählen** aus.

Sie sehen nun, dass die Bereitstellung erfolgreich mit einer Bereitstellungs-ID im integrierten Terminal erstellt wurde.

Sie können Ihren Containerstatus durch Ausführen des Befehls `docker ps` im Terminal überprüfen. Wenn Visual Studio Code und Ihre IoT Edge-Runtime auf demselben Computer ausgeführt werden, können Sie den Status auch in der Docker-Ansicht von Visual Studio Code überprüfen.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Verfügbarmachen der IP-Adresse und des Ports des Moduls für den Debugger

Sie können diesen Abschnitt überspringen, wenn Ihre Module auf demselben Computer wie Visual Studio Code ausgeführt werden, da Sie zum Anfügen an den Container localhost nutzen und in der Dockerfile **.debug**, den `createOptions`-Einstellungen für den Container des Moduls und der Datei `launch.json` bereits die richtigen Porteinstellungen vorliegen. Wenn Ihre Module und Visual Studio Code auf unterschiedlichen Computern ausgeführt werden, führen Sie die Schritte für Ihre Programmiersprache aus.

- **C#, einschließlich Azure Functions**

  [Konfigurieren Sie den SSH-Kanal auf Ihrem Entwicklungscomputer und IoT Edge-Gerät](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes), und bearbeiten Sie dann die anzufügende Datei `launch.json`.

- **Node.js**

  - Stellen Sie sicher, dass das Modul auf dem zu debuggenden Computer ausgeführt wird und für die anzufügenden Debugger bereit ist und dass auf Port 9229 extern zugegriffen werden kann. Sie können dies überprüfen, indem Sie `http://<target-machine-IP>:9229/json` auf dem Debuggercomputer öffnen. Diese URL sollte Informationen zum Node.js-Modul zeigen, das debuggt werden soll.
  
  - Öffnen Sie auf dem Entwicklungscomputer Visual Studio Code, und bearbeiten Sie die Datei `launch.json` so, dass der Adresswert des Profils **_&lt;Modulname&gt;_ Remotedebug (Node.js)** (oder des Profils **_&lt;Modulname&gt;_ Remotedebug (Node.js in Windows-Container)** , wenn das Modul als Windows-Container ausgeführt wird) die IP-Adresse des zu debuggenden Computers ist.

- **Java**

  - Erstellen Sie einen SSH-Tunnel zu dem zu debuggenden Computer, indem Sie `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` ausführen.
  
  - Öffnen Sie auf dem Entwicklungscomputer Visual Studio Code, und bearbeiten Sie das Profil **_&lt;Modulname&gt;_ Remotedebugging (Java)** in `launch.json`, damit Sie es an den Zielcomputer anfügen können. Weitere Informationen zum Bearbeiten von `launch.json` und zum Debuggen von Java mit Visual Studio Code finden Sie im Abschnitt zum [Konfigurieren des Debuggers](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Stellen Sie sicher, dass auf dem zu debuggenden Computer Port 5678 geöffnet und zugänglich ist.

  - Ändern Sie im Code `ptvsd.enable_attach(('0.0.0.0', 5678))`, den Sie zuvor in `main.py` eingefügt haben, **0.0.0.0** in die IP-Adresse des zu debuggenden Computers. Erstellen Sie Ihre IoT Edge-Module erneut, pushen Sie sie, und stellen Sie sie bereit.

  - Öffnen Sie auf dem Entwicklungscomputer Visual Studio Code, und bearbeiten Sie `launch.json` so, dass für den Wert `host` des Profils **_&lt;Modulname&gt;_ Remotedebugging (Python)** die IP-Adresse des Zielcomputers anstelle von `localhost` verwendet wird.

### <a name="debug-your-module"></a>Debuggen Ihres Moduls

1. Wählen Sie in der Visual Studio Code-Debugansicht die Debugkonfigurationsdatei für Ihr Modul aus. Der Name der Debugoption sollte ähnlich lauten wie **_&lt;Name Ihres Moduls&gt;_ -Remotedebuggen**

1. Öffnen Sie die Moduldatei für Ihre Entwicklungssprache, und fügen Sie einen Haltepunkt hinzu:

   - **Azure-Funktion (C#):** Fügen Sie der Datei `<your module name>.cs` Ihren Breakpoint hinzu.
   - **C#** : Fügen Sie der Datei `Program.cs` Ihren Breakpoint hinzu.
   - **Node.js**: Fügen Sie der Datei `app.js` Ihren Breakpoint hinzu.
   - **Java**: Fügen Sie der Datei `App.java` Ihren Breakpoint hinzu.
   - **Python**: Fügen Sie der Datei `main.py` Ihren Breakpoint in der Rückrufmethode hinzu, der Sie die Zeile `ptvsd.break_into_debugger()` hinzugefügt hatten.
   - **C**: Fügen Sie der Datei `main.c` Ihren Breakpoint hinzu.

1. Wählen Sie **Debuggen starten** oder **F5** aus. Wählen Sie den Prozess zum Anfügen aus.

1. In der Visual Studio Code-Debugansicht werden die Variablen im linken Bereich angezeigt.

> [!NOTE]
> Im obigen Beispiel wird veranschaulicht, wie Sie IoT Edge-Module in Containern debuggen. Dabei wurden den Containereinstellungen Ihres Moduls verfügbar gemachte Ports für `createOptions` hinzugefügt. Nachdem Sie das Debuggen Ihrer Module abgeschlossen haben, empfehlen wir Ihnen, diese verfügbar gemachten Ports für IoT Edge-Module, die für die Produktion bereit sind, zu entfernen.

## <a name="build-and-debug-a-module-remotely"></a>Erstellen und Debuggen eines Moduls per Remotezugriff

Dank kürzlicher Änderungen in der Docker- und Moby-Engine zur Unterstützung von SSH-Verbindungen und einer neuen Einstellung in Azure IoT-Tools, die die Einschleusung von Umgebungseinstellungen in die Visual Studio Code-Befehlspalette und Azure IoT Edge-Terminals ermöglicht, können Sie jetzt Module auf Remotegeräten erstellen und debuggen.

Weitere Informationen und schrittweise Anleitungen finden Sie in diesem [IoT Developer-Blogeintrag](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie das Modul erstellt haben, erfahren Sie, wie Sie [Azure IoT Edge-Module aus Visual Studio Code bereitstellen](how-to-deploy-modules-vscode.md).

Weitere Informationen zum Entwickeln Ihrer Module für Ihre IoT Edge-Geräte finden Sie unter [Verstehen und Verwenden von Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).