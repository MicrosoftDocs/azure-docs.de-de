---
title: 'Tutorial: Erkennen von Anomalien im Edgebereich in einer Lösung – Azure | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie Ihre IoT Edge-Geräte mit dem Solution Accelerator für die Remoteüberwachung überwachen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a812155474b244682613b38b9b9379fa6cdcdcd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66117504"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Tutorial: Erkennen von Anomalien im Edgebereich mit dem Solution Accelerator für die Remoteüberwachung

In diesem Tutorial konfigurieren Sie die Remoteüberwachungslösung zur Reaktion auf Anomalien, die von einem IoT Edge-Gerät erkannt werden. IoT Edge-Geräte ermöglichen Ihnen das Verarbeiten von Telemetriedaten im Edgebereich, um die an die Lösung gesendete Menge an Telemetriedaten zu reduzieren und die Reaktion auf Geräteereignisse zu beschleunigen. Weitere Informationen dazu, welchen Nutzen die Verarbeitung im Edgebereich bietet, finden Sie unter [Was ist Azure IoT Edge?](../iot-edge/about-iot-edge.md).

In diesem Tutorial wird die Verarbeitung im Edgebereich mit Remoteüberwachung am Beispiel einer simulierten Ölpumpe erläutert. Die Ölpumpe wird von der Organisation Contoso verwaltet und ist mit dem Solution Accelerator für die Remoteüberwachung verbunden. Sensoren in der Ölpumpe messen die Temperatur und den Druck. Die Bediener bei Contoso wissen, dass ein ungewöhnlicher Temperaturanstieg zu einer Abnahme der Ölpumpengeschwindigkeit führen kann. Die Bediener bei Contoso müssen die Temperatur des Geräts nicht überwachen, wenn diese innerhalb des normalen Bereichs liegt.

Contoso möchte ein intelligentes Edgemodul für die Ölpumpe bereitstellen, das Temperaturanomalien erkennt. Ein weiteres Edgemodul sendet Warnungen an die Remoteüberwachungslösung. Wenn eine Warnung empfangen wird, kann ein Bediener bei Contoso einen Wartungstechniker entsenden. Contoso könnte auch eine automatisierte Aktion konfigurieren, die ausgeführt wird, wenn die Lösung eine Warnung empfängt (z. B. das Senden einer E-Mail).

Im folgenden Diagramm sind die Hauptkomponenten des Tutorialszenarios dargestellt:

![Übersicht](media/iot-accelerators-remote-monitoring-edge/overview.png)

In diesem Tutorial führen Sie Folgendes durch:

>[!div class="checklist"]
> * Hinzufügen eines IoT Edge-Geräts zur Lösung
> * Erstellen eines IoT Edge-Manifests
> * Importieren des Manifests als Paket, das die Module definiert, die auf dem Gerät ausgeführt werden sollen
> * Bereitstellen des Pakets auf Ihrem IoT Edge-Gerät
> * Anzeigen von Warnungen vom Gerät

Auf dem IoT Edge-Gerät:

* Die Runtime empfängt das Paket und installiert die Module.
* Das Stream Analytics-Modul erkennt Temperaturanomalien in der Pumpe und sendet Befehle zum Beheben des Problems.
* Das Stream Analytics-Modul leitet gefilterte Daten an den Solution Accelerator weiter.

Dieses Tutorial verwendet einen virtuellen Linux-Computer als Azur IoT Edge-Gerät. Sie installieren außerdem ein Edge-Modul zum Simulieren der Ölpumpe.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Hinzufügen eines IoT Edge-Geräts

Zum Hinzufügen eines IoT Edge-Geräts zu Ihrem Solution Accelerator für die Remoteüberwachung müssen Sie zwei Schritte ausführen. In diesem Abschnitt erfahren Sie, wie Sie:

* Auf der Seite **Device Explorer** der Webbenutzeroberfläche für die Remoteüberwachung ein IoT Edge-Gerät hinzufügen
* Die IoT Edge-Runtime auf einer Linux-VM installieren

### <a name="add-an-iot-edge-device-to-your-solution"></a>Hinzufügen eines IoT Edge-Geräts zu Ihrer Lösung

Um dem Solution Accelerator für die Remoteüberwachung ein IoT Edge-Gerät hinzuzufügen, navigieren Sie auf der Webbenutzeroberfläche zur Seite **Device Explorer**, und klicken Sie auf **+ Neues Gerät**.

Wählen Sie im Bereich **Neues Gerät** die Option **IoT Edge-Gerät** aus, und geben Sie **oil-pump** als Geräte-ID ein. Für alle anderen Einstellungen können Sie die Standardwerte beibehalten. Klicken Sie anschließend auf **Übernehmen**:

[![Hinzufügen eines IoT Edge-Geräts](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Notieren Sie sich die Geräteverbindungszeichenfolge. Sie benötigen Sie im nächsten Abschnitt dieses Tutorials.

Wenn Sie im Solution Accelerator für die Remoteüberwachung ein Gerät beim IoT-Hub registrieren, wird es auf der Webbenutzeroberfläche auf der Seite **Device Explorer** aufgelistet:

[![Neues IoT Edge-Gerät](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Um die Verwaltung der IoT Edge-Geräte in der Lösung zu vereinfachen, erstellen Sie eine Gerätegruppe, und fügen Sie das IoT Edge-Gerät hinzu:

1. Wählen Sie das Gerät **oil-pump** in der Liste auf der Seite **Device Explorer** aus, und klicken Sie dann auf **Aufträge**.

1. Erstellen Sie einen Auftrag mit den folgenden Einstellungen, um dem Gerät das Tag **IsEdge** hinzuzufügen:

    | Einstellung | Wert |
    | ------- | ----- |
    | Auftrag     | `Tags`  |
    | Auftragsname | AddEdgeTag |
    | Schlüssel     | IsOilPump |
    | Wert   | J     |
    | Type    | Text  |

    [![Hinzufügen eines Tags](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Klicken Sie auf **Übernehmen** und dann auf **Schließen**.

1. Klicken Sie auf der Seite **Device Explorer** auf **Gerätegruppen verwalten**.

1. Klicken Sie auf **Create new device group** (Neue Gerätegruppe erstellen). Erstellen Sie eine neue Gerätegruppe mit den folgenden Einstellungen:

    | Einstellung | Wert |
    | ------- | ----- |
    | NAME    | OilPumps |
    | Feld   | Tags.IsOilPump |
    | Operator | = (ist gleich) |
    | Wert    | J |
    | Type     | Text |

    [![Erstellen einer Gerätegruppe](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Klicken Sie auf **Speichern**.

Ihr IoT Edge-Gerät ist jetzt in der Gruppe **OilPumps** enthalten.

### <a name="install-the-edge-runtime"></a>Installieren der IoT Edge-Runtime

Auf einem IoT Edge-Gerät muss die IoT Edge-Runtime installiert sein. In diesem Tutorial installieren Sie die IoT Edge-Runtime auf einer Azure-Linux-VM, um das Szenario zu testen. In den folgenden Schritten wird Azure Cloud Shell zum Installieren und Konfigurieren der VM verwendet:

1. Führen Sie zum Erstellen einer Linux-VM in Azure die folgenden Befehle aus. Sie können einen Standort in Ihrer Nähe verwenden:

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. Führen Sie zum Konfigurieren der Edge-Runtime mit der Geräteverbindungszeichenfolge den folgenden Befehl aus. Verwenden Sie dabei die zuvor notierte Geräteverbindungszeichenfolge:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Achten Sie darauf, die Verbindungszeichenfolge in doppelte Anführungszeichen zu setzen.

Damit haben Sie die IoT Edge-Runtime auf einem Linux-Gerät installiert und konfiguriert. An späterer Stelle dieses Tutorials verwenden Sie die Remoteüberwachungslösung, um IoT Edge-Module auf diesem Gerät bereitzustellen.

## <a name="create-an-edge-manifest"></a>Erstellen eines IoT Edge-Manifests

Um die Ölpumpe zu simulieren, müssen Sie Ihrem IoT Edge-Gerät die folgenden Module hinzufügen:

* Modul zur Temperatursimulation
* Azure Stream Analytics-Anomalieerkennung

In den folgenden Schritten wird erläutert, wie Sie ein IoT Edge-Bereitstellungsmanifest mit diesen Modulen erstellen. An späterer Stelle des Tutorials importieren Sie dieses Manifest als Paket in den Solution Accelerator für die Remoteüberwachung.

### <a name="create-the-azure-stream-analytics-job"></a>Erstellen des Azure Stream Analytics-Auftrags

Sie definieren den Stream Analytics-Auftrag im Portal, bevor Sie ihn als IoT Edge-Modul verpacken.

1. Erstellen Sie im Azure-Portal ein Azure-Speicherkonto mit den Standardoptionen in der Ressourcengruppe **IoTEdgeDevices**. Notieren Sie sich den Namen, den Sie ausgewählt haben.

1. Erstellen Sie im Azure-Portal einen **Stream Analytics-Auftrag** in der Ressourcengruppe **IoTEdgeDevices**. Verwenden Sie die folgenden Konfigurationswerte:

    | Option | Wert |
    | ------ | ----- |
    | Auftragsname | EdgeDeviceJob |
    | Abonnement | Ihr Azure-Abonnement |
    | Ressourcengruppe | IoTEdgeDevices |
    | Location | USA (Ost) |
    | Hosting-Umgebung | Microsoft Edge |
    | Streamingeinheiten | 1 |

1. Öffnen Sie im Portal den Stream Analytics-Auftrag **EdgeDeviceJob**, klicken Sie auf „Eingaben“, und fügen Sie eine **Edge Hub**-Datenstromeingabe mit dem Namen **telemetry** hinzu.

1. Klicken Sie im Portal im Stream Analytics-Auftrag **EdgeDeviceJob** auf **Ausgaben**, und fügen Sie eine **Edge Hub**-Ausgabe mit dem Namen **output** hinzu.

1. Klicken Sie im Portal im Stream Analytics-Auftrag **EdgeDeviceJob** auf **Ausgaben**, und fügen Sie eine zweite **Edge Hub**-Ausgabe mit dem Namen **alert** hinzu.

1. Klicken Sie im Portal im Stream Analytics-Auftrag **EdgeDeviceJob** auf **Abfrage**, und fügen Sie die folgende **select**-Anweisung hinzu:

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. Klicken Sie im Portal im Stream Analytics-Auftrag **EdgeDeviceJob** auf **Speicherkontoeinstellungen**. Fügen Sie das Speicherkonto hinzu, das Sie der Ressourcengruppe **IoTEdgeDevices** am Anfang dieses Abschnitts hinzugefügt haben. Erstellen Sie einen neuen Container mit dem Namen **edgeconfig**.

Der folgende Screenshot zeigt den gespeicherten Stream Analytics-Auftrag:

[![Stream Analytics-Auftrag](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Damit haben Sie einen Stream Analytics-Auftrag zur Ausführung auf Ihrem IoT Edge-Gerät definiert. Der Auftrag berechnet die durchschnittliche Temperatur in einem Zeitfenster von fünf Sekunden. Außerdem sendet der Auftrag eine Warnung, wenn die durchschnittliche Temperatur in einem Zeitfenster von drei Sekunden über 400 steigt.

### <a name="create-the-iot-edge-deployment"></a>Erstellen der IoT Edge-Bereitstellung

Als Nächstes erstellen Sie ein IoT Edge-Bereitstellungsmanifest, das die Module definiert, die auf Ihrem IoT Edge-Gerät ausgeführt werden sollen. Im nächsten Abschnitt importieren Sie dieses Manifest als Paket in die Remoteüberwachungslösung.

1. Navigieren Sie im Azure-Portal zum IoT-Hub in Ihrer Remoteüberwachungslösung. Den IoT-Hub finden Sie in der Ressourcengruppe, die den gleichen Namen hat wie Ihre Remoteüberwachungslösung.

1. Klicken Sie im IoT-Hub im Abschnitt **Automatische Geräteverwaltung** auf **IoT Edge**. Klicken Sie auf **Add an IoT Edge deployment** (IoT Edge-Bereitstellung hinzufügen).

1. Geben Sie auf der Seite **Bereitstellung erstellen > Name und Bezeichnung** den Namen **oil-pump-device** ein. Klicken Sie auf **Weiter**.

1. Klicken Sie auf der Seite **Bereitstellung erstellen > Module hinzufügen** auf **+ Hinzufügen**. Wählen Sie **IoT Edge-Modul** aus.

1. Geben Sie im Bereich  **Benutzerdefinierte IoT Edge-Module** den Namen **temperatureSensor** und den Image-URI **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** ein. Klicken Sie auf **Speichern**.

1. Klicken Sie auf der Seite **Bereitstellung erstellen > Module hinzufügen** auf **+ Hinzufügen**, um ein zweites Modul hinzuzufügen. Wählen Sie **Azure Stream Analytics-Modul** aus.

1. Wählen Sie im Bereich **Edgebereitstellung** Ihr Abonnement und den im vorherigen Abschnitt erstellten Auftrag **EdgeDeviceJob** aus. Klicken Sie auf **Speichern**.

1. Klicken Sie auf der Seite **Bereitstellung erstellen > Module hinzufügen** auf **Weiter**.

1. Fügen Sie auf der Seite **Bereitstellung erstellen > Routen angeben** den folgenden Code hinzu:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Dieser Code leitet die Ausgabe des Stream Analytics-Moduls an die richtigen Speicherorte weiter.

    Klicken Sie auf **Weiter**.

1. Klicken Sie auf der Seite **Bereitstellung erstellen > Metriken angeben** auf **Weiter**.

1. Geben Sie auf der Seite **Bereitstellung erstellen > Zielgeräte** 10 als Priorität ein. Klicken Sie auf **Weiter**.

1. Klicken Sie auf der Seite **Bereitstellung erstellen > Bereitstellung überprüfen** auf **Senden**:

    [![Bereitstellung überprüfen](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. Klicken Sie auf der **IoT Edge**-Hauptseite auf **IoT Edge-Bereitstellungen**. **oil-pump-device** wird in der Liste der Bereitstellungen angezeigt.

1. Klicken Sie auf die Bereitstellung **oil-pump-device** und anschließend auf **IoT Edge-Manifest herunterladen**. Speichern Sie die Datei unter dem Namen **oil-pump-device.json** an einem geeigneten Speicherort auf Ihrem lokalen Computer. Diese Datei benötigen Sie im nächsten Abschnitt des Tutorials.

Damit haben Sie ein IoT Edge-Manifest erstellt, das als Paket in die Remoteüberwachungslösung importiert werden kann. In der Regel erstellt ein Entwickler die IoT Edge-Module und die IoT Edge-Manifestdatei.

## <a name="import-a-package"></a>Importieren eines Pakets

In diesem Abschnitt importieren Sie das IoT Edge-Manifest als Paket in die Remoteüberwachungslösung.

1. Navigieren Sie in der Webbenutzeroberfläche für die Remoteüberwachung zur Seite **Pakete**, und klicken Sie auf **+ Neues Paket**:

    [![Neues Paket](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. Wählen Sie im Bereich **Neues Paket** die Option **Edge Manifest** (IoT Edge-Manifest) als Pakettyp aus, klicken Sie auf **Durchsuchen**, um nach der Datei **oil-pump-device.json** auf dem lokalen Computer zu suchen, und klicken Sie auf **Hochladen**:

    [![Hochladen des Pakets](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    Die Liste der Pakete enthält jetzt das Paket **oil-pump-device.json**.

Im nächsten Abschnitt erstellen Sie eine Bereitstellung, die das Paket auf Ihr IoT Edge-Gerät anwendet.

## <a name="deploy-a-package"></a>Bereitstellen eines Pakets

Nun können Sie das Paket auf Ihrem Gerät bereitstellen.

1. Navigieren Sie in der Webbenutzeroberfläche für die Remoteüberwachung zur Seite **Bereitstellungen**, und klicken Sie auf **+ Neue Bereitstellung**:

    [![Neue Bereitstellung](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. Erstellen Sie im Bereich **Neue Bereitstellung** eine Bereitstellung mit den folgenden Einstellungen:

    | Option | Wert |
    | ------ | ----- |
    | NAME   | OilPumpDevices |
    | Pakettyp | Edge Manifest (IoT Edge-Manifest) |
    | Paket | oil-pump-device.json |
    | Device Group (Gerätegruppe) | OilPumps |
    | Priorität | 10 |

    [![Erstellen einer Bereitstellung](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Klicken Sie auf **Anwenden**.

Sie müssen einige Minuten warten, bis das Paket auf Ihrem Gerät bereitgestellt wurde und Telemetriedaten vom Gerät gesendet werden.

[![Bereitstellung aktiv](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

Auf der Seite **Bereitstellungen** werden die folgenden Metriken angezeigt:

* **Ziel** zeigt die Anzahl von Geräten in der Gerätegruppe.
* **Angewendet** zeigt die Anzahl von Geräten, auf die der Bereitstellungsinhalt angewendet wurde.
* **Erfolgreich** zeigt die Anzahl von IoT Edge-Geräten in der Bereitstellung, für die von der IoT Edge-Clientruntime eine Erfolgsmeldung ausgegeben wurde.
* **Fehler** zeigt die Anzahl von IoT Edge-Geräten in der Bereitstellung, für die von der IoT Edge-Clientruntime eine Fehlermeldung ausgegeben wurde.

## <a name="monitor-the-device"></a>Überwachen des Geräts

Sie können die Temperaturtelemetriedaten von Ihrer Ölpumpe in der Webbenutzeroberfläche für die Remoteüberwachung anzeigen:

1. Navigieren Sie zur Seite **Device Explorer**, und wählen Sie die Ölpumpe aus.
1. Klicken Sie im Abschnitt **Telemetrie** des Bereichs **Gerätedetails** auf **Temperatur**:

    [![Anzeigen von Telemetriedaten](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Sie können sehen, wie die Temperatur steigt, bis sie einen Schwellenwert erreicht. Das Stream Analytics-IoT Edge-Modul erkennt das Erreichen dieses Temperaturschwellenwerts und sendet in diesem Fall einen Befehl an das Gerät, um die Temperatur sofort zu senken. Diese gesamte Verarbeitung erfolgt auf dem Gerät ohne Kommunikation mit der Cloud.

Wenn Sie Bediener benachrichtigen möchten, wenn der Schwellenwert erreicht wurde, können Sie in der Webbenutzeroberfläche für die Remoteüberwachung eine Regel erstellen:

1. Navigieren Sie zur Seite **Regeln**, und klicken Sie auf **+ Neue Regel**.
1. Erstellen Sie eine neue Regel mit den folgenden Einstellungen:

    | Option | Wert |
    | ------ | ----- |
    | Regelname | Ölpumpentemperatur |
    | BESCHREIBUNG | Ölpumpentemperatur über 300 |
    | Gerätegruppe | OilPumps |
    | Berechnung | Sofort |
    | Feld | Temperatur |
    | Operator | > |
    | Wert | 300 |
    | Schweregrad | Information |

    [![Erstellen einer Regel](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Klicken Sie auf **Anwenden**.

1. Navigieren Sie zur Seite **Dashboard**. Im Bereich **Warnungen** wird eine Warnung angezeigt, wenn die Temperatur des Geräts **oil-pump** über 300 steigt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde erläutert, wie Sie ein IoT Edge-Gerät im Solution Accelerator für die Remoteüberwachung hinzufügen und konfigurieren. Weitere Informationen zum Arbeiten mit IoT Edge-Paketen in der Remoteüberwachungslösung finden Sie in der folgenden Anleitung:

> [!div class="nextstepaction"]
> [Import an IoT Edge package into your Remote Monitoring solution accelerator](iot-accelerators-remote-monitoring-import-edge-package.md) (Importieren eines IoT Edge-Pakets in den Solution Accelerator für die Remoteüberwachung)

Weitere Informationen zum Installieren der IoT Edge-Runtime finden Sie unter [Installieren der Azure IoT Edge-Runtime unter Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md).

Weitere Informationen zu Azure Stream Analytics auf IoT Edge-Geräten finden Sie unter [Tutorial: Bereitstellen von Azure Stream Analytics als IoT Edge-Modul](../iot-edge/tutorial-deploy-stream-analytics.md).
