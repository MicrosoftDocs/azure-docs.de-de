---
title: 'Schnellstart: Aufrufen des Textanalysediensts mit dem Python SDK'
titleSuffix: Azure Cognitive Services
description: Informationen und Codebeispiele für die ersten Schritte mit der Textanalyse-API in Azure Cognitive Services
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: b319abf22f9aa4cdd9a5fef91be0628672d47bd4
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297784"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>Schnellstart: Aufrufen des Textanalysediensts mit dem Python SDK 
<a name="HOLTop"></a>

Verwenden Sie diesen Schnellstart, um mit dem Textanalyse-SDK für Python in die Sprachanalyse einzusteigen. Obwohl die Textanalyse-REST-API mit den meisten Programmiersprachen kompatibel ist, bietet das SDK eine einfache Möglichkeit, den Dienst in Ihre Anwendungen zu integrieren, ohne JSON serialisieren oder deserialisieren zu müssen. Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).

## <a name="prerequisites"></a>Voraussetzungen

* [Python 3.x](https://www.python.org/)

* Das [Python SDK](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) für die Textanalyse können Sie wie folgt installieren:

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Außerdem benötigen Sie den [Endpunkt- und den Zugriffsschlüssel](../How-tos/text-analytics-how-to-access-key.md), die bei der Registrierung für Sie generiert wurden.

## <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Erstellen Sie in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE eine neue Python-Anwendung. Fügen Sie dann der Datei die folgenden import-Anweisungen hinzu.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>Authentifizieren Ihrer Anmeldeinformationen

> [!Tip]
> Für eine sichere Bereitstellung von Geheimnissen in Produktionssystemen empfehlen wir die Verwendung von [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

Nachdem Sie eine Variable für Ihren Abonnementschlüssel für die Textanalyse erstellt haben, instanziieren Sie damit ein `CognitiveServicesCredentials`-Objekt.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>Erstellen eines Textanalyseclients

Erstellen Sie ein `TextAnalyticsClient`-Objekt mit `credentials` und `text_analytics_url` als Parameter. Verwenden Sie die richtige Azure-Region für Ihr Textanalyse-Abonnement (z. B. `westcentralus`).

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Stimmungsanalyse

Die Nutzlast an die API besteht aus einer Liste von `documents`, die jeweils Wörterbücher aus einem `id`- und einem `text`-Attribut sind. Das `text`-Attribut speichert den Text, der analysiert werden soll, und die `id` kann ein beliebiger Wert sein. 

```python
documents = [
  {
    "id": "1", 
    "language": "en", 
    "text": "I had the best day of my life."
  },
  {
    "id": "2", 
    "language": "en", 
    "text": "This was a waste of my time. The speaker put me to sleep."
  },  
  {
    "id": "3", 
    "language": "es", 
    "text": "No tengo dinero ni nada que dar..."
  },  
  {
    "id": "4", 
    "language": "it", 
    "text": "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
  }
]
```

Rufen Sie die `sentiment()`-Funktion auf, um das Ergebnis abzurufen. Durchlaufen Sie dann die Ergebnisse, und drucken Sie die ID und den Stimmungswert jedes Dokuments. Eine gegen 0 tendierende Punktzahl deutet auf eine negative Stimmung hin, eine gegen 1 tendierende Punktzahl auf eine positive.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
     print("Document Id: ", document.id, ", Sentiment Score: ", "{:.2f}".format(document.score))
```

### <a name="output"></a>Output

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>Spracherkennung

Erstellen Sie eine Liste von Wörterbüchern, die jeweils das Dokument enthalten, das Sie analysieren möchten. Das `text`-Attribut speichert den Text, der analysiert werden soll, und die `id` kann ein beliebiger Wert sein. 

```python
documents = [
    { 
        'id': '1', 
        'text': 'This is a document written in English.' 
    },
    {
        'id': '2', 
        'text': 'Este es un document escrito en Español.' 
    },
    { 
        'id': '3', 
        'text': '这是一个用中文写的文件' 
    }
]
``` 

Rufen Sie mit dem zuvor erstellten Client `detect_language()` auf, um das Ergebnis abzurufen. Durchlaufen Sie dann die Ergebnisse, und drucken Sie die ID und die erste zurückgegebene Sprache.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id , ", Language: ", document.detected_languages[0].name)
```

### <a name="output"></a>Output

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>Entitätserkennung

Erstellen Sie eine Liste von Wörterbüchern, die die Dokumente enthalten, die Sie analysieren möchten. Das `text`-Attribut speichert den Text, der analysiert werden soll, und die `id` kann ein beliebiger Wert sein. 


```python
documents = [
    {
        "id": "1",
        "language": "en", 
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    },
    {
        "id": "2",
        "language": "es", 
        "text": "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    }
]
```

Rufen Sie mit dem zuvor erstellten Client die `entities()`-Funktion auf, um das Ergebnis abzurufen. Durchlaufen Sie dann die Ergebnisse, und geben Sie für jedes Dokument die ID und die enthaltenen Entitäten aus.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ",entity.name, "\tType: ", entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>Output

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
Document Id:  2
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  21     Length:  9  Score:  1.00
         NAME:  Redmond (Washington)    Type:  Location     Sub-type:  None
            Offset:  60     Length:  7  Score:  0.99
         NAME:  21 kilómetros   Type:  Quantity     Sub-type:  Dimension
            Offset:  71     Length:  13     Score:  0.80
         NAME:  Seattle     Type:  Location     Sub-type:  None
            Offset:  88     Length:  7  Score:  1.00
```

## <a name="key-phrase-extraction"></a>Schlüsselwortextraktion

Erstellen Sie eine Liste von Wörterbüchern, die die Dokumente enthalten, die Sie analysieren möchten. Das `text`-Attribut speichert den Text, der analysiert werden soll, und die `id` kann ein beliebiger Wert sein. 


```python
documents = [
    {
        "id": "1", 
        "language": "ja", 
        "text": "猫は幸せ"
    },
    {
        "id": "2", 
        "language": "de", 
        "text": "Fahrt nach Stuttgart und dann zum Hotel zu Fu."
    },
    {
        "id": "3", 
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    },
    {
        "id": "4", 
        "language": "es", 
        "text": "A mi me encanta el fútbol!"
    }
]
```

Rufen Sie mit dem zuvor erstellten Client die `key_phrases()`-Funktion auf, um das Ergebnis abzurufen. Durchlaufen Sie dann die Ergebnisse, und geben Sie für jedes Dokument die ID und die enthaltenen Schlüsselausdrücke aus.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t",phrase)
```

### <a name="output"></a>Output

```console
Document Id:  1
    Phrases:
         幸せ
Document Id:  2
    Phrases:
         Stuttgart
         Hotel
         Fahrt
         Fu
Document Id:  3
    Phrases:
         cat
         veterinarian
Document Id:  4
    Phrases:
         fútbol
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Textanalyse mit Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Weitere Informationen

* [Worum handelt es sich bei der Textanalyse-API?](../overview.md)
* [Beispielbenutzerszenarien](../text-analytics-user-scenarios.md)
* [Häufig gestellte Fragen (FAQ)](../text-analytics-resource-faq.md)
