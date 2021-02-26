---
title: 'Schnellstart: Erstellen eines Azure IoT Edge-Geräts unter Windows | Microsoft-Dokumentation'
description: In diesem Schnellstart erfahren Sie, wie ein IoT Edge-Geräte erstellen und anschließend vordefinierten Code remote über das Azure-Portal bereitstellen.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a00e5d262724f4799f8b7b91a54e4b2710f4ce2b
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008067"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Schnellstart: Bereitstellen Ihres ersten IoT Edge-Moduls auf einem Windows-Gerät (Vorschauversion)

In dieser Schnellstartanleitung können Sie Azure IoT Edge ausprobieren, indem Sie Code in Containern auf einem IoT Edge-Gerät für Linux unter Windows bereitstellen. IoT Edge ermöglicht Ihnen die Remoteverwaltung von Code auf Ihren Geräten, damit Sie noch mehr Workloads an den Edgebereich senden können. In dieser Schnellstartanleitung empfiehlt es sich, ein eigenes Gerät zu verwenden, um zu sehen, wie einfach es ist, Azure IoT Edge für Linux unter Windows zu verwenden.

In diesem Schnellstart erfahren Sie, wie die folgenden Aufgaben ausgeführt werden:

* Erstellen Sie einen IoT Hub.
* Registrieren eines IoT Edge-Geräts für Ihren IoT Hub
* Installieren und Starten der Runtime von IoT Edge für Linux unter Windows auf Ihrem Gerät
* Durchführen der Remotebereitstellung eines Moduls für ein IoT Edge-Gerät und Senden von Telemetriedaten

![Diagramm: Architektur dieser Schnellstartanleitung für Ihr Gerät und die Cloud](./media/quickstart/install-edge-full.png)

In dieser Schnellstartanleitung erfahren Sie Schritt für Schritt, wie Sie Ihr Gerät mit Azure IoT Edge für Linux unter Windows einrichten. Anschließend stellen Sie ein Modul über das Azure-Portal auf Ihrem Gerät bereit. Das verwendete Modul ist ein simulierter Sensor, mit dem Daten zu Temperatur, Luftfeuchtigkeit und Luftdruck generiert werden. Andere Tutorials zu Azure IoT Edge bauen hierauf auf, indem Module zum Analysieren der simulierten Daten bereitgestellt werden, um geschäftsrelevante Erkenntnisse zu gewinnen.

Wenn Sie über kein aktives Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

>[!NOTE]
>IoT Edge für Linux unter Windows befindet sich in der [Public Preview-Phase](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Bereiten Sie die Umgebung für die Azure CLI vor.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Erstellen Sie eine Cloudressourcengruppe zum Verwalten aller Ressourcen, die Sie in dieser Schnellstartanleitung verwenden.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Stellen Sie sicher, dass Ihr IoT Edge-Gerät die folgenden Anforderungen erfüllt:

* Windows-PC oder -Server, Version 1809 oder höher
* Mindestens 4 GB Arbeitsspeicher (empfohlen: 8 GB)
* 10 GB freier Festplattenspeicher

>[!NOTE]
>In dieser Schnellstartanleitung wird Windows Admin Center verwendet, um eine Bereitstellung von IoT Edge für Linux unter Windows zu erstellen. Sie können auch PowerShell verwenden. Führen Sie die Schritte in der Schrittanleitung zum [Installieren und Bereitstellen von Azure IoT Edge für Linux auf einem Windows-Gerät](how-to-install-iot-edge-on-windows.md) aus, falls Sie Ihre Bereitstellung mit PowerShell erstellen möchten.

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

Erstellen Sie zunächst mit der Azure CLI einen IoT-Hub.

![Diagramm: Schritt zum Erstellen eines IoT-Hubs](./media/quickstart/create-iot-hub.png)

Für diese Schnellstartanleitung können Sie auch die kostenlose Version von Azure IoT Hub nutzen. Wenn Sie IoT Hub schon einmal genutzt und bereits einen Hub erstellt haben, können Sie diesen IoT-Hub verwenden.

Mit dem folgenden Code wird ein kostenloser **F1**-Hub in der Ressourcengruppe `IoTEdgeResources` erstellt. Ersetzen Sie `{hub_name}` durch einen eindeutigen Namen für Ihren IoT-Hub. Es kann einige Minuten dauern, bis ein IoT-Hub erstellt wurde.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Ändern Sie die SKU in `S1`, falls Sie eine Fehlermeldung erhalten, weil bereits ein kostenloser Hub in Ihrem Abonnement vorhanden ist. Sollten Sie eine Fehlermeldung mit dem Hinweis erhalten, dass der Name für den IoT-Hub nicht verfügbar ist, ist bereits ein Hub mit diesem Namen vorhanden. Probieren Sie einen neuen Namen aus.

## <a name="register-an-iot-edge-device"></a>Registrieren eines IoT Edge-Geräts

Registrieren Sie ein IoT Edge-Gerät bei Ihrem neu erstellten IoT Hub.

![Diagramm: Schritt zum Registrieren eines Geräts mit einer IoT-Hub-Identität](./media/quickstart/register-device.png)

Erstellen Sie eine Geräteidentität für das simulierte Gerät, sodass es mit dem IoT Hub kommunizieren kann. Die Geräteidentität befindet sich in der Cloud, und Sie verwenden eine eindeutige Geräte-Verbindungszeichenfolge, um einem physischen Gerät eine Geräteidentität zuzuordnen.

IoT Edge-Geräte weisen ein anderes Verhalten als typische IoT-Geräte auf und können anders verwaltet werden. Verwenden Sie das Flag `--edge-enabled`, um zu deklarieren, dass diese Identität für ein IoT Edge-Gerät bestimmt ist.

1. Geben Sie in Azure Cloud Shell den folgenden Befehl ein, um in Ihrem Hub ein Gerät mit dem Namen **myEdgeDevice** zu erstellen.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Falls Sie eine Fehlermeldung zu `iothubowner`-Richtlinienschlüsseln erhalten, sollten Sie überprüfen, ob in Cloud Shell die aktuelle Version der Azure IoT-Erweiterung ausgeführt wird.

1. Zeigen Sie die Verbindungszeichenfolge für Ihr Gerät an, über die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft wird. Sie enthält den Namen Ihres IoT-Hubs, den Namen Ihres Geräts und einen gemeinsam verwendeten Schlüssel, mit dem die Verbindungen zwischen diesen Komponenten authentifiziert werden.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Kopieren Sie den Wert des Schlüssels `connectionString` aus der JSON-Ausgabe, und speichern Sie ihn. Dieser Wert ist die Verbindungszeichenfolge des Geräts. Sie verwenden ihn, um im nächsten Abschnitt die IoT Edge-Runtime zu konfigurieren.

     ![Screenshot: Ausgabe von „connectionString“ in Cloud Shell](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Installieren und Starten der IoT Edge-Runtime

Installieren Sie IoT Edge für Linux unter Windows auf Ihrem Gerät, und konfigurieren Sie es mit der Geräte-Verbindungszeichenfolge.

![Diagramm: Schritt zum Starten der IoT Edge-Runtime](./media/quickstart/start-runtime.png)

1. [Laden Sie Windows Admin Center herunter.](https://aka.ms/WACDownloadEFLOW)

1. Befolgen Sie die Aufforderungen im Installations-Assistenten, um Windows Admin Center auf Ihrem Gerät einzurichten.

1. Öffnen Sie Windows Admin Center.

1. Wählen Sie oben rechts das Symbol für die **Einstellungen** (Zahnrad) und dann **Erweiterungen** aus.

1. Wählen Sie auf der Registerkarte **Feeds** die Option **Hinzufügen** aus.

1. Geben Sie `https://aka.ms/wac-insiders-feed` in das Textfeld ein, und wählen Sie anschließend **Hinzufügen** aus.

1. Navigieren Sie nach dem Hinzufügen des Feeds zur Registerkarte **Verfügbare Erweiterungen**, und warten Sie darauf, dass die Liste mit den Erweiterungen aktualisiert wird.

1. Wählen Sie in der Liste **Verfügbare Erweiterungen** die Option **Azure IoT Edge** aus.

1. Installieren Sie die Erweiterung.

1. Wählen Sie nach der Installation der Erweiterung oben links die Option **Windows Admin Center** aus, um zur Hauptseite des Dashboards zu navigieren.

     Die Verbindung **localhost** steht für den PC, auf dem Sie Windows Admin Center ausführen.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Screenshot: Startseite von Windows Admin Center":::

1. Wählen Sie **Hinzufügen**.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Screenshot: Auswahl der Schaltfläche „Hinzufügen“ in Windows Admin Center":::

1. Wählen Sie auf der Azure IoT Edge-Kachel die Option **Neu erstellen** aus, um den Installations-Assistenten zu starten.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Screenshot: Erstellen einer neuen Bereitstellung auf der Azure IoT Edge-Kachel":::

1. Fahren Sie im Installations-Assistenten fort, um die Microsoft-Software-Lizenzbedingungen zu akzeptieren, und wählen Sie anschließend **Weiter** aus.

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Screenshot: Auswahl von „Weiter“ im Installations-Assistenten":::

1. Wählen Sie die Option **Optionale Diagnosedaten** aus, und dann **Weiter: Bereitstellen**. Nach dieser Auswahl werden erweiterte Diagnosedaten angezeigt, mit denen Microsoft die Servicequalität überwachen und aufrechterhalten kann.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Screenshot: Optionen für Diagnosedaten":::

1. Wählen Sie auf dem Bildschirm **Zielgerät auswählen** das gewünschte Zielgerät aus, um sich zu vergewissern, dass es die Mindestanforderungen erfüllt. Im Rahmen dieser Schnellstartanleitung wird IoT Edge auf dem lokalen Gerät installiert. Wählen Sie daher die Verbindung **localhost** aus. Wenn das Zielgerät die Anforderungen erfüllt, wählen Sie **Weiter** aus, um fortzufahren.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Screenshot: Liste mit Zielgeräten":::

1. Wählen Sie **Weiter** aus, um die Standardeinstellungen zu übernehmen. Auf dem Bereitstellungsbildschirm werden das Herunterladen des Pakets, das Installieren des Pakets, das Konfigurieren des Hosts und das abschließende Einrichten des virtuellen Linux-Computers (VM) angezeigt. Eine erfolgreiche Bereitstellung sieht wie folgt aus:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Screenshot: Erfolgreiche Bereitstellung":::

1. Klicken Sie auf **Weiter: Verbinden**, um zum letzten Schritt zu gelangen und Ihr Azure IoT Edge-Gerät mit seiner Geräte-ID aus Ihrer IoT Hub-Instanz bereitzustellen.

1. Fügen Sie die Verbindungszeichenfolge, die Sie [weiter oben in dieser Schnellstartanleitung](#register-an-iot-edge-device) kopiert haben, in das Feld **Geräte-Verbindungszeichenfolge** ein. Wählen Sie anschließend die Option **Provisioning with the selected method** (Mit der ausgewählten Methode bereitstellen) aus.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Screenshot: Verbindungszeichenfolge im Feld „Geräte-Verbindungszeichenfolge“":::

1. Wählen Sie nach Abschluss der Bereitstellung die Option **Fertig stellen** aus, um den Vorgang abzuschließen und zum Startbildschirm von Windows Admin Center zurückzukehren. Ihr Gerät sollte nun als IoT Edge-Gerät aufgeführt werden.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Screenshot: Alle Verbindungen in Windows Admin Center":::

1. Wählen Sie Ihr Azure IoT Edge-Gerät aus, um das zugehörige Dashboard anzuzeigen. Sie sollten sehen, dass die Workloads von Ihrem Gerätezwilling in Azure IoT Hub bereitgestellt wurden. In der **Liste der IoT Edge-Module** sollte ein einzelnes ausgeführtes Modul (**edgeAgent**) angezeigt werden, und der **IoT Edge-Status** sollte **Aktiv (wird ausgeführt)** lauten.

Ihr IoT Edge-Gerät ist jetzt konfiguriert. Es kann nun zum Ausführen von in der Cloud bereitgestellten Modulen verwendet werden.

## <a name="deploy-a-module"></a>Bereitstellen eines Moduls

Verwalten Sie Ihr Azure IoT Edge-Gerät über die Cloud, um ein Modul bereitzustellen, das Telemetriedaten an die IoT Hub-Instanz sendet.

![Diagramm: Schritt zum Bereitstellen eines Moduls](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-the-generated-data"></a>Anzeigen der generierten Daten

In diesem Schnellstart haben Sie ein neues IoT Edge-Gerät erstellt und die IoT Edge-Runtime darauf installiert. Anschließend haben Sie das Azure-Portal verwendet, um ein IoT Edge-Modul zur Ausführung auf dem Gerät bereitzustellen, ohne Änderungen am Gerät selbst vornehmen zu müssen.

Von dem Modul, das Sie gepusht haben, werden Daten für die Beispielumgebung generiert, die Sie später zum Testen verwenden können. Der simulierte Sensor überwacht sowohl einen Computer als auch seine Umgebung. Beispielsweise kann sich dieser Sensor in einem Serverraum, in einer Fabrik oder in einer Windturbine befinden. Die gesendeten Nachrichten enthalten Informationen zu Umgebungstemperatur und Luftfeuchtigkeit, Computertemperatur und Druck sowie einen Zeitstempel. In IoT Edge-Tutorials werden diese vom Modul erstellten Daten als Testdaten für die Analyse verwendet.

Vergewissern Sie sich in der Befehlsshell in Windows Admin Center, dass das über die Cloud bereitgestellte Modul auf Ihrem IoT Edge-Gerät ausgeführt wird.

1. Stellen Sie eine Verbindung mit Ihrem neu erstellten IoT Edge-Gerät her.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Screenshot: Auswahl von „Verbinden“ in Windows Admin Center":::

     Auf der Seite **Übersicht** werden die Optionen für **Liste der IoT Edge-Module** und **IoT Edge-Status** angezeigt. Sie können die bereitgestellten Module und den Gerätestatus sehen.  

1. Wählen Sie unter **Tools** die Option **Befehlsshell** aus. Die Befehlsshell ist ein PowerShell-Terminal, in dem automatisch Secure Shell (SSH) verwendet wird, um eine Verbindung mit dem virtuellen Linux-Computer Ihres Azure IoT Edge-Geräts auf Ihrem Windows-PC herzustellen.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Screenshot: Öffnen der Befehlsshell":::

1. Führen Sie den folgenden Bash-Befehl aus, um die drei Module auf Ihrem Gerät zu überprüfen:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Screenshot: Befehlsshell mit Ausgabe von „iotedge list“":::

1. Zeigen Sie die vom Temperatursensormodul an die Cloud gesendeten Nachrichten an.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >Bei IoT Edge-Befehlen wird beim Verweisen auf Modulnamen Groß- und Kleinschreibung unterschieden.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Screenshot: Liste mit Nachrichten, die vom Modul an die Cloud gesendet werden":::

Sie können auch die [Azure IoT Hub-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) verwenden, um das Eintreffen von Nachrichten auf Ihrem IoT-Hub zu verfolgen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Überspringen Sie diesen Schritt, falls Sie mit dem Durcharbeiten der IoT Edge-Tutorials fortfahren möchten. Sie können das Gerät verwenden, das Sie in dieser Schnellstartanleitung registriert und eingerichtet haben. Andernfalls können Sie die erstellten Azure-Ressourcen löschen, um Kosten zu vermeiden.

Wenn Sie Ihren virtuellen Computer und Azure IoT Hub in einer neuen Ressourcengruppe erstellt haben, können Sie diese Gruppe und alle zugehörigen Ressourcen löschen. Wenn Sie nicht die gesamte Gruppe löschen möchten, können Sie stattdessen einzelne Ressourcen löschen.

> [!IMPORTANT]
> Überprüfen Sie den Inhalt der Ressourcengruppe, um sicherzustellen, dass sie keine Elemente enthält, die Sie beibehalten möchten. Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden.

Verwenden Sie den folgenden Befehl, um die Gruppe **IoTEdgeResources** zu entfernen. Das Löschen kann einige Minuten dauern.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Sie können sich vergewissern, dass die Ressourcengruppe entfernt wurde, indem Sie diesen Befehl zum Anzeigen der Liste mit den Ressourcengruppen verwenden.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Entfernen von Azure IoT Edge für Linux unter Windows

Verwenden Sie die Dashboard-Erweiterung in Windows Admin Center, um Azure IoT Edge für Linux unter Windows zu deinstallieren.

1. Stellen Sie eine Verbindung mit dem IoT Edge-Gerät in Windows Admin Center her. Die Azure-Dashboard-Toolerweiterung wird geladen.

1. Wählen Sie **Deinstallieren** aus. Nach dem Entfernen von Azure IoT Edge wird von Windows Admin Center auf der Seite **Start** der Eintrag für die Verbindung mit dem Azure IoT Edge-Gerät entfernt.

>[!Note]
>Alternativ können Sie auch auf Ihrem IoT Edge-Gerät **Start** > **Einstellungen** > **Apps** > **Azure IoT Edge** > **Deinstallieren** auswählen, um Azure IoT Edge von Ihrem Windows-System zu entfernen. Mit diesem Verfahren wird Azure IoT Edge zwar von Ihrem IoT Edge-Gerät entfernt, aber die Verbindung ist weiterhin in Windows Admin Center vorhanden. Deinstallieren Sie zum Abschließen des Entfernungsprozesses auch Windows Admin Center über das Menü **Einstellungen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein IoT Edge-Gerät erstellt und die Azure IoT Edge-Cloudschnittstelle zum Bereitstellen von Code auf dem Gerät verwendet. Sie verfügen nun über ein Testgerät, das Rohdaten zu seiner Umgebung generiert.

Als Nächstes richten Sie Ihre lokale Entwicklungsumgebung ein, damit Sie mit der Entwicklung von IoT Edge-Modulen für Ihre Geschäftslogik beginnen können.

> [!div class="nextstepaction"]
> [Tutorial: Entwickeln von IoT Edge-Modulen für Linux-Geräte](tutorial-develop-for-linux.md)
