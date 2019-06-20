---
title: Zuordnen von mit der kognitiven Suche angereicherten Eingabefeldern zu Ausgabefeldern – Azure Search
description: Extrahieren Sie Quelldatenfelder und reichern Sie diese mit Daten an, um sie Ausgabefeldern in einem Azure Search-Index zuzuordnen.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 506acee6cd9cd3c50e10f1c45768230564eeaaf1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65022093"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Zuordnen angereicherter Felder zu einem durchsuchbaren Index

In diesem Artikel erfahren Sie, wie Sie angereicherte Eingabefelder Ausgabefeldern in einem durchsuchbaren Index zuordnen. Nachdem Sie ein [Skillset definiert](cognitive-search-defining-skillset.md) haben, müssen Sie die Ausgabefelder aller Skills zuordnen, die Werte direkt für ein bestimmtes Feld in Ihrem Suchindex bereitstellen. Feldzuordnungen sind für das Verschieben von Inhalt aus angereicherten Dokumenten in den Index erforderlich.


## <a name="use-outputfieldmappings"></a>Verwenden von „outputFieldMappings“
Um Felder zuzuordnen, fügen Sie `outputFieldMappings` zu Ihrer Indexerdefinition hinzu, wie unten gezeigt:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

Der Text der Anforderung ist wie folgt strukturiert:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```
Setzen Sie für jede Ausgabefeldzuordnung den Namen des angereicherten Feldes (sourceFieldName) und den Namen des im Index referenzierten Feldes (targetFieldName) fest.

Der Pfad in einem sourceFieldName kann ein Element oder mehrere Elemente darstellen. Im obigen Beispiel stellt ```/document/content/sentiment``` einen einzelnen numerischen Wert dar, während ```/document/content/organizations/*/description``` für mehrere Organisationsbeschreibungen steht. In Fällen, in denen es mehrere Elemente gibt, werden diese zu einem Array „vereinfacht“, das jedes der Elemente enthält. Für das Beispiel ```/document/content/organizations/*/description``` würden die Daten im Feld *Beschreibungen* demnach wie ein flaches Array von Beschreibungen aussehen, bevor sie indiziert werden:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Ihre angereicherten Felder den durchsuchbaren Feldern zugeordnet haben, können Sie die Feldattribute für jedes der durchsuchbaren Felder [als Teil der Indexdefinition](search-what-is-an-index.md) festlegen.

Weitere Informationen zu Feldzuordnungen finden Sie unter [Feldzuordnungen in Azure Search-Indexern](search-indexer-field-mappings.md).
