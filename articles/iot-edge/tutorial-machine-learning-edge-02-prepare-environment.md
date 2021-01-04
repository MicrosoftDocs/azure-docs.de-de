---
title: 'Tutorial: Einrichten einer Umgebung: Machine Learning in Azure IoT Edge'
description: 'Tutorial: Es wird beschrieben, wie Sie Ihre Umgebung für die Entwicklung und Bereitstellung von Modulen für maschinelles Lernen im Edgebereich vorbereiten.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d86a273a69203a92a9b437f021486feb3bcb31da
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932369"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Tutorial: Einrichten einer Umgebung für maschinelles Lernen in IoT Edge

Dieser Artikel enthält Informationen dazu, wie Sie Ihre Umgebung für die Entwicklung und Bereitstellung vorbereiten. Richten Sie zuerst einen Entwicklungscomputer mit allen benötigten Tools ein. Erstellen Sie anschließend die erforderlichen Cloudressourcen in Azure.

In diesem Abschnitt des Tutorials lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Einrichten eines virtuellen Computers für die Entwicklung
> * Einrichten einer IoT Hub-Instanz und eines Cloudspeichers für Ihre Entwicklungsumgebung

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel ist Teil einer Tutorialreihe zur Verwendung von Azure Machine Learning für IoT Edge. Jeder Artikel in der Reihe baut auf der Arbeit im vorherigen Artikel auf. Wenn Sie diesen Artikel direkt aufgerufen haben, wechseln Sie zum [ersten Artikel](tutorial-machine-learning-edge-01-intro.md) in der Reihe.

## <a name="set-up-the-development-vm"></a>Einrichten des virtuellen Entwicklungscomputers

Dieser Schritt wird normalerweise von einem Cloudentwickler ausgeführt. Ein Teil der Software ist ggf. auch für einen Data Scientist hilfreich.

Wir haben ein PowerShell-Skript zusammengestellt, mit dem ein virtueller Azure-Computer erstellt wird, für den viele erforderliche Komponenten bereits konfiguriert sind. Die von uns erstellte VM muss die [geschachtelte Virtualisierung](../virtual-machines/windows/nested-virtualization.md) verarbeiten können. Aus diesem Grund haben wir einen Computer mit der Größe [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) gewählt.

Für die Einrichtung des virtuellen Entwicklungscomputers wird Folgendes verwendet:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop für Windows](https://www.docker.com/products/docker-desktop)
* [Git für Windows](https://gitforwindows.org/)
* [Git Credential Manager für Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](/powershell/azure/)
* [VS Code-Erweiterungen](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Der virtuelle Entwicklungscomputer ist nicht unbedingt erforderlich. Alle Entwicklungstools können auf einem lokalen Computer ausgeführt werden. Wir empfehlen Ihnen aber dringend die Nutzung des virtuellen Computers, damit bei Ihnen die gleichen Voraussetzungen bestehen.

Es dauert ungefähr 30 Minuten, um den virtuellen Computer zu erstellen und zu konfigurieren.

1. Klonen Sie das Beispielrepository [Machine Learning and IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample), oder laden Sie es auf Ihren lokalen Computer herunter.

1. Öffnen Sie PowerShell als Administrator, und navigieren Sie zum Verzeichnis **\IoTEdgeAndMlSample\DevVM** unter dem Stammverzeichnis, in das Sie den Code heruntergeladen haben. Das Stammverzeichnis für Ihre Quelle hat die Bezeichnung `srcdir`.

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   Das Verzeichnis „DevVM“ enthält die Dateien, die zum Erstellen eines für dieses Tutorial geeigneten virtuellen Azure-Computers benötigt werden.

1. Führen Sie den folgenden Befehl aus, um die Ausführung von Skripts zu ermöglichen. Wählen Sie **Ja, alle**, wenn die Aufforderung angezeigt wird.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Führen Sie „Create-AzureDevVM.ps1“ aus.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Geben Sie bei entsprechender Aufforderung die folgenden Informationen ein:

    * **Azure-Abonnement-ID**: Ihre Abonnement-ID, die Sie im Portal unter [Azure-Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) finden.
    * **Ressourcengruppenname**: Der Name einer neuen oder vorhandenen Ressourcengruppe in Azure.
    * **Standort**: Wählen Sie einen Azure-Standort aus, an dem der virtuelle Computer erstellt wird. Beispiel: „USA, Westen 2“ oder „Europa, Norden“. Weitere Informationen finden Sie unter [Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Benutzername**: Geben Sie für das Administratorkonto für den virtuellen Computer einen einprägsamen Namen an.
    * **Kennwort**: Legen Sie ein Kennwort für das Administratorkonto für den virtuellen Computer fest.

   Die Ausführung des Skripts dauert mehrere Minuten, und es werden die folgenden Schritte ausgeführt:

    1. Installiert das [Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).
    1. Fordert Sie zum Anmelden bei Azure auf.
    1. Überprüft die Informationen für die Erstellung Ihres virtuellen Computers. Drücken Sie **y** oder die **EINGABETASTE**, um fortzufahren.
    1. Erstellt die Ressourcengruppe, falls sie nicht vorhanden ist.
    1. Stellt den virtuellen Computer bereit.
    1. Aktiviert Hyper-V auf dem virtuellen Computer.
    1. Installiert die erforderliche Software für die Entwicklung und das Klonen des Beispielrepositorys.
    1. Startet die virtuellen Computer neu.
    1. Erstellt auf Ihrem Desktop eine RDP-Datei für die Verbindungsherstellung mit dem virtuellen Computer.

   Wenn Sie zum Eingeben des Namens des virtuellen Computers aufgefordert werden, um diesen neu zu starten, können Sie den Namen aus der Ausgabe des Skripts kopieren. In der Ausgabe wird auch der Pfad zur RDP-Datei für die Verbindungsherstellung mit dem virtuellen Computer angezeigt.

### <a name="set-auto-shutdown-schedule"></a>Festlegen eines Zeitplans zum automatischen Herunterfahren

Als Beitrag zur Kostenreduzierung wurde der virtuelle Entwicklungscomputer mit einem Zeitplan für das automatische Herunterfahren erstellt, der auf 19:00 Uhr PST festgelegt ist. Unter Umständen müssen Sie diese Einstellung je nach Standort und Zeitplan aktualisieren. Aktualisieren Sie den Zeitplan zum Herunterfahren wie folgt:

1. Navigieren Sie im Azure-Portal zu dem virtuellen Computer, der mit dem Skript erstellt wurde.

1. Wählen Sie im Menü auf der linken Seite unter **Vorgänge** die Option **Automatisch herunterfahren** aus.

1. Passen Sie **Geplantes Herunterfahren** und **Zeitzone** wie gewünscht an, und wählen Sie anschließend **Speichern** aus.

## <a name="connect-to-the-development-vm"></a>Herstellen einer Verbindung mit dem virtuellen Entwicklungscomputer

Nachdem wir nun eine VM erstellt haben, müssen wir die Installation der Software abschließen, die für das Tutorial benötigt wird.

1. Doppelklicken Sie auf die RDP-Datei, die vom Skript auf Ihrem Desktop erstellt wurde.

1. Es wird ein Dialogfeld mit dem Hinweis angezeigt, dass der Herausgeber der Remoteverbindung unbekannt ist. Dies ist akzeptabel, und Sie können **Verbinden** auswählen.

1. Geben Sie das Administratorkennwort ein, das Sie bei der Erstellung des virtuellen Computers angegeben haben, und klicken Sie auf **OK**.

1. Sie werden aufgefordert, das Zertifikat für den virtuellen Computer zu akzeptieren. Wählen Sie **Ja** aus.

## <a name="install-visual-studio-code-extensions"></a>Installieren von Visual Studio Code-Erweiterungen

Nachdem Sie nun eine Verbindung mit dem Entwicklungscomputer hergestellt haben, sollten Sie Visual Studio Code einige nützliche Erweiterungen hinzufügen, um die Entwicklungsumgebung zu vereinfachen.

1. Stellen Sie eine Verbindung mit dem virtuellen Entwicklungscomputer her, öffnen Sie ein PowerShell-Fenster, und navigieren Sie zum Verzeichnis **C:\source\IoTEdgeAndMlSample\DevVM**. Dieses Verzeichnis wurde mit dem Skript erstellt, mit dem der virtuelle Computer erstellt wurde.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Führen Sie den folgenden Befehl aus, um die Ausführung von Skripts zu ermöglichen. Wählen Sie **Ja, alle**, wenn die Aufforderung angezeigt wird.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Führen Sie das Skript für die Visual Studio Code-Erweiterungen aus.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Die Ausführung des Skripts dauert einige Minuten, während die VS Code-Erweiterungen installiert werden:

    * Azure IoT-Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Einrichten von IoT Hub und Storage

Diese Schritte werden normalerweise von einem Cloudentwickler ausgeführt.

Azure IoT Hub ist das Kernstück jeder IoT-Anwendung, weil damit die sichere Kommunikation zwischen IoT-Geräten und der Cloud gesteuert wird. Es ist das wichtigste Element für die Koordination des Betriebs einer IoT Edge Machine Learning-Lösung.

* Für IoT Hub werden Routen genutzt, um eingehende Daten von IoT-Geräten an andere nachgeschaltete Dienste zu leiten. Wir nutzen IoT Hub-Routen, um Gerätedaten an Azure Storage zu senden. In Azure Storage werden die Gerätedaten von Azure Machine Learning genutzt, um den Klassifizierer für die verbleibende Nutzungsdauer (Remaining Useful Life, RUL) zu trainieren.

* Später im Tutorial verwenden wir IoT Hub zum Konfigurieren und Verwalten unseres Azure IoT Edge-Geräts.

In diesem Abschnitt nutzen Sie ein Skript, um eine Azure IoT Hub-Instanz und ein Azure Storage-Konto zu erstellen. Anschließend konfigurieren Sie im Azure-Portal eine Route, über die vom Hub empfangene Daten an einen Azure Storage-Container weitergeleitet werden. Es dauert ungefähr zehn Minuten, bis diese Schritte abgeschlossen sind.

1. Stellen Sie eine Verbindung mit dem virtuellen Entwicklungscomputer her, öffnen Sie ein PowerShell-Fenster, und navigieren Sie zum Verzeichnis **IoTHub**.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Führen Sie das Erstellungsskript aus. Verwenden Sie die gleichen Werte für Abonnement-ID, Standort und Ressourcengruppe wie beim Erstellen des virtuellen Entwicklungscomputers.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Sie werden aufgefordert, sich bei Azure anzumelden.
    * Das Skript bestätigt die Informationen für die Erstellung Ihres Hubs und Speicherkontos. Drücken Sie **y** oder die **EINGABETASTE**, um fortzufahren.

Die Ausführung des Skripts dauert ungefähr zwei Minuten. Nach Abschluss des Vorgangs gibt das Skript den Namen des IoT-Hubs und des Speicherkontos aus.

## <a name="review-route-to-storage-in-iot-hub"></a>Überprüfen der Route zum Speicher in IoT Hub

Bei der Erstellung des IoT-Hubs wurden mit dem Skript, das wir im vorherigen Abschnitt ausgeführt haben, auch ein benutzerdefinierter Endpunkt und eine Route erstellt. IoT-Hub-Routen bestehen aus einem Abfrageausdruck und einem Endpunkt. Wenn eine Nachricht mit dem Ausdruck übereinstimmt, werden die Daten über die Route an den zugeordneten Endpunkt gesendet. Bei Endpunkten kann es sich um Event Hubs und Service Bus-Warteschlangen und -Themen handeln. In diesem Fall ist der Endpunkt ein Blobcontainer in einem Speicherkonto. Wir verwenden das Azure-Portal, um die vom Skript erstellte Route zu überprüfen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zu der Ressourcengruppe, die Sie für dieses Tutorial verwenden.

1. Wählen Sie in der Liste mit den Ressourcen den IoT-Hub aus, der mit dem Skript erstellt wurde. Der Name endet auf zufällig erzeugte Zeichen, z. B. `IotEdgeAndMlHub-jrujej6de6i7w`.

1. Wählen Sie im Menü im linken Bereich unter **Messaging** die Option **Nachrichtenrouting** aus.

1. Wählen Sie auf der Seite **Nachrichtenrouting** die Registerkarte **Benutzerdefinierte Endpunkte** aus.

1. Erweitern Sie den Abschnitt **Speicher**:

   ![Überprüfen, ob „turbofanDeviceStorage“ in der Liste mit den benutzerdefinierten Endpunkten enthalten ist](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   Sie sehen, dass **turbofanDeviceStorage** in der Liste mit den benutzerdefinierten Endpunkten enthalten ist. Beachten Sie die folgenden Eigenschaften dieses Endpunkts:

   * Er verweist auf den von Ihnen erstellten Blobspeichercontainer mit dem Namen `devicedata`. Dies wird über den **Containernamen** angegeben.
   * Unter **Format des Dateinamens** ist „Partition“ als letztes Element des Namens angegeben. Dieses Format ist besser für die Dateivorgänge geeignet, die wir später in diesem Tutorial mit Azure Notebooks durchführen.
   * Als **Status** sollte „Fehlerfrei“ angezeigt werden.

1. Wählen Sie die Registerkarte **Routen**.

1. Wählen Sie die Route mit dem Namen **turbofanDeviceDataToStorage**.

1. Beachten Sie auf der Seite **Details zur Route**, dass **turbofanDeviceStorage** der Endpunkt der Route ist.

   ![Überprüfen der Details zur Route „turbofanDeviceDataToStorage“](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Sehen Sie sich die **Routingabfrage** an, die auf **true** festgelegt ist. Diese Einstellung bedeutet, dass alle Gerätetelemetrienachrichten mit dieser Route übereinstimmen, sodass alle Nachrichten an den Endpunkt **turbofanDeviceStorage** gesendet werden.

1. Da keine Änderungen vorgenommen wurden, können Sie die Seite einfach schließen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Dieses Tutorial ist Teil einer Reihe, in der jeder Artikel auf den Schritten aufbaut, die jeweils im vorherigen Artikel ausgeführt wurden. Warten Sie mit dem Bereinigen von Ressourcen, bis Sie das letzte Tutorial abgeschlossen haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben wir eine IoT Hub-Instanz erstellt und eine Route zu einem Azure Storage-Konto konfiguriert. Als Nächstes senden wir Daten von einer Gruppe simulierter Geräte über die IoT Hub-Instanz an das Speicherkonto. Später in diesem Tutorial nach der Konfiguration unseres IoT Edge-Geräts und der Module gehen wir noch einmal auf Routen und die Routingabfrage ein.

Fahren Sie mit dem nächsten Artikel fort, um ein zu überwachendes simuliertes Gerät zu erstellen.

> [!div class="nextstepaction"]
> [Generieren von Gerätedaten](tutorial-machine-learning-edge-03-generate-data.md)
