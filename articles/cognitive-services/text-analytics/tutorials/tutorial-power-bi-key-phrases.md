---
title: 'Tutorial: Integrieren von Power BI in die Textanalyse von Cognitive Services'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie die Textanalyse-API zum Extrahieren von Schlüsselbegriffen aus in Power BI gespeichertem Text verwendet wird.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: d987797c2c25f685a3c9250afeb17cec3ad3cb2e
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369544"
---
# <a name="tutorial-integrate-power-bi-with-the-text-analytics-cognitive-service"></a>Tutorial: Integrieren von Power BI in die Textanalyse von Cognitive Services

Microsoft Power BI Desktop ist eine kostenlose Anwendung, mit der Sie Ihre Daten vernetzen, transformieren und visualisieren können. Der Textanalysedienst, der Teil von Microsoft Azure Cognitive Services ist, ermöglicht die Verarbeitung natürlicher Sprache. So können aus unformatiertem unstrukturiertem Text die wichtigsten Ausdrücke extrahiert, Standpunkte analysiert und bekannte Entitäten wie Marken identifiziert werden. Zusammen bieten diese Tools Ihnen einen schnellen Überblick über die Gesprächsthemen Ihrer Kunden und ihr Empfinden darüber.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Importieren und Transformieren von Daten mithilfe von Power BI Desktop
> * Erstellen einer benutzerdefinierten Funktion in Power BI Desktop
> * Integrieren von Power BI Desktop in die Schlüsselbegriffserkennung der Textanalyse-API
> * Extrahieren der wichtigsten Ausdrücke aus Kundenfeedback mit der Schlüsselbegriffserkennung der Textanalyse-API
> * Erstellen einer Wortwolke aus Kundenfeedbacks

## <a name="prerequisites"></a>Voraussetzungen
<a name="Prerequisites"></a>

- Microsoft Power BI Desktop. [Kostenlos herunterladen](https://powerbi.microsoft.com/get-started/).
- Ein Microsoft Azure-Konto. [Erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/), oder [melden Sie sich an](https://portal.azure.com/).
- Sie benötigen ein Cognitive Services-API-Konto mit der Textanalyse-API. Wenn Sie keines besitzen, können Sie sich für den Free-Tarif [registrieren](../../cognitive-services-apis-create-account.md) und so Zugriff auf 5.000 Transaktionen pro Monat erhalten. (Details zur Durchführung dieses Tutorials finden Sie auf der Seite mit den [Preisdetails](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).)
- Außerdem benötigen Sie den [Zugriffsschlüssel für die Textanalyse](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource), der bei der Registrierung für Sie generiert wurde.
- Kundenkommentare. Sie können [unsere Beispieldaten](https://aka.ms/cogsvc/ta) oder Ihre eigenen Daten verwenden. In diesem Tutorial wird davon ausgegangen, dass Sie unsere Beispieldaten verwenden.

## <a name="load-customer-data"></a>Laden von Kundendaten
<a name="LoadingData"></a>

Öffnen Sie zum Einstieg Power BI Desktop, und laden Sie die CSV-Datei `FabrikamComments.csv` herunter, die Sie unter [Voraussetzungen](#Prerequisites) heruntergeladen haben. Diese Datei stellt die hypothetische Aktivität eines Tages im Supportforum eines kleinen fiktiven Unternehmen dar.

> [!NOTE]
> Power BI kann Daten aus einer Vielzahl von Quellen verarbeiten, z.B. Facebook oder einer SQL-Datenbank. Weitere Informationen dazu finden Sie unter [Facebook-Integration](https://powerbi.microsoft.com/integrations/facebook/) und [SQL Server-Integration](https://powerbi.microsoft.com/integrations/sql-server/).

Klicken Sie im Hauptfenster von Power BI Desktop auf das Menüband **Start**. Öffnen Sie in der Gruppe **Externe Daten** des Menübands das Dropdownmenü **Daten abrufen** , und klicken Sie auf **Text/CSV**.

![[Die Schaltfläche „Daten abrufen“]](../media/tutorials/power-bi/get-data-button.png)

Das Dialogfeld „Öffnen“ wird angezeigt. Navigieren Sie zum Ordner „Downloads“ oder zum Ordner, in dem Sie die Datei `FabrikamComments.csv` heruntergeladen haben. Klicken Sie auf `FabrikamComments.csv`, dann auf die Schaltfläche **Öffnen**. Das Dialogfeld „CSV-Dateien importieren“ wird angezeigt.

![[Das Dialogfeld „CSV-Dateien importieren“]](../media/tutorials/power-bi/csv-import.png)

Im Dialogfeld „CSV-Dateien importieren“ können sicherstellen, dass Power BI Desktop den Zeichensatz, Trennzeichen, Kopfzeilen und Spaltentypen richtig erkannt hat. Alle Informationen sind korrekt, also klicken Sie auf **Laden**.

Zum Anzeigen der geladenen Daten klicken Sie auf die Schaltfläche **Datenansicht** am linken Rand des Power BI-Arbeitsbereichs. Eine Tabelle mit den Daten wird geöffnet, wie in Microsoft Excel.

![[Die anfängliche Ansicht der importierten Daten]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="prepare-the-data"></a>Vorbereiten der Daten
<a name="PreparingData"></a>

Möglicherweise müssen Sie Ihre Daten in Power BI Desktop transformieren, bevor sie von der Schlüsselbegriffs-API des Textanalysediensts verarbeitet werden können.

Die Beispieldaten enthalten eine Spalte namens `subject` und eine Spalte namens `comment`. Mit der Funktion „Spalten zusammenführen“ in Power BI Desktop können Sie Schlüsselbegriffe aus den Daten in diese beiden Spalten extrahieren statt nur in die Spalte `comment`.

Klicken Sie in Power BI Desktop auf das Menüband **Start**. Klicken Sie in der Gruppe **Externe Daten** auf **Abfragen bearbeiten**.

![[Die Gruppe „Externe Daten“ im Menüband „Start“]](../media/tutorials/power-bi/edit-queries.png)

Wählen Sie in der Liste `FabrikamComments`Abfragen **auf der linken Seite des Fensters die Option** aus, falls sie nicht bereits ausgewählt ist.

Wählen Sie jetzt in der Tabelle die Spalten `subject` und `comment` aus. Möglicherweise müssen Sie einen horizontalen Bildlauf durchführen, um diese Spalten anzuzeigen. Klicken Sie zuerst auf die Spaltenüberschrift `subject`, halten Sie die STRG-Taste gedrückt, und klicken Sie dann auf die Spaltenüberschrift `comment`.

![[Auswählen von Feldern, die zusammengeführt werden sollen]](../media/tutorials/power-bi/select-columns.png)

Wählen Sie das Menüband **Transformieren** aus. Klicken Sie in der Gruppe **Textspalten** des Menübands auf **Spalten zusammenführen**. Das Dialogfeld „Spalten zusammenführen“ wird angezeigt.

![[Zusammenführen von Feldern über das Dialogfeld „Spalten zusammenführen“]](../media/tutorials/power-bi/merge-columns.png)

Wählen Sie im Dialogfeld „Spalten zusammenführen“ die Option `Tab` als Trennlinie aus, und klicken Sie dann auf **OK**.

Sie können auch den Filter „Leere entfernen“ verwenden, um leere Nachrichten herauszufiltern, oder die Transformation „Bereinigen“, um nicht druckbare Zeichen zu entfernen. Wenn Ihre Daten eine Spalte wie die Spalte `spamscore` in der Beispieldatei enthalten, können Sie Spam-Kommentare mithilfe eines Anzahlfilters überspringen.

## <a name="understand-the-api"></a>Grundlegendes zur API
<a name="UnderstandingAPI"></a>

Die [Schlüsselbegriffs-API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6) des Textanalysediensts kann bis zu tausend Textdokumente pro HTTP-Anforderung verarbeiten. Power BI bevorzugt die Verarbeitung einzelner Datensätze. Deshalb enthalten Ihre Aufrufe an die API in diesem Tutorial jeweils nur ein Dokument. Die Schlüsselbegriffs-API erfordert die folgenden Felder für jedes verarbeitete Dokument.

| Feld | BESCHREIBUNG |
| - | - |
| `id`  | Ein eindeutiger Bezeichner für dieses Dokument in der Anforderung. Dieses Feld ist auch in der Antwort enthalten. So können Sie bei der Verarbeitung mehrerer Dokumente die extrahierten Schlüsselbegriffe problemlos dem Dokument zuordnen, aus dem sie stammen. Da Sie in diesem Tutorial nur ein Dokument pro Anforderung verarbeiten, können Sie den Wert von `id` als vordefinierten Code aufnehmen, damit dieser für jede Anforderung identisch ist.|
| `text`  | Der zu verarbeitende Text. Der Wert dieses Felds stammt aus der Spalte `Merged`, die Sie im [vorherigen Abschnitt](#PreparingData) erstellt haben und die Betreffzeile zusammen mit dem Kommentartext enthält. Die Schlüsselbegriffs-API erfordert, dass diese Daten höchstens ca. 5.120 Zeichen umfassen.|
| `language` | Der Code zur Darstellung der natürlichen Sprache, in der das Dokument geschrieben ist. Alle Nachrichten in den Beispieldaten sind in Englisch verfasst, sodass Sie den Wert `en` für dieses Feld als vordefinierten Code aufnehmen können.|

## <a name="create-a-custom-function"></a>Erstellen einer benutzerdefinierten Funktion
<a name="CreateCustomFunction"></a>

Nun sind Sie bereit zum Erstellen der benutzerdefinierte Funktion, die Power BI und die Textanalyse integriert. Die Funktion erhält den zu verarbeitenden Text als Parameter. Sie konvertiert Daten in das erforderliche und aus dem erforderlichen JSON-Format und stellt die HTTP-Anforderung an die Schlüsselbegriffs-API. Die Funktion analysiert dann die Antwort von der API und gibt eine Zeichenfolge zurück, die eine durch Trennzeichen getrennte Liste der extrahierten Schlüsselbegriffe enthält.

> [!NOTE]
> Benutzerdefinierte Power BI Desktop-Funktionen sind in der [Power Query-Formelsprache „M“](/powerquery-m/power-query-m-reference) oder kurz „M“ geschrieben. M ist eine funktionale Programmiersprache, die auf [F#](/dotnet/fsharp/) basiert. Sie müssen jedoch kein Programmierer sein, um dieses Tutorial abzuschließen; der erforderliche Code ist unten angegeben.

Stellen Sie sicher, dass in Power BI Desktop nach wie vor das Fenster des Abfrage-Editors geöffnet ist. Klicken Sie anderenfalls auf das Menüband **Start** und in der Gruppe **Externe Daten** auf **Abfragen bearbeiten**.

Öffnen Sie nun im Menüband **Start** in der Gruppe **Neue Abfrage** das Dropdownmenü **Neue Quelle** , und wählen Sie **Leere Abfrage** aus. 

Der Abfrageliste wird eine neue Abfrage mit dem Standardnamen `Query1` hinzugefügt. Doppelklicken Sie auf diesen Eintrag, und nennen Sie ihn `KeyPhrases`.

Öffnen Sie jetzt das Fenster **Erweiterter Editor** , indem Sie in der Gruppe **Abfrage** des Menübands **Start** auf „Erweiterter Editor“ klicken. Löschen Sie den Code, der sich bereits in diesem Fenster befindet, und fügen Sie den folgenden Code ein. 

> [!NOTE]
> Ersetzen Sie den unten angegebenen Beispielendpunkt (der `<your-custom-subdomain>` enthält) durch den für Ihre Textanalyseressource generierten Endpunkt. Sie finden diesen Endpunkt durch Anmeldung beim [Azure-Portal](https://azure.microsoft.com/features/azure-portal/), Auswählen Ihres Textanalyseabonnements und Auswählen von `Quick start`.


```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics" & "/v3.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

Ersetzen Sie `YOUR_API_KEY_HERE` durch Ihren Zugriffsschlüssel für die Textanalyse. Sie finden diesen Schlüssel durch Anmeldung beim [Azure-Portal](https://azure.microsoft.com/features/azure-portal/), Auswählen Ihres Textanalyseabonnements und Klicken auf die Seite „Übersicht“. Achten Sie darauf, die Anführungszeichen vor und nach dem Schlüssel beizubehalten. Klicken Sie anschließend auf **Fertig**.

## <a name="use-the-custom-function"></a>Verwenden der benutzerdefinierten Funktion
<a name="UseCustomFunction"></a>

Jetzt können Sie die benutzerdefinierte Funktion verwenden, um die Schlüsselbegriffe aus allen Kundenkommentaren abzurufen und in einer neuen Spalte in der Tabelle zu speichern. 

Kehren Sie in Power BI Desktop im Fenster des Abfrage-Editors zur Abfrage `FabrikamComments` zurück. Wählen Sie das Menüband **Spalte hinzufügen** aus. Klicken Sie in der Gruppe **Allgemein** auf **Benutzerdefinierte Funktion aufrufen**.

![[Schaltfläche „Benutzerdefinierte Funktion aufrufen“]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

Das Dialogfeld „Benutzerdefinierte Funktion aufrufen“ wird angezeigt. Geben Sie unter **Neuer Spaltenname** Folgendes ein: `keyphrases`. Wählen Sie unter **Funktionsabfrage** die benutzerdefinierte Funktion aus, die Sie erstellt haben: `KeyPhrases`.

Ein neues Feld wird im Dialogfeld **Text (optional)** angezeigt. In diesem Feld werden Sie zur Auswahl der Spalte aufgefordert, in der Werte für den Parameter `text` der Schlüsselbegriffs-API angegeben werden sollen. (Beachten Sie, dass Sie bereits die Werte für die Parameter `language` und `id` als vordefinierte Werte aufgenommen haben.) Wählen Sie im Dropdownmenü `Merged` aus (die Spalte, die wir [zuvor](#PreparingData) durch Zusammenführen der Betreffs- und Nachrichtenfelder erstellt haben).

![[Aufrufen einer benutzerdefinierte Funktion]](../media/tutorials/power-bi/invoke-custom-function.png)

Klicken Sie abschließend auf **OK**.

Power BI ruft Ihre benutzerdefinierte Funktion einmal für jede Zeile in der Tabelle auf. Der Dienst sendet Abfragen an die Schlüsselbegriffs-API und fügt der Tabelle zum Speichern der Ergebnisse eine neue Spalte hinzu. Zuvor müssen Sie jedoch möglicherweise Authentifizierungs- und Datenschutzeinstellungen festlegen.

## <a name="authentication-and-privacy"></a>Authentifizierung und Datenschutz
<a name="Authentication"></a>

Nachdem Sie das Dialogfeld „Benutzerdefinierte Funktion aufrufen“ geschlossen haben, wird u.U. ein Banner angezeigt, das Sie auffordert, festzulegen, wie die Verbindung mit der Schlüsselbegriffs-API hergestellt wird.

![[Banner zu Anmeldeinformationen]](../media/tutorials/power-bi/credentials-banner.png)

Klicken Sie auf **Anmeldeinformationen bearbeiten** , stellen Sie sicher, dass `Anonymous` im Dialogfeld ausgewählt ist, und klicken Sie dann auf **Verbinden**. 

> [!NOTE]
> Sie wählen `Anonymous` aus, da der Textanalysedienst Sie über Ihren Zugriffsschlüssel authentifiziert, sodass Power BI für die HTTP-Anforderung selbst keine Anmeldeinformationen angeben muss.

> [!div class="mx-imgBorder"]
> ![[Festlegen der Authentifizierung auf „Anonym“]](../media/tutorials/power-bi/access-web-content.png)

Wenn das Banner „Anmeldeinformationen bearbeiten“ nach der Auswahl des anonymen Zugriffs weiter angezeigt wird, haben Sie möglicherweise vergessen, Ihren Zugriffsschlüssel für die Textanalyse in den Code der [benutzerdefinierten Funktion](#CreateCustomFunction) `KeyPhrases` einzufügen.

Als Nächstes kann ein Banner angezeigt werden, der Sie auffordert, Informationen zum Datenschutz Ihrer Datenquellen anzugeben. 

![[Datenschutz-Banner]](../media/tutorials/power-bi/privacy-banner.png)

Klicken Sie auf **Weiter** , und wählen Sie für jede Datenquelle im Dialogfeld `Public` aus. Klicken Sie anschließend auf **Speichern**.

![[Festlegen des Datenschutzes der Datenquelle]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="create-the-word-cloud"></a>Erstellen der Wortwolke
<a name="WordCloud"></a>

Wenn Sie mit allen angezeigten Bannern fertig sind, klicken Sie auf dem Menüband „Start“ auf **Schließen und übernehmen** , um den Abfrage-Editor zu schließen.

Power BI Desktop braucht einen Moment, um die erforderlichen HTTP-Anforderungen zu stellen. Für jede Zeile in der Tabelle enthält die neue Spalte `keyphrases` die von der Schlüsselbegriffs-API im Text erkannten Schlüsselbegriffe. 

Verwenden Sie diese Spalte nun, um eine Wortwolke zu generieren. Als Erstes klicken Sie im Hauptfenster von Power BI Desktop links vom Arbeitsbereich auf die Schaltfläche **Bericht**.

> [!NOTE]
> Warum werden Schlüsselbegriffe zum Erstellen einer Wortwolke verwendet, anstatt des vollständigen Texts aller Kommentare? Die Schlüsselbegriffe enthalten die *wichtigen* Wörter aus unseren Kundenkommentaren, nicht nur die *am häufigsten verwendeten* Wörter. Außerdem ist die Wortgrößenanpassung in der Wolke dann nicht durch die häufige Verwendung eines Worts in einer relativ geringen Anzahl von Kommentaren verzerrt.

Installieren Sie das benutzerdefinierte visuelle Objekt „Wortwolke“, wenn es noch nicht installiert ist. Klicken Sie im Bereich „Visualisierungen“ rechts neben dem Arbeitsbereich auf die drei Punkte ( **...** ), und wählen Sie **Import from Store** (Aus Store importieren). Suchen Sie nach „Wolke“, und klicken Sie dann neben dem visuellen Element „Wortwolke“ auf **Hinzufügen**. Power BI installiert das Visual „Wortwolke“ und weist Sie auf eine erfolgreiche Installation hin.

![[Hinzufügen eines benutzerdefinierten visuellen Elements]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Klicken Sie zunächst im Visualisierungsbereich auf das Wortwolkensymbol.

![[Wortwolkensymbol im Visualisierungsbereich]](../media/tutorials/power-bi/visualizations-panel.png)

Im Arbeitsbereich wird ein neuer Bericht angezeigt. Ziehen Sie das Feld `keyphrases` aus den Bereich „Felder“ in das Feld „Kategorie“ im Visualisierungsbereich. Die Wortwolke wird innerhalb des Berichts angezeigt.

Wechseln Sie nun zur Seite „Format“ des Visualisierungsbereichs. Aktivieren Sie in der Kategorie „Stoppwörter“ die Option **Standardstoppwörter** , um kurze häufige Wörter wie „von“ aus der Wolke auszuschließen. 

![[Aktivieren von Standardstoppwörtern]](../media/tutorials/power-bi/default-stop-words.png)

Deaktivieren Sie etwas weiter unten in diesem Bereich die Optionen **Text drehen** und **Titel**.

![[Aktivieren des Fokusmodus]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Klicken Sie im Bericht auf das Fokusmodus-Tool aus, um einen besseren Blick auf unsere Wortwolke zu erhalten. Das Tool erweitert die Wortwolke auf den gesamten Arbeitsbereich, wie unten dargestellt.

![[Eine Wortwolke]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Weitere Textanalysedienste
<a name="MoreServices"></a>

Der Textanalysedienst, welcher im Rahmen von Cognitive Services von Microsoft angeboten wird, stellt auch eine Standpunktanalyse und Sprachenerkennung bereit. Die Sprachenerkennung ist insbesondere nützlich, wenn nicht Ihr gesamtes Kundenfeedback in englischer Sprache verfasst ist.

Beide dieser APIs sind der Schlüsselbegriffs-API ähnlich. Das bedeutet, dass Sie sie in Power BI Desktop mithilfe von benutzerdefinierten Funktionen integrieren können, die fast identisch mit der Funktion sind, die Sie in diesem Tutorial erstellt haben. Dazu erstellen Sie einfach eine leere Abfrage und fügen wie zuvor den entsprechenden Code unten in den erweiterten Editor ein. (Vergessen Sie Ihren Zugriffsschlüssel nicht!) Verwenden Sie dann wie zuvor die Funktion, um der Tabelle eine Spalte hinzuzufügen.

Die unten dargestellte Standpunktanalysefunktion gibt ein Ergebnis zurück, das angibt, wie positiv die im Text ausgedrückte Stimmung ist.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[confidenceScores]
in  sentiment
```

Hier sind zwei Versionen einer Sprachenerkennungsfunktion. Die erste Funktion gibt den ISO-Sprachencode (z.B. `en` für Englisch) zurück, während die zweite Funktion den Anzeigenamen zurückgibt (z.B. `English`). Sie stellen möglicherweise fest, dass sich zwischen den beiden Versionen nur die letzte Zeile des Texts unterscheidet.

```fsharp
// Returns the two-letter language code (for example, 'en' for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (for example, 'English') of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Und hier ist schließlich eine Variante der bereits vorgestellten Schlüsselbegriffsfunktion, die die Begriffe als Listenobjekt anstatt als eine einzelne Zeichenfolge mit durch Trennzeichen getrennten Begriffen zurückgibt. 

> [!NOTE]
> Das Zurückgeben einer einzelnen Zeichenfolge hat unser Wortwolkenbeispiel vereinfacht. Andererseits ist eine Liste ein flexibleres Format für das Arbeiten mit den zurückgegebenen Begriffen in Power BI. Sie können Listenobjekte in Power BI Desktop mithilfe der Gruppe „Strukturierte Spalte“ im Menüband „Transformation“ des Abfrage-Editors ändern.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Nächste Schritte
<a name="NextSteps"></a>

Erfahren Sie mehr über den Textanalysedienst, die Power Query-Formelsprache „M“ oder Power BI.

> [!div class="nextstepaction"]
> [Referenz zur Textanalyse-API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0)

> [!div class="nextstepaction"]
> [Referenz zu Power Query M](/powerquery-m/power-query-m-reference)

> [!div class="nextstepaction"]
> [Power BI-Dokumentation](https://powerbi.microsoft.com/documentation/powerbi-landing-page/)