---
title: 'Tutorial: Erstellen einer Flask-App zum Übersetzen, Synthetisieren und Analysieren von Text: Translator'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial erstellen Sie eine Flask-basierte Web-App, um Text zu übersetzen, Stimmungen zu analysieren und die Synthese von übersetztem Text in Sprache durchzuführen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: 1cbe4d44f5e1c8b34a3d7bb9d05b9546f320b81c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023467"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Tutorial: Erstellen einer Flask-App mit Azure Cognitive Services

In diesem Tutorial erstellen Sie eine Flask-Web-App, für die Azure Cognitive Services genutzt wird, um Text zu übersetzen, Stimmungen (auch als „Standpunkt“ bezeichnet) zu analysieren und für übersetzten Text die Sprachsynthese durchzuführen. Der Schwerpunkt liegt auf dem Python-Code und den Flask-Routen als Grundlage unserer Anwendung. Wir geben als Hilfe für Sie aber den HTML- und JavaScript-Code an, auf dem unsere App basiert. Falls bei Ihnen Probleme auftreten, können Sie uns über die Schaltfläche „Feedback“ am Ende dieses Artikels darüber informieren.

Hier ist angegeben, was in diesem Tutorial vermittelt wird:

> [!div class="checklist"]
> * Abrufen von Azure-Abonnementschlüsseln
> * Einrichten Ihrer Entwicklungsumgebung und Installieren von Abhängigkeiten
> * Erstellen einer Flask-App
> * Verwenden von Translator zum Übersetzen von Text
> * Verwenden der Textanalyse zum Analysieren von positiven/negativen Stimmungen für Eingabetext und Übersetzungen
> * Verwenden der Spracherkennungsdienste zum Konvertieren von übersetztem Text in synthetisierte Sprache
> * Lokales Ausführen Ihrer Flask-App

> [!TIP]
> Sie können auch weiterspringen, um den gesamten Code auf einmal anzuzeigen. Das gesamte Beispiel und die Anleitung zur Erstellung finden Sie auf [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Was ist Flask?

Flask ist ein Microframework zum Erstellen von Webanwendungen. Dies bedeutet, dass Sie mit Flask über Tools, Bibliotheken und Technologien verfügen, mit denen Sie eine Webanwendung erstellen können. Diese Webanwendung kann einige Webseiten, ein Blog, ein Wiki oder auch mehr umfassen, z. B. eine webbasierte Kalenderanwendung oder eine kommerzielle Website.

Hier sind einige hilfreiche Links angegeben, falls Sie sich nach Abschluss dieses Tutorials eingehender informieren möchten:

* [Flask-Dokumentation](http://flask.pocoo.org/)
* [Flask for Dummies – A Beginner's Guide to Flask](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1) (Flask-Leitfaden für Einsteiger)

## <a name="prerequisites"></a>Voraussetzungen

Hier sind die Software und Abonnementschlüssel angegeben, die Sie für dieses Tutorial benötigen.

* [Python 3.5.2 oder höher](https://www.python.org/downloads/)
* [Git-Tools](https://git-scm.com/downloads)
* Eine IDE oder ein Text-Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/) oder [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) oder [Firefox](https://www.mozilla.org/firefox)
* Ein Abonnementschlüssel für **Translator**  (Hinweis: Sie müssen keine Region auswählen.)
* Ein Abonnementschlüssel für die **Textanalyse** in der Region **USA, Westen**.
* Ein Abonnementschlüssel für **Spracherkennungsdienste** in der Region **USA, Westen**.

## <a name="create-an-account-and-subscribe-to-resources"></a>Erstellen eines Kontos und Abonnieren der Ressourcen

Wie bereits erwähnt, benötigen Sie für dieses Tutorial drei Abonnementschlüssel. Dies bedeutet, dass Sie unter Ihrem Azure-Konto Ressourcen für folgende Komponenten erstellen müssen:
* Übersetzer
* Textanalyse
* Spracherkennungsdienste

Unter [Schnellstart: Erstellen eines Cognitive Services-Kontos im Azure-Portal](../cognitive-services-apis-create-account.md) finden Sie eine Schritt-für-Schritt-Anleitung zur Erstellung von Ressourcen.

> [!IMPORTANT]
> In diesem Tutorial erstellen Sie Ihre Ressourcen in der Region „USA, Westen“. Bei Verwendung einer anderen Region müssen Sie die Basis-URL in Ihren gesamten Python-Dateien anpassen.

## <a name="set-up-your-dev-environment"></a>Einrichten Ihrer Entwicklungsumgebung

Vor dem Erstellen Ihrer Flask-Web-App müssen Sie ein Arbeitsverzeichnis für Ihr Projekt erstellen und einige Python-Pakete installieren.

### <a name="create-a-working-directory"></a>Erstellen eines Arbeitsverzeichnisses

1. Öffnen Sie die Befehlszeile (Windows) oder ein Terminal (macOS/Linux). Erstellen Sie anschließend ein Arbeitsverzeichnis und Unterverzeichnisse für Ihr Projekt:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Wechseln Sie in das Arbeitsverzeichnis Ihres Projekts:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Erstellen und Aktivieren Ihrer virtuellen Umgebung mit `virtualenv`

Wir erstellen mit `virtualenv` eine virtuelle Umgebung für unsere Flask-App. Durch die Verwendung einer virtuellen Umgebung wird sichergestellt, dass Sie in einer bereinigten Umgebung arbeiten können.

1. Führen Sie in Ihrem Arbeitsverzeichnis diesen Befehl aus, um eine virtuelle Umgebung zu erstellen: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Wir haben explizit deklariert, dass für die virtuelle Umgebung Python 3 verwendet werden soll. Hierdurch wird sichergestellt, dass Benutzer mit mehreren Python-Installationen die richtige Version nutzen.

   **Windows CMD/Windows Bash:**
   ```
   virtualenv venv
   ```
   Der Einfachheit halber nennen wir Ihre virtuelle Umgebung „venv“.

2. Die Befehle zum Aktivieren Ihrer virtuellen Umgebung variieren je nach Plattform/Shell:   

   | Plattform | Shell | Get-Help |
   |----------|-------|---------|
   | macOS/Linux | Bash/zsh | `source venv/bin/activate` |
   | Windows | Bash | `source venv/Scripts/activate` |
   | | Befehlszeile | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Nach Ausführung dieses Befehls sollte in der Befehlszeile bzw. Terminalsitzung der Name `venv` als Voranstellung zu sehen sein.

3. Sie können die Sitzung jederzeit deaktivieren, indem Sie in der Befehlszeile bzw. im Terminal Folgendes eingeben: `deactivate`.

> [!NOTE]
> Für Python ist eine ausführliche Dokumentation zur Erstellung und Verwaltung von virtuellen Umgebungen vorhanden (siehe [virtualenv](https://virtualenv.pypa.io/en/latest/)).

### <a name="install-requests"></a>Installieren von „Requests“

„Requests“ ist ein beliebtes Modul, das zum Senden von HTTP 1.1-Anforderungen verwendet wird. Es ist nicht erforderlich, Ihren URLs manuell Abfragezeichenfolgen hinzuzufügen oder Ihre POST-Daten mit einer Formularcodierung zu versehen.

1. Führen Sie Folgendes aus, um „Requests“ zu installieren:

   ```
   pip install requests
   ```

> [!NOTE]
> Weitere Informationen zu „Requests“ finden Sie unter [Requests: HTTP for Humans](https://2.python-requests.org/en/master/) (Requests: HTTP für Menschen).

### <a name="install-and-configure-flask"></a>Installieren und Konfigurieren von Flask

Als Nächstes müssen wir Flask installieren. Flask führt die Verarbeitung des Routings für unsere Web-App durch und ermöglicht die Durchführung von Aufrufen von Server zu Server, bei denen unsere Abonnementschlüssel vor dem Endbenutzer verborgen werden.

1. Führen Sie Folgendes aus, um Flask zu installieren:
   ```
   pip install Flask
   ```
   Wir stellen nun sicher, dass Flask installiert wurde. Führen Sie folgenden Befehl aus:
   ```
   flask --version
   ```
   Die Version sollte im Terminal ausgegeben werden. Alle anderen Ergebnisse bedeuten, dass ein Fehler vorliegt.

2. Zum Ausführen der Flask-App können Sie entweder den Befehl „flask“ oder die Python-Option „-m“ mit Flask verwenden. Zuvor müssen Sie Ihrem Terminal mitteilen, mit welcher App gearbeitet werden soll, indem Sie die Umgebungsvariable `FLASK_APP` exportieren:

   **macOS/Linux:**
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Erstellen Ihrer Flask-App

In diesem Abschnitt erstellen Sie eine abgespeckte Flask-App, mit der eine HTML-Datei zurückgegeben wird, wenn Benutzer auf den Stamm Ihrer App auswählen. Wenden Sie nicht zu viel Zeit für das Auseinandernehmen des Codes auf. Wir führen zu einem späteren Zeitpunkt noch eine Aktualisierung dieser Datei durch.

### <a name="what-is-a-flask-route"></a>Was ist eine Flask-Route?

Nun geht es kurz um „[Routen](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)“. Das Routing wird verwendet, um eine URL an eine spezifische Funktion zu binden. Flask nutzt Decorator-Elemente für Routen, um Funktionen für spezifische URLs zu registrieren. Wenn ein Benutzer zum Stamm (`/`) der Web-App navigiert, wird beispielsweise `index.html` gerendert.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Wir sehen uns noch ein weiteres Beispiel an, um dies zu verdeutlichen.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Mit diesem Code wird sichergestellt, dass die Datei `about.html` gerendert wird, wenn ein Benutzer zu `http://your-web-app.com/about` navigiert.

In diesen Beispielen wird veranschaulicht, wie Sie HTML-Seiten für einen Benutzer rendern. Routen können aber auch verwendet werden, um APIs aufzurufen, wenn eine Schaltfläche betätigt wird, oder um eine beliebige Anzahl von Aktionen durchzuführen, ohne dass die Startseite verlassen werden muss. Sie können dies beobachten, wenn Sie Routen für die Übersetzung, Standpunktanalyse und Sprachsynthese erstellen.

### <a name="get-started"></a>Erste Schritte

1. Öffnen Sie das Projekt in Ihrer IDE, und erstellen Sie anschließend im Stamm Ihres Arbeitsverzeichnisses eine Datei mit dem Namen `app.py`. Kopieren Sie als Nächstes diesen Code in `app.py`, und speichern Sie:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Mit diesem Codeblock wird die App angewiesen, jeweils `index.html` anzuzeigen, wenn ein Benutzer zum Stamm Ihrer Web-App (`/`) navigiert.

2. Als Nächstes erstellen wir das Front-End für unsere Web-App. Erstellen Sie im Verzeichnis `templates` eine Datei mit dem Namen `index.html`. Kopieren Sie diesen Code dann in `templates/index.html`.

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Wir testen die Flask-App jetzt. Führen Sie im Terminal Folgendes aus:

   ```
   flask run
   ```

4. Öffnen Sie einen Browser, und navigieren Sie zur angegebenen URL. Ihre Single-Page-App sollte angezeigt werden. Drücken Sie **STRG+C**, um die App zu beenden.

## <a name="translate-text"></a>Text übersetzen

Gehen Sie nun, nachdem Sie sich mit der Funktionsweise einer einfachen Flask-App vertraut gemacht haben, wie folgt vor:

* Schreiben von Python-Code zum Aufrufen von Translator und Zurückgeben einer Antwort
* Erstellen einer Flask-Route zum Aufrufen Ihres Python-Codes
* Aktualisieren des HTML-Codes mit einem Bereich für die Texteingabe und -übersetzung, einer Sprachauswahl und der Schaltfläche „Translate“ (Übersetzen)
* Schreiben von JavaScript, um Benutzern die Interaktion mit Ihrer Flask-App über den HTML-Code zu ermöglichen

### <a name="call-the-translator"></a>Aufrufen von Translator

Zunächst müssen Sie eine Funktion zum Aufrufen von Translator schreiben. Für diese Funktion werden zwei Argumente verwendet: `text_input` und `language_output`. Diese Funktion wird jeweils aufgerufen, wenn ein Benutzer in Ihrer App die Schaltfläche „Übersetzen“ betätigt. Der Textbereich im HTML-Code wird als `text_input` und der Wert für die Sprachauswahl im HTML-Code als `language_output` gesendet.

1. Zunächst erstellen wir im Stammverzeichnis Ihres Arbeitsverzeichnisses eine Datei mit dem Namen `translate.py`.
2. Fügen Sie als Nächstes der Datei `translate.py` diesen Code hinzu. Für diese Funktion werden zwei Argumente verwendet: `text_input` und `language_output`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': 'location',
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Fügen Sie Ihren Abonnementschlüssel für Translator hinzu, und speichern Sie ihn.

### <a name="add-a-route-to-apppy"></a>Hinzufügen einer Route zu `app.py`

Als Nächstes müssen Sie in Ihrer Flask-App eine Route erstellen, mit der `translate.py` aufgerufen wird. Diese Route wird jeweils aufgerufen, wenn ein Benutzer in Ihrer App die Schaltfläche „Translate“ (Übersetzen) betätigt.

Für diese App akzeptiert Ihre Route `POST`-Anforderungen. Der Grund ist, dass die Funktion den zu übersetzenden Text und eine Ausgabesprache für die Übersetzung erwartet.

Flask verfügt über Hilfsfunktionen zum Analysieren und Verwalten der einzelnen Anforderungen. Im angegebenen Code werden mit `get_json()` die Daten aus den `POST`-Anforderungen im JSON-Format zurückgegeben. Mit `data['text']` und `data['to']` werden die Werte für den Text und die Ausgabesprache dann an die Funktion `get_translation()` übergeben, die über `translate.py` verfügbar ist. Im letzten Schritt wird die Antwort im JSON-Format zurückgegeben, da Sie diese Daten in Ihrer Web-App anzeigen müssen.

In den folgenden Abschnitten wiederholen Sie diesen Prozess, wenn Sie Routen für die Standpunktanalyse und Sprachsynthese erstellen.

1. Öffnen Sie `app.py`, und suchen Sie oben in `app.py` nach der Importanweisung. Fügen Sie die folgende Zeile hinzu:

   ```python
   import translate
   ```
   Jetzt kann für unsere Flask-App die Methode verwendet werden, die über `translate.py` verfügbar ist.

2. Kopieren Sie diesen Code an das Ende von `app.py`, und speichern Sie:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aktualisieren von `index.html`

Nachdem Sie nun über eine Funktion zum Übersetzen von Text und in Ihrer Flask-App über eine Route zum Aufrufen verfügen, besteht der nächste Schritt darin, mit der Erstellung des HTML-Codes für Ihre App zu beginnen. Der hier angegebene HTML-Code bewirkt Folgendes:

* Bereitstellen eines Textbereichs, in dem Benutzer zu übersetzenden Text eingeben können
* Bereitstellen einer Sprachauswahl
* Bereitstellen von HTML-Elementen zum Rendern der erkannten Sprach- und Zuverlässigkeitsbewertungen, die während der Übersetzung zurückgegeben werden
* Bereitstellen eines schreibgeschützten Textbereichs, in dem die Übersetzungsausgabe angezeigt wird
* Bereitstellen von Platzhaltern für den Code für die Standpunktanalyse und Sprachsynthese, den Sie dieser Datei zu einem späteren Zeitpunkt des Tutorials hinzufügen

Wir aktualisieren nun `index.html`.

1. Öffnen Sie `index.html`, und suchen Sie nach diesen Codekommentaren:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Ersetzen Sie die Codekommentare durch diesen HTML-Block:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

Im nächsten Schritt wird JavaScript-Code geschrieben. Dies ist die Brücke zwischen Ihrer HTML- und Flask-Route.

### <a name="create-mainjs"></a>Erstellen Sie `main.js`.  

Die Datei `main.js` ist die Brücke zwischen Ihrer HTML- und Flask-Route. Für Ihre App wird eine Kombination aus jQuery, Ajax und XMLHttpRequest verwendet, um Inhalt zu rendern und `POST`-Anforderungen an Ihre Flask-Routen zu senden.

Im unten angegebenen Code wird Inhalt aus dem HTML-Code verwendet, um eine Anforderung für Ihre Flask-Route zu erstellen. Genauer gesagt: Der Inhalt des Textbereichs und der Sprachauswahl wird Variablen zugewiesen und dann in der Anforderung an `translate-text` übergeben.

Der Code durchläuft anschließend die Antwort und aktualisiert den HTML-Code mit der Übersetzung, der erkannten Sprache und der Zuverlässigkeitsbewertung.

1. Erstellen Sie in Ihrer IDE im Verzeichnis `static/scripts` eine Datei mit dem Namen `main.js`.
2. Kopieren Sie diesen Code in `static/scripts/main.js`:
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Testübersetzung

Wir testen nun die Übersetzung in der App.

```
flask run
```

Navigieren Sie zur angegebenen Serveradresse. Geben Sie Text in den Eingabebereich ein, wählen Sie eine Sprache aus, und betätigen Sie die Schaltfläche „Translate“ (Übersetzen). Sie sollten eine Übersetzung erhalten. Falls dies nicht funktioniert, sollten Sie sicherstellen, dass Sie Ihren Abonnementschlüssel hinzugefügt haben.

> [!TIP]
> Wenn die vorgenommenen Änderungen nicht angezeigt werden oder die App nicht wie erwartet funktioniert, sollten Sie versuchen, Ihren Cache zu löschen oder ein InPrivate- bzw. Inkognito-Fenster zu öffnen.

Drücken Sie **STRG+C**, um die App zu beenden, und fahren Sie dann mit dem nächsten Abschnitt fort.

## <a name="analyze-sentiment"></a>Analysieren von Stimmungen

Die [Textanalyse-API](../text-analytics/overview.md) kann genutzt werden, um die Standpunktanalyse durchzuführen, Schlüsselbegriffe aus Text zu extrahieren oder die Quellsprache zu erkennen. In dieser App nutzen wir die Standpunktanalyse, um zu ermitteln, ob der angegebene Text positiv, neutral oder negativ ist. Die API gibt eine numerische Bewertung zwischen 0 und 1 zurück. Dabei weisen Werte nahe 1 auf eine positive Stimmung und Werte nahe 0 auf eine negative Stimmung hin.

In diesem Abschnitt führen Sie einige Schritte aus:

* Schreiben von Python-Code zum Aufrufen der Textanalyse-API, um eine Standpunktanalyse durchzuführen und eine Antwort zurückzugeben
* Erstellen einer Flask-Route zum Aufrufen Ihres Python-Codes
* Aktualisieren des HTML-Codes mit einem Bereich für Stimmungswerte und einer Schaltfläche zum Durchführen der Analyse
* Schreiben von JavaScript, um Benutzern die Interaktion mit Ihrer Flask-App über den HTML-Code zu ermöglichen

### <a name="call-the-text-analytics-api"></a>Aufrufen der Textanalyse-API

Wir schreiben nun eine Funktion zum Aufrufen der Textanalyse-API. Für diese Funktion werden vier Argumente verwendet: `input_text`, `input_language`, `output_text` und `output_language`. Diese Funktion wird jeweils aufgerufen, wenn ein Benutzer in Ihrer App die Schaltfläche für die Standpunktanalyse betätigt. Vom Benutzer bereitgestellte Daten aus dem Textbereich und der Sprachauswahl sowie die erkannte Sprache und die Übersetzungsausgabe werden mit jeder Anforderung angegeben. Das Antwortobjekt enthält Stimmungswerte für den Quelltext und die Übersetzung. In den folgenden Abschnitten schreiben Sie JavaScript-Code zum Analysieren der Antwort und Verwenden in Ihrer App. Zunächst geht es um das Aufrufen der Textanalyse-API.

1. Wir erstellen im Stammverzeichnis Ihres Arbeitsverzeichnisses eine Datei mit dem Namen `sentiment.py`.
2. Fügen Sie als Nächstes der Datei `sentiment.py` diesen Code hinzu.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Fügen Sie Ihren Abonnementschlüssel für die Textanalyse hinzu, und speichern Sie.

### <a name="add-a-route-to-apppy"></a>Hinzufügen einer Route zu `app.py`

Wir erstellen eine Route in Ihrer Flask-App, für die `sentiment.py` aufgerufen wird. Diese Route wird jedes Mal aufgerufen, wenn ein Benutzer in Ihrer App die Schaltfläche für die Standpunktanalyse betätigt. Wie auch bei der Route für die Übersetzung, akzeptiert diese Route `POST`-Anforderungen, da die Funktion Argumente erwartet.

1. Öffnen Sie `app.py`, und suchen Sie oben in der Datei `app.py` nach der Importanweisung. Aktualisieren Sie sie:

   ```python
   import translate, sentiment
   ```
   Jetzt kann für unsere Flask-App die Methode verwendet werden, die über `sentiment.py` verfügbar ist.

2. Kopieren Sie diesen Code an das Ende von `app.py`, und speichern Sie:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aktualisieren von `index.html`

Nachdem Sie nun über eine Funktion zum Ausführen der Standpunktanalyse und in Ihrer Flask-App über eine Route zum Aufrufen verfügen, besteht der nächste Schritt darin, mit dem Schreiben des HTML-Codes für Ihre App zu beginnen. Der hier angegebene HTML-Code bewirkt Folgendes:

* Hinzufügen einer Schaltfläche zu Ihrer App zum Ausführen der Standpunktanalyse
* Hinzufügen eines Elements zur Beschreibung der Stimmungsbewertung
* Hinzufügen eines Elements zum Anzeigen von Stimmungswerten

1. Öffnen Sie `index.html`, und suchen Sie nach diesen Codekommentaren:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Ersetzen Sie die Codekommentare durch diesen HTML-Block:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Aktualisieren von `main.js`

Im unten angegebenen Code wird Inhalt aus dem HTML-Code verwendet, um eine Anforderung für Ihre Flask-Route zu erstellen. Genauer gesagt: Der Inhalt des Textbereichs und der Sprachauswahl wird Variablen zugewiesen und dann in der Anforderung an die Route `sentiment-analysis` übergeben.

Der Code durchläuft dann die Antwort und aktualisiert den HTML-Code mit den Stimmungswerten.

1. Erstellen Sie in Ihrer IDE im Verzeichnis `static` eine Datei mit dem Namen `main.js`.

2. Kopieren Sie diesen Code in `static/scripts/main.js`:
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Test der Standpunktanalyse

Wir testen die Standpunktanalyse nun in der App.

```
flask run
```

Navigieren Sie zur angegebenen Serveradresse. Geben Sie Text in den Eingabebereich ein, wählen Sie eine Sprache aus, und betätigen Sie die Schaltfläche „Translate“ (Übersetzen). Sie sollten eine Übersetzung erhalten. Betätigen Sie als Nächstes die Schaltfläche für die Standpunktanalyse. Es sollten zwei Werte angezeigt werden. Falls dies nicht funktioniert, sollten Sie sicherstellen, dass Sie Ihren Abonnementschlüssel hinzugefügt haben.

> [!TIP]
> Wenn die vorgenommenen Änderungen nicht angezeigt werden oder die App nicht wie erwartet funktioniert, sollten Sie versuchen, Ihren Cache zu löschen oder ein InPrivate- bzw. Inkognito-Fenster zu öffnen.

Drücken Sie **STRG+C**, um die App zu beenden, und fahren Sie dann mit dem nächsten Abschnitt fort.

## <a name="convert-text-to-speech"></a>Konvertieren von Text in Sprache

Mit der [Text-to-Speech-API](../speech-service/text-to-speech.md) kann Ihre App Text in synthetisierte Sprache konvertieren, die natürlich und menschenähnlich ist. Der Dienst unterstützt standardmäßige, neuronale und benutzerdefinierte Stimmen. In unserer Beispiel-App werden einige der verfügbaren Stimmen genutzt. Eine vollständige Liste finden Sie unter [Unterstützte Sprachen](../speech-service/language-support.md#text-to-speech).

In diesem Abschnitt führen Sie einige Schritte aus:

* Schreiben von Python-Code, um mit der Text-to-Speech-API Text in Sprache zu konvertieren
* Erstellen einer Flask-Route zum Aufrufen Ihres Python-Codes
* Aktualisieren des HTML-Codes mit einer Schaltfläche zum Konvertieren von Text in Sprache und einem Element für die Audiowiedergabe
* Schreiben von JavaScript, um Benutzern die Interaktion mit Ihrer Flask-App zu ermöglichen

### <a name="call-the-text-to-speech-api"></a>Aufrufen der Text-to-Speech-API

Wir schreiben nun eine Funktion zum Konvertieren von Text in Sprache. Für diese Funktion werden zwei Argumente verwendet: `input_text` und `voice_font`. Diese Funktion wird jeweils aufgerufen, wenn ein Benutzer in Ihrer App die Schaltfläche zum Konvertieren von Text in Sprache betätigt. `voice_font` ist die Übersetzungsausgabe, die vom Aufruf der Textübersetzung zurückgegeben wird, und `input_text` ist der Wert aus der Voicefont-Auswahl im HTML-Code.

1. Wir erstellen im Stammverzeichnis Ihres Arbeitsverzeichnisses eine Datei mit dem Namen `synthesize.py`.

2. Fügen Sie als Nächstes der Datei `synthesize.py` diesen Code hinzu.
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Fügen Sie Ihren Abonnementschlüssel für den Spracherkennungsdienst hinzu, und speichern Sie.

### <a name="add-a-route-to-apppy"></a>Hinzufügen einer Route zu `app.py`

Wir erstellen eine Route in Ihrer Flask-App, für die `synthesize.py` aufgerufen wird. Diese Route wird jeweils aufgerufen, wenn ein Benutzer in Ihrer App die Schaltfläche zum Konvertieren von Text in Sprache betätigt. Wie auch die Routen für die Übersetzung und die Standpunktanalyse, akzeptiert diese Route `POST`-Anforderungen, weil die Funktion zwei Argumente erwartet: den Text für die Synthese und den Voicefont für die Wiedergabe.

1. Öffnen Sie `app.py`, und suchen Sie oben in der Datei `app.py` nach der Importanweisung. Aktualisieren Sie sie:

   ```python
   import translate, sentiment, synthesize
   ```
   Jetzt kann für unsere Flask-App die Methode verwendet werden, die über `synthesize.py` verfügbar ist.

2. Kopieren Sie diesen Code an das Ende von `app.py`, und speichern Sie:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>Aktualisieren von `index.html`

Nachdem Sie nun über eine Funktion zum Konvertieren von Text in Sprache und in Ihrer Flask-App über eine Route zum Aufrufen verfügen, besteht der nächste Schritt darin, mit dem Schreiben des HTML-Codes für Ihre App zu beginnen. Der hier angegebene HTML-Code bewirkt Folgendes:

* Bereitstellen einer Dropdownliste für die Auswahl
* Hinzufügen einer Schaltfläche zum Konvertieren von Text in Sprache
* Hinzufügen eines Audioelements zum Wiedergeben der synthetisierten Sprache

1. Öffnen Sie `index.html`, und suchen Sie nach diesen Codekommentaren:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Ersetzen Sie die Codekommentare durch diesen HTML-Block:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yating, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, AriaRUS)">English (US) | Female | AriaRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Heami</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Suchen Sie als Nächstes nach diesen Codekommentaren:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Ersetzen Sie die Codekommentare durch diesen HTML-Block:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Achten Sie darauf, dass Sie Ihre Änderungen speichern.

### <a name="update-mainjs"></a>Aktualisieren von `main.js`

Im unten angegebenen Code wird Inhalt aus dem HTML-Code verwendet, um eine Anforderung für Ihre Flask-Route zu erstellen. Genauer gesagt: Die Übersetzung und der Voicefont werden Variablen zugewiesen und dann in der Anforderung an die `text-to-speech`-Route übergeben.

Der Code durchläuft dann die Antwort und aktualisiert den HTML-Code mit den Stimmungswerten.

1. Erstellen Sie in Ihrer IDE im Verzeichnis `static` eine Datei mit dem Namen `main.js`.
2. Kopieren Sie diesen Code in `static/scripts/main.js`:
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. Sie sind fast fertig. Abschließend fügen Sie der Datei `main.js` Code hinzu, um basierend auf der Sprache, die für die Übersetzung ausgewählt wurde, automatisch einen Voicefont auszuwählen. Fügen Sie diesen Codeblock der Datei `main.js` hinzu:
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Testen Ihrer App

Wir testen die Sprachsynthese nun in der App.

```
flask run
```

Navigieren Sie zur angegebenen Serveradresse. Geben Sie Text in den Eingabebereich ein, wählen Sie eine Sprache aus, und betätigen Sie die Schaltfläche „Translate“ (Übersetzen). Sie sollten eine Übersetzung erhalten. Wählen Sie als Nächstes eine Stimme aus, und betätigen Sie dann die Schaltfläche zum Konvertieren von Text in Sprache. Die Übersetzung sollte als synthetisierte Sprache wiedergegeben werden. Falls dies nicht funktioniert, sollten Sie sicherstellen, dass Sie Ihren Abonnementschlüssel hinzugefügt haben.

> [!TIP]
> Wenn die vorgenommenen Änderungen nicht angezeigt werden oder die App nicht wie erwartet funktioniert, sollten Sie versuchen, Ihren Cache zu löschen oder ein InPrivate- bzw. Inkognito-Fenster zu öffnen.

Das ist alles! Sie verfügen über eine funktionierende App, mit der Übersetzungen durchgeführt, Stimmungen analysiert und Sprache synthetisiert werden kann. Drücken Sie **STRG+C**, um die App zu beenden. Sehen Sie sich auch die anderen Komponenten von [Azure Cognitive Services](../index.yml) an.

## <a name="get-the-source-code"></a>Herunterladen des Quellcodes

Der Quellcode für dieses Projekt ist auf [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial) verfügbar.

## <a name="next-steps"></a>Nächste Schritte

* [Referenz zu Translator](./reference/v3-0-reference.md)
* [Referenz zur Textanalyse-API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Referenz zur Text-to-Speech-API](../speech-service/rest-text-to-speech.md)