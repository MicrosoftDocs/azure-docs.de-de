---
title: 'Tutorial: Erstellen von Gebäudeplänen mithilfe von Microsoft Azure Maps Creator (Vorschauversion)'
description: In diesem Tutorial erfahren Sie, wie Sie Gebäudepläne mithilfe von Microsoft Azure Maps Creator (Vorschauversion) erstellen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: eab8a2729209bb0023662b652f862b4fa678470e
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905722"
---
# <a name="tutorial-use-creator-preview-to-create-indoor-maps"></a>Tutorial: Erstellen von Gebäudeplänen mithilfe von Creator (Vorschau)

> [!IMPORTANT]
> Azure Maps Creator-Dienste befinden sich derzeit in der Public Preview-Phase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



In diesem Tutorial wird die Erstellung von Gebäudeplänen gezeigt. Sie erfahren in diesem Tutorial, wie Sie mithilfe der API folgende Aktionen ausführen:

> [!div class="checklist"]
> * Hochladen des Zeichnungspakets für Ihren Gebäudeplan
> * Konvertieren Ihres Zeichnungspakets in Kartendaten
> * Erstellen eines Datasets auf der Grundlage Ihrer Kartendaten
> * Erstellen eines Kachelsets auf der Grundlage der Daten in Ihrem Dataset
> * Abfragen der Azure Maps-WFS-API (Web Feature Service), um Informationen zu Ihren Kartenfeatures zu erhalten
> * Erstellen eines Featurezustandssets unter Verwendung Ihrer Kartenfeatures und der Daten in Ihrem Dataset
> * Aktualisieren Ihres Featurezustandssets

## <a name="prerequisites"></a>Voraussetzungen

Zum Erstellen von Gebäudeplänen ist Folgendes erforderlich:

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)
3. [Erstellen einer Creator-Ressource (Vorschau)](how-to-manage-creator.md)
4. Herunterladen des [Beispielzeichenpakets](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip)

In diesem Tutorial wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können aber auch eine andere API-Entwicklungsumgebung verwenden.

>[!IMPORTANT]
> Die API-URLs in diesem Dokument müssen möglicherweise entsprechend dem Speicherort der Creator-Ressource angepasst werden. Weitere Details finden Sie unter [Zugreifen auf Creator-Dienste](how-to-manage-creator.md#access-to-creator-services).

## <a name="upload-a-drawing-package"></a>Hochladen eines Zeichnungspakets

Verwenden Sie die [Datenupload-API](/rest/api/maps/data/uploadpreview), um das Zeichnungspaket in Azure Maps-Ressourcen hochzuladen.

Bei der Datenupload-API handelt es sich um eine zeitintensive Transaktion, durch die das hier definierte Muster implementiert wird. Nach Abschluss des Vorgangs wird die eindeutige Daten-ID (`udid`) verwendet, um auf das hochgeladene Paket zuzugreifen und es zu konvertieren. Führen Sie die folgenden Schritte aus, um die eindeutige Daten-ID (`udid`) zu erhalten:

1. Öffnen Sie die Postman-App. Wählen Sie oben in der Postman-App **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Collection** (Sammlung) aus.  Geben Sie einen Namen für die Sammlung ein, und klicken Sie dann auf **Create** (Erstellen).

2. Klicken Sie erneut auf **New** (Neu), um die Anforderung zu erstellen. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) ein. Wählen Sie die im vorherigen Schritt erstellte Sammlung und anschließend **Save** (Speichern) aus.

3. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **POST** aus, und geben Sie die folgende URL ein, um das Zeichnungspaket in den Azure Maps-Dienst hochzuladen. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Geben Sie auf der Registerkarte **Headers** (Header) einen Wert für den Schlüssel `Content-Type` an. Da es sich bei dem Zeichnungspaket um einen gezippten Ordner handelt, verwenden Sie den Wert `application/octet-stream`. Wählen Sie auf der Registerkarte **Body** (Textkörper) die Option **binary** (Binär) aus. Klicken Sie auf **Select File** (Datei auswählen), und wählen Sie ein Zeichnungspaket aus.

     ![data-management](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. Klicken Sie auf die blaue Schaltfläche **Send** (Senden), und warten Sie, bis die Anforderung verarbeitet wurde. Navigieren Sie nach Abschluss der Anforderung zur Registerkarte **Headers** (Header) der Antwort. Kopieren Sie den Wert des Schlüssels **Location** (Speicherort). Hierbei handelt es sich um die Status-URL (`status URL`).

6. Erstellen Sie zum Überprüfen des Status des API-Aufrufs eine **GET**-HTTP-Anforderung für `status URL`. An die URL muss zur Authentifizierung der primäre Abonnementschlüssel angefügt werden. Die **GET**-Anforderung sollte wie die folgende URL aussehen:

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. Wenn die **GET**-HTTP-Anforderung erfolgreich abgeschlossen wurde, wird ein `resourceLocation` zurückgegeben. Der `resourceLocation` enthält die eindeutige `udid` für den hochgeladenen Inhalt. Optional können Sie im nächsten Schritt mithilfe der `resourceLocation`-URL Metadaten aus dieser Ressource abzurufen.

    ```json
    {
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
    }
    ```

8. Erstellen Sie zum Abrufen von Inhaltsmetadaten eine **GET**-HTTP-Anforderung für die `resourceLocation`-URL, die Sie in Schritt 7 kopiert haben. Stellen Sie sicher, dass Sie für die Authentifizierung den primären Abonnementschlüssel an die URL anfügen. Die **GET**-Anforderung sollte wie die folgende URL aussehen:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Wenn die **GET**-HTTP-Anforderung erfolgreich ausgeführt wird, enthält der Antworttext die in Schritt 7 im `resourceLocation` angegebene `udid`, den Speicherort für den Zugriff bzw. das Herunterladen des Inhalts sowie einige weitere Metadaten zum Inhalt wie beispielsweise das Erstellungs- und Aktualisierungsdatum, die Größe und Ähnliches. Im Anschluss sehen Sie ein Beispiel für eine vollständige Antwort:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Konvertieren eines Zeichnungspakets

 Nach dem Hochladen des Zeichnungspakets wird die eindeutige Daten-ID (`udid`) für das hochgeladene Paket verwendet, um das Paket in Kartendaten zu konvertieren. Von der Konvertierungs-API wird eine zeitintensive Transaktion verwendet, durch die das [hier](creator-long-running-operation.md) definierte Muster implementiert wird. Nach Abschluss des Vorgangs wird die Konvertierungs-ID (`conversionId`) verwendet, um auf die konvertierten Daten zuzugreifen. Führen Sie die folgenden Schritte aus, um die Konvertierungs-ID (`conversionId`) zu erhalten:

1. Wählen Sie **Neu** aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen **Anforderungsnamen** ein, und wählen Sie eine Sammlung aus. Klicken Sie auf **Speichern**.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **POST** aus, und geben Sie die folgende URL ein, um Ihr hochgeladenes Zeichnungspaket in Kartendaten zu konvertieren. Verwenden Sie die eindeutige Daten-ID (`udid`) für das hochgeladene Paket.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > Die API-URLs in diesem Dokument müssen möglicherweise entsprechend dem Speicherort der Creator-Ressource angepasst werden. Ausführlichere Informationen finden Sie unter [Zugreifen auf Creator-Dienste](how-to-manage-creator.md#access-to-creator-services).

3. Klicken Sie auf die Schaltfläche **Send** (Senden), und warten Sie, bis die Anforderung verarbeitet wurde. Navigieren Sie nach Abschluss der Anforderung zur Registerkarte **Headers** (Header) der Antwort, und suchen Sie nach dem Schlüssel **Location** (Speicherort). Kopieren Sie den Wert des Schlüssels **Location** (Speicherort). Hierbei handelt es sich um die Status-URL (`status URL`) für die Konvertierungsanforderung. Diesen verwenden Sie im nächsten Schritt.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="Kopieren Sie den Wert des Location-Schlüssels":::.

4. Starten Sie auf der Registerkarte „Builder“ (Generator) eine neue HTTP-Methode vom Typ **GET**. Fügen Sie Ihren primären Abonnementschlüssel für Azure Maps an die Status-URL (`status URL`) an. Erstellen Sie eine **GET**-Anforderung an die `status URL`, die Sie in Schritt 3 kopiert haben. Die `status URL` sieht wie die folgende URL aus:

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

    Sollte der Konvertierungsprozess noch nicht abgeschlossen sein, wird möglicherweise eine JSON-Antwort wie die folgende angezeigt:

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. Nach erfolgreichem Abschluss der Anforderung enthält der Antworttext eine Erfolgsmeldung.  Kopieren Sie die Konvertierungs-ID (`conversionId`) aus der URL vom Typ `resourceLocation` für das konvertierte Paket. Die Konvertierungs-ID (`conversionId`) wird von einer anderen API verwendet, um auf die konvertierten Kartendaten zuzugreifen.

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>Zeitintensive HTTP-Anforderungen werden von der Postman-Anwendung nicht nativ unterstützt. Dies führt bei Anforderungen vom Typ **GET** für die Status-URL unter Umständen zu einer langen Verzögerung.  Warten Sie etwa 30 Sekunden, und klicken Sie erneut auf die Schaltfläche **Send** (Senden), bis die Antwort eine Erfolgs- oder Fehlermeldung enthält.

Das Beispielzeichnungspaket sollte sich ohne Fehler oder Warnungen konvertieren lassen. Falls bei Ihrem eigenen Zeichnungspaket Fehler oder Warnungen zurückgegeben werden, erhalten Sie in der JSON-Antwort einen Link zur [Schnellansicht für Zeichnungsfehler](drawing-error-visualizer.md). Mithilfe der Schnellansicht für Zeichnungsfehler können Sie Details von Fehlern und Warnungen untersuchen. Empfehlungen zur Behebung von Konvertierungsfehlern und -warnungen finden Sie unter [Fehler und Warnungen bei der Zeichnungskonvertierung](drawing-conversion-error-codes.md).

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Erstellen eines Datasets

Das Dataset ist eine Sammlung von Kartenfeatures wie Gebäuden, Ebenen und Räumen. Verwenden Sie zum Erstellen eines Datasets die [Dataseterstellungs-API](/rest/api/maps/dataset/createpreview). Die Dataseterstellungs-API akzeptiert die Konvertierungs-ID (`conversionId`) für das konvertierte Zeichnungspaket und gibt eine Dataset-ID (`datasetId`) des erstellten Datasets zurück. Die folgenden Schritte veranschaulichen das Erstellen eines Datasets.

1. Wählen Sie in der Postman-Anwendung **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen **Anforderungsnamen** ein, und wählen Sie eine Sammlung aus. Klicken Sie unten auf der Seite auf **Speichern**.

2. Senden Sie eine Anforderung vom Typ **POST** an die [Dataseterstellungs-API](/rest/api/maps/dataset/createpreview), um ein neues Dataset zu erstellen. Fügen Sie vor dem Übermitteln der Anforderung sowohl Ihren Abonnementschlüssel als auch die Konvertierungs-ID (`conversionId`) an, und verwenden Sie dabei die Konvertierungs-ID (`conversionId`), den Sie im Rahmen des Konvertierungsprozesses in Schritt 5 erhalten haben.  Die Anforderung sollte wie die folgende URL aussehen:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Entnehmen Sie dem Schlüssel **Location** (Speicherort) in den **Antwortheadern** die Status-URL (`statusURL`).

4. Erstellen Sie eine Anforderung vom Typ **GET** für die Status-URL (`statusURL`), um die Dataset-ID (`datasetId`) zu erhalten. Fügen Sie zur Authentifizierung Ihren primären Abonnementschlüssel für Azure Maps an. Die Anforderung sollte wie die folgende URL aussehen:

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. Nach erfolgreichem Abschluss der HTTP-Anforderung vom Typ **GET** enthält der Antwortheader die Dataset-ID (`datasetId`) für das erstellte Dataset. Kopieren Sie die Dataset-ID (`datasetId`). Die Dataset-ID (`datasetId`) wird zum Erstellen eines Kachelsets benötigt.

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Erstellen eines Kachelsets

Bei einem Kachelset handelt es sich um eine Gruppe von Vektorkacheln, die auf der Karte gerendert werden. Kachelsets werden auf der Grundlage vorhandener Datasets erstellt. Ein Kachelset ist jedoch von dem zugrunde liegenden Dataset unabhängig. Wenn das Dataset gelöscht wird, ist das Kachelset weiterhin vorhanden. Gehen Sie zum Erstellen eines Kachelsets wie folgt vor:

1. Wählen Sie in der Postman-Anwendung **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen **Anforderungsnamen** ein, und wählen Sie eine Sammlung aus. Klicken Sie unten auf der Seite auf **Speichern**.

2. Erstellen Sie auf der Registerkarte „Builder“ (Generator) eine Anforderung vom Typ **POST**. Die Anforderungs-URL sollte wie die folgende URL aussehen:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Erstellen Sie eine Anforderung vom Typ **GET** für die Status-URL (`statusURL`) für das Kachelset. Fügen Sie zur Authentifizierung Ihren primären Abonnementschlüssel für Azure Maps an. Die Anforderung sollte wie die folgende URL aussehen:

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Nach erfolgreichem Abschluss der HTTP-Anforderung vom Typ **GET** enthält der Antwortheader die Dataset-ID (`tilesetId`) für das erstellte Kachelset. Kopieren Sie die Kachelset-ID (`tilesetId`).

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>Abfragen von Datasets mit der WFS-API

 Datasets können mithilfe der [WFS-API](/rest/api/maps/wfs) abgefragt werden. Mit der WFS-API können Sie Featuresammlungen, eine bestimmte Sammlung oder ein bestimmtes Feature mit einer **Feature-ID** abfragen. Durch die **Feature-ID** wird das Feature innerhalb des Datasets eindeutig identifiziert. Sie wird beispielsweise verwendet, um anzugeben, welcher Featurezustand in einem bestimmten Zustandsset aktualisiert werden soll.

1. Wählen Sie in der Postman-Anwendung **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen **Anforderungsnamen** ein, und wählen Sie eine Sammlung aus. Klicken Sie unten auf der Seite auf **Speichern**.

2. Erstellen Sie eine Anforderung vom Typ **GET**, um eine Liste mit den Sammlungen in Ihrem Dataset anzuzeigen. Ersetzen Sie `<dataset-id>` durch Ihre Dataset-ID (`datasetId`). Verwenden Sie anstelle des Platzhalters Ihren Primärschlüssel für Azure Maps. Die Anforderung sollte wie die folgende URL aussehen:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. Der Antworttext wird im GeoJSON-Format zurückgegeben und enthält alle Sammlungen im Dataset. Der Einfachheit halber enthält das hier gezeigte Beispiel nur die Sammlung `unit`. Ein Beispiel mit allen Sammlungen finden Sie unter [WFS-API für Sammlungsbeschreibungen](/rest/api/maps/wfs/collectiondescriptionpreview). Wenn Sie weitere Informationen zu einer Sammlung benötigen, können Sie auf eine der URLs im Element `link` klicken.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. Erstellen Sie eine Anforderung vom Typ **GET** für die Featuresammlungen vom Typ `unit`.  Ersetzen Sie `{datasetId}` durch Ihre Dataset-ID (`datasetId`). Verwenden Sie anstelle des Platzhalters Ihren Primärschlüssel für Azure Maps. Der Antworttext enthält alle Features der Sammlung `unit`. Die Anforderung sollte wie die folgende URL aussehen:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Kopieren Sie das Feature `id` für ein Einheitenfeature mit dynamisch änderbaren Stileigenschaften.  Da der Belegungsstatus und die Temperatur der Einheit dynamisch aktualisiert werden können, wird im nächsten Abschnitt die ID (`id`) dieses Features verwendet. Im folgenden Beispiel lautet die ID (`id`) des Features „UNIT26“. Die Stileigenschaften dieses Features werden als Zustände bezeichnet, und das Feature wird zum Erstellen eines Zustandssets verwendet.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Erstellen eines Featurezustandssets

1. Wählen Sie in der Postman-Anwendung **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen **Anforderungsnamen** ein, und wählen Sie eine Sammlung aus. Klicken Sie unten auf der Seite auf **Speichern**.

2. Senden Sie eine Anforderung vom Typ **POST** an die [API zum Erstellen von Zustandssets](/rest/api/maps/featurestate/createstatesetpreview). Verwenden Sie die Dataset-ID (`datasetId`) des Datasets mit dem Zustand, den Sie ändern möchten. Die Anforderung sollte wie die folgende URL aussehen:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Legen Sie in den **Headern** der Anforderung vom Typ **POST** die Option `Content-Type` auf `application/json` fest. Geben Sie im **Text** die unten angegebenen Stile an, um Änderungen an den *Zuständen* `occupied` und `temperature` widerzuspiegeln. Klicken Sie abschließend auf **Send** (Senden).

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. Kopieren Sie die Zustandsset-ID (`statesetId`) aus dem Antworttext.

5. Erstellen Sie eine Anforderung vom Typ **POST**, um den Zustand zu aktualisieren: Übergeben Sie die Zustandsset-ID und die Feature-ID (`ID`) zusammen mit Ihrem Abonnementschlüssel für Azure Maps. Die Anforderung sollte wie die folgende URL aussehen:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Legen Sie in den **Headern** der Anforderung vom Typ **POST** die Option `Content-Type` auf `application/json` fest. Kopieren Sie den JSON-Code aus dem folgenden Beispiel, und fügen Sie ihn in den **TEXT** der Anforderung vom Typ **POST** ein.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > Die Aktualisierung wird nur gespeichert, wenn der Zeitstempel der Bereitstellung nach dem Zeitstempel der vorherigen Anforderung liegt. Sie können einen beliebigen Schlüsselnamen übergeben, der zuvor im Rahmen der Erstellung konfiguriert wurde.

7. Nach erfolgreicher Aktualisierung erhalten Sie den HTTP-Statuscode `200 OK`. Falls Sie [dynamische Stile](indoor-map-dynamic-styling.md) für einen Gebäudeplan implementiert haben, wird die Aktualisierung zum angegebenen Zeitstempel auf Ihrer Karte gerendert.

Mit der [API zum Abrufen von Featurezuständen](/rest/api/maps/featurestate/getstatespreview) können Sie den Zustand eines Features unter Verwendung der zugehörigen Feature-ID (`ID`) abrufen. Mit der [API zum Löschen von Featurezuständen](/rest/api/maps/featurestate/deletestatesetpreview) können Sie das Zustandsset und die zugehörigen Ressourcen löschen.

Weitere Informationen zu den verschiedenen Azure Maps Creator-Diensten (Vorschau) aus diesem Artikel finden Sie unter [Creator (Vorschau) für Gebäudepläne](creator-indoor-maps.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Verwendung des Moduls „Gebäudepläne“ finden Sie in folgendem Artikel:

> [!div class="nextstepaction"]
> [Verwenden des Moduls „Gebäudepläne“ von Azure Maps](how-to-use-indoor-module.md)