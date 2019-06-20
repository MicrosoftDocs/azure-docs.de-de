---
title: Ausführen von Bildvorgängen – Java
titlesuffix: Azure Cognitive Services
description: Erfahren Sie mehr über eine grundlegende Java Swing-App, die die Maschinelles Sehen-API in Azure Cognitive Services verwendet. Führen Sie eine optische Zeichenerkennung durch, erstellen Sie Miniaturansichten, und arbeiten Sie mit visuellen Merkmalen in einem Bild.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.author: kefre
ms.custom: seodec18
ms.date: 04/30/2019
ms.openlocfilehash: a22308e0c7ff924205f715692d011a4572b2bdb8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232636"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-java"></a>Verwenden von Maschinelles Sehen-Features mit REST-API und Java

Dieses Tutorial zeigt die Funktionen der Maschinellen Sehen-REST-API von Azure Cognitive Services.

Erfahren Sie mehr über eine Java Swing-Anwendung, die die Maschinelles Sehen-REST-API verwendet, um optische Zeichenerkennung (OCR) auszuführen, intelligent zugeschnittene Miniaturansichten zu erstellen sowie visuelle Merkmale wie Gesichter in Bildern zu erkennen, zu kategorisieren, zu markieren und zu beschreiben. In diesem Beispiel können Sie eine Bild-URL zur Analyse oder Verarbeitung einreichen. Sie können dieses Open Source-Beispiel als Vorlage zum Erstellen Ihrer eigenen App in Java verwenden, um die Maschinelles Sehen-REST-API zu verwenden.

Dieses Tutorial erklärt, wie Sie mit maschinellem Sehen folgende Aufgaben ausführen können:

> [!div class="checklist"]
> * Analysieren von Bildern
> * Identifizieren von natürlichen und von Menschen geschaffenen Wahrzeichen in Bildern
> * Identifizieren von Prominenten in Bildern
> * Erstellen von Miniaturansicht in hoher Qualität aus Bildern
> * Lesen von gedrucktem Text in Bildern
> * Lesen von handgeschriebenem Text in Bildern

Die Java Swing-Formularanwendung wurde bereits geschrieben, hat aber keine Funktionalität. In diesem Tutorial fügen Sie den spezifischen Code der Maschinellen Sehen-REST-API hinzu, um die Funktionalität der Anwendung zu vervollständigen.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="platform-requirements"></a>Plattformanforderungen

Dieses Tutorial wurde mit der NetBeans-IDE entwickelt. Insbesondere mit der **Java SE** von NetBeans, die Sie [hier herunterladen](https://netbeans.org/downloads/index.html) können.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Abonnieren der Maschinelles Sehen-API und Anfordern eines Abonnementschlüssels

Vor dem Erstellen des Beispiels müssen Sie die Maschinelles Sehen-API abonnieren, die zu den Azure Cognitive Services gehört. Informationen zu Abonnements und zur Schlüsselverwaltung finden Sie unter [Abonnements](https://azure.microsoft.com/try/cognitive-services/). In diesem Tutorial sind sowohl der Primär- als auch der Sekundärschlüssel gültig.

## <a name="acquire-incomplete-tutorial-project"></a>Abrufen eines unvollständigen Tutorialprojekts

### <a name="download-the-project"></a>Herunterladen des Projekts

1. Öffnen Sie das Repository [Tutorial: Maschinelles Sehen von Cognitive Services in Java](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial).
1. Klicken Sie auf die Schaltfläche **Klonen oder Herunterladen**.
1. Klicken Sie auf **ZIP-Datei herunterladen**, um eine ZIP-Datei des Tutorialprojekts herunterzuladen.

Es ist nicht erforderlich, den Inhalt der ZIP-Datei zu extrahieren, da NetBeans das Projekt aus der ZIP-Datei importiert.

### <a name="import-the-tutorial-project"></a>Importieren des Tutorialprojekts

Importieren Sie die Datei **cognitive-services-java-computer-vision-tutorial-master.zip** in NetBeans.

1. Klicken Sie in NetBeans auf **Datei** > **Projekt importieren** > **Aus ZIP-Datei**. Daraufhin wird das Dialogfeld **Projekte aus ZIP-Datei importieren** angezeigt.
1. Klicken Sie im Feld **ZIP-Datei:** auf die Schaltfläche **Durchsuchen**, um die Datei **cognitive-services-java-computer-vision-tutorial-master.zip** zu finden, und klicken Sie dann auf **Öffnen**.
1. Klicken Sie im Dialogfeld **Projekte aus ZIP-Datei importieren** auf **Importieren**.
1. Erweitern Sie im Bereich **Projekte** **ComputerVision** > **Quellpakete** >  **&lt;Standardpaket&gt;** . 
   Einige NetBeans-Versionen verwenden **src** anstelle von **Quellpakete** >  **&lt;Standardpaket&gt;** . Erweitern Sie in diesem Fall **src**.
1. Doppelklicken Sie auf **MainFrame.java**, um die Datei in den NetBeans-Editor zu laden. Daraufhin wird die Registerkarte **Entwurf** der **MainFrame.java**-Datei angezeigt.
1. Klicken Sie auf die Registerkarte **Quelle**, um den Java-Quellcode anzuzeigen.

### <a name="build-and-run-the-tutorial-project"></a>Erstellen und Ausführen des Tutorialprojekts

1. Drücken Sie **F6**, um die Tutorialanwendung zu erstellen und auszuführen.

    Klicken Sie in der Tutorialanwendung auf eine Registerkarte, um den Bereich für diese Funktion aufzurufen. Die Schaltflächen verfügen über leere Methoden, sodass sie keine Aktionen ausführen.

    Unten im Fenster befinden sich die Felder **Abonnementschlüssel** und **Abonnementregion**. Diese Felder müssen mit einem gültigen Abonnementschlüssel und der richtigen Region für diesen Abonnementschlüssel ausgefüllt werden. Wie Sie einen Abonnementschlüssel erhalten, erfahren Sie unter [Abonnements](https://azure.microsoft.com/try/cognitive-services/). Wenn Sie Ihren Abonnementschlüssel im Rahmen der kostenlosen Testversion unter diesem Link abgerufen haben, ist die Standardregion **westcentralus** die richtige Region für Ihre Abonnementschlüssel.

1. Beenden Sie die Tutorialanwendung.

## <a name="add-tutorial-code-to-the-project"></a>Hinzufügen von Tutorialcode zum Projekt

Die Java Swing-Anwendung verfügt über sechs Registerkarten. Jede Registerkarte zeigt eine andere Funktion von Maschinelles Sehen (Analysieren, OCR usw.). Die sechs Tutorialabschnitte sind voneinander unabhängig, sodass Sie beliebig viele der Abschnitte hinzufügen können. Darüber hinaus können Sie die Abschnitte in beliebiger Reihenfolge hinzufügen.

### <a name="analyze-an-image"></a>Analysieren von Bildern

Das Analysefeature von Maschinelles Sehen überprüft Bilder auf mehr als 2.000 erkennbare Objekte, Lebewesen, Landschaften und Aktionen. Sobald die Analyse abgeschlossen ist, gibt die Funktion ein JSON-Objekt zurück, das das Bild mit entsprechenden Tags, einer Farbanalyse, Beschriftungen und mehr beschreibt.

Um die Analysefunktion der Tutorialanwendung abzuschließen, führen Sie die folgenden Schritte aus:

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>Hinzufügen des Ereignishandlercodes für die Analyseschaltfläche

Die Ereignishandlermethode **analyzeImageButtonActionPerformed** löscht das Formular, zeigt das in der URL angegebene Bild an und ruft dann die Methode **AnalyzeImage** auf, um das Bild zu analysieren. Wenn **AnalyzeImage** zurückgegeben wird, zeigt die Methode die formatierte JSON-Antwort im Textbereich **Antwort** an, extrahiert die erste Beschriftung aus **JSONObject** und zeigt die Beschriftung sowie den Zuverlässigkeitsgrad an, dass die Beschriftung korrekt identifiziert wurde.

Kopieren Sie den folgenden Code und fügen Sie ihn in die Methode **analyzeImageButtonActionPerformed** ein.

> [!NOTE]
> NetBeans lässt das Einfügen in die Methodendefinitionszeile (```private void```) oder in die abschließende geschweifte Klammer dieser Methode nicht zu. Um den Code zu kopieren, kopieren Sie die Zeilen zwischen der Methodendefinition und der abschließenden geschweiften Klammer, und fügen Sie sie über den Inhalt der Methode ein.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

Die Methode **AnalyzeImage** dient als Wrapper für den REST-API-Aufruf zur Analyse von Bildern. Die Methode gibt ein **JSONObject**-Element zurück, das das Bild beschreibt, oder **NULL**, wenn ein Fehler aufgetreten ist.

Kopieren Sie die Methode **AnalyzeImage** und fügen Sie sie direkt unter der Methode **analyzeImageButtonActionPerformed** ein.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

#### <a name="run-the-analyze-function"></a>Ausführen der Analysefunktion

Drücken Sie **F6**, um die Anwendung auszuführen. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Geben Sie die URL zu dem zu analysierenden Bild ein, und klicken Sie dann auf die Schaltfläche **Bild analysieren**, um das Bild zu analysieren und das Ergebnis anzuzeigen.

### <a name="recognize-a-landmark"></a>Erkennen von Wahrzeichen

Die Funktion „Wahrzeichen“ für Maschinelles Sehen analysiert Bilder hinsichtlich Naturdenkmäler oder künstliche Wahrzeichen, wie z.B. Berge oder berühmte Gebäude. Sobald die Analyse abgeschlossen ist, gibt die Funktion ein JSON-Objekt zurück, das die im Bild gefundenen Wahrzeichen identifiziert.

Um die Funktion „Wahrzeichen“ der Tutorialanwendung abzuschließen, führen Sie die folgenden Schritte aus:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Hinzufügen des Ereignishandlercodes für die Formularschaltfläche

Die Ereignishandlermethode **landmarkImageButtonActionPerformed** löscht das Formular, zeigt das in der URL angegebene Bild an und ruft dann die Methode **LandmarkImage** auf, um das Bild zu analysieren. Wenn **LandmarkImage** zurückgegeben wird, zeigt die Methode die formatierte JSON-Antwort im Textbereich **Antwort** an, extrahiert den ersten Wahrzeichennamen aus **JSONObject** und zeigt ihn im Fenster gemeinsam mit dem Zuverlässigkeitsgrad an, dass das Wahrzeichen korrekt identifiziert wurde.

Kopieren Sie den folgenden Code und fügen Sie ihn in die Methode **landmarkImageButtonActionPerformed** ein.

> [!NOTE]
> NetBeans lässt das Einfügen in die Methodendefinitionszeile (```private void```) oder in die abschließende geschweifte Klammer dieser Methode nicht zu. Um den Code zu kopieren, kopieren Sie die Zeilen zwischen der Methodendefinition und der abschließenden geschweiften Klammer, und fügen Sie sie über den Inhalt der Methode ein.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

Die Methode **LandmarkImage** dient als Wrapper für den REST-API-Aufruf zur Analyse von Bildern. Die Methode gibt ein **JSONObject**-Element zurück, das das im Bild gefundene Wahrzeichen beschreibt, oder **NULL**, wenn ein Fehler aufgetreten ist.

Kopieren Sie die Methode **LandmarkImage** und fügen Sie sie direkt unter der Methode **landmarkImageButtonActionPerformed** ein.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-landmark-function"></a>Ausführen der Wahrzeichenfunktion

Drücken Sie **F6**, um die Anwendung auszuführen. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Klicken Sie auf die Registerkarte **Wahrzeichen**, geben Sie eine URL zu einem Bild eines Wahrzeichens ein, und klicken Sie dann auf die Schaltfläche **Bild analysieren**, um ein Bild zu analysieren und das Ergebnis anzuzeigen.

### <a name="recognize-celebrities"></a>Erkennen von Prominenten

Die Funktion „Prominente“ für Maschinelles Sehen analysiert Bilder hinsichtlich berühmter Persönlichkeiten. Sobald die Analyse abgeschlossen ist, gibt die Funktion ein JSON-Objekt zurück, das die im Bild gefundenen Prominenten identifiziert.

Um die Funktion „Prominente“ der Tutorialanwendung abzuschließen, führen Sie die folgenden Schritte aus:

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>Hinzufügen des Ereignishandlercodes für die Prominentenschaltfläche

Die Ereignishandlermethode **celebritiesImageButtonActionPerformed** löscht das Formular, zeigt das in der URL angegebene Bild an und ruft dann die Methode **CelebritiesImage** auf, um das Bild zu analysieren. Wenn **CelebritiesImage** zurückgegeben wird, zeigt die Methode die formatierte JSON-Antwort im Textbereich **Antwort** an, extrahiert den ersten Prominentennamen aus **JSONObject** und zeigt ihn im Fenster gemeinsam mit dem Zuverlässigkeitsgrad an, dass der Prominente korrekt identifiziert wurde.

Kopieren Sie den folgenden Code und fügen Sie ihn in die Methode **celebritiesImageButtonActionPerformed** ein.

> [!NOTE]
> NetBeans lässt das Einfügen in die Methodendefinitionszeile (```private void```) oder in die abschließende geschweifte Klammer dieser Methode nicht zu. Um den Code zu kopieren, kopieren Sie die Zeilen zwischen der Methodendefinition und der abschließenden geschweiften Klammer, und fügen Sie sie über den Inhalt der Methode ein.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

Die Methode **CelebritiesImage** dient als Wrapper für den REST-API-Aufruf zur Analyse von Bildern. Die Methode gibt ein **JSONObject**-Element zurück, das den im Bild gefundenen Prominenten beschreibt, oder **NULL**, wenn ein Fehler aufgetreten ist.

Kopieren Sie die Methode **CelebritiesImage** und fügen Sie sie direkt unter der Methode **celebritiesImageButtonActionPerformed** ein.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-celebrities-function"></a>Ausführen der Prominentenfunktion

Drücken Sie **F6**, um die Anwendung auszuführen. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Klicken Sie auf die Registerkarte **Prominente**, geben Sie eine URL zu einem Bild eines Prominenten ein, und klicken Sie dann auf die Schaltfläche **Bild analysieren**, um ein Bild zu analysieren und das Ergebnis anzuzeigen.

### <a name="intelligently-generate-a-thumbnail"></a>Intelligentes Generieren einer Miniaturansicht

Die Funktion „Miniaturansicht“ für Maschinelles Sehen generiert Miniaturansichten aus Bildern. Mithilfe der Funktion **Intelligentes Zuschneiden** identifiziert die Funktion „Miniaturansicht“ den für ein Bild relevanten Bereich, und zentriert das Miniaturbild auf diesem Bereich, um optisch ansprechendere Miniaturbilder zu generieren.

Um die Funktion „Miniaturansicht“ der Tutorialanwendung abzuschließen, führen Sie die folgenden Schritte aus:

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>Hinzufügen des Ereignishandlercodes für die Miniaturansichtsschaltfläche

Die Ereignishandlermethode **thumbnailImageButtonActionPerformed** löscht das Formular, zeigt das in der URL angegebene Bild an und ruft dann die Methode **getThumbnailImage** auf, um die Miniaturansicht zu erstellen. Wenn **getThumbnailImage** zurückgegeben wird, zeigt die Methode die generierte Miniaturansicht an.

Kopieren Sie den folgenden Code und fügen Sie ihn in die Methode **thumbnailImageButtonActionPerformed** ein.

> [!NOTE]
> NetBeans lässt das Einfügen in die Methodendefinitionszeile (```private void```) oder in die abschließende geschweifte Klammer dieser Methode nicht zu. Um den Code zu kopieren, kopieren Sie die Zeilen zwischen der Methodendefinition und der abschließenden geschweiften Klammer, und fügen Sie sie über den Inhalt der Methode ein.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

Die Methode **getThumbnailImage** dient als Wrapper für den REST-API-Aufruf zur Analyse von Bildern. Die Methode gibt ein **BufferedImage**-Element zurück, das die Miniaturansicht enthält, oder **NULL**, wenn ein Fehler aufgetreten ist. Die Fehlermeldung wird im ersten Element des **jsonError**-Zeichenfolgearrays zurückgegeben.

Kopieren Sie die folgende Methode **getThumbnailImage** und fügen Sie sie direkt unter der Methode **thumbnailImageButtonActionPerformed** ein.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

#### <a name="run-the-thumbnail-function"></a>Ausführen der Miniaturansichtsfunktion

Drücken Sie **F6**, um die Anwendung auszuführen. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Klicken Sie auf die Registerkarte **Miniaturansicht**, geben Sie eine URL zu einem Bild ein, und klicken Sie dann auf die Schaltfläche **Miniaturansicht generieren**, um ein Bild zu analysieren und das Ergebnis anzuzeigen.

### <a name="read-printed-text-ocr"></a>Auslesen von gedrucktem Text (OCR)

Die OCR-Funktion (Optical Character Recognition) für Maschinelles Sehen analysiert Bilder hinsichtlich gedrucktem Text. Nach Abschluss der Analyse gibt OCR ein JSON-Objekt zurück, das den Text und die Position des Textes im Bild enthält.

Um die OCR-Funktion der Tutorialanwendung abzuschließen, führen Sie die folgenden Schritte aus:

#### <a name="add-the-event-handler-code-for-the-ocr-button"></a>Hinzufügen des Ereignishandlercodes für die OCR-Schaltfläche

Die Ereignishandlermethode **ocrImageButtonActionPerformed** löscht das Formular, zeigt das in der URL angegebene Bild an und ruft dann die Methode **OcrImage** auf, um das Bild zu analysieren. Wenn **OcrImage** zurückgegeben wird, zeigt die Methode den erkannten Text im formatierten JSON-Format im Textbereich **Antwort** an.

Kopieren Sie den folgenden Code und fügen Sie ihn in die Methode **ocrImageButtonActionPerformed** ein.

> [!NOTE]
> NetBeans lässt das Einfügen in die Methodendefinitionszeile (```private void```) oder in die abschließende geschweifte Klammer dieser Methode nicht zu. Um den Code zu kopieren, kopieren Sie die Zeilen zwischen der Methodendefinition und der abschließenden geschweiften Klammer, und fügen Sie sie über den Inhalt der Methode ein.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

Die Methode **OcrImage** dient als Wrapper für den REST-API-Aufruf zur Analyse von Bildern. Die Methode gibt ein **JSONObject**-Element der vom Aufruf zurückgegebenen JSON-Daten zurück, oder **NULL**, wenn ein Fehler aufgetreten ist.

Kopieren Sie die folgende Methode **OcrImage** und fügen Sie sie direkt unter der Methode **ocrImageButtonActionPerformed** ein.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-ocr-function"></a>Ausführen der OCR-Funktion

Drücken Sie **F6**, um die Anwendung auszuführen. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Klicken Sie auf die Registerkarte **OCR**, geben Sie eine URL zu einem Bild von gedrucktem Text ein, und klicken Sie dann auf die Schaltfläche **Bild lesen**, um ein Bild zu analysieren und das Ergebnis anzuzeigen.

### <a name="read-handwritten-text-handwriting-recognition"></a>Lesen von handgeschriebenem Text (Schrifterkennung)

Die Funktion „Schrifterkennung“ für Maschinelles Sehen analysiert Bilder hinsichtlich handgeschriebenem Text. Nach Abschluss der Analyse gibt die Schrifterkennung ein JSON-Objekt zurück, das den Text und die Position des Textes im Bild enthält.

Um die Funktion der Schrifterkennung der Tutorialanwendung abzuschließen, führen Sie die folgenden Schritte aus:

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>Hinzufügen des Ereignishandlercodes für die Schrifterkennungsschaltfläche

Die Ereignishandlermethode **handwritingImageButtonActionPerformed** löscht das Formular, zeigt das in der URL angegebene Bild an und ruft dann die Methode **HandwritingImage** auf, um das Bild zu analysieren. Wenn **HandwritingImage** zurückgegeben wird, zeigt die Methode den erkannten Text im formatierten JSON-Format im Textbereich **Antwort** an.

Kopieren Sie den folgenden Code und fügen Sie ihn in die Methode **handwritingImageButtonActionPerformed** ein.

> [!NOTE]
> NetBeans lässt das Einfügen in die Methodendefinitionszeile (```private void```) oder in die abschließende geschweifte Klammer dieser Methode nicht zu. Um den Code zu kopieren, kopieren Sie die Zeilen zwischen der Methodendefinition und der abschließenden geschweiften Klammer, und fügen Sie sie über den Inhalt der Methode ein.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

Die Methode **HandwritingImage** dient als Wrapper für die beiden REST-API-Aufrufe zur Analyse von Bildern. Da die Schrifterkennung viel Zeit in Anspruch nimmt, wird ein zweistufiger Prozess verwendet. Beim ersten Aufruf wird das Bild zur Verarbeitung gesendet; der zweite Aufruf ruft den erkannten Text ab, wenn die Bearbeitung abgeschlossen ist.

Nachdem der Text abgerufen wurde, gibt die Methode **HandwritingImage** ein **JSONObject**-Element zurück, das den Text und die Textposition beschreibt, oder **NULL**, wenn ein Fehler aufgetreten ist.

Kopieren Sie die folgende Methode **HandwritingImage** und fügen Sie sie direkt unter der Methode **handwritingImageButtonActionPerformed** ein.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occurred. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-handwriting-function"></a>Ausführen der Schrifterkennungsfunktion

Drücken Sie **F6**, um die Anwendung auszuführen. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Klicken Sie auf die Registerkarte **Handgeschriebenen Text lesen**, geben Sie eine URL zu einem Bild von handgeschriebenem Text ein, und klicken Sie dann auf die Schaltfläche **Bild lesen**, um ein Bild zu analysieren und das Ergebnis anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie die Maschinelles Sehen-REST-API mit Java verwendet, um viele der verfügbaren Features zur Bildanalyse zu testen. In der Referenzdokumentation finden Sie weitere Informationen zu den beteiligten APIs.

- [Maschinelles Sehen-REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
