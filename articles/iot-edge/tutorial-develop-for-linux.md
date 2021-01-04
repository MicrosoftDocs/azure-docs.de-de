---
title: 'Tutorial: Entwickeln eines Moduls für Linux-Geräte mithilfe von Azure IoT Edge'
description: In diesem Tutorial wird Schritt für Schritt das Einrichten Ihres Entwicklungscomputers und der Cloudressourcen für die Entwicklung von IoT Edge-Modulen mit Linux-Containern für Linux-Geräte beschrieben.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: b352bd92ecc69ca68a6870d3a59ef5e0cdd1daba
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920852"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Tutorial: Entwickeln von IoT Edge-Modulen für Linux-Geräte

Nutzen Sie Visual Studio Code, um Code zu entwickeln und auf Linux-Geräten bereitzustellen, auf denen IoT Edge ausgeführt wird.

In der Schnellstartanleitung haben Sie ein IoT Edge-Gerät mit einem virtuellen Linux-Computer erstellt und über Azure Marketplace ein Modul bereitgestellt. In diesem Tutorial werden die Entwicklung und Bereitstellung Ihres eigenen Codes auf einem IoT Edge-Gerät beschrieben. Dieser Artikel ist eine nützliche Ausgangsbasis für die anderen Tutorials, in denen bestimmte Programmiersprachen oder Azure-Dienste ausführlicher behandelt werden.

In diesem Tutorial wird das Beispiel für die Bereitstellung eines **C#-Moduls auf einem Linux-Gerät** verwendet. Dieses Beispiel wurde gewählt, weil es ein sehr gängiges Szenario für Entwickler von IoT Edge-Lösungen ist. Auch wenn Sie die Verwendung einer anderen Sprache oder die Bereitstellung eines Azure-Diensts planen, ist dieses Tutorial dennoch nützlich, um mehr über Entwicklungstools und -konzepte zu erfahren. Nach dieser Einführung in den Entwicklungsprozess können Sie dann Ihre bevorzugte Sprache oder den Azure-Dienst wählen, um sich in die Details zu vertiefen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Einrichten Ihres Entwicklungscomputers
> * Verwenden der IoT Edge-Tools für Visual Studio Code zum Erstellen eines neuen Projekts
> * Erstellen Ihres Projekts als Container und Speichern in einer Azure-Containerregistrierung
> * Bereitstellen Ihres Codes auf einem IoT Edge-Gerät

## <a name="prerequisites"></a>Voraussetzungen

Ein Entwicklungscomputer:

* Sie können je nach Ihren Präferenzen bei der Entwicklung Ihren eigenen Computer oder einen virtuellen Computer verwenden.
  * Stellen Sie sicher, dass Ihr Entwicklungscomputer geschachtelte Virtualisierung unterstützt. Diese Funktion ist zum Ausführen einer Containerengine erforderlich, die Sie im nächsten Abschnitt installieren.
* Die meisten Betriebssysteme, unter denen eine Container-Engine ausgeführt werden kann, können zum Entwickeln von IoT Edge-Modulen für Linux-Geräte genutzt werden. In diesem Tutorial wird ein Windows-Computer verwendet, aber es wird auf die bekannten Unterschiede unter macOS oder Linux hingewiesen.
* Installieren Sie [Git](https://git-scm.com/), damit Sie später im Tutorial die Pakete mit den Modulvorlagen per Pullvorgang abrufen können.  
* [Erweiterung C# for Visual Studio Code mit Unterstützung von OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

Ein Azure IoT Edge-Gerät unter Linux:

* Wir empfehlen Ihnen, IoT Edge nicht auf Ihrem Entwicklungscomputer auszuführen, sondern ein separates Gerät zu verwenden. Diese Unterscheidung zwischen Entwicklungscomputer und IoT Edge-Gerät entspricht eher einem realen Bereitstellungsszenario und trägt dazu bei, die unterschiedlichen Bereiche auseinanderzuhalten.
* Gehen Sie wie folgt vor, wenn Ihnen kein zweites Gerät zur Verfügung steht: Verwenden Sie den Schnellstartartikel, um mit einem [virtuellen Linux-Computer](quickstart-linux.md) ein IoT Edge-Gerät in Azure zu erstellen.

Cloudressourcen:

* Einen [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Azure im Tarif „Free“ oder „Standard“.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Wichtige Begriffe

In diesem Tutorial wird Schritt für Schritt die Entwicklung eines IoT Edge-Moduls beschrieben. Ein *IoT Edge-Modul* (auch kurz als *Modul* bezeichnet) ist ein Container mit ausführbarem Code. Sie können ein oder mehrere Module auf einem IoT Edge-Gerät bereitstellen. Mit Modulen werden bestimmte Aufgaben durchgeführt, z. B. das Erfassen der Daten von Sensoren, das Bereinigen oder Analysieren von Daten oder das Senden von Nachrichten an einen IoT-Hub. Weitere Informationen finden Sie unter [Grundlegendes zu Azure IoT Edge-Modulen](iot-edge-modules.md).

Beim Entwickeln von IoT Edge-Modulen ist es wichtig, dass Sie den Unterschied zwischen dem Entwicklungscomputer und dem IoT Edge-Zielgerät verstehen, auf dem das Modul später bereitgestellt wird. Der Container, den Sie für Ihren Modulcode erstellen, muss mit dem Betriebssystem (BS) des *Zielgeräts* übereinstimmen. Am häufigsten kommt beispielsweise ein Szenario vor, bei dem ein Modul auf einem Windows-Computer entwickelt wird, das für ein Linux-Gerät mit IoT Edge bestimmt ist. In diesem Fall wird als Betriebssystem für den Container Linux verwendet. Denken Sie beim Durcharbeiten dieses Tutorials an den Unterschied zwischen dem *Betriebssystem des Entwicklungscomputers* und dem *Betriebssystem des Containers*.

In diesem Tutorial geht es um Linux-Geräte, auf denen IoT Edge ausgeführt wird. Sie können Ihr bevorzugtes Betriebssystem verwenden, solange auf Ihrem Entwicklungscomputer Linux-Container ausgeführt werden. Es wird empfohlen, Visual Studio Code für die Entwicklung für Linux-Geräte zu nutzen, und dies ist auch die Vorgehensweise in diesem Tutorial. Alternativ können Sie Visual Studio nutzen, aber zwischen den beiden Tools gibt es Unterschiede in Bezug auf die Unterstützung.

In der folgenden Tabelle sind die unterstützten Entwicklungsszenarien für **Linux-Container** in Visual Studio Code und Visual Studio aufgeführt.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Architektur des Linux-Geräts** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure-Dienste** | Azure-Funktionen <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Sprachen** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Weitere Informationen** | [Azure IoT Edge für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-Tools für Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge-Tools für Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Unterstützung für Linux ARM64-Geräte ist in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verfügbar. Weitere Informationen finden Sie unter [Develop and debug ARM64 IoT Edge modules in Visual Studio Code (preview) (Entwickeln und Debuggen von ARM64 IoT Edge-Modulen in Visual Studio Code (Vorschauversion))](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

In diesem Tutorial werden die Entwicklungsschritte für Visual Studio Code beschrieben. Falls Sie Visual Studio bevorzugen, hilft Ihnen die Anleitung unter [Entwickeln und Debuggen von Modulen für Azure IoT Edge (Vorschauversion) mithilfe von Visual Studio 2017](how-to-visual-studio-develop-module.md) weiter.
## <a name="install-container-engine"></a>Installieren der Container-Engine

Da IoT Edge-Module als Container verpackt werden, benötigen Sie auf Ihrem Entwicklungscomputer eine Container-Engine zum Erstellen und Verwalten der Container. Wir empfehlen Docker Desktop für die Entwicklung, da diese beliebte Anwendung zahlreiche Features unterstützt. Mit Docker Desktop unter Windows können Sie zwischen Linux-Containern und Windows-Containern wechseln, damit Sie auf einfache Weise für unterschiedliche Arten von IoT Edge-Geräten Module entwickeln können.

Verwenden Sie die Docker-Dokumentation für die Installation auf Ihrem Entwicklungscomputer:

* [Install Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/) (Installieren von Docker Desktop für Windows)

  * Bei der Installation von Docker Desktop für Windows werden Sie gefragt, ob Sie Linux- oder Windows-Container nutzen möchten. Sie können diese Entscheidung jederzeit ändern, indem Sie eine einfache Umschaltoption verwenden. In diesem Tutorial verwenden wir Linux-Container, weil unsere Module für Linux-Geräte bestimmt sind. Weitere Informationen finden Sie unter [Switch between Windows and Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) (Umschalten zwischen Windows- und Linux-Containern).

* [Install Docker Desktop for Mac](https://docs.docker.com/docker-for-mac/install/) (Installieren von Docker Desktop für Mac)

* Unter [About Docker CE](https://docs.docker.com/install/) (Informationen zu Docker CE) finden Sie Informationen zur Installation auf mehreren Linux-Plattformen.
  * Installieren Sie für das Windows-Subsystem für Linux (WSL) Docker Desktop für Windows.

## <a name="set-up-vs-code-and-tools"></a>Einrichten von VS Code und Tools

Verwenden Sie die IoT-Erweiterungen für Visual Studio Code zum Entwickeln von IoT Edge-Modulen. Diese Erweiterungen dienen zum Bereitstellen von Projektvorlagen, Automatisieren der Erstellung des Bereitstellungsmanifests und Ermöglichen der Überwachung und Verwaltung von IoT Edge-Geräten. In diesem Abschnitt installieren Sie Visual Studio Code und die IoT-Erweiterung und richten dann Ihr Azure-Konto ein, um IoT Hub-Ressourcen über Visual Studio Code zu verwalten.

1. Installieren Sie [Visual Studio Code](https://code.visualstudio.com/) auf Ihrem Entwicklungscomputer.

2. Wählen Sie nach Abschluss der Installation **Ansicht** > **Erweiterungen**.

3. Suchen Sie nach **Azure IoT Tools**. Hierbei handelt es sich eigentlich um eine Sammlung mit Erweiterungen, die Ihnen als Unterstützung beim Interagieren mit IoT Hub und IoT-Geräten sowie beim Entwickeln von IoT Edge-Modulen dienen.

4. Wählen Sie **Installieren** aus. Jede enthaltene Erweiterung wird einzeln installiert.

5. Öffnen Sie nach der Installation der Erweiterungen die Befehlspalette, indem Sie **Ansicht** > **Befehlspalette** wählen.

6. Suchen Sie in der Befehlspalette nach dem Befehl **Azure: Sign in** (Azure: Anmelden), und führen Sie ihn aus. Folgen Sie den Aufforderungen auf dem Bildschirm, um sich an Ihrem Azure-Konto anzumelden.

7. Wählen Sie in der Befehlspalette den Befehl **Azure IoT Hub: IoT Hub auswählen** aus. Folgen Sie den Aufforderungen, um Ihr Azure-Abonnement und den IoT-Hub auszuwählen.

8. Öffnen Sie den Explorer-Abschnitt von Visual Studio Code, indem Sie entweder das Symbol in der Aktivitätsleiste auf der linken Seite oder **Ansicht** > **Explorer** wählen.

9. Erweitern Sie unten im Explorer-Abschnitt das reduzierte Menü **Azure IoT Hub / Devices** (Azure IoT Hub/Geräte). Es sollten die Geräte und IoT Edge-Geräte angezeigt werden, die dem über die Befehlspalette ausgewählten IoT-Hub zugeordnet sind.

   ![Anzeigen von Geräten in Ihrem IoT-Hub](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Erstellen eines neuen Modulprojekts

Mit der Erweiterung „Azure IoT Tools“ werden Projektvorlagen für alle unterstützten IoT Edge-Modulsprachen in Visual Studio Code bereitgestellt. Diese Vorlagen verfügen über alle erforderlichen Dateien und den Code zum Bereitstellen eines funktionierenden Moduls für einen IoT Edge-Test. Darüber hinaus dienen Sie Ihnen als Ausgangspunkt zum Anpassen der Vorlage mit Ihrer eigenen Geschäftslogik.

Für dieses Tutorial nutzen wir die C#-Modulvorlage, weil sie die am häufigsten verwendete Vorlage ist.

### <a name="create-a-project-template"></a>Erstellen einer Projektvorlage

Wählen Sie in der Befehlspalette von Visual Studio Code die Option **Azure IoT Edge: Neue IoT Edge-Projektmappe** ein. Folgen Sie den Aufforderungen, und verwenden Sie die folgenden Werte, um Ihre Projektmappe zu erstellen:

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein, oder übernehmen Sie den Standardnamen **EdgeSolution**. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **C#-Modul** aus. |
   | Provide a module name (Modulname angeben) | Übernehmen Sie den Standardnamen **SampleModule**. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Für das Containerimage wird der von Ihnen im vorherigen Schritt angegebene Name übernommen. Ersetzen Sie **localhost:5000** durch den Wert für **Anmeldeserver** aus Ihrer Azure-Containerregistrierung. Den Wert für „Anmeldeserver“ finden Sie im Azure-Portal auf der Seite „Übersicht“ Ihrer Containerregistrierung. <br><br> Das endgültige Imagerepository sieht so aus: \<registry name\>.azurecr.io/samplemodule. |

   ![Bereitstellen eines Docker-Imagerepositorys](./media/tutorial-develop-for-linux/image-repository.png)

Nehmen Sie sich nach dem Laden Ihrer neuen Projektmappe im Visual Studio Code-Fenster etwas Zeit, um sich mit den erstellten Dateien vertraut zu machen:

* Der Ordner **.vscode** enthält eine Datei mit dem Namen **launch.json**, die zum Debuggen von Modulen verwendet wird.
* Der Ordner **modules** enthält einen Ordner für jedes Modul Ihrer Projektmappe. Derzeit sollte nur **SampleModule** vorhanden sein (bzw. der Ordner mit dem Namen, den Sie dem Modul gegeben haben). Der Ordner „SampleModule“ enthält den Hauptprogrammcode, die Modulmetadaten und mehrere Docker-Dateien.
* Die **.env**-Datei enthält die Anmeldeinformationen für Ihre Containerregistrierung. Diese Anmeldeinformationen werden auch für Ihr IoT Edge-Gerät genutzt, damit Zugriff zum Abrufen der Containerimages per Pullvorgang besteht.
* Die Datei **deployment.debug.template.json** und die Datei **deployment.template.json** sind Vorlagen, mit denen Sie ein Bereitstellungsmanifest erstellen können. Ein *Bereitstellungsmanifest* ist eine Datei, mit der Folgendes genau definiert wird: welche Module auf einem Gerät bereitgestellt werden sollen, wie sie konfiguriert werden sollen und wie diese miteinander und mit der Cloud kommunizieren können. In den Vorlagendateien werden für einige Werte Zeiger verwendet. Wenn Sie die Vorlage in ein reales Bereitstellungsmanifest transformieren, werden die Zeiger durch die Werte aus anderen Projektmappendateien ersetzt. Suchen Sie in Ihrer Bereitstellungsvorlage nach den beiden allgemeinen Platzhaltern:

  * Im Abschnitt mit den Anmeldeinformationen für die Registrierung wird die Adresse automatisch anhand der Informationen eingefügt, die Sie beim Erstellen der Projektmappe angegeben haben. Der Benutzername und das Kennwort basieren aber auf den Variablen, die in der ENV-Datei gespeichert sind. Diese Konfiguration dient der Sicherheit, da die ENV-Datei von Git ignoriert wird, während dies für die Bereitstellungsvorlage nicht der Fall ist.
  * Im Abschnitt „SampleModule“ wird das Containerimage nicht eingefügt, obwohl Sie das Repository für das Image beim Erstellen der Projektmappe angegeben haben. Dieser Platzhalter verweist auf die Datei **module.json** im Ordner „SampleModule“. Wenn Sie auf diese Datei zugreifen, sehen Sie, dass das Repository im Imagefeld enthalten ist. Es wird aber auch ein Tagwert angezeigt, der aus der Version und der Plattform des Containers besteht. Sie können die Version im Rahmen des Entwicklungszyklus manuell durchlaufen und wählen die Containerplattform mit einem Umschalter aus, der weiter unten in diesem Abschnitt beschrieben wird.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Angeben Ihrer Anmeldeinformationen für die Registrierung über den IoT Edge-Agent

Die Umgebungsdatei speichert die Anmeldeinformationen für Ihre Containerregistrierung und gibt sie an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre Containerimages per Pullvorgang auf das IoT Edge-Gerät zu übertragen.

>[!NOTE]
>Wenn Sie im Schritt [**Erstellen einer Projektvorlage**](#create-a-project-template) den Wert **localhost:5000** nicht durch den Wert des Anmeldeservers aus Ihrer Azure-Containerregistrierung ersetzt haben, fehlen die Datei vom Typ **.env** und der Abschnitt „registryCredentials“ des Bereitstellungsmanifests. 

Die IoT Edge-Erweiterung versucht, Ihre Anmeldeinformationen für die Containerregistrierung per Pullvorgang aus Azure abzurufen und in die Umgebungsdatei einzufügen. Überprüfen Sie, ob Ihre Anmeldeinformationen bereits enthalten sind. Fügen Sie sie jetzt hinzu, wenn dies nicht der Fall ist:

1. Öffnen Sie in Ihrer Modulprojektmappe die **.env**-Datei.
2. Fügen Sie die Werte für **Benutzername** und **Kennwort** hinzu, die Sie aus der Azure-Containerregistrierung kopiert haben.
3. Speichern Sie die Änderungen an der ENV-Datei.

### <a name="select-your-target-architecture"></a>Auswählen Ihrer Zielarchitektur

Mit Visual Studio Code können derzeit C#-Module für Linux AMD64- und ARM32v7-Geräte entwickelt werden. Sie müssen bei jeder Projektmappe auswählen, welche Architektur Sie als Ziel verwenden möchten, weil sich dies darauf auswirkt, wie der Container erstellt und ausgeführt wird. Der Standardwert ist „Linux AMD64“.

1. Öffnen Sie die Befehlspalette, und suchen Sie nach **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Standardzielplattform für Edge-Projektmappe festlegen), oder wählen Sie das Verknüpfungssymbol in der Seitenleiste unten im Fenster aus.

   ![Auswählen des Architektursymbols in der Seitenleiste](./media/tutorial-develop-for-linux/select-architecture.png)

2. Wählen Sie in der Befehlspalette die Zielarchitektur aus der Liste mit Optionen aus. Weil in diesem Tutorial ein virtueller Ubuntu-Computer als IoT Edge-Gerät verwendet wird, behalten Sie den Standardwert **amd64** bei.

### <a name="review-the-sample-code"></a>Überprüfen des Beispielcodes

Die von Ihnen erstellte Lösungsvorlage enthält Beispielcode für ein IoT Edge-Modul. Dieses Beispielmodul empfängt lediglich Nachrichten und leitet sie dann weiter. Mit der Pipelinefunktionalität wird ein wichtiges Konzept von IoT Edge veranschaulicht. Hierbei geht es darum, wie Module miteinander kommunizieren.

Für jedes Modul können im Code mehrere Warteschlangen für die *Eingabe* und *Ausgabe* deklariert werden. Der IoT Edge-Hub, der auf dem Gerät ausgeführt wird, leitet Nachrichten aus der Ausgabe eines Moduls an die Eingabe mindestens eines anderen Moduls weiter. Der spezifische Code zum Deklarieren von Ein- und Ausgaben variiert je nach Sprache, aber das Konzept ist modulübergreifend identisch. Weitere Informationen zum Routing zwischen Modulen finden Sie unter [Deklarieren von Routen](module-composition.md#declare-routes).

Der in der Projektvorlage enthaltene C#-Beispielcode verwendet die [ModuleClient-Klasse](/dotnet/api/microsoft.azure.devices.client.moduleclient) aus dem IoT Hub SDK für .NET.

1. Öffnen Sie die Datei **Program.cs**, die im Ordner **modules/SampleModule/** enthalten ist.

2. Suchen Sie in der Datei „program.cs“ die **SetInputMessageHandlerAsync**-Methode.

3. Die [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync)-Methode richtet eine Eingabewarteschlange für den Empfang eingehender Nachrichten ein. Überprüfen Sie diese Methode, und vergewissern Sie sich, dass sie eine Eingabewarteschlange namens **input1** initialisiert.

   ![Suchen nach dem Eingabenamen im Konstruktor „SetInputMessageCallback“](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Suchen Sie als Nächstes die **SendEventAsync**-Methode.

5. Die [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync)-Methode verarbeitet empfangene Nachrichten und richtet eine Ausgabewarteschlange ein, um sie zu übergeben. Überprüfen Sie diese Methode, und vergewissern Sie sich, dass sie eine Ausgabewarteschlange namens **output1** initialisiert.

   ![Ermitteln des Ausgabenamens in „SendEventToOutputAsync“](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Öffnen Sie die Datei **deployment.template.json**.

7. Suchen Sie nach der **modules**-Eigenschaft der gewünschten „$edgeAgent“-Eigenschaften.

   Hier sollten zwei Module aufgeführt sein. Ein Modul ist **SimulatedTemperatureSensor**. Es ist standardmäßig in allen Vorlagen enthalten und stellt simulierte Temperaturdaten bereit, die Sie zum Testen Ihrer Module verwenden können. Das andere Modul ist **SampleModule**, das Sie im Rahmen dieser Projektmappe erstellt haben.

8. Suchen Sie unten in der Datei nach den gewünschten Eigenschaften für das Modul **$edgeHub**.

   Eine der Funktionen des IoT Edge-Hub-Moduls ist das Weiterleiten von Nachrichten zwischen allen Modulen einer Bereitstellung. Sehen Sie sich die Werte in der **routes**-Eigenschaft an. Für die eine Route (**SampleModuleToIoTHub**) wird ein Platzhalterzeichen (* *\** _) verwendet, um die Nachrichten zu kennzeichnen, die aus Ausgabewarteschlangen im Modul „SampleModule“ stammen. Diese Nachrichten werden an _$upstream* geleitet. Hierbei handelt es sich um einen reservierten Namen, der auf IoT Hub hinweist. Über die andere Route (**sensorToSampleModule**) werden vom Modul „SimulatedTemperatureSensor“ stammende Nachrichten an die Eingabewarteschlange *input1* geleitet, die – wie Sie gesehen haben – im Code von „SampleModule“ initialisiert wird.

   ![Überprüfen von Routen in „deployment.template.json“](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Erstellen und Pushen Ihrer Projektmappe

Sie haben sich den Modulcode und die Bereitstellungsvorlage angesehen, um sich mit einigen wichtigen Konzepten der Bereitstellung vertraut zu machen. Sie können nun das Containerimage „SampleModule“ erstellen und per Pushvorgang an Ihre Containerregistrierung übertragen. Mit der IoT Tools-Erweiterung für Visual Studio Code wird bei diesem Schritt auch das Bereitstellungsmanifest generiert, und zwar basierend auf den Informationen in der Vorlagendatei und den Modulinformationen aus den Projektmappendateien.

### <a name="sign-in-to-docker"></a>Anmelden bei Docker

Geben Sie Ihre Anmeldeinformationen für die Containerregistrierung für Docker an, damit Ihr Containerimage per Pushvorgang übertragen und in der Registrierung gespeichert werden kann.

1. Öffnen Sie das in Visual Studio Code integrierte Terminal, indem Sie **Ansicht** > **Terminal** wählen.

2. Melden Sie sich an Docker mit den Anmeldeinformationen für die Azure-Containerregistrierung an, die Sie nach dem Erstellen der Registrierung gespeichert haben.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Möglicherweise wird Ihnen in einem Sicherheitshinweis die Verwendung von `--password-stdin` empfohlen. Diese bewährte Methode wird für Produktionsszenarien empfohlen, aber sie ist nicht Gegenstand dieses Tutorials. Weitere Informationen finden Sie in der [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)-Referenz.
   
3. Anmelden bei Azure Container Registry

   ```cmd/sh
   az acr login -n <ACR registry name>
   ```

### <a name="build-and-push"></a>Erstellen und Pushen

Da Visual Studio Code jetzt über Zugriff auf Ihre Containerregistrierung verfügt, können Sie den Projektmappencode in ein Containerimage verwandeln.

1. Klicken Sie im Visual Studio Code-Explorer mit der rechten Maustaste auf die Datei **deployment.template.json**, und klicken Sie anschließend auf **Build and Push IoT Edge solution** (IoT Edge-Projektmappe erstellen und übertragen).

   ![Erstellen und Pushen von IoT Edge-Modulen](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Der Befehl zum Erstellen und Übertragen per Push startet drei Vorgänge. Zuerst erstellt er in der Projektmappe einen neuen Ordner mit dem Namen **config**. Darin ist das vollständige Bereitstellungsmanifest gespeichert, das aus Informationen in der Bereitstellungsvorlage und anderen Projektmappendateien erstellt wurde. Danach führt er `docker build` zum Erstellen des Containerimages aus, das auf der entsprechenden Dockerfile-Datei für Ihre Zielarchitektur basiert. Und schließlich führt er `docker push` aus, um das Imagerepository per Push in Ihre Containerregistrierung zu übertragen.

   Dieser Vorgang kann beim ersten Mal einige Minuten dauern, aber er ist bei der nächsten Ausführung von Befehlen schon schneller.

2. Öffnen Sie die Datei **deployment.amd64.json** im neu erstellten Ordner „config“. Der Dateiname spiegelt die Zielarchitektur wider und weicht daher ab, wenn Sie eine andere Architektur wählen.

3. Beachten Sie Folgendes: Die beiden Parameter, die über Platzhalter verfügt haben, wurden jetzt durch die entsprechenden Werte ersetzt. Der Abschnitt **registryCredentials** enthält Ihren Benutzernamen und das zugehörige Kennwort für die Registrierung aus der ENV-Datei. **SampleModule** enthält das vollständige Image-Repository mit Namens-, Versions- und Architekturtag aus der Datei „module.json“.

4. Öffnen Sie die Datei **module.json** im Ordner „SampleModule“.

5. Ändern Sie die Versionsnummer für das Modulimage. (Die Version, nicht die $schema-Version.) Inkrementieren Sie beispielsweise die Patchversionsnummer auf **0.0.2**, als ob wir eine geringe Änderung am Modulcode vorgenommen hätten.

   >[!TIP]
   >Für Modulversionen kann die Versionskontrolle genutzt werden, und Sie können Änderungen für eine kleine Gruppe mit Geräten testen, bevor Sie Updates für die Produktion bereitstellen. Falls Sie die Modulversion vor dem Erstellen und Pushen nicht inkrementieren, überschreiben Sie das Repository in Ihrer Containerregistrierung.

6. Speichern Sie Ihre Änderungen an der Datei „module.json“.

7. Klicken Sie erneut mit der rechten Maustaste auf die Datei **deployment.template.json**, und wählen Sie erneut die Option **Build and Push IoT Edge solution** (IoT Edge-Projektmappe erstellen und übertragen).

8. Öffnen Sie erneut die Datei **deployment.amd64.json**. Beachten Sie, dass beim erneuten Ausführen des Befehls zum Erstellen und Pushen keine neue Datei erstellt wurde. Stattdessen wurde dieselbe Datei aktualisiert, um die Änderungen widerzuspiegeln. Das Image „SampleModule“ verweist jetzt auf Version 0.0.2 des Containers.

9. Sie können noch genauer überprüfen, was mit dem Befehl zum Erstellen und Pushen durchgeführt wurde, indem Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Containerregistrierung navigieren.

10. Wählen Sie in der Containerregistrierung **Repositorys** und dann **samplemodule**. Stellen Sie sicher, dass beide Versionen des Images per Pushvorgang an die Registrierung übertragen wurden.

    ![Anzeigen beider Imageversionen in der Containerregistrierung](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen und Pushen Ihres Modulimages Fehler auftreten, liegt dies häufig an der Docker-Konfiguration auf Ihrem Entwicklungscomputer. Überprüfen Sie für Ihre Konfiguration Folgendes:

* Haben Sie den Befehl `docker login` mit den Anmeldeinformationen ausgeführt, die Sie aus Ihrer Containerregistrierung kopiert haben? Hierbei handelt es sich um andere Anmeldeinformationen als für die Anmeldung an Azure.
* Ist Ihr Containerrepository richtig? Verfügt es über den richtigen Namen für die Containerregistrierung und den richtigen Modulnamen? Öffnen Sie die Datei **module.json** im Ordner „SampleModule“, um dies zu überprüfen. Der Repositorywert sollte das Format **\<registry name\>.azurecr.io/samplemodule** haben.
* Wenn Sie für Ihr Modul einen anderen Namen als **SampleModule** verwendet haben: Wurde dieser Name in der gesamten Projektmappe einheitlich verwendet?
* Entsprechen die auf Ihrem Computer ausgeführten Containertypen den von Ihnen erstellten Containertypen? Dieses Tutorial ist für Linux-IoT Edge-Geräte bestimmt. In Visual Studio Code sollte in der Seitenleiste also **amd64** oder **arm32v7** angezeigt werden, und für Docker Desktop sollten Linux-Container ausgeführt werden.  

## <a name="deploy-modules-to-device"></a>Bereitstellen von Modulen auf dem Gerät

Sie haben sich vergewissert, dass die Containerimages in Ihrer Containerregistrierung gespeichert sind. Diese können nun also auf einem Gerät bereitgestellt werden. Sorgen Sie dafür, dass Ihr IoT Edge-Gerät ordnungsgemäß ausgeführt wird.

1. Erweitern Sie im Visual Studio Code-Explorer im Abschnitt **Azure IoT Hub** den Bereich **Geräte**, um Ihre IoT-Geräteliste anzuzeigen.

2. Klicken Sie mit der rechten Maustaste auf das IoT Edge-Gerät, auf dem Sie die Bereitstellung durchführen möchten, und wählen Sie dann **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen).

   ![Erstellen einer Bereitstellung für ein einzelnes Gerät](./media/tutorial-develop-for-linux/create-deployment.png)

3. Navigieren Sie im Datei-Explorer zum Ordner **config**, und wählen Sie die Datei **deployment.amd64.json** aus.

   Verwenden Sie nicht die Datei „deployment.template.json“, da sie nicht die Anmeldeinformationen für die Containerregistrierung bzw. die Modulimagewerte enthält. Wenn Sie ein Linux-ARM32-Gerät nutzen, hat das Bereitstellungsmanifest den Namen „deployment.arm32v7.json“.

4. Erweitern Sie unter Ihrem Gerät den Bereich **Module**, um eine Liste mit bereitgestellten und ausgeführten Modulen anzuzeigen. Klicken Sie auf die Schaltfläche „Aktualisieren“. Die neuen Module „SimulatedTemperatureSensor“ und „SampleModule“ sollten nun auf Ihrem Gerät ausgeführt werden.

   Es dauert ggf. einige Minuten, bis die Module gestartet werden. Die IoT Edge-Runtime muss das neue Bereitstellungsmanifest empfangen, die Modulimages per Pullvorgang aus der Containerruntime abrufen und dann jedes neue Modul starten.

   ![Anzeigen von Modulen, die auf Ihrem IoT Edge-Gerät ausgeführt werden](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Anzeigen von Nachrichten vom Gerät

Der Code von „SampleModule“ empfängt Nachrichten über die zugehörige Eingabewarteschlange und leitet diese an die Ausgabewarteschlange weiter. Im Bereitstellungsmanifest wurden Routen deklariert, über die Nachrichten von „SimulatedTemperatureSensor“ an „SampleModule“ und dann von „SampleModule“ an IoT Hub weitergeleitet wurden. Mit den Azure IoT Tools für Visual Studio Code können Sie Nachrichten anzeigen, wenn diese von Ihren einzelnen Geräten auf IoT Hub eingehen.

1. Klicken Sie im Visual Studio Code-Explorer mit der rechten Maustaste auf das IoT Edge-Gerät, das Sie überwachen möchten, und wählen Sie dann die Option **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus.

2. Im Ausgabefenster von Visual Studio Code können Sie anschließend verfolgen, wie die Nachrichten auf Ihrem IoT-Hub eingehen.

   ![Anzeigen von eingehenden Gerät-zu-Cloud-Nachrichten](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Anzeigen von Änderungen auf dem Gerät

Wenn Sie die Vorgänge direkt auf dem Gerät verfolgen möchten, können Sie die Befehle in diesem Abschnitt verwenden, um die IoT Edge-Runtime und die Module zu untersuchen, die auf Ihrem Gerät ausgeführt werden.

Die Befehle in diesem Abschnitt gelten für Ihr IoT Edge-Gerät und nicht für Ihren Entwicklungscomputer. Wenn Sie einen virtuellen Computer für Ihr IoT Edge-Gerät verwenden, sollten Sie nun die Verbindung dafür herstellen. Navigieren Sie in Azure zur Übersichtsseite des virtuellen Computers, und wählen Sie die Option **Verbinden**, um auf die Secure Shell-Verbindung zuzugreifen.

* Zeigen Sie alle Module an, die auf Ihrem Gerät bereitgestellt wurden, und überprüfen Sie ihren Status:

   ```bash
   iotedge list
   ```

   Es sollten vier Module angezeigt werden: die beiden IoT Edge-Runtime-Module, „SimulatedTemperatureSensor“ und „SampleModule“. Für alle vier Module sollte „Wird ausgeführt“ angezeigt werden.

* Überprüfen Sie die Protokolle für ein bestimmtes Modul:

   ```bash
   iotedge logs <module name>
   ```

   Bei IoT Edge-Modulen wird die Groß-/Kleinschreibung beachtet.

   In den Protokollen für „SimulatedTemperatureSensor“ und „SampleModule“ sollten die verarbeiteten Nachrichten angezeigt werden. Das Modul „edgeAgent“ ist für das Starten der anderen Module zuständig. Die Protokolle dieses Moduls enthalten also Informationen zur Implementierung des Bereitstellungsmanifests. Falls ein Modul nicht aufgelistet ist oder nicht ausgeführt wird, sind die Fehler wahrscheinlich in den Protokollen von „edgeAgent“ enthalten. Das Modul „edgeHub“ ist für die Kommunikation zwischen den Modulen und IoT Hub zuständig. Wenn die Module betriebsbereit sind und ausgeführt werden, aber die Nachrichten nicht auf Ihrem IoT-Hub ankommen, sind die Fehler wahrscheinlich in den Protokollen von „edgeHub“ enthalten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen.

Andernfalls können Sie die in diesem Artikel verwendeten lokalen Konfigurationen und die Azure-Ressourcen löschen, um Kosten zu vermeiden.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Visual Studio Code auf Ihrem Entwicklungscomputer eingerichtet und damit Ihr erstes IoT Edge-Modul bereitgestellt. Nachdem Sie sich nun mit den grundlegenden Konzepten vertraut gemacht haben, können Sie versuchen, einem Modul Funktionen hinzuzufügen, damit es den Datenfluss analysieren kann. Wählen Sie Ihre bevorzugte Sprache aus:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)
