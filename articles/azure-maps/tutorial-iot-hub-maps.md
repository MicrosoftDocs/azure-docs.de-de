---
title: 'Tutorial: Implementieren der räumlichen IoT-Analyse | Microsoft Azure Maps'
description: Tutorial zum Integrieren von IoT Hub in Microsoft Azure Maps-Dienst-APIs
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 6109164d8827a343a550a114acc42db2461f3a2c
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905348"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Tutorial: Implementieren der räumlichen IoT-Analyse mit Azure Maps

Ein gängiges IoT-Szenario ist die Erfassung und Nachverfolgung relevanter Ereignisse in Raum und Zeit. Beispiele hierfür sind die Verwaltung von Fuhrparks, Assetnachverfolgung, Mobilität und Smart City-Anwendungen. In diesem Tutorial wird Schritt für Schritt eine Lösung beschrieben, bei der mit den Azure Maps-APIs die Bewegung von genutzten Mietwagen nachverfolgt wird.

In diesem Lernprogramm führen Sie folgende Schritte aus:

> [!div class="checklist"]
> * Erstellen eines Azure Storage-Kontos zur Protokollierung der Daten für die Fahrzeugnachverfolgung
> * Hochladen eines Geofence per Datenupload-API in den Azure Maps-Datendienst (Vorschau)
> * Erstellen eines Hubs in Azure IoT Hub und Registrieren eines Geräts
> * Erstellen einer Funktion in Azure Functions und Implementieren von Geschäftslogik basierend auf einer räumlichen Azure Maps-Analyse
> * Abonnieren von IoT-Gerätetelemetrieereignissen aus der Azure-Funktion per Azure Event Grid
> * Filtern der Telemetrieereignisse per IoT Hub-Nachrichtenrouting

## <a name="prerequisites"></a>Voraussetzungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. [Erstellen Sie ein Azure Maps-Konto](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt) Weitere Informationen finden Sie unter [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

4. [Erstellen Sie eine Ressourcengruppe](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). In diesem Tutorial nennen wir unsere Ressourcengruppe *ContosoRental*, aber Sie können einen beliebigen Namen auswählen.

5. Laden Sie das [C#-Projekt „rentalCarSimulation“](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) herunter.

In diesem Tutorial wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können aber auch eine andere API-Entwicklungsumgebung nutzen.

## <a name="use-case-rental-car-tracking"></a>Anwendungsfall: Nachverfolgung von Mietwagen

Angenommen, ein Mietwagenunternehmen möchte Standortinformationen, zurückgelegte Entfernung und Betriebsstatus seiner Mietwagen protokollieren. Außerdem möchte das Unternehmen diese Informationen immer dann speichern, wenn ein Fahrzeug die autorisierte geografische Region verlässt.

Die Mietwagen sind mit IoT-Geräten ausgestattet, die regelmäßig Telemetriedaten an IoT Hub senden. Die Telemetriedaten beinhalten den aktuellen Standort sowie die Information, ob der Motor des Fahrzeugs läuft. Das Schema für den Gerätestandort basiert auf dem [IoT Plug & Play-Schema für Geodaten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Das Gerätetelemetrieschema des Mietwagens sieht wie der folgende JSON-Code aus:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

In diesem Tutorial verfolgen Sie nur ein Fahrzeug nach. Nachdem Sie die Azure-Dienste eingerichtet haben, müssen Sie das [C#-Projekt „rentalCarSimulation“](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) herunterladen, um den Fahrzeugsimulator auszuführen. Der gesamte Prozess vom Ereignis bis zur Funktionsausführung wird in den folgenden Schritten zusammengefasst:

1. Das Gerät im Fahrzeug sendet Telemetriedaten an IoT Hub.

2. Wenn der Fahrzeugmotor läuft, veröffentlicht der Hub die Telemetriedaten in Event Grid.

3. Eine Azure-Funktion wird aufgrund ihres Ereignisabonnements für Gerätetelemetrieereignisse ausgelöst.

4. Die Funktion protokolliert die Positionskoordinaten des Fahrzeuggeräts, den Zeitpunkt des Ereignisses und die Geräte-ID. Anschließend wird anhand der [Spatial Geofence-Get-API](/rest/api/maps/spatial/getgeofence) bestimmt, ob das Fahrzeug außerhalb des Geofence gefahren wurde. Wenn es die Geofencegrenzen überschritten hat, speichert die Funktion die vom Ereignis empfangenen Positionsdaten in einem Blobcontainer. Außerdem führt die Funktion eine Abfrage von [Get Search Address Reverse](/rest/api/maps/search/getsearchaddressreverse) (inverse Adresssuche) durch, um die Koordinatenposition in eine Straße zu übersetzen, und speichert diese Informationen zusammen mit den restlichen Gerätestandortdaten.

Das folgende Diagramm enthält eine allgemeine Übersicht über das System.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Diagramm: Systemübersicht":::

In der folgenden Abbildung ist der Geofencebereich blau eingefärbt. Die Route des Mietwagens ist als grüne Linie dargestellt.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Abbildung: Geofenceroute":::

## <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

Zum Speichern von Nachverfolgungsdaten zu Verstößen des Fahrzeugs erstellen Sie ein [universelles v2-Speicherkonto](../storage/common/storage-account-overview.md#general-purpose-v2-accounts) in Ihrer Ressourcengruppe. Wenn Sie noch keine Ressourcengruppe erstellt haben, befolgen Sie die Anweisungen in [Erstellen von Ressourcengruppe](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). In diesem Tutorial nennen Sie Ihre Ressourcengruppe *ContosoRental*.

Befolgen Sie zum Erstellen eines Speicherkontos die Anweisungen unter [Speicherkonto erstellen](../storage/common/storage-account-create.md?tabs=azure-portal). In diesem Tutorial nennen Sie das Speicherkonto *contosorentalstorage*, aber sonst können Sie einen beliebigen Namen verwenden.

Nachdem Sie die Erstellung Ihres Speicherkontos erfolgreich abgeschlossen haben, müssen Sie einen Container zum Speichern von Protokollierungsdaten erstellen.

1. Navigieren Sie zu Ihrem neu erstellten Speicherkonto. Wählen Sie im Abschnitt **Essentials** den Link **Container** aus.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Screenshot: Container für Blobspeicher":::

2. Wählen Sie oben links die Option **+ Container** aus. Rechts im Browser wird ein Bereich angezeigt. Geben Sie Ihrem Container den Namen *contoso-rental-logs*, und wählen Sie die Option **Erstellen** aus.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Screenshot: Erstellen eines Blobcontainers":::

3. Navigieren Sie in Ihrem Speicherkonto zum Bereich **Zugriffsschlüssel**, und kopieren Sie den **Namen des Speicherkontos** und den Wert des **Schlüssels** in den Abschnitt **key1**. Sie benötigen diese beiden Werte im Abschnitt „Erstellen einer Azure-Funktion und Hinzufügen eines Event Grid-Abonnements“.

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Screenshot: Kopieren des Speicherkontonamens und -schlüssels":::

## <a name="upload-a-geofence"></a>Hochladen eines Geofence

Verwenden Sie als Nächstes die [Postman-App](https://www.getpostman.com), um den [Geofence für Azure Maps hochzuladen](./geofence-geojson.md). Der Geofence definiert den autorisierten geografischen Bereich für unser Mietfahrzeug. Sie verwenden den Geofence in Ihrer Azure-Funktion, um zu bestimmen, ob ein Fahrzeug außerhalb des Geofencebereichs bewegt wurde.

Führen Sie die folgenden Schritte aus, um den Geofence mit der Datenupload-API von Azure Maps hochzuladen: 

1. Öffnen Sie die Postman-App, und wählen Sie **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Collection** (Sammlung) aus. Geben Sie der Sammlung einen Namen, und wählen Sie **Create** (Erstellen) aus.

2. Klicken Sie erneut auf **New** (Neu), um die Anforderung zu erstellen. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus, und geben Sie einen Namen für die Anforderung ein. Wählen Sie die im vorherigen Schritt erstellte Sammlung und anschließend **Save** (Speichern) aus.

3. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **POST** aus, und geben Sie die folgende URL ein, um die Geofencedaten in die Datenupload-API hochzuladen. Stellen Sie sicher, dass Sie `{subscription-key}` durch Ihren primären Abonnementschlüssel ersetzen.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Im URL-Pfad steht der Wert von `geojson` des Parameters `dataFormat` für das Format der hochzuladenden Daten.

4. Wählen Sie **Body** > **raw** („Hauptteil“ > „Rohdaten“) als Eingabeformat und dann in der Dropdownliste die Option **JSON** aus. [Öffnen Sie die JSON-Datendatei](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4), und kopieren Sie den JSON-Code in den Textabschnitt. Klicken Sie auf **Senden**.

5. Wählen Sie die Option **Send** (Senden) aus, und warten Sie auf die Verarbeitung der Anforderung. Navigieren Sie nach Abschluss der Anforderung zur Registerkarte **Headers** (Header) der Antwort. Kopieren Sie den Wert des Schlüssels **Location** (Speicherort). Hierbei handelt es sich um die Status-URL (`status URL`).

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Erstellen Sie zum Überprüfen des Status des API-Aufrufs eine **GET**-HTTP-Anforderung für `status URL`. An die URL muss zur Authentifizierung der primäre Abonnementschlüssel angefügt werden. Die **GET**-Anforderung sollte wie die folgende URL aussehen:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it returns a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. Copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

IoT Hub ermöglicht die sichere und zuverlässige bidirektionale Kommunikation zwischen einer IoT-Anwendung und den verwalteten Geräten. In diesem Tutorial möchten Sie Informationen von Ihrem Gerät im Fahrzeug erhalten, um die Position des Mietfahrzeugs zu ermitteln. In diesem Abschnitt erstellen Sie einen IoT-Hub in der Ressourcengruppe *ContosoRental*. Dieser Hub ist für das Veröffentlichen Ihrer Gerätetelemetrieereignisse zuständig.

> [!NOTE]
> Die Option zum Veröffentlichen von Gerätetelemetrieereignissen in Event Grid befindet sich derzeit in der Vorschauphase. Dieses Feature ist in allen Regionen verfügbar, mit Ausnahme von: „USA, Osten“, „USA, Westen“, „Europa, Westen“, „Azure Government“, „Azure China 21Vianet“ und „Azure Deutschland“.

Führen Sie zum Erstellen eines IoT-Hubs in der Ressourcengruppe *ContosoRental* die Schritte unter [Erstellen eines IoT-Hubs](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub) aus.

## <a name="register-a-device-in-your-iot-hub"></a>Registrieren eines Geräts bei Ihrem IoT-Hub

Geräte können nur dann eine Verbindung mit dem IoT-Hub herstellen, wenn sie in der IoT-Hub-Identitätsregistrierung registriert sind. Hier erstellen Sie ein einzelnes Gerät mit dem Namen *InVehicleDevice*. Um das Gerät in Ihrem IoT-Hub zu erstellen und zu registrieren, führen Sie die Schritte unter [Registrieren eines neuen Geräts beim IoT-Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub) aus. Achten Sie darauf, dass Sie die primäre Verbindungszeichenfolge Ihres Geräts kopieren. Sie benötigen die Information später.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Erstellen einer Funktion und Hinzufügen eines Event Grid-Abonnements

Azure Functions ist ein serverloser Computedienst, mit dem Sie kleine Codeabschnitte („Funktionen“) ausführen können, ohne explizit eine Computeinfrastruktur bereitstellen oder verwalten zu müssen. Weitere Informationen finden Sie unter [Einführung in Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

Eine Funktion wird von einem bestimmten Ereignis ausgelöst. Hier erstellen Sie eine Funktion, die durch einen Event Grid-Trigger ausgelöst wird. Erstellen Sie die Beziehung zwischen Trigger und Funktion, indem Sie ein Ereignisabonnement für IoT-Hub-Gerätetelemetrieereignisse erstellen. Wenn ein Gerätetelemetrieereignis auftritt, wird Ihre Funktion als Endpunkt aufgerufen und empfängt die relevanten Daten für das Gerät, das Sie zuvor im IoT-Hub registriert haben.

Den C#-Skriptcode, der in Ihrer Funktion enthalten ist, finden Sie [hier](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Richten Sie jetzt Ihre Azure-Funktion ein.

1. Wählen Sie im Dashboard des Azure-Portals die Option **Ressource erstellen** aus. Geben Sie im Suchtextfeld **Funktionen-App** ein. Wählen Sie **Funktions-App** > **Erstellen** aus.

1. Geben Sie Ihrer Funktions-App auf der Erstellungsseite **Funktions-App** einen Namen. Wählen Sie unter **Ressourcengruppe** die Option **ContosoRental** in der Dropdownliste aus. Wählen Sie **.NET Core** als **Runtimestapel** aus. Wählen Sie am unteren Rand der Seite die Option **Nächster Schritt: Hosting >** aus.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Screenshot: Erstellen einer Funktions-App":::

1. Wählen Sie als **Speicherkonto** das Speicherkonto aus, das Sie unter [Erstellen eines Azure-Speicherkontos](#create-an-azure-storage-account) erstellt haben. Klicken Sie auf **Überprüfen + erstellen**.

1. Überprüfen Sie die Details der Funktions-App, und wählen Sie **Erstellen** aus.

1. Nach der Erstellung der App fügen Sie ihr eine Funktion hinzu. Navigieren Sie zur Funktions-App. Wählen Sie den Bereich **Funktionen** aus. Wählen Sie oben auf der Seite die Option **+ Hinzufügen** aus. Der Bereich mit den Funktionsvorlagen wird angezeigt. Scrollen Sie im Bereich nach unten, und wählen Sie **Azure Event Grid-Trigger** aus.

     >[!IMPORTANT]
    > Die Vorlagen **Azure Event Hub-Trigger** und **Azure Event Grid-Trigger** haben ähnliche Namen. Stellen Sie sicher, dass Sie die Vorlage **Azure Event Grid-Trigger** auswählen.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Screenshot: Erstellen einer Funktion":::

1. Geben Sie der Funktion einen Namen. In diesem Tutorial verwenden Sie den Namen *GetGeoFunction*, aber sonst können Sie einen beliebigen Namen nutzen. Wählen Sie **Funktion erstellen** aus.

1. Wählen Sie im Menü auf der linken Seite den Bereich **Programmieren und testen** aus. Kopieren Sie das [C#-Skript](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx), und fügen Sie es in das Codefenster ein.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kopie/Screenshot: Einfügen des Codes in das Funktionsfenster":::

1. Ersetzen Sie im C#-Code die folgenden Parameter:
    * Ersetzen Sie **SUBSCRIPTION_KEY** durch den primären Abonnementschlüssel Ihres Azure Maps-Kontos.
    * Ersetzen Sie **UDID** durch die `udid` des in [Hochladen eines Geofence](#upload-a-geofence) hochgeladenen Geofencebereichs.
    * Mit der Funktion `CreateBlobAsync` im Skript wird im Datenspeicherkonto ein Blob pro Ereignis erstellt. Ersetzen Sie **ACCESS_KEY**, **ACCOUNT_NAME** und **STORAGE_CONTAINER_NAME** durch den Zugriffsschlüssel, den Kontonamen und den Datenspeichercontainer Ihres Speicherkontos. Diese Werte wurden generiert, als Sie in [Erstellen eines Azure-Speicherkontos](#create-an-azure-storage-account) ein Speicherkonto erstellt haben.

1. Wählen Sie im linken Menü den Bereich **Integration** aus. Wählen Sie im Diagramm die Option **Event Grid-Trigger** aus. Geben Sie für den Trigger den Namen *eventGridEvent* ein, und wählen Sie die Option **Event Grid-Abonnement erstellen** aus.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Screenshot: Hinzufügen eines Ereignisabonnements":::

1. Fügen Sie die Abonnementdetails ein. Benennen Sie das Ereignisabonnement. Wählen Sie für **Ereignisschema** die Option **Event Grid-Schema** aus. Wählen Sie für **Thementypen** die Option **Azure IoT Hub-Konten** aus. Wählen Sie für **Ressourcengruppe** die Ressourcengruppe aus, die Sie zu Beginn dieses Tutorials erstellt haben. Wählen Sie unter **Ressource** den IoT-Hub aus, den Sie in „Erstellen einer Azure IoT Hub-Instanz“ erstellt haben. Wählen Sie für **Nach Ereignistypen filtern** die Option **Gerätetelemetrie** aus.

   Nachdem Sie diese Optionen ausgewählt haben, sehen Sie, dass der **Thementyp** automatisch in **IoT Hub** geändert wurde. Für **Name des Systemthemas** können Sie denselben Namen wie für Ihre Ressource verwenden. Wählen Sie abschließend im Abschnitt **Endpunktdetails** die Option **Endpunkt auswählen** aus. Akzeptieren Sie alle Einstellungen, und wählen Sie **Auswahl bestätigen** aus.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Screenshot: Erstellen eines Ereignisabonnements":::

1. Überprüfen Sie Ihre Einstellungen. Stellen Sie sicher, dass der Endpunkt die Funktion angibt, die Sie am Anfang dieses Abschnitts erstellt haben. Klicken Sie auf **Erstellen**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Screenshot: Bestätigung der Erstellung eines Ereignisabonnements":::

1. Nun befinden Sie sich wieder im Fenster **Trigger bearbeiten**. Wählen Sie **Speichern** aus.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Filtern von Ereignissen per IoT Hub-Nachrichtenrouting

Wenn Sie der Azure-Funktion ein Event Grid-Abonnement hinzufügen, wird automatisch eine Nachrichtenroute im angegebenen IoT-Hub erstellt. Das Nachrichtenrouting ermöglicht Ihnen, verschiedene Datentypen an verschiedene Endpunkte weiterzuleiten. Dadurch können Sie beispielsweise Gerätetelemetrienachrichten, Gerätelebenszyklusereignisse und Änderungsereignisse für Gerätezwillinge weiterleiten. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Screenshot: IoT Hub-Nachrichtenrouting":::

In Ihrem Beispielszenario möchten Sie nur dann Nachrichten empfangen, wenn das Mietfahrzeug bewegt wird. Erstellen Sie eine Routingabfrage, um die Ereignisse zu filtern, bei denen die `Engine`-Eigenschaft auf **ON** festgelegt ist. Wählen Sie zum Erstellen einer Routingabfrage die Route **RouteToEventGrid** aus, und ersetzen Sie die **Routingabfrage** durch **„Engine='ON'“** . Klicken Sie dann auf **Speichern**. Vom IoT-Hub werden jetzt nur dann Gerätetelemetriedaten veröffentlicht, wenn die Engine aktiviert ist.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Screenshot: Filtern von Routingnachrichten":::

>[!TIP]
>Es gibt verschiedene Möglichkeiten, IoT-Nachrichten vom Typ „Gerät-zu-Cloud“ abzufragen. Weitere Informationen zur Syntax des Nachrichtenroutings finden Sie unter [Abfragesyntax für das IoT Hub-Nachrichtenrouting](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Senden von Telemetriedaten an IoT Hub

Sobald Ihre Azure-Funktion ausgeführt wird, können Sie Telemetriedaten an die IoT Hub-Instanz senden, von wo aus sie dann an Event Grid weitergeleitet werden. Verwenden Sie eine C#-Anwendung, um Standortdaten für ein Gerät in einem Mietfahrzeug zu simulieren. Zum Ausführen der Anwendung benötigen Sie auf Ihrem Entwicklungscomputer .NET Core SDK 2.1.0 oder höher. Führen Sie die folgenden Schritte aus, um simulierte Telemetriedaten an den IoT-Hub zu senden:

1. Wenn Sie dies noch nicht getan haben, laden Sie das C#-Projekt [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) herunter.

2. Öffnen Sie die Datei `simulatedCar.cs` in einem Text-Editor Ihrer Wahl, und ersetzen Sie den Wert von `connectionString` durch den Wert, den Sie beim Registrieren des Geräts gespeichert haben. Speichern Sie die an der Datei vorgenommenen Änderungen.

3. Vergewissern Sie sich, dass auf Ihrem Computer .NET Core installiert ist. Navigieren Sie in Ihrem lokalen Terminalfenster zum Stammordner des C#-Projekts, und führen Sie den folgenden Befehl aus, um die erforderlichen Pakete für die simulierte Geräteanwendung zu installieren:

    ```cmd/sh
    dotnet restore
    ```

4. Führen Sie in demselben Terminal den folgenden Befehl aus, um die Anwendung für die Mietwagensimulation zu erstellen und auszuführen:

    ```cmd/sh
    dotnet run
    ```


  Ihr lokales Terminal sollte wie folgt aussehen.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Screenshot: Terminalausgabe":::

Wenn Sie den Blobspeichercontainer jetzt öffnen, können Sie vier Blobs für Standorte sehen, an denen sich das Fahrzeug außerhalb des Geofencebereichs befunden hat.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Screenshot: Anzeigen von Blobs im Container":::

Die folgende Karte enthält vier Fahrzeugpositionspunkte außerhalb des Geofence. Jede Position wurde in regelmäßigen Zeitabständen protokolliert.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Screenshot: Karte mit Verstößen":::

## <a name="explore-azure-maps-and-iot"></a>Erkunden von Azure Maps und IoT

Informationen zu den in diesem Tutorial verwendeten Azure Maps-APIs finden Sie unter:

* [Get Search Address Reverse (Suchadresse invers abrufen)](/rest/api/maps/search/getsearchaddressreverse)
* [Get Geofence](/rest/api/maps/spatial/getgeofence)

Eine vollständige Liste mit Azure Maps-REST-APIs finden Sie unter:

* [Azure Maps-REST-APIs](/rest/api/maps/spatial/getgeofence)

* [Dokumentation zu IoT Plug & Play (Vorschauversion)](../iot-pnp/index.yml)

Eine Liste mit Geräten, die über eine Azure-Zertifizierung für IoT verfügen, finden Sie unter:

* [Geräte mit Azure-Zertifizierung](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Senden von Telemetriedaten von einem Gerät an die Cloud (und umgekehrt) finden Sie unter:


> [!div class="nextstepaction"]
> [Senden von Telemetriedaten von einem Gerät](../iot-hub/quickstart-send-telemetry-dotnet.md)