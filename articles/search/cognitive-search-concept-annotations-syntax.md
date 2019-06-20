---
title: Verweise auf Eingaben und Ausgaben in Pipelines für die kognitive Suche – Azure Search
description: Erläutert die Syntax der Anmerkung und, wie auf eine Anmerkung in den Eingaben und Ausgaben eines Skillsets in einer Pipeline der kognitiven Suche in Azure Search verwiesen wird.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 637edc0e45daa37a753fbaa15313b076e8af4d7c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023875"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Verweisen auf Anmerkungen in einem Skillset der kognitiven Suche

In diesem Artikel erfahren Sie, wie Sie auf Anmerkungen in Skilldefinitionen verweisen und verschiedene Szenarien anhand von Beispielen veranschaulichen können. Während der Inhalt eines Dokuments eine Reihe von Skills durchläuft, wird er mit Anmerkungen angereichert. Anmerkungen können als Eingaben für die nachfolgende Anreicherung verwendet oder einem Ausgabefeld in einem Index zugeordnet werden. 
 
Die Beispiele in diesem Artikel basieren auf dem *content*-Feld, das automatisch von [Azure Blob-Indexern](search-howto-indexing-azure-blob-storage.md) während der Dokumententschlüsselung generiert wird. Wenn Sie sich auf Dokumente aus einem Blobcontainer beziehen, verwenden Sie ein Format wie `"/document/content"`, wobei das *content*-Feld Teil von *document* ist. 

## <a name="background-concepts"></a>Hintergrundkonzepte

Gehen Sie vor dem Überprüfen der Syntax nun noch einmal einige wichtige Konzepte durch, um die später in diesem Artikel vorgestellten Beispiele besser zu verstehen.

| Begriff | BESCHREIBUNG |
|------|-------------|
| Angereichertes Dokument | Ein angereichertes Dokument ist eine interne Struktur, die von der Pipeline erstellt und verwendet wird, um alle Anmerkungen zu einem Dokument zu speichern. Stellen Sie sich ein angereichertes Dokument als einen Baum von Anmerkungen vor. Im Allgemeinen wird eine Anmerkung, die aus einer vorherigen Anmerkung erstellt wurde, zu deren untergeordnetem Element.<p/>Angereicherte Dokumente existieren nur für die Dauer der Skillsetausführung. Sobald der Inhalt dem Suchindex zugeordnet ist, wird das angereicherte Dokument nicht mehr benötigt. Obwohl Sie nicht direkt mit angereicherten Dokumenten interagieren, ist es nützlich, ein Gedankenmodell der Dokumente zu haben, wenn Sie ein Skillset erstellen. |
| Anreicherungskontext | Der Kontext, in dem die Anreicherung stattfindet, d.h. welches Element angereichert wird. Standardmäßig befindet sich der Anreicherungskontext auf der Ebene `"/document"`, die auf einzelne Dokumente bezogen ist. Wenn ein Skill ausgeführt wird, werden die Ergebnisse dieses Skills zu [Eigenschaften des definierten Kontexts](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Beispiel 1: Einfacher Anmerkungsverweis

Angenommen, Sie haben in Azure Blob Storage eine Vielzahl von Dateien, die Verweise auf die Namen von Personen enthalten, die Sie mithilfe der Entitätserkennung extrahieren möchten. In der folgenden Skilldefinition ist `"/document/content"` die Textdarstellung des gesamten Dokuments, und „people“ ist eine Extrahierung von vollständigen Namen für als Personen identifizierte Entitäten.

Da der Standardkontext `"/document"` ist, kann auf die Liste der Personen nun als `"/document/people"` verwiesen werden. In diesem speziellen Fall ist `"/document/people"` eine Anmerkung, die nun einem Feld in einem Index zugeordnet oder in einem anderen Skill im gleichen Skillset verwendet werden kann.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>Beispiel 2: Verweis auf ein Array innerhalb eines Dokuments

Dieses Beispiel baut auf dem vorherigen auf und zeigt Ihnen, wie Sie einen Anreicherungsschritt mehrfach über dasselbe Dokument aufrufen können. Angenommen, das vorherige Beispiel generiert ein Array von Zeichenfolgen mit zehn Personennamen aus einem einzigen Dokument. Ein sinnvoller nächster Schritt könnte eine zweite Anreicherung sein, die den Nachnamen aus einem vollständigen Namen extrahiert. Da es zehn Namen gibt, soll dieser Schritt in diesem Dokument zehnmal aufgerufen werden, d.h. einmal pro Person. 

Um die richtige Anzahl von Iterationen aufzurufen, setzen Sie den Kontext auf `"/document/people/*"`, wobei das Sternchen (`"*"`) für alle Knoten im angereicherten Dokument als untergeordnete Elemente von `"/document/people"` steht. Obwohl dieser Skill nur einmal im Skillsarray definiert ist, wird er für jedes Element innerhalb des Dokuments aufgerufen, bis alle Elemente bearbeitet sind.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Wenn es sich bei Anmerkungen um Arrays oder Sammlungen von Zeichenfolgen handelt, empfiehlt es sich, für bestimmte Elemente statt auf das Array als Ganzes abzuzielen. Der Code im obigen Beispiel generiert unter jedem Knoten, der durch den Kontext repräsentiert wird, eine Anmerkung namens `"last"`. Wenn Sie auf diese Familie von Anmerkungen verweisen möchten, können Sie die Syntax `"/document/people/*/last"` verwenden. Wenn Sie auf eine bestimmte Anmerkung verweisen möchten, können Sie einen expliziten Index verwenden: `"/document/people/1/last`", um auf den Nachnamen der ersten im Dokument identifizierten Person zu verweisen. Beachten Sie, dass in dieser Syntax Arrays „0 indiziert“ sind.

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Beispiel 3: Verweis auf Elemente innerhalb eines Arrays

Manchmal müssen Sie alle Anmerkungen eines bestimmten Typs gruppieren, um sie an einen bestimmten Skill zu übergeben. Angenommen, es gibt einen hypothetischen benutzerdefinierten Skill, der den häufigsten Nachnamen aus allen in Beispiel 2 extrahierten Nachnamen identifiziert. Um dem benutzerdefinierten Skill nur die Nachnamen bereitzustellen, legen Sie den Kontext auf `"/document"` und die Eingabe auf `"/document/people/*/lastname"` fest.

Beachten Sie, dass die Kardinalität von `"/document/people/*/lastname"` größer als die des Dokuments ist. Es kann zehn Nachnamenknoten geben, während es nur einen Dokumentknoten für dieses Dokument gibt. In diesem Fall wird automatisch ein Array von `"/document/people/*/lastname"` erstellt, das alle Elemente des Dokuments enthält.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Weitere Informationen
+ [Integrieren eines benutzerdefinierten Skills in einer Anreicherungspipeline](cognitive-search-custom-skill-interface.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Erstellen eines Skillsets (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Zuordnen angereicherter Felder zu einem Index](cognitive-search-output-field-mapping.md)
