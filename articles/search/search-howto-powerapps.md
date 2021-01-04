---
title: 'Tutorial: Abfragen in Power Apps'
titleSuffix: Azure Cognitive Search
description: Detaillierte Anleitung zum Erstellen einer Power App, die eine Verbindung mit einem Azure Cognitive Search-Index herstellt, Abfragen sendet und Ergebnisse rendert.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: e8c16f02cf6b77fa54d2a19abac48e9914aa99bd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008564"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>Tutorial: Abfragen eines Cognitive Search-Index über Power Apps

Nutzen Sie die Umgebung für die schnelle Anwendungsentwicklung von Power Apps, um eine benutzerdefinierte App für Ihre durchsuchbaren Inhalte in Azure Cognitive Search zu erstellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Herstellen einer Verbindung mit Azure Cognitive Search
> * Einrichten einer Abfrageanforderung
> * Visualisieren der Ergebnisse in einer Canvas-App

Sollten Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* [Power Apps-Konto](https://make.powerapps.com)

* Im Suchdienst gehosteter [Index „hotels-sample“](search-get-started-portal.md)

* [Abfrage-API-Schlüssel](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>Schritt 1: Erstellen eines benutzerdefinierten Connectors

Ein Connector in Power Apps ist eine Datenquellenverbindung. In diesem Schritt erstellen Sie einen benutzerdefinierten Connector, um eine Verbindung mit einem Suchindex in der Cloud herzustellen.

1. Führen Sie die [Anmeldung](https://make.powerapps.com) bei Power Apps durch.

1. Erweitern Sie auf der linken Seite **Daten** > **Benutzerdefinierte Connectors**.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menü „Benutzerdefinierte Connectors“" border="true":::

1. Wählen Sie **+ Neuer benutzerdefinierter Connector** und dann **Ohne Vorlage erstellen** aus.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Menü „Ohne Vorlage erstellen“" border="true":::

1. Geben Sie einen Namen für Ihren benutzerdefinierten Connector ein (z. B. *AzureSearchQuery*), und klicken Sie dann auf **Weiter**.

1. Geben Sie Informationen auf der Seite „Allgemein“ ein:

   * Symbolhintergrundfarbe (z. B. #007ee5)
   * Beschreibung (z. B. „Connector für Azure Cognitive Search“)
   * Unter „Host“ müssen Sie die URL des Suchdiensts eingeben (z. B. `<yourservicename>.search.windows.net`).
   * Geben Sie für „Basis-URL“ einfach „/“ ein.

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Dialogfeld „Allgemeine Informationen“" border="true":::

1. Legen Sie auf der Seite „Sicherheit“ den **Authentifizierungstyp** auf *API-Schlüssel* und „Parameterbezeichnung“ sowie „Parametername“ auf *api-key* fest. Wählen Sie für **Parameterstandort** *Header* aus, wie unten dargestellt.

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Option „Authentifizierungstyp“" border="true":::

1. Wählen Sie auf der Seite „Definitionen“ **+ Neue Aktion** aus, um eine Aktion zu erstellen, mit der der Index abgefragt wird. Geben Sie für die Zusammenfassung und den Namen der Vorgangs-ID den Wert „Abfrage“ ein. Geben Sie als Beschreibung z. B. *„Fragt den Suchindex ab“* ein.

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Optionen für „Neue Aktion“" border="true":::

1. Scrollen Sie nach unten. Klicken Sie unter „Anforderungen“ auf die Schaltfläche **+ Aus Beispiel importieren**, um eine Abfrageanforderung an Ihren Suchdienst zu konfigurieren:

   * Wählen Sie das Verb `GET` aus.

   * Geben Sie unter „URL“ eine Beispielabfrage für Ihren Suchindex ein (`search=*` gibt alle Dokumente zurück, `$select=` ermöglicht die Auswahl von Feldern). Die API-Version muss angegeben werden. Eine vollständige URL sieht beispielsweise wie folgt aus: `https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * Geben Sie unter „Header“ `Content-Type` ein. Sie legen den Wert später auf `application/json` fest.

     **Power Apps** nutzt die Syntax in der URL, um Parameter aus der Abfrage zu extrahieren: Die Parameter „search“, „select“ und „api-version“ können beim Durchlaufen des Assistenten konfiguriert werden.

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Aus Beispiel importieren" border="true":::

1. Klicken Sie auf **Importieren**, um die Anforderung automatisch auszufüllen. Klicken Sie neben den einzelnen Parametern auf das Symbol **...** , um die Parametermetadaten abschließend festzulegen. Klicken Sie nach jeder Parameteraktualisierung auf **Zurück**, um zur Seite „Anforderung“ zurückzukehren.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Dialogfeld „Aus Beispiel importieren“" border="true":::

1. Für *search*: Legen Sie `*` als **Standardwert** fest, wählen Sie unter **Erforderlich** die Option *Nein* aus und unter **Sichtbarkeit** die Option *Keine*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Suchparametermetadaten" border="true":::

1. Für *select*: Legen Sie `HotelName,Description,Address/City` als **Standardwert** fest, wählen Sie unter **Erforderlich** die Option *Nein* aus und unter **Sichtbarkeit** die Option *Keine*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="Auswählen der Parametermetadaten" border="true":::

1. Für *api-version*: Legen Sie `2020-06-30` als **Standardwert** fest, wählen Sie unter **Erforderlich** die Option *Ja* aus und unter **Sichtbarkeit** die Option *Intern*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadaten des Versionsparameters" border="true":::

1. Für *Content-Type*: Legen Sie diese Option auf `application/json` fest.

1. Nachdem Sie diese Änderungen vorgenommen haben, wechseln Sie zur Ansicht **Swagger-Editor**. Im Abschnitt „parameters“ wird die folgende Konfiguration angezeigt:

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. Wechseln Sie zurück zum Assistenten und zu Schritt **3. Anfordern**. Scrollen Sie nach unten zum Abschnitt „Antwort“. Klicken Sie auf **Standardantwort hinzufügen**. Dies ist wichtig, da Power Apps dadurch das Schema der Antwort verstehen kann. 

1. Fügen Sie eine Beispielantwort ein. Eine einfache Möglichkeit zum Erfassen einer Beispielantwort ist die Verwendung des Suchexplorers im Azure-Portal. Geben Sie im Suchexplorer die gleiche Abfrage wie für die Anforderung ein, fügen Sie jedoch **$top=2** hinzu, um die Ergebnisse auf zwei Dokumente zu beschränken: `search=*&$select=HotelName,Description,Address/City&$top=2` 

   Power Apps benötigt nur einige wenige Ergebnisse, um das Schema zu erkennen. Sie können die folgende Antwort nun in den Assistenten kopieren, vorausgesetzt, Sie verwenden „hotels-sample-index“.

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > Sie können für die JSON-Antwort nur eine begrenzte Anzahl von Zeichen eingeben. Vereinfachen Sie den JSON-Code daher ggf., bevor Sie ihn einfügen. Das Schema und das Format der Antwort sind wichtiger als die Werte selbst. Beispielsweise könnte der Text für das Beschreibungsfeld auf den ersten Satz beschränkt werden.

1. Klicken Sie auf **Importieren**, um die Standardantwort hinzuzufügen.

1. Klicken Sie oben rechts auf **Connector erstellen**, um die Definition zu speichern.

1. Klicken Sie auf **Schließen**, um den Connector zu schließen.

## <a name="2---test-the-connection"></a>Schritt 2: Testen der Verbindung

Nach der erstmaligen Erstellung müssen Sie den Connector erneut über die Liste „Benutzerdefinierte Connectors“ öffnen, um ihn zu testen. Wenn Sie später weitere Änderungen vornehmen, können Sie die Tests im Assistenten durchführen.

Für diese Aufgabe benötigen Sie einen [Abfrage-API-Schlüssel](search-security-api-keys.md#find-existing-keys). Bei jeder Verbindungserstellung – ganz gleich, ob für einen Testlauf oder zur Einbindung in eine App – benötigt der Connector den zum Herstellen der Verbindung mit Azure Cognitive Search verwendeten Abfrage-API-Schlüssel.

1. Klicken Sie ganz links auf **Benutzerdefinierte Connectors**.

1. Suchen Sie in der Liste nach dem Connector (in diesem Tutorial „AzureSearchQuery“).

1. Wählen Sie den Connector aus, erweitern Sie die Liste mit den Aktionen, und wählen Sie **Eigenschaften anzeigen** aus.

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="Anzeigen von Eigenschafte" border="true":::

1. Wählen Sie oben rechts die Option **Bearbeiten** aus.

1. Wählen Sie **4. Testen** aus, um die Testseite zu öffnen.

1. Klicken Sie unter „Vorgang testen“ auf **+ Neue Verbindung**.

1. Geben Sie einen Abfrage-API-Schlüssel ein. Dies ist eine Azure Cognitive Search-Abfrage für den schreibgeschützten Zugriff auf einen Index. Sie können im Azure-Portal nach dem [Schlüssel suchen](search-security-api-keys.md#find-existing-keys). 

1. Klicken Sie unter „Vorgänge“ auf die Schaltfläche **Vorgang testen**. Wenn der Vorgang erfolgreich ausgeführt wurde, sollte der Status 200 angezeigt werden, und im Text der Antwort sollte JSON-Code zu sehen sein, der die Suchergebnisse beschreibt.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="JSON-Antwort" border="true":::

## <a name="3---visualize-results"></a>Schritt 3: Visualisieren der Ergebnisse

In diesem Schritt erstellen Sie eine Power App mit einem Suchfeld, einer Suchschaltfläche und einem Anzeigebereich für die Ergebnisse. Die Power App stellt eine Verbindung mit dem zuvor erstellten benutzerdefinierten Connector her, um die Daten aus Azure Search abzurufen.

1. Erweitern Sie auf der linken Seite **Apps** >  **+ Neue App** > **Canvas**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Erstellen einer Canvas-App" border="true":::

1. Wählen Sie den Anwendungstyp aus. Erstellen Sie für dieses Tutorial eine **Leere App** mit **Telefonlayout**. **Power Apps Studio** wird angezeigt.

1. Wählen Sie in Studio die Registerkarte **Datenquellen** aus, und klicken Sie auf den neuen Connector, den Sie gerade erstellt haben. In unserem Fall lautet sein Name *AzureSearchQuery*. Klicken Sie auf **Verbindung hinzufügen**.

   Geben Sie den Abfrage-API-Schlüssel ein.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="Verbinden des Connectors" border="true":::

    *AzureSearchQuery* ist nun eine Datenquelle, die für die Verwendung in Ihrer Anwendung verfügbar ist.

1. Fügen Sie der Canvas auf der Registerkarte **Einfügen** einige Steuerelemente hinzu.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Einfügen von Steuerelementen" border="true":::

1. Fügen Sie die folgenden Elemente ein:

   * „Bezeichnungstext“ mit dem Wert „Abfrage:“
   * „Texteingabe“ (benennen Sie sie mit *txtQuery*, Standardwert: „*“)
   * Eine Schaltfläche mit dem Text „Suchen“ 
   * „Vertical Gallery“ (Vertikaler Katalog) (benennen Sie ihn mit *galleryResults*)

    Die Canvas sollte in etwa wie folgt aussehen:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Layout der Steuerelemente" border="true":::

1. Damit die **Schaltfläche „Suchen“** eine Abfrage sendet, fügen Sie die folgende Aktion in **OnSelect** ein:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   Der folgende Screenshot zeigt die Bearbeitungsleiste für die **OnSelect**-Aktion.

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="OnSelect für Schaltfläche" border="true":::

   Diese Aktion bewirkt, dass die Schaltfläche eine neue Sammlung mit dem Namen *azResult* mit dem Ergebnis der Suchabfrage aktualisiert. Dabei wird der Text im Textfeld *txtQuery* als Abfragebegriff verwendet.

   > [!NOTE]
   > Versuchen Sie Folgendes, wenn Sie einen Formelsyntaxfehler mit dem Hinweis erhalten, dass die ClearCollect-Funktion einige ungültige Funktionen aufweist:
   > 
   > * Stellen Sie zunächst sicher, dass der Connectorverweis korrekt ist. Löschen Sie den Verbindungsnamen, und geben Sie die ersten Buchstaben des Namens Ihres Connectors ein. IntelliSense sollte den richtigen Connector und das richtige Verb vorschlagen.
   > 
   > * Falls der Fehler weiterhin auftritt, müssen Sie den Connector löschen und neu erstellen. Wenn mehrere Instanzen eines Connectors vorhanden sind, verwendet die App möglicherweise die falsche Instanz.
   > 

1. Verknüpfen Sie das Steuerelement „Vertikaler Katalog“ mit der Sammlung *azResult*, die im vorherigen Schritt erstellt wurde. 

   Wählen Sie das Katalogsteuerelement aus, und führen Sie im Bereich „Eigenschaften“ die folgenden Aktionen aus.

   * Legen Sie **DataSource** auf *azResult* fest.
   * Wählen Sie ein **Layout**, das für den Typ der Daten im Index geeignet ist. In diesem Fall haben wir das Layout *Titel, Untertitel und Text* verwendet.
   * **Bearbeiten Sie die Felder**, und wählen Sie die Felder aus, die Sie visualisieren möchten.

    Da wir beim Definieren des Connectors ein Beispielergebnis angegeben haben, erkennt die App die im Index verfügbaren Felder.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Katalogfelder" border="true":::   
 
1. Drücken Sie **F5**, um eine Vorschau der App anzuzeigen.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="Fertiggestellte App" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben.

## <a name="next-steps"></a>Nächste Schritte

Power Apps ermöglicht die schnelle Anwendungsentwicklung von benutzerdefinierten Apps. Nun wissen Sie, wie Sie eine Verbindung mit einem Suchindex herstellen. Als Nächstes können Sie sich darüber informieren, wie Sie eine umfassende Visualisierungsoberfläche in einer benutzerdefinierten Power App erstellen.

> [!div class="nextstepaction"]
> [Lernkatalog für Power Apps](/powerapps/learning-catalog/get-started)