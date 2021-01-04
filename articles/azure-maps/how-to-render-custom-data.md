---
title: Rendern von benutzerdefinierten Daten auf einer Rasterkarte | Microsoft Azure Maps
description: Hier erfahren Sie, wie Sie Ortsmarken, Bezeichnungen und geometrische Formen zu einer Rasterkarte hinzufügen. Informieren Sie sich zu diesem Zweck über die Verwendung des statischen Bilddiensts in Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5c70835c11bafb3fd06645ba51099b33d1eb6149
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906079"
---
# <a name="render-custom-data-on-a-raster-map"></a>Rendern von benutzerdefinierten Daten auf einer Rasterkarte

In diesem Artikel wird erläutert, wie Sie den [statischen Bilddienst](/rest/api/maps/render/getmapimage) mit der Bildzusammensetzungsfunktionalität verwenden können, um Überlagerungen auf einer Rasterkarte zu ermöglichen. Die Bildzusammensetzung beinhaltet die Möglichkeit, eine Rasterkachel mit zusätzlichen Daten wie benutzerdefinierten Ortsmarken, Bezeichnungen und Geometrieüberlagerungen abzurufen.

Sie können die Postman-Anwendung verwenden, um benutzerdefinierte Ortsmarken, Bezeichnungen und Geometrieüberlagerungen zu rendern. Sie können die [Datendienst-APIs](/rest/api/maps/data) von Azure Maps verwenden, um Überlagerungen zu speichern und zu rendern.

> [!Tip]
> Häufig ist die Nutzung des Azure Maps Web-SDK zum Anzeigen einer einfachen Karte auf einer Webseite kostengünstiger als die Verwendung des Diensts für statische Bilder. Das Web-SDK verwendet Kartenkacheln, und sofern der Benutzer in der Karte nicht schwenkt und zoomt, wird oftmals nur ein Bruchteil einer Transaktion pro Kartenladevorgang generiert. Beachten Sie, dass das Azure Maps Web SDK über Optionen zum Deaktivieren von Schwenken und Zoomen verfügt. Außerdem bietet das Azure Maps Web SDK umfassendere Optionen für die Datenvisualisierung als ein statischer Kartenwebdienst.  

## <a name="prerequisites"></a>Voraussetzungen

### <a name="create-an-azure-maps-account"></a>Erstellen eines Azure Maps-Kontos

Zum Ausführen der in diesem Artikel beschriebenen Verfahren müssen Sie zunächst ein Azure Maps-Konto erstellen und Ihren Kontoschlüssel für Maps abrufen. Befolgen Sie die Anleitung unter [Erstellen eines Kontos](quick-demo-map-app.md#create-an-azure-maps-account), um ein Azure Maps-Kontoabonnement zu erstellen. Führen Sie außerdem die Schritte unter [Abrufen des Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) aus, um den Primärschlüssel für Ihr Konto abzurufen. Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Rendern von Ortsmarken mit Bezeichnungen und benutzerdefinierten Bildern

> [!Note]
> Für die in diesem Abschnitt erläuterte Vorgehensweise ist ein Azure Maps-Konto in den Tarifen „S0“ oder „S1“ erforderlich.

Azure Maps-Konten im Tarif „S0“ unterstützen nur eine einzelne Instanz des Parameters `pins`. Sie können bis zu fünf Ortsmarken rendern, die in der URL-Anforderung mit einem benutzerdefinierten Bild festgelegt werden.

Führen Sie die folgenden Schritte aus, um Ortsmarken mit Bezeichnungen und einem benutzerdefinierten Bild zu rendern:

1. Erstellen Sie eine Sammlung, in der die Anforderungen gespeichert werden. Klicken Sie in der Postman-App auf **New** (Neu). Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Collection** (Sammlung) aus. Geben Sie einen Namen für die Sammlung ein, und klicken Sie dann auf **Create** (Erstellen). 

2. Klicken Sie erneut auf **New** (Neu), um die Anforderung zu erstellen. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) für die Ortsmarken ein. Wählen Sie die im vorherigen Schritt erstellte Sammlung als Speicherort für die Anforderung aus. Wählen Sie anschließend **Speichern** aus.
    
    ![Erstellen einer Anforderung in Postman](./media/how-to-render-custom-data/postman-new.png)

3. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode „GET“ aus, und geben Sie die folgende URL ein, um eine GET-Anforderung zu erstellen.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Das Ergebnis ist das folgende Bild:

    ![Eine benutzerdefinierte Ortsmarke mit einer Bezeichnung](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Abrufen von Daten aus dem Azure Maps-Datenspeicher

> [!Note]
> Für die in diesem Abschnitt erläuterte Vorgehensweise ist ein Azure Maps-Konto im Tarif „S1“ erforderlich.

Sie können den Pfad und die Standortinformationen einer Ortsmarke auch mithilfe der [Datenupload-API](/rest/api/maps/data/uploadpreview) abrufen. Gehen Sie folgendermaßen vor, um die Daten zu Pfaden und Ortsmarken hochladen.

1. Öffnen Sie in der Postman-App eine neue Registerkarte in der Sammlung, die Sie im vorherigen Abschnitt erstellt haben. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode „POST“ aus, und geben Sie die folgende URL ein, um eine POST-Anforderung zu erstellen:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Geben Sie die folgenden Schlüssel-Wert-Paare auf der Registerkarte **Params** (Parameter) ein, die für die URL der POST-Anforderung verwendet werden. Ersetzen Sie den Wert `subscription-key` durch Ihren Azure Maps-Abonnementschlüssel.
    
    ![Schlüssel-Wert-Parameter in Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Klicken Sie auf der Registerkarte **Body** (Text) auf „raw input format“ (Rohdaten-Eingabeformat), und wählen Sie JSON aus der Dropdownliste als Eingabeformat aus. Geben Sie den folgenden JSON-Code als hochzuladende Daten ein:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Klicken Sie auf **Send** (Senden), und überprüfen Sie den Antwortheader. Bei einer erfolgreichen Anforderung enthält der Adressheader den Status-URI, um den aktuellen Status der Uploadanforderung zu überprüfen. Der Status-URI hat folgendes Format.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Kopieren Sie Ihren Status-URI, und fügen Sie den subscription-key-Parameter mit dem Wert des Abonnementschlüssels Ihres Azure Maps-Kontos an diesen an. Verwenden Sie den gleichen Kontoabonnementschlüssel, den Sie zum Hochladen der Daten verwendet haben. Das Format des Status-URI sollte wie folgt aussehen:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Zum Abrufen des Werts für „udId“ öffnen Sie eine neue Registerkarte in der Postman-App. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-GET-Methode aus. Senden Sie eine GET-Anforderung an den Status-URI. Wenn der Datenupload erfolgreich ausgeführt wurde, erhalten Sie im Antworttext einen Wert für „udId“. Kopieren Sie den Wert für „udId“.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Verwenden Sie den von der Data Upload-API empfangenen Wert `udId`, um Merkmale auf der Karte zu rendern. Öffnen Sie hierzu eine neue Registerkarte in der Sammlung, die Sie im vorherigen Abschnitt erstellt haben. Wählen Sie die GET HTTP-Methode auf der Registerkarte „Builder“ aus, ersetzen Sie {subscription-key} und {udId} durch Ihre Werte, und geben Sie diese URL ein, um eine GET-Anforderung zu stellen:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Hier sehen Sie das Antwortbild:

    ![Abrufen von Daten aus dem Azure Maps-Datenspeicher](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Rendern eines Polygons mit Farbe und Transparenz

> [!Note]
> Für die in diesem Abschnitt erläuterte Vorgehensweise ist ein Azure Maps-Konto im Tarif „S1“ erforderlich.


Sie können das Aussehen eines Polygons ändern, indem Sie Stilmodifikatoren mit dem Parameter [path](/rest/api/maps/render/getmapimage#uri-parameters) verwenden.

1. Öffnen Sie in der Postman-App eine neue Registerkarte in der zuvor erstellten Sammlung. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode „GET“ aus, und geben Sie die folgende URL ein, um eine GET-Anforderung zum Rendern eines Polygons mit Farbe und Transparenz zu konfigurieren:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Hier sehen Sie das Antwortbild:

    ![Rendern eines durchsichtigen Polygons](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Rendern von Kreisen und Ortsmarken mit benutzerdefinierten Bezeichnungen

> [!Note]
> Für die in diesem Abschnitt erläuterte Vorgehensweise ist ein Azure Maps-Konto im Tarif „S1“ erforderlich.


Sie können die Darstellung der Ortsmarken durch Hinzufügen von Stilmodifizierern ändern. Sie können Ortsmarken und ihre Bezeichnungen beispielsweise vergrößern oder verkleinern, indem Sie den „Skalierungsstil“-Modifizierer `sc` verwenden. Dieser Modifizierer akzeptiert Werte über 0 (null). Der Wert 1 ist die Standardskalierung. Werte größer als 1 machen die Ortsmarken größer, und Werte kleiner als 1 machen sie kleiner. Weitere Informationen zu den Modifizierern finden Sie unter [static image service path parameters (Parameter für statische Bilddienstpfade)](/rest/api/maps/render/getmapimage#uri-parameters).


Führen Sie die folgenden Schritte aus, um einen Kreis und Ortsmarken mit benutzerdefinierten Bezeichnungen zu rendern:

1. Öffnen Sie in der Postman-App eine neue Registerkarte in der zuvor erstellten Sammlung. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode „GET“ aus, und geben Sie die folgende URL ein, um eine GET-Anforderung zu senden:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Hier sehen Sie das Antwortbild:

    ![Rendern eines Kreises mit benutzerdefinierten Ortsmarken](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Zum Ändern der Farbe der Ortsmarken aus dem letzten Schritt ändern Sie den „Co“-Stilmodifizierer. Betrachten Sie `pins=default|la15+50|al0.66|lc003C62|co002D62|`, wird die aktuelle Farbe als „#002D62“ in CSS angegeben. Angenommen, Sie möchten den Wert in „#41d42a“ ändern. Schreiben Sie den neuen Farbwert nach dem „Co“-Spezifizierer wie folgt: `pins=default|la15+50|al0.66|lc003C62|co41D42A|`. Stellen einer neuen GET-Anforderung:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Im folgenden sehen Sie das Antwortbild nach dem Ändern der Farben der Ortsmarken:

    ![Rendern eines Kreises mit aktualisierten Ortsmarken](./media/how-to-render-custom-data/circle-updated-pins.png)

Auf ähnliche Weise können Sie andere Stilmodifiziererer ändern, hinzufügen und entfernen.

## <a name="next-steps"></a>Nächste Schritte


* Machen Sie sich mit der API-Dokumentation zum [Get Map-Image-API von Azure Maps](/rest/api/maps/render/getmapimage) vertraut.
* Weitere Informationen zum Azure Maps-Datendienst (Vorschau) finden Sie in der [Dienstdokumentation](/rest/api/maps/data).