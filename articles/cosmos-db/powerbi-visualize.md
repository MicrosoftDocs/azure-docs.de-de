---
title: Power BI-Tutorial für den Azure Cosmos DB-Connector
description: Verwenden Sie dieses Power BI-Tutorial, um JSON zu importieren, aufschlussreiche Berichte zu erstellen und Daten mithilfe des Azure Cosmos DB- und Power BI-Connectors zu visualisieren.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 76531de279dfe6e9b73b3895f0ef63c4c88b63cd
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979008"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualisieren von Azure Cosmos DB-Daten mit dem Power BI-Connector

[Power BI](https://powerbi.microsoft.com/) ist ein Onlinedienst, in dem Sie Dashboards und Berichte erstellen und freigeben können. Power BI Desktop ist ein Berichterstellungstool, das Sie zum Abrufen von Daten aus verschiedenen Datenquellen verwenden können. Azure Cosmos DB ist eine der Datenquellen, die Sie mit Power BI Desktop verwenden können. Sie können Power BI Desktop mit dem Azure Cosmos DB-Konto über den Azure Cosmos DB-Connector für Power BI verbinden.  Nachdem Sie Azure Cosmos DB-Daten in Power BI importiert haben, können Sie diese transformieren, Berichte erstellen und die Berichte in Power BI veröffentlichen.   

Dieser Artikel beschreibt die Schritte, die erforderlich sind, um das Azure Cosmos DB-Konto mit Power BI Desktop zu verbinden. Nachdem die Verbindung hergestellt wurde, navigieren Sie zu einer Sammlung, extrahieren die Daten, transformieren die JSON-Daten in ein Tabellenformat und veröffentlichen einen Bericht in Power BI.

> [!NOTE]
> Der Power BI-Connector für Azure Cosmos DB stellt eine Verbindung mit Power BI Desktop her. In Power BI Desktop erstellte Berichte können auf PowerBI.com veröffentlicht werden. Eine direkte Extraktion von Azure Cosmos DB-Daten kann nicht auf PowerBI.com ausgeführt werden. 

> [!NOTE]
> Das Herstellen einer Verbindung mit Azure Cosmos DB mit dem Power BI-Connector wird derzeit nur für Azure Cosmos DB-SQL-API- und Gremlin-API-Konten unterstützt.

## <a name="prerequisites"></a>Voraussetzungen
Vergewissern Sie sich vor dem Ausführen der Anweisungen dieses Power BI-Tutorials, dass Sie über Zugriff auf folgende Ressourcen verfügen:

* [Laden Sie die neueste Version von Power BI Desktop herunter](https://powerbi.microsoft.com/desktop).

* Laden Sie die [Beispieldaten zu Vulkanen](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) von GitHub herunter.

* [Erstellen Sie ein Azure Cosmos DB-Datenbankkonto](https://azure.microsoft.com/documentation/articles/create-account/), und importieren Sie die Vulkandaten mithilfe des [Azure Cosmos DB-Datenmigrationstools](import-data.md). Berücksichtigen Sie beim Importieren von Daten die folgenden Einstellungen für die Quelle und die Ziele im Datenmigrationstool:

   * **Quellparameter** 

       * **Importieren aus:** JSON-Datei(en)

   * **Zielparameter** 

      * **Verbindungszeichenfolge:** `AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Partitionsschlüssel:** /Country 

      * **Sammlungsdurchsatz:** 1000 

Zum Freigeben von Berichten in PowerBI.com müssen Sie über ein Konto in PowerBI.com verfügen.  Weitere Informationen zu Power BI und zu Power BI Pro finden Sie unter [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Erste Schritte
In diesem Tutorial wird angenommen, dass Sie als Geologe Vulkane auf der ganzen Welt studieren. Die Daten zu Vulkanen werden in einem Azure Cosmos DB-Konto gespeichert, und das Format des JSON-Dokuments sieht folgendermaßen aus:

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Sie rufen die Daten zu Vulkanen aus dem Cosmos DB-Konto ab und visualisieren sie in einem interaktiven Power BI-Bericht wie dem folgenden.

1. Führen Sie Power BI Desktop aus.

2. Direkt über die Willkommensseite können Sie **Daten abrufen**, **aktuelle Quellen** anzeigen oder **andere Berichte öffnen**. Klicken Sie oben rechts auf das „X“, um das Dialogfeld zu schließen. Die Ansicht **Bericht** von Power BI Desktop wird angezeigt.
   
   ![Power BI Desktop-Berichtsansicht – Power BI-Connector](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Wählen Sie das Menüband **Start** aus, und klicken Sie dann auf **Daten abrufen**.  Das Fenster **Daten abrufen** wird angezeigt.

4. Klicken Sie auf **Azure**, wählen Sie **Azure Cosmos DB (Beta)** aus, und klicken Sie dann auf **Verbinden**. 

    ![Power BI Desktop-Datenabruf – Power BI-Connector](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Klicken Sie auf der Seite **Connector (Vorschau)** auf **Fortfahren**. Das Fenster **Azure Cosmos DB** wird angezeigt.

6. Geben Sie die Endpunkt-URL des Azure Cosmos DB-Kontos an, von dem Sie die Daten abrufen möchten, wie unten dargestellt, und klicken Sie dann auf **OK**. Um Ihr eigenes Konto zu verwenden, können Sie die URL aus dem Feld „URI“ auf dem Blatt **Schlüssel** des Azure-Portals abrufen. Optional können Sie den Datenbanknamen oder den Namen der Sammlung angeben oder den Navigator verwenden, um die Datenbank und die Sammlung auszuwählen, um festzustellen, woher die Daten stammen.
   
7. Wenn Sie zum ersten Mal eine Verbindung mit diesem Endpunkt herstellen, werden Sie aufgefordert, den Kontoschlüssel anzugeben. Für Ihr eigenes Konto können Sie den Schlüssel aus dem Feld **Primärschlüssel** auf dem Blatt **Schreibgeschützte Schlüssel** des Azure-Portals abrufen. Geben Sie den richtigen Schlüssel ein, und klicken Sie dann auf **Verbinden**.
   
   Es wird empfohlen, den Schlüssel mit Leseberechtigung beim Erstellen von Berichten zu verwenden. Dadurch wird verhindert, dass der Hauptschlüssel unnötig potenziellen Sicherheitsrisiken ausgesetzt wird. Der schreibgeschützte Schlüssel ist auf dem Blatt **Schlüssel** des Azure-Portals verfügbar. 
    
8. Wenn das Konto erfolgreich verbunden wurde, wird der Bereich **Navigator** angezeigt. Der **Navigator** zeigt eine Liste der Datenbanken für das Konto an.

9. Klicken Sie auf die Datenbank, aus der die Daten für den Bericht stammen, und erweitern Sie diese, und wählen Sie dann **volcanodb** aus (Ihr Datenbankname kann abweichend sein).   

10. Wählen Sie nun eine Sammlung aus, die die abzurufenden Daten enthält, und wählen Sie dann **volcano1** aus (Ihr Sammlungsname kann abweichend sein).
    
    Im Vorschaubereich wird eine Liste der **Datensatz** -Elemente angezeigt.  Ein Dokument wird mit dem Typ **Datensatz** in Power BI dargestellt. Auch ein geschachtelter JSON-Block innerhalb eines Dokuments ist ein **Datensatz**.
    
    ![Power BI-Tutorial für den Power BI-Connector für Azure Cosmos DB – Navigatorfenster](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Klicken Sie auf **Bearbeiten**, um den Abfrage-Editor in einem neuen Fenster zu starten und die Daten zu transformieren.

## <a name="flattening-and-transforming-json-documents"></a>Vereinfachen und Transformieren von JSON-Dokumenten
1. Wechseln Sie zum Abfrage-Editor-Fenster von Power BI, in dem im mittleren Bereich die Spalte **Dokument** angezeigt wird.
   ![Power BI Desktop – Abfrage-Editor](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Klicken Sie auf das Erweiterungssteuerelement rechts in der Spaltenüberschrift **Dokument**.  Das Kontextmenü mit einer Liste von Feldern wird angezeigt.  Wählen Sie die Felder aus, die Sie für Ihren Bericht benötigen, z.B. „Volcano Name“, „Country“, „Region“, „Location“, „Elevation“, „Type“, „Status“ und „Last Known Eruption“. Deaktivieren Sie das Kontrollkästchen **Ursprünglichen Spaltennamen als Präfix verwenden**, und klicken Sie dann auf **OK**.
   
    ![Power BI-Tutorial für den Power BI-Connector für Azure Cosmos DB – Erweitern von Dokumenten](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. Im mittleren Bereich wird eine Vorschau des Ergebnisses mit den ausgewählten Feldern angezeigt.
   
    ![Power BI-Tutorial für den Power BI-Connector für Azure Cosmos DB – Einfügen von Ergebnissen](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. In diesem Beispiel ist die Eigenschaft „Location“ ein GeoJSON-Block in einem Dokument.  Wie Sie sehen können, wird „Location“ mit dem Typ **Datensatz** in Power BI Desktop dargestellt.  
5. Klicken Sie rechts neben der Spaltenüberschrift „Document.Location“ auf das Erweiterungssteuerelement.  Das Kontextmenü mit Typ- und Koordinatenfeldern wird angezeigt.  Aktivieren Sie das Koordinatenfeld, stellen Sie sicher, dass die Option **Ursprünglichen Spaltennamen als Präfix verwenden** deaktiviert ist, und klicken Sie auf **OK**.
   
    ![Power BI-Tutorial für den Power BI-Connector für Azure Cosmos DB – Standortdatensatz](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Im mittleren Bereich wird nun eine Spalte „coordinates“ mit dem Typ **Liste** angezeigt.  Wie am Anfang des Tutorials dargestellt, weisen die GeoJSON-Daten in diesem Tutorial den Typ „Punkt“ auf. Die Werte für den Breiten- und Längengrad wurden im Koordinatenarray aufgezeichnet.
   
    Das Element „coordinates[0]“ stellt den Längengrad und „coordinates[1]“ den Breitengrad dar.
    ![Power BI-Tutorial für den Power BI-Connector für Azure Cosmos DB – Koordinatenliste](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Um das Koordinatenarray zu vereinfachen, erstellen wir eine **benutzerdefinierte Spalte** mit dem Namen „LatLong“.  Wählen Sie das Menüband **Spalte hinzufügen** aus, und klicken Sie auf **Benutzerdefinierte Spalte**.  Das Fenster **Benutzerdefinierte Spalte** wird angezeigt.
8. Geben Sie einen Namen für die neue Spalte an, z. B. „LatLong“.
9. Geben Sie dann die benutzerdefinierte Formel für die neue Spalte ein.  In unserem Beispiel verketten wir die Werte für Breiten- und Längengrad, getrennt durch ein Komma, wie unten dargestellt, mit der folgenden Formel: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Klicken Sie auf **OK**.
   
    Weitere Informationen zu DAX (Data Analysis Expressions), einschließlich DAX-Funktionen, finden Sie unter [DAX-Grundlagen in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
    ![Power BI-Tutorial für den Power BI-Connector für Azure Cosmos DB – Hinzufügen einer benutzerdefinierten Spalte](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Im mittleren Bereich werden nun die neuen LatLong-Spalten mit eingefügten Werten angezeigt.
    
    ![Power BI-Tutorial für den Power BI-Connector für Azure Cosmos DB – Benutzerdefinierte „LatLong“-Spalte](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Wenn Sie in der neuen Spalte einen Fehler erhalten, stellen Sie sicher, dass die unter „Abfrageeinstellungen“ angewendeten Schritte der folgenden Abbildung entsprechen:
    
    ![Die angewendeten Schritte sollten lauten: Quelle, Navigation, Dokument erweitert, Dokumentspeicherort erweitert, Benutzerdefiniertes Element hinzugefügt](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Wenn die Schritte unterschiedlich sind, löschen Sie die zusätzlichen Schritte, und versuchen Sie erneut, die benutzerdefinierte Spalte hinzuzufügen. 

11. Klicken Sie auf **Schließen und übernehmen** , um das Datenmodell zu speichern.
    
    ![Power BI-Tutorial für den Power BI-Connector für Azure Cosmos DB – Schließen und Übernehmen](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Erstellen der Berichte
In der Berichtsansicht von Power BI Desktop können Sie Berichte erstellen, um Daten zu visualisieren.  Sie können Berichte erstellen, indem Sie Felder in den Zeichenbereich **Bericht** ziehen.

![Power BI Desktop-Berichtsansicht – Power BI-Connector](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

In der Berichtsansicht wird Folgendes angezeigt:

1. Der Bereich **Felder**, in dem eine Liste von Datenmodellen mit Feldern angezeigt wird, die Sie für Ihre Berichte verwenden können
2. Der Bereich **Visualisierungen** . Ein Bericht kann eine einzelne oder mehrere Visualisierungen enthalten.  Wählen Sie die zu Ihren Bedürfnissen passenden Visualisierungstypen im Bereich **Visualisierungen** aus.
3. Der Zeichenbereich **Bericht**, in dem Sie die visuellen Objekte für den Bericht erstellen
4. Die Seite **Bericht** . Sie können mehrere Berichtsseiten in Power BI Desktop hinzufügen.

Das folgende Beispiel zeigt die grundlegenden Schritte zum Erstellen eines einfachen interaktiven Berichts mit einer Kartenansicht.

1. In unserem Beispiel erstellen wir eine Kartenansicht, die den Standort der einzelnen Vulkane anzeigt.  Klicken Sie im Bereich **Visualisierungen** auf den Visualisierungstyp für Karten, wie im Screenshot oben dargestellt.  Der Visualisierungstyp für Karten wird im Zeichenbereich **Bericht** angezeigt.  Zudem wird im Bereich **Visualisierung** ein Satz von Eigenschaften angezeigt, die zum Visualisierungstyp für Karten gehören.
2. Ziehen Sie jetzt das Feld „LatLong“ aus dem Bereich **Felder** auf die Eigenschaft **Standort** im Bereich **Visualisierungen**.
3. Ziehen Sie dann das Feld „Volcano Name“ auf die Eigenschaft **Legende** .  
4. Ziehen Sie anschließend das Feld „Elevation“ auf die Eigenschaft **Größe** .  
5. Jetzt sollte eine Karte mit einer Reihe von Blasen angezeigt werden. Die Blasen markieren den Standort der einzelnen Vulkane, und die Größe der Blasen korreliert mit der Höhe (Elevation) der Vulkane.
6. Sie haben jetzt einen einfachen Bericht erstellt.  Sie können den Bericht weiter anpassen, indem Sie weitere Visualisierungen hinzufügen.  In unserem Fall haben wir einen Datenschnitt für den Vulkantyp hinzugefügt, um den Bericht interaktiv zu gestalten.  
   
7. Klicken Sie im Menü „Datei“ auf **Speichern**, und speichern Sie die Datei unter „PowerBITutorial.pbix“.

## <a name="publish-and-share-your-report"></a>Veröffentlichen und Freigeben des Berichts
Zum Freigeben des Berichts müssen Sie über ein Konto in PowerBI.com verfügen.

1. Klicken Sie in Power BI Desktop auf das Menüband **Start** .
2. Klicken Sie auf **Veröffentlichen**.  Sie werden aufgefordert, den Benutzernamen und das Kennwort für Ihr PowerBI.com-Konto einzugeben.
3. Sobald die Anmeldeinformationen authentifiziert wurden, wird der Bericht im ausgewählten Ziel veröffentlicht.
4. Klicken Sie auf **'PowerBITutorial.pbix' in Power BI öffnen** , um Ihren Bericht auf PowerBI.com anzuzeigen und freizugeben.
   
    ![Erfolgreiches Veröffentlichen in Power BI! Öffnen des Tutorials in Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Erstellen eines Dashboards auf PowerBI.com
Nun, da Sie einen Bericht haben, können sie ihn auf PowerBi.com freigeben.

Beim Veröffentlichen Ihres Berichts aus Power BI Desktop auf „PowerBi.com“ werden in Ihrem PowerBi.com-Mandanten ein **Bericht** und ein **Dataset** generiert. Nachdem Sie beispielsweise einen Bericht namens **PowerBITutorial** auf „PowerBI.com“ veröffentlicht haben, wird „PowerBITutorial“ sowohl im Abschnitt **Berichte** als auch im Abschnitt **Datasets** auf „PowerBI.com“ angezeigt.

   ![Screenshot des neuen Berichts und Datasets auf PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Zum Erstellen eines Dashboards, das freigegeben werden kann, klicken Sie in Ihrem PowerBi.com-Bericht auf die Schaltfläche **Live-Seite anheften** .

   ![Screenshot des neuen Berichts und Datasets auf PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Führen Sie dann die Anweisungen unter [Anheften einer Kachel aus einem Bericht](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) aus, um ein neues Dashboard zu erstellen. 

Sie können auch Ad-hoc-Änderungen am Bericht durchführen, ehe Sie ein Dashboard erstellen. Es wird allerdings empfohlen, dass Sie Power BI Desktop verwenden, um die Änderungen vorzunehmen und den Bericht erneut auf PowerBi.com zu veröffentlichen.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu Power BI finden Sie unter [Erste Schritte mit Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Weitere Informationen zu Azure Cosmos DB finden Sie auf der [Startseite der Azure Cosmos DB-Dokumentation](https://azure.microsoft.com/documentation/services/cosmos-db/).

