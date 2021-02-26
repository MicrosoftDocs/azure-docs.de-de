---
title: Ähnlichkeitsalgorithmus für die Rangfolge
titleSuffix: Azure Cognitive Search
description: Festlegen des Ähnlichkeitsalgorithmus zum Testen eines neuen Ähnlichkeitsalgorithmus für die Rangfolge
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: e2caa09d41abb1842100ed8259e82ec411390ccb
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520628"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Ähnlichkeitsalgorithmus für die Rangfolge in Azure Cognitive Search

> [!IMPORTANT]
> Ab dem 15. Juli 2020 verwenden neu erstellte Suchdienste die BM25-Rangfolgefunktion automatisch, die sich in den meisten Fällen bewährt hat, um Suchrangfolgen bereitzustellen, die mit den Benutzererwartungen besser übereinstimmen als die aktuelle Standardrangfolge. Neben der höheren Rangfolge ermöglicht BM25 auch Konfigurationsoptionen, mit denen Ergebnisse auf der Basis von Faktoren wie der Dokumentgröße optimiert werden können.  
>
> Mit dieser Änderung wird die Reihenfolge Ihrer Suchergebnisse wahrscheinlich geringfügige Änderungen aufweisen. Für diejenigen, die die Auswirkung dieser Änderung testen möchten, ist der BM25-Algorithmus in der API-Version 2019-05-06-Preview und in 2020-06-30 verfügbar.  

Dieser Artikel beschreibt, wie Sie den neuen BM25- Rangfolgealgorithmus bei vorhandenen Suchdiensten für neue Indizes verwenden können, die mit der Vorschau-API erstellt und abgefragt werden.

Azure Cognitive Search übernimmt zurzeit die offizielle Lucene-Implementierung des Okapi BM25-Algorithmus, *BM25Similarity*, die die zuvor verwendete *ClassicSimilarity*-Implementierung ersetzt. Wie der ältere ClassicSimilarity-Algorithmus ist BM25Similarity eine TF-IDF-ähnliche Abruffunktion, bei der die Begriffshäufigkeit (Term Frequency, TF) und die inverse Dokumenthäufigkeit (Inverse Document Frequency, IDF) als Variablen verwendet werden, um die Relevanzbewertungen für jedes Dokument/Abfrage-Paar zu berechnen, die dann für die Rangfolge verwendet werden. 

BM25 ist konzeptionell dem älteren klassischen Ähnlichkeitsalgorithmus ähnlich und verwendet seinen Kern zum Abruf probabilistischer Informationen, um ihn zu verbessern. BM25 bietet auch erweiterte Anpassungsoptionen, z. B. kann der Benutzer entscheiden, wie weit die Relevanzbewertung von der Häufigkeit übereinstimmender Begriffe abhängt.

## <a name="how-to-test-bm25-today"></a>Wie BM25 heute getestet werden kann

Wenn Sie einen neuen Index erstellen, können Sie eine **Ähnlichkeitseigenschaft** festlegen, um den Algorithmus anzugeben. Sie können die `api-version=2019-05-06-Preview` verwenden, wie unten dargestellt, oder `api-version=2020-06-30`.

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

Die **Ähnlichkeitseigenschaft** ist während dieser Übergangszeit, in der beide Algorithmen verfügbar sind, nur für vorhandene Dienste nützlich. 

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| Ähnlichkeit | Optional. Gültige Werte sind *„#Microsoft.Azure.Search.ClassicSimilarity“* und *„#Microsoft.Azure.Search.BM25Similarity“* . <br/> Erfordert `api-version=2019-05-06-Preview` oder höher für einen Suchdienst, der vor dem 15. Juli 2020 erstellt wurde. |

Für neue Dienste, die nach dem 15. Juli 2020 erstellt werden, wird BM25 automatisch verwendet und ist der einzige Ähnlichkeitsalgorithmus. Wenn Sie versuchen, **Ähnlichkeit** für einen neuen Dienst auf `ClassicSimilarity` festzulegen, wird ein Fehler „400“ zurückgegeben, da dieser Algorithmus für einen neuen Dienst nicht unterstützt wird.

Für vorhandene Dienste, die vor dem 15. Juli 2020 erstellt wurden, bleibt die klassische Ähnlichkeit der Standardalgorithmus. Wenn die Eigenschaft **Ähnlichkeit** ausgelassen oder auf NULL festgelegt wird, verwendet der Index den klassischen Algorithmus. Wenn Sie den neuen Algorithmus verwenden möchten, müssen Sie **Ähnlichkeit** wie oben beschrieben festlegen.

## <a name="bm25-similarity-parameters"></a>BM25-Ähnlichkeitsparameter

BM25-Ähnlichkeit fügt zwei durch Benutzer anpassbare Parameter hinzu, um die berechnete Relevanzbewertung zu steuern.

### <a name="k1"></a>k1

Der *k1*-Parameter steuert die Skalierungsfunktion, die bestimmt, wie weit die endgültige Relevanzbewertung eines Dokument/Abfrage-Paars von der Häufigkeit der einzelnen übereinstimmenden Begriffe abhängt.

Der Wert 0 (null) stellt ein „binäres Modell“ dar, bei dem der Anteil eines einzelnen übereinstimmenden Begriffs für alle übereinstimmenden Dokumente identisch ist, unabhängig davon, wie oft dieser Begriff im Text vorkommt, während ein größerer k1-Wert bestimmt, dass die Bewertung höher ausfällt, je mehr Instanzen desselben Begriffs im Dokument gefunden werden. Standardmäßig verwendet Azure Cognitive Search den Wert 1,2 für den k1-Parameter. Die Verwendung eines höheren k1-Werts kann wichtig sein, wenn wir erwarten, dass mehrere Begriffe Teil einer Suchabfrage sind. In diesen Fällen bevorzugen wir möglicherweise Dokumente, in denen viele der unterschiedlichen gesuchten Abfragebegriffe vorkommen, gegenüber Dokumenten, in denen nur ein einzelner Begriff häufig vorkommt. Wenn Sie z. B. den Index nach Dokumenten abfragen, die die Begriffe „Apollo Spaceflight“ enthalten, möchten wir die Bewertung eines Artikels über griechische Mythologie, der den Begriff „Apollo“ ein paar Dutzend Mal enthält, ohne dass „Spaceflight“ vorkommt, gegenüber einem Artikel reduzieren, in dem „Apollo“ als auch „Spaceflight“ nur einige wenige Male explizit erwähnt werden. 
 
### <a name="b"></a>b

Der *b*-Parameter steuert, wie sich die Länge eines Dokuments auf die Relevanzbewertung auswirkt.

Der Wert 0,0 bedeutet, dass die Länge des Dokuments das Ergebnis nicht beeinflusst, während der Wert 1,0 bedeutet, dass die Auswirkung der Begriffshäufigkeit auf die Relevanzbewertung durch die Länge des Dokuments normalisiert wird. In Azure Cognitive Search wird für den b-Parameter der Standardwert 0,75 verwendet. Das Normalisieren der Begriffshäufigkeit durch die Länge des Dokuments ist nützlich, wenn Sie längere Dokumente zurückstellen möchten. In einigen Fällen enthalten längere Dokumente (z. B. komplette Roman) im Vergleich zu deutlich kürzeren Dokumenten eher viele irrelevante Begriffe.

### <a name="setting-k1-and-b-parameters"></a>Festlegen des „k1“- und „b“-Parameters

Wenn Sie den Wert „b“ oder „k1“ anpassen möchten, fügen Sie ihn einfach beim Verwenden von BM25 dem Ähnlichkeitsobjekt als Eigenschaften hinzu:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

Der Ähnlichkeitsalgorithmus kann nur zum Zeitpunkt der Indexerstellung festgelegt werden. Dies bedeutet, dass der verwendete Ähnlichkeitsalgorithmus für vorhandene Indizes nicht geändert werden kann. Die Parameter *„b“* und *„K1“* können geändert werden, wenn eine vorhandene Indexdefinition, die BM25 verwendet, aktualisiert wird. Die Änderung dieser Werte bei einem vorhandenen Index schaltet den Index für mindestens ein paar Sekunden offline, sodass Indizierungs- und Abfrageanforderungen nicht gelingen. Aus diesem Grund müssen Sie den Parameter „allowIndexDowntime=true“ in der Abfragezeichenfolge Ihrer Aktualisierungsanforderung festlegen:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>Weitere Informationen  

+ [REST-API-Referenz](/rest/api/searchservice/)
+ [Hinzufügen von Bewertungsprofilen zum Index](index-add-scoring-profiles.md)
+ [Indexerstellungs-API](/rest/api/searchservice/create-index)
+ [.NET SDK für die kognitive Azure-Suche](/dotnet/api/overview/azure/search)