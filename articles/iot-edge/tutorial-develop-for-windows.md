---
title: 'Entwickeln von Modulen für Windows-Geräte: Azure IoT Edge | Microsoft-Dokumentation'
description: In diesem Tutorial wird Schritt für Schritt das Einrichten Ihres Entwicklungscomputers und der Cloudressourcen für die Entwicklung von IoT Edge-Modulen mit Windows-Containern für Windows-Geräte beschrieben.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/20/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 81d660857eff63e0dfeeda400b168ea424152081
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808605"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Tutorial: Entwickeln von IoT Edge-Modulen für Windows-Geräte

Nutzen Sie Visual Studio, um Code zu entwickeln und auf Windows-Geräten bereitzustellen, auf denen IoT Edge ausgeführt wird.

In der Schnellstartanleitung haben Sie ein IoT Edge-Gerät mit einem virtuellen Windows-Computer erstellt und über Azure Marketplace ein vordefiniertes Modul bereitgestellt. In diesem Tutorial werden die Voraussetzungen beschrieben, die für die Entwicklung und Bereitstellung Ihres eigenen Codes auf einem IoT Edge-Gerät erfüllt sein müssen. Dieses Tutorial ist eine nützliche Ausgangsbasis für alle anderen Tutorials, in denen bestimmte Programmiersprachen oder Azure-Dienste ausführlicher behandelt werden. 

In diesem Tutorial wird das Beispiel für die Bereitstellung eines **C-Moduls auf einem Windows-Gerät** verwendet. Das Beispiel wurde der Einfachheit halber gewählt. Sie können sich über die Entwicklungstools informieren, ohne sich damit befassen zu müssen, ob Sie die richtigen Bibliotheken installiert haben. Nachdem Sie sich mit den Entwicklungskonzepten vertraut gemacht haben, können Sie Ihre bevorzugte Sprache bzw. den Azure-Dienst auswählen, um in die Details einzusteigen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten Ihres Entwicklungscomputers
> * Verwenden der IoT Edge-Tools für Visual Studio zum Erstellen eines neuen Projekts
> * Erstellen Ihres Projekts als Container und Speichern in einer Azure-Containerregistrierung
> * Bereitstellen Ihres Codes auf einem IoT Edge-Gerät 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Wichtige Begriffe

In diesem Tutorial wird Schritt für Schritt die Entwicklung eines IoT Edge-Moduls beschrieben. Ein *IoT Edge-Modul* (auch kurz als *Modul* bezeichnet) ist ein Container, der ausführbaren Code enthält. Sie können ein oder mehrere Module auf einem IoT Edge-Gerät bereitstellen. Mit Modulen werden bestimmte Aufgaben durchgeführt, z. B. das Erfassen der Daten von Sensoren, das Durchführen von Vorgängen zur Datenanalyse oder Datenbereinigung oder das Senden von Nachrichten an einen IoT-Hub. Weitere Informationen finden Sie unter [Grundlegendes zu Azure IoT Edge-Modulen](iot-edge-modules.md).

Beim Entwickeln von IoT Edge-Modulen ist es wichtig, dass Sie den Unterschied zwischen dem Entwicklungscomputer und dem IoT Edge-Zielgerät verstehen, auf dem das Modul später bereitgestellt wird. Der Container, den Sie für Ihren Modulcode erstellen, muss mit dem Betriebssystem (BS) des *Zielgeräts* übereinstimmen. Für die Entwicklung von Windows-Containern ist dieses Konzept einfacher, weil Windows-Container nur unter Windows-Betriebssystemen ausgeführt werden. Sie können aber beispielsweise Ihren Windows-Entwicklungscomputer verwenden, um Module für Linux-IoT Edge-Geräte zu entwickeln. In diesem Szenario müssen Sie sicherstellen, dass auf Ihrem Entwicklungscomputer Linux-Container ausgeführt werden. Denken Sie beim Durcharbeiten dieses Tutorials an den Unterschied zwischen dem *Betriebssystem des Entwicklungscomputers* und dem *Betriebssystem des Containers*.

In diesem Tutorial geht es um Windows-Geräte, auf denen IoT Edge ausgeführt wird. Für Windows IoT Edge-Geräte werden Windows-Container verwendet. Es wird empfohlen, Visual Studio für die Entwicklung für Windows-Geräte zu nutzen, und dies ist auch die Vorgehensweise in diesem Tutorial. Alternativ können Sie Visual Studio Code nutzen, aber zwischen den beiden Tools gibt es Unterschiede in Bezug auf die Unterstützung.

In der folgenden Tabelle sind die unterstützten Entwicklungsszenarien für **Windows-Container** in Visual Studio Code und Visual Studio aufgeführt.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure-Dienste** | Azure-Funktionen <br> Azure Stream Analytics |   |
| **Sprachen** | C# (Debuggen wird nicht unterstützt) | C <br> C# |
| **Weitere Informationen** | [Azure IoT Edge für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-Tools für Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools), [Azure IoT Edge-Tools für Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

In diesem Tutorial werden die Entwicklungsschritte für Visual Studio 2019 beschrieben. Falls Sie Visual Studio Code bevorzugen, hilft Ihnen die Anleitung unter [Entwickeln und Debuggen von Modulen für Azure IoT Edge mithilfe von Visual Studio Code](how-to-vs-code-develop-module.md) weiter. Wenn Sie Visual Studio 2017 (Version 15.7 oder höher) verwenden, laden Sie die [Azure IoT Edge-Tools für Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) herunter, und installieren Sie sie.

## <a name="prerequisites"></a>Voraussetzungen

Ein Entwicklungscomputer:

* Windows 10 mit Update 1809 oder höher.
* Sie können je nach Ihren Präferenzen bei der Entwicklung Ihren eigenen Computer oder einen virtuellen Computer verwenden.
* Installieren Sie [Git](https://git-scm.com/). 
* Installieren Sie das Azure IoT C SDK für Windows x64 über vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

<!--vcpkg only required for C development-->

Ein Azure IoT Edge-Gerät unter Windows:

* Wir empfehlen Ihnen, IoT Edge nicht auf Ihrem Entwicklungscomputer auszuführen, sondern ein separates Gerät zu verwenden. Diese Unterscheidung zwischen Entwicklungscomputer und IoT Edge-Gerät entspricht eher einem realen Bereitstellungsszenario und trägt dazu bei, die unterschiedlichen Bereiche auseinanderzuhalten.
* Gehen Sie wie folgt vor, wenn Ihnen kein zweites Gerät zur Verfügung steht: Verwenden Sie den Schnellstartartikel, um mit einem [virtuellen Windows-Computer](quickstart.md) ein IoT Edge-Gerät in Azure zu erstellen.

Cloudressourcen:

* Einen [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Azure im Tarif „Free“ oder „Standard“. 

## <a name="install-container-engine"></a>Installieren der Container-Engine

Da IoT Edge-Module als Container verpackt werden, benötigen Sie auf Ihrem Entwicklungscomputer eine Container-Engine zum Erstellen und Verwalten der Container. Es wird empfohlen, Docker Desktop für die Entwicklung zu nutzen, weil diese Anwendung über viele Features verfügt und als Container-Engine sehr beliebt ist. Mit Docker Desktop auf einem Windows-Computer können Sie zwischen Linux-Containern und Windows-Containern wechseln, damit Sie auf einfache Weise für unterschiedliche Arten von IoT Edge-Geräten Module entwickeln können. 

Verwenden Sie die Docker-Dokumentation für die Installation auf Ihrem Entwicklungscomputer: 

* [Install Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/) (Installieren von Docker Desktop für Windows)

  * Bei der Installation von Docker Desktop für Windows werden Sie gefragt, ob Sie Linux- oder Windows-Container nutzen möchten. Verwenden Sie für dieses Tutorial **Windows-Container**. Weitere Informationen finden Sie unter [Switch between Windows and Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) (Umschalten zwischen Windows- und Linux-Containern).


## <a name="set-up-visual-studio-and-tools"></a>Einrichten von Visual Studio und Tools

Verwenden Sie die IoT-Erweiterungen für Visual Studio 2019 zum Entwickeln von IoT Edge-Modulen. Diese Erweiterungen dienen zum Bereitstellen von Projektvorlagen, Automatisieren der Erstellung des Bereitstellungsmanifests und Ermöglichen der Überwachung und Verwaltung von IoT Edge-Geräten. In diesem Abschnitt installieren Sie Visual Studio und die IoT Edge-Erweiterung und richten dann Ihr Azure-Konto ein, um IoT Hub-Ressourcen über Visual Studio zu verwalten. 

1. Wenn Visual Studio auf Ihrem Entwicklungscomputer noch nicht installiert ist, müssen Sie die [Installation von Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) mit den folgenden Workloads durchführen: 

   * Azure-Entwicklung
   * Desktopentwicklung mit C++
   * Plattformübergreifende .NET Core-Entwicklung

1. Falls Visual Studio 2019 bereits auf dem Entwicklungscomputer vorhanden ist: Führen Sie die Schritte unter [Ändern von Visual Studio durch Hinzufügen oder Entfernen von Arbeitsauslastungen und Komponenten](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) aus, um die erforderlichen Arbeitsauslastungen hinzuzufügen, sofern Sie dies noch nicht durchgeführt haben.

2. Laden Sie die Erweiterung [Azure IoT Edge-Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) für Visual Studio 2019 herunter, und installieren Sie sie. 

3. Öffnen Sie Visual Studio 2019, nachdem Ihre Installationen abgeschlossen sind, und wählen Sie **Ohne Code fortfahren** aus.

4. Wählen Sie **Ansicht** > **Cloud-Explorer**. 

5. Wählen Sie das Profilsymbol im Cloud-Explorer aus, und melden Sie sich an Ihrem Azure-Konto an, falls Sie noch nicht angemeldet sind. 

6. Nach dem Anmelden werden Ihre Azure-Abonnements aufgelistet. Wählen Sie die Abonnements aus, auf die Sie über den Cloud-Explorer zugreifen möchten, und wählen Sie anschließend **Anwenden**. 

7. Erweitern Sie nacheinander die Einträge für Ihr Abonnement, **IoT Hubs** und Ihren IoT-Hub. Es sollte eine Liste mit Ihren IoT-Geräten angezeigt werden, und Sie können diesen Explorer für deren Verwaltung verwenden. 

   ![Zugreifen auf IoT Hub-Ressourcen im Cloud-Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Erstellen eines neuen Modulprojekts

Mit der Erweiterung „Azure IoT Edge-Tools“ werden Projektvorlagen für alle unterstützten IoT Edge-Modulsprachen in Visual Studio bereitgestellt. Diese Vorlagen verfügen über alle erforderlichen Dateien und den Code zum Bereitstellen eines funktionierenden Moduls für einen IoT Edge-Test. Darüber hinaus dienen Sie Ihnen als Ausgangspunkt zum Anpassen der Vorlage mit Ihrer eigenen Geschäftslogik. 

1. Wählen Sie **Datei** > **Neu** > **Projekt...** aus.

2. 2\. Suchen Sie im Fenster für das neue Projekt das Projekt **IoT Edge**, wählen Sie dann das Projekt **Azure IoT Edge (Windows amd64)** aus. Klicken Sie auf **Weiter**. 

   ![Erstellen eines neuen Azure IoT Edge-Projekts](./media/tutorial-develop-for-windows/new-project.png)

3. Geben Sie im Fenster zum Konfigurieren des neuen Projekts dem Projekt und der Projektmappe einen aussagekräftigen Namen, etwa **CTutorialApp**. Klicken Sie auf **Erstellen** , um das Projekt zu erstellen.

   ![Konfigurieren eines neuen Azure IoT Edge-Projekts](./media/tutorial-develop-for-windows/configure-project.png)
 

4. Konfigurieren Sie im Fenster für die IoT Edge-Anwendung und das Modul Ihr Projekt mit den folgenden Werten: 

   | Feld | Wert |
   | ----- | ----- |

   | Vorlage auswählen | Wählen Sie **C Module** (C-Modul) aus. | | Modulprojektname | Übernehmen Sie den Standardnamen **IoTEdgeModule1**. | | Docker-Imagerepository | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Für Ihr Containerimage ist der Wert des Modulprojektnamens bereits vorhanden. Ersetzen Sie **localhost:5000** durch den Anmeldeserverwert aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. <br><br> Das endgültige Imagerepository sieht wie folgt aus: \<Registrierungsname\>.azurecr.io/iotedgemodule1. |

   ![Konfigurieren von Zielgerät, Modultyp und Containerregistrierung für Ihr Projekt](./media/tutorial-develop-for-windows/add-application-and-module.png)

5. Klicken Sie zum Übernehmen der Änderungen auf **OK**. 

Nehmen Sie sich nach dem Laden Ihres neuen Projekts im Visual Studio-Fenster etwas Zeit, um sich mit den erstellten Dateien vertraut zu machen: 

* Ein IoT Edge-Projekt mit dem Namen **AzureIoTEdgeApp1.Windows.Amd64**.
    * Der Ordner **Module** enthält Zeiger zu den im Projekt enthaltenen Modulen. In diesem Fall sollte nur „IoTEdgeModule1“ vorhanden sein. 
    * Die Datei **deployment.template.json** ist eine Vorlage für die Erstellung eines Bereitstellungsmanifests. Ein *Bereitstellungsmanifest* ist eine Datei, mit der Folgendes genau definiert wird: welche Module auf einem Gerät bereitgestellt werden sollen, wie sie konfiguriert werden sollen und wie diese miteinander und mit der Cloud kommunizieren können. 
* Ein IoT Edge-Modulprojekt mit dem Namen **IoTEdgeModule1**.
    * Die Datei **main.c** enthält den C-Standardmodulcode, der in der Projektvorlage enthalten ist. Das Standardmodul leitet die Eingabe einer Quelle an IoT Hub weiter. 
    * Die Datei **module.json** enthält Details zum Modul, z. B. vollständiges Imagerepository, Imageversion und die Information, welche Dockerfile für die einzelnen unterstützten Plattformen verwendet werden soll.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Angeben Ihrer Anmeldeinformationen für die Registrierung über den IoT Edge-Agent

Die IoT Edge-Runtime benötigt die Anmeldeinformationen für Ihre Registrierung, um Ihre Containerimages per Pullvorgang auf das IoT Edge-Gerät zu übertragen. Fügen Sie diese Anmeldeinformationen der Bereitstellungsvorlage hinzu. 

1. Öffnen Sie die Datei **deployment.template.json**.

2. Suchen Sie unter den gewünschten Eigenschaften von „$edgeAgent“ nach der **registryCredentials**-Eigenschaft. 

3. Aktualisieren Sie die Eigenschaft mit Ihren Anmeldeinformationen, und verwenden Sie das folgende Format: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Speichern Sie die Datei „deployment.template.json“. 

### <a name="review-the-sample-code"></a>Überprüfen des Beispielcodes

Die von Ihnen erstellte Lösungsvorlage enthält Beispielcode für ein IoT Edge-Modul. Dieses Beispielmodul empfängt lediglich Nachrichten und leitet sie dann weiter. Mit der Pipelinefunktionalität wird ein wichtiges Konzept von IoT Edge veranschaulicht. Hierbei geht es darum, wie Module miteinander kommunizieren.

Für jedes Modul können im Code mehrere Warteschlangen für die *Eingabe* und *Ausgabe* deklariert werden. Der IoT Edge-Hub, der auf dem Gerät ausgeführt wird, leitet Nachrichten aus der Ausgabe eines Moduls an die Eingabe mindestens eines anderen Moduls weiter. Die spezifische Sprachanweisung zum Deklarieren von Ein- und Ausgaben variiert je nach Sprache, aber das Konzept ist modulübergreifend identisch. Weitere Informationen zum Routing zwischen Modulen finden Sie unter [Deklarieren von Routen](module-composition.md#declare-routes).

1. Suchen Sie in der Datei **main.c** die Funktion **SetupCallbacksForModule**.

2. Mit dieser Funktion wird eine Eingabewarteschlange zum Empfangen eingehender Nachrichten eingerichtet. Sie ruft die Modulclientfunktion [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback) des C SDK auf. Überprüfen Sie diese Funktion, und vergewissern Sie sich, dass sie eine Eingabewarteschlange namens **input1** initialisiert. 

   ![Ermitteln des Eingabenamens im Konstruktor „SetInputMessageCallback“](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Suchen Sie als Nächstes die Funktion **InputQueue1Callback**.

4. Diese Funktion verarbeitet empfangene Nachrichten und richtet eine Ausgabewarteschlange ein, um sie zu übergeben. Sie ruft die Modulclientfunktion [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync) des C SDK auf. Überprüfen Sie diese Funktion, und vergewissern Sie sich, dass sie eine Ausgabewarteschlange namens **output1** initialisiert. 

   ![Ermitteln des Ausgabenamens im Konstruktor „SendEventToOutputAsync“](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Öffnen Sie die Datei **deployment.template.json**.

6. Suchen Sie nach der **modules**-Eigenschaft der gewünschten „$edgeAgent“-Eigenschaften. 

   Hier sollten zwei Module aufgeführt sein. Das erste Modul ist **tempSensor**. Es ist in allen Vorlagen standardmäßig enthalten, um simulierte Temperaturdaten bereitzustellen, die Sie zum Testen Ihrer Module verwenden können. Das zweite Modul ist **IotEdgeModule1**, das Sie im Rahmen dieses Projekts erstellt haben.

   Diese Moduleigenschaft deklariert, welche Module in der Bereitstellung auf Ihren Geräten enthalten sein sollen. 

7. Suchen Sie nach der **routes**-Eigenschaft der gewünschten $edgeHub-Eigenschaften. 

   Eine der Funktionen des IoT Edge-Hub-Moduls ist das Weiterleiten von Nachrichten zwischen allen Modulen einer Bereitstellung. Sehen Sie sich die Werte in der routes-Eigenschaft an. Für die erste Route (**IotEdgeModule1ToIoTHub**) wird ein Platzhalterzeichen ( **\*** ) verwendet, um die Nachrichten aufzunehmen, die aus einer Ausgabewarteschlange im Modul „IoTEdgeModule1“ stammen. Diese Nachrichten werden an *$upstream* geleitet. Hierbei handelt es sich um einen reservierten Namen, der auf IoT Hub hinweist. Über die zweite Route (**sensorToIotEdgeModule1**) werden vom Modul „tempSensor“ stammende Nachrichten an die Eingabewarteschlange *input1* des Moduls „IotEdgeModule1“ geleitet. 

   ![Überprüfen von Routen in „deployment.template.json“](./media/tutorial-develop-for-windows/deployment-routes.png)


## <a name="build-and-push-your-solution"></a>Erstellen und Pushen Ihrer Projektmappe

Sie haben sich den Modulcode und die Bereitstellungsvorlage angesehen, um sich mit einigen wichtigen Konzepten der Bereitstellung vertraut zu machen. Sie können nun das Containerimage „IotEdgeModule1“ erstellen und per Pushvorgang an Ihre Containerregistrierung übertragen. Mit der IoT Tools-Erweiterung für Visual Studio wird bei diesem Schritt auch das Bereitstellungsmanifest generiert, und zwar basierend auf den Informationen in der Vorlagendatei und den Modulinformationen aus den Projektmappendateien. 

### <a name="sign-in-to-docker"></a>Anmelden bei Docker

Geben Sie Ihre Anmeldeinformationen für die Containerregistrierung für Docker auf Ihrem Entwicklungscomputer an, damit Ihr Containerimage per Pushvorgang übertragen und in der Registrierung gespeichert werden kann. 

1. Öffnen Sie PowerShell oder eine Eingabeaufforderung.

2. Melden Sie sich an Docker mit den Anmeldeinformationen für die Azure-Containerregistrierung an, die Sie nach dem Erstellen der Registrierung gespeichert haben. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Unter Umständen wird Ihnen in einem Sicherheitshinweis die Verwendung von `--password-stdin` empfohlen. Diese bewährte Methode wird für Produktionsszenarien empfohlen, aber sie ist nicht Gegenstand dieses Tutorials. Weitere Informationen finden Sie in der [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)-Referenz.

### <a name="build-and-push"></a>Erstellen und Pushen

Ihr Entwicklungscomputer hat jetzt Zugriff auf Ihre Containerregistrierung, und dies gilt auch für Ihre IoT Edge-Geräte. Der Projektcode kann nun in ein Containerimage umgewandelt werden. 

1. Klicken Sie mit der rechten Maustaste auf den Projektordner **AzureIotEdgeApp1.Windows.Amd64**, und wählen Sie **Build and Push IoT Edge Modules** (IoT Edge-Module erstellen und pushen). 

   ![Erstellen und Pushen von IoT Edge-Modulen](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Mit dem Befehl zum Erstellen und Übertragen per Pushvorgang werden drei Vorgänge gestartet. Zuerst erstellt er in der Projektmappe einen neuen Ordner mit dem Namen **config**. Darin ist das vollständige Bereitstellungsmanifest gespeichert, das aus Informationen in der Bereitstellungsvorlage und anderen Projektmappendateien erstellt wurde. Danach führt er `docker build` zum Erstellen des Containerimages aus, das auf der entsprechenden Dockerfile-Datei für Ihre Zielarchitektur basiert. Und schließlich führt er `docker push` aus, um das Imagerepository per Pushvorgang in Ihre Containerregistrierung zu übertragen. 

   Dieser Vorgang kann beim ersten Mal einige Minuten dauern, aber er ist bei der nächsten Ausführung von Befehlen schon schneller. 

2. Öffnen Sie die Datei **deployment.windows-amd64.json** im neu erstellten Ordner „config“. (Unter Umständen wird der Ordner „config“ in Visual Studio im Projektmappen-Explorer nicht angezeigt. Wählen Sie in diesem Fall in der Taskleiste des Projektmappen-Explorers das Symbol **Alle Dateien anzeigen**.)

3. Suchen Sie im Abschnitt „IotEdgeModule1“ nach dem Parameter **image**. Beachten Sie, dass das Image das vollständige Image-Repository mit Namens-, Versions- und Architekturtag aus der Datei „module.json“ enthält.

4. Öffnen Sie die Datei **module.json** im Ordner „IotEdgeModule1“. 

5. Ändern Sie die Versionsnummer für das Modulimage. (Die Version, nicht die $schema-Version.) Inkrementieren Sie beispielsweise die Patchversionsnummer auf **0.0.2**, als ob wir eine geringe Änderung am Modulcode vorgenommen hätten. 

   >[!TIP]
   >Für Modulversionen kann die Versionskontrolle genutzt werden, und Sie können Änderungen für eine kleine Gruppe mit Geräten testen, bevor Sie Updates für die Produktion bereitstellen. Falls Sie die Modulversion vor dem Erstellen und Pushen nicht inkrementieren, überschreiben Sie das Repository in Ihrer Containerregistrierung. 

6. Speichern Sie Ihre Änderungen an der Datei „module.json“.

7. Klicken Sie erneut mit der rechten Maustaste auf den Projektordner **AzureIotEdgeApp1.Windows.Amd64**, und wählen Sie erneut **Build and Push IoT Edge Modules** (IoT Edge-Module erstellen und pushen). 

8. Öffnen Sie erneut die Datei **deployment.windows-amd64.json**. Beachten Sie, dass beim erneuten Ausführen des Befehls zum Erstellen und Pushen keine neue Datei erstellt wurde. Stattdessen wurde dieselbe Datei aktualisiert, um die Änderungen widerzuspiegeln. Das Image „IotEdgeModule1“ verweist jetzt auf Version 0.0.2 des Containers. Mit dieser Änderung im Bereitstellungsmanifest teilen Sie dem IoT Edge-Gerät mit, dass per Pullvorgang eine neue Version des Moduls abgerufen werden kann. 

9. Sie können noch genauer überprüfen, was mit dem Befehl zum Erstellen und Pushen durchgeführt wurde, indem Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Containerregistrierung navigieren. 

10. Wählen Sie in Ihrer Containerregistrierung **Repositorys** und dann **iotedgemodule1**. Stellen Sie sicher, dass beide Versionen des Images per Pushvorgang an die Registrierung übertragen wurden.

    ![Anzeigen beider Imageversionen in der Containerregistrierung](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen und Pushen Ihres Modulimages Fehler auftreten, liegt dies häufig an der Docker-Konfiguration auf Ihrem Entwicklungscomputer. Überprüfen Sie für Ihre Konfiguration Folgendes: 

* Haben Sie den Befehl `docker login` mit den Anmeldeinformationen ausgeführt, die Sie aus Ihrer Containerregistrierung kopiert haben? Hierbei handelt es sich um andere Anmeldeinformationen als für die Anmeldung an Azure. 
* Ist Ihr Containerrepository richtig? Verfügt es über den richtigen Namen für die Containerregistrierung und den richtigen Modulnamen? Öffnen Sie die Datei **module.json** im Ordner „IotEdgeModule1“, um dies zu überprüfen. Der Repositorywert sollte das Format **\<Registrierungsname\>.azurecr.io/iotedgemodule1** haben. 
* Wenn Sie für Ihr Modul einen anderen Namen als **IotEdgeModule1** verwendet haben: Wurde dieser Name in der gesamten Projektmappe einheitlich verwendet?
* Entsprechen die auf Ihrem Computer ausgeführten Containertypen den von Ihnen erstellten Containertypen? Dieses Tutorial ist für Windows IoT Edge-Geräte bestimmt. Ihre Visual Studio-Dateien sollten daher über die Erweiterung **windows-amd64** verfügen, und unter Docker Desktop sollten Windows-Container ausgeführt werden. 

## <a name="deploy-modules-to-device"></a>Bereitstellen von Modulen auf dem Gerät

Sie haben sich vergewissert, dass die Containerimages in Ihrer Containerregistrierung gespeichert sind. Diese können nun also auf einem Gerät bereitgestellt werden. Sorgen Sie dafür, dass Ihr IoT Edge-Gerät korrekt ausgeführt wird. 

1. Öffnen Sie den Cloud-Explorer in Visual Studio, und erweitern Sie die Details für Ihren IoT-Hub. 

2. Wählen Sie den Namen des Geräts aus, auf dem Sie die Bereitstellung durchführen möchten. Wählen Sie in der Liste **Aktionen** die Option **Bereitstellung erstellen**.

   ![Erstellen einer Bereitstellung für ein einzelnes Gerät](./media/tutorial-develop-for-windows/create-deployment.png)


3. Navigieren Sie im Datei-Explorer zum Ordner „config“ Ihres Projekts, und wählen Sie die Datei **deployment.windows-amd64.json** aus. Diese Datei befindet sich häufig unter `C:\Users\<username>\source\repos\AzureIotEdgeApp1\AzureIotEdgeApp1.Windows.Amd64\config\deployment.windows-amd64.json`.

   Verwenden Sie nicht die Datei „deployment.template.json“, da diese nicht die vollständigen Modulimagewerte enthält. 

4. Erweitern Sie die Details für Ihr IoT Edge-Gerät im Cloud-Explorer, um die Module auf Ihrem Gerät anzuzeigen.

5. Verwenden Sie die Schaltfläche **Aktualisieren**, um den Gerätestatus zu aktualisieren und sich zu vergewissern, dass die Module „tempSensor“ und „IotEdgeModule1“ auf Ihrem Gerät bereitgestellt wurden. 


   ![Anzeigen von Modulen, die auf Ihrem IoT Edge-Gerät ausgeführt werden](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Anzeigen von Nachrichten vom Gerät

Der Code von „IotEdgeModule1“ empfängt Nachrichten über die zugehörige Eingabewarteschlange und leitet diese an die Ausgabewarteschlange weiter. Im Bereitstellungsmanifest wurden Routen deklariert, über die Nachrichten von „tempSensor“ an „IotEdgeModule1“ und von „IotEdgeModule1“ dann an IoT Hub übergeben wurden. Mit den Azure IoT Edge-Tools für Visual Studio können Sie Nachrichten anzeigen, wenn diese von Ihren einzelnen Geräten auf IoT Hub eingehen. 

1. Wählen Sie im Cloud-Explorer von Visual Studio den Namen des IoT Edge-Geräts aus, auf dem Sie die Bereitstellung durchgeführt haben. 

2. Wählen Sie im Menü **Aktionen** die Option **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus.

3. Im Abschnitt **Ausgabe** von Visual Studio können Sie dann verfolgen, wie die Nachrichten auf Ihrem IoT-Hub eingehen. 

   Es dauert ggf. einige Minuten, bis beide Module gestartet wurden. Die IoT Edge-Runtime muss das neue Bereitstellungsmanifest empfangen, die Modulimages per Pullvorgang aus der Containerruntime abrufen und dann jedes neue Modul starten. Wenn Sie 

   ![Anzeigen von eingehenden Gerät-zu-Cloud-Nachrichten](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Anzeigen von Änderungen auf dem Gerät

Wenn Sie die Vorgänge direkt auf dem Gerät verfolgen möchten, können Sie die Befehle in diesem Abschnitt verwenden, um die IoT Edge-Runtime und die Module zu untersuchen, die auf Ihrem Gerät ausgeführt werden. 

Die Befehle in diesem Abschnitt gelten für Ihr IoT Edge-Gerät und nicht für Ihren Entwicklungscomputer. Wenn Sie einen virtuellen Computer für Ihr IoT Edge-Gerät verwenden, sollten Sie nun die Verbindung dafür herstellen. Navigieren Sie in Azure zur Übersichtsseite des virtuellen Computers, und wählen Sie die Option **Verbinden**, um auf die Remotedesktopverbindung zuzugreifen. Öffnen Sie auf dem Gerät einen Befehl oder das PowerShell-Fenster, um die `iotedge`-Befehle auszuführen.

* Zeigen Sie alle Module an, die auf Ihrem Gerät bereitgestellt wurden, und überprüfen Sie ihren Status:

   ```cmd
   iotedge list
   ```

   Es sollten vier Module angezeigt werden: die beiden IoT Edge-Runtime-Module, „tempSensor“ und „IotEdgeModule1“. Für alle vier Module sollte „Wird ausgeführt“ angezeigt werden.

* Überprüfen Sie die Protokolle für ein bestimmtes Modul:

   ```cmd
   iotedge logs <module name>
   ```

   Bei IoT Edge-Modulen wird die Groß-/Kleinschreibung beachtet. 

   Für die Protokolle von „tempSensor“ und „IotEdgeModule1“ sollten die verarbeiteten Nachrichten angezeigt werden. Das Modul „edgeAgent“ ist für das Starten der anderen Module zuständig. Die Protokolle dieses Moduls enthalten also Informationen zur Implementierung des Bereitstellungsmanifests. Falls ein Modul nicht aufgelistet ist oder nicht ausgeführt wird, sind die Fehler wahrscheinlich in den Protokollen von „edgeAgent“ enthalten. Das Modul „edgeHub“ ist für die Kommunikation zwischen den Modulen und IoT Hub zuständig. Wenn die Module betriebsbereit sind und ausgeführt werden, aber die Nachrichten nicht auf Ihrem IoT-Hub ankommen, sind die Fehler wahrscheinlich in den Protokollen von „edgeHub“ enthalten. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Visual Studio 2019 auf Ihrem Entwicklungscomputer eingerichtet und damit Ihr erstes IoT Edge-Modul bereitgestellt. Nachdem Sie sich nun mit den grundlegenden Konzepten vertraut gemacht haben, können Sie versuchen, einem Modul Funktionen hinzuzufügen, damit es den Datenfluss analysieren kann. Wählen Sie Ihre bevorzugte Sprache aus: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
