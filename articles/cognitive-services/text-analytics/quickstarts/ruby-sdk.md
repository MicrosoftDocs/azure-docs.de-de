---
title: 'Schnellstart: Aufrufen der Textanalyse von Cognitive Services mit dem Ruby SDK'
titleSuffix: Azure Cognitive Services
description: Informationen und Codebeispiele für die ersten Schritte mit der Textanalyse-API in Azure Cognitive Services
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: tasharm
ms.openlocfilehash: 688887826fa803b616ca737bc8558aa17ed80e37
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297770"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>Schnellstart: Aufrufen des Textanalysediensts mit dem Ruby SDK

<a name="HOLTop"></a>


Verwenden Sie diesen Schnellstart, um mit dem Textanalyse-SDK für Ruby in die Sprachanalyse einzusteigen. Die [Textanalyse](//go.microsoft.com/fwlink/?LinkID=759711)-REST-API ist zwar mit den meisten Programmiersprachen kompatibel, das SDK bietet aber eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb).

Die technische Dokumentation für die APIs finden Sie in den [API-Definitionen](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Voraussetzungen

* [Ruby 2.5.5 oder höher](https://www.ruby-lang.org/)
* Textanalyse-[SDK für Ruby](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
 
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Außerdem benötigen Sie den [Endpunkt und den Zugriffsschlüssel](../How-tos/text-analytics-how-to-access-key.md) – beide wurden bei der Registrierung für Sie generiert. 

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>Erstellen eines Ruby-Projekts und Installieren des SDK

1. Erstellen Sie ein Ruby-Projekt, und fügen Sie eine neue Datei mit dem Namen `Gemfile` hinzu.
2. Fügen Sie dem Projekt das Textanalyse-SDK hinzu, indem Sie in `Gemfile` folgenden Code einfügen.

    ```ruby
    source 'https://rubygems.org'
    gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
    ```

## <a name="create-a-text-analytics-client"></a>Erstellen eines Textanalyseclients

1. Erstellen Sie in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE eine Datei mit dem Namen `TextAnalyticsExamples.rb`. Importieren Sie das Textanalyse-SDK.

2. Vom Textanalyseclient wird ein Anmeldeinformationsobjekt verwendet. Erstellen Sie dieses mit `CognitiveServicesCredentials.new()`, und übergeben Sie Ihren Abonnementschlüssel.

3. Erstellen Sie den Client mit dem richtigen Textanalyse-Endpunkt.

    ```ruby
    require 'azure_cognitiveservices_textanalytics'
    
    include Azure::CognitiveServices::TextAnalytics::V2_1::Models
    
    credentials =
        MsRestAzure::CognitiveServicesCredentials.new("enter key here")
    # Replace 'westus' with the correct region for your Text Analytics subscription
    endpoint = String.new("https://westus.api.cognitive.microsoft.com/")
    
    textAnalyticsClient =
        Azure::TextAnalytics::Profiles::Latest::Client.new({
            credentials: credentials
        })
    textAnalyticsClient.endpoint = endpoint
    ```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Stimmungsanalyse

Sie können Textanalyse-SDK oder -API für die Standpunktanalyse über einen Satz von Textdatensätzen verwenden. Das folgende Beispiel zeigt die Stimmungsbewertungen für mehrere Dokumente.

1. Erstellen Sie eine neue Funktion mit dem Namen `SentimentAnalysisExample()`, die den Textanalyseclient von oben als Parameter annimmt.

2. Definieren Sie einen Satz von `MultiLanguageInput`-Objekten für die Analyse. Fügen Sie für jedes Objekt eine Sprache und Text hinzu. Die ID kann ein beliebiger Wert sein.

    ```ruby
    def SentimentAnalysisExample(client)
      # The documents to be analyzed. Add the language of the document. The ID can be any value.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'en'
      input_1.text = 'I had the best day of my life.'
    
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'en'
      input_2.text = 'This was a waste of my time. The speaker put me to sleep.'
    
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'es'
      input_3.text = 'No tengo dinero ni nada que dar...'
    
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'it'
      input_4.text = "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    ```

3. Kombinieren Sie innerhalb der gleichen Funktion die Dokumente in einer Liste. Fügen Sie diese dem Feld `documents` eines `MultiLanguageBatchInput`-Objekts hinzu. 

4. Rufen Sie die `sentiment()`-Funktion des Clients mit dem `MultiLanguageBatchInput`-Objekt als Parameter auf, um die Dokumente zu senden. Wenn Ergebnisse zurückgegeben werden, geben Sie sie aus.
    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.sentiment(
          multi_language_batch_input: input_documents
      )
      
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== SENTIMENT ANALYSIS ====='
        result.documents.each do |document|
          puts "Document Id: #{document.id}: Sentiment Score: #{document.score}"
        end
      end
    end
    ```

5. Rufen Sie die Funktion `SentimentAnalysisExample()` auf.

    ```ruby
    SentimentAnalysisExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Spracherkennung

Der Textanalysedienst kann die Sprache jedes Textdokuments für eine große Anzahl von Sprachen und Gebietsschemas erkennen. Das folgende Beispiel zeigt die Sprache, in der mehrere Dokumente geschrieben wurden.

1. Erstellen Sie eine neue Funktion mit dem Namen `DetectLanguageExample()`, die den Textanalyseclient von oben als Parameter annimmt. 

2. Definieren Sie einen Satz von `LanguageInput`-Objekten für die Analyse. Fügen Sie für jedes Objekt eine Sprache und Text hinzu. Die ID kann ein beliebiger Wert sein.

    ```ruby
    def DetectLanguageExample(client)
       # The documents to be analyzed.
       language_input_1 = LanguageInput.new
       language_input_1.id = '1'
       language_input_1.text = 'This is a document written in English.'
    
       language_input_2 = LanguageInput.new
       language_input_2.id = '2'
       language_input_2.text = 'Este es un document escrito en Español..'
    
       language_input_3 = LanguageInput.new
       language_input_3.id = '3'
       language_input_3.text = '这是一个用中文写的文件'
    ```

3. Kombinieren Sie innerhalb der gleichen Funktion die Dokumente in einer Liste. Fügen Sie diese dem Feld `documents` eines `LanguageBatchInput`-Objekts hinzu. 

4. Rufen Sie die `detect_language()`-Funktion des Clients mit dem `LanguageBatchInput`-Objekt als Parameter auf, um die Dokumente zu senden. Wenn Ergebnisse zurückgegeben werden, geben Sie sie aus.
    ```ruby
       input_documents = LanguageBatchInput.new
       input_documents.documents = [language_input_1, language_input_2, language_input_3]
    
    
       result = client.detect_language(
           language_batch_input: input_documents
       )
    
       if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
         puts '===== LANGUAGE DETECTION ====='
         result.documents.each do |document|
           puts "Document ID: #{document.id} , Language: #{document.detected_languages[0].name}"
         end
       else
         puts 'No results data..'
       end
     end
    ```

5. Rufen Sie die `DetectLanguageExample`-Funktion auf.

    ```ruby
    DetectLanguageExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Entitätserkennung

Der Textanalysedienst kann Entitäten (Personen, Orte und Gegenstände) in Textdokumenten unterscheiden und extrahieren. Das folgende Beispiel zeigt die Entitäten, die in mehreren Beispieldokumenten gefunden wurden.

1. Erstellen Sie eine neue Funktion mit dem Namen `Recognize_Entities()`, die den Textanalyseclient von oben als Parameter annimmt.

2. Definieren Sie einen Satz von `MultiLanguageInput`-Objekten für die Analyse. Fügen Sie für jedes Objekt eine Sprache und Text hinzu. Die ID kann ein beliebiger Wert sein.

    ```ruby
      def RecognizeEntitiesExample(client)
        # The documents to be analyzed.
        input_1 = MultiLanguageInput.new
        input_1.id = '1'
        input_1.language = 'en'
        input_1.text = 'Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.'
    
        input_2 = MultiLanguageInput.new
        input_2.id = '2'
        input_2.language = 'es'
        input_2.text = 'La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.'
    ```

3. Kombinieren Sie innerhalb der gleichen Funktion die Dokumente in einer Liste. Fügen Sie diese dem Feld `documents` eines `MultiLanguageBatchInput`-Objekts hinzu. 

4. Rufen Sie die `entities()`-Funktion des Clients mit dem `MultiLanguageBatchInput`-Objekt als Parameter auf, um die Dokumente zu senden. Wenn Ergebnisse zurückgegeben werden, geben Sie sie aus.

    ```ruby
        input_documents =  MultiLanguageBatchInput.new
        input_documents.documents = [input_1, input_2]
    
        result = client.entities(
            multi_language_batch_input: input_documents
        )
    
        if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
          puts '===== ENTITY RECOGNITION ====='
          result.documents.each do |document|
            puts "Document ID: #{document.id}"
              document.entities.each do |entity|
                puts "\tName: #{entity.name}, \tType: #{entity.type == nil ? "N/A": entity.type},\tSub-Type: #{entity.sub_type == nil ? "N/A": entity.sub_type}"
                entity.matches.each do |match|
                  puts "\tOffset: #{match.offset}, \Length: #{match.length},\tScore: #{match.entity_type_score}"
                end
                puts
              end
          end
        else
          puts 'No results data..'
        end
      end
    ```

5. Rufen Sie die `RecognizeEntitiesExample`-Funktion auf.
    ```ruby
    RecognizeEntitiesExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Schlüsselwortextraktion

Der Textanalysedienst kann Schlüsselausdrücke in Sätzen extrahieren. Das folgende Beispiel zeigt die Entitäten, die in mehreren Beispieldokumenten und mehreren Sprachen gefunden wurden.

1. Erstellen Sie eine neue Funktion mit dem Namen `KeyPhraseExtractionExample()`, die den Textanalyseclient von oben als Parameter annimmt.

2. Definieren Sie einen Satz von `MultiLanguageInput`-Objekten für die Analyse. Fügen Sie für jedes Objekt eine Sprache und Text hinzu. Die ID kann ein beliebiger Wert sein.

    ```ruby
    def KeyPhraseExtractionExample(client)
      # The documents to be analyzed.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'ja'
      input_1.text = '猫は幸せ'
  
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'de'
      input_2.text = 'Fahrt nach Stuttgart und dann zum Hotel zu Fu.'
  
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'en'
      input_3.text = 'My cat is stiff as a rock.'
  
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'es'
      input_4.text = 'A mi me encanta el fútbol!'
      ```

3. Kombinieren Sie innerhalb der gleichen Funktion die Dokumente in einer Liste. Fügen Sie diese dem Feld `documents` eines `MultiLanguageBatchInput`-Objekts hinzu. 

4. Rufen Sie die `key_phrases()`-Funktion des Clients mit dem `MultiLanguageBatchInput`-Objekt als Parameter auf, um die Dokumente zu senden. Wenn Ergebnisse zurückgegeben werden, geben Sie sie aus.

    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.key_phrases(
          multi_language_batch_input: input_documents
      )
    
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        result.documents.each do |document|
          puts "Document Id: #{document.id}"
          puts '  Key Phrases'
          document.key_phrases.each do |key_phrase|
            puts "    #{key_phrase}"
          end
        end
      else
        puts 'No results data..'
      end
    end
    ```

5. Rufen Sie die `KeyPhraseExtractionExample`-Funktion auf.

    ```ruby
    KeyPhraseExtractionExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Textanalyse mit Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Weitere Informationen

 [Übersicht über die Textanalyse](../overview.md)  
 [Häufig gestellte Fragen (FAQ)](../text-analytics-resource-faq.md)
