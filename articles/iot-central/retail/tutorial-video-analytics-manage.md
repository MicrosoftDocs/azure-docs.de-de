---
title: 'Tutorial: Überwachen von Videodaten mit der Anwendungsvorlage „Videoanalyse: Objekt- und Bewegungserkennung“ von Azure IoT Central'
description: 'In diesem Tutorial wird veranschaulicht, wie Sie die Dashboards in der Anwendungsvorlage „Videoanalyse: Objekt- und Bewegungserkennung“ verwenden, um Ihre Kameras zu verwalten und die Videodaten zu überwachen.'
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: fbfef094cd062e437f2a28369162de96631ef41b
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832605"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>Tutorial: Überwachen und Verwalten einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> * Fügen Sie Ihrer IoT Central-Anwendung Kameras für die Objekt- und Bewegungserkennung hinzu.
> * Verwalten Sie Ihre Videodatenströme, und geben Sie sie wieder, wenn interessante Ereignisse erkannt werden.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, sollten Sie Folgendes durcharbeiten:

* Eines der folgenden vorgeschalteten Tutorials: [Tutorial: Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (YOLO v3)](./tutorial-video-analytics-create-app-yolo-v3.md) oder [Tutorial: Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md)
* Eines der vorherigen Tutorials [Erstellen einer IoT Edge-Instanz für die Livevideoanalyse (Linux-VM)](tutorial-video-analytics-iot-edge-vm.md) oder [Erstellen einer IoT Edge-Instanz für die Livevideoanalyse (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md).

Sie sollten [Docker](https://www.docker.com/products/docker-desktop) auf Ihrem lokalen Computer installiert haben, um die Anwendung für die Videoanzeige ausführen zu können.

## <a name="add-an-object-detection-camera"></a>Hinzufügen einer Objekterkennungskamera

Navigieren Sie in Ihrer IoT Central-Anwendung zum Gerät **LVA-Gateway 001**, das Sie zuvor erstellt haben. Klicken Sie anschließend auf die Registerkarte **Befehle**.

Verwenden Sie die Werte in der folgenden Tabelle als Parameterwerte für den Befehl **Add Camera Request** (Kameraanforderung hinzufügen). Für die in der Tabelle enthaltenen Werte wird angenommen, dass Sie die simulierte Kamera auf der Azure-VM nutzen. Passen Sie die Werte entsprechend an, falls Sie eine echte Kamera verwenden:

| Feld| BESCHREIBUNG| Beispielwert|
|---------|---------|---------|
| Camera ID (Kamera-ID)      | Geräte-ID für die Bereitstellung | camera-003 |
| Camera Name (Kameraname)    | Anzeigename           | Object detection camera (Objekterkennungskamera) |
| RTSP Url (RTSP-URL)       | Adresse des Datenstroms   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| RTSP Username (RTSP-Benutzername)  |                         | user    |
| RTSP Password (RTSP-Kennwort)  |                         | password    |
| Erkennungstyp | Dropdown                | Objekterkennung       |

Wählen Sie **Ausführen** aus, um das Kameragerät hinzuzufügen:

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="Kamera hinzufügen":::

> [!NOTE]
> Die Gerätevorlage **LVA Edge Object Detector** (LVA-Edgeobjekterkennung) ist in der Anwendung bereits vorhanden.

## <a name="add-a-motion-detection-camera-optional"></a>Hinzufügen einer Kamera für die Bewegungserkennung (optional)

Wenn Sie zwei Kameras an Ihr IoT Edge-Gatewaygerät angeschlossen haben, sollten Sie die vorherigen Schritte wiederholen, um die Kamera für die Bewegungserkennung der Anwendung hinzuzufügen. Verwenden Sie unterschiedliche Werte für die Parameter **Camera ID** (Kamera-ID), **Camera Name** (Kameraname) und **RTSP Url** (RTSP-URL).

## <a name="view-the-downstream-devices"></a>Anzeigen der nachgeschalteten Geräte

Wählen Sie die Registerkarte **Nachgeschaltete Geräte** für das Gerät **LVA-Gateway 001** aus, um die eben hinzugefügten Kamerageräte anzuzeigen:

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="Überprüfen":::

Die Kamerageräte werden auch in der Liste auf der Seite **Geräte** der Anwendung angezeigt.

## <a name="configure-and-manage-the-camera"></a>Konfigurieren und Verwalten der Kamera

Navigieren Sie zum Gerät **camera-003**, und wählen Sie die Registerkarte **Verwalten** aus.

Verwenden Sie die Standardwerte, oder ändern Sie sie, falls Sie die Geräteeigenschaften anpassen müssen:

**Kameraeinstellungen**

| Eigenschaft | BESCHREIBUNG | Empfohlener Wert |
|-|-|-|
| Video Playback Host (Host für Videowiedergabe) | Host für den Azure Media Player-Viewer | http://localhost:8094 |

**LVA-Vorgänge und -Diagnose**

| Eigenschaft | BESCHREIBUNG | Empfohlener Wert |
|-|-|-|
| Automatischer Start | Start der Objekterkennung bei einem Neustart des LVA-Gateways | Überprüft |
| Debug Telemetry (Debugtelemetrie) | Ereignisverfolgungen | Optional |
|Inference Timeout (sec) (Rückschlusszeitlimit (Sek.))| Zeitraum für die Ermittlung, ob Rückschlussvorgänge beendet wurden | 20 |

**KI-Objekterkennung**

| Eigenschaft | BESCHREIBUNG | Empfohlener Wert |
|-|-|-|
| Max Inference Capture Time (sec) (Max. Rückschlusserfassungsdauer (Sek.)) | Maximale Erfassungsdauer | 15 |
| Detection Classes (Erkennungsklassen) | Zeichenfolgen (durch Kommas getrennt) mit den Erkennungstags. Weitere Informationen finden Sie in der [Liste mit den unterstützten Tags](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt). | truck, bus (LKW, Bus) |
| Confidence Threshold (Zuverlässigkeitsschwellenwert) | Qualifikationsprozentsatz für die Ermittlung, ob die Objekterkennung gültig ist | 70 |
| Inference Frame Sample Rate (fps) (Bildabtastrate für Rückschlüsse (Bilder/Sek.)) | [Beschreibung hier einfügen] | 2 |

Wählen Sie **Speichern** aus.

Nach einigen Sekunden wird die Bestätigungsmeldung **Accepted** (Akzeptiert) für jede Einstellung angezeigt:

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="Objekterkennung":::

## <a name="start-lva-processing"></a>Starten der LVA-Verarbeitung

Navigieren Sie zum Gerät **camera-003**, und wählen Sie die Registerkarte **Befehle** aus.

Führen Sie den Befehl **Start LVA Processing** (LVA-Verarbeitung starten) aus.

Zeigen Sie nach Abschluss des Befehls den Befehlsverlauf an, um sicherzustellen, dass keine Fehler vorhanden sind:

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="Befehl „Start LVA processing“ (LVA-Verarbeitung starten)":::

## <a name="monitor-the-cameras"></a>Überwachen der Kameras

Navigieren Sie zum Gerät **camera-003**, und wählen Sie die Registerkarte **Dashboard** aus:

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="Dashboard für Kamera":::

Auf der Kachel **Detection Count** (Erkennungsanzahl) wird die durchschnittliche Erkennungsanzahl jeweils für das ausgewählte Erkennungsklassenobjekt für ein Erkennungsintervall von einer Sekunde angezeigt.

Im Kreisdiagramm **Detection Classes** (Erkennungsklassen) wird der Prozentsatz der Erkennungen für die einzelnen Klassentypen angezeigt.

Das **Inference Event Video** (Rückschlussereignis-Video) ist eine Liste mit den Medienobjekten in Azure Media Services, die die Erkennungen enthalten. Für den Link wird der Host-Player verwendet, der im folgenden Abschnitt beschrieben ist.

## <a name="start-the-streaming-endpoint"></a>Starten des Streamingendpunkts

Starten Sie den Media Services-Endpunkt, um für Ihre Media Player-Clientanwendung das Streamen des aufgezeichneten Videos zu ermöglichen:

* Navigieren Sie im Azure-Portal zur Ressourcengruppe **lva-rg**.
* Öffnen Sie die Ressource **Streamingendpunkt**.
* Wählen Sie auf der Seite **Streamingendpunktdetails** die Option **Starten** aus. Es wird eine Warnung angezeigt, dass die Abrechnung beginnt, sobald der Endpunkt gestartet wird.

## <a name="view-stored-video"></a>Anzeigen von gespeicherten Videos

Die Zeiten, in denen Kameras überwacht werden mussten, um auf verdächtige Bilder reagieren zu können, sind vorbei. Dank automatischer Ereigniskennzeichnung und direkter Links zum gespeicherten Video, für das der Erkennungsrückschluss erfolgt ist, können Sicherheitsmitarbeiter auf die gewünschten Ereignisse in einer Liste zugreifen und dann den Link verwenden, um das Video anzuzeigen.

Sie können den [AMP-Videoplayer](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer) verwenden, um das Video anzuzeigen, das unter Ihrem Azure Media Services-Konto gespeichert ist.

Die IoT Central-Anwendung speichert das Video in Azure Media Services an einem Ort, von dem aus Sie es streamen können. Sie benötigen einen Videoplayer, um das in Azure Media Services gespeicherte Video wiedergeben zu können.

Vergewissern Sie sich, dass **Docker** auf dem lokalen Computer ausgeführt wird.

Öffnen Sie eine Eingabeaufforderung, und verwenden Sie den folgenden Befehl, um den Videoplayer in einem Docker-Container auf Ihrem lokalen Computer auszuführen. Ersetzen Sie die Platzhalter im Befehl durch die Werte, die Sie sich zuvor in der Datei *scratchpad.txt* notiert haben. Sie haben sich die Werte `amsAadClientId`, `amsAadSecret`, `amsAadTenantId`, `amsSubscriptionId` und `amsAccountName` notiert, als Sie den Dienstprinzipal für Ihr Media Services-Konto erstellt haben:

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Docker-Parameter | AMS-API-Zugriff (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

Navigieren Sie zum Gerät **camera-003**, und wählen Sie die Registerkarte **Dashboard** aus. Klicken Sie anschließend auf der Kachel **Inference Event Video** (Rückschlussereignis-Video) auf einen der Hyperlinks der erfassten Ereignisse für die Bewegungserkennung. Das Video wird auf einer Seite des lokalen Videoplayers angezeigt:

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="Videocodeausschnitt":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>Ändern der simulierten Geräte auf Anwendungsdashboards

Die Anwendungsdashboards werden ursprünglich mit Telemetriedaten und Eigenschaften gefüllt, die über die simulierten IoT Central-Geräte generiert werden. Führen Sie diese Schritte aus, um die Kacheln für die Telemetriedaten echter Kameras oder des Live555-Simulators zu konfigurieren:

1. Navigieren Sie zum Anwendungsdashboard **(Sample) Real Camera Monitor** ((Beispiel) Überwachung für echte Kamera).
1. Wählen Sie **Bearbeiten** aus.
1. Wählen Sie die Kachel **Hinweis** aus, und löschen Sie sie.
1. Ändern Sie den Titel des Dashboards in *Real Camera Monitor* (Überwachung für echte Kamera).
1. Wählen Sie auf der Kachel **Inference Count** (Rückschlussanzahl) das Symbol „Konfigurieren“ aus.
1. Wählen Sie im Abschnitt **Diagramm konfigurieren** in der Gerätegruppe **LVA Edge Object Detector** (LVA-Edgeobjekterkennung) mindestens eine echte Kamera aus.
1. Wählen Sie das Telemetriedatenfeld `AI Inference Interface/Inference Count` aus.
1. Wählen Sie **Update** aus.

1. Wiederholen Sie die Schritte für die folgenden Kacheln:
    1. Im Kreisdiagramm **Erkennung** wird der Telemetrietyp `AI Inference Interface/Inference/entity/tag/value` verwendet.
    1. Unter **Rückschluss** wird `AI Inference Interface/Inference/entity/tag/value` als letzter bekannter Wert verwendet.
    1. Unter **Confidence %** (Zuverlässigkeit in %) wird `AI Inference Interface/Inference/entity/tag/confidence` als letzter bekannter Wert verwendet.
    1. Unter **Momentaufnahme** wird `AI Inference Interface/Inference Image` als Bild angezeigt.
    1. Für **Inference Event Video** (Rückschlussereignis-Video) wird `AI Inference Interface/Inference Event Video` als Link angezeigt.
1. Wählen Sie **Speichern** aus.

Im Dashboard **Real Camera Monitor** (Überwachung für echte Kamera) werden jetzt Werte Ihres echten Kamerageräts angezeigt:

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="Anwendungsdashboard für echte Kameras":::

## <a name="pause-processing"></a>Anhalten der Verarbeitung

Sie können die Verarbeitung der Livevideoanalyse in der Anwendung anhalten:

1. Navigieren Sie in Ihrer Anwendung zur Seite **Befehle** für die Objekterkennungskamera. Führen Sie anschließend den Befehl **Stop LVA Processing** (LVA-Verarbeitung beenden) aus.

1. Beenden Sie den Streamingendpunkt für Ihr Media Services-Konto:
    * Navigieren Sie im Azure-Portal zur Ressourcengruppe **lva-rg**.
    * Klicken Sie auf die Ressource **Streamingendpunkt**.
    * Wählen Sie auf der Seite **Streamingendpunktdetails** die Option **Beenden** aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Anwendung nicht mehr benötigen, können Sie alle erstellten Ressourcen wie folgt entfernen:

1. Navigieren Sie in der IoT Central-Anwendung zur Seite **Ihre Anwendung** im Abschnitt **Verwaltung**. Wählen Sie anschließend die Option **Löschen**.
1. Löschen Sie im Azure-Portal die Ressourcengruppe **lva-rg**.
1. Beenden Sie auf Ihrem lokalen Computer den Docker-Container **amp-viewer**.

## <a name="next-steps"></a>Nächste Schritte

Sie haben jetzt gelernt, wie Sie der IoT Central-Anwendung Kameras hinzufügen und diese für die Objekt- und Bewegungserkennung konfigurieren.

Passen Sie den Quellcode für die IoT Edge-Module wie folgt an:

> [!div class="nextstepaction"]
> [Ändern und Erstellen der Gatewaymodule für die Livevideoanalyse](./tutorial-video-analytics-build-module.md)
