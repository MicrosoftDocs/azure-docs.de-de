---
title: 'Features: Merkmale: Aktion und Kontext – Personalisierung'
titleSuffix: Azure Cognitive Services
description: Die Personalisierung verwendet Merkmale, Informationen über Aktionen und Kontext, um bessere Rangfolgenvorschläge zu machen. Merkmale können sehr allgemein oder spezifisch für ein Element sein.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: edd1549ddabef0ae1ba37150ad75a371ac6e6d85
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365515"
---
# <a name="features-are-information-about-actions-and-context"></a>Merkmale sind Informationen über Aktionen und Kontext.

Der Personalisierungsdienst funktioniert, indem er lernt, was Ihre Anwendung Benutzern in einem bestimmten Kontext anzeigen sollte.

Die Personalisierung verwendet **Merkmale** , wobei es sich um Informationen über den **aktuellen Kontext** handelt, um die beste **Aktion** auszuwählen. Die Merkmale stellen alle Informationen dar, von denen Sie glauben, dass sie bei der Personalisierung hilfreich sein können, um höhere Belohnungen zu erzielen. Merkmale können sehr allgemein oder spezifisch für ein Element sein. 

Beispielsweise können Sie ein **Merkmal** haben:

* Die _Benutzerpersona_ , wie z. B. `Sports_Shopper`. Dabei sollte es sich nicht um die ID eines einzelnen Benutzers handeln. 
* Zu dem _Inhalt_ , z. B. ob ein Video ein `Documentary` ist, ein `Movie` oder eine `TV Series`, oder ob ein Einzelhandelsartikel im Store verfügbar ist.
* Zum _aktuellen_ Zeitraum dar, z. B. welcher Wochentag es ist.

Die Personalisierung schreibt weder vor noch schränkt sie ein, welche Merkmale Sie für Aktionen und Kontext senden können:

* Sie können manche Merkmale für einige Aktionen senden, und nicht für andere nicht, wenn Sie nicht über sie verfügen. Beispielsweise können TV-Serien Attribute besitzen, die Filme nicht haben.
* Manche Merkmale stehen eventuell nur manchmal zur Verfügung. Beispielsweise kann eine mobile Anwendung mehr Informationen als eine Webseite bereitstellen. 
* Im Laufe der Zeit können Sie Merkmale von Kontext und Aktionen hinzufügen und entfernen. Die Personalisierung weiterhin von verfügbaren Informationen.
* Es muss mindestens ein Merkmal für den Kontext geben. Die Personalisierung unterstützt keinen leeren Kontext. Wenn Sie jedes Mal nur einen festen Kontext senden, wählt die Personalisierung die Aktion für Rangfolgen nur unter Berücksichtigung der Merkmale in Aktionen aus.
* Bei Features, die unter eine Kategorie fallen, müssen Sie die möglichen Werte nicht definieren, und Sie müssen keine Bereiche für numerische Werte vorab definieren.

## <a name="supported-feature-types"></a>Unterstützte Merkmalstypen

Die Personalisierung unterstützt Merkmale der Typen Zeichenfolge, numerisch und boolesch.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Auswirkungen der Wahl des Merkmalstyps auf Machine Learning in der Personalisierung

* **Zeichenfolgen** : Bei Zeichenfolgentypen erstellt jede Kombination aus Schlüssel und Wert neue Gewichtungen im Machine Learning-Modell „Personalisierung“. 
* **Numerisch** : Sie sollten numerische Werte verwenden, wenn sich die Zahl proportional auf das Personalisierungsergebnis auswirken soll. Dies hängt stark vom Szenario ab. Hier ist ein vereinfachtes Beispiel: Bei der Personalisierung eines Kundenerlebnisses im Einzelhandel könnte „NumberOfPetsOwned“ (AnzahlVonHaustierenImBesitz) ein numerisches Merkmal sein, wenn Personen mit 2 oder 3 Haustieren das Personalisierungsergebnis zwei- oder dreimal so stark wie Personen beeinflussen sollen, die nur ein Haustier besitzen. Merkmale, die auf numerischen Einheiten basieren, bei denen aber die Bedeutung nicht linear ist – z.B. „Alter“, „Temperatur“ oder „Körpergröße einer Person“ – werden am besten als Zeichenfolgen codiert, und die Merkmalsqualität kann durch die Verwendung von Bereichen normalerweise verbessert werden. „Alter“ könnte beispielsweise codiert werden als „Alter“: „0–5“, „Alter“: „6–10“ usw.
* **Boolesche** Werte, die mit dem Wert „false“ gesendet wurden, agieren so, als ob sie nie gesendet worden wären.

Merkmale, die nicht vorhanden sind, sollten aus der Anforderung entfernt werden. Vermeiden Sie das Senden von Merkmalen mit einem Nullwert, weil dieser beim Trainieren des Modells als vorhanden und mit einem Wert von „Null“ verarbeitet wird.

## <a name="categorize-features-with-namespaces"></a>Kategorisieren von Merkmalen mit Namespaces

Die Personalisierung nimmt in Namespaces organisierte Merkmale auf. Sie bestimmen in Ihrer Anwendung, ob Namespaces verwendet werden und was diese repräsentieren sollen. Namespaces werden verwendet, um Merkmale zu einem ähnlichen Thema zu gruppieren oder Merkmale, die aus einer bestimmten Quelle stammen.

Im Folgenden finden Sie Beispiele für Merkmalsnamespaces, die von Anwendungen verwendet werden:

* User_Profile_from_CRM
* Time
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Weather
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Sie können Merkmalsnamespaces nach Ihren eigenen Konventionen benennen, solange es sich dabei um gültige JSON-Schlüssel handelt. Namespaces werden zum Sortieren von Features in verschiedene Gruppen und zum Unterscheiden von Features mit ähnlichen Namen verwendet. Sie können sich Namespaces als „Präfix“ vorstellen, der zu Namen von Features hinzugefügt wird. Namespaces können nicht geschachtelt werden.


Im folgenden JSON-Code sind `user`, `state` und `device` Merkmalsnamespaces. 

> [!Note]
> Derzeit wird dringend empfohlen, UTF-8-basierte Namen mit verschiedenen Anfangsbuchstaben für Featurenamespaces zu verwenden. Beispielsweise Namen wie `user`, `state` und `device`, die jeweils mit `u`, `s` und `d` beginnen. Derzeit können Namespaces mit denselben Anfangsbuchstaben zu Konflikten in Indizes für maschinelles Lernen führen.

JSON-Objekte können geschachtelte JSON-Objekte und einfache Eigenschaften/Werte enthalten. Ein Array kann nur einbezogen werden, wenn die Arrayelemente Zahlen sind. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": [47.6, -122.1]
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>Einschränkungen bei Zeichensätzen für Namespaces

Die Zeichenfolge, die Sie für die Benennung des Namespace verwenden, muss einige Einschränkungen einhalten: 
* Sie kann kein Unicode sein.
* Sie können einige der druckbaren Symbole mit Codes < 256 für die Namespacenamen verwenden. 
* Sie können keine Symbole mit Codes < 32 (nicht druckbar), 32 (Leerzeichen), 58 (Doppelpunkt), 124 (Pipe) und 126–140 verwenden.
* Sie darf nicht mit einem Unterstrich "_" beginnen; andernfalls wird die Funktion ignoriert.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Merkmalssätze für die Personalisierung effektiver gestalten

Ein guter Merkmalssatz hilft der Personalisierung zu lernen, wie die Aktion vorherzusagen ist, die die höchste Belohnung erzeugt. 

Erwägen Sie, Merkmale an die Relevanz-API der Personalisierung zu senden, die diese Empfehlungen befolgen:

* Es gibt genügend Merkmale zum Betreiben der Personalisierung. Je exakter der Inhalt zielgerichtet sein muss, desto mehr Merkmale werden benötigt.

* Es gibt genügend Merkmale vielfältiger *Dichten*. Ein Merkmal ist *dicht* , wenn viele Elemente in wenigen Buckets gruppiert sind. Beispielsweise lassen sich Tausende von Videos als „Lang“ (länger als 5 Minuten) oder „Kurz“ (kürzer als 5 Minuten) klassifizieren. Dies ist ein *sehr dichtes* Merkmal. Andererseits können dieselben Tausenden von Elementen ein Attribut namens „Titel“ besitzen, das praktisch nie denselben Wert bei zwei Elementen haben wird. Dies ist ein sehr undichtes Merkmal oder *von geringer Dichte*.  

Das Vorhandensein von Merkmalen mit hoher Dichte hilft der Personalisierung bei der Extrapolierung des Lernfortgangs von einem zum anderen Element. Wenn aber nur wenige Merkmale vorhanden und diese zu dicht sind, versucht die Personalisierung, Inhalt exakt zu treffen, wobei nur aus wenigen Buckets ausgewählt werden kann.

### <a name="improve-feature-sets"></a>Verbessern von Merkmalssätzen 

Analysieren Sie das Verhalten von Benutzern, indem Sie eine Offlineauswertung vornehmen. Dies ermöglicht Ihnen die Betrachtung vergangener Daten, um festzustellen, welche Merkmale in hohem Maß zu positiven Belohnungen beitragen, im Gegensatz zu denjenigen, die weniger dazu beitragen. Sie können sehen, welche Merkmale hilfreich sind, und es liegt bei Ihnen und Ihrer Anwendung, bessere Merkmale zu finden, um sie an die Personalisierung zu senden, um die Ergebnisse noch weiter zu verbessern.

In diesen folgenden Abschnitten finden Sie allgemeine Methoden zur Verbesserung von Merkmalen, die an die Personalisierung gesendet werden.

#### <a name="make-features-more-dense"></a>Erhöhen der Dichte von Merkmalen

Es ist möglich, Ihre Merkmalssätze zu verbessern, indem Sie sie bearbeiten, um sie größer und mehr oder weniger dicht zu machen.

Ein Zeitstempel mit Sekundenangaben ist beispielsweise ein Merkmal von sehr geringer Dichte. Seine Dichte könnte erhöht (effektiver) werden, indem Zeiten in „Morgen“, „Mittag“, „Nachmittag“ usw. klassifiziert werden.

Standortinformationen profitieren in der Regel auch von der Erstellung umfassenderer Klassifizierungen. Eine Koordinate mit Breitengrad-Längengrad wie Breitengrad: 47.67402° N, Längengrad: 122.12154° W ist z. B. zu präzise und zwingt das Modell, Breiten- und Längengrade als unterschiedliche Dimensionen zu erlernen. Wenn Sie versuchen, auf der Grundlage von Standortinformationen eine Personalisierung vorzunehmen, hilft es, Standortinformationen in größeren Sektoren zu gruppieren. Eine einfache Möglichkeit dazu besteht darin, eine geeignete Rundungsgenauigkeit für die Breitengrad-Längengrad-Zahlen auszuwählen und Breiten- und Längengrade zu „Bereichen“ zu kombinieren, indem sie zu einer Zeichenfolge zusammengefasst werden. Es wäre z. B. eine gute Möglichkeit, 47.67402° N, Längengrad: 122,12154° W in Regionen mit einer Breite von einigen Kilometern als "location":"34.3 , 12.1" darzustellen.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Erweitern von Merkmalssätzen mit extrapolierten Informationen

Sie können auch mehr Merkmale erhalten, indem Sie sich Gedanken zu nicht erkundeten Attributen machen, die aus bereits vorhandenen Informationen abgeleitet werden können. Ist es bei der Personalisierung einer fiktiven Filmliste beispielsweise möglich, dass ein Wochentag gegenüber einem Wochenende unterschiedliches Verhalten bei Benutzern hervorruft? Zeit könnte um ein Attribut „Wochenende“ oder „Wochentag“ erweitert werden. Lenken nationale kulturelle Feiertage die Aufmerksamkeit auf bestimmte Arten von Filmen? Beispielsweise eignet sich ein Attribut „Halloween“ an Orten, wo dieses relevant ist. Ist es möglich, dass regnerisches Wetter bei vielen Benutzern signifikante Auswirkungen auf die Auswahl eines Films hat? Zusammen mit Zeit und Ort könnte ein Wetterdienst diese Informationen bereitstellen, und Sie können sie als zusätzliches Merkmal hinzufügen. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Erweitern von Merkmalssätzen mit künstlicher Intelligenz und Cognitive Services

Künstliche Intelligenz und ausführungsbereite Cognitive Services können eine sehr leistungsfähige Ergänzung der Personalisierung sein. 

Indem Sie Ihre Elemente mithilfe von KI-Diensten vorverarbeiten, können Sie automatisch Informationen extrahieren, die wahrscheinlich relevant für die Personalisierung sind.

Beispiel:

* Sie können eine Filmdatei mit [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) ausführen, um Szenenelemente, Text, Stimmungen und viele andere Attribute zu extrahieren. Diese Attribute können dann verdichtet werden, um die Merkmale wiederzugeben, die die ursprünglichen Elementmetadaten nicht aufwiesen. 
* Bilder können mit einer Objekterkennung behandelt werden, Gesichter mit einer Stimmungsanalyse usw.
* Informationen in Text können durch die Extraktion von Entitäten, Stimmungen, die Erweiterung von Entitäten mit Bing Knowledge Graph usw. angereichert werden.

Sie können mehrere weitere [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) verwenden, z. B.

* [Entitätsverknüpfung](../text-analytics/index.yml)
* [Textanalyse](../text-analytics/overview.md)
* [Emotionen](../face/overview.md)
* [Maschinelles Sehen](../computer-vision/overview.md)

## <a name="actions-represent-a-list-of-options"></a>Aktionen stellen eine Liste mit Optionen dar.

Jede Aktion:

* Hat eine _Ereignis-ID_. Wenn Sie bereits über eine Ereignis-ID verfügen, sollten Sie diese übermitteln. Wenn Sie keine Ereignis-ID haben, senden Sie keine, die Personalisierung erstellt eine für Sie und gibt sie in der Antwort auf die Ranganfrage zurück. Die ID wird dem Rangereignis zugeordnet, nicht dem Benutzer. Wenn Sie eine ID erstellen, funktioniert eine GUID am besten. 
* Hat eine Liste von Merkmalen.
* Die Liste der Merkmale kann groß ist (Hunderte), aber wir empfehlen, die Effektivität von Merkmalen zu bewerten, um Merkmale zu entfernen, die nicht zum Erzielen von Belohnungen beitragen. 
* Die Merkmale in den **Aktionen** können eine Korrelation mit Merkmalen in dem von der Personalisierung verwendeten **Kontext** aufweisen oder nicht.
* Merkmale für Aktionen können in einigen Aktionen vorhanden sein, in anderen aber nicht. 
* Merkmale für eine bestimmte Aktions-ID können an einem Tag verfügbar sein, später aber nicht mehr. 

Die Leistung der Machine Learning-Algorithmen der Personalisierung verbessert sich, wenn stabile Merkmalssätze vorliegen, aber Rangfolgenaufrufe schlagen nicht fehl, wenn sich der Merkmalssatz im Laufe der Zeit ändert.

Senden Sie beim Zuweisen von Rangfolgen für Aktionen nicht in mehr als 50 Aktionen als Eingabe. Dies können jedes Mal dieselben 50 Aktionen sein, oder sie können sich ändern. Wenn Sie beispielsweise einen Produktkatalog mit 10.000 Artikeln für eine E-Commerce-Anwendung haben, können Sie eine Empfehlungs- oder Filter-Engine verwenden, um die Top 40 Produkte zu bestimmen, die einem Kunden eventuell gefallen, und die Personalisierung verwenden, um das eine Produkt zu finden, das die höchste Belohnung (z. B. dass der Benutzer es in den Warenkorb legt) im aktuellen Kontext erzeugt.


### <a name="examples-of-actions"></a>Beispiele für Aktionen

Die Aktionen, die Sie an die Relevanz-API senden, sind davon abhängig, was Sie zu personalisieren versuchen.

Im Folgenden finden Sie einige Beispiele:

|Zweck|Aktion|
|--|--|
|Personalisieren, welcher Artikel auf einer Nachrichtenwebsite hervorgehoben wird.|Jede Aktion ist ein potenzieller Nachrichtenartikel.|
|Optimieren einer Anzeigenplatzierung auf einer Website.|Jede Aktion ist ein Layout oder Regeln zum Erstellen eines Layouts für die Anzeigen (z. B. oben, rechts, kleine Bilder, große Bilder).|
|Anzeigen einer personalisierten Rangfolge empfohlener Artikel auf einer Einkaufswebsite.|Jede Aktion ist ein bestimmtes Produkt.|
|Vorschlagen von Benutzeroberflächenelementen, z. B. Filtern, die auf ein bestimmtes Foto angewendet werden sollen.|Jede Aktion kann ein anderer Filter sein.|
|Auswählen der Antwort eines Chatbots, um eine Benutzerabsicht zu klären oder eine Aktion vorzuschlagen.|Jede Aktion ist eine Möglichkeit, wie die Antwort interpretiert werden kann.|
|Auswählen, was am Anfang einer Liste von Suchergebnissen angezeigt werden soll|Jede Aktion ist eine der obersten paar Suchergebnisse.|


### <a name="examples-of-features-for-actions"></a>Beispiele für Merkmale für Aktionen

Im Folgenden finden Sie gute Beispiele für Merkmale für Aktionen. Diese hängen in hohem Maß von der jeweiligen Anwendung ab.

* Merkmale mit Eigenschaften der Aktionen. Z. B., ist es ein Film oder eine TV-Serie?
* Merkmale dazu, wie Benutzer mit dieser Aktion in der Vergangenheit eventuell interagiert haben. Z. B., dieser Film wird hauptsächlich von Personen in der Demografie A oder B gesehen und wird in der Regel ist nicht mehr als einmal abgespielt.
* Merkmale zu den Eigenschaften, wie der Benutzer die Aktionen *sieht*. Z. B., enthält das für den Film in der Miniaturansicht angezeigte Poster Gesichter, Autos oder Landschaften?

### <a name="load-actions-from-the-client-application"></a>Laden von Aktionen aus der Clientanwendung

Merkmale aus Aktionen können typischerweise aus Content-Management-Systemen, Katalogen und Empfehlungssystemen stammen. Ihre Anwendung ist für das Laden der Informationen über die Aktionen aus den relevanten Datenbanken und Systemen, die Sie besitzen, verantwortlich. Wenn sich Ihre Aktionen nicht ändern, oder wenn es die Leistung unnötig beeinträchtigt, wenn sie jedes Mal geladen werden, können Sie in Ihrer Anwendung eine Logik für das Zwischenspeichern dieser Informationen hinzufügen.

### <a name="prevent-actions-from-being-ranked"></a>Verhindern der Zuweisung eines Rangs zu Aktionen

In einigen Fällen gibt es Aktionen, die Sie Benutzer nicht anzeigen möchten. Die beste Möglichkeit, zu verhindern, dass einer Aktion ein oberster Rang zugewiesen wird, besteht darin, sie in erster Linie nicht in die Liste der Aktionen für die Relevanz-API aufzunehmen.

In einigen Fällen kann dies erst später in Ihrer Geschäftslogik bestimmt werden, wenn eine aus einem Relevanz-API-Aufruf resultierende _Aktion_ einem Benutzer angezeigt werden soll. In diesen Fällen sollten Sie _Inaktive Ereignisse_ verwenden.

## <a name="json-format-for-actions"></a>JSON-Format für Aktionen

Beim Aufrufen von Relevanz senden Sie mehrere Aktionen zur Auswahl:

JSON-Objekte können geschachtelte JSON-Objekte und einfache Eigenschaften/Werte enthalten. Ein Array kann nur einbezogen werden, wenn die Arrayelemente Zahlen sind. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Beispiele für Kontextinformationen

Informationen für den _Kontext_ sind von jeder Anwendung und jedem Anwendungsfall abhängig, können aber typischerweise Informationen umfassen wie:

* Demografische und Profilinformationen über Ihre Benutzer.
* Aus HTTP-Kopfzeilen extrahierte Informationen wie Benutzer-Agent oder aus HTTP-Informationen abgeleitete wie umgekehrte geografische Suchen, basierend auf IP-Adressen.
* Informationen zur aktuellen Uhrzeit, z. B. den Wochentag, Wochenende oder nicht, Morgen oder Nachmittag, Weihnachtszeit oder nicht usw.
* Aus mobilen Anwendungen extrahierte Informationen, z. B. Standort, Bewegung oder Ladezustand der Batterie.
* Historische Aggregate von Benutzerverhalten wie: welche Filmgenres hat dieser Benutzer am häufigsten angeschaut.

Ihre Anwendung ist für das Laden der Informationen über den Kontext aus den relevanten Datenbanken, Sensoren und Systemen, die Sie möglicherweise besitzen, verantwortlich. Wenn sich Ihre Kontextinformationen nicht ändern, können Sie in Ihrer Anwendung eine Logik hinzufügen, die diese Informationen vor dem Senden an die Relevanz-API zwischenspeichert.

## <a name="json-format-for-context"></a>JSON-Format für Kontext 

Kontext wird als JSON-Objekt ausgedrückt, das an die Relevanz-API gesendet wird:

JSON-Objekte können geschachtelte JSON-Objekte und einfache Eigenschaften/Werte enthalten. Ein Array kann nur einbezogen werden, wenn die Arrayelemente Zahlen sind. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

[Vertiefendes Lernen](concepts-reinforcement-learning.md)