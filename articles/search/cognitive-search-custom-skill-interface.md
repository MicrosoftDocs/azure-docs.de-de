---
title: Schnittstellendefinition für benutzerdefinierte Qualifikationen in der kognitiven Suche – Azure Search
description: Passen Sie die Schnittstelle für die Datenextrahierung für benutzerdefinierte Web-API-Skills in der Pipeline der kognitiven Suche in Azure Search an.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 1bf42e5f418f99f5e5327d790c1adffe2357b84e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65021935"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Hinzufügen eines benutzerdefinierten Skills zu einer Pipeline der kognitiven Suche

Eine [Pipeline für die Indizierung der kognitive Suche](cognitive-search-concept-intro.md) in Azure Search kann aus [vordefinierten Skills](cognitive-search-predefined-skills.md) und [benutzerdefinierten Skills](cognitive-search-custom-skill-web-api.md) zusammengestellt werden, die Sie persönlich erstellen und der Pipeline hinzufügen. In diesem Artikel erfahren Sie, wie Sie einen benutzerdefinierten Skill erstellen, der eine Schnittstelle verfügbar macht, sodass er in eine Pipeline für die kognitive Suche eingefügt werden kann. 

Das Erstellen eines benutzerdefinierten Skills gibt Ihnen die Möglichkeit, Transformationen einzufügen, die für Ihren Inhalt eindeutig sind. Ein benutzerdefinierter Skill wird unabhängig ausgeführt, wobei jeder gewünschte Anreicherungsschritt angewendet wird. Sie können beispielsweise feldspezifische benutzerdefinierte Entitäten definieren, benutzerdefinierte Klassifizierungsmodelle erstellen, um Geschäfts- und Finanzverträge und -dokumente zu unterscheiden, oder einen Spracherkennungsskill hinzufügen, um selbst in Audiodateien relevante Inhalte zu erreichen. Ein Beispiel mit Schrittanleitung finden Sie unter [Beispiel: Erstellen eines benutzerdefinierten Skills](cognitive-search-create-custom-skill-example.md).

 Welchen benutzerdefinierten Skill Sie auch benötigen, es gibt eine einfache und klare Schnittstelle, um einen benutzerdefinierten Skill mit dem Rest der Anreicherungspipeline zu verbinden. Damit die Aufnahme in ein [Skillset](cognitive-search-defining-skillset.md) möglich ist, müssen Eingaben akzeptiert und Ausgaben emittiert werden können, sodass diese innerhalb des Skillsets umfassend verwendet werden können. Der Fokus dieses Artikels liegt auf den Ein- und Ausgabeformaten, die die Anreicherungspipeline benötigt.

## <a name="web-api-custom-skill-interface"></a>Web-API-Schnittstelle für benutzerdefinierte Skills

Benutzerdefinierte WebAPI-Skill-Endpunkte nach Standardtimeout, wenn sie innerhalb eines 30-Sekunden-Fensters keine Antwort zurückgeben. Die Indizierungspipeline ist synchron, und die Indizierung erzeugt ein Timeoutfehler, wenn in diesem Zeitraum keine Antwort empfangen wird.  Durch Festlegen des Timeoutparameters ist es möglich, den Timeout auf bis zu 90 Sekunden zu konfigurieren:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 90 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT90S",
```

Derzeit ist die einzige Methode für die Interaktion mit einem benutzerdefinierten Skill über eine Web-API-Schnittstelle. Die Web-API-Anforderungen müssen die in diesem Abschnitt beschriebenen Voraussetzungen erfüllen.

### <a name="1--web-api-input-format"></a>1.  Web-API-Eingabeformat

Die Web-API muss ein Array von zu verarbeitenden Datensätzen akzeptieren. Jeder Datensatz muss einen „Eigenschaftenbehälter“, d.h. die Eingabewerte für Ihre Web-API, umfassen. 

Angenommen, Sie möchten eine einfache Anreicherungsfunktion erstellen, die das erste im Vertragstext genannte Datum identifiziert. In diesem Beispiel akzeptiert der Skill eine einzige Eingabe *contractText* als Vertragstext. Der Skill hat auch eine einzige Ausgabe: das Datum des Vertrags. Um die Anreicherungsfunktion interessanter zu machen, geben Sie *contractDate* in Form eines mehrteiligen komplexen Typs zurück.

Ihre Web-API muss bereit sein, um einen Stapel von Eingabedatensätzen zu empfangen. Jedes Element des Arrays *values* stellt die Eingabe für einen bestimmten Datensatz dar. Jeder Datensatz muss die folgenden Elemente enthalten:

+ Ein *recordId*-Element, das der eindeutige Bezeichner für einen bestimmten Datensatz ist. Wenn Ihre Anreicherungsfunktion die Ergebnisse zurückgibt, muss diese *recordId*-Element zur Verfügung gestellt werden, damit der Aufrufer die Datensätze mit den Eingaben abgleichen kann.

+ Ein *data*-Element, das im Wesentlichen ein Behälter mit Eingabefeldern für jeden Datensatz ist.

Das bedeutet für das oben gezeigte Beispiel, dass die Web-API Anforderungen zu erwarten hat, die wie folgt aussehen:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
In der Praxis können kann Ihr Dienst mit Hunderten oder Tausenden von Datensätzen, anstatt nur mit den hier gezeigten drei, aufgerufen werden.

### <a name="2-web-api-output-format"></a>2. Web-API-Ausgabeformat

Das Format der Ausgabe ist eine Gruppe von Datensätzen, die ein *recordId*-Element umfassen, und ein Eigenschaftenbehälter. 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Dieses spezielle Beispiel hat nur eine Ausgabe, Sie können jedoch auch mehr als eine Eigenschaft ausgeben. 

### <a name="errors-and-warning"></a>Fehler und Warnungen

Wie im vorherigen Beispiel gezeigt, können Sie für jeden Datensatz Fehler- und Warnmeldungen zurückgeben.

## <a name="consuming-custom-skills-from-skillset"></a>Verwenden von benutzerdefinierten Skills aus dem Skillset

Wenn Sie eine Web-API-Anreicherungsfunktion erstellen, können Sie HTTP-Header und Parameter als Teil der Anforderung beschreiben. Der folgende Codeausschnitt zeigt, wie Anforderungsparameter und HTTP-Header als Teil der Skillsetdefinition beschrieben werden können.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

+ [Beispiel: Erstellen einer benutzerdefinierten Qualifikation für die Textübersetzungs-API](cognitive-search-create-custom-skill-example.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Erstellen eines Skillsets (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Zuordnen angereicherter Felder](cognitive-search-output-field-mapping.md)
