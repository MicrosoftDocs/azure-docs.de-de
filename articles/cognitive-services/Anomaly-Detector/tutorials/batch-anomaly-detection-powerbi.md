---
title: 'Tutorial: Visualisieren von Anomalien mithilfe von Batcherkennung und Power BI'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Anomalieerkennungs-API und Power BI zum Visualisieren von Anomalien in Zeitreihendaten verwenden.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: a17301e0807877662ae1bf34ade48e90a1d30c0c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006199"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Tutorial: Visualisieren von Anomalien mithilfe von Batcherkennung und Power BI

In diesem Tutorial erfahren Sie, wie Sie Anomalien innerhalb eines Zeitreihendatasets im Batchmodus finden. Sie arbeiten in Power BI Desktop mit einer Excel-Datei, bereiten die Daten für die Anomalieerkennungs-API auf und visualisieren statistische Anomalien in der gesamten Datei.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Importieren und Transformieren eines Zeitreihendatasets mithilfe von Power BI Desktop
> * Integrieren von Power BI Desktop in die Anomalieerkennungs-API zur Erkennung von Anomalien im Batchmodus
> * Visualisieren von Anomalien, die in Ihren Daten gefunden wurden, einschließlich erwarteter und beobachteter Werte, und Bestimmen von Grenzen für die Erkennung von Anomalien

## <a name="prerequisites"></a>Voraussetzungen
* [Ein Azure-Abonnement](https://azure.microsoft.com/free/cognitive-services)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), kostenlos verfügbar.
* Eine Excel-Datei (.xlsx) mit Datenpunkten in einer Zeitreihe. Die Beispieldaten für diesen Schnellstart finden Sie auf [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962).
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Erstellen einer Anomalieerkennungsressource"  target="_blank"> eine Anomalieerkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Anomalieerkennungs-API zu verbinden. Dies geschieht später in der Schnellstartanleitung.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Laden und Formatieren der Zeitreihendaten

Öffnen Sie zum Einstieg Power BI Desktop, und laden Sie die Zeitreihendaten, die Sie unter „Voraussetzungen“ heruntergeladen haben. Diese Excel-Datei enthält eine Reihe von Zeitstempel- und Wertepaaren im UTC-Format (Coordinated Universal Time).  

> [!NOTE]
> Power BI kann Daten aus einer Vielzahl von Quellen verarbeiten, beispielsweise u.a. CSV-Dateien, SQL-Datenbanken und Azure Blob Storage.  

Klicken Sie im Hauptfenster von Power BI Desktop auf das Menüband **Start**. Öffnen Sie in der Gruppe **Externe Daten** des Menübands das Dropdownmenü **Daten abrufen**, und klicken Sie auf **Excel**.

![Abbildung der Schaltfläche „Daten abrufen“ in Power BI](../media/tutorials/power-bi-get-data-button.png)

Navigieren Sie im angezeigten Dialogfeld zum Ordner, in den Sie die XLSX-Beispieldatei heruntergeladen haben, und wählen Sie sie aus. Klicken Sie im eingeblendeten Dialogfeld **Navigator** auf **Sheet1** und dann auf **Bearbeiten**.

![Abbildung des Bildschirms „Navigator“ mit der Datenquelle in Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI konvertiert die Zeitstempel in der ersten Spalte in den Datentyp `Date/Time`. Diese Zeitstempel müssen in Text konvertiert werden, um an die Anomalieerkennungs-API gesendet werden zu können. Wenn der Power Query-Editor nicht automatisch geöffnet wird, klicken Sie auf der Registerkarte „Start“ auf **Abfragen bearbeiten**.

Klicken Sie im Power Query-Editor auf das Menüband **Transformieren**. Öffnen Sie in der Gruppe **Beliebige Spalte** das Dropdownmenü **Datentyp:** , und wählen Sie **Text** aus.

![Abbildung des Dropdownmenüs für den Datentyp](../media/tutorials/data-type-drop-down.png)

Wenn Sie einen Hinweis zum Ändern des Spaltentyps erhalten, klicken Sie auf **Aktuelle ersetzen**. Klicken Sie anschließend auf dem Menüband **Start** auf **Schließen und übernehmen** oder **Übernehmen**.

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Erstellen einer Funktion zum Senden der Daten und Formatieren der Antwort

Zum Formatieren und Senden der Datendatei an die Anomalieerkennungs-API können Sie eine Abfrage für die oben erstellte Tabelle aufrufen. Öffnen Sie im Power Query-Editor auf dem Menüband **Start** das Dropdownmenü **Neue Quelle**, und klicken Sie auf **Leere Abfrage**.

Stellen Sie sicher, dass Ihre neue Abfrage ausgewählt ist, und klicken Sie dann auf **Erweiterter Editor**.

![Abbildung des Bildschirms „Erweiterter Editor“](../media/tutorials/advanced-editor-screen.png)

Verwenden Sie in „Erweiterter Editor“ den folgende Power Query M-Codeausschnitt, um die Spalten aus der Tabelle zu extrahieren und an die API zu senden. Anschließend erstellt die Abfrage aus der JSON-Antwort eine Tabelle und gibt diese zurück. Ersetzen Sie die Variable `apiKey` durch Ihren gültigen Anomalieerkennungs-API-Schlüssel und `endpoint` durch Ihren Endpunkt. Nachdem Sie die Abfrage in „Erweiterter Editor“ eingegeben haben, klicken Sie auf **Fertig**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody, ManualStatusHandling={400}]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({

                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),

    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Rufen Sie die Abfrage auf dem Datenblatt durch Auswahl von `Sheet1` unter **Parameter eingeben** und Klicken auf **Aufrufen** auf.

![Abbildung der Aufruffunktion](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Datenschutz und Authentifizierung für die Datenquelle

> [!NOTE]
> Beachten Sie die Richtlinien Ihres Unternehmens für den Schutz von Daten und den Zugriff darauf. Weitere Informationen finden Sie unter [Power BI Desktop – Datenschutzebenen](/power-bi/desktop-privacy-levels).

Sie erhalten möglicherweise eine Warnmeldung, wenn Sie versuchen, die Abfrage auszuführen, da sie eine externe Datenquelle verwendet.

![Abbildung einer von Power BI erstellten Warnung](../media/tutorials/blocked-function.png)

Um dieses Problem zu beheben, klicken Sie auf **Datei** und **Optionen und Einstellungen**. Klicken Sie dann auf **Optionen**. Wählen Sie unter **Aktuelle Datei** zunächst **Datenschutz** und dann **Sicherheitsstufen ignorieren und potenziell die Leistung verbessern** aus.

Außerdem erhalten Sie möglicherweise eine Meldung, in der Sie aufgefordert werden, anzugeben, wie Sie sich mit der API verbinden möchten.

![Abbildung einer Anforderung zur Angabe von Anmeldeinformationen für den Zugriff](../media/tutorials/edit-credentials-message.png)

Als Reaktion darauf klicken Sie in der Meldung auf **Anmeldeinformationen bearbeiten**. Wählen Sie im eingeblendeten Dialogfeld **Anonym** aus, um mit der API eine anonyme Verbindung herzustellen. Klicken Sie auf **Verbinden**.

Klicken Sie anschließend auf dem Menüband **Start** auf **Schließen und übernehmen**, um die Änderungen zu übernehmen.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualisieren der Antwort der Anomalieerkennungs-API

Beginnen Sie auf dem Hauptbildschirm von Power BI mit dem Verwenden der oben erstellten Abfragen, um die Daten zu visualisieren. Wählen Sie in **Visualisierungen** zuerst **Liniendiagramm** aus. Fügen Sie dann den Zeitstempel der aufgerufenen Funktion zur **Achse** des Liniendiagramms hinzu. Klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Zeitstempel** aus.

![Mit der rechten Maustaste auf den Wert „Zeitstempel“ klicken](../media/tutorials/timestamp-right-click.png)

Fügen Sie die folgenden Felder aus der **aufgerufenen Funktion** dem Feld **Werte** des Diagramms hinzu. Erstellen Sie Ihr Diagramm anhand des folgenden Screenshots.

* Wert
* UpperMargins
* LowerMargins
* ExpectedValues

![Abbildung der Diagrammeinstellungen](../media/tutorials/chart-settings.png)

Klicken Sie nach dem Hinzufügen der Felder auf das Diagramm, und ändern Sie dessen Größe so, dass alle Datenpunkte angezeigt werden. Ihr Diagramm sieht etwa wie der folgende Screenshot aus:

![Abbildung der Diagrammvisualisierung](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Anzeigen anomaler Datenpunkte

Klicken Sie rechts im Power BI-Fenster unter dem Bereich **FELDER** mit der rechten Maustaste unter der Abfrage **Aufgerufene Funktion** auf **Wert** und dann auf **Neues Quickmeasure**.

![Abbildung des Bildschirms „Neues Quickmeasure“](../media/tutorials/new-quick-measure.png)

Wählen Sie auf dem eingeblendeten Bildschirm als Berechnung **Gefilterter Wert** aus. Legen Sie **Basiswert** auf `Sum of Value` fest. Ziehen Sie dann `IsAnomaly` aus den Feldern von **Aufgerufene Funktion** auf **Filter**. Wählen Sie `True` im Dropdownmenü **Filter** aus.

![Zweite Abbildung des neuen Bildschirms „Quickmeasures“](../media/tutorials/new-quick-measure-2.png)

Nachdem Sie auf **OK** geklickt haben, sehen Sie unten in der Liste Ihrer Felder das Feld `Value for True`. Klicken Sie mit der rechten Maustaste darauf, und benennen Sie es in **Anomalie** um. Fügen Sie es dem Bereich **Werte** des Diagramms hinzu. Wählen Sie dann das Tool **Format** aus, und legen Sie den Typ der X-Achse auf **Kategorie** fest.

![Abbildung der Einstellungen für das Format der x-Achse](../media/tutorials/format-x-axis.png)

Wenden Sie Farben auf Ihr Diagramm an, indem Sie auf das Tool **Format** und dann auf **Datenfarben** klicken. Ihr Diagramm sollte etwa wie folgt aussehen:

![Abbildung des endgültigen Diagramms](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Anomalieerkennung in Streamingdaten mit Azure Databricks](anomaly-detection-streaming-databricks.md)